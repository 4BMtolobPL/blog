+++
title = "Rust for Linux 11"
date = "2026-09-15T18:20:47+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["Rust-for-Linux"]
keywords = ["linux", "rust-for-linux"]
description = "실전! 러스트로 배우는 리눅스 커널 프로그래밍 11장"
showFullContent = false
readingTime = false
hideComments = false
+++
# 러스트로 배우는 리눅스 커널 프로그래밍 11장

11장을 학습하면서 최신 소스에서 변경된 내용, 추가로 참고한 내용, 복사하기 위한 코드를 작성합니다.

## 1. 프로세스 스케줄
[프로세스 스케줄링 코드](https://github.com/4BMtolobPL/Rust-Linux-Kernel/blob/master/rust-linux-kernel-11-04-process-scheduling/src/main.rs)

권한 부족으로 예제 코드에서 sched_setscheduler가 실패하였다. 하지만 해당 코드에선 result로 반환값을 사용하지 않고 버리고 있어서 실패 여부가 출력되지 않았다.

기대 출력
```
자식 프로세스 PID: Pid(12345)
자식 프로세스 스케줄링 정책: 2
자식 프로세스 종료
```

실제 출력
```
자식 프로세스 PID: Pid(12345)
자식 프로세스 스케줄링 정책: 0
자식 프로세스 종료
```

libc에서 제공하는 정책은 다음과 같음
```rust
#[cfg(not(target_os = "l4re"))]
pub const SCHED_NORMAL: c_int = 0;
pub const SCHED_OTHER: c_int = 0;
pub const SCHED_FIFO: c_int = 1;
pub const SCHED_RR: c_int = 2;
pub const SCHED_BATCH: c_int = 3;
pub const SCHED_IDLE: c_int = 5;
pub const SCHED_DEADLINE: c_int = 6;
```

코드를 수정해 result에서 정보를 출력해보았다.
```
자식 프로세스 PID: 12345
SCHED_RR 우선순위 범위: 1 ~ 99
설정할 우선순위: 50
변경 전 스케줄링 정책: 0
스케줄링 정책 변경 실패
  대상 PID: 12345
  요청 정책: SCHED_RR (2)
  우선순위: 50
  errno: Operation not permitted (os error 1)
  원인: SCHED_RR을 설정할 권한이 없습니다 (EPERM).
  일반적으로 CAP_SYS_NICE 권한 또는 적절한 RLIMIT_RTPRIO 설정이 필요합니다.
자식 프로세스 시작: PID 12345
자식 프로세스 스케줄링 정책: 0
자식 프로세스 종료
```

부모 프로세스 코드 하단에 아래 코드를 추가해 자식 종료까지 부모가 기다리게 하여 실습하였다.
```rust
if let Err(err) = waitpid(child, None) {
    eprintln!("waitpid 실패: {err}");
}
```



## 2. 프로세스 동기화와 통신 (pipe)
[pipe 실습 코드](https://github.com/4BMtolobPL/Rust-Linux-Kernel/blob/master/rust-linux-kernel-11-05-pipe/src/main.rs)

책에서 제공하는 예제 코드는 아래와 같다.
```rust
fn main() {
    let (read_fd, write_fd) = pipe().expect("pipe 실패");

    match unsafe { fork() } {
        Ok(ForkResult::Parent { child, .. }) => {
            close(read_fd).expect("부모 프로세스에서 읽기 디스크립터 닫기 실패");

            let message = "안녕하세요, 자식 프로세스!";
            write(write_fd, message.as_bytes()).expect("부모 프로세스에서 파이프 쓰기 실패");
            
            close(write_fd).expect("부모 프로세스에서 쓰기 디스크립터 닫기 실패");

            waitpid(child, None).expect("waitpid 실패");
        } 
```

이때, write에서 write_fd의 소유권이 넘어가 직후 close에서 소유권 문제가 생겼다.
write_fd 자체를 넘기면 write가 종료됨과 동시에 write_fd가 drop된다.

최신 버전의 write는 아래와 같다.
```rust
pub fn write<Fd: std::os::fd::AsFd>(fd: Fd, buf: &[u8]) -> Result<usize>
```

AsFd는 ```impl<T: AsFd + ?Sized> AsFd for &T```으로 참조자에도 구현이 되어있다.
따라서 &write_fd로 넘겨주어 소유권 문제를 해결하였다.

또한, 최신 코드에선 close()를 직접 호출하지 않는 것이 일반적이라고 한다.
따라서 ```drop(write_fd);```와 같이 drop()을 사용하거나, 스코프를 이용해 자연스럽게 닫히게 할 수 있다.

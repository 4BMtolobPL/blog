+++
title = "Rust for Linux 10장"
date = "2026-09-13T22:34:00+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["Rust-for-Linux"]
keywords = ["linux", "rust-for-linux"]
description = "실전! 러스트로 배우는 리눅스 커널 프로그래밍 10장"
showFullContent = false
readingTime = false
hideComments = false
+++
# 러스트로 배우는 리눅스 커널 프로그래밍 10장

10장을 학습하면서 최신 소스에서 변경된 내용, 추가로 참고한 내용, 복사하기 위한 코드를 작성합니다.

## 1. Rust-for-Linux

[Rust for Linux](https://rust-for-linux.com/)
Rust for Linux 소스코드 클론

```sh
git clone https://github.com/Rust-for-Linux/linux.git
```



## 2. Virtio(9P) 모듈 활성화
qemu에서 호스트의 디렉토리를 공유하기 위해 Virtio 기능을 활성화 한다.

```sh
make menuconfig
```
Networking support - Plan 9 Resource Sharing Support (9P2000) 활성화(하위 모듈도)
File systems - Network File Systems - Plan 9 Resource Sharing Support (9P2000) 활성화

## 3. 커널 빌드
```bash
make -j$(nproc)
```

## 4. QEMU 실행
```sh
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -initrd ../busybox/initramfs.cpio.gz -nographic -append "console=ttyS0" -virtfs local,path=../kernel_modules,security_model=none,mount_tag=rust_modules
```

## 5. Mount
```bash
mkdir mnt
mount -t 9p -o trans=virtio rust_modules ./mnt

mount
```

## 6. 샘플 모듈 빌드
make시 samples/rust 경로에 있는 샘플 모듈들이 빌드된다.
```sh
ls samples/rust

# Qemu 공유 경로로 복사
cp samples/rust/*.ko ../kernel_modules
```

## 7. 샘플 모듈 로드
```sh
cd /mnt

# 모듈 로드
insmod rust_minimal.ko
lsmod

# 모듈 언로드
rmmod rust_minimal.ko
lsmod
```

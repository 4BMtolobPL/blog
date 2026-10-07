+++
title = "Rust for Linux 13"
date = "2026-09-22T19:50:24+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["Rust-for-Linux"]
keywords = ["linux", "rust-for-linux"]
description = "실전! 러스트로 배우는 리눅스 커널 프로그래밍 13장"
showFullContent = false
readingTime = false
hideComments = false
+++
# 러스트로 배우는 리눅스 커널 프로그래밍 13장

13장을 학습하면서 최신 소스에서 변경된 내용, 추가로 참고한 내용, 복사하기 위한 코드를 작성합니다.

## 1. Rust-for-Linux

빌드 명령어
```sh
make LLVM=1 -j$(nproc)
```


## 2. Module

모듈을 만든 후 아래와 같이 Kconfig과 Makefile에 설정 추가한다.
```Kconfig
config SAMPLE_RUST_PRINTK
	tristate "Printing my module"
	help
	  A simple print kernel message by ffi

config SAMPLE_RUST_MODULE_PARAMETERS
	tristate "Rust module parameters sample"
	help
	  A simple rust module parameters sample
```
```Makefile
obj-$(CONFIG_SAMPLE_RUST_PRINTK)		+= rust_printk.o
obj-$(CONFIG_SAMPLE_RUST_MODULE_PARAMETERS)	+= rust_module_parameters.o
```

menuconfig을 이용해 새 모듈 빌드 설정을 한다.(M)
```sh
make menuconfig
```

## 3. 코드 변경

책에서 제공하는 예제는 Rust-for-Linux의 rust 브랜치로 만들어진것으로 보인다. [해당 브랜치](https://rust-for-linux.com/past-branches)는 아카이브되고 더이상 새로운 변경사항이 추가되지 않는다.
따라서 새 브랜치인 rust-next를 이용해 실습하였다. 다만 예제 코드와 변경점이 아주 많이 있어 코드에 많은 수정이 필요했다.

1. module! 매크로 변경
```rust
module! {
    type: RustMinimal,
    name: "rust_minimal",
    author: "Rust for Linux Contributors",
    description: "Rust minimal sample",
    license: "GPL",
    params: {
        my_bool: bool {
            default: true,
            permission: 0,
            description: "Example of bool",
        },
        my_i32: i32 {
            default: 42,
            permission: 0o644,
            description: "Example of i32",
        },
        my_str: str {
            default: b"default str val",
            permission: 0o644,
            description: "Example of string param",
        },
        my_usize: usize {
            default: 42,
            permission: 0o644,
            description: "Example of usize",
        },
        my_array: ArrayParam<i32, 3> {
            default: [0, 1],
            permission: 0,
            description: "Example of array",
        },
    },
}
```
```rust
module! {
    type: RustMinimal,
    name: "rust_minimal",
    authors: ["Rust for Linux Contributors"],
    description: "Rust minimal sample",
    license: "GPL",
    params: {
        my_bool: bool {
            default: true,
            description: "Example of bool",
        },
        my_i32: i32 {
            default: 42,
            description: "Example of i32",
        },
        my_usize: usize {
            default: 42,
            description: "Example of usize",
        },
    },
}
```
author이 authors 배열로 변경되었다.
params 항목에서 permissions가 제거되었다.
type에 str과 ArrayParam 또한 사용할 수 없어졌다.

2. Vec
Rust-for-Linux에선 Vec가 Type과 Allocator를 요구한다.
```rust
pub type KVec<T>  = Vec<T, Kmalloc>;
pub type VVec<T>  = Vec<T, Vmalloc>;
pub type KVVec<T> = Vec<T, KVmalloc>;
```
위와 같은 alias가 있음

3. Misc device
예제에서는 module_misc_device! 매크로를 이용해 misc 디바이스를 만들었으나, 최신 Rust-for-Linux에서는 MiscDevice 트레잇으로 구현하게 변경되었다. [Rust-for-Linux Misc device 샘플](https://github.com/Rust-for-Linux/linux/blob/rust-next/samples/rust/rust_misc_device.rs)

[변경한 예제](https://github.com/4BMtolobPL/Rust-Linux-Kernel/blob/master/rust-linux-kernel-13-02-kernel-modules/rust_random_file.rs)

```rust
unsafe extern "C" {
    fn get_random_bytes(buf: *mut kernel::ffi::c_void, len: usize);
    fn add_device_randomness(buf: *const kernel::ffi::c_void, len: usize);
}
```
kernel::random::add_randomness()가 없어졌기 때문에 ffi로 직접 사용하였다.
또한 file::Operations의 read(), write() 메서드는 MiscDevice의 read_iter(), write_iter()로 각각 대응하여 구현하였다.

[전체 코드](https://github.com/4BMtolobPL/Rust-Linux-Kernel/tree/master/rust-linux-kernel-13-02-kernel-modules)

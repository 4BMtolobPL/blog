+++
title = "Fedora SELinux 환경에서 Lemurs 로그인 매니저 설정하기"
date = "2026-05-22T20:02:53+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Kimtalmo"
authorTwitter = "" #do not include @
cover = "posts/lemurs/lemurs.png"
tags = ["fedora", "lemurs", "selinux"]
keywords = ["fedora", "linux", "display-manager"]
description = "Fedora 최소설치 환경에서 SELinux를 활성화한 상태로 Lemurs 로그인 매니저를 설치하고 문제를 해결하는 과정을 다룹니다."
showFullContent = false
readingTime = false
hideComments = false
+++

# Lemurs 설정 가이드: SELinux 환경 대응

## 개요

**Lemurs** 설치 후 로그인시 SELinux 보안 정책으로 인해 로그인이 실패하고 로그인 화면으로 돌아가는 문제를 해결하는 방법을 정리합니다.

## 1. 사전 설치

Lemurs를 빌드하고 실행하기 위해 필요한 의존성을 먼저 설치해야 합니다.

### 필수 도구 설치
- **Rust**: [Rust 공식 홈페이지](https://rust-lang.org/)의 안내에 따라 설치합니다.
- **Git & PAM Development**: 빌드 도구와 인증 모듈 헤더가 필요합니다.

```bash
sudo dnf install git pam-devel
```

## 2. lemurs 빌드 및 설치

[lemurs github](https://github.com/coastalwhite/lemurs)

Lemurs는 현재 Fedora 공식 레포지토리에서 제공되지 않으므로, Github 소스에서 직접 빌드해야 합니다.

```bash
git clone https://github.com/coastalwhite/lemurs.git
cd lemurs
sudo ./install.sh
```
> **주의**: 외부 스크립트를 실행하기 전에는 반드시 `install.sh`의 내용을 검토하여 안전한지 확인해야 합니다.

설치가 완료되면 재부팅 후 로그인 매니저를 사용할 수 있습니다.


## 3. 주요 문제 해결

### 3.1. 로그인 화면이 표시되지 않는 경우 (systemd target)

Fedora 최소 설치 시 systemd target 기본값이 `multi-user.target`으로 설정되어 있어 그래픽 로그인 환경이 실행되지 않을 수 있습니다. `graphical.target`으로 설정하여 로그인 화면이 표시되도록 변경합니다.

```bash
sudo systemctl set-default graphical.target
```


### 3.2. 로그인 실패 및 무한 루프 (SELinux)

로그인을 시도했으나 인증 후 다시 Lemurs 화면으로 돌아온다면, SELinux가 Lemurs의 특정 동작을 차단했을 가능성이 높습니다. 아래 명령어로 최근 발생한 거부(AVC) 로그를 확인 할 수 있습니다.

```bash
sudo ausearch -m avc -ts recent
```



## 4. 해결

관련 문제를 찾던 중 lemurs 이슈에서 관련 문제를 발견했습니다.
[lemurs issue](https://github.com/coastalwhite/lemurs/issues/166#issuecomment-2628757868)

### 정책 생성 도구 설치
`audit2allow` 명령어가 없다면 아래 명령어로 설치 할 수 있습니다.
```bash
sudo dnf install policycoreutils-python-utils
```

### 정책 모듈 생성 및 로드
```bash
# 거부 로그을 바탕으로 정책 파일 생성(.te, .pp)
sudo ausearch -c 'lemurs' --raw | audit2allow -M lemurs_policy

# 정책 모듈 로드
sudo semodule -i lemurs_policy.pp
```

적용 후 다시 로그인하면 정상적으로 세션에 진입 할 수 있습니다.
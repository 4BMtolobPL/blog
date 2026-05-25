+++
title = "SSH key 만들고 사용하기"
date = "2026-05-25T14:15:22+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["ssh", "linux"]
keywords = ["ssh-keygen", "ssh-copy-id"]
description = "ssh-keygen과 ssh-copy-id를 사용하여 SSH key를 생성하고 사용하는 방법을 설명합니다."
showFullContent = false
readingTime = false
hideComments = false
+++
# SSH key 만들고 사용하기


## 1. ssh-keygen
```bash
ssh-keygen -t ed25519 -C [이메일 또는 용도 표시]
```

키 생성중 passphrase를 물어보는데, 일반적인 홈 서버등에서든 편의를 의해 passphrase를 입력하지 않고 키를 생성한다. 하지만 나는 보안 우려와 외부에서 간혹 접속하는 경우가 있어서 항상 passphrase를 설정한다.

**-t 옵션**: 키 타입을 지정한다. 특별한 이유가 없다면 기본 설정인 `ed25519`를 사용한다.  
**-C 옵션**: 키에 대한 설명을 추가한다. 이메일 주소나 용도를 표시할 수 있다.

## 2. ssh-copy-id
```bash
ssh-copy-id [user@]hostname

# 특정 키 파일을 사용하여 복사
ssh-copy-id -i [identity_file] [user@]hostname
```

ssh-copy-id를 사용하여 생성한 키를 원격 서버에 복사한다.
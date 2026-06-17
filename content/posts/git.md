+++
title = "Git"
date = "2026-06-17T15:27:59+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["git"]
keywords = ["git"]
description = "Git 사용할때 자주 사용하는 명령어 모음"
showFullContent = false
readingTime = false
hideComments = false
+++

1. ## 원격에서 삭제된 브랜치가 로컬 log에 뜰때

```bash
# remote에 삭제된 브랜치 참조 삭제
git fetch --prune

# 로컬 브랜치도 삭제하기
# 이때 [origin/<branch-name>: gone]이라고 표시된 브랜치가 원격에서 삭제된 브랜치
git branch -vv
git branch -d <branch-name>
```

```bash
# git fetch 기본 설정을 --prune으로 변경
git config --global fetch.prune true
```

2. ## 원격 레포지토리 관리
```bash
# 원격 레포지토리 삭제
git remote remove origin

# 원격 레포지토리 추가
git remote add origin <url>

# 원격 레포지토리 변경
git remote set-url origin <new-url>
```

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

3. ## 첫 커밋을 포함한 squash
일반적인 squash는 아래와 같다.
```bash
# HEAD~2까지의 커밋을 squash
git rebase -i HEAD~2
```

squash 대상에 첫 커밋(root)가 포함되면 'fatal: invalid upstream HEAD~2'과 같은 에러가 발생한다.
첫 커밋을 squash 하려면 아래와 같이 `--root` 옵션을 사용한다.
```bash
git rebase -i --root
```

그 후 push시엔 force push한다.
```bash
git push origin <branch-name> --force
```

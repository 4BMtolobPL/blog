+++
title = "Yazi를 File Chooser로 사용하기"
date = "2026-10-23T13:01:43+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "KimTalmo"
authorTwitter = "" #do not include @
cover = ""
tags = ["fedora", "filechooser"]
keywords = ["linux", "filechooser"]
description = "터미널 기반의 FileChooser 사용하기"
showFullContent = false
readingTime = false
hideComments = false
+++

# yazi를 file-chooser로 사용하기

## 1. xdg-desktop-portal-termfilechooser 설치

[xdg-desktop-portal-termfilechooser Github](https://github.com/hunkyburrito/xdg-desktop-portal-termfilechooser)

## 2. config 복사 및 수정

```bash
# config 경로는 일반적으로 아래 목록 중 하나
# 1. /usr/local/share/xdg-desktop-portal-termfilechooser/config
# 2. /usr/share/xdg-desktop-portal-termfilechooser/config
cp /usr/share/xdg-desktop-portal-termfilechooser/config $XDG_CONFIG_HOME/xdg-desktop-portal-termfilechooser
```

```conf
[filechooser]
cmd=yazi-wrapper.sh
default_dir=$HOME
; Uncomment to skip creating destination save files with instructions in them
; create_help_file=0
; Uncomment and edit the line below to change the terminal emulator command
env=TERMCMD='ghostty -e'

; Mode must be one of 'suggested', 'default', or 'last'.
open_mode=suggested
save_mode=suggested
```

cmd와 TERMCMD을 사용하는 파일 매니저와 터미널에 맞게 수정한다.

## 3. xdg-desktop-portal 선호 FileChooser 설정

`$XDG_CONFIG_HOME/xdg-desktop-portal/portals.conf` 파일에 FileChooser 선호 설정을 추가한다.

```conf
[preferred]
org.freedesktop.impl.portal.FileChooser=termfilechooser
```

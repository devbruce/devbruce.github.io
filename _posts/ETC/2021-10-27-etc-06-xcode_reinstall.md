---
title: "[ETC] Reinstall Xcode of MacOS"
excerpt: 
last_modified_at: 2021-10-27

categories:
  - ETC

tags:
  - Xcode
  - MacOS

---

# Reinstall Xcode of MacOS

MacOS를 Upgrade 하였을 경우 발생하는 에러를 해결하는 방법 중 하나가 xcode 재설치이다.  

<br>

## Command

- 기존에 설치되어있던 CommandLineTools 제거

```bash
sudo rm -rf /Library/Developer/CommandLineTools
```

- Xcode 설치

```bash
sudo xcode-select --install
```

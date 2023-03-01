---
title: "[AWS] EC2 - Set Instance Environment"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - AWS

tags:
  - AWS
  - deploy
  - EC2
  - instance
  - ubuntu
  - zsh
  - pyenv

---

![AWS-EC2](https://img.shields.io/badge/AWS-EC2-brightgreen.svg)
![AMI-Ubuntu Server 18.04 LTS](https://img.shields.io/badge/AMI-Ubuntu%20Server%2018.04%20LTS-orange.svg)

# Set Instance Environment

인스턴스(Instance)에 파일을 업로드하고 배포하기 위해서는 기본적인 설정이 필요하다.  
(인스턴스의 AMI 는 Ubuntu Server 18.04 LTS 임을 가정한다.)

ssh로 인스턴스로 연결을 하게되면, 아무것도 깔려있지 않은 ubuntu 인 것을 확인할 수 있다.  
우선, 기존에 설치되어 있는 패키지들을 업그레이드한다.

<br>

## Package

### 패키지 정보 업데이트

```bash
$ sudo apt-get update
```

<br>

### 패키지 업그레이드 (with 의존성 검사)

```bash
$ sudo apt-get dist-upgrade
```

Upgrade 과정에서 나오는 질의는 Yes 또는 Default값으로 Pass

<br>

## ZSH

터미널 활용 편의성 ↑ (권장사항)

### Install ZSH

```bash
$ sudo apt-get install zsh
```

<br>

### Install oh-my-zsh

```bash
$ sudo sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

<br>

### Change Default Shell To ZSH

```bash
$ sudo chsh ubuntu -s /usr/bin/zsh
```

<br><br>

## Set locale

아래의 내용을 셸 프로필 파일(Shell Profile File)에 추가한다.  
(zsh 를 사용중이므로 `~/.zshrc` 에 추가)

```bash
export LC_ALL="en_US.UTF-8"
```

해당 설정을 추가하지 않을 경우 locale 에러가 발생할 수 있다.

<br><br>

## pyenv

### Requirements

pyenv 를 사용하기 위해서 설치해야할 패키지들이 있다.  
ubuntu 의 경우 아래의 코드를 터미널에서 입력하여 관련 패키지들을 설치한다.

```bash
$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev
```

<br>

### Install pyenv

```bash
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

<br>

### Set Shell Profile

아래의 내용을 셸 프로필 파일(Shell Profile File)에 추가한다.  
(zsh 를 사용중이므로 `~/.zshrc` 에 추가)

```bash
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
```

아래의 코드를 터미널에서 입력한다.

```bash
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

<br>

## pyenv-virtualenv

### Install pyenv-virtualenv

```bash
$ git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

<br>

### Set Shell Profile

아래의 내용을 셸 프로필 파일(Shell Profile File)에 추가한다.  
(zsh 를 사용중이므로 `~/.zshrc` 에 추가)

```bash
eval "$(pyenv virtualenv-init -)"
```

<br><br>

# Reference

- [pyenv](https://github.com/pyenv/pyenv)
- [pyenv-virtualenv]()
- [Oh My ZSH!](https://ohmyz.sh/)

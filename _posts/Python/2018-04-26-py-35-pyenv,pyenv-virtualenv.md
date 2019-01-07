---
title: "[Python] pyenv / pyenv-virtualenv"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - pyenv
  - pyenv-virtualenv

---

![OS-macOS](https://img.shields.io/badge/OS-macOS-blue.svg)


# pyenv

Python Version Manager

<br>

## Install

1\. brew를 통해 Install

```bash
$ brew install pyenv
```

2\. 아래 코드를 터미널에 입력  
(마지막 `.zshrc` 은 사용하는 셸에 맞게 수정필요)

```bash
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

<br><br>

# pyenv-virtualenv

- pyenv plugin

- Manage virtualenvs

<br>

## Install

1\. brew를 통해 Install

```bash
$ brew install pyenv-virtualenv
```

2\. 아래 코드를 본인의 셸 프로필 파일에 추가한다.

```bash
eval "$(pyenv virtualenv-init -)"
```

<br><br>

# Command

## pyenv

> `<ENV>` : Python 버전(ex. `3.6.4`) 또는 pyenv virtualenv 로 네이밍된 환경

### Build Environment

- `$ pyenv install --list`  : pyenv로 설치가능한 Python 버전 리스트을 보여준다.

- `$ pyenv install X.X.X` : Python X.X.X 버전을 설치한다.

- `$ pyenv uninstall <ENV>` : Python `ENV` 환경을 제거한다.

<br>

### Check Environment

- `$ pyenv version` : 현재 pyenv로 사용중인 Python 환경을 보여준다.

- `$ pyenv versions` : 현재 구축되어있는 환경 리스트를 보여준다.

<br>

### Set Environment

- `$ pyenv shell <ENV>` : Python 사용환경을 `ENV` 로 변경한다.

- `$ pyenv global <ENV>` :  `ENV` 환경을 default 로 지정한다.

- `$ pyenv local <ENV>` : 현재 디렉토리에 ENV가 입력된 `.python-version`파일 생성
  - `.python-version` 파일이 저장된 디렉토리에 진입시, `.python-version` 에 입력된 python 환경으로 자동 지정된다.

<br>

### ETC

- `$ python --version` or `$ python -V` : 현재 Python 버전을 확인가능하다.

- `$ pyenv rehash` : 환경 재설정 (변경사항 발생시 사용권장)

<br><br>

## pyenv-virtualenv

- `$ pyenv virtualenv X.X.X <ENV>` : X.X.X 버전의 `ENV` 로 네이밍된 가상환경을 만든다.

- `$ pyenv activate <ENV>` : `ENV` 가상환경 활성화

- `$ pyenv deactivate` :  현재 가상환경 비활성화

<br><br>

# Reference

- [pyenv](https://github.com/pyenv/pyenv)

- [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

---
title: "[Python] pyenv / pyenv-virtualenv"
excerpt: 
last_modified_at: 2020-08-15

categories:
  - Python

tags:
  - python
  - pyenv
  - pyenv-virtualenv

---

# pyenv

Python Version Manager

<br>

## Install (macOS)

```bash
$ brew install pyenv
```

```bash
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

> `>> ~/.zshrc` 부분은 본인이 사용하는 Shell 에 맞게 변경필요

<br><br>

## Install (ubuntu)

### Prerequisites ([Common-build-problems Link](https://github.com/pyenv/pyenv/wiki/Common-build-problems))

```bash
$ sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

<br>

```bash
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

```bash
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

> `>> ~/.zshrc` 부분은 본인이 사용하는 Shell 에 맞게 변경필요

<br><br>

# pyenv-virtualenv

- pyenv plugin

- Manage virtualenvs

<br>

## Install (macOS)

```bash
$ brew install pyenv-virtualenv
```

```bash
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```
> `>> ~/.zshrc` 부분은 본인이 사용하는 Shell 에 맞게 변경필요

<br>

## Install (ubuntu)

```bash
$ git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

```bash
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```
> `>> ~/.zshrc` 부분은 본인이 사용하는 Shell 에 맞게 변경필요

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

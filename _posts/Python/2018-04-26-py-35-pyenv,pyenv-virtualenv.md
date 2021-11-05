---
title: "[Python] pyenv / pyenv-virtualenv"
excerpt: 
last_modified_at: 2021-10-27

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

## Install via Basic GitHub Checkout

### macOS

- with zsh

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zprofile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zprofile
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile

echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

<br><br>

### Ubuntu

- with zsh

#### Prerequisites ([Common-build-problems Link](https://github.com/pyenv/pyenv/wiki/Common-build-problems))

```bash
sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

#### Install

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zprofile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zprofile
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile

echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.profile
echo 'eval "$(pyenv init --path)"' >> ~/.profile

echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

<br><br>

# pyenv-virtualenv

- pyenv plugin

- Manage virtualenvs

<br>

## Installing as a pyenv plugin with zsh

```bash
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

```bash
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```

<br><br>

# Command

## pyenv

> `${ENV}` : Python 버전(ex. `3.6.4`) 또는 pyenv virtualenv 로 네이밍된 환경

### Build Environment

- pyenv로 설치가능한 Python 버전 리스트을 보여준다.

```bash
pyenv install --list
```

- Python X.X.X 버전을 설치한다.

```bash
pyenv install X.X.X
```

- Python `ENV` 환경을 제거한다.

```bash
pyenv uninstall ${ENV}
```

<br>

### Check Environment

- 현재 pyenv로 사용중인 Python 환경을 보여준다.

```bash
pyenv version
```

- 현재 구축되어있는 환경 리스트를 보여준다.

```bash
pyenv versions
```

<br>

### Set Environment

- Python 사용환경을 `ENV` 로 변경한다.

```bash
pyenv shell ${ENV}
```


- `ENV` 환경을 default 로 지정한다.

```bash
pyenv global ${ENV}
```

- 현재 디렉토리에 ENV가 입력된 `.python-version`파일 생성  
`.python-version` 파일이 저장된 디렉토리에 진입시, `.python-version` 에 입력된 python 환경으로 자동 지정된다.

```bash
pyenv local ${ENV}
``` 

<br>

### ETC

- 환경 재설정 (변경사항 발생시 사용권장)

```bash
pyenv rehash
```

<br><br>

## pyenv-virtualenv

- X.X.X 버전의 `ENV` 로 네이밍된 가상환경을 만든다.

```bash
pyenv virtualenv X.X.X ${ENV}
```

- `ENV` 가상환경 활성화

```bash
pyenv activate ${ENV}
```

- 현재 가상환경 비활성화

```bash
pyenv deactivate
```

<br><br>

# Reference

- [pyenv](https://github.com/pyenv/pyenv)

- [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

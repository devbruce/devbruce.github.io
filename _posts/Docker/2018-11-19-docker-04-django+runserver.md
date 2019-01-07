---
title: "[Docker] Django Runserver"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Docker

tags:
  - docker
  - dockerfile
  - image
  - container
  - django
  - python
  - pyenv
  - pyenv-virtualenv
  - runserver

---

![Docker-version-18.06.1-ce](https://img.shields.io/badge/Docker-18.06.1--ce-blue.svg)
![django-version-2.1.2](https://img.shields.io/badge/django-v2.1.2-brightgreen.svg)
![python-version-3.6.6](https://img.shields.io/badge/python-v3.6.6-blue.svg)

# Django Runserver In Container

- Dockerfile 을 작성하여 이미지를 빌드할 경우 (\# 1)
  - 기본 Shell 이 bash 가 아니다. 기본값이 `SHELL ["/bin/sh", "-c"]` 임을 염두한다.
  - Bash 와는 다르게 환경변수를 직접 할당해주어야 하는 경우가 많다.
  - `$HOME` 을 호출하면 `/root` 가 정상적으로 출력된다. 하지만 환경변수 설정에 사용하면 빈값으로 들어간다(?).  
  따라서 `$HOME` 을 `/root` 를 새로 할당해주고 사용하자. (`ENV HOME /root`)

<br>

- 컨테이너를 직접 실행시켜서 이미지를 빌드할 경우 (\# 2)
  - `docker run` 에서 실행 커맨드 `[COMMAND]` 를 `/bin/bash` 로 실행하면 Default Shell 이 bash 이다.  
    - 익숙하게 사용하던 Shell 이어서 사용하는 것이 더 친숙하다.  
    - `ubuntu:18.04` 와 같은 이미지의 경우 `CMD /bin/bash` 가 작성되어 있어 `[COMMAND]` 의 기본값이 `/bin/bash` 이다.
  - 환경변수를 셸 프로필 파일에 추가할 경우 `.bashrc` 에 추가한다. 그리고 `source ~/.bashrc` 명령어를 통해 적용가능하다.

<br><br>

## \# 1 : Dockerfile

```dockerfile
FROM ubuntu:18.04

# Ignore debconf: Warning
ENV DEBCONF_NOWARNINGS yes

# Ignore query
ENV DEBIAN_FRONTEND noninteractive

# Update & Upgrade
RUN apt-get update && apt-get -y dist-upgrade

# git install
RUN apt-get install -y git

# pyenv requirements
RUN apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev

# pyenv install
RUN git clone https://github.com/pyenv/pyenv.git ~/.pyenv

# pyenv set PATH
ENV HOME /root
ENV PYENV_ROOT $HOME/.pyenv
ENV PATH $PYENV_ROOT/bin:$PATH

# pyenv-virtualenv install
RUN git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv

# Set Python Environment
RUN pyenv install 3.6.6
RUN pyenv virtualenv 3.6.6 prc

ENV prc_path $PYENV_ROOT/versions/prc
ENV prc_pip $prc_path/bin/pip
ENV prc_python $prc_path/bin/python
ENV prc_dj $prc_path/lib/python3.6/site-packages/django/bin/django-admin.py

# Django Start Project
WORKDIR /srv/project
RUN $prc_pip install django
RUN $prc_python $prc_dj startproject runserver

# Set Runserver Command
WORKDIR /srv/project/runserver
CMD $prc_python manage.py runserver 0:9777
```

<br>

### Build Image

위와 같이 작성된 Dockerfile 을 임의의 디렉토리로 이동시킨다.  
그리고 해당 디렉토리로 이동하여 아래의 명령어를 입력하여 Image 를 빌드한다.

```bash
$ docker build -t dj:run .
```

- `<ImageName>` : dj  
- `<tag>` : run  
- Dockerfile 경로 : `.` (현재 디렉토리)

<br><br>

## \# 2 : Set Container Directly

Dockerfile 을 작성하는 것이 아니라 직접 Container 의 환경을 셋팅한 뒤, Image 를 빌드할 수 있다.  

```bash
$ docker -it ubuntu:18.04 /bin/bash
```

- `--rm` 옵션을 주지않는다. (컨테이너를 종료했을 때 삭제되면 `commit` 을 할 수 없다.)

- `/bin/bash` : bash 로 해당 이미지를 컨테이너로 실행시킨다. (작성하지 않아도 기본값으로 주어지긴한다.)

<br>

Container가 실행되면 Dockerfile 에 작성한 내용들을 직접 하나씩 실행한다.  
**이 때 설정값들을 `~/.bash_profile` 이 아닌 `~/.bashrc` 에 추가해야 인식된다.**  

<br>

### Build Image

`python manage.py runserver 0:9777` 까지 완료했다면 `exit` 로 해당 컨테이너를 종료한다.  
그리고 아래와 같이 `commit` 명령어를 통해 이미지를 생성한다.  

```bash
$ docker commit <ContainerID> dj:run
```

- `<ImageName>` : dj  
- `<tag>` : run  

<br><br>

## Run Image

위의 두가지 방식 중에 어떤 것을 선택했는지는 상관없다.  
문제없이 빌드가 끝난 이미지를 실행시켜보자.  

```bash
$ docker run --rm -it -p 7777:9777 <ImagneName>:<tag>
```

- `--rm` : 컨테이너를 exit 하면 자동삭제

- `-it` : 터미널 환경제공

- `-p` : 컨테이너의 포트 9777번을 호스트에 7777로 publish  
(9777번 포트는 7777번 포트로 오는 요청을 받는다.)

 <br>
 
`[COMMAND]` 는 따로 작성하지 않는다.  
Dockerfile 에 `CMD python manage.py runserver 0:9777` 가 이미 작성되어 있으므로 해당값이 기본값으로 실행된다.  

컨테이너에서 django 서버가 실행되면,
`-p` 옵션에 준 값에 따라 `localhost:7777` 으로 접근해보자.  
Django 페이지에 정상적으로 접근할 수 있을 것이다.

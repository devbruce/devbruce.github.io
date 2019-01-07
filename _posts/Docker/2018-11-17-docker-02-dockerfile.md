---
title: "[Docker] Dockerfile"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Docker

tags:
  - docker
  - dockerfile
  - image
  - build
  - bash

---

![Docker-version-18.06.1-ce](https://img.shields.io/badge/Docker-18.06.1--ce-blue.svg)

# Dockerfile

- Dockerfile 을 기반으로 Docker Image 를 빌드할 수 있다.

- `docker build` 명령어를 사용

```bash
$ docker build [OPTIONS] <Path>
```

- `<Path>` 에는 Dockerfile 이 위치한 **경로만 입력**한다. (Dockerfile 을 자동으로 찾아서 읽는다.)  

- `<Path>` 에는 URL 이 들어갈 수도 있다.

<br>

## docker build OPTIONS

- `-t` : `--tag` 와 동일  
생성할 이미지의 `<ImageName>:<tag>` 를 설정한다.

- `-f` : `--file` 와 동일  
Dockerfile 을 구체적으로 지정한다.  
기본값은 `<PATH>/Dockerfile` 이다.  

- `--rm` : 이미지 생성 중간과정에서 생성되는 컨테이너를 이미지 생성 후 제거한다.  
기본값은 true 이다.

<br><br>

# Make Dockerfile

## Command

### FROM

기준이 될 Docker Image 를 지정한다. (`<ImageName>:<tag>` 형태)  
지정한 이미지가 없을경우 자동으로 pull 을 시도한다.

```dockerfile
FROM ubuntu:18.4
```

<br>

### RUN

Shell 명령어 실행 (Image 빌드할 때 실행)

```dockerfile
RUN apt-get -y update
```

ubuntu 패키지를 설치하기 위해 `apt` 명령어를 사용하면 아래와 같은 경고가 나타난다.

`WARNING: apt does not have a stable CLI interface. Use with caution in scripts.`  

따라서 Dockerfile 에서는 `apt-get` 을 사용하자.

<br>

### CMD

**컨테이너가 실행될 때** 실행할 Shell 명령어 또는 파일  

- 여러번 사용해도 마지막에 작성한 명령어만 적용된다.(Override)  
- `docker run` 명령어의 `[COMMAND]` 의 기본값이 된다. `[COMMAND]` 값을 입력시 `CMD` 를 Override 한다.  
- 마지막에 작성하는 것을 권장

```dockerfile
CMD /bin/bash
```

<br>

### WORKDIR

명령어가 실행될 경로 설정  

- Shell 에서의 `cd` 와 유사하다.

- `WORKDIR` 에 해당하는 경로가 없을경우 자동으로 해당 디렉토리들을 생성

```dockerfile
WORKDIR /tmp/tmp_project
```

<br>

### COPY

HOST 의 파일과 디렉토리를 이미지에 복사한다.  

- 복사할 경로가 이미지 내에 없을 경우 자동으로 만든다.
- 컨테이너의 설정경로는 **절대경로**만 설정가능하다.
- 디렉토리를 복사할 경우 컨테이너의 복사경로 마지막 디렉토리에 호스트의 디렉토리(폴더)가 디렉토리 자체로 들어가는 것이 아니라 경로의 마지막 디렉토리명으로 변경된다.  
(업로드 형태)
- 복사하려는 파일 또는 디렉토리가 이미 존재할 경우 **덮어씌운다.**
- 호스트의 경로는 Dockerfile 이 위치한 경로 상위로 갈 수 없다.  

```dockerfile
COPY . /srv/tmp_project
```

<br>

### SHELL

- Default Shell(기본 셸)을 재정의한다.

- JSON 형식으로 작성

- 리눅스의 기본값 : `["/bin/sh", "-c"]`

```dockerfile
SHELL ["/bin/bash", "-c"]
RUN git clone <URL>

SHELL ["/bin/bash", "-c"]
RUN pyenv install 3.6.6
```

<br>

#### Bash OPTIONS

- `-c` : 문자열(String)에 의해 명령어가 인식된다.

- `-l` : `--login` 과 동일  
bash 를 로그인 셸로 호출된 것처럼 작동시킨다.  
`-l` 옵션으로 각 명령어마다 셸을 지정하는 것이 아닌 로그인 셸을 재정의할 수 있다.

```dockerfile
# Set Login Shell To Bash
SHELL ["/bin/bash", "-l", "-c"]

# RUN
RUN git clone <URL>
RUN pyenv install 3.6.6
```

> 로그인 셸을 변경하였을 때 부작용으로 에러 및 경고가 나타날 수 있다.

<br>

### ENV

환경변수 설정

- 환경변수 한개 설정 형태 : `ENV <Key> <Value>`   
- 환경변수 다수 설정 형태 : `ENV <Key1>=<Value1> <Key2>=<Value2> . . .`

```dockerfile
ENV TMPVAR 1234
ENV TMPVAR1=1111 TMPVAR2=2222
```

> 환경변수 한개를 설정할 때 `ENV TMPVAR=1234` 로 해도 작동한다.

<br>

### EXPOSE

런타임에 지정된 포트에서 수신대기  

- publish 가 아니다. publish 는 `docker run -p` 가 해당된다.
- 수신 프로토콜(TCP 또는 UDP)을 지정할 수 있다.(기본값은 TCP)

```dockerfile
EXPOSE 60/tcp
EXPOSE 7777/udp
EXPOSE 80
```

<br><br>

# Reference

- [Docker Documentation - Reference:docker build](https://docs.docker.com/engine/reference/commandline/build/)

- [Docker Documentation - Reference:Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

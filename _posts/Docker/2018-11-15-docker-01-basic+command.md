---
title: "[Docker] Basic Command"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Docker

tags:
  - docker
  - image
  - container
  - command

---

![Docker-version-18.06.1-ce](https://img.shields.io/badge/Docker-18.06.1--ce-blue.svg)

# Docker Command

## version

```bash
$ docker version
```

<br>

## info

```bash
$ docker info
```

<br><br>

## Image

### images (Show List)

```bash
$ docker images
```

<br>

### pull (Download)

```bash
$ docker pull <ImageName>:<tag>
```

- 해당 도커 이미지를 다운받는다.

- `<tag>` 를 명시하지 않을시 `latest` 가 default 에 해당된다.

- ex) `ubuntu:18.04`

<br>

### rmi (Remove Image)

```bash
$ docker rmi <ImageName>:<tag>
```

해당 이미지를 참조하고 있는 컨테이너가 있을 경우 먼저 제거해야한다.  
OPTION  `-f` 로 강제로 지우는 것이 가능하긴하다.

<br>

### commit (Build Image By Container)

```bash
$ docker commit <ContainerID> <ImageName>:<tag>
```

- 해당 컨테이너의 상태를 도커 이미지파일로 만든다.  

- `commit` 명령어로 이미지를 빌드하기 위해서는 당연하지만 컨테이너가 지워져선 안된다.  
그런데 docker run 명렁어에 `--rm` 을 주었을 경우는 exit 하는 순간 컨테이너가 지워진다.  
이런 경우에는 컨테이너가 실행되고 있는 상태에서 새로운 터미널을 열어서 commit 명령어를 실행한다.  

<br>

### build (Build Image By Dockerfile)

```bash
$ docker build [OPTIONS] <Path>
```

- `<Path>` 에는 Dockerfile 이 위치한 **경로만 입력**한다. (Dockerfile 을 자동으로 찾아서 읽는다.)  

- `<Path>` 에는 URL 이 들어갈 수도 있다.

<br>

#### OPTIONS

- `-t` : `--tag` 와 동일  
생성할 이미지의 `<ImageName>:<tag>` 를 설정한다.

- `-f` : `--file` 와 동일  
Dockerfile 을 구체적으로 지정한다.  
기본값은 `<PATH>/Dockerfile` 이다.  

- `--rm` : 이미지 생성 중간과정에서 생성되는 컨테이너를 이미지 생성 후 제거한다.  
기본값은 true 이다.

<br><br>

## Container

- `<ContainerID>` 는 첫번째 한자리만 작성해도 해당값에 중복되는 것이 없다면 인식된다.

- `<ContainerID>` 에는 `<ContainerName>` 이 올 수 있다.

<br>

### ps (Show List)

```bash
$ docker ps [OPTIONS]
```

#### OPTIONS

- `-a` : `--all` 과 동일  
종료 상태 Exited (0) 인 컨테이너도 보여준다. 

<br>

### run (Build Container)

```bash
$ docker run [OPTIONS] <ImageName>:<tag> [COMMAND]
```

<br>

#### OPTIONS

- `--rm` : 컨테이너를 나갈 경우(Exit) 자동으로 해당 컨테이너를 제거

- `--name` : 해당 컨테이너에 이름을 부여한다.  
지정안할경우 도커에서 자동으로 임의의 이름을 할당한다.

- `-t` : `--tty` 와 동일.  
의사(pseudo) TTY 할당 (터미널과 유사한 환경을 만들어준다.)

- `-i` :  `--interactive` 와 동일.  
해당 옵션을 주지않고 컨테이너를 run 하게되면 터미널 명령어를 입력해도 아무런 값을 출력하지 않는다.  

- `-d` : `--detach` 와 동일.  
컨테이너를 백그라운드에서 실행시킨다.

- `-p` : `--publish` 와 동일.  
컨테이너의 포트를 호스트에 publish

<br>

#### Example

```bash
$ docker run --rm -it ubuntu:18.04 /bin/bash
```

ubuntu:18.04 이미지로 컨테이너를 실행시키고 bash 실행

- `--rm` 옵션 : 컨테이너를 나가면 (exit) 컨테이너 자동삭제

- `-it` 옵션 : `-i` \+ `-t` 이다. 터미널과 같은 환경을 제공한다.

- `[COMMAND]` 위치에 `/bin/bash` 를 입력하여 bash 를 실행시킨다.  

> Dockerfile 에 `CMD` 명령어가 있을 경우 `[COMMAND]` 값을 자동으로 채워준다.  
> 
> **Example**
> 
> ```
> CMD ["/bin/bash"]
> ```
> 
> `[COMMAND]` 에 빈값을 주어도 bash 가 실행된다.  
> 
> ubuntu 이미지가 이러한 형태이기 때문에 `/bin/bash` 를 입력하지 않아도 bash 가 실행된다.

<br>

### rm (Remove Container)

```bash
$ docker rm <ContainerID>
```

- 해당 컨테이너를 제거한다.

<br>

### restart

```bash
$ docker restart <ContainerID>
```

- Exited (0) 상태의 컨테이너를 다시 실행시킨다. (STATUS : UP)

- 다시 실행된 Container 에 접근하기 위해서는 attach 명령어를 사용한다.

<br>

### exec

```bash
$ docker exec <ContainerID> <COMMAND>
```

실행중인 컨테이너에 명령어를 실행한다.  

<br>

#### \-it /bin/bash

```bash
$ docker exec -it <ContainerID> /bin/bash
```

- `-it` : 터미널 환경 제공  

위와 같이 `exec` 명령어를 `-it` 옵션을 사용하여 bash 를 실행하면  
`attach` 명령어를 통해 컨테이너를 접근한 것과 유사하게 작동한다.  
차이점은 터미널을 여러개 실행하여 각각 터미널에서 같은 컨테이너로 접근했을 때 개별적으로 작동가능하다는 점이다.

> `attach` 의 경우 여러개의 터미널을 실행하여 동일한 컨테이너에 접근할 경우 명령어가 동시에 실행되는 것을 알 수 있다.

<br>

### attach

```bash
$ docker attach <ContainerID>
```

- 실행중인 컨테이너에 접근

> `docker exec -it /bin/bash` 와는 다르게  
> 터미널을 여러개 실행하여 각각의 터미널이 같은 컨테이너에 `attach` 로 접근하면  
> 동시에 같은 작업을 수행한다.

<br>

### stop

```bash
$ docker stop <ContainerID>
```

- 해당 컨테이너 종료

<br>

### rename

```bash
$ docker rename <PreviousName> <NewName>
```

- 해당 컨테이너의 이름을 재할당한다.

<br>

### diff

```bash
$ docker diff <ContainerID>
```

- 컨테이너를 생성한 이미지(Image)를 기준으로 해당 컨테이너의 변경사항을 보여준다.
  - `A` : 파일 추가 (Add)
  - `D` : 파일 삭제 (Delete)
  - `C` : 파일 변경 (Change)

<br>

### cp (Copy)

> `-r` 옵션을 주지않아도 디렉토리도 복사된다.

#### Host To Container

```
$ docker cp <HostFilePath> <ContainerID>:<ContainerFilePath>
```

- `<HostFilePath>` 에 해당하는 파일을 `<ContainerID>` 의  `<ContainerFilePath>` 에 복사한다.

<br>

#### Container To Host

```bash
$ docker cp <ContainerID>:<ContainerFilePath> <HostFilePath>
```

- `<ContainerID>` 의 `<ContainerFilePath>` 에 해당하는 파일을 Host 의 `<HostFilePath>` 에 복사한다.

<br><br>

# Reference

- [Docker Documentation - Reference:commandline](https://docs.docker.com/engine/reference/commandline/docker/)

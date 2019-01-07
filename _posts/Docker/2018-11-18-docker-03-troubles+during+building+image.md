---
title: "[Docker] Troubles During Building Image"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Docker

tags:
  - docker
  - image
  - build

---

![Docker-version-18.06.1-ce](https://img.shields.io/badge/Docker-18.06.1--ce-blue.svg)

# Troubles During Building Image

Docker Image 를 빌드하다보면 여러가지 문제가 발생한다.  
그 중 몇가지 문제들에 대해서 정리해보았다.  

<br><br>

## debconf: Warning

`debconf: delaying package configuration, since apt-utils is not installed`

> 해당 경고가 발생해도 이미지 빌드는 진행된다.

- 해결하고자 한다면 아래의 코드를 Dockerfile 에 추가한다.

```dockerfile
ENV DEBCONF_NOWARNINGS yes
```

<br><br>

## noninteractive

일부 패키지를 설치하다보면 설정관련 질의가 나타난다. (tzdata 관련)  
Dockerfile 에서는 답을 줄 수 없으므로 자동적으로 패스가 되어야한다.  
아래의 환경변수 설정이 그러한 문제를 해결해준다.

```dockerfile
ENV DEBIAN_FRONTEND noninteractive
```

- 패키지 설치시 일부 설정관련 질의를 패스  

- 일부 패키지는 설치시 해당 설정값 때문에 오류가 날 수 있으므로 순서를 고려해야 할 수도 있다.

---
title: "[AWS] EC2 - Django Runserver"
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
  - django
  - runserver

---

![AWS-EC2](https://img.shields.io/badge/AWS-EC2-brightgreen.svg)
![AMI-Ubuntu Server 18.04 LTS](https://img.shields.io/badge/AMI-Ubuntu%20Server%2018.04%20LTS-orange.svg)
![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Django Runserver

EC2 인스턴스에서 Django 프로젝트 runserver 로 서버를 띄운 뒤, 외부에서 서버에 접속하는 방법을 작성하였다.  

<br><br>

## Django Project At Instance

아래 두가지 중 어떠한 방식을 써도 상관없다.  
인스턴스 내부에 django project 를 배치시킨다.

- scp 명령어를 통해 로컬에서 django 프로젝트를 업로드

- ssh 명령어를 통해 인스턴스에 직접 접근하여 django 프로젝트 생성

<br><br>

## Edit Inbound Rules  

- 1\. AWS Management Console 에서 EC2 서비스로 이동

- 2\. *네트워크 및 보안* 탭 → *보안 그룹* → 해당 인스턴스의 보안그룹 선택

- 3\. 하단의 *인바운드* 탭 → 편집

- 4\. 규칙 추가 클릭
  - 유형 : 사용지 지정 TCP 규칙
  - 포트범위 : \* (ex. 7777)
  - 소스 : 위치무관
  - 설명 : \* (ex. Django Runserver)

![aws-ec2_django+runserver1](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-05-ec2_django+runserver01.png?raw=true){: width="600px"}

위와 같은 인바운드 규칙을 추가하면 (포트범위 : 7777 으로 가정)  
**외부에서 Port 7777번으로 접속을 허용** 이라는 의미가 된다.

<br>

추가가 완료되면 하단에 아래와 같이 규칙이 추가된 것을 확인할 수 있다.  

![aws-ec2_django+runserver2](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-05-ec2_django+runserver02.png?raw=true){: width="650px"}

이제 인스턴스로 돌아가서 django 의 manage.py 가 있는 경로로 이동하여  
설정한 포트에 맞게 runserver 를 한다.

```
python manage.py runserver 0:7777
```

위와같이 runserver 를 한 뒤, 브라우저에 `<PublicDNS>:7777` 를 입력하면  
접속되는 것 같아 보이지만, Django 에서 `DisallowedHost` 에러가 발생한다.  
마지막으로 이 문제를 해결해보자.

<br><br>

## ALLOWED\_HOSTS

Django 프로젝트의 settings.py 를 살펴보면 `ALLOWED_HOSTS` 라는 이름의 빈 리스트를 확인할 수 있다.

```
ALLOWED_HOSTS = []
```

`ALLOWED_HOSTS` 에 아래와 같이 `'.amazonaws.com'` 을 추가해보자.

```
ALLOWED_HOSTS = [
    '.amazonaws.com',
]
```

리스트에 요소를 추가한 뒤 `<PublicDNS>:7777` 로 접속해보자.  
아래와 같이 정상적으로 django 로 접근했음을 알 수 있다.

> `'.amazonaws.com'` 대신 `'<PublicDNS>'` 를 추가해도 된다.  
> 후자인 `<PublicDNS>` 가 구체적으로 명시해주는 것

![aws-ec2_django+runserver3](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-05-ec2_django+runserver03.png?raw=true){: width="450px"}

<br><br>

## Request Process

Django 페이지로 접근하는 과정은 다음과 같다.

<br>

**HTTP Request → Amazon EC2 → (runserver:7777) → Django**

<br>

위의 과정은 `runserver` 가 *Web Server* 와 *WSGI* 의 역할을 모두 해주고 있는 것과 같다.  
하지만 `runserver` 은 개발서버이므로 성능이 좋지않다.  
실제 서비스를 하기 위해서는 `runserver` 가 아닌 상용화되어있는 *Web Server* 와 *WSGI* 를 사용한다.  

<br>

### Web Server

- HTTP를 통해 클라이언트의 요청(Request)을 받고 응답(Response)을 보낸다.

- 정적데이터를 다룬다.

- 예시 : Apache HTTP Server, Nginx

<br>

### WSGI (Web Server Gateway Interface)

- Web Server 와 Python Web Application 의 연결을 담당한다.

<br>

### Web Application

- 동적데이터를 다룬다.

- Python Web Application 예시 : Django

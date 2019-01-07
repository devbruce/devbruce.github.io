---
title: "[Django] Install / Runserver"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - install
  - project
  - start
  - runserver
  - localhost
  - loopback

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)

# 1. Install django

터미널에서 아래 명령어를 통해 django 를 설치

```bash
$ pip install django
```

<br><br>

# 2. Start a project

Django 설치가 완료되었다면 프로젝트를 시작해보자.  

터미널에서 원하는 디렉토리로 이동하여 아래의 명령어를 입력한다.  
(django 프로젝트 시작 명령어)

```bash
$ django-admin startproject <Project_Name>
```

<br>

명령어를 입력하게되면, 해당 디렉토리에 프로젝트명으로 폴더가 생성된다.  
내부에는 manage.py 와 프로젝트명과 동일한 폴더가 생성된다.  

- 예시

```bash
$ django-admin startproject mysite
```

위의 명령어를 터미널에 입력하게 되면, 아래와 같은 구조가 만들어지게 된다.

```
mysite  ⬅︎ Django 폴더
├── manage.py  ⬅︎ Django 유틸리티 모듈
└── mysite  ⬅︎ Django 설정패키지
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```


<br>

# 3. Run Server

최상단에 있는 manage.py 를 통해 서버를 실행할 수 있다.  
아래 명령어를 입력한다.

```bash
$ python manage.py runserver
```

위 명령어를 입력하게 되면

`127.0.0.1:8000/` 로 서버가 실행되고,  
서버가 실행된 주소로 브라우저를 통해 진입하면 아래 이미지와 같은 결과가 나오게 된다.

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-02-runserver.png?raw=true){: width="400px"}  

## IP Address 127.0.0.1

- `127.0.0.1` 은 "내 컴퓨터" 를 의미한다. (= localhost)  

- Loopback(루프백) 이라고 불리기도 한다. (자신이 요청을 보내고 자신이 요청을 받기때문)  

- `localhost:8000` 로 실행해도 `127.0.0.1:8000` 과 같은 주소로 이동하는 것을 알 수 있다.  

- 8000 은 Port Number 를 의미한다.

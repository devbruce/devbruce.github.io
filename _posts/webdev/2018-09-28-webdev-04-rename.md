---
title: "[Django] Rename (Refactor)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - django
  - python
  - rename
  - refactor

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Rename (Refactor)

project 폴더 내부에서

```bash
django-admin startproject mysite
```

위의 코드를 터미널에 입력하면 아래와 같은 구조가 만들어지게 된다.


```
project  ⬅︎ 프로젝트 폴더 (최상위)
└── mysite  ⬅︎ Django 폴더
    ├── manage.py  ⬅︎ Django 유틸리티 모듈
    └── mysite  ⬅︎ Django 설정패키지
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
        
.gitignore
README.md
... 등등 (Django 이외의 파일들)
```

위의 구조에서 **[최상위 폴더 / Django 폴더]** 는 이름을 바꾸어도 아무런 문제가 발생하지 않는다. (해당 폴더를 참조하는 파일이 없음)  

따라서 아래와 같이 Django 폴더명을 변경가능하다.

```
project  ⬅︎ 프로젝트 폴더 (최상위)
└── app  ⬅︎ Django 폴더 (mysite → app)
    ├── manage.py  ⬅︎ Django 유틸리티 모듈
    └── mysite  ⬅︎ Django 설정패키지
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
        
.gitignore
README.md
... 등등 (Django 이외의 파일들)
```

만약 mysite 폴더가 설정패키지임을 좀 더 쉽게 파악할 수 있도록 폴더명을 변경하고 싶다고 가정해보자. 

당연히, 해당폴더를 참조하고 있는 모든 코드를 수정할 이름에 맞게 바꿔주는  
Refactoring 이 필요할 것이다.  
이 과정을 직접 다하기에는 상당히 귀찮고 힘든 작업이 될 것이다.  
이러한 번거로움을 IDE 에서 제공해주는 기능을 통해 해결할 수 있다.  

<br><br>

## By [Pycharm](https://www.jetbrains.com/pycharm/)

Pycharm의 **Refactor** 기능을 통해 문제를 해결해보자. 
우선, Pycharm을 통해 해당 프로젝트를 오픈한다.  
그 후, 변경할 디렉토리를 선택한다. 현재 예시의 경우에는 Django 설정 패키지이므로,  
mysite 폴더를 선택하여 아래 그림과 같이 Refactor애서 Rename을 선택한다.

![django-refactor1](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/webdev/images/webdev-04-refactor01.png?raw=true){: width="500px"}

Rename을 클릭하면 아래와 같은 창이 나타난다.

![django-refactor2](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/webdev/images/webdev-04-refactor02.png?raw=true){: width="350px"}

위와 같이, 변경할 폴더명을 입력한 뒤, 아래의 체크박스를 모두 체크한다.  
그리고 Refactor 를 클릭한다.

그럼 아래 이미지처럼, Pycharm 하단에 Preview가 나타난다.

![django-refactor3](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/webdev/images/webdev-04-refactor03.png?raw=true){: width="500px"}

하단의 Preview 를 통해 어떤 값들이 자동으로 변경되는지 알 수 있다.  
또한 변경을 원하지 않는 부분을 편집할 수도 있다.  
편집을 완료하면, Do Refactor 를 클릭하여 작업을 완료한다.  
이제, 의도하던바와 같이 폴더명이 수정되었다.  
최종 결과는 아래와 같다.

<br>

**Result**

```
project  ⬅︎ 프로젝트 폴더 (최상위)
└── app  ⬅︎ Django 폴더 (mysite → app)
    ├── manage.py  ⬅︎ Django 유틸리티 모듈
    └── config  ⬅︎ Django 설정패키지 (mysite → config)
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
        
.gitignore
README.md
... 등등 (Django 이외의 파일들)
```

<br>

## Notice

Django 의 app 이름을 Refactoring 을 할 경우 해당 app 의 `apps.py` 는 자동으로 변경되지 않는다.  
`apps.py` 는 직접 수정해주는 것을 잊어서는 안된다.  

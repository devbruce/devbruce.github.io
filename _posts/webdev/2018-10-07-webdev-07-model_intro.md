---
title: "[Django] Model - Intro"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - django
  - python
  - model
  - migrate
  - field
  - verbose_name

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Model (Intro)

## Model Definition

### 1. Intro

Model 을 정의하기 전, 정의할 Model 이 속할 library 라는 이름의 임시 앱을 만들어보겠다.  

```bash
$ python manage.py startapp library
```

library 앱을 만들면 다음과 같은 디렉토리 구조가 될 것이다.

```
app
├── config
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── library
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
└── manage.py
```

<br>

앱을 만들었다면 `config/settings.py` 에서 `INSTALLED_APPS` 에 추가한다.  
추가하게 되면 django 가 해당 앱의 `models.py` 를 로드할 수 있게 한다.  

```python
# config/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'library',  # 새로 만든 app 추가
]
```

<br>

### 2. Define Models in models.py
  
기본적인 셋팅이 끝났다면,  
 `library/models.py` 에 가서 아래와 같이 정의해보자.

```python
# library/models.py

from django.db import models
from django.utils import timezone


class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    pub_date = models.DateTimeField(default=timezone.now)
```

**Model 의 특징은 다음과 같다.**  

- `<AppName>/models.py` 에 Class 형태로 정의되어있다.  

- Class 선언시 `models.Model` 을 상속받는다.  

- Class 변수들은 DB 의 Column (열) 을 담당한다.

- Primary Key 를 따로 지정하지 않았기에, 자동으로 `AutoField` 가 추가된다.

| id 	| title 	| author 	| pub_date 	|
|----	|-------	|--------	|----------	|
|    	|       	|        	|          	|

Book 이라는 이름을 가진 Model 은 위와 같은 형태의 테이블이 된다.  
위의 테이블에서 `id` Column (열) 이 자동추가된 **AutoField** 에 해당한다.  
> `id` 는 데이터가 추가될 때마다 1씩 증가하는 IntegerField 에 해당한다.  
> Reference : [Django Documentation - Model field reference: AutoField](https://docs.djangoproject.com/ko/2.1/ref/models/fields/#django.db.models.AutoField)

<br>

### 3. makemigrations

migration 파일을 생성한다.  

- migration 파일은 model 의 변경사항을 관리한다.

```bash
$ python manage.py makemigrations library
```

위의 명령어는 library 앱의 migration 파일을 생성하도록 한다.  
> `library` 를 생략하면 모든 App 의 migration 파일을 생성한다.

<br>

### 4. migrate

migration 파일을 통해 변경사항을 적용한다.(DB Table 생성)  

```bash
$ python manage.py migrate library
```

> `library` 를 생략하면 모든 App 을 migrate 한다.  
> Project 의 첫 migrate 일 경우, 기본앱을 포함한 전반적인 migrate 가 필요하기 때문에 명시하지 않는 것을 권장

<br><br>

## Model Field

- `CharField` : 문자열 필드 (`max_length` 속성 필수)  
많은 양의 텍스트를 작성할 경우 `TextField` 사용권장

- `EmailField` : 이메일 필드
  - 해당 값이 이메일 주소가 맞는지 `EmailValidator`를 통해 검사하는 `CharField` 의 한 종류

- `TextField` : 제한없는 텍스트 필드

- `IntegerField` : 정수 필드

- `FloatField` : 부동 소수점 수 필드

- `DateField` : 날짜 필드
  - 속성 : `auto_now_add` : Data 생성시 현재시간 저장
  - 속성 : `auto_now` : Data 수정시 현재시간 저장

- `DateTimeField` : 날짜 / 시간 필드
  - 속성 : `auto_now_add` : Data 생성시 현재시간 저장
  - 속성 : `auto_now` : Data 수정시 현재시간 저장


<br>

### Field Option

- `default` : 필드의 기본값 (Value or Callable Object)
  - mutable 객체는 올 수 없다.

- `null` : 빈 값을 DB에 NULL로 저장 (Boolean)
  - 기본값 : False
  - DB 관련 (유효성 검사와 관련 X)

- `blank` : 빈 값 허용 유무 (Boolean)
  - 기본값 : False
  - 유효성 검사관련 (DB 와 관련 X)

- `unique` : 필드의 값이 unique 값이 되도록 설정 (Boolean)

- `primary_key` : 해당 필드를 Primary Key 로 지정 (Boolean)
  - 해당옵션 지정시 `null=False`, `unique=True` 를 포함한다.
  - 정의한 Model 내부에 어떠한 필드에도 지정하지 않을시 Django 에서 Primary Key 를 보유할 `AutoField` 를 자동으로 추가한다.

- `help_text` : 폼 위젯에 표시할 Help 텍스트 (String)

<br><br>

### Verbose Field Name

- 상세이름

- 사용자에게 보여지는 이름

- 소문자로 작성 (첫글자는 Django에서 자동으로 대문자로 변환해준다.)

- 필드의 **첫번째 인수**(Argument)로 문자열을 주게되면, 그 문자열이 필드의 Verbose Name 이 된다.  
아무런 값도 주지 않을경우, Django 필드명으로 자동생성한다.

> `ForeignKey`, `ManyToMany`, `OneToOneField` 의 경우 첫 인수가 클래스가 전달되야 하므로,  
> `verbose_name` keyword argument 를 사용한다.

<br>

#### Example

```python
title = models.CharField("book's title", max_length=100)
```
"book's title" 이라는 Verbose Field Name 설정

<br><br>

# Reference

- [Django Documentation - Migrations](https://docs.djangoproject.com/en/2.1/topics/migrations/)

- [Django Documentation - Model field reference](https://docs.djangoproject.com/ko/2.1/ref/models/fields/)

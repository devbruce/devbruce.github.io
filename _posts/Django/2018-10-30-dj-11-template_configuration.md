---
title: "[Django] Template Configuration"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - template
  - config
  - settings
  - backend
  - jinja

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Template Configuration

## Django 에서의 template 이란?

view 함수로 전달받은 context(Dictionary Type)를 통해 Dynamic(동적)한 HTML 을 생성하는 파일(.html)

<br>

## settings.py

`settings.py` 에서 `TEMPLATES` 리스트에서 template 관련 설정이 가능하다.  

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

<br>

### BACKEND

`'BACKEND': 'django.template.backends.django.DjangoTemplates'` 의 의미는 Django 의 기본 template engine을 사용한다는 의미이다. `BACKEND` 의 값을 바꿀경우 다른 template engine 을 사용가능하다. 대표적으로 [jinja2](http://jinja.pocoo.org/) 가 있다.  
jinja2 의 경우 아래와 같이 `BACKEND` 값을 변경한다.  

`'BACKEND': 'django.template.backends.jinja2.Jinja2'`  

하지만, template engine 을 변경하기 위해서는 단순히 `BACKEND` 만을 변경해서는 사용할 수 없다. 구체적인 변경법은 추후 포스팅을 하겠다.

<br>

### APP\_DIRS

`APP_DIRS : True` 로 설정이 되어있기 때문에 해당 app 의 templates 디렉토리에서 template 을 Django가 검색할 수가 있다.  
(`python manage.py startapp myapp` 을 통해 app을 새로 만들면 **templates 디렉토리는 자동으로 생성되지 않는다.** 개발자가 직접 만들어줘야하는 디렉토리이다.)

<br>

**Example**

```
project
├── config
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── myapp  <-- 새로만든 app
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    ├── models.py
    ├── templates  ← 'APP_DIRS':True 에 의해 template 파일 검색가능
    ├── tests.py
    └── views.py
```

<br>

### DIRS

개발자가 수동으로 template 파일의 검색경로를 추가할 수 있도록 해주는 요소이다.  
해당 리스트에 `os.path.join(BASE_DIR, 'tmp_templates')` 라고 추가할 경우, 추가된 경로를 template 검색경로에 추가되어 template 파일을 찾을 때 검색하게 된다.

<br>

**Example**

```
project
├── config
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
├── tmp_templates  ← DIRS 에 추가되어 template 파일 검색가능해진 디렉토리
└── myapp  ← 새로만든 app
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    ├── models.py
    ├── templates  ← 'APP_DIRS':True 에 의해 template 파일 검색가능
    ├── tests.py
    └── views.py
```

<br>

### OPTIONS

`BACKEND` 로 지정한 template engine 의 구체적인 설정값을 지정할 수 있다.  

```python
'context_processors': [
    'django.template.context_processors.debug',
    'django.template.context_processors.request',
    'django.contrib.auth.context_processors.auth',
    'django.contrib.messages.context_processors.messages',
],
```
OPTIONS 의 기본값으로는 위의 값이 지정되어있다.  
`context_processors` 리스트에 추가되어있는 값은 view 함수에서 context 로 전달받지 않아도 template 에서 불러올 수 있는 값을 의미한다.  
예시로, `'django.contrib.auth.context_processors.auth'` 가 추가되어 있기에 template 에서 `user` 라는 변수를 context 로 전달받지않아도 사용 가능하다.

<br><br>

## Reference

- [Django Documentation - templates](https://docs.djangoproject.com/ko/2.1/topics/templates/)

- [Jinja](http://jinja.pocoo.org/)

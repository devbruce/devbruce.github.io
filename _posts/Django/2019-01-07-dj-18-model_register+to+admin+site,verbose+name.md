---
title: "[Django] Model - Register To Admin Site / Verbose Name"
excerpt: 
last_modified_at: 2019-01-07

categories:
  - Django

tags:
  - django
  - python
  - model
  - admin site
  - register
  - verbose_name
  - verbose_name_plural

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Preparation

해당 포스트는 아래의 상황을 전제로 한다.

- Django 에서 firstapp 이라는 앱 생성

- `settings.py` 의 `INSTALLED_APPS` 에 firstapp 을 추가

- firstapp 의 `models.py` 를 작성 (내용은 아래와 같다.)

```python
from django.db import models


class FirstModel(models.Model):
    name = models.CharField(max_length=15)


class SecondModel(models.Model):
    name = models.CharField(max_length=15)
```

- `makemigrations` 와 `migrate` 를 한다.

- `createsuperuser` 로 관리자계정 생성

이러한 상태에서 admin site 에 진입할 경우  
아래의 이미지와 같이 정의한 모델이 나타나지 않고 비어있음을 알 수 있다.  

<br>

![dj-18-admin01](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin01.png?raw=true){: width="550px"}  

<br><br>

# Register Models to Admin Site

정의한 모델을 admin site 에서 관리하기 위해서는 등록하는 과정이 필요하다.  
해당 앱의 `admin.py` 에서 이 과정이 이루어진다.  

admin site 에서 관리하고 싶은 모델을 아래와 같이 추가할 수 있다.  

```python
# app/firstapp/admin.py
from django.contrib import admin
from .models import FirstModel, SecondModel  # 정의한 모델 불러오기

admin.site.register(FirstModel)  # FirstModel 을 Admin 페이지에 등록
admin.site.register(SecondModel)  # SecondModel 을 Admin 페이지에 등록
```

<br>

![dj-18-admin02](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin02.png?raw=true){: width="550px"}  

해당 모델이 admin site 에 추가되었음을 알 수 있다.

<br><br>

# Set Verbose Name Of Apps

admin site 에서 아래의 이미지에 표시된 부분을 변경하기 위해서는  
app 의 `verbose_name` 을 변경하여야 한다.  

<br>

![dj-18-admin03](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin03.png?raw=true){: width="550px"}  

해당 앱의 `apps.py` 를 열어서 아래와 같이 `verbose_name` 을 추가한다.

```python
# app/firstapp/apps.py
from django.apps import AppConfig


class FirstappConfig(AppConfig):
    name = 'firstapp'
    verbose_name = '첫번째 앱'
```

지금은 admin site 가 app 의 `verbose_name` 을 반영하지 못한다.  
해당 내용이 반영되기 위해서는 `settings.py` 의 `INSTALLED_APPS` 를 변경해줄 필요가 있다.

```python
# app/config/settings.py
INSTALLED_APPS = [
    'firstapp',

    . . .
]
```

> 위와같이 app 을 추가한 상태에서는 firstapp 의 `apps.py` 의 내용을 인식하지 못한다.  
> 따라서 아래와 같이 구체적으로 지정해주어야 한다.

```python
# app/config/settings.py
INSTALLED_APPS = [
    'firstapp.apps.FirstappConfig',

    . . .
]
```

이제 admin site 를 살펴보자.  

<br>

![dj-18-admin04](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin04.png?raw=true){: width="550px"}  

app 의 `verbose_name` 이 적용되었다.

<br><br>

# Set Verbose Name Of Models

```python
# app/firstapp/models.py
from django.db import models


class FirstModel(models.Model):
    name = models.CharField(max_length=15)

    class Meta:
        verbose_name = '첫번째 모델'


class SecondModel(models.Model):
    name = models.CharField(max_length=15)

    class Meta:
        verbose_name = '두번째 모델'
```

Model 의 `verbose_name` 은 위와 같이 정의할 수 있다.  
변경된 사항을 저장한 뒤 admin site 를 살펴보면 아래의 이미지처럼  
`verbose_name` 이 적용된다.  

<br>

![dj-18-admin05](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin05.png?raw=true){: width="550px"}  

`verbose_name` 끝에 `s` 가 붙어있는데,  
`s` 는 `verbose_name_plural`(verbose name 복수) 과 관련된 것으로  
`verbose_name + s` 가 기본값이 된다.  

`verbose_name_plural`도 직접 지정하여 변경가능하다.  

```python
# app/firstapp/models.py
from django.db import models


class FirstModel(models.Model):
    name = models.CharField(max_length=15)

    class Meta:
        verbose_name = '첫번째 모델'
        verbose_name_plural = '첫번째 모델 복수'


class SecondModel(models.Model):
    name = models.CharField(max_length=15)

    class Meta:
        verbose_name = '두번째 모델'
        verbose_name_plural = '두번째 모델 복수'
```

<br>

![dj-18-admin06](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-18-admin06.png?raw=true){: width="550px"}  

변경된 값이 적용되었다.

<br><br>

# Reference

- [Django Documentation - The Django admin site](https://docs.djangoproject.com/en/2.1/ref/contrib/admin/)

- [Django Documentation - Applications](https://docs.djangoproject.com/en/2.1/ref/applications/)

- [Django Documentation - Model Meta options](https://docs.djangoproject.com/ko/2.1/ref/models/options/)

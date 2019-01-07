---
title: "[Django] Model - Many To Many (Recursive Relationship)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - model
  - many-to-many
  - recursive
  - self
  - symmetrical
  - ORM

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Many To Many

Many To Many 의 기본 사용법 참조 : [[Django] Model - Many To Many](https://devbruce.github.io/django/dj-14-model_many+to+many/)

<br>

## Recursive Relationship

Recursive Relationship 이란 다른 모델의 인스턴스와 관계를 형성하는 것이 아닌  
자기 자신의 인스턴스와 관계를 형성할 때 사용한다.  

예시를 위해 tmp 라는 온라인 게임이 있다고 가정하겠다.  
해당 게임은 회원가입을 통해 아이디를 생성한 일반 유저들의 데이터를 관리하기 위해  
`GeneralUser` 라는 모델을 사용한다.  
회원가입을 한 유저들의 데이터가 `GeneralUser` 모델의 형식에 맞게 형성된다.  

게임이 운영되던 때, 게임을 진행하던 유저 A 가 유저 B 를 친구추가하였다.  
이제 유저 A 와 유저 B 사이에 관계가 형성된다.  
그런데 **유저 A, 유저 B 둘 다 `GeneralUser` 라는 모델의 인스턴스이다.**  
다른 모델이 아닌 자기 자신의 인스턴스끼리 관계를 형성하는 형태이다.  
이러한 관계를 Recursive relationship 이라고 한다.  

Recursive relationship 을 설정하는 방법은 간단하다.  
관계를 형성할 모델대신 `'self'` 를 할당해준다.  

<br><br>

## Django Model Code

```python
from django.db import models


class GeneralUser(models.Model):
    username = models.CharField(max_length=20)
    friends = models.ManyToManyField('self')

    def __str__(self):
        return self.username
```

<br><br>

## Practice

위의 구현된 모델을 통해 실제 인스턴스(객체)를 생성하고 확인해보자.  

> 실습을 위해 tmpgame 이라는 이름을 가진 app 을 생성하고 해당 앱의 models.py 에 구현코드를 작성한다.  
> 이후 `makemigrations` 와 `migrate` 를 하였음을 가정한다.

```python
from tmpgame.models import GeneralUser

a = GeneralUser.objects.create(username='A')  # 유저 A 생성
b = GeneralUser.objects.create(username='B')  # 유저 B 생성
c = GeneralUser.objects.create(username='C')  # 유저 C 생성

a.friends.add(b, c)  # 유저 A 가 유저 B, 유저 C 친구추가
```

```python
a.friends.all()
```
\>\>\> `<QuerySet [<GeneralUser: B>, <GeneralUser: C>]>`  

유저 A 의 친구 목록에 유저 B, 유저 C 가 추가되었음을 확인할 수 있다.

<br><br>

## Notice (symmetrical)

#### Many To Many + Recursive relationship

- 기본적으로 대칭관계 형성 (Default: `symmetrical=True`)

해당 관계는 django 가 역방향 관계에 대한 descriptor 을 추가하지 않기 때문에 대칭적 관계로 형성된다.  
일방적인(비대칭) 관계를 형성하고자 할 경우 `symmetrical=False` 옵션을 `ManyToManyField` 에 추가한다.  

<br>

#### Many To Many + Recursive relationship + Intermediary Model

- 기본적으로 대칭관계 형성하지 않는다. (Default: `symmetrical=False`)

> Intermediary Model 관련 참조 : [[Django] Model - Many To Many (Intermediary Model)](https://devbruce.github.io/django/dj-15model_many+to+many_intermediary+model/)

<br><br>

### Example

유저 B 와 유저 C 의 친구 목록을 확인해보자.  

```python
b.friends.all()
```
\>\>\> `<QuerySet [<GeneralUser: A>]>`  

```python
c.friends.all()
```
\>\>\> `<QuerySet [<GeneralUser: A>]>`  

<br>

위의 Practice 를 살펴보면  
유저 B 는 유저 A 를 친구추가 한 적 없다.  
유저 C 도 유저 A 를 친구추가 한 적 없다.  

그럼에도 불구하고 이 두 유저의 친구 목록에는 유저 A 가 추가되었다.  
해당 관계는 대칭관계가 설정되어 있음을 알 수 있다.

<br><br>

## DataBase

### Table List

- tmpgame\_generaluser

- tmpgame\_generaluser\_friends

<br>

#### tmpgame\_generaluser

| id | username |
|----|----------|
| 1  | A        |
| 2  | B        |
| 3  | C        |

<br>

#### tmpgame\_generaluser\_friends

| id | from\_generaluser\_id | to\_generaluser\_id |
|----|-----------------------|---------------------|
| 1  | 1                     | 3                   |
| 2  | 1                     | 2                   |
| 3  | 3                     | 1                   |
| 4  | 2                     | 1                   |

해당모델이 기본적으로 대칭관계를 형성함에 따라  
id=3, id=4 (3번째, 4번째 데이터) 같은 관계가 자동으로 생성되어 있음을 확인할 수 있다.

<br><br>

# Reference

- [Django Documentation - Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

- [Django Documentation - Fields](https://docs.djangoproject.com/en/2.1/ref/models/fields/)

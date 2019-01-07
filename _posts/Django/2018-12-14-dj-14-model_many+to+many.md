---
title: "[Django] Model - Many To Many"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - model
  - many-to-many
  - ORM

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Many To Many

- 다대다 관계 (M : N)

- Django 에서는 해당 관계를 정의하기 위해 `django.db.models.ManyToManyField` 를 사용

- 관계를 생성하게 되면 관계 연결을 위한 중간 테이블(Intermediate Table)이 자동생성된다.  
  - 이에따라 `1 : M : 1`  관계형태로 변형된다.
  - `ManyToManyField` 의 속성 `through`로 중간 테이블을 직접 제작한 모델로 변경가능하다. (커스터마이징)  
  해당 내용은 [[Django] Model - Many To Many (Intermediary Model)](https://devbruce.github.io/django/dj-15-model_many+to+many_intermediary+model/) 포스트를 참조

<br>

## Exmaple

이 포스트에서는 마켓(market)에서 손님(Customer)과 과일(Fruit)을 예시로 사용한다.  

<br>

**상황가정**  

- 손님 a 의 구매목록 : apple, orange, grape

- 손님 b 의 구매목록 : orange, grape

<br>

### Visualization

![dj-mtm](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-14-mtm01.png?raw=true){: width="500px"}  

<br>

위의 관계는 중간 테이블 (Intermediate Table) 이 생성됨에 따라  
`1 : M : 1` 형태가 된다. (아래 이미지 참조)  

![dj-mtm2](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Django/images/dj-14-mtm02.png?raw=true){: width="650px"}  

<br><br>

## Django Model Code

`ManyToManyField` 를 `Customer` 또는 `Fruit` 어디에 추가하더라도 Many To Many 관계로써 동일하다.  

> Many To One 에 해당하는 ForeignKey 의 경우 관계설정이 **단 한개**만 되기 때문에  
> 연결될 두 모델 중 어느곳에 추가되느냐에 따라 의미가 달라진다.

- 연결할 수 있는 관계의 수에 제한이 없다.

- 두 모델의 관계는 자동생성되는 Intermediate Table 에 의해 관리되기 때문에 **해당 모델의 테이블에는 필드가 생성되지 않는다.**  

그래도 가능하면 의미상 어울리는 곳에 필드를 만들고 관계를 연결하는 것을 권장한다.  
아래의 코드는 상대적으로 주체적인 `Customer` 모델에 `Fruit`를 연결하는 `ManyToManyField`를 추가하였다.  

```python
from django.db import models


class Customer(models.Model):
    name = models.CharField(max_length=20)
    choice = models.ManyToManyField('Fruit', related_name='buyers')

    def __str__(self):
        return self.name


class Fruit(models.Model):
    name = models.CharField(max_length=20)

    def __str__(self):
        return self.name
```

- `choice = models.ManyToManyField('Fruit', related_name='buyers')` 에서  
`Fruit` 모델 클래스를 따음표로 감싸 문자열로 만든 뒤 변환한 이유는  
`Fruit` 클래스가 정의되기전 할당되었기 때문이다. (`Fruit` 클래스는 `Customer` 클래스 이후 정의되었다.)  
이렇게 문자열로 바꾸어서 할당해주면 클래스 정의 순서에 제한받지 않고 작성할 수 있다.  

- `related_name='buyers'` : 역방향 참조의 기본값 `customer_set` 에서 `buyers` 로 변경

<br><br>

## Practice

위의 구현된 모델을 통해 실제 인스턴스(객체)를 생성하고 확인해보자.  

> 실습을 위해 market 이라는 이름을 가진 app을 생성하고 해당 앱의 models.py 에 구현코드를 작성한다.  
> 이후 `makemigrations` 와 `migrate` 를 하였음을 가정한다.

```python
from market.models import Customer, Fruit


a = Customer.objects.create(name='a')  # 손님 a 생성
b = Customer.objects.create(name='b')  # 손님 b 생성

apple = Fruit.objects.create(name='apple')  # 과일 apple 생성
grape = Fruit.objects.create(name='grape')  # 과일 grape 생성
orange = Fruit.objects.create(name='orange')  # 과일 orange 생성
```

```python
a.choice.add(apple)  # a 의 선택에 apple 추가
a.choice.add(grape)  # a 의 선택에 grape 추가
a.choice.add(orange)  # a 의 선택에 orange 추가

b.choice.add(grape)  # b 의 선택에 grape 추가
b.choice.add(orange)  # b 의 선택에 orange 추가
```

```python
a.choice.all()  # a 의 선택목록
```
\>\>\> `<QuerySet [<Fruit: apple>, <Fruit: grape>, <Fruit: orange>]>`

<br>

```python
b.choice.all()  # b 의 선택목록
```
\>\>\> `<QuerySet [<Fruit: grape>, <Fruit: orange>]>`

<br>

```python
apple.buyers.all()  # apple 을 구매한 손님 목록
```
\>\>\> `<QuerySet [<Customer: a>]>`

<br>

```python
grape.buyers.all()  # grape 를 구매한 손님 목록
```
\>\>\> `<QuerySet [<Customer: a>, <Customer: b>]>`

<br>

```python
orange.buyers.all()  # orange 를 구매한 손님 목록
```
\>\>\> `<QuerySet [<Customer: a>, <Customer: b>]>`

<br><br>

## DataBase

> Table Name은 `AppName_ClassName` 소문자화로 만들어진다.

<br>

### Table List

- market\_customer

- market\_fruit

- **market\_customer\_choice** (자동 생성된 Intermediate Table)  
  - `AppName_ClassName_FieldName` (소문자) 형태로 만들어진 것을 알 수 있다.  

<br>

#### market\_customer

| id | name |
|----|------|
| 1  | a    |
| 2  | b    |

<br>

#### market\_fruit

| id | name   |
|----|--------|
| 1  | apple  |
| 2  | grape  |
| 3  | orange |

<br>

#### market\_customer\_choice

| id | customer_id | fruit_id |
|----|-------------|----------|
| 1  | 1           | 1        |
| 2  | 1           | 2        |
| 3  | 1           | 3        |
| 4  | 2           | 2        |
| 5  | 2           | 3        |


<br>

- `customer_id` : market\_customer 으로 연결된 ForeignKey

- `fruit_id` : market\_fruit 으로 연결된 ForeignKey

- 해당 Table 을 통해 `M : N` 관계에서 `1 : M : 1` 로 변형

<br><br>

# Reference

- [Django Documentation - Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

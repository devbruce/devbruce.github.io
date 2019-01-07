---
title: "[Django] Model - Many To Many (Intermediary Model)"
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
  - through
  - intermediary model
  - foreignkey

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Many To Many

Many To Many 의 기본 사용법 참조 : [[Django] Model - Many To Many](https://devbruce.github.io/django/dj-14-model_many+to+many/)

<br>

## Intermediary Model

Many To Many 관계를 생성하면 `M : N` 의 관계를 `1 : M : 1` 로 변형될 수 있도록  
Intermediate Table 이 자동생성된다.  

하지만 자동생성되는 Intermediate Table 의 경우에는 ForeginKey 를 통해 두개의 테이블을  
연결시켜줄 뿐 다른 데이터는 없다.

데이터를 추가하고 싶은 경우 (연결관계가 생성된 시간, 추가설명 등) 자동생성 테이블이 아닌 직접 모델을 만들어야한다.  
그 때 직접 만든 모델을 Intermediary Model 이라고 하며 직접 생성 후,  
`ManyToManyField` 의 속성 `through` 를 통해 지정할 수 있다.

<br><br>

## Django Model Code

[[Django] Model - Many To Many](https://devbruce.github.io/django/dj-14-model_many+to+many/) 에서 사용한 모델 코드에 Intermediary Model 을 생성해보겠다.  
아래의 Intermediary Model 에서는 `Customer` 와 `Fruit` 의 관계에서  
구매시간(time), 구매수량(quantity)을 추가하였다.

```python
from django.db import models


class Customer(models.Model):
    name = models.CharField(max_length=20)
    choice = models.ManyToManyField(
        'Fruit',
        through='Purchase',  # Specify Intermediary Model
        related_name='buyers',
        )

    def __str__(self):
        return self.name

# -- Intermediaty Model -- #

class Purchase(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)
    fruit = models.ForeignKey('Fruit', on_delete=models.CASCADE)
    quantity = models.IntegerField()
    time = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return '{} : {} ({})'.format(
            self.customer.name,
            self.fruit.name,
            self.quantity
        )
    
# ------------------------ #

class Fruit(models.Model):
    name = models.CharField(max_length=20)

    def __str__(self):
        return self.name
```

- `DateTimeField`의 `auto_now_add=True` 속성을 통해 해당 데이터가 생성되는 시간을 자동으로 필드값을 채워준다.

> `auto_now` 는 데이터가 생성된 시간 + 수정된 시간을 자동으로 필드값을 채운다.

<br><br>

## Practice

위의 구현된 모델을 통해 실제 인스턴스(객체)를 생성하고 확인해보자.  

> 실습을 위해 market 이라는 이름을 가진 app을 생성하고 해당 앱의 models.py 에 구현코드를 작성한다.  
> 이후 `makemigrations` 와 `migrate` 를 하였음을 가정한다.

```python
from market.models import Customer, Fruit, Purchase


a = Customer.objects.create(name='a')  # 손님 a 생성
b = Customer.objects.create(name='b')  # 손님 b 생성

apple = Fruit.objects.create(name='apple')  # 과일 apple 생성
grape = Fruit.objects.create(name='grape')  # 과일 grape 생성
orange = Fruit.objects.create(name='orange')  # 과일 orange 생성
```

```python
# 손님 : a / 과일 : apple / 수량: 1
Purchase.objects.create(customer=a, fruit=apple, quantity=1)

# 손님 : a / 과일 : grape / 수량: 2
Purchase.objects.create(customer=a, fruit=grape, quantity=2)

# 손님 : a / 과일 : orange / 수량: 3
Purchase.objects.create(customer=a, fruit=orange, quantity=3)

# 손님 : b / 과일 : grape / 수량: 7
Purchase.objects.create(customer=b, fruit=grape, quantity=7)

# 손님 : b / 과일 : orange / 수량: 3
Purchase.objects.create(customer=b, fruit=orange, quantity=3)
```

```python
Purchase.objects.all()  # Purchase List
```
\>\>\> `<QuerySet [<Purchase: a : apple (1)>, <Purchase: a : grape (2)>, <Purchase: a : orange (3)>, <Purchase: b : grape (7)>, <Purchase: b : orange (3)>]>`

<br><br>

## Notice

Intermediate Model 를 정의하고 `through` 로 지정한 `ManyToManyField` 는  
아래의 메서드를 사용할 수 없다.

- `add()`
- `create()`
- `set()`
- `remove()`

```python
a.choice.add(apple)
```
\>\>\> `AttributeError`  

<br>

**원인** : Intermediary Model 을 정의함에 따라 단순히 `Customer` 와 `Fruit` 의 관계만(ForeginKey)을 추가, 제거하는 것이 아니다.  
이 두 모델의 관계를 만들기 위해서는 (관계 데이터) + `quantity`, `time` 이 필요하다.  
따라서 두 모델의 관계를 추가하기 위해서는 Intermediary Model 에 해당하는 `Purchase` 모델을 호출하여 생성하여야 한다.  

```python
Purchase.objects.create(customer=<customer>, fruit=<fruit>, quantity=<int>)
```

**하지만 `clear()` 는 사용가능하다.**  

`clear()` 의 경우는 어떠한 값이 필요한지는 신경쓰지 않는다.  
그냥 모두 지우는 것이기 때문이다.  

```python
a.choice.clear()
a.choice.all()
```
\>\>\> `<QuerySet []>`

<br>

역방향 참조는 문제없이 작동한다.  

```python
grape.buyers.all()
```
\>\>\> `<QuerySet [<Customer: a>, <Customer: b>]>`

<br><br>

## DataBase

> Table Name은 `AppName_ClassName` 소문자화로 만들어진다.

<br>

### Table List

- market\_customer

- market\_fruit

- **market\_purchase** (직접 생성한 Intermediary Model 에 의해 생성된 Table)  

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

#### market\_purchase

`ManyToManyField` 의 `through` 로 지정된 테이블

| id | quantity | time                       | customer_id | fruit_id |
|----|----------|----------------------------|-------------|----------|
| 1  | 1        | 2018-12-17 --:--:--.------ | 1           | 1        |
| 2  | 2        | 2018-12-17 --:--:--.------ | 1           | 2        |
| 3  | 3        | 2018-12-17 --:--:--.------ | 1           | 3        |
| 4  | 7        | 2018-12-17 --:--:--.------ | 2           | 2        |
| 5  | 3        | 2018-12-17 --:--:--.------ | 2           | 3        |

<br>

- `quantity` : 구매수량

- `time` : 구매시간 (`auto_now_add=True` 속성에 의해 생성시 생성시간이 자동으로 채워진다.)

- `customer_id` : market\_customer 으로 연결된 ForeignKey (자동생성되는 테이블에 기본적으로 있는 필드)

- `fruit_id` : market\_fruit 으로 연결된 ForeignKey (자동생성되는 테이블에 기본적으로 있는 필드)

<br><br>

# Reference

- [Django Documentation - Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

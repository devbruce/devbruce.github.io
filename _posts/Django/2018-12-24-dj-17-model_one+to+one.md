---
title: "[Django] Model - One To One"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - model
  - one-to-one

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# One To One

- `1 : 1` 관계

- ForeignKey 에 `unique=True` 옵션을 준 것과 의미상 동일  

- 중복값이 없기 때문에(`unique=True`) Primary Key 로 지정가능

- 역방향 참조를 할 경우 `related_name` 의 기본값이  
`classname_set` 이 아닌 `classname` 이 된다.  

- 참조의 반환값(역방향 참조 포함)이 단일 인스턴스이다. (QuerySet 이 아니다.)  

<br><br>

## Example

예시로 임의의 온라인게임과 유저가 있고,  
유저에게는 단 한개의 캐릭터만 생성이 가능하다.  
캐릭터는 다음과 같은 데이터를 가지고 있다.  

- 유저네임 (username)

- 레벨 (level)

- 직업 (selected\_class)

- 유저의 private data (private\_data) (`OneToOneField`)

유저의 private data 는 다른 테이블에서 따로 관리하고자 한다.  

<br><br>

## Django Model Code

```python
from django.db import models


class Character(models.Model):
    username = models.CharField(max_length=20)
    level = models.IntegerField()
    selected_class = models.CharField(max_length=10)
    private_data = models.OneToOneField(
        'PrivateData',
        on_delete=models.CASCADE
    )

    def __str__(self):
        return self.username


class PrivateData(models.Model):
    name = models.CharField(max_length=20)
    phone_number = models.CharField(max_length=30)
    address = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```

<br><br>

## Practice

위의 구현된 모델을 통해 실제 인스턴스(객체)를 생성하고 확인해보자.  

> 실습을 위해 oto 라는 이름을 가진 app 을 생성하고 해당 앱의 models.py 에 구현코드를 작성한다.  
> 이후 `makemigrations` 와 `migrate` 를 하였음을 가정한다.

```python
# Alice 와 Bruce 의 Private Data 생성
alice = PrivateData.objects.create(name='Alice', phone_number='000-0000', address='A city')
bruce = PrivateData.objects.create(name='Bruce', phone_number='111-1111', address='B city')

# 유저 aaa (Alice) 와 유저 bbb (Bruce) 를 생성
aaa = Character.objects.create(username='aaa', level=30, selected_class='Wizard', private_data=alice)
bbb = Character.objects.create(username='bbb', level=50, selected_class='Warrior', private_data=bruce)
```

```python
aaa.private_data
```
\>\>\> `<PrivateData: Alice>`

```python
bbb.private_data
```
\>\>\> `<PrivateData: Bruce>`

```python
alice.character  # 역방향 참조
```
\>\>\> `<Character: aaa>`

```python
bruce.character  # 역방향 참조
```
\>\>\> `<Character: bbb>`

<br><br>

## Notice

One To One 관계인 만큼 이미 관계를 가진 데이터는 다른 데이터에서 연결할 수 없다.  

```python
ccc = Character.objects.create(username='ccc', level=999, selected_class='Warrior', private_data=bruce)
```
\>\>\> `IntegrityError: UNIQUE constraint failed: oto_character.private_data_id`  

ccc 의 private_data 의 대상을 bruce 로 지정하였다.  
하지만 bruce 는 이미 bbb 의 private\_data 로 지정되어 있기 때문에  
위와 같은 에러가 발생함을 알 수 있다.

<br><br>

## DataBase

### Table List

- oto\_character

- oto\_privatedata

<br>

#### oto\_character

| id | username | level | selected_class | private\_data\_id |
|----|----------|-------|----------------|-------------------|
| 1  | aaa      | 30    | Wizard         | 1                 |
| 2  | bbb      | 50    | Warrior        | 2                 |


<br>

#### oto\_privatedata

| id | name  | phone_number | address |
|----|-------|--------------|---------|
| 1  | Alice | 000-0000     | A city  |
| 2  | Bruce | 111-1111     | B city  |


<br><br>

# Reference

- [Django Documentation - Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

- [Django Documentation - Fields](https://docs.djangoproject.com/en/2.1/ref/models/fields/)

- [Django Documentation - One-to-one relationships](https://docs.djangoproject.com/en/2.1/topics/db/examples/one_to_one/)

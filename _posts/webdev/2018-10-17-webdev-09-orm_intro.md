---
title: "[Django] ORM - Intro"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - django
  - python
  - ORM
  - queryset

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# ORM (Intro)
  
Object Relational Mapping (객체 관계 매핑)
      
- 데이터베이스(DB) 테이블과 객체 지향 프로그래밍 언어에서의 객체를 Mapping 해주는 기법

- SQL Query 를 직접 작성하지 않고, 해당 언어의 코드로 간결하게 작성 가능

<br>

## QuerySet
  
- Iterable Data

- Model Class 에 의해 생성된 인스턴스(객체) 모음 (\=\= Row 데이터 모음)

- 반환받은 QuerySet 은 인덱싱과 슬라이스를 통해 SubSet 을 가져올 수 있다. (음수 `-` 는 지원하지 않는다.)
> 슬라이싱 `[:5]` 의 경우 SQL 의 `LIMIT 5` 와 동일  
> 슬라이싱 `[5:10]`의 경우 SQL 의 `OFFSET 5 LIMIT 5` 와 동일

<br>

## How To Use Model in Django

Django에서 Model 은 `<AppName>/models.py` 에 정의되어있다.  
그러므로 Model 을 사용하기 위해선 아래와 같이 import를 통해 가져와야한다.  

```python
from <AppName>.models import <Model1>, <Model2>, <Model3>, ... 
```

`<ModelName>.objects.<Method>` 형태로 Query 를 작성

> `objects` 는 Django 의 기본 Model Manager 에 해당한다.  
> 필요하다면 변경하는 것도 가능하다.

<br>

### Example

```python
from TmpApp.models import TmpModel  # TmpModel 을 불러온다.

TmpModel.objects.all()  # TmpModel 의 모든 객체를 반환받는다.
```

<br><br>

## Method

각 메서드는 필요하다면 연결가능하다. (Chaining)

**Example**

```python
TmpModel.objects.filter(
    pk__gte=1  # pk 가 1 이상인 값
    ).filter(
    name__contains="A"  # name 에 "A" 를 포함한 값
    ).exclude(
    name__endswith="e"  # name 끝이 "e" 로 끝나는 값 제외
    )
```

<br>

### save()

```python
data = TmpModel(name='Bruce', job='developer')  # TmpModel 인스턴스 생성
data.save()  # DB 저장
```

- 위의 예시코드는 `create()` 예시코드와 동일한 결과 

- 인스턴스(Row) 데이터 생성뿐만 아니라 변경 사항을 DB에 저장할 때 사용

<br>

### create()

```python
TmpModel.objects.create(name='Bruce', job='developer')
```

- 해당 모델의 인스턴스(Row)를 생성한다.

- `save()` 없이 자동으로 DB에 저장돤다.

<br>

### delete()

```python
TmpModel.objects.get(pk=1).delete()  # pk=1 인 인스턴스(Row) 데이터 제거
TmpModel.objects.filter(name__contains='q').delete()  # name 에 q를 포함한 인스턴스(Row) 모두 제거
```

- 인스턴스(Row) 데이터 제거

<br>

### all()

```python
TmpModel.objects.all()
```

- 모든 인스턴스(Row)를 QuerySet 으로 반환해준다.  

<br>

### get()

```python
TmpModel.objects.get(pk=1, name='tmp')
```

- 조건에 해당하는 하나의 인스턴스(Row)를 가져온다. (QuerySet 형태가 아닌, **하나의 인스턴스를 반환**)

- `get()` 메서드의 인수(argument) 로 여러가지 조건을 줄 수 있다. (`AND` 연산)

- 2개이상의 값이 반환되면 에러를 반환한다.

<br>

### values()

```python
TmpModel.objects.values('pk', 'name')
``` 
\>\>\> `<QuerySet [{'pk': 1, 'name': 'A'}, {'pk': 2, 'name': 'B'}]>`  

- 인수로 전달된 Column(열) 데이터 반환 (SQL Query 의 `SELECT` 와 동일)

- QuerySet 의 각 요소가 **Dictionary 형태로 반환**

<br>

### filter()

```python
TmpModel.objects.filter(pk__gte=1)  # pk가 1보다 큰 경우
```

- 위의 예시는 `TmpModel.objects.all().filter(pk__gte=1)` 과 동일 (`all()` 과 Chaining)

- 조건을 충족하는 1개 이상의 인스턴스(Row)를 QuerySet 형태로 반환한다.

- 메서드의 인수(argument) 로 여러가지 조건을 줄 수 있다. (`AND` 연산)

<br>

### exclude()

```python
TmpModel.objects.exclude(pk__lte=2)  # pk가 2보다 작거나 같은 경우 제외
```

- 조건을 충족하는 1개 이상의 인스턴스(Row)를 제외한 QuerySet 을 반환한다.

- 메서드의 인수(argument) 로 여러가지 조건을 줄 수 있다. (`AND` 연산)

<br>

### order\_by()

```python
TmpModel.objects.all().order_by('-pk', 'name')  # pk기준 내림차순 정렬 이후 name 기준 오름차순 정렬
```

- QuerySet 정렬 메서드

- 인수를 여러개 전달할 경우, 주어진 인수 중 왼쪽에서부터 순서대로 정렬

- `-` 를 붙일 시, 내림차순(Descending) 으로 정렬 (Default = Ascending)

- `?` 를 인수로 전달 시, 랜덤정렬

<br>

### reverse()

```python
TmpModel.objects.order_by('name').reverse()  # name 기준 오름차순 정렬 후, 역순으로 정렬
```

- QuerySet 순서 반대로 정렬

- 순서가 정의되어있는 QuerySet 의 경우에만 유효하다.

<br>

### exists()

```python
TmpModel.objects.filter(name__icontains="q").exists()  # name 중 q를 포함하고 있는 인스턴스(Row) 존재여부
```

- QuerySet 에 어떠한 값이라도 존재한다면 `True`, 없다면 `False` 를 반환한다.

<br>

### first()

```python
TmpModel.objects.all().first()  # TmpModel 의 모든 인스턴스(객체) 중 첫번 째 인스턴스 반환
```

- QuerySet 의 첫번째 값 반환

<br>

### last()

```python
TmpModel.objects.all().last()  # TmpModel 의 모든 인스턴스(객체) 중 마지막 인스턴스 반환
```

- QuerySet 의 마지막 값 반환

<br><br>

## 조건 연산자

**Form** : `<ColName>__<Condition>=<Value>__<Condition>=<Value> . . .`

하나의 조건 작성 뒤, `__` 를 붙여서 조건을 계속 걸 수 있다.

<br>

- `__isnull=<bool>` : Null 값 여부

- `__in=[a, b, c]` : 리스트안에 존재하는 값 (`[]` 가 아닌 `()` 로 감싸주어도 상관 X)

- `__exact=<Value>` : 특정 값 하나만을 명시 (**보통 생략된다.** `pk__exact=3` 은 `pk=3` 과 동일)

- `__iexact=<Value>` : 특정 값 하나만을 명시 + 대소문자 무시 (Value 가 str 일 때 유효하게 사용가능)

<br>

### Number

**입력값이 int 가 아닌 float 일 겅우, 소수점을 무시한다.**

- `__gt=<int>` : greater than → 크다

- `__gte=<int>` : greater than, equal → 크거나 같다.

- `__lt=<int>` : less than → 작다

- `__lte=<int>` : less than, equal → 작거나 같다. 

- `__range=(<int:n>, <int:m>)` : `n <= Value <= m` (`()` 가 아닌 `[]` 로 감싸주어도 상관 X)

<br>

### String

- `__contains=<str>` : 해당 문자열 포함 (SQL의 `LIKE '%Value%'` 와 동일)

- `__icontains=<str>` : 해당 문자열 포함 (대소문자 구분 X)

- `__startwith=<str>` : 시작 문자열

- `__istartwith=<str>` : 시작 문자열 (대소문자 구분 X)

- `__endswith=<str>` : 끝 문자열

- `__endswith=<str>` : 끝 문자열 (대소문자 구분 X)

<br>

### Date

- `__day=<int>` : 일 [1, 31]

- `__month=<int>` : 월(달) [1, 12]

- `__quarter=<int>` : 분기 [1, 4]

- `__year=<int>` : 연도

<br><br>

## Reference

- [Django Documentation - Making queries](https://docs.djangoproject.com/ko/2.1/topics/db/queries/)

- [Django Documentation - QuerySet API reference](https://docs.djangoproject.com/ko/2.1/ref/models/querysets/)

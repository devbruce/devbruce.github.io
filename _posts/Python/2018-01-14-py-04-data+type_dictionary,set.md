---
title: "[Python] Data type - dict / set"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - data type
  - dict
  - set

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Dictionary (딕셔너리)

ex) `a = {'name' : 'Bruce' , 'class' : 'A'}`

- unordered, iterable  

- 대응 관게를 나타내는 자료형이다. Associative Array (연관 배열) 또는 Hash (해시) 라고 불린다.  

- key 와 그에 대응하는 value 값을 가지며 key값을 통해 value를 얻을 수 있다.  
(Value를 통해 key를 구하는 것은 불가능)  

```python
a = {'name' : 'Bruce' , 'class' : 'A'}

print(a['name'])  # name 이라는 key를 a에게 전달
```
\>\>\> `'Burce'`

<br><br>

## dict() Function

### Case1

```python
dict_example = dict()  # 비어있는 dict 자료형 생성
```

<br>

### Case2

```python
dict(First=1, Second=2, Third=3)
```
\>\>\> `{'First': 1, 'Second': 2, 'Third': 3}`  

<br>

### Case3

```python
example = (['a',1], ['b',2], ['c',3])

print(dict(example))
```
\>\>\> `{'a': 1, 'b': 2, 'c': 3}`  

각 요소가 두개의 요소를 가진 iterable 데이터일 경우  
자동으로 Key, Value 값을 가진 dict 자료형으로 변형시킨다.  

<br><br>

## Add, Remove Element

`a = {'name' : 'Bruce' , 'class' : 'A'}` 로 정의해두겠다.

### Add
  
`a['number'] = 777` \>\>\> `a = {'name' : 'Bruce' , 'class' : 'A', 'number' : 777}`

> unordred(순서 X) 이기 때문에 어느 위치에 추가될지는 모른다.

### Remove

`del a['class']` \>\>\> `a = {'name' : 'Bruce'}`

<br><br>

## Method

`a = {'name' : 'Bruce' , 'class' : 'A'}` 로 정의해두겠다.

- `a.keys()` \>\>\> `dict_keys(['name', 'class'])` 라는 iterable 객체 반환

- `a.values()` \>\>\> `dict_values(['Bruce', 'A'])` 라는 iterable 객체 반환

- `a.items()` \>\>\> `dict_items([('name', 'Bruce'), ('class', 'A')])` 라는 iterable 객체 반환

- `a.clear()` \>\>\> `a = {}` : 해당 딕셔너리 요소 모두 제거

- `key in a` : 해당 Key값 존재 유무에 따라 `True` or `False` 반환  
`'name' in a` \>\>\> `True`

- `a.get(key)` : `a[key]` 와 동일하지만 해당 key 값이 없을경우 `None`을 반환하는 것이 차이점  
> `.get` 메서드의 두번째 argument를 줄 경우, None이 아닌 그 값을 반환한다.  
> ex) `a.get(key, -1)` → `-1`반환 (해당 key값이 없을경우)

<br>

### dict\_keys, dic\_values, dict\_items

반환된 `dict_keys`, `dic_values`, `dict_items` 를 list 로 변환가능하다. (`list()`)  
메모리 낭비를 줄이기 위해 리스트가 아닌 `dict_keys`, `dic_values`, `dict_items` 라는 객체로 반환해주는 것이다.

<br>

#### 자동 갱신 특성

```python
a = {'name' : 'Bruce' , 'class' : 'A'}

key_object = a.keys()
print(key_object)
```
\>\>\> `dict_keys(['name', 'class'])`  

그런데
 
```python
a['number'] = 777  # a에 {'number' : 777} 추가

print(key_object)
```
\>\>\> `dict_keys(['name', 'class','number'])`
 
변수 `key_object`에 변화를 주지 않았음에도
자동갱신되어 `key_object` = `dict_keys(['name', 'class','number'])` 이 된다.  
 
**즉 dictionary 의 view 객체는 해당 dinctionary에 변화가 생기면 자동갱신된다.**

<br><br>

## Notice
  
- 중복되는 key값을 사용하지 말자.  
  - 중복된 key가 존재하는 딕셔너리를 만드는 것은 가능하지만, 한개를 제외한 나머지는 무시된다.

- key 값은 int, string, tuple 등과 같이 immutable (변경 불가능)한 데이터만 허용된다.  
  - 대표적으로 List 자료형이 key값이 될 수 없다. (Type Error 발생)

<br><br>

# Set (집합)

ex) `a = set([1,2,3,4])`

- unordered, iterable

- 중복 허용 X

<br><br>

## 관련 메서드

> `a = set([1,2,3,4])` , `b = set([3,4,5,6])` 로 정의해두겠다.  

- `a & b` \>\>\> `{3,4}` : 교집합 반환

<br>

- `a.intersection(b)` \>\>\> `{3,4}` : 교집합 반환 (위와 동일)

<br>

- `a | b` \>\>\> `{1,2,3,4,5,6}` : 합집합 반환

<br>

- `a.union(b)` \>\>\> `{1,2,3,4,5,6}` : 합집합 반환 (위와 동일)

<br>

- `a - b` \>\>\> `{1,2}` : 차집합 반환

<br>

- `a.difference(b)` \>\>\> `{1,2}` : 차집합 반환 (위와 동일)

<br>

- `a.add(7)` \>\>\> `a = {1,2,3,4,7}` : 1개 값 추가

<br>

- `a.update(iterable data)` : a에 iterable data 를 추가한다.  
ex) `a.update(range(7, 9+1))` \>\>\> `a = {1,2,3,4,7,8,9}`

<br>

- `a.remove(2)` \>\>\> `a = {1,3,4}` : 특정 값 제거 (해당값이 존재하지않으면 에러메세지 출력)

<br>

- `a.discard(2)` \>\>\> `a = {1,3,4}` : 특정 값 제거

<br>

- `a.clear()` : 빈 집합자료형으로 만들기

<br>

- `a.copy()`: 집합자료형을 복사하여 반환

<br><br>

## Practice

### 중복값 제거

- 해당 자료형을 Set 자료형으로 변환 후, 다시 이전 자료형으로 변환시킨다.

```python
newlist = [1,2,2,3,3,4,4]
set_newlist = set(newlist) # newlist를 set 자료형으로 변환
newlist = list(set_newlist) # set자료형에서 다시 list로 변환
print(newlist)
```
\>\>\> `[1,2,3,4]`  

**중복된 요소가 제거된다.**

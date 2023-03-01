---
title:  "[Python] Data type - Boolean / Comparison Operator"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - data type
  - boolean
  - comparison operator
  - sys
  - getrefcount
  - id

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Boolean

ex) `True` or `False`  

> `True` 와 `False` 의 **첫글자는 대문자** 임을 유의하자.
  
- 참과 거짓을 나타낸다.
  
- True(참) 을 나타내는 경우 : False를 나타내는 값을 제외한 모든 경우
  
- False(거짓) 을 나타내는 경우  
빈 자료형 (`''`, `[]`, `()`, `{}` ... ), `0`, `None`
  
- Comparison Operator (비교연산자) 를 통해 값이 주로 나타난다.

<br><br>

# Comparison Operator (비교연산자)

`a = 3`, `b = 7` , `tmp_list = [1, 2, 3]` 로 정의해두겠다.

- `==` : 값이 같다.  
`a == b` → `False` 반환
  
- `is` : **메모리 주소**가 같다.  
`a is b` → `False` 반환
  
- `is not` : **메모리 주소**가 다르다.  
`a is not b` → `True` 반환
  
- `!=` : 다르다.(Not Equal)  
`a != b` → `True` 반환
  
- `>` : 왼쪽값이 크다.  
`a > b` → `False` 반환
  
- `>=` : 왼쪽값이 크거나 같다.  
`a >= b` → `False` 반환
  
- `<` : 오른쪽 값이 크다.  
` a < b ` → `True` 반환
  
- `<=` : 오른쪽 값이 크거나 같다.  
` a < b ` → `True` 반환
  
- `<Data> in <IterableData>` : `<IterableData>` 요소 중 `<Data>`에 해당하는 값이 존재한다.  
`a in tmp_list` → `True` 반환
  
- `<Data> not in <IterableData>` : `<IterableData>` 요소 중 `<Data>`에 해당하는 값이 존재한다.  
`a not in tmp_list` → `False` 반환
  
---

# `==` & `is` 비교

```python
small = 1
small_same = 1

print(small == small_same)
```
\>\>\> `True`   
  
`small`, `small_same` 둘 다 1을 참조하고 있다.  
즉 값이 같기 때문에 `True`를 반환한다.  
  
이번엔 `is` 를 사용해보자.  

```python
print(small is small_same)
```
\>\>\> `True`  
  
`==` 과 동일하게 `True` 가 반환되었다.  
`1`처럼 작은 숫자의 경우는 자주 사용되는 경우가 많아 파이썬이 미리 캐싱해둔다.  
그렇기 때문에 따로 객체를 새로 생성하는 것이 아닌, 같은 객체를 참조한다.  
그래서 변수 `small`와 `small_same`가 가르키는 **메모리 주소(ID)가 같다.**  
그래서 `True`가 반환된 것이다.
실제로 그런지 `id` 함수를 통해 확인해보자.

```python
print(id(small))
print(id(small_same))
```
`small` 과  `small_same` 의 ID 값이 동일함을 확인할 수 있다.  
  
**하지만 숫자가 커질 경우는 결과가 달라진다.**  
  
```python
big = 1050
big_same = 1050

print(big == big_same)
```
\>\>\> `True`  
<br>
`big`, `big_same` 둘 다 1050을 참조하고 있다.  
즉 값이 같기 때문에 `True`가 반환된다.  

```python
big is big_same
```
\>\>\> `False`  
<br>
`1050`과 같은 큰 수의 경우, 작은 숫자와는 다르게 캐싱해두지 않았기에 메모리를 새로 할당하여 객체를 만든다.  
그 결과 `big`, `big_same` 이 참조하는 1050의 객체 주소(ID)는 다르게 된다.  

<br>

## 작은 수는 정말로 캐싱되어 있는가?

확인을 위해 `getrefcount` 메서드를 활용해보겠다.  
`getrefcount` 는 argument 로 주어진 값의 레퍼런스 카운트를 반환해준다.  
  
먼저 `sys` 를 import 한다.  

```python
import sys
```

```python
small = 1  # 작은 값 중 하나인 1을 small에 할당
sys.getrefcount(small)  # small(==1) 의 레퍼런스 카운트 확인
```
\>\>\> `4000`  
  
컴퓨터마다 다르지만 보통 큰수가 나온다.  
여러부분에서 이미 레퍼런스(참조) 되고 있는 경우가 많기 때문이다.  
  
이번엔 상대적으로 큰 값에 해당하는 `1050`을 확인해보자.
  
```python
big = 1050  # 상대적으로 큰 값인 1050을 big에 할당
sys.getrefcount(big)  # big(==1050) 의 레퍼런스 카운트 확인
```
\>\>\> `2`  
  
`2`가 나온 이유는 `1050`에 `big` 이라는 변수가 가르키고 있는 것 한개,  
`getrefcount` 메서드 자체가 접근하고 있는 것 (총 2개) 이 있기 때문이다.  
  
이를 통해 작은 값들은 캐싱되어 있으며, 여러부분에서 레퍼런스 되고 있음을 확인할 수 있다.


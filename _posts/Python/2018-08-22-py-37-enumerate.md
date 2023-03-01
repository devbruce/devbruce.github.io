---
title: "[Python] enumerate"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - enumerate
  - iterable
  - iterator

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# About enumerate()

- **iterable 타입의 데이터를 enumerate object 로 반환한다.**

- 형태 : `enumerate(iterable, start=0)`
  - `iterable` : enumerate object 로 변환할 iterable data 입력
    - ex) List, Tuple ...
  - `start` : 처음시작할 index 를 설정 (Default = 0)

<br>

## Example

```python
list_data = ['a','b','c'] # List 타입의 임시 데이터 생성
e_data = enumerate(list_data) # 생성한 List 데이터를 enumerate 함수 적용
e_data
```
\>\>\> `<enumerate at 0x000000000>`

<br><br>

## enumerate Object

- Iterator 타입이다.
    - `next` 를 적용 가능
    - 마지막 요소를 반환시 `Stop Iteration` 에러를 반환

- 각 요소의 형태 : `(index, data)`

- 각 요소의 타입 : Tuple


```python
next(e_data)
```
\>\>\> `(0, 'a')`

```python
next(e_data)
```
\>\>\> `(1, 'b')`

```python
next(e_data)
```
\>\>\> `(2, 'c')`

```python
next(e_data)
```
\>\>\> **StopIteration**

<br><br>

## Practice

```python
sample = ["A","B","C"]

for num, name in enumerate(sample, 1): # start index를 1로 지정하였다.
    print("{}번 | 이름:{}".format(num, name))
```
\>\>\> 1번 | 이름:A  
\>\>\> 2번 | 이름:B  
\>\>\> 3번 | 이름:C  

<br><br>

# Reference

- [Python Documentation - 내장 함수](https://docs.python.org/ko/3.6/library/functions.html)
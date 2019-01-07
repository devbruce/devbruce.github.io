---
title: "[Python] Lazy evaluation / map / filter"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - lazy evaluation
  - iterable
  - iterator
  - map
  - filter

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Lazy evaluation

- 연산을 늦춘다.

- 필요할 때 값을 순차적으로 전달받을 수 있다.

<br>

## iterable & iterator

`iter(x)` → iterable 를 iterator 로 변환

> x는 iterable 객체

<br>

### iterable

- `list`, `tuple`, `dict`, `set` ...

- `next` 함수를 사용하여 순차적으로 호출이 불가

<br>

### iterator

- `map`, `filter`, `list_iterator`, `tuple_iterator` ...

- `next` 함수로 요소를 순차적으로 호출이 가능

- 호출이 모두 끝나면 `StopIteration` 에러 반환

- 호출이 모두 끝나면 재활용 불가

<br>

### Example

```python
a = [1,2,3]

k = iter(a)
type(k)
```
\>\>\> `list_iterator`  

`k` 라는 변수에 `[1,2,3]` 를 `list_iterator` 로 변환시킨 자료를 대입시켰다.  
이제 `next` 함수를 통해 원할 때마다 순차적으로 값을 전달받을 수 있다.  

```python
next(k)
```
\>\>\> `1`  

```python
next(k)
```
\>\>\> `2`  

```python
next(k)
```
\>\>\> `3`  

```python
next(k)
```
\>\>\> `StopIteration`  

<br>

## map

형태 : `map(function, iterable, ...)`

인수로 할당된 iterable 자료형의 모든 요소를 function 에 의해 반환된 요소들로  
구성된 `map` 객체 (iterator)를 반환  

```python
a = map(lambda x : x*2, [1,2,3])
type(a)
```
\>\>\> `map`  

lambda 를 통해 function 을 만들고, iterable 객체인 `[1,2,3]`을 인수로 하여 `map` 객체를 만들었다.  

```python
list(a)
```
\>\>\> `[2, 4, 6]`  

리스트화시켜서 결과를 보면 각 리스트의 요소가 두배가 된 것을 알 수 있다.  
`map` 객체 또한 lazy evaluation이 가능하므로 `next` 함수를 사용가능하다.

```python
next(a)
```
\>\>\> `2`  

```python
next(a)
```
\>\>\> `4`  

```python
next(a)
```
\>\>\> `6`  

```python
next(a)
```
\>\>\> `StopIteration`  

<br><br>

## filter

형태 : `filter(function, iterable)`

인수로 할당된 iterable 자료형의 모든 요소를 function 에 의해 반환된 결과 중  
**True** 에 해당하는 값을 `filter` 객체 (Iterator)로 반환

```python
a = filter(lambda x : x > 0, [-3, -2, -1, 0, 1, 2, 3])
type(a)
```
\>\>\> `filter`  

`filter` 객체를 만들었다.

```python
list(a)
```
\>\>\> `[1,2,3]`  

`x > 0` 의 참에 해당하는 요소만 `filter` 객체로 반환되어진 것을 알 수 있다.  
`filter` 객체 또한 lazy evaluation 이 가능하므로 `next` 함수를 사용가능하다.

```python
next(a)
```
\>\>\> `1`  

```python
next(a)
```
\>\>\> `2`  

```python
next(a)
```
\>\>\> `3`  

```python
next(a)
```
\>\>\> `StopIteration`  

<br><br>

## Practice (map & filter)

```python
new = [-2, -1, 0, 1, 2, 3, 4]
```

정의된 new 리스트에서

1. 2 보다 큰 수 중에서
2. 3배를 한 값들로 구성된 리스트

<br>

### Answer

```python
answer = map(lambda x : x * 3, filter(lambda x : x > 2, new))

list(answer)
```
\>\>\> `[9, 12]`

<br><br>

# Reference

- [Python Documentation - 내장 함수](https://docs.python.org/ko/3.6/library/functions.html)

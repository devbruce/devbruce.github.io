---
title: "[Python] Data type - Summary"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - data type
  - number
  - complex number
  - binary
  - octal
  - hexademical
  - int
  - float
  - string
  - list
  - tuple
  - dictionary
  - set
  - boolean

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Data Type

- ordered / unordered

- immutable / mutable

- iterable

<br>

> 연산이 가능한 자료형일 경우, 같은 자료형끼리만 가능하다.  `3 + "ABC"`  \=\= (integer + string) → Error
>
> But 자료형을 동일하게 변형시킬 경우 연산이 가능해진다.  `str(3) + "ABC"` \=\= `"3ABC"`

<br><br>

## 1. Number (숫자형)

<br>

- Integer (정수)
ex) `3`

<br>

- Floating-point (실수)
ex) `3.7`

<br>

- Complex number (복소수)
ex) `1 + 2j`

<br>

- Binary (2진수)
ex) `0b111`

<br>

- Octal (8진수) : 숫자 0 + 알파벳 소문자 o 또는 대문자 o
ex) `0o111`

<br>

- Hexademical (16진수)
ex) `0x111`

<br><br>

## 2. String (문자열)

> ordered, iterable, immutable

ex) `"Hello World!"`

<br><br>

## 3. List (리스트)

> ordered, iterable, mutable

ex) `[1,2,3,4]`

<br><br>

## 4. Tuple (튜플)

> ordered, iterable, immutable

ex) `(1,2,3,4)`, `1, 2, 3, 4`

<br><br>

## 5. Dictionary (딕셔너리)

> unordered, iterable, mutable

ex) `{'name' : 'Bruce' , 'class' : 'A'}`

<br><br>

## 6. Set (집합)

> unordered, iterable

ex) `set([1,2,3,4])` ▶ `{1,2,3,4}`

- 중복을 허용하지 않는다.

<br><br>

## 7. Boolean (논리)

ex) `True` or `False`  

- 참과 거짓을 나타낸다.

- 주로 Comparison Operator (비교연산자) 를 통해 반환된다.

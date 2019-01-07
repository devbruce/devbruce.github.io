---
title: "[Python] Generator / Generator Expression"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - generator
  - generator expression
  - yield
  - iterator
  - lazy evaluation

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Generator

- Generator object를 생성해주는 함수를 의미한다.
  - Generator object 는 Iterator 의 한 종류이다.
  -  `yield`를 사용한다는 점이 일반함수와의 차이점
  - Generator object 자체를 의미하기도 한다.

- Iterator와 동일하게 Lazy evaluation 가능

- 메모리 효율성 상승

<br>

> Iterator 로 변환하는 함수 : `iter(iterable data)`  
> 
> ```python
> a = [1,2,3]
> a = iter(a)
> print(a)
> ```
> \>\>\> `<list_iterator object at 0x000000000>`

<br><br>

## Step1. Make Generator

```python
def generator(n):

    for i in range(1, n + 1):
        yield i
```
위와 같이 `yield`를 통하여 함수를 정의하면 generator object를 만드는 함수가 된다.  
`yield` 의 의미를 이해하면 위 코드를 해석하는 것에 어렵지 않다.  

`yield` 는 `return`과 비슷하게 값을 반환한 뒤,  
함수 호출을 끝내는 것이 아니라 해당 값에서 **대기**시킨다.  

<br><br>

## Step2. Make Generator Object

```python
a = generator(3)

type(a)
```
\>\>\> generator  

<br><br>

## Step3. Lazy Evaluation By Generator Object

`next()` 또는 `.__next__()` 는 Iterator 자료형에 사용가능한 함수이다.  
위의 두 함수는 Iterator의 다음값을 순서대로 하나씩 반환받는다.  
아래의 예시는 위에서 만든 generator object인 `a`를 활용하여 Lazy Evaluation (게으른 연산)을 시도하는 과정이다.

```python
next(a)
```
\>\>\> 1  

위 코드에서 `yield`는 반복문 `for i in range(1, 3 + 1)`에 의해 첫번째 i에 해당하는 1을 반환시킨다.  
그리고 함수를 일시정지시킨다.

```python
next(a)
```
\>\>\> 2  

위 코드에서 `yield`는 반복문 `for i in range(1, 3 + 1)`에 의해 두번째 i에 해당하는 2를 반환시킨다.  
그리고 함수를 일시정지시킨다.

```python
next(a)
```
\>\>\> 3  

위 코드에서 `yield`는 반복문 `for i in range(1, 3 + 1)`에 의해 세번째 i에 해당하는 3을 반환시킨다.  
그리고 함수를 일시정지시킨다.

```python
next(a)
```
\>\>\> `StopIteration`  

위 코드에서 `yield`는 반복문 `for i in range(1, 3 + 1)`이 끝났다. 더이상 반환시킬 값이 없다.  
따라서 `StopIteration` 을 반환하고 함수를 끝낸다.

<br><br>

# Generator Expression

- Generator Object 를 간결하게 만들 수 있다.

- Comprehension 문법과 유사하다.

<br>

```python
a = (i for i in range(10))
type(a)
```
\>\>\> generator

```python
list(a)
```
\>\>\> `[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]`

<br><br>

# 메모리 효율성

sys 라이브러리의 `getsizeof()` 메서드를 활용하여 살펴본다.
> 결과값 단위 : byte

```python
import sys

list_1 = [i for i in range(100)]
list_2 = [i for i in range(30000)]
list_3 = [i for i in range(5000000)]

######

generator_1 = (i for i in range(100))
generator_2 = (i for i in range(30000))
generator_3 = (i for i in range(5000000))
```

위 코드에서 generator 객체 3개, list 객체 3개를 만들었다.  
먼저 list 객체들의 크기를 구해보겠다.

```python
print(sys.getsizeof(list_1))
print(sys.getsizeof(list_2))
print(sys.getsizeof(list_3))
```
\>\>\> 912  
\>\>\> 253632  
\>\>\> 40215168  

List의 경우 요소의 수가 많아질수록 크기가 커지는 것을 알 수 있다.  
이번엔 generator 객체를 살펴보겠다.

```python
print(sys.getsizeof(generator_1))
print(sys.getsizeof(generator_2))
print(sys.getsizeof(generator_3))
```
\>\>\> 88  
\>\>\> 88  
\>\>\> 88  

<br>

# Conclusion

Generator Object는 요소의 수가 많아져도 **메모리 크기가 동일**함을 알 수 있다.

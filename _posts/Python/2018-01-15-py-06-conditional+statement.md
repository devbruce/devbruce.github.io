---
title: "[Python] Conditional Statement"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - if

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Conditional Statement (조건문)

## if

**기본 조건문**

```python
x = 7
if x > 5:
	print("contitional statement")
```
\>\>\> `'conditional statement'`  

<br><br>

## else

**선행으로 제시된 조건(if, elif ...)을 제외한 조건**  

```python
x = 7
if x > 10:
    print("first")
elif x > 8:
    print("second")
else:
    print("last")
```
\>\>\> `'last'`  

이 코드에서의 `elif` 는 ( 8 < x <= 10 ),
`else`는 ( x <= 8 ) ( 먼저 제시된 조건을 제외한 ) 을 의미한다고 볼 수 있다.  

<br><br>

## elif

**`else` 와 `if`의 합성과 같다.**  

```python
x = 7
if x > 10:
    print("first")
elif x > 5:
    print("second")
```
\>\>\> `'second'`  

`elif` 에 제시된 조건 ( x > 5 ) 가 조건에 맞기 때문에 "second"가 출력되었다.
`elif`는 `else` +`if` 라고 할 수 있다.  
밑의 예제코드를 통해 의미를 이해해보자.

```python
x = 7
if x > 10:
    print("first")
else:
    if x > 5:
        print("second")
```
\>\>\> `'second'`  

<br><br>

## 조건문을 한줄로 만들기

```python
x = 10
if x > 7 :
    print("It is true")
else:
    print("It is false")
```
\>\>\> `'It is true'`  

조건문을 한줄로 변환시켜보자.

```python
if x > 7 : print("It is true")
else : print("It is false")
```
\>\>\> `'It is true'`  

<br><br>

## Notice

```python
x = 15
if x > 10:
    print("It is true")
elif x > 5:
    print("it is also true")
else:
    print("It is false")
```
\>\>\> `'It is true'`  

위 코드를 보면 `if` 조건과 `elif`조건 두가지가 모두 `True` 이지만 첫번째 조건문만 성립되어
`"It is true"` 가 출력되었다.  
이유는 `elif` 의 조건문은 x > 5 만을 의미하는 것이 아니기 때문이다.  
위에 설명했다시피 `else`+ `if`이기 때문에 정확한 의미는 **(5 < x <= 10)** 이라고 볼 수 있다.  
그래서 결과값이 출력되지 않은 것이다.

```python
x = 15
if x > 10 :
    print("It is true")
if x > 5 :
    print("It is also true")
else:
    print("It is false")
```

\>\>\> `'It is true'`  
\>\>\> `'It is also true'`  

위 코드의 경우에는 if 조건문 두가지가 모두 성립(`True`) 한다.  
첫번째 `if`( x > 10 ) \== `True` 이고,  
두번째 if는 ( x > 5 ) 그 자체를 의미하기 때문에 이 또한 `True`이다.  
그래서 첫번째 `if`( x > 10 ), 두번째 `if` ( x > 5 ) 결과가 모두 출력된 것이다.  
만약 if조건문만 사용하여 elif를 쓴 것과 같은 효과를 얻기 위해 아래코드와 같이 작성하면 된다고 착각할 수 있다.

```python
x = 15
if x > 10 :
    print("It is true")
if 5 < x and x <= 10 : # == ( x > 5 and x <= 10 )
    print("It is also true")
else:
    print("It is false")
```
\>\>\> `'It is true'`  
\>\>\> `'It is false'`  

예상치 못하게 else의 결과값까지 출력되었다.  
원인을 분석해보자면  
첫번째 `if`문 ( x > 20 ) 을 실행 후에  
두번째 `if`문을 실행할 때 파이썬은 마지막으로 실행된 2번째 `if`문을 시작으로 인식하여 `else`와 연결하기 때문이다.  

다시 말해, 첫번째 `if`( x > 10)을 실행한 뒤 `True`가 나와 결과값을 출력 후,  
두번째 `if`( 5 < x and x <= 10 )을 실행하고, 이 조건문을 첫번째 조건문으로 인식하였기 때문에  
`else`는 2번째 `if`( 5 < x and x <= 10 )의 예외값 ( x <= 5 and x > 10) 을 의미하게 되어 `True` 가 되고, 결과값을 출력하는 것이다.  
if 만을 활용하여 만든다면 아래 코드처럼 만들어야 할 것이다.

```python
x = 15
if x > 10 :
    print("It is true")
if 5 < x and x <= 10 :
    print("It is also true")
if x <=5 :
    print("It is false")
```

> 만약 `if`만을 활용하여 조건문을 만든다면 `else`또한 사용을 피해야한다.
> 또한 `if`만을 활용하여 만든다면, 모든 조건문을 실행하기 때문에 메모리상으로 비효율적이다.
> 특수한 상황이 아니라면 **`if`만을 활용한 코드는 사용을 피하는 것이 적절**하다.

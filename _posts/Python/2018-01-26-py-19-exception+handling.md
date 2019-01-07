---
title: "[Python] Exception Handling"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - exception handling
  - try
  - except

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Exception Handling (예외처리)

Error 가 발생할 수 있는 구간을 지정하여, 에러 발생구간을 컨트롤 할 수 있다.

<br>

```python
try:
    # 에러가 발생할 수 있는 구간
    
except <ErrorType> as <Variable> :  # as 는 생략가능
    # 해당에러 발생시 실행할 코드

except <ErrorType> as <Variable> :
    # 해당에러 발생시 실행할 코드

else:
    # 에러가 발생하지 않았을 경우 실행할 코드

finally:
    # 무조건 실행되는 코드
```

- 두번째 이상의 `except`, `else`, `finally`는 생략가능

- `except` 는 조건문의 `elif`처럼 여러개 사용가능

- `as` 생략가능  
`as`를 사용시 왼쪽에 입력된 에러(클래스객체)를 할당받는다.
> Error는 보통 Class 의 형태를 띄고있다.

<br><br>

- `raise` : 에러를 발생시키는 명령어다.  

```python
raise ValueError
```
\>\>\> `ValueError Traceback (most recent call last)`

<br><br>

## Practice

```python
try:
    int("example")
    
except:  # Error 의 종류를 명시하지 않았으므로 모든 error
    pass  # 아무것도 하지 않는다.
```

위와 같이 작성하면 어떠한 에러가 발생하든 무시하고 반복문을 진행시키고,  
try 블럭 밖의 코드를 실행하게된다.

<br>

```python
try:
    int("example")
    
except ValueError as e:
    print("e")
```
\>\>\> `invalid literal for int() with base 10: 'example'`  

`int("example")` 코드가 ValueError 를 발생시킴에 따라 발생에러를 e로 대입받고,  
`except` 의 코드 `print("e")` 가 실행된 결과이다.

<br>

```python
try:
    raise ValueError  # ValueError 를 발생시킨다.

except StopIteration:
    print("StopIteration Error 발생한 경우 출력")

except:
    print("StopIteration 이외의 Error 발생할 경우 출력")
    
else:
    print("else : try ~ except 사이의 코드에서 에러가 발생하지 않을 경우 출력")

finally:
    print("Finally : 무조건 출력")

print("try 블럭 밖 : try 구문이 끝남에 따라 출력")
```
\>\>\> `'StopIteration 이외의 Error 발생할 경우 출력'`  
\>\>\> `'Finally : 무조건 출력'`  
\>\>\> `'Finally : try 블럭 밖, try 블럭이 끝남에 따라 출력'`

<br><br>

## Make Custom Error

`Exception` 클래스를 상속받아 에러를 직접 만들 수 있다.

```python
class CustomError(Exception):
    pass
```

직접 만든 에러를 발생시켜보겠다.

```python
raise CustomError
```
\>\>\> `CustomError:`

에러가 정상적으로 작동한다.  
이런 경우에는 아무런 메세지도 출력되지 않는다.  
에러에서 본인이 원하는 메세지가 출력되도록 하는 방법은 두가지가 있다.  

<br>

- 첫번째 방법: argument로 본인이 원하는 문자열 주기

```python
raise CustomError("Hi")
```
\>\>\> `CustomError: Hi`

<br>

- 두번째 방법: 작성한 CustomError의 `__str__` 메서드를 작성하기

```python
class CustomError(Exception):
    def __str__(self):
        return "Some String"
```

```python
raise CustomError
```
\>\>\> `CustomError: Some String`

> `__str__` 을 작성하게 되면 argument로 주어진 값은 무시된다.

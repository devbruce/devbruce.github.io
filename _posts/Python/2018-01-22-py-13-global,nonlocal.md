---
title: "[Python] global / nonlocal"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - global
  - nonlocal

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# global / nonlocal

- 지역변수를 전역변수에 영향을 줄 수 있도록 해주는 명령어  

- `global` 명령어는 사용하지않는 것을 권장

<br><br>

## global

<br>

```python
x = 15  # 전역변수 (global variable)

def f():
    x = 30  # 지역변수 (local variable)
    

f()

print(x)
```
\>\>\> `15`  

전역변수의 값 `15`이 출력되었다. 변수의 값에 변화가 없었음을 알 수 있다.  
파이썬은 Call By Assignment 의 특성을 가지고 있다.  
> [[Python] Call By Assignment](https://devbruce.github.io/python/py-42-call+by+assignment/) 참조  

즉, mutable 자료형을 가진 변수가 아닌이상 지역변수가 전역변수에 영향을 줄 수 없다.  
하지만 `global` 명령어를 사용할 경우 지역변수가 전역변수에 영향을 줄 수 있다.  

```python
x = 15  # 전역변수 (global variable)

def f():
    global x  # 함수의 지역변수 x 가 전역변수 x를 가르키게 된다.
    x = 30 # 지역변수 (local variable)
    

f()

print(x)
```
\>\>\> `30`  

`global`에 의해 함수 `f` 에서 지역변수의 값이 전역변수에 적용되어 출력된 것을 알 수 있다.  
`global`명령어를 조금 더 복잡한 구조에서 사용해보겠다.  

```python
x = 15
def f():
    x = 30
    def g():
        x = 60
        def h():
            global x
            x = 120 
        h() 
    g()
f()

print(x)
```
\>\>\> `100`  

함수 h에서 `global`이 사용되었고, h의 지역변수인 `x = 120`가 전역변수에 영향을 주었다.  
그 결과 `120` 이 출력된 것을 알 수 있다.  

`global`은 함수 내의 함수로 몇차례 연속으로 들어가건, `global`이 사용된 함수의 지역변수를 전역변수로 연결시킨다.  
만약 `global` 을 `g` 함수에서 입력했을 경우 출력 결과는 `60` 이 되었을 것이다.

<br><br>

## nonlocal

- `nonlocal` : 지역변수가 아님을 선언  
`nonlocal` 이 사용된 함수 바로 한단계 바깥쪽에 위치한 변수와 바인딩을 할 수 있다.

```python
x = 20 # 전역변수 (global variable)

def f():
    x = 40
    def g():
        nonlocal x
        x = 80
    g()  # 함수 g를 실행하여 nonlocal이 적용되도록 한다.
    print(x)  # 함수 f에서의 x값이 출력된다.(함수 g에서 nonlocal 의 영향을 받아 변수가 80으로 변경되었다.)
f()
print(x)  # 모든 함수 실행이 끝나고, 변수 x를 출력한다.(출력값은 처음값인 20이다)
```
\>\>\> `80`  
\>\>\> `20`  

위 코드를 통해 알 수 있듯이, 바로 위에 해당하는 `f` 함수의 지역변수에만 영향을 주고, 전역변수에는 영향을 주지 않았다.  

<br>

### Notice

`nonlocal` 은 아래와 같이, **함수 한개를 정의하고 전역변수에 영향을 주게 하는 것은 안된다.**

```python
x = 70  # 전역변수 (global variable)

def f():
    nonlocal x
    x= 140
    
f()
print(x)
```
\>\>\> `no binding for nonlocal 'x' found`

<br><br>

## global, nonlocal Example

```python
x = 50
def f():
    a = 777
    def g():
        a = 100
        def h():
            global x
            x = 999
            nonlocal a
            a = 333
        h()
        print("[Level 2] a = {}".format(a))
    g()
    print("[Level 1] a = {}".format(a))

f()
print("[Level 0] x = {}".format(x))
```
\>\>\> `'[Level 2] a = 333'`  
\>\>\> `'[Level 1] a = 777'`  
\>\>\> `'[Level 0] x = 999'`  

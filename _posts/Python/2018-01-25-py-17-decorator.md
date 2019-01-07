---
title: "[Python] Decorator (1)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - decorator
  - closure

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Decorator

- 함수를 꾸며준다. (기능추가)

- 구조를 이해하기 위해서는 Closure 에 대한 전반적인 이해가 필요하다.

<br>

## Example

```python
def test(a, b):
    print(a + b)
    
def test2(a, b):
    print(a * b)
    
def test3(a, b):
    print(a // b)
```

함수 3개가 정의되어있다.  
모든 함수에 같은 명령어를 추가하고 싶은 경우가 있다.  
예를 들면 결과 출력 전과 후에 원하는 함수마다 같은 문장을 출력하고 싶다고 가정하자.  
그러한 경우 아래와 같이 코드를 일일이 넣어줄수 있다.  

```python
def test(a, b):
    print("계산 시작합니다.")
    print(a + b)
    print("계산 완료되었습니다.")
    
def test2(a, b):
    print("계산 시작합니다.")
    print(a * b)
    print("계산 완료되었습니다.")
    
def test3(a, b):
    print("계산 시작합니다.")
    print(a // b)
    print("계산 완료되었습니다.")
```

위와 같이 일일이 코드를 넣는 것은 번거롭다.  
이러한 과정이 더 많이 필요하다면 그 번거로움은 배가 될 것이다.  
이런 과정을 간단하게 해주는 것이 decorator 이다.  
위의 과정을 decorator를 활용하여 처리해보겠다.  

```python
def deco_func(origin_func):
    def nested_func(*args, **kwargs):
        print("계산 시작합니다.")
        origin_func(*args, **kwargs)
        print("계산 완료되었습니다.")
        return
    
    return nested_func
```
먼저 꾸며주는 함수를 제작한다.  
이제 `deco_func` 의 기능을 다른 함수들에 추가해보겠다.

<br>

```python
@deco_func
def test(a, b):
    print(a + b)

@deco_func
def test2(a, b):
    print(a * b)

@deco_func
def test3(a, b):
    print(a // b)
```

`@deco_func` 라는 명령어를 함수 바로위에 추가함으로써 위의 과정을 마무리하였다.  

결과를 살펴보자.

```python
test(5,2)
print("-" * 30)  # 분류선
test2(5,2)
print("-" * 30)  # 분류선
test3(5,2)
```
\>\>\> 계산 시작합니다.  
\>\>\> 7  
\>\>\> 계산 완료되었습니다.  
\>\>\> -\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-  
\>\>\> 계산 시작합니다.  
\>\>\> 10  
\>\>\> 계산 완료되었습니다.  
\>\>\> -\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-   
\>\>\> 계산 시작합니다.  
\>\>\> 2  
\>\>\> 계산 완료되었습니다.  

원하는 결과가 나왔다. 그렇다면 decorator 의 적용과정을 구체적으로 살펴보자.

<br>

## Practice

```python
def deco_func(origin_func):  # Starting
    def nested_func(*args, **kwargs):
        print("계산 시작합니다.")
        origin_func(*args, **kwargs)
        print("계산 완료되었습니다.")
        return
    
    return nested_func
```
데코레이터 함수 `deco_func` 함수를 만들었다.

```python
def new_func(a, b, c):
    print(a, b, c)
```

이번엔 새로운 함수 `new_func` 를 만들었다.  
decorator 함수가 적용되는 과정은 결국엔 Closure 활용과 동일하다.

```python
deco_func(new_func)
```
\>\>\> `<function __main__.deco_func.<locals>.nested_func>`  

`nested_func` 함수객체가 됐음을 알 수 있다.  
이제 결과값을 출력해보자.  

```python
deco_func(new_func)(1,2,3)
```
\>\>\> 계산 시작합니다.  
\>\>\> 6  
\>\>\> 계산 완료되었습니다.  

문제없이 결과가 출력되었다.  
이 과정은 Closure 를 다룬 포스팅에서 자세하게 다루었으니 필요하다면 참고하길 바란다.  

[[Python] Closure](https://devbruce.github.io/python/py-16-closure/)  

그 과정을 다시 간단하게 위의 예를 통해 다루어보겠다.  

- 1st. `deco_func` 함수의 인수에 `new_func` 함수객체를 인자로 할당하였다.

- 2nd. 인자를 받은 `deco_func` 함수는 `nested_func` 함수를 호출과 동시에 반환한다.

- 3rd. *1st*, *2nd* 과정을 통해 `nested_func` 함수객체가 된 값에 인자로 (1,2,3)을 할당한다.

```python
deco_func(new_func)(1,2,3)
```

- 4th. 중첩함수인 `nested_func` 함수는 첫번째 라인 `print("계산 시작합니다.")`를 실행한다.  

- 5th. 두번째 라인 `origin_func(1,2,3)`를 실행하려고 하는데 `origin_func`는  
`nested_func`내에서 정의된 적 없다.  
부모함수 `deco_func`에서 인수 `origin_func` 가 인자로 `new_func`를 받았으니  
`origin_func = new_func` 라고 부모함수에서 정의되었었다.  
그럼 closure에 저장되어있으니 closure data로 접근한다.

```python
deco_func(new_func).__closure__[0].cell_contents
```
\>\>\> `<function __main__.new_func>`  

따로 저장되어있던 new_func함수  

- 6th. `origin_func(1,2,3)` → `new_func(1,2,3)` 로 실행한다.

- 7th. 세번째 라인 `print("계산 완료되었습니다.")` 를 실행한다.  

- 8th. 함수가 종료되며 최종결과가 나온다.

위의 과정을 `@` 을 통해 직관적으로 적용한 것이 decorator 라고 볼 수 있다.  

<br><br>

```python
@deco_func
def new_func(a, b, c):
    print(a, b, c)
```
결국 이러한 코드의 모습은 아래와 동일하게 사용할 수 있다.  

```python
def new_func(a, b, c):
    print(a, b, c)

deco_func(new_func)
```

<br><br>

## Decorator vs Closure

최종적으로 decorator 를 활용할 때와, closure를 활용할 때의 차이를 보자.

```python
def deco_func(origin_func):  # 데코레이터 함수
    def nested_func(*args, **kwargs):
        print("계산 시작합니다.")
        origin_func(*args, **kwargs)
        print("계산 완료되었습니다.")
        return
    
    return nested_func
    
def output(a, b):  # 적용받을 함수
    print(a + b)
```
먼저, decorator 함수 `deco_func` 와 적용받을 함수 `output` 을 만든다.  

**decorator를 적용한 output 함수를 보자.**  

```python
@deco_func
def output(a, b):
    print(a + b)
```

**closure 를 적용시킨 output 함수를 살펴보자**  

```python
deco_func(output)
```

결과를 한번 살펴보겠다.  

**decorator가 적용된 output 함수의 결과이다.**

```python
output(3,2)
```

\>\>\> 계산 시작합니다.  
\>\>\> 5  
\>\>\> 계산 완료되었습니다.  

**closure를 활용한 output 의 결과를 살펴보자.** 

```python
deco_func(output)(3,2)
```
\>\>\> 계산 시작합니다.  
\>\>\> 5  
\>\>\> 계산 완료되었습니다.  

이를 통해 알 수 있는 것은,  
decorator는 모양만 다를뿐 closure 를 활용한 것과 동일함을 알 수 있다.

<br>

위 글을 읽다보면 decorator 를 적용한 함수의 값을 반환받아야 할 때는 어떻게 해야될지 의문이 생길 수 있다.  
output 함수의 결과값이 print가 아닌 return 을 하게됐을 때 decorator 함수정의에 제한이 생기지 않는가에 대한 것일텐데,  
이 부분에 대해서는 [[Python] Decorator (2)](https://devbruce.github.io/python/py-29-decorator2/) 에서 다루도록 하겠다.

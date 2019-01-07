---
title: "[Python] Call By Assignment"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - call by assignment

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Call By Assignment

- Python 의 특징 ( Call By Object Reference 라고도 불린다.)

- immutable 자료형에는 Call By Value 와 같은 결과

- mutable 자료형에는 Call By Reference 와 같은 결과

<br>

## Call By Value

- 변수를 선언하면, 그 변수에 특정 메모리를 할당 후 값을 입력

- Stack Frame 내부의 변수와 외부의 변수는 메모리 주소가 다르기 때문에 간섭불가능

<br>

## Call By Reference

- 포인터를 통하여 선언된 변수의 메모리 주소 자체에 접근

- Stack Frame 사라져도 변수에 간섭가능

<br>

## Example

```python
x = 10  # global variable(전역변수)

def f():
    x = 20  # local variable(지역변수)
    
#### 함수 f는 x = 20 을 정의하고 끝 ####

f() # f를 호출

print(x)
```
\>\>\> 10  

`x = 10`으로 전역변수를 정의하고, 함수내부에서 `x = 20`이라고 했으나,  
`x`의 값을 출력해보면 그대로 `10`이다.  

원인에 대해서 살펴보자.  
함수내부에서 선언된 변수는 local variable(지역변수)라 불리는데,  
이러한 지역변수는 메모리에서 Stack Frame 이라는 곳에 쌓이게 된다.  
아래의 이미지를 참고하자.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment01.png?raw=true){: width="300px"}
  
위 그림은 `x = 10`이라는 전역변수가 선언되었을 때의 모습을 보여준다.  
Call By Value, Call By Reference 의 경우, 변수라는 하나의 메모리 공간을 만들어 그곳에 데이터를 입력시키는 방식이지만, 파이썬은 다르다.  
파이썬은 변수를 선언하고 값을 지정하면, "변수가 값을 가리키고 있다" 라고 생각하면 이해가 쉬울 것이다.  
다음으로, 함수 `f`가 정의되고 지역변수를 선언하게 될 경우를 아래그림을 통해 살펴보자.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment02.png?raw=true){: width="450px"}  

함수 `f`가 선언됨에 따라 Stack Frame이 생성되고, 그 내부에 변수 `x = 20`이 선언되었다.  
그리고 함수가 종료됨에 따라 아래 그림과 같이 Stack Frame은 사라지게 된다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment03.png?raw=true){: width="450px"}  

그리고 `print(x)`를 실행하는 상황에서는 아래와 같은 상황에서 x를 출력하게된다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment01.png?raw=true){: width="300px"}  

여기까지만 본다면 Call by value 와 크게 다른점은 없어보인다.  
하지만 Call by reference 와 같은 결과 또한 보여주는 것이 파이썬의 특징이다.  
바로, mutable 자료형 ( List, Dictionary, Set ) 에 영향을 주는 경우이다.  
아래의 예제를 보자.

```python
x = [1,2,3] # 전역변수 (global variable)

def f():
    x.append(100) # 지역변수 (local variable)
    

f()

print(x)
```
\>\>\> `[1, 2, 3, 100]`  

<br>

위와는 다르게 변수 `x`에 영향을 주었다.  
이해가 쉽도록 이미지를 통해 보도록 하겠다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment04.png?raw=true){: width="300px"}  

`x = [1,2,3]`이 정의되어 위의 그림처럼 되었다.
그리고 함수 `f`가 선언되고, `x.append(100)`가 실행되는 과정이 아래의 이미지이다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment05.png?raw=true){: width="450px"}  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment06.png?raw=true){: width="450px"}  

그리고 함수 `f`가 종료되며 Stack Frame이 사라진다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment07.png?raw=true){: width="450px"}  

그리고 마지막으로 아래 이미지와 같은 상황에서 append가 이루어진 변화된 x를 출력하게 된다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-42-callbyassignment08.png?raw=true){: width="300px"}

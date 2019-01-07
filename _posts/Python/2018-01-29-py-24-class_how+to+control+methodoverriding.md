---
title: "[Python] Class - How to control Method Overriding"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - method overriding
  - super

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# How to control Method Overriding

Method Overriding 을 하게 될 경우,  
부분적으로 부모클래스의 메서드를 사용하고 싶거나  
부모클래스의 메서드 기능을 추가해서 사용하고 싶은 경우가 있다.  
방법은 크게 두가지가 있다.

1. 클래스명을 통한 직접적인 부모클래스의 메서드 호출

2. `super()`를 활용한 부모클래스의 메서드 호출

<br><br>

## 1. 클래스명을 통한 직접적인 부모클래스의 메서드 호출

형태 : `클래스명.메서드이름(self, *args)`

<br>

단순한 구조로 예시를 만들어보겠다.

```python
class A:
    def __init__(self):
        print("클래스 A 생성자 실행")

class B(A):  # 클래스 A를 상속받았다.
    def __init__(self):
        print("클래스 B 생성자 실행")
```
```python
instance = B()
```
\>\>\> `'클래스 B 생성자 실행'`  

클래스 B 의 인스턴스를 만들 경우, A를 상속받았지만 Method Overriding 에 의해  
클래스 B의 생성자가 적용됨을 알 수 있다.  
이제 Method Overriding 이 적용되어도, 클래스 A의 생성자가 호출되도록 컨트롤해보겠다.

```python
class A:
    def __init__(self):
        print("클래스 A 생성자 실행")

class B(A):
    def __init__(self):
        A.__init__(self)
```
```python
instance = B()
```
\>\>\> `'클래스 A 생성자 실행'`  

클래스 B 의 생성자에서 클래스 A 의 생성자를 직접 호출하여  
클래스 B 의 생성자에서 클래스 A 의 생성자가 실행되어 위와 같은 결과가 나타났다.  
(Method Overriding 이 되었음에도 부모클래스의 메서드를 호출하였다.)  

단순히 호출만 하는 것이 아닌, 호출 뒤의 추가적인 코드를 작성할 수도 있다.

```python
class A:
    def __init__(self):
        print("클래스 A 생성자 실행")

class B(A):
    def __init__(self):
        A.__init__(self)
        print("Add new code")
```
\>\>\> `'클래스 A 생성자 실행'`  
\>\>\> `'Add new code'`  

이제 조금 더 예시를 복잡하게 만들어보겠다.

```python
class Person:
    def __init__(self, name):
        self.name = name
        
class PersonDetail(Person):  # 클래스 Person 을 상속받았다.
    def __init__(self, name, gender, major):
        Person.__init__(self, name)  # Person 클래스의 생성자를 호출한다.
        self.gender = gender
        self.major = major
        
    def show_all_info(self):
        print("Name: {}".format(self.name))
        print("Gender: {}".format(self.gender))
        print("Major: {}".format(self.major))
```
```python
p1 = PersonDetail("Bruce", "Male", "Business Administration")

p1.show_all_info()
```
\>\>\> `'Name: Bruce'`  
\>\>\> `'Gender : Male'`  
\>\>\> `'Major : Business Administration'`  

클래스 Person 의 생성자를 호출하기 위해서는 생성자의 인수인 self, name 은 그대로 가져와서 전달해야만 한다.  
그렇기 때문에 클래스 PersonDetail 의 생성자에는 self, name 을 포함한 인수를 가져야한다.

<br><br>

## 2. super()를 활용한 부모클래스의 메서드 호출

형태 : `super().메서드이름(*args)`  
> self 가 생략되는 것이 특징이다.

<br>

super()를 활용한 예시를 만들어보겠다.

```python
class A:
    def __init__(self):
        print("클래스 A 생성자 실행")

class B(A):
    def __init__(self):
        super().__init__()
```
```python
instance = B()
```
\>\>\> `클래스 A 생성자 실행`  

`super()`를 사용하는 것은, 클래스명을 통해 직접호출하는 것과 다르게 
부모클래스를 호출하는 것이다. (메서드의 인수에 self가 없는 것을 유의해야한다.)  
마찬가지로, 메서드를 호출한 뒤, 코드를 추가하는 것도 가능하다.  

```python
class A:
    def __init__(self):
        print("클래스 A 생성자 실행")

class B(A):
    def __init__(self):
        super().__init__()
        print("Add new code")
```
\>\>\> `'클래스 A 생성자 실행'`  
\>\>\> `'Add new code'`  

<br>

그렇다면 클래스명을 통해 직접 메서드를 호출 하는 것과 `super()` 를 통해 메서드를 호출하는 것의 차이가 무엇일까?  
이 부분에 대해서는 [[Python] Class - Diamond Inheritance](https://devbruce.github.io/python/py-26-class_diamond+inheritance/) 에서 다루도록 하겠다.

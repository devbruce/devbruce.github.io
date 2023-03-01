---
title: "[Python] Class - Multiple Inheritance / MRO"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - inheritance
  - MRO

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Multiple Inheritance (다중 상속)

클래스는 동시에 여러가지의 클래스를 상속받을 수 있다.  
이를 Multuple Inheritance (다중 상속) 이라고 한다.  

<br>

## Example

```python
class Human:
    def tool(self):
        print("I can use tools")
        

class Bird:
    def fly(self):
        print("I can fly")
```
위와 같은 클래스가 정의되어있다. 이제 다중 상속을 받는 클래스를 만들어보겠다.  

```python
class Unknown(Human, Bird):  # Human, Bird 클래스 두개를 상속받았다.(다중 상속)
    pass
```
새로 정의된 Unknown 은 인간처럼 도구를 쓰고, 새처럼 날 수도 있다는 설정이므로 class Human 과 class Bird 를 둘 다 상속받았다. 이제 Unknown의 인스턴스를 만들고 결과를 출력해보자.  

```python
instance = Unknown()  # class Unknown 의 인스턴스를 생성

instance.tool()
instance.fly()
```
\>\>\> `'I can use tools'`  
\>\>\> `'I can fly'`  

Human 과 Bird 클래스 두가지를 상속받았기에 Human 과 Bird 의 메서드를 모두 다 사용가능함을 알 수 있다.

<br>

## Notice

다중 상속시, 상속 순서에 따라 결과가 달라질 수 있다.

### Example

```python
class Airplane:
    def fly(self):
        print("Airplanes can fly")
        

class Bird:
    def fly(self):
        print("Birds can fly")
```
위에 정의된 Airplane 과 Bird 클래스는 같은 이름의 메서드 fly를 가지고 있다.

```python
class Unknown(Airplane, Bird):  # Airplane 클래스와 Bird 클래스를 상속받았다. (Airplane 클래스를 먼저 상속받았다.)
    pass
```
Unknown 클래스는 Airplane 과 Bird 를 다중 상속받았다.  
이제 객체를 만들고 fly 메서드를 호출했을 때의 결과를 살펴보자.  

```python
instance = Unknown()
instance.fly()
```
\>\>\> `'Airplanes can fly'`  

Airplane 클래스의 fly 메서드가 호출되었다.  

이번엔 Unknown 클래스의 다중 상속 순서를 바꿔 Bird 클래스를 먼저 상속시키겠다.  

```python
class Unknown(Bird, Airplane):  # Bird 클래스를 먼저 상속받았다.
    pass
```
이제 결과를 보자.  

```python
instance = Unknown()
instance.fly()
```
\>\>\> `'Birds can fly'`  

이번엔 Bird 클래스의 fly 메서드가 호출되었음을 알 수 있다.  

이러한 결과를 통해 알 수 있는 것은, 다중 상속을 할 때 **먼저 상속된 클래스(왼쪽)가 우선순위**가 된다는 것을 알 수 있다.  따라서 다중 상속을 할 때, 순서에도 유의를 해야한다. 메서드의 우선순위를 쉽게 알 수 있는 방법은 아래의 MRO에서 계속해서 다루도록 하겠다.

<br><br>

# Method Resolution Order (MRO)

**형태 :** `ClassName.mro()`, `ClassName.__mro__`  

Method Resolution Order (MRO) 는 "메서드 탐색 순서" 라고도 불리며  
클래스의 복잡한 상속관계로 인해 알기힘든 메서드 처리순서를 알 수 있게 해준다.  

<br>

## Example

```python
class Airplane:
    def fly(self):
        print("Airplanes can fly")
        

class Bird:
    def fly(self):
        print("Birds can fly")
```
```python
class Unknown(Airplane, Bird):  # 클래스 Airplane 을 먼저 상속받았다.
    pass
```
다중 상속에서 먼저 상속받는 것이 우선됨을 위의 코드를 통해 알 수 있었다.  
그러나 훨씬 복잡한 관계속에서는 우선순위 판단이 힘들어질수있기에 순서를 보여주는 MRO 를 활용해보자.

```python
Unknown.mro()  # Unknown.__mro__ 와 동일한 결과가 나온다.
```
\>\>\> `[__main__.Unknown, __main__.Airplane, __main__.Bird, object]`  

자식클래스인 Unknown, 그리고 상속받은 순서대로 Airplane → Bird 순임을 알 수 있다.  
이번엔 Unknown 클래스의 상속 순서를 바꾼 뒤 살펴보자.

```python
class Unknown(Bird, Airplane):
    pass

Unknown.mro()
```
\>\>\> `[__main__.Unknown, __main__.Bird, __main__.Airplane, object]`  

자식클래스인 Unknown, 그리고 상속받은 순서대로 Bird  → Airplane 순임을 알 수 있다.  
클래스의 상속관계가 복잡할 경우, mro를 통해서 메서드의 우선순위를 쉽게 알 수 있다.

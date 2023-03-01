---
title: "[Python] Class - Abstract Class"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - abstract

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Abstract Class (추상클래스)

- 추상클래스가 되면 인스턴스(객체)를 생성할 수 없다.  

- 다른 클래스에게 상속만 가능해진다.  

- `@abstractmethod` 를 통해 자식클래스가 필수로 오버라이딩(Overriding) 해야하는 메서드를 지정한다. (적어도 한개는 필수로 지정해야한다.)

<br>

## Example

```python
class Animal:
    def __init__(self):
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")

        
class Human(Animal):
    def __init__(self):
        print("I'm a human")
        
    def tool(self):
        print("I can use tools")

        
class Bird(Animal):
    def __init__(self):
        print("I'm a bird")
        
    def fly(self):
        print("I can fly")
```
위의 예시코드를 보면, Human 과 Bird 클래스는 Animal 의 일종이기 때문에 (IS-A 관계) Animal 클래스를 상속받고있음을 알 수 있다.  
그리고 Human 과 Bird 는 인스턴스를 생성해도 상대적으로 구체적인 모습이 떠오른다.  
Human 의 인스턴스를 만들면 어떤 한 사람이라는 객체가 생기는 것이고,  
Bird 의 객체를 만들면 어떤 새라는 객체가 생기는 것이다.  
그런데 Animal 은 구체화된 형태가 없는 추상적인 큰 의미이다.  
객체를 만들기에는 형태가 매우 추상적이다.  
이러한 형태의 클래스를 실제로 **객체를 만들지 못하게 선언하는 것이 클래스의 추상화이다.**  
추상화된 클래스는 다른 클래스에 상속시키는 용도로 주로 사용되며,  
자식클래스에게 구현해야할 필수 메서드를 지정할 수 있다.  

<br><br>

## How to Make Abstract Class

### Step1

**abc 모듈을 import 한다.**

```python
from abc import *
```
> abc 는 Abstract Base Class 의 약자이다.

<br>

### Step2

**추상화 시킬 클래스의 `()`안에 `metaclass=ABCMeta` 를 입력**

```python
class Animal(metaclass=ABCMeta):
    def __init__(self):
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    def additional(self):
        pass
```

<br>

### Step3

**자식클래스에서 구현해야할 메서드에 @abstractmethod 를 적용**  


- 추상클래스에서 어떤 메서드에도 `@abstractmethod` 를 적용하지 않을 경우, 해당 클래스는 추상화되지 않는다. 따라서, 적어도 한개는 `@abstractmethod` 를 적용해야한다.

- `@abstractmethod` 가 적용된 메서드는 자식클래스가 필수로 오버라이딩 해야한다. 그렇게 하지 않으면, 자식클래스도 추상화되어버린다.  


```python
class Animal(metaclass=ABCMeta):
    @abstractmethod  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
    def __init__(self):
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    @abstractmethod  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
    def additional(self):
        pass
```

<br>

### Completed Form

```python
from abc import *

class Animal(metaclass=ABCMeta):
    @abstractmethod  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
    def __init__(self):
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    @abstractmethod  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
    def additional(self):
        pass
```

<br><br>

## Practice

```python
from abc import *


class Animal(metaclass=ABCMeta):
    @abstractmethod
    def __init__(self):
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    @abstractmethod
    def additional(self):
        pass


class Human(Animal):
    def __init__(self):
        print("I'm a human")
        
    def tool(self):
        print("I can use tools")

        
class Bird(Animal):
    def __init__(self):
        print("I'm a bird")
        
    def fly(self):
        print("I can fly")
        
    def eat(self):
        print("I eat something")
```
이제 Animal 의 인스턴스가 만들어지는지 확인해보자.

```python
new_obj = Animal()
```
\>\>\> `TypeError`

<br>

Animal 은 추상클래스가 되었기 때문에 인스턴스가 만들어지지않는다.  
문제없이 추상화 시켰음을 알 수 있다. 이제 Human 의 인스턴스를 만들어보자.

<br>

```python
new_obj = Human()
```
\>\>\> `TypeError`

<br>

추상클래스가 아닌 Human 또한 인스턴스를 만들 수가 없다.  
그 이유는 Human 클래스에 additional 메서드가 정의되지 않았기 때문이다.  
Animal 클래스에서 `@abstractmethod` 로 지정한 메서드 두가지이다.

- `__init__`

- `additional`

이 두가지 메서드는 Animal 클래스를 상속받은 자식클래스(파생클래스)는 필수적으로 구현해야만 한다.  
따라서 Human 클래스와 Bird 클래스에 additional 메서드를 추가적으로 구현해주면 문제가 해결된다.  


추상메서드로 지정된(`@abstractmethod`) 메서드를 오버라이딩 하지 않으면, 상속받은 자식클래스도 추상클래스가 되어버린다. 그래서 인스턴스가 생성되지 않은 것이다.  
**지정된 추상메서드는 오버라이딩 해야함을 잊어서는 안된다.**

```python
from abc import *


class Animal(metaclass=ABCMeta):
    @abstractmethod
    def __init__(self):  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
        print("I'm an animal")
        
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    @abstractmethod
    def additional(self):  # 해당 메서드는 자식클래스가 필수로 오버라이딩 해야한다.
        pass


class Human(Animal):
    def __init__(self):  # 필수
        print("I'm a human")
        
    def tool(self):
        print("I can use tools")
        
    def additional(self):  # 필수
        print("Problem is solved")

       
class Bird(Animal):
    def __init__(self):  # 필수
        print("I'm a bird")
        
    def fly(self):
        print("I can fly")
        
    def eat(self):
        print("I eat something")

    def additional(self):  # 필수
        print("Problem is solved")
```
`@abstractmethod` 로 지정된 메서드를 자식클래스에서도 모두 구현하였다.  
이제 인스턴스 생성에 문제없는지 확인해보자.  

```python
new_obj = Human()
```
\>\>\> `'I'm a human'`

<br>

문제없이 생성되었다. Human 의 메서드와 상속받은 메서드가 잘 동작하는지 확인해보겠다.

```python
new_obj.move()
```
\>\>\> `'I can move'`

```python
new_obj.tool()
```
\>\>\> `'I can use tools'`

```python
new_obj.additional()
```
\>\>\> `'Problem is solved'`

<br>

문제없이 작동함을 확인할 수 있다.
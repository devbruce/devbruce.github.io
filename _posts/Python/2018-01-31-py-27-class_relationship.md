---
title: "[Python] Class - Relationship (IS-A / HAS-A)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - relationship
  - IS-A
  - HAS-A

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# IS-A

- Human (IS-A)n Animal : 사람은 동물이다.(사람은 동물의 한 종류이다.) 처럼 포함관계에 해당하면 IS-A 관계라고 한다.
  - 다른 예 : A Laptop IS-A Computer (노트북은 컴퓨터이다.)
  - 주의점: 명제의 역은 성립하지않는다. 즉 Animal (IS-A) Human (동물은 사람이다.) 은 잘못된 명제가 된다.

<br>

- 코드로 위의 상황을 구현할 경우
  - Human(IS-A)n animal 의 경우에는 Human 이 Animal 에 속하는 관계이다.  
  따라서 Animal이 부모클래스가 되고, Human이 자식클래스가 되어 Animal을 상속받는 관계가 된다.  
  즉 자식클래스는 부모클래스의 특성을 모두 가지고 있어야 IS-A 관계가 성립한다.

<br><br>

## Human (IS-A)n Animal 의 관계를 코드로 구현

```python
class Animal:
    def move(self):
        print("I can move")
        
    def eat(self):
        print("I can eat")
        
    def sleep(self):
        print("I need sleep")
        
class Human(Animal):  # Animal 을 상속받았다. (IS-A)
    def tool(self):
        print("I can use tools")
```

동물이라는 존재를 animal 이라는 class로 구현하였고,  
움직이고, 먹고, 잠이 필요한 것을 각각의 메서드(move, eat, sleep)으로 구현하였다.  
사람이라는 존재를 Human 이라는 class로 구현하고,  
동물의 특성을 모두 가지고 있기 때문에 Animal 클래스를 상속받았다.(IS-A 관계)  

이제 첫번째 사람을 인스턴스로 만들어서 행동을 취해보자.

```python
first_human = Human()  # Human 클래스의 인스턴스 first_human 을 만든다.

first_human.move()  # Animal 클래스의 메서드 move 를 호출 (Human 클래스가 Animal 클래스를 상속받았기에 가능하다.)
first_human.tool()  # Human 클래스의 메서드 tool 을 호출
```
\>\>\> `'I can move'`  
\>\>\> `'I can use tools'`  

`first_human` 이라는 Human의 인스턴스를 만들어서,  
동물이라면 어떤 존재도 할 수 있는 움직임(move)를 하고,  
사람의 특징 중 하나인 도구사용(tool)을 한 결과, 문제없이 결과가 출력됨을 알 수 있다.

<br><br>

# HAS-A

- 포함관계가 아닌, '특정객체가 다른 객체를 소유한다' 라는 개념의 관계  

- 종류
  - Aggregation Relationship
  - Composition Relationship

<br>

## Aggregation Relatonship (HAS-A)

- Life cycle 이 다르다.

- 결합도가 낮다. (Weak Coupling)

- 예시 : A police HAS-A gun. (경찰과 총의 관계)

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-27-has+a+aggregation.png?raw=true){: width="400px"}

<br>

### Example

```python
class Gun:
    def __init__(self):
        print("New gun is created")
        
    def bang(self):
        print("Bang! Bang! Bang!")
        
class Police:
    def __init__(self):
        print("One person become a police")
        self.gun = None
        
    def receive_gun(self, gun_type):  # 총을 받는다. (gun_type 은 Gun 클래스의 인스턴스가 인자로 전달된다.)
        self.gun = gun_type
        
    def shoot(self):  
        if self.gun == None:  # 총을 가지고 있지 않다면, 아래 구문 출력
            print("I don't have a gun")
        else:
            self.gun.bang()
            
    def return_gun(self):  # 총을 반납한다.
        self.gun = None
```

위와 같이 Gun 과 Police 를 각각 클래스로 만든다.  
먼저 총 3가지를 만들어보자.(Gun 의 인스턴스를 3개를 만든다.)

```python
pistol = Gun()
revolver = Gun()
shot_gun = Gun()
```
\>\>\> `'New gun is created'`  
\>\>\> `'New gun is created'`  
\>\>\> `'New gun is created'`  

총의 종류 중에서 권총, 리볼버, 샷건 3가지를 만들어보았다.  

이제 한 사람이 경찰이 되어보자.(Police 의 인스턴스 한개를 만든다.)

```python
Bruce = Police()
```
\>\>\> `'One person become a police'`  

Bruce 가 경찰이 되었다.  

경찰이 된 Bruce는 경찰이 되었으니 테스트겸 총을 쐈다.  

```python
Bruce.shoot()
```
\>\>\> `'I don't have a gun'`  

아직 총이없어서 총을 쏘지 못한다. 먼저 총을 받아보자.  

```python
Bruce.receive_gun(pistol)
```

Bruce 는 권총, 리볼버, 샷건 중에 권총(pistol)을 받았다. 이제 총을 쏴보겠다.  

```python
Bruce.shoot()
```
\>\>\> `'Bang! Bang! Bang!'`  

총이 문제없이 발사된다. Bruce는 지금 총이 필요없다. 반납해보자.

```python
Bruce.return_gun()
```

총을 반납했다. 총이 없는데 총을 사용할 수 있는지 테스트해보자.  

```python
Bruce.shoot()
```
\>\>\> `'I don't have a gun'`  

총이 없다고 한다. 문제없이 총을 반납했다.  

위의 사례에서 알 수 있듯이, 경찰과 총은 Life cycle 이 다르다.  
결합도도 낮은 것을 알 수있다.(총과 경찰은 별개의 객체임을 의미한다.)  
이러한 관게를 **Aggregation Relationship** 이라고 한다.  

<br><br>

## Composition Relatonship (HAS-A)

- Life cycle 이 같다.

- 결합도가 높다. (Strong Coupling)

- 예시 : A Car HAS-A Engine. (자동차와 엔진의 관계)

<br>

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-27-has+a+composition.png?raw=true){: width="400px"}

<br>

### Example

```python
class Engine:
    def __init__(self):
        print("New Engine is created")
        
class Car:
    def __init__(self):
        print("New Car is created!")
        self.engine = Engine()  # Engine 의 인스턴스 생성과 동시에 Car 인스턴스에 할당
        print("Engine is equipped")
```

```python
tmp = Car()
```
\>\>\> `'New Car is created!'`  
\>\>\> `'New Engine is created'`  
\>\>\> `'Engine is equipped'`  

클래스 Car 의 인스턴스가 생김과 동시에 클래스 Engine 의 인스턴스도 동시에 생성된다. (자동차에 장착되어 출시)  
같이 생성되고 같이 사라지는 높은 결합성을 가지고 있음을 알 수 있다.  
이러한 관계를 **Composition Relationship** 이라고 한다.  

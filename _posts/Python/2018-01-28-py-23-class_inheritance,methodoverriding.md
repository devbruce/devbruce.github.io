---
title: "[Python] Class - Inheritance / Method Overriding"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - inheritance
  - method overriding

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Inheritance (상속)

클래스의 상속 : 자식클래스(상속받는 클래스)가 부모클래스(상속하는 클래스)의 멤버를 물려받을 수 있다.  

`class 클래스명(부모클래스)`

```python
class StartGame:
    game_title = "Fantasy_world"
    
    def __init__(self, name, _class):  # 생성자 (Constructor)
        self.name = name
        self._class = _class
        
    def show_info(self):
        print("Name : {}".format(self.name))
        print("Class : {}".format(self._class))
        
    def __del__(self):  # 소멸자
        print("{} 캐릭터가 삭제되었습니다.".format(self.name))

class NewGame:
    pass
```
클래스 두가지가 정의되었다. `NewGame` 클래스는 아무런 기능이없다.  
이제 `NewGame` 클래스가 자식클래스가 되어 상속을 받아보자.

<br><br>

```python
class NewGame(StartGame):
    pass
```

NewGame 클래스가 `StartGame` 클래스를 상속받았다.
> StartGame 클래스 = 부모클래스  
> NewGame 클래스 = 자식클래스

<br><br>

```python
gun_first = NewGame("first", "gunner")
gun_first.show_info()
```
\>\>\> `'Name : first'`  
\>\>\> `'Class : gunner'`  

`NewGame` 의 인스턴스 `gun_first` 가 `StartGame` 의 메서드를 상속받아 문제없이 사용함을 알 수 있다.  

이제 의문이 생길 것이 있다. 만약 부모클래스와 자식클래스에 같은 이름의 메서드가 있다면 어떻게 될까?  
이 부분에 대한 해답은 Method Overriding 파트에서 설명하겠다.

<br><br>

# Method Overriding (메서드 오버라이딩)

Method overriding : 자식클래스가 부모클래스를 상속받았을 때, 동일한 이름의 메서드가 있을 경우 **자식클래스의 메서드가 오버라이딩한다.**

예시를 바로 작성해보자.

```python
class StartGame:
    game_title = "Fantasy_world"
    
    def __init__(self, name, _class):  # 생성자 (Constructor)
        self.name = name
        self._class = _class
        
    def show_info(self):  # 자식클래스 (NewGame) 와 중복되는 이름의 메서드
        print("Name : {}".format(self.name))
        print("Class : {}".format(self._class))
        
    def __del__(self):  # 소멸자
        print("{} 캐릭터가 삭제되었습니다.".format(self.name))
        

class NewGame(StartGame):  # 자식클래스 (NewGame) 이 부모클래스 (StartGame) 을 상속받았다.
    game_title = "Gun_World"
    
    def show_info(self):  # 부모클래스 (StartGame) 와 중복되는 이름의 메서드
        print("This message will be appeared by Method Overriding")
```
위에 정의된 `StartGame` 클래스, `NewGame` 클래스 모두 `show_info` 라는 이름을 가진 메서드를 가지고 있다.  
하지만 `NewGame` 클래스가 `StartGame` 클래스를 상속받은 상태이다.  
이제 결과를 살펴보자.

```python
gun_first = NewGame("first", "gunner")
gun_first.show_info()
```
\>\>\> `This message will be appeared by Method Overriding`  

자식 클래스에 해당하는 `NewGame` 클래스의 메서드가 호출됨을 알 수있다.  
조금 더 나아가서 메서드가 아닌 변수도 적용이 되는지 살펴보자.

```python
gun_first.game_title
```
\>\>\> `'Gun_World'`  

변수 또한 오버라이딩 된 것을 알 수 있다.  
이를통해 알 수 있는 것은, 파이썬은 상속받을 경우, **자식 클래스의 멤버들이 우선**시 된다는 것이다.

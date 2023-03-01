---
title:  "[Python] Class - Constructor / Destructor"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - constructor
  - destructor

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Constructor (생성자)

생성자는 객체를 생성할 때 초깃값을 설정할 수 있게 해준다.

```python
class MakeCharacter:
    game_title = "Fantasy_World"
    
    def set_name(self, name):
        self.name = name
        
    def set_class(self, _class):
        self._class = _class
        
    def show_info(self):
        print("Name : {}".format(self.name))
        print("Class : {}".format(self._class))
```

```python
ch_first = MakeCharacter()
```

클래스를 생성하고 객체를 `ch_first` 라는 객체를 생성하였다. 
문제는 `set_name` 과 `set_class` 라는 메서드를 통해 이름과 클래스를 설정하지않고,  
이름과 클래스 정보를 보여주는 `show_info` 메서드를 호출했을 경우에는 아래와 같이 에러가 발생한다.

```python
ch_first.show_info()
```
\>\>\> `AttributeError`  

위와 같은 상황은 생성자를 사용한다면 통제가 가능하다.  
객체를 생성할 때, 처음부터 이름과 클래스를 지정하도록 할 수 있기 때문이다.  
아래 코드는 생성자를 사용하여 재정의한 클래스이다.

```python
class MakeCharacter:
    game_title = "Fantasy_World"
    
    def __init__(self, name, _class):  # 생성자 (Constructor)
        self.name = name
        self._class = _class
        
    def show_info(self):
        print("Name : {}".format(self.name))
        print("Class : {}".format(self._class))
```

<br>

이제 객체를 생성할 때, 이름과 클래스를 지정해주어야 생성된다.  
그렇지 않을경우 에러가 발생함을 아래코드를 통해 살펴보자.

```python
ch_first = MakeCharacter()
```
\>\>\> `AttributeError`

<br>

객체를 제대로 생성해보자.

```python
ch_first = MakeCharacter("first", "Wizard")
```

<br>

생성된 객체를 통해 `show_info` 메서드를 호출해보자.

```python
ch_first.show_info()
```
\>\>\> `'Name : first'`  
\>\>\> `'Class : Wizard'`  

<br><br>

# Destructor (소멸자)

소멸자 : 생성된 객체를 제거할 때의 상황을 통제할 수 있다.

```python
class MakeCharacter:
    game_title = "Fantasy_World"
    
    def __init__(self, name, _class):  # 생성자 (Constructor)
        self.name = name
        self._class = _class
        
    def show_info(self):
        print("Name : {}".format(self.name))
        print("Class : {}".format(self._class))
        
    def __del__(self):  # 소멸자
        print("{} 캐릭터가 삭제되었습니다.".format(self.name))
```

객체를 생성해보겠다.

```python
ch_first = MakeCharacter("first", "Wizard")
```

<br>

생성된 객체를 삭제하겠다.

```python
del ch_first
```
\>\>\> `'first 캐릭터가 삭제되었습니다.'`  

소멸자가 정상적으로 호출됐음을 알 수 있다.

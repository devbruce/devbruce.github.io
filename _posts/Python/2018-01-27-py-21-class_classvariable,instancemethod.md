---
title: "[Python] Class - Class variable / Instance method / Instance variable"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - naming convention
  - variable
  - instance
  - method

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Class Naming Convention

**CamelCase** : 클래스 이름을 정의할 때는 대문자를 활용하여 작성한다.  
sneak_case와는 다르게 언더바를 사용하지않고, 대문자를 이용하여 단어의 띄워쓰기를 구분한다고 할 수 있다.

<br><br>

# Class variable (클래스 변수)

```python
class MakeCharacter:
    game_title = "Fantasy_World"  # 클래스 변수
```
클래스를 선언하고, 클래스 변수로 game_title 을 선언하였다.  
클래스 변수는 클래스 내부에서 전역변수의 역할을 하기 때문에  
클래스가 선언됨과 동시에 메모리에 저장된다.  
즉 인스턴스(객체)가 만들어지기 전부터 메모리에 할당되기 때문에 인스턴스 생성과는 상관없이 호출이 가능하다.

```python
print(MakeCharacter.game_title)
```
\>\>\> `'Fantasy_World'`  

```python
ch_first = MakeCharacter()
ch_second = MakeCharacter()
```
MakeCharacter 의 인스턴스 2개를 만들었다. 이를 통해 클래스 변수를 호출해보겠다.

```python
print(ch_first.game_title)
print(ch_first.game_title)
```
\>\>\> `'Fantasy_World'`  
\>\>\> `'Fantasy_World'`  

<br><br>

# Instance method (인스턴스 메서드) / Instance variable (인스턴스 변수)

위에서 정의했던 클래스에 인스턴스 메서드와 인스턴스 변수를 추가해보겠다.  
클래스 내에서 함수를 정의하는 것과 동일하다.

```python
class MakeCharacter:
    game_title = "Fantasy_World"  # 클래스 변수
    
    def setname(self, name):  # 인스턴스 메서드
        self.name = name  # 인스턴스 변수
```

```python
ch_first = MakeCharacter()
```

MakeCharacter의 인스턴스(객체)를 만들었다.  
인스턴스 메서드를 통해서 ch_first의 이름을 정한 뒤 출력해보자.

```python
ch_first.setname("first")
print(ch_first.name)
```
\>\>\> `'first'`  

문제없이 출력되었다.

<br>

그런데 코드내에서 self의 존재에 의문을 가질 것이다.  
파이썬 클래스의 특징이기도 한 부분인데, self는 해당 인스턴스가 자동으로 인자로 들어간다고 이해하면 된다.  
수동으로 self에 인자를 전달하는 경우를 코드로 보자.

```python
ch_first.setname(ch_first, "first") # (1) 인스턴스로 접근 (보편적인 접근방법이며, self 인수 부분을 생략가능)

MakeCharacter.setname(ch_first, "first")  # (2) 클래스명으로 접근 (self 인수 생략 불가)
```

인스턴스를 self 에 인자로 수동으로 할 경우 위와 같이 된다.  
인스턴스로 접근할 경우에는 self 인수 부분을 수동적으로 써주어도 되고, 무시해도 상관없다.  
클래스명으로 접근하는 두번째의 경우, self 부분을 무조건 써주어야만 한다는 것이 큰 차이점이다.  

**파이썬의 클래스는 제작할 때는 self를 필수적으로 써주어야 한다. 하지만 클라이언트로서 사용할 경우, 신경쓰지 않아도 된다.**

> 신경쓰지 않아도 된다고 언급한 것은 인스턴스로 접근했을 경우(위의 코드에서 (1) 라인)를 말한 것이다.  
> 위에도 설명했듯, 클래스명으로 접근할 경우(위의 코드에서 (2) 라인)에는 self를 무시해서는 안된다.

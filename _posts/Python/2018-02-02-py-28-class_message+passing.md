---
title: "[Python] Class - Message Passing"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - message passing

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Message Passing (메시지 전달)

Class 에서 Message Passing 이란 하나의 객체가 다른 객체에게  
함수호출을 기반으로 메시지를 전달 (영향을 준다) 하는 것을 의미한다.  

<br>

## Example

```python
class MakeCh:
    def __init__(self, name, power):
        self.name = name
        self.power = power
        self.HP = 100
        
    def attack(self, other):
        other.HP -= self.power
        print("{} 가 {}에게 {}의 피해를 주었다.".format(self.name, other.name, self.power))
```

<br>

위의 코드는 캐릭터를 만드는 클래스가 있다.  
위의 MakeCh 의 인스턴스 두개를 만들어보겠다.  

```python
ch_a = MakeCh('A', 5)  # 캐릭터이름: A, 공격력: 5
ch_b = MakeCh('B', 3)  # 캐릭터이름: B, 공격력: 3
```

두개의 캐릭터(MakeCh의 인스턴스 2개)를 만들었다.  
이제 캐릭터 A가 B에게 공격을 시도하게 된다면 서로 다른 객체에게 영향을 주는 상황이 된다.  
(A가 B의 HP에 영향을 준다. 즉 A가 B의 객체의 HP를 줄이도록 메시지를 전달한다.)  

실제로 A가 B를 공격하는 명령을 해보자.  

```python
ch_a.attack(ch_b)
```
\>\>\> `'A 가 B에게 5의 피해를 주었다.'`  

B의 체력이 실제로 줄어들었는지 확인해보자.  

```python
ch_b.HP
```
\>\>\> 95  

메시지가 문제없이 전달되었음을 알 수 있다.  
그러나 이것을 Message Passing 이라고 할 수 있을까?  
위 코드를 살펴보면, 메시지를 전달하는데 함수를 이용한 것이 아니라,  
객체 `ch_b` 의 인스턴스 변수에 직접적으로 접근했음을 알 수 있다.  

Message Passing 은 함수 호출을 기반으로 메시지를 전달하는 것이므로 위의 코드는  
Message Passing 이라 보기 어렵다.  

이제 위의 상황을 Messsage Passing 을 통하여 재구성해보겠다.  

```python
class MakeChMP:
    def __init__(self, name, power):
        self.name = name
        self.power = power
        self.HP = 100
        
    def damaged(self, other):  # Message Passing을 위해 추가된 메서드
        self.HP -= other.power
        print("{} 의 HP : {}".format(self.name, self.HP))
        
    def attack(self, other):
        other.damaged(self)  # 인스턴스 변수에 직접 접근이 아닌, 함수 호출(메서드 호출) 을 통하여 메시지를 전달하였다.
        print("{} 가 {}에게 {}의 피해를 주었다.".format(self.name, other.name, self.power))
```

처음 제시했던 코드에 damaged라는 메서드가 추가되었음을 알 수있다.  
그리고 attack 메서드에 damaged 메서드를 호출하여 다른 객체에 메시지를 전달하고 있다.  

문제없이 작동하는지 확인해보자.  

<br>

```python
ch_a = MakeChMP("A", 5)
ch_b = MakeChMP("B", 3)

ch_a.attack(ch_b)  # A가 B를 공격 (ch_a 객체가 ch_b 객체에 메시지 전달)
```
\>\>\> `'B 의 HP : 95'`  
\>\>\> `'A 가 B에게 5의 피해를 주었다.'`  

객체 `ch_b`의 HP가 정상적으로 줄어들었는지 확인해보자.

```python
ch_b.HP
```
\>\>\> 95  

다른 객체에게 메시지가 잘 전달되었음을 알 수 있다.  
즉 Message Passing 의 중요한 점은  
**함수 호출을 기반** 으로 다른 객체에게 메시지를 전달하는 것이다.

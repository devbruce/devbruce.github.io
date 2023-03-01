---
title: "[Python] Class - Diamond Inheritance"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - diamond inheritance
  - super

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Diamond Inheritance (다이아몬드 상속)

- 다이아몬드 상속이란 상속관계가 아래의 이미지와 같은 경우를 의미한다.

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-26-diamond+inheritance01.png?raw=true){: width="400px"}

<br><br>

- 최상단의 클래스 A가 존재한다.  

- 클래스 B와 클래스 C는 클래스 A를 상속받는다.  

- 클래스 D는  클래스 B와 C (클래스 A를 상속받은) 를 상속받는다.  

여기서 **문제의 여지가 있는 것은, 클래스 D이다.**  

클래스 D는 클래스 B와 C를 상속받았는데, 클래스 B와 C는 클래스 A를 각각 상속받은 상태이다.  
즉 클래스 D가 클래스 B와 C를 상속받으면 클래스 A를 중복으로 2번 상속받는 결과가 나타난다.

이러한 상황을 쉽게 이해하기 위해 있을법한(?) 상황을 만들어보았다.  

![image](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/Python/images/py-26-diamond+inheritance02.png?raw=true){: width="400px"}

<br>

Animal (동물) 에는 여러가지가 있지만 이 그림에서는 Human(사람) 과 Bird(새) 두가지를 클래스로 나타내었다.  
Human 과 Bird 는 둘 다 Animal 이기 때문에 Animal 클래스를 상속받았다.  
그런데 여기서 새로운 알 수 없는 생명체가 나타났다. 이를 Unknown 이라고 정의하겠다.  
이 알 수 없는 생명체 Unknown 은 Human 과 Bird 의 특징을 모두 가지고 있다. 그래서 Human 과 Bird 를 상속받았다.  

이제 위의 상황을 코드로 구현해보겠다.

```python
class Animal:
    def __init__(self):
        print("I'm an animal")
        
class Human(Animal):  # 사람은 동물이기 때문에 Animal 클래스를 상속받았다.
    def __init__(self):
        Animal.__init__(self)
        print("I'm a human")
        
class Bird(Animal):  # 새는 동물이기 때문에 Animal 클래스를 상속받았다.
    def __init__(self):
        Animal.__init__(self)
        print("I'm a bird")
        
class Unknown(Human, Bird):  # Unknown 은 사람과 새의 특징을 모두 가졌기 때문에 Human 과 Bird 클래스를 둘 다 상속받았다.
    def __init__(self):
        Human.__init__(self)
        Bird.__init__(self)
        print("I'm Unknown. Please give me a name")
```

이제 Unknown 의 인스턴스를 만들어보자.
> 혹시라도, `Animal.__init__(self)` 부분이 이해가 되지 않는다면,  
> [[Python] Class - How to control Method Overriding](https://devbruce.github.io/python/py-24-class_how+to+control+methodoverriding/) 참고

<br>

```python
instance = Unknown()
```
\>\>\> `'I'm an animal'`  
\>\>\> `'I'm a human'`  
\>\>\> `'I'm an animal'`  
\>\>\> `'I'm a bird'`  
\>\>\> `'I'm Unknown. Please give me a name'`  

위에서 우려한바와 같이, 클래스 Animal 이 중복되어 적용된 나머지 I'm an animal 이 두번 나온 것을 알 수 있다.  
다시 설명하자면, Unknown은 Human 과 Bird 두가지 클래스를 상속받았다.  
여기서, Human 도 Animal 을 상속받았고, Bird 또한 Animal을 상속받았기에,  
Human 과 Bird 를 둘 다 상속받은 Unknown 은 Animal 을 두번 상속받은 꼴이 되기 때문이다.  

그렇다면, 이러한 다이아몬드 형태의 상속관계를 중복되는 문제없이 적용시킬 방법은 없을까?  
여기서 `super()` 메서드를 통해 해답을 찾을 수 있다.  
위의 코드를 super()를 활용하여 수정해보겠다.

> `super()` 에 대해서 잘 모른다면  
> [[Python] Class - How to control Method Overriding](https://devbruce.github.io/python/py-24-class_how+to+control+methodoverriding/) 참고

<br>

```python
class Animal:
    def __init__(self):
        print("I'm an animal")
        
class Human(Animal):
    def __init__(self):
        super().__init__()
        print("I'm a human")
        
class Bird(Animal):
    def __init__(self):
        super().__init__()
        print("I'm a bird")
        
class Unknown(Human, Bird):
    def __init__(self):
        super().__init__()
        print("I'm Unknown. Please give me a name")
```

```python
instance = Unknown()
```
\>\>\> `'I'm an animal'`  
\>\>\> `'I'm a bird'`  
\>\>\> `'I'm a human'`  
\>\>\> `'I'm Unknown. Please give me a name'`  

중복되던 Animal 클래스의 메서드 호출이 사라졌음을 알 수 있다.
> 추가적으로, 상속 순서의 우선순위도 Human → Bird 에서 Bird → Human 으로 바뀌었음을 알 수 있다.  
> `super()`메서드를 사용했을 때의 또다른 특징 중 하나이다.

<br>

이러한 결과를 통해, 클래스명을 통해 부모클래스를 호출하는 것과 `super()` 메서드를 활용했을 때의 차이점을 알 수 있다.  
코드의 순서그대로 출력을 원한다면 클래스명을 통해 메서드를 출력하는 것이 타당할 것이고,  
관계가 복잡해져 **중복호출을 예방**하기 위해서는 `super()` 메서드를 사용해야함을 알 수 있다.

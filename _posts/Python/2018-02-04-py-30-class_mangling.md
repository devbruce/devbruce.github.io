---
title: "[Python] Class - Mangling"
excerpt: 
last_modified_at: 2020-05-11

categories:
  - Python

tags:
  - python
  - class
  - Mangling
  - information hiding

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Class - Mangling

## Information Hiding (정보은닉)

- 코드를 제공할 때, 수정되어서는 안되는 코드를 보호하기 위해 접근을 못하도록 제한하는 것을 의미한다.(안정적 구현이 목적)  

- Python 은 지원하지 않지만, `__` 를 통해 정보은닉 의도를 표현할 수는 있다.  
  - 정확히는 Name Mangling 이다.

<br><br>

## Example

- Information Hiding 사용하지 않은 경우

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        
        self.num = MakeCh.ch_num
        self.name = name
```

위의 코드는, 캐릭터를 만드는 클래스이다.  
`MakeCh` 클래스의 인스턴스를 생성(캐릭터 생성)을 하게되면 고유번호가 캐릭터에게 붙게된다.  
첫번째 캐릭터는 1이, 두번째는 2가 되도록 `num`이 1씩 상승하도록 해두었다.  
그리고 캐릭터 이름은 생성자를 통해 작성하도록 하였다.  
이제 캐릭터를 직접 만들어보자.  

```python
ch_first = MakeCh("first")  # 인스턴스 생성
```
첫번째 인스턴스 `ch_first` 는 이름을 `first`로 생성하였다.  
생성된 인스턴스의 이름과 번호를 확인해보자.

```python
ch_first.num
```
\>\>\> 1  

```python
ch_first.name
```
\>\>\> `'first'`  

문제없이 출력됨을 알 수 있다.  
그런데, 생성된 캐릭터 `ch_first` 의 소유자가 캐릭터의 고유번호와 이름이 갑자기 마음에 안들어서 바꾸고자 마음 먹었다.  
그리고 코드를 살펴본 결과 변수에 접근하는 것에 아무런 제한이 없다.  
그래서 아래와 같이 마음대로 수정하였다.  

```python
ch_first.num = 77
ch_first.name = "Awesome"
```

원하는대로 인스턴스 변수에 직접 접근하여 수정하였다. 이제 결과를 보자.

```python
ch_first.num
```
\>\>\> 77  

```python
ch_first.name
```
\>\>\> `'Awesome'`  

수정이 너무나도 쉽게 되었다.  
코드를 직접 작성했던 개발자는 이러한 상황을 원치않을 수 있다.  
이러한 상황을 막기위해 Information Hiding (정보은닉)을 사용해보자.  

<br>

- Information Hiding 사용한 경우

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num  # __ (double underscore) 를 num 앞에 붙였다.
        self.__name = name  # __ (double underscore)를 name 앞에 붙였다.
        
    def get_num(self):  # num 접근자
        return self.__num
        
    def get_name(self):  # num 접근자
        return self.__name
```

처음 제시된 코드와 다른점은, `__`(double underscore) 가 `num` 과 `name` 앞에 붙었다는 것이다.  
Double Underscore 가 앞에 붙게되면 Python 에서 정보은닉과 유사한 효과를 볼 수 있다.  
변수에 직접적인 접근이 되는지 확인해보자.  

```python
ch_first = MakeCh("first")  # 인스턴스 생성
```

먼저 인스턴스를 생성한다.

```python
ch_first.__name
```
\>\>\> `AttributeError`  

```python
ch_first.__num
```
\>\>\> `AttributeError`  

변수 `__num` 과 `__name` 에 접근이 되질 않는다.  
정보은닉 (Information Hiding)이 되었다고 할 수 있다.  
그런데 Python은 Information Hiding 을 사실상 지원하지 않는다.  
해당 결과는 Information Hiding 이 아닌, Python 의 Mangling 이라고 한다.  

```python
ch_first.__dict__
```
\>\>\> ```{'_MakeCh__name': 'first', '_MakeCh__num': 1}```  

각 인스턴스 변수들이 `__클래스이름__변수` 형태로 변환되어있다.  
즉 다른이름으로 Mangling 되었다고 할 수 있다.  
그럼 위의 형태로 접근하는 것이 가능한지 확인해보겠다.  

```python
ch_first.__MakeCh__num = 111
ch_first.__MakeCh__name = "NewName"
```

직접 변수에 접근한 것이 맞는지 접근자 함수를 통해 결과값을 확인해보자.  

> 접근자에 대한 설명은 다음 포스팅 [[Python] Class - getter, setter](https://devbruce.github.io/python/py-31-class_getter_setter/) 에서 다루겠다.  
어려운 내용은 아니므로 `get_num()` 과 `get_name()` 메서드를 직접 해석해보고 아래내용을 이해해도 문제없다.

```python
ch_first.get_num()
```
\>\>\> `111`  

```python
ch_first.get_name()
```
\>\>\> `'NewName'`  

인스턴스 변수가 문제없이 변경되었다.  

정리하자면  
Python 에서는 Information Hiding (정보은닉) 기능은 존재하지 않는다.  
하지만, `__` (double underscore)를 통해 "접근금지" 라는 의사표현을 Name Mangling 을 통해 가능하다.  

> 변수 앞에 `_` (single underscore)를 하게되면, 접근을 자제하라는 경고, (protected 제한자와 유사)  
> `__` (double underscore)는 강한 접근 거부 의사를 표현하는 것이라고 볼 수 있다. (private 제한자와 유사)  

---
title: "[Python] Class - Information Hiding (2)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - information hiding

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# 정보은닉된 값에 접근하는 방법

**Example**  

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num  # __ (double underscore) 를 num 앞에 붙였다.
        self.__name = name  # __ (double underscore)를 name 앞에 붙였다.
```

<br>

[[Python] Class - Information Hiding (1)](https://devbruce.github.io/python/py-30-class_information+hiding/) 에서 다루었던 예제코드이다.  
위 코드에는 설명했던대로, `__`(double underscore)를 통해 information hiding 을 하였다.  
즉 인스턴스를 생성 후, `num` 과 `name` 에 직접 접근이 불가능하다.(값 확인 및 수정이 불가능하다.)

<br><br>

```python
ch_first = MakeCh('first')  # 인스턴스 생성
ch_first.__num
```
\>\>\> `AttributeError`

```python
ch_first.__name
```
\>\>\> `AttributeError`  

이러한 문제의 해결책은 아래의 두가지를 클래스내부에 만드는 것이다.  

- **접근자(getter)**: 정보은닉된 값에 접근을 할 수 있도록 해주는 메서드  
- **설정자(setter)**: 정보은닉된 값에 접근하여 수정할 수 있도록 해주는 메서드  

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num  # __ (double underscore) 를 num 앞에 붙였다.
        self.__name = name  # __ (double underscore)를 name 앞에 붙였다.
        
    def get_num(self):  # num 의 접근자
        return self.__num
        
    def set_num(self, num):  # num 의 설정자
        self.__num = num
        
    def get_name(self):  # name 의 접근자
        return self.__name
        
    def set_name(self, name):  # name 의 설정자
        self.__name = name
```

클래스 내부에 접근자와 설정자 메서드를 작성하였다.  
이제 작성된 메서드를 통해 정보은닉된 변수에 접근해보겠다.  

```python
ch_first = MakeCh('first')  # 인스턴스 생성
```

```python
ch_first.get_num()  # 접근자를 통하여 __num 에 접근
```
\>\>\> `1`  

<br>

인스턴스 변수 `__num` 값에 접근자를 통하여 접근하였다.

```python
ch_first.get_name()  # 접근자를 통하여 __name 에 접근
```
\>\>\> `'first'`  

인스턴스 변수 `__name` 값에 접근자를 통하여 접근하였다.  
이번엔 설정자를 통해 변수들의 값을 변경해보겠다.  

```python
ch_first.set_num(777)  # 설정자를 통하여 __num 값 777로 변경
ch_first.set_name('Awesome')  # 설정자를 통하여 __name 값 'Awesome' 으로 변경
```

이제 값이 변경되었는지 접근자를 통해 결과값을 확인해보겠다.

```python
ch_first.get_num()
```
\>\>\> `777`  

인스턴스 변수 `__num` 값에 생성자를 통하여 값을 변경하는 것에 성공하였다.

```python
ch_first.get_name()
```
\>\>\> `'Awesome'`  

인스턴스 변수 `__name` 값에 생성자를 통하여 값을 변경하는 것에 성공하였다.

<br><br>

## 직접 접근방법

`__` (double underscore) 에 의해서 정보은닉된 변수에 직접 접근이 불가능 한 것은 아니다.  
[[Python] Class - Information Hiding (1)](https://devbruce.github.io/python/py-30-class_information+hiding/) 에서도 언급되었듯이,  
해당변수는 `self._ClassName__VariableName`형태로 변환되어 있음을 알 수 있다.  
즉 `self._ClassName__VariableName` 을 호출 및 할당할 경우 직접 접근 할 수 있다.

> `dir()` 메서드를 해당 인스턴스에 사용해보면  
> `self._ClassName__VariableName` 를 확인 가능하다.

<br><br>

## Conclusion

Information hiding (정보은닉) 처리된 값은  
**접근자(getter), 설정자(setter)** 메서드를 통해 접근할 수 있도록 구성하자

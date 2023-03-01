---
title: "[Python] Class - getter, setter"
excerpt: 
last_modified_at: 2020-05-11

categories:
  - Python

tags:
  - python
  - class
  - mangling

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Class - getter, setter

**Example**  

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num  # __ 를 통해 Name Mangling
        self.__name = name  # __ 를 통해 Name Mangling
```

[[Python] Class - Mangling](https://devbruce.github.io/python/py-30-class_mangling/) 에서 다루었던 예제코드이다.  
위 코드에는 설명했던대로, `__`(double underscore)를 통해 Mangling 을 하였다.  
즉 인스턴스를 생성 후, `num` 과 `name` 은 해당 단순하게는 접근 불가능하다. (Mangling 된 이름으로 직접 접근 가능)  

> 해당변수들은 `self._ClassName__VariableName`로 Mangling 된 것을 알 수 있다.  
> 즉 `self._ClassName__VariableName` 을 호출 및 할당할 경우 직접 접근가능하다.  
> 하지만 이러한 접근법은 지양하는 것이 좋다.  
>
> - `dir()` 메서드를 해당 인스턴스에 사용해보면 `self._ClassName__VariableName` 를 확인 가능하다.

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

Mangling 된 이름을 직접 접근하는 것이 아닌,  
변수에 접근하는 방법은 아래의 두가지를 클래스내부에 만드는 것이다.  

- **접근자(getter)**: Mangling 된 값에 접근을 할 수 있도록 해주는 메서드  
- **설정자(setter)**: Mangling 된 값에 접근하여 수정할 수 있도록 해주는 메서드  

```python
class MakeCh:
    ch_num = 0
    
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num
        self.__name = name
        
    def get_num(self):  # num 의 getter(접근자)
        return self.__num
        
    def set_num(self, num):  # num 의 setter(설정자)
        self.__num = num
        
    def get_name(self):  # name 의 getter(접근자)
        return self.__name
        
    def set_name(self, name):  # name 의 setter(설정자)
        self.__name = name
```

클래스 내부에 접근자와 설정자 메서드를 작성하였다.  
이제 작성된 메서드를 통해 Mangling 된 변수에 접근해보겠다.  

```python
ch_first = MakeCh('first')  # 인스턴스 생성
```

```python
ch_first.get_num()  # 접근자를 통하여 __num 에 접근
```
\>\>\> `1`

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

getter, setter를 통해서 Mangling 된 값에 접근하거나 값을 변경이 가능하며,
getter, setter 메서드를 통해서만 접근가능하게 하여 예외처리 및 특정 로직을 설정할 수 있게된다.  

이렇게 직접 getter, setter를 구현할 수도 있지만, python 에서는 `@property`, `@{name}.setter`을 활용하여 구현할 수 있다.  
해당 내용은 [\[Python\] Class - @property / @name.setter](https://devbruce.github.io/python/py-32-class_@property,@name.setter/) 참고

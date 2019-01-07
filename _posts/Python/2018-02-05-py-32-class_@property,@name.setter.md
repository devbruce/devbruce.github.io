---
title: "[Python] Class - @property / @name.setter"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - property
  - getter
  - setter

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# @property, @name.setter

[[Python] Class - Information Hiding (2)](https://devbruce.github.io/python/py-31-class_information+hiding2/)
에서 접근자 method 와 설정자 method 에 대해서 다루었다.

<br>

- **접근자(getter)** : 정보은닉된 값에 접근을 할 수 있도록 해주는 메서드
  - `@property`

- **설정자(setter)** : 정보은닉된 값에 접근하여 수정할 수 있도록 해주는 메서드
  - `@name.setter`

<br>

`@property` 와 `@name.setter` 는 위의 접근자, 설정자와 중요한 관계에 있다.

> Decorator에 대해서는 아래의 포스트 참고  
> [[Python] Decorator (1)](https://devbruce.github.io/python/py-25-decorator/)  
> [[Python] Decorator (2)](https://devbruce.github.io/python/py-29-decorator2/)

<br>

- `@property` 로 메서드를 꾸며주게되면 그 메서드는 접근자가 된다.  

- `@name.setter` 로 메서드를 꾸며주게되면 그 메서드는 설정자가 된다.  

> `@property` 는 인스턴스 메서드에만 사용가능하다.  
> 즉 클래스메서드에는 사용불가능하다. Python에서 자체적으로 지원하지않는다.  
> 필요하다면 구현은 가능하다고 한다.(?)

<br>

## Example

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

[[Python] Class - Information Hiding (2)](https://devbruce.github.io/python/py-31-class_information+hiding2/) 에서 사용했던 예시코드이다.  
간단하게 정보은닉된 인스턴스 변수에 접근하는 과정을 살펴보자.  

```python
ch_first = MakeCh('first')  # 인스턴스 생성

ch_first.get_num()  # 접근자로 인스턴스 변수 __num 에 접근
ch_first.get_name()  # 접근자로 인스턴스 변수 __name 에 접근
ch_first.set_num(777)  # 설정자로 인스턴스 변수 __num 값 변경
ch_first.set_name('Awesome')  # 설정자로 인스턴스 변수 __name 값 변경
```

접근자, 설정자 메서드로 위와같이 정보은닉된 인스턴스 변수에 접근가능하다.  
이제 위의 접근자, 설정자 메서드를 `@property` 를 적용해서 바꾸어보겠다.  

<br>

- `@property` 적용 예시코드

```python
class MakeCh:
    ch_num = 0
    def __init__(self, name):
        MakeCh.ch_num += 1
        self.__num = MakeCh.ch_num  # __ (double underscore) 를 num 앞에 붙였다.
        self.__name = name  # __ (double underscore)를 name 앞에 붙였다.
        
    @property
    def num(self):  # num 의 접근자
        return self.__num
    
    @num.setter
    def num(self, num):  # num 의 설정자
        self.__num = num
        
    @property
    def name(self):  # name 의 접근자
        return self.__name
    
    @name.setter
    def name(self, name):  # name 의 설정자
        self.__name = name
```

<br>

## 적용과정

1. 접근자, 설정마 메서드의 이름은 변수이름으로 통일시킨다.

2. 접근자 메서드에는 `@property` 를 적용

3. 설정자 메서드에는 `@name.setter` 를 적용

<br><br>

위의 과정을 적용시킬 경우, 차이점이 무엇인지 살펴보겠다.  

```python
ch_first = MakeCh('first')  # 인스턴스 생성
```
인스턴스를 생성하는 것까지는 차이점은 없다.  
그러나, 이제부터 접근자, 설정자 매서드 사용방법이 달라진다.  
마치, 변수 자체에 접근하는 것과 같아 보인다. 이것이 가장 큰 차이점이다.

```python
ch_first.num
```
\>\>\> 1

```python
ch_first.name
```
\>\>\> `'first'`  

위에서도 언급했지만, 변수 자체에 접근하는 것 같아 보이지만 실제로는 접근자 메서드를 통해 접근한 것이다.  
설정자도 확인해보자.

```python
ch_first.num = 777
ch_first.name = 'Awesome'
```

인스턴스 변수를 변경하였다.  
보기에는 변수 자체에 접근한 것처럼 보이지만 아니다. 설정자 매서드를 통해 값을 변경한 것이다.  
실제로 값이 변경되었는지 확인해보자.

```python
ch_first.num  # @property 접근자로 접근한 것이다. 변수로 접근한 것으로 착각 X
```
\>\>\> 777

```python
ch_first.name  # @property 접근자로 접근한 것이다. 변수로 접근한 것으로 착각 X
```
\>\>\> `'Awesome'`  

인스턴스 변수값이 문제없이 변경되었음을 알 수 있다.

<br><br>

## Conclusion

`@property`, `@name.setter`를 활용하는 것은  
접근자와 설정자 메서드를 통하여 변수에 접근하는 것과 동일하다.  

- 변수처럼 사용가능하다.  
→ 변수에 직접 접근하는 것과 같은 **직관성**, **간결성** 을 가질 수 있다.  

- 클래스 코드 작성시에도 적용되므로, **편의성**도 생긴다.

- 함수 호출방식이 불가능해진다.

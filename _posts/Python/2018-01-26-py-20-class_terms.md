---
title: "[Python] Class - Terms"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - class
  - term

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Class 기본 용어

> - Method (메서드): 클래스 내부에서 정의된 함수
> - 인스턴스(Instance): 메모리에 구현된 구체적인 하나의 객체

<br>

## Class (클래스)

Member variable (멤버 변수) + Member function (멤버 함수)

<br><br>

### Member variable (멤버 변수)

Class variable (클래스 변수) + Instance variable (인스턴스 변수)

<br>

#### Class variable (클래스 변수)

- 클래스 내부의 전역변수

- 모든 인스턴스가 참조가능

- 인스턴스 생성 없이도 참조가능
 
<br>

#### Instance variable (인스턴스 변수)

- 변수선언시 앞에 `self` 가 오게된다. (ex. `self.tmp = 1234`)

- 인스턴스마다 독립적인 값을 할당가능

<br><br>

### Member function (멤버 함수)

Class method (클래스 메서드) + Instance method (인스턴스 메서드)

<br><br>

#### Class method (클래스 메서드)

- 클래스 내부의 전역함수

- 모든 인스턴스가 호출 가능

- 인스턴스 생성 없이도 호출 가능  

- `@classmethod` 또는 `@static` 데코레이터를 통해 정의된다.

<br>

#### Instance method (인스턴스 메서드)

- 생성된 인스턴스에서 사용가능

<br><br>

## Example

```python
class TmpClass:
    class_var = 'qwer'  # 클래스 변수
    
    def tmp_method(self, a):  # 인스턴스 메서드
        self.var = a  # 인스턴스 변수

tmp_instance = TmpClass()  # TmpClass 의 인스턴스 tmp_instance 생성

tmp_instance.tmp_method('Hello World!')  # tmp_instance.var = 'Hello World!'
```

```python
print(tmp_instance.class_var)  # 클래스 변수 참조
print(tmp_instance.var)  # 인스턴스 변수 참조
```
\>\>\> `'qwer'`  
\>\>\> `'Hello World!'`  

<br><br>

```python
print(TmpClass.class_var)  # 인스턴스 생성없이 클래스 변수참조
```
\>\>\> `'RPG'`

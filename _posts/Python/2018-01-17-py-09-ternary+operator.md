---
title: "[Python] Ternary Operator"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - ternary operator

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Ternary Operator (삼항연산자)

- `Value` + `if조건문` + `else` + `Value` + . . .

<br>

- 삼항연산자란 조건문을 **간결성, 가독성**을 위해 줄바꿈없이 작성하는 것

- 작성시 `elif`는 사용하지 않는다.
  - `elif` = `else` + `if` 임을 인지하고 작성한다.

## Example

```python
num = 3
```

먼저 변수 `num`을 선언하고, 조건문을 활용하여 원하는 값을 도출해보겠다.

```python
if num == 3:
    print("Equal!")

else:
    print("Wrong!")
```
\>\>\> `'Equal!'`  

<br>

### 삼항연산자 적용

```python
print("Equal!") if num == 3 else print("Wrong!")
```
\>\>\> `'Equal!'`  

**한줄로 작성되었다**

<br>

## Notice

삼항연산자를 사용시 튜플 형태를 사용하는 경우가 있다.  
이 경우 parentheses(괄호`()`)를 생략하지 않는 것이 좋다.  
괄호로 두개의 값을 묶어주지 않을 경우, `,` 뒤에있는 값만을 연산해버리는 경우가 발생하여 의도하지 않은 결과가 나타날 수 있다.  
  
> Python 에서 튜플(Tuple) 자료형은 `()`(명칭: parentheses)으로 묶어주는 형태를 사용한다. 하지만 parentheses를 생략하고 `,`만을 사용해도 튜플로 인식하는 특징이 있다.

<br>

## Practice

```python
num = 6

if num > 7:
    print("First")

elif num > 5:
    print("Second")

else:
    print("Third")
```
\>\>\> `'Second'`  

<br>

### 삼항연산자 적용

```python
print("First") if num > 7 else print("Second") if num > 5 else print("Third")
```
\>\>\> `'Second'`  

조건이 여러개일 경우에 사용하면 **오히려 가독성이 떨어질 수 있다.**

따라서 조건이 많지않고, 라인을 줄이고 싶은 경우에 사용하는 것을 권장한다.

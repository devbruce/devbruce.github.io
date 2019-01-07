---
title: "[Python] getattr / hasattr / setattr"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - getattr
  - hasattr
  - setattr

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Define Example Class

```python
# Define Class
class ExampleClass:
    def __init__(self):
        self.tmp = 'Get Attribute!'

a = ExampleClass()  # Make Instance
```

<br>

# getattr

형태 : `getattr(object, name[, default])`

- name : String

- `getattr(some, 'attr')` == `some.attr`

- 해당 attribute(속성) 가 없을 경우, **AttributeError** 반환

- **default 값을 지정하면 해당속성이 없을 경우 에러가 아닌, 반환받을 값을 지정할 수 있다.**

<br>

## Example

```
getattr(a, 'tmp')
```
\>\>\> `'Get Attribute!'`

```
getattr(a, 'new', 'Not Exist')
```
\>\>\> `'Not Exist'`

<br><br>

# hasattr

형태 : `hasattr(object, name)`

- name : String

- name 에 해당하는 문자열이 object 의 속성에 해당하면 True,  
아니면 False 반환

<br>

## Example

```
hasattr(a, 'tmp')
```
\>\>\> `True`

```
hasattr(a, 'new')
```
\>\>\> `False`

<br><br>

# setattr

형태 : `setattr(object, name, value)`

- name : String

- object 의 name 에 해당하는 속성을 value 값으로 할당

- `setattr(some, 'attr', "7")` == `some.attr = 7`

<br>

## Example

```python
setattr(a, 'tmp', 'Set New Value")
print(a.tmp)
```
\>\>\> `'Set New Value'`

<br><br>

# Reference

- [Python Documentation - 내장 함수](https://docs.python.org/ko/3.6/library/functions.html)

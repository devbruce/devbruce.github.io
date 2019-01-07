---
title: "[Python] os.path"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - django
  - os
  - path
  - abspath
  - dirname
  - join
  - basename
  - getcwd

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)
![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)

# os.path

Python 에서 path 관련 함수를 사용하려면 import 가 필요하다.

```python
import os
```

path관련 메서드로 경로를 선언하는 이유는  
OS 에 따라서  경로표시의 Syntax가 다르기 때문이다.  
호환성을 높이기 위해서는 이러한 메서드를 사용하는 것을 권장한다.  

- mac, linux : `/`

- window : `\`

<br>

아래의 예시는 `'/Users/Bruce/Desktop/path_test/path.py'` 에서 진행했다고 가정한다.

<br>

## os.path.abspath(path)

**절대경로 반환**

```python
import os
os.path.abspath(__file__)
```
\>\>\> `'/Users/Bruce/Desktop/path_test/path.py'`  

<br>

- `__file__` 은 Python 의 예약어로, 실행되는 스크립트 파일명을 나타낸다.

- `abspath` 의 argument 는 해당경로에 실제로 파일이 존재해야만 하는것은 아니다. 임의의 문자열을 넣어도 해당 경로를 반환해준다.

```python
import os
os.path.abspath('tmp_string')
```
\>\>\> `'/Users/Bruce/Desktop/path_test/tmp_string'`  

```python
import os
os.path.abspath('../some')
```
\>\>\> `'/Users/Bruce/Desktop/some'`

<br>

## os.path.dirname(path)

**path의 디렉토리(경로) 반환 (상위)**

```python
import os
os.path.dirname('/Users/Bruce/Desktop/path_test/tmp_string')
```
\>\>\> `'/Users/Bruce/Desktop/path_test'`

```python
import os
os.path.dirname('/Users/Bruce/Desktop/path_test')
```
\>\>\> `'/Users/Bruce/Desktop'`

<br>

## os.path.join(path, path)

**경로 합치기 (하위)**

```python
import os
os.path.join('/Users/Bruce/Desktop', 'join/test')
```
\>\>\> `'/Users/Bruce/Desktop/join/test'`

<br>

## os.path.basename(path)

**경로의 끝에 해당하는 값 반환**

```
import os
os.path.basename('/Users/Bruce/Desktop/path_test/path.py')
```
\>\>\> `'path.py'`

```
import os
os.path.basename('/Users/Bruce/Desktop/path_test')
```
\>\>\> `'path_test'`

```
import os
os.path.basename('/Users/Bruce/Desktop/path_test/')
```
\>\>\> `''`

<br>

## os.getcwd()

**현재 실행경로 반환**

```python
import os
os.getcwd()
```
\>\>\> `'/Users/Bruce/Desktop/path_test'`  

현재 실행경로를 반환하는 것이기 때문에 `os.getcwd()` 가 쓰여진 파일을 직접 실행했을 경우에는 `os.path.dirname(os.path.abspath(__file__))` 를 통해 경로를 구한것과 차이가 없어보인다.  

하지만, 해당 스크립트를 import 해서 사용할 경우에는 값이 달라진다.  
실행경로를 반환하기 때문에  
`os.getcwd()` 코드가 위치하는 파일이 아닌, import한 스크립트를 기준으로  경로가 반환되는 점을 주의해야한다.

<br><br>

# Django 의 settings.py 를 살펴보자.

`'/Users/Bruce/Desktop/path_test/'` 경로에서  
startproject 를 하게되었을 때의 디렉토리 구조는 아래와 같다.

```
# /Users/Bruce/Desktop/path_test/mysite

mysite
├── manage.py
└── mysite
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

설정패키지 폴더에 해당하는 mysite 내부의 settings.py 를 살펴보면 `BASE_DIR`은 아래와 같이 할당되어있다.

```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.apspath(__file__)))
```

os.path 메서드를 토대로 위의 코드를 해석해보자.

<br>

```python
os.path.abspath(__file__)
```
\>\>\> `'/Users/Bruce/Desktop/path_test/mysite/mysite/settings.py'`

```python
os.path.dirname(os.path.apspath(__file__))
```
\>\>\> `'/Users/Bruce/Desktop/path_test/mysite/mysite'`

```python
os.path.dirname(os.path.dirname(os.path.apspath(__file__)))
```
\>\>\> `'/Users/Bruce/Desktop/path_test/mysite'`

<br>

순서대로 접근해본 결과, `BASE_DIR` 은 django 의 최상위 폴더가 됨을 알 수 있다.

<br><br>

# Reference

- [Python Documentation - os.path](https://docs.python.org/3/library/os.path.html)

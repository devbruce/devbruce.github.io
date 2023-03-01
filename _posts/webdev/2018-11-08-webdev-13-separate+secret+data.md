---
title: "[Django] Separate Secret Data"
excerpt: 
last_modified_at: 2019-02-05

categories:
  - webdev

tags:
  - django
  - python
  - config
  - secret
  - json
  - path

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Secret Data

Django Project 를 공개적으로 배포할 때 노출되어서는 안되는 secret 값들이 존재한다.  
가장 쉬운예로 project 를 시작한 뒤 `settings.py` 를 살펴보면 `SECRET_KEY` 값을 확인할 수 있을 것이다.  
(주석에서도 노출에 주의를 주고있다.)  
이러한 secret 값을 따로 분리해서 관리하는 방법을 아래에 작성하였다.

<br><br>

# json File

**json (JavaScript Object Notation)**  

<br>

secret 값들을 관리하는 방법으로 json 파일을 활용할 수 있다.  
Python 에서는 json 파일을 다루기 위해선 json 라이브러리를 불러와주기만 하면 된다.  

```python
import json
```

json 파일의 형식은 Python 의 Dictionary 형태와 매우 유사하다.  
하지만, json 파일로 인식되기 위해서는 주의할 점이 존재한다.

<br>

## Notice

- attribute(속성)과 value(값)을 감싸는 것은 `"` (Quotation mark / 큰따음표) 이다.  
`'` (Apostrophe / 작은따음표) 가 되어서는 안된다.

- 마지막 attribute(속성), value(값) 쌍 뒤에는 `,` (Comma)가 와서는 안된다.

<br>

## Example of json Form

```json
{
  "A": "Apple",
  "B": "Banana"
}
```

<br>

## Load json

Python 에서 json 파일을 로드하기 위해서는 아래의 두가지 메서드가 사용된다.  

- `json.load()` : For File

- `json.loads()` : For String

만약 `json.loads()` 의 인수로 File Path 를 활용하고 싶다면 `open()` 함수와 `.read()` 메서드를 활용할 수 있다.

```python
json_data = json.loads(open('./tmp/data.json').read())
```

<br>

### Example

```python
json_data = json.load(open('./tmp/data.json'))
```

또는 `with` 를 활용할 수도 있다.

```python
with open('./tmp/data.json') as f:
    json_data = json.load(f)
```

<br><br>

# Separate Secret Data

이제 json 을 활용해서 secret 값들을 분리해보자.  

## Make Files

> 아래에 사용될 디렉토리명 및 파일명(`.secrets`, `secret_data.json` . . .)은 임의로 변경해도 문제없다.  
> 변경할 시 path 설정을 포함하여 참조하는 다른 부분에서도 그에맞게 수정해야한다.

- 우선, Django Project 의 ROOT 디렉토리에 `.secrets` 파일을 만든다.  
디렉토리 이름앞에 `.` 을 붙임으로써 숨김파일(invisible)로 지정한다.(권장사항)    

- `.secrets` 폴더 내부에 json 파일을 생성한다. 여기서는 `secret_data.json` 라고 지정하겠다.

`.secrets/secret_data` 의 형태

```json
{
  "SECRET_KEY": "<SECRET_KEY>"
}
```

`<SECRET_KEY>` 에 `settings.py` 에 있는 `SECRET_KEY` 값을 입력한다.  
(`settings.py` 에 `SECRET_KEY` 값은 당연히 지워주어야 한다. json 파일로 이동시킨 값을 불러오게 될 것이다.)

<br>

## Set Path

`settings.py` 에 위에서 만든 json 파일을 불러올 수 있도록 path 를 설정한다.

```python
# settings.py

ROOT_DIR = os.path.dirname(BASE_DIR)
SECRETS_DIR = os.path.join(ROOT_DIR, '.secrets')

secrets = json.load(open(os.path.join(SECRETS_DIR, 'secret_data.json')))
```

`secrets` 라는 변수에 로드한 json 파일을 할당한 것을 알 수 있다.  
이제 마지막으로 `SECRET_KEY` 를 json 파일에서 불러오자.

<br>

## Load Secret Data

```python
# settings.py

ROOT_DIR = os.path.dirname(BASE_DIR)
SECRETS_DIR = os.path.join(ROOT_DIR, '.secrets')

secrets = json.load(open(os.path.join(SECRETS_DIR, 'secret_data.json')))

SECRET_KEY = secrets['SECRET_KEY']  # secret_data.json 에서 SECRET_KEY 값을 불러온다.
```

json 데이터가 할당된 `secrets` 변수에서  
dictionary 의 key 값을 통해 value 를 호출하는 것과 같은 방법으로 `SECRET_KEY` 를 호출한 것을 알 수 있다.  

이렇게 Secret Data 를 json 파일로 분리한 뒤 불러오는 것까지 진행하였다.  
하지만 `SECRET_KEY` 값이 작성된 json 파일까지 공개적으로 업로드가 된다면 의미가 없어진다.  
따라서, `.gitignore` 를 작성하여 마무리를 해보자.  

<br><br>

# gitignore

`.gitignore` 작성법에 따라 `.secrets` 디렉토리를 업로드가 되지 않도록  
아래의 값을 `.gitignore` 파일에 추가한다.

```
# Secrets
/.secrets
```

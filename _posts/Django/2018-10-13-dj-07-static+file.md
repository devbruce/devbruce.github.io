---
title: "[Django] Static File"
excerpt: 
last_modified_at: 2019-01-07

categories:
  - Django

tags:
  - django
  - python
  - static
  - config
  - prefix

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Static Files in Django

- Web Server : 정적 자원 처리 담당

- Web Application : 동적 자원처리 담당 (ex. Django)

Web Application Server 에 해당하는 Django 는 Static File(정적 파일) 을 직접 Serving 하지 않는다.(가능하지만 비효율적이기 때문이다.)  
하지만 개발단계에서는 정적파일을 전달받아 결과를 확인하며 작업하는 것이 필요하다.  
이러한 문제를 해결하기 위해서 Django 에서는 기본적으로 `django.contrib.staticfiles` 앱이 설치되어있다.  
(`settings.py` 의 `INSTALLED_APPS` 리스트를 확인해보자.)  

이를통해, Django 에서는 static (정적) 파일을 기본적으로 App 디렉토리 하위의 static 폴더를 검색하여 찾는다.  
또한 `settings.py` 에 `STATICFILES_DIRS` 를 통해 검색 범위를 확장할 수 있다.  
이곳에 추가된 경로는 특정 App에 종속되지 않으며 주로 여러 App의 공통적인 정적파일을 담당한다.  
`STATICFILES_DIRS`를 포함한 정적파일에 관련한 설정들을 아래에 정리하였다.

<br><br>

## STATICFILES\_DIRS

**Example**

```python
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static_example'),
)
```

- **개발단계**에서 사용 (`DEBUG=True`)

- 정적파일들이 위치하는 경로묶음 (`List` or `Tuple`)

- Django 가 직접 접근

<br>

### Prefix

```python
STATICFILES_DIRS = (
    ('Prefix_Example', os.path.join(BASE_DIR, 'static_example')),
)
```

위의 예시와 같이 경로를 다시 `Tuple` 로 묶어서 첫번째 요소로 문자열을 주면 해당 경로에 **Prefix**를 줄 수 있다.  
Prefix 는 `collectstatic` 명령어로 정적파일을 모을 때 `STATIC_ROOT` 하위에  
prefix 의 이름으로 하위폴더를 만들어서 해당 정적파일을 모으게 된다.
> URL 에도 prefix 가 붙는다.

<br>

### Notice

Case. 경로의 갯수가 1개일 경우에도  

- `List`, `Tuple` 형태로 설정

- 경로의 끝에 `,` (Comma) 를 잊지말 것

<br><br>

## STATIC\_ROOT

**Example**

```python
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

- **서비스 단계**에서 사용 (`DEBUG=False`)

- 문자열로 할당 (`List`, `Tuple` 형태가 아니다.) → 유일한 1개이다.

- 웹 서버에서 접근 (Django 가 접근하지는 않는다.)

- collectstatic 명령어를 통해 정적파일들이 모일 경로

<br>

### collectstatic

```bash
$ python manage.py collectstatic
```

위의 명령어는 App 디렉토리 하위의 `static` 디렉토리, `STATICFILES_DIRS` 에 지정된 경로의 모든 정적파일을  
`STATIC_ROOT` 경로 한 곳에 모아주는 명령어이다.
  
#### Notice 

`STATICFILES_DIRS` 의 경로 중에서 prefix 가 지정된 경로가 있을시,  
`STATIC_ROOT` 하위에 prefix 이름의 디렉토리 생성 후 그 디렉토리 안에 prefix 가 지정된 경로의 정적파일을 복사한다.

<br>

### findstatic

```bash
$ python manage.py findstatic <FileName or DirectoryName>
```

위의 명령어를 통해 찾고하자는 정적파일의 경로를 알 수 있다.  
만약 검색애서 찾지 못할 경우, 해당 정적파일이 `STATICFILES_DIRS` 또는 App 디렉토리 하위의 static 디렉토리에 해당하지 않기 때문이다.

<br>

### Notice

`STATIC_ROOT` 는 `STATICFILES_DIRS` 에 지정된 경로와 동일한 경로가 되어서는 안된다.

<br><br>

## STATIC\_URL

**Example**

```python
STATIC_URL = '/static/'
```

- 정적파일의 최상위 URL

- 문자열 끝은 `/` 로 끝나야한다.

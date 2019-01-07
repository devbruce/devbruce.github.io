---
title: "[Django] Media File"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - media
  - static
  - image
  - Pillow
  - mimetypes

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)
  
# Media File

- 유저가 업로드하는 정적파일(Static File)
> Static File 관련 참고 : [[Django] Static File](https://devbruce.github.io/django/dj-07-static+file/)

- Django 의 관련 모델필드 : `FileField`, `ImageField`
  - `ImageField`의 경우 Pillow 라이브러리 필요

- 업로드된 파일은 기본적으로 `settings.MEDIA_ROOT` 경로에 저장된다.

<br><br>

## Pillow (Python Library)

**Install Command**

```bash
$ pip install Pillow
```

- Django 의 `ImageField` 를 사용하기 위해 필요한 Python 이미지 처리 라이브러리

- 기존 Python 이미지 처리 라이브러리인 PIL (Python Image Library) 프로젝트가 정지되며 PIL을 Fork 하여 진행중인 프로젝트

- PIL 과 API 가 유사하다.

<br><br>

## Model Field

### FileField

- 파일 업로드 필드

- `primary_key` 속성이 지원되지 않는다.

- `upload_to` 속성으로 업로드된 파일의 저장 경로를 `MEDIA_ROOT` 하위에 지정할 수 있다.
  - `MEDIA_URL` 에도 prefix 처럼 붙게된다.

- DB의 필드에는 `settings.MEDIA_ROOT` 의 하위 경로를 String 으로 저장


<br>

### ImageField

- `FileField` 를 상속받은 필드

- 업로드된 파일이 이미지인지 유효성 검사를 한다.

<br><br>

## Template

- Form 태그의 속성으로 `method:"POST" enctype:"multipart/form-data"` 지정필요

```html
<form action="" method="POST" enctype="multipart/form-data">
```

- template 에서 `MEDIA_URL` 을 불러오기 위해서는, view 에서 전달받은 객체 중 FileField 에 해당하는 속성에 `.url` 을 붙여주면 된다.

```html
{% raw %}<img src="{{ TmpUser.photo.url }}">{% endraw %}
```
tmp 의 photo 속성의 `MEDIA_URL` 을 반환받는다.  
이를 통해서 바로 img 를 html 에 반환할 수 있다.

<br><br>

## settings.py

### MEDIA\_ROOT

- 유저가 업로드한 파일(Media File)이 저장될 경로

- `settings.py` 에서 변수 `MEDIA_ROOT` 에 새로운 경로를 할당함으로써 설정 가능하다.

**Example**

```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

- 설정된 `MEDIA_ROOT` 를 다른 Python Script 에서 불러오고 싶은 경우, 아래와 같이 할 수 있다.

```python
from django.conf import settings

media_path = settings.MEDIA_ROOT  # settings.py 의 MEDIA_ROOT 를 media_path 라는 변수에 할당
```

<br>

### MEDIA\_URL

- 유저가 업로드한 파일(Media File)의 기본 URL

- 끝에 `/` 를 붙여준다.


**Example**

```python
MEDIA_URL = '/media/'
```

<br>

## Serving Media File During Development

Media File 은 Static File 과는 다르게 Django 의 개발단계(`DEBUG=True`)에서 기본으로 serving 해주지 않는다. 따라서 개발단계에서 Media File을 serving 받기 위해서는 추가적인 설정을 해줄 필요가 있다.  

settings.py 를 아래와 같이 설정한 것으로 가정하겠다.

**settings.py**

```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'
```

<br>

### static Method

**urls.py**  

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    . . .
]

urlpatterns += static(
    prefix=settings.MEDIA_URL,
    document_root=settings.MEDIA_ROOT,
)
```

Django 에서 제공하는 `static` 메서드로 Media File 을 개발단계에서도 serving 이 되도록 설정한 것이다. `static` 메서드는 `DEBUG=True` 일 때만 유효하다. `DEBUG=False` 가 되면 빈 리스트를 반환한다.  

<br>

### Make Oneself (Not Using static Method)

`static` 메서드를 사용하지 않는다면 아래와 같은 작업을 진행해야한다.(권장 X)  

**urls.py**  

```python
from django.urls import path
from . import views

urlpatterns = [
    path('media/<path:path>/', views.media_serve, name='media_serve'),
]
```
`settings.py` 에서 `MEDIA_URL` 을 `'/media/'` 로 설정했기 때문에 urlpattern 에서 media 로 시작하는 경로(`MEDIA_URL`)는 `media_serve` 뷰 함수를 반환하도록 설정하였다.

<br>

**views.py**  

```python
from django.http import FileResponse
from django.conf import settings
import os, mimetypes

def media_serve(request, path):
    file_path = os.path.join(settings.MEDIA_ROOT, path)
    return FileResponse(open(file_path, 'rb'), content_type=mimetypes.guess_type(file_path)[0])
```

`media_serve` 뷰 함수는 `media/<path:path>/` 의 path 라고 네이밍된 경로를 반환받은 뒤, `MEDIA_ROOT` 와 합쳐서 `file_path` 로 할당한다. 이후 `FileResponse` 를 통해 해당 파일을 반환한다. 이 때, `content_type` 은 `mimetypes.guess_type` 메서드를 통해 파일의 타입을 자동으로 반환받은 값을 전달한다.  

- `mimetypes.guess_type` 은 요소가 두개인 Tuple 을 반환한다. `content_type` 에 구체적인 값을 명시하기 위해 인덱스가 0인 값을 지정해주었다(`[0]`). 인덱스를 지정안하고, 튜플을 전달해도 문제없이 작동하긴 한다. 브라우저의 개발자 도구에서 `content_type` 을 확인해보았을 때, 인덱스를 지정해서 전달한 값과 튜플로 전달한 값 각각 그대로 전달받은 것을 알 수 있다. 둘 다 문제없이 인식하는 것을 보았을 때 브라우저에서 두가지 값을 모두 자동으로 인식해주는 것 같다.

> `<path:path>` 에서 왼쪽의 path 는 Path converter 중 하나를 의미한다. (일종의 Type)  
오른쪽의 path 는 view 함수의 path 인수로 전달할 값(경로)에 해당한다.  
관련 참조 : [Django Documentation - URL dispatcher: Path converters](https://docs.djangoproject.com/en/2.1/topics/http/urls/#path-converters)

<br><br>

## Reference

- [Django Documentation - Settings](https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-MEDIA_ROOT)

- [Django Documentation - Model field](https://docs.djangoproject.com/ko/2.1/ref/models/fields/)

- [Django Documentation - Managing static files](https://docs.djangoproject.com/en/2.1/howto/static-files/)

- [Django Documentation - URL dispatcher](https://docs.djangoproject.com/en/2.1/topics/http/urls/)

---
title: "[Django] URL resolve / reverse"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - url resolve
  - url reverse

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# URL resolve

URL (Uniform Resource Locator)

```
# 1. 클라이언트가 서버에게 url 전송
Client (Browser): request(url) --> Server(Django)

# 2. 서버의 URL resolver 가 요청받은 url 을 resolve
Server(Django)[URL resolver]: resolve request(url)

# 3. resolved url 과 매칭된 view 함수 호출
urls.py --> 매칭되는 views 함수 호출
```

<br>

## Example

```
mysite
├── config
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── views.py
│   └── wsgi.py
└── manage.py
```

```python
# mysite/config/views.py

from django.http import HttpResponse

def tmp_view(request):
    return HttpResponse("Succeed")
```

```python
# mysite/config/urls.py

from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('tmp/', views.tmp_view, name="tmp_name"),
]
```

위와 같은 상황을 가정하겠다. (나머지 값들은 Default 상태이다.)  
이제 터미널에서 서버를 띄우고, 아래의 과정을 시도해보자.

```
$ python manage.py runserver
```

<br>


## Phase.1

```
# 1. 클라이언트가 서버에게 url 전송
Client (Browser): request(url) --> Server(Django)
```   
\>\>\> url: `localhost:8000/tmp` 접속하여 Server(Django)에게 url에 해당하는 정보를 요청한다.

<br>

## Phase.2

```
# 2. 서버의 URL resolver 가 요청받은 url 을 resolve
Server(Django)[URL resolver]: resolve request(url)
```
\>\>\> 요청을 받은 Server(Django)가 `urls.py` 에서 `localhost:8000/tmp` 와 일치하는 url 패턴을 검색한다.  

```python
# mysite/config/urls.py

from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('tmp/', views.tmp_view, name="tmp_name"), # <-- 일치
]
```
`path('tmp/', views.tmp_view, name="tmp_name")` 와 일치함을 인식

<br>

## Phase.3

```
# 3. resolved url 과 매칭된 view 함수 호출
urls.py --> 매칭되는 views 함수 호출
```

`'tmp/'` 와 일치하므로, Server(Django)는 `views.tmp_view` 를 호출한다.  

<br>

`views.tmp_view` 를 살펴보자.

```python
# mysite/config/views.py

from django.http import HttpResponse

def tmp_view(request):
    return HttpResponse("Succeed")
```

`views.tmp_view` 는 `HttpResponse("Succeed")`를 반환한다.  
  
**최종결과**      
  
`127.0.0.1:8000/tmp/` 로 접속하면  
`"Succeed"` 라는 문자가 쓰여진 페이지가 반환된다.

<br><br>

# URL reverse

URL reverse 는 URL resolve 와 반대되는 개념이다.  
resolve 가 요청받은 url의 패턴과 일치하는 view를 찾아 호출하였다면,  
reverse 는 view 의 이름을 통해 해당 url을 찾는 것이다.  
  
**Example**  
  
- `reverse('ViewName')` 메서드

- `{% raw %}{% url 'ViewName' %}{% endraw %}` in Template Files


<br>

## reverse('ViewName')

`reverse('ViewName')` 메서드를 사용하기 위해서는 import가 필요하다.

```python
from django.urls import reverse
```

URL resolve 에서의 케이스를 그대로 가져왔을 때,  

```python
reverse('tmp_name')
```

의 결과를 예상한다면, `/tmp/` 가 나와야 할 것이다.  
예상과 결과가 같을지 확인해보자.  
  
결과를 간단하게 확인하기 위해서 Django Shell 을 활용해보자.  

```
$ python manage.py shell
```

Shell 에 들어가서 아래와 같이 코드를 입력한다.

```python
from django.urls import reverse

reverse('tmp_name')
```
\>\>\> `'/tmp/'`

예상했던 결과 그대로 출력됨을 알 수 있다.  

<br>

## {% raw %}{% url 'ViewName' %}{% endraw %}

Template 파일에서 URL reverse 를 통해 동적인 `href` (HyperText Reference) 속성을 줄 수 있다.

```html
{% raw %}<a href="{% url 'tmp_name' %}">Link</a>{% endraw %}
```

`<a>` (Anchor) 태그에 `href` 값을 위와 같이 주게될 경우,  
`tmp_name` 이라는 View 이름으로 URL reverse 과정을 거쳐서  `'/tmp/'` 이라는 url과 매칭된다.  
그 결과 Link 라는 텍스트는 `localhost:8000/tmp/` URL로 연결이 되고, 해당 URL 에 해당하는 `tmp_view` 라는 view 함수를 호출하고 결과를 반환하게 된다.

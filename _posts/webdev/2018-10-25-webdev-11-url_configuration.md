---
title: "[Django] URL Configuration"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - django
  - python
  - url
  - config

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# URL Configuration

Django 에서 기본적인 url 설정은 urls.py 에서 가능하다.  
기본적인 형태는 아래와 같다.

```python
from django.urls import path
from . import views

urlpatterns = [
    path('your_url/', views.view_func, name='view-name'),
]
```

- url pattern 의 **끝은 항상 항상 `/`(Slash) 로 끝나야 한다.**  
`/`(Slash) 로 끝나지 않을 경우 해당 URL 은 매칭이 되지 않는다.

- url pattern 의 시작은 `/` (Slash) 를 붙일 필요 없다.  
모든 URL 은 `/`(Slash) 끝나기 때문이다.

- url pattern 에서 일정 부분에 grouping을 할 경우, 해당 값은 해당 view 함수에게 위치인수로 전달가능하다.
  - `path` 의 경우 naming 이 필수이기 때문에 위치인수로 전달이 불가능하다.  

- url pattern 에서 일정 부분에 grouping 후, naming 까지 할 경우 view 함수에 해당 값을 키워드 인수로 전달 가능하다.  

<br>

## path

Django 의 url 설정을 담당하는 메서드 중 하나 (버전 2.0 부터 제공된 메서드)  
이전 버전에서는 정규표현식을 활용했었으나, `path` 메서드를 통해 더 직관적으로 url 을 작성할 수 있게 되었다.  

- 첫번째 위치 인수 `route`: 설정할 url pattern  
- 두번째 위치 인수 `view`: 해당 url 에서 실행될 view 함수  
- 키워드 인수 `name`: 실행될 view 함수의 이름 (URL reverse 를 통해 접근가능)  

- `<path coverter:name>` 을 통해 grouping 과 naming이 가능하다.
  - naming 이 필수가 되기 때문에 view 함수에 키워드인수로만 전달가능해진다.

<br>

### Path coverter

- `str` : `/` 를 제외한 모든 비어있지 않은 문자열과 매치 **(Default)**

- `path` : `/` 를 포함한 모든 비어있지 않는 문자열과 매치

- `int` : 0 이상의 양수와 매치 (`int` 로 반환)

- `slug` : ASCII 문자, 숫자, hypen(`-`), underscore(`_`) 로 구성된 모든 slug 문자열과 매치

- `uuid` : 형식화된(포맷되어진) UUID 와 매치

> Path converter : [Django Documentation - URL dispatcher: pathconverter](https://docs.djangoproject.com/en/2.1/topics/http/urls/#path-converters)

<br>

### Example

```python
from django.urls import path
from . import views

urlpatterns = [
    path('your_url/<int:user_id>/', views.show_id, name='show-id'),
]
```

위의 예시는 `your_url/1/` 이라는 url 요청이 올 경우 `user_id=1` 이라는 키워드인수를 `show_id` 라는 view 함수에 전달하여 값을 반환하게 된다. 이 때 `show_id` view 함수는 인자(parameter)를 request 뿐만 아니라 전달받을 수 있는 `user_id` 인자를 설정해두어야 한다.  

<br>

**View Function 예시**

```python
from django.http import HttpResponse

def show_id(request, user_id):
    return HttpResponse(f'ID Number: {user_id}')
```

<br>

## re\_path

- `path` 메서드와 유사  
- Django 2.0 이전 버전의 `url` 메서드와 동일하게 정규표현식을 통해 url pattern 을 작성할 수 있다.  
- grouping 후 naming 이 필수가 아니므로, grouping만 해서 view 함수에 **위치인수로 전달 가능**하다.

<br>

### Example

```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path(r'^your_url/(?P<user_id>\w+)/$', views.show_id, name='show-id'),
]
```
위의 예시는 `path` 함수의 예시와 기능상 동일하다.

<br>

## Pass arguments to view function by url tag in template

**urls.py**

```python
from django.urls import path
from . import views

urlpatterns = [
    path('<int:arg1><int:arg2>/', views.show_id, name='show-id'),
]
```

**views.py**

```python
from django.http import HttpResponse
from django.shortcuts import render


def show_id(request, arg1, arg2):
    return HttpResponse(f'arg1: {arg1}, arg2: {arg2}')


def index(request):
    return render(request, 'index.html')
```

위와같은 상황을 가정한다. (template 설정 및 작성은 생략)  
이 때, Template 의 url tag 에서 view 함수에 특정 값을 전달하고 싶은 경우 아래와 같이 작성한다.  

참고로, template 에서 view 함수에 인수를 전달한다는 것의 의미를 구체적으로 말한다면 특정값을 url 의 grouping된 값에 전달하는 것이다. 즉 template 의 url tag 에서 직접 view 함수에게 값을 전달할 수는 없기때문에 URL 을 매개체로 전달하는 것이다.

<br>

### 키워드인수 전달

```html
{% raw %}<a href="{% url 'show-id' arg1=1 arg2=2 %}">Link</a>{% endraw %}
```

- `=` 앞 뒤에 띄워쓰기가 있어서는 안된다.

<br>

### 위치인수 전달

```html
{% raw %}<a href="{% url 'show-id' 1 2 %}">Link</a>{% endraw %}
```

이렇게 template 을 작성하고, 해당 URL로 연결되는 Link 를 클릭하게 되면 해당 값이 전달된 URL과 그 URL 에 해당하는 view 함수가 호출되는 것을 확인할 수 있다.  

<br>

### Result

- URL: `localhost:8000/12/`  
- 출력값: `arg1: 1, arg2: 2`  

<br>

## include

- urlpattern 에 다른 URLconf module 을 추가할 때 사용하는 함수이다.  
- path 의 두번째 위치인자 `view` 에 위치한다.  
- name 을 지정할 필요없다. (추가된 URLconf module 에서 view 이름을 지정해주기 때문)
- import 를 해주어야한다.


<br>

### Example

```python
from django.urls import path, include
from . import views

urlpatterns = [
    path('your_url/', include('second.urls')),
]
```
위와 같이 `second.urls` 을 include 를 하게 되면 second 앱의 URLconf module 이 해당 url 에 추가되는 형식이다. 이에따라 second 앱의 urls.py 에서는 `'new/'` 로 url을 설정하면 `'your_url/new/'` 의 형태가 된다.

<br>

## app\_name

url 에 namespace 를 지정하는 방법 중 하나이다.  
> 또 다른 방법은 `include` 를 활용한다.  
> 궁금하다면 아래의 링크를 참고하자.  
> [Django Documentation - URL dispatcher: URL namespaces and included URLconfs](https://docs.djangoproject.com/en/2.1/topics/http/urls/#url-namespaces-and-included-urlconfs)

`name` 을 통해 view 의 이름을 지정해주어도 다른 app 의 urls.py 의 `name` 과 동일한 이름을 사용할 경우 문제가 발생할 수 있다. namespace 를 명시하면 이러한 `name` **중복문제를 방지**할 수 있다.  

<br>

### Example

```python
from django.urls import path
from . import views

app_name = 'example'

urlpatterns = [
    path('your_url/<int:user_id>/', views.show_id, name='show-id'),
]
```

`app_name = 'example'` 을 통해 해당 app 의 url namespace 를 'example' 로 지정하였다. 이제 위에 정의된 `show-id` 를 URL reverse 를 통해 호출할 경우 namespace 를 아래와 같이 **항상 명시**해주어야 한다.  

<br>

**URL Reverse by url tag in template**

```html
{% raw %}<a href="{% url 'example:show-id' %}">Link</a>{% endraw %}
```

<br>

**URL Reverse by redirect**

```python
from django.shortcut import redirect

def go_show_id(request):
    return redirect('example:show-id')
```

URL Reverse 관련 참고 : [[Django] URL resolve / reverse](https://devbruce.github.io/django/dj-05-url_resolve,reverse/)

<br><br>

# Reference

- [Django Documentation - URL dispatcher](https://docs.djangoproject.com/en/2.1/topics/http/urls/)

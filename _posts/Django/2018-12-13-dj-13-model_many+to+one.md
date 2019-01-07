---
title: "[Django] Model - Many To One"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Django

tags:
  - django
  - python
  - model
  - many-to-one
  - foreignkey
  - ORM

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Many To One

- 다대일 관계 (M : 1)

- One To Many (일대다, 1 : M) 관계로도 불린다.

- Django 에서는 해당 관계를 정의하기 위해 `django.db.models.ForeignKey` 를 사용

<br>

이 포스트에서는 예시를 책(Book)과 책의 저자(Author)를 예시로 사용한다.  
> 저자(Author)가 A, B, C 세가지 책을 집필한 것으로 가정

```
[One]       [Many]  
(Author)    (Book)

Bruce  ──────── A
            ├── B
            └── C
```

<br>

## Django Model Code

### Author (One)

- 저자(Author)는 Many to One 에서 One 에 해당한다. 

- 책(Book)에게 있어서 책의 저자(Author)는 **단 한개**만 관계를 가질 수 있다.  

<br>

**저자(Author) 모델 구현코드**

```python
from django.db import models


class Author(models.Model):
    name = models.CharField(max_length=20)
    
    def __str__(self):
        return self.name
```

<br>

### Book (Many)

- 책(Book)은 Many To One 에서 Many 에 해당한다. 

- 책의 저자(Author)는 여러가지 책을 집필한다.  
그리고 저자(Author)는 자신이 집필한 **여러개의 책들과 관계**를 가진다.  

<br>

**책(Book) 모델 구현코드**

```python
from django.db import models


class Book(models.Model):
    name = models.CharField(max_length=20)
    author = models.ForeignKey(Author, related_name='books', on_delete=models.CASCADE)
    
    def __str__(self):
        return self.name
```

- `related_name=books` : author 인스턴스에서 자신을 참조하고 있는 book 인스턴스를 참조할 때(역참조)의 이름을 정의하였다.  
따로 지정하지 않을 경우 `related_name` 은 기본값으로 (`클래스이름의 소문자` + `_` + `set`)이다.  
즉 `related_name` 을 지정하지 않았다면 `book_set` 이 기본값이다.

- `on_delete=models.CASCADE` : 관계를 가지고 있는 author 객체가 삭제되면 해당 book 객체도 삭제된다.

<br>

### models.py

```python
from django.db import models


class Author(models.Model):
    name = models.CharField(max_length=20)
    
    def __str__(self):
        return self.name
        

class Book(models.Model):
    name = models.CharField(max_length=20)
    author = models.ForeignKey(Author, related_name='books', on_delete=models.CASCADE)
    
    def __str__(self):
        return self.name
```

<br><br>

## Practice

위의 구현된 모델을 통해 실제 인스턴스(객체)를 생성하고 확인해보자.  

> 실습을 위해 library 라는 이름을 가진 app을 생성하고 해당 앱의 models.py 에 구현코드를 작성한다.  
> 이후 `makemigrations` 와 `migrate` 를 하였음을 가정한다.

```python
from library.models import Author, Book

a1 = Author.objects.create(name="Bruce")  # 저자(Author) a1 생성

b1 = Book.objects.create(name='A', author=a1)  # author 가 a1인 책(Book) b1 생성
b2 = Book.objects.create(name='B', author=a1)  # author 가 a1인 책(Book) b2 생성
b3 = Book.objects.create(name='C', author=a1)  # author 가 a1인 책(Book) b3 생성
```

```python
a1
```
\>\>\> `<Author: Bruce>`

<br>

```python
b1
```
\>\>\> `<Book: A>`

<br>

```python
b2
```
\>\>\> `<Book: B>`

<br>

```python
b3
```
\>\>\> `<Book: C>`

<br>

```python
a1.books.all()  # related_name 을 books 로 지정하였으므로 이와같이 역참조가 가능하다.
```
\>\>\> `<QuerySet [<Book: A>, <Book: B>, <Book: C>]>`

<br><br>

### DataBase

> Table Name은 `AppName_ClassName` 소문자화로 만들어진다.

<br>

#### Author

Table Name : library\_author

| id | name  |
|----|-------|
| 1  | Bruce |

<br>

#### Book

Table Name : library\_book

| id | name | author_id |
|----|------|-----------|
| 1  | A    | 1         |
| 2  | B    | 1         |
| 3  | C    | 1         |
  
  
각 Book 인스턴스의 저자(author)는 library\_author 테이블의  
`id=1, name=Bruce` (a1) 를 참조하고 있음을 알 수 있다.  

즉, `author_id` 열은 `library_author` 테이블을 참조하는 ForeignKey

<br><br>

# Reference

- [Django Documentation - Models](https://docs.djangoproject.com/en/2.1/topics/db/models/)

---
title: "[Django] Basic Command"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - django
  - command

---

![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)

# Django Basic Command

## startproject

```bash
$ django-admin startproject <ProjectName>
```

<br>

## startapp

```bash
$ python manage.py startapp <AppName>
```

<br>

## runserver

```bash
$ python manage.py runserver
```

<br>

## Migration

### makemigrations

```bash
$ python manage.py makemigrations <AppName>
```

**migration 파일 생성**  

모델 변경사항을 migration 으로 저장  

> `<AppName>` 을 쓰지 않으면, 모든 App이 선택된다.

<br>

### migrate

```bash
$ python manage.py migrate <AppName>
```

`INSTALLED_APPS` (`settings.py`)를 탐색하여  
DB설정, migration에 따라 필요한 **테이블 생성**  

> `<AppName>` 을 쓰지 않으면, 모든 App이 선택된다.

<br>

#### migrate 되돌리기 (취소)

```bash
$ python manage.py migrate <AppName> <MigrationFileNumber>
```

migration file 번호를 지정하여, DB를 해당 migration 파일상태로 되돌릴 수 있다.  
처음으로 돌리고 싶은 경우, `zero` 를 사용한다.

```bash
$ python manage.py migrate <AppName> zero
```

<br>

### showmigrations

```bash
$ python manage.py showmigrations <AppName>
```

**migration 파일 적용여부 확인**

- `[X]` : 적용된 Migration 파일  
- `[ ]` : 적용되지 않고, 생성만 되어있는 Migration 파일

> `<AppName>` 을 쓰지 않으면, 모든 App 이 선택된다.

<br>

## SuperUser

### createsuperuser

```bash
$ python manage.py createsuperuser
```

**관리자계정 만들기**

> email 설정은 null값을 주어도 되므로 입력없이 Enter로 넘어갈 수 있다.

<br>

### changepassword

```bash
$ python manage.py changepassword <AccountName>
```

**관리자계정 비밀번호 재설정**

<br>

## shell

```bash
$ python manage.py shell
```
Django Shell 진입

<br>

### shell\_plus

```bash
$ python manage.py shell_plus
```

`django_extensions` 를 설치했을 경우 사용가능  

> Install `django_extensions`
> 
> ```
> $ pip install django_extensions
> ```

<br>

## Static File

### collectstatic

```bash
$ python manage.py collectstatic
```

**정적파일 `STATIC_ROOT` 에 모으기**

<br>

### findstatic

```bash
$ python manage.py findstatic <FileName or DirectoryName>
```

**정적파일 검색**  

Full Path 를 반환해준다.

<br><br>

# Reference

- [Django Documentation](https://docs.djangoproject.com/en/2.1/)

---
title: "[AWS] S3 - Connecting Django To S3 With Boto3 and Django Storages"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - AWS

tags:
  - AWS
  - S3
  - IAM
  - boto3
  - storage
  - django
  - python
  - django-storages

---

![AWS-S3](https://img.shields.io/badge/AWS-S3-green.svg)
![django-version-2.1.2](https://img.shields.io/badge/django-v2.1.2-brightgreen.svg)
![python-version-3.6.6](https://img.shields.io/badge/python-v3.6.6-blue.svg)

# Intro

앞으로 진행될 내용은 boto3 가 설치되어 있는 상태이며,  
AWS IAM 에서 `AmazonS3FullAccess` 권한을 가진 계정이 있어야 가능하다.  

**Boto3 설치 및 IAM 계정관련은 아래 글 참조**

- [[AWS] S3 - Using S3 With Boto3](https://devbruce.github.io/aws/aws-06-s3_boto3/)

<br>

# Django Storages

## Install

```bash
$ pip install django-storages
```

<br>

## Settings

Media File 과 Static File 의 기본 저장소를 settings.py 에 아래의 변수를 정의하는 것을 통해  
S3의 Bucket 으로 변경할 수 있다.  

- `DEFAULT_FILE_STORAGE` : Media File

- `STATICFILES_STORAGE` : Static File

<br>

아래의 예시는 Media File 의 저장소를 변경하는 예시이다. settings.py 에 아래 내용을 추가한다.

```python
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
AWS_ACCESS_KEY_ID = '<AWS_ACCESS_KEY_ID>'
AWS_SECRET_ACCESS_KEY = '<AWS_SECRET_ACCESS_KEY>'
AWS_STORAGE_BUCKET_NAME = '<AWS_STORAGE_BUCKET_NAME>'
AWS_S3_REGION_NAME = '<AWS_S3_REGION_NAME>'
```

> `DEFAULT_FILE_STORAGE` 와 `STATICFILES_STORAGE` 를 둘 다 정의한 뒤,  
> 다른 설정값을 지정하고 싶은 경우 Storage Class 재정의를 활용한다.  

<br>

### Necessary Variable

- `AWS_ACCESS_KEY_ID` : IAM 에서 생성한 `AmazonS3FullAccess` 권한을 지닌 계정의 **액세스 키 ID**

- `AWS_SECRET_ACCESS_KEY` : IAM 에서 생성한 `AmazonS3FullAccess` 권한을 지닌 계정의 **비밀 액세스 키**

- `AWS_STORAGE_BUCKET_NAME` : 위의 계정이 가지고 있는 S3 Bucket 중 연결할 Bucket 의 이름

- `AWS_S3_REGION_NAME` : 지정한 Bucket 의 Region (`'ap-northeast-2'` → 서울)  
지정하지 않으면 배포 후에 미디어 파일에 접근할 경우 `InvalidRequest` 가 나타나게 된다.

<br>

### Optional Variable

- `AWS_DEFAULT_ACL` : 해당 저장소의 권한설정 (`public-read`, `private` 등)

- `AWS_LOCATION` : 업로드 PATH 에 추가할 prefix

- . . .

<br>

### Storage 권한설정 주의 (Static File)

static file 의 경우 `private` 설정을 할 경우 파일을 로드하는 것에 문제가 발생할 수 있다.  
`private` 이 적용되면 static 파일의 URL 뒤에  
인증 패러미터가 같이 전송되어 해당 파일을 불러올 때 인증이 진행된다.  

이러한 **인증 패러미터는 URL이 동적으로 생성될 때에는 정상적으로 전송**되지만,  
상대경로로 static 파일을 불러오게 되는 경우에는 인증 패러미터가 전송되지 않아 인증이 거부된다.  
(CSS 파일 내부의 url 이 대표적인 예시)  

media 파일의 경우에는 문제가 발생하지 않지만 static 파일의 경우에는 문제가 발생할 수 있으므로  
각각 권한을 다르게 설정하는 것을 권장한다. (아래의 Storage Class 재정의에서 따로 설정 가능하다.)

<br><br>

## Storage Class 새로 만들기 (상속)

프로젝트 내부에 `.py` 파일을 새로 생성한다.  
아래 예시의 경우는 프로젝트의 설정패키지 내부에 `storages.py` 라는 이름으로 생성하였다.

> 프로젝트의 설정패키지는 settings.py 가 위치한 디렉토리를 의미한다.

```python
from storages.backends.s3boto3 import S3Boto3Storage


class TmpStorage(S3Boto3Storage):
    pass
```

현재 위의 `TmpStorage` 는 `S3Boto3Storage` 와 동일하다.  
상속만 받고 아무것도 하지 않았기 때문이다.  
이제 필요한 부분을 오버라이딩하여 커스터마이징을 해보자. 

<br>

```python
from storages.backends.s3boto3 import S3Boto3Storage


class TmpStorage(S3Boto3Storage):
    location = 'tmp'
    default_acl = 'private'
```

- `location` : 업로드 PATH 에 추가할 prefix  
`'tmp'` 가 지정되었다면, Bucket 에서 `tmp` 디렉토리가 생성되고 그 내부에 키(파일)들이 저장된다.

- `default_acl` : 권한 설정을 할 수 있다.

<br>

위와 같이 Storage Class 를 새로 만든 뒤, 해당 모듈의 위치와 클래스를 아래와 같이 입력한다.

```python
DEFAULT_FILE_STORAGE = 'config.storages.TmpStorage'  # 모듈의 위치가 입력되어야한다.
AWS_ACCESS_KEY_ID = '<AWS_ACCESS_KEY_ID>'
AWS_SECRET_ACCESS_KEY = '<AWS_SECRET_ACCESS_KEY>'
AWS_STORAGE_BUCKET_NAME = '<AWS_STORAGE_BUCKET_NAME>'
AWS_S3_REGION_NAME = '<AWS_S3_REGION_NAME>'
```

> `SignatureDoesNotMatch` 오류는 Bucket 이 생성된지 얼마안되었을 경우 발생할 수도 있음을 유의한다.

<br><br>

# Reference

- [django-storages Documentation: Amazon S3](https://django-storages.readthedocs.io/en/latest/backends/amazon-S3.html)

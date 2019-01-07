---
title: "[AWS] RDS - Launch Instance (PostgreSQL) and Connect To Django"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - AWS

tags:
  - AWS
  - RDS
  - instance
  - postgresql
  - django
  - python
  - psycopg2
  - SQL
  - DB

---

![aws-rds](https://img.shields.io/badge/AWS-RDS-71D0F0.svg)
![django-version-2.1.4](https://img.shields.io/badge/django-v2.1.4-brightgreen.svg)
![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# RDS

Amazon Relational Database Service

<br><br>

## Step1. DB 보안 그룹 생성

EC2 서비스 → 네트워크 및 보안(보안 그룹) → 보안 그룹 생성 → 인바운드 규칙 추가

- 유형: PostgreSQL

- 프로토콜: TCP

- 포트 범위: 5432

- 소스: Public 으로 생성 권장 X → 임시 보안을 위해 `내 IP` 로 설정

<br><br>

## Step2. DB 인스턴스 만들기

RDS 서비스 → 데이터베이스 생성버튼 클릭

<br>

### 1. 엔진 선택  

- 엔진 옵션: PostgreSQL 선택

- 하단의 `RDS 프리 티어에 적용되는 옵션만 사용` 체크

<br>

### 2. DB 세부 정보 지정

- 인스턴스 사양 (Default 값으로 PASS)

- 설정

![aws-ec2_intro2](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/AWS/images/aws-08-rds.png?raw=true){: width="700px"}

<br>

**마스터 사용자 이름** 과 **마스터 암호** 는 분실하면 안된다.  
해당 RDS Instance 에 접근하기위해 필요하다.

<br>

### 3. 고급 설정 구성

#### 네트워크 및 보안

- 퍼블릭 액세스 가능성 : `예` 체크

- VPC 보안 그룹: 기존 VPC 보안 그룹 사용
  - DB 보안 그룹 생성 항목에서 생성한 RDS 보안 그룹 선택
  - 기본으로 추가되어있는 default 는 목록에서 제거

- 나머지는 Default 값으로 PASS

<br>

#### 데이터베이스 옵션

- **데이터베이스 이름** 지정

- 나머지 Default 값으로 PASS

<br><br>

## Step3. Django DB 엔진 변경

- sqlite3 (기본엔진) → postgresql (변경할 엔진)

<br>

`settings.py`

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '<데이터베이스 이름>',
        'USER': '<마스터 사용자 이름>',
        'PASSWORD': '<마스터 암호>',
        'HOST': '<엔드 포인트>',
        'PORT': '5432',
    }
}
```

- 인스턴스를 생성할 때 작성했던 내용들을 위의 값에 맞게 입력한다.  

- 엔드 포인트는 RDS 인스턴스 생성 후 확인 가능하다. (인스턴스 생성시간이 조금 걸린다.)  

- 해당 데이터도 공개되어선 안되므로 데이터를 분리하여 관리하는 것을 권장  

<br><br>

## DB Migrate

### Install psycopg2-binary

```bash
$ pip install psycopg2-binary
```

`psycopg2` 는 python 과 postgresql 을 중개해주는 역할

<br>

### Migrate

```bash
$ python manage.py migrate
```

<br><br>

## PostgreSQL Install

```bash
$ brew install postgresql
```

```bash
$ psql --user=<마스터 사용자 이름> --host=<엔드 포인트> <데이터베이스 이름>
```

`<데이터베이스 이름>` 중 `postgres` 는 기본 DB 이다.  
`postgres` 로 psql 을 실행    
   
→ 비밀번호 입력 → postgres DB 에 접속  
> 현재 인바운드 설정에 `내 IP` 를 허용하였기 때문에 해당 IP에서만 접속이 가능하다.  
> 다른 IP로 접속을 시도하면 실패하므로 필요에 따라 인바운드를 재설정한다.
 
<br>

DB 에 진입한 상태에서 SQL Query 를 사용가능하다.  

<br>

### Command

- `\l`: 전체 DB 목록 프린트

- `\dt`: 전체 테이블 프린트

- `\c <DB_Name>` : 해당 DB 연결

- `\q`: 종료

- . . .

<br><br>

# Reference

- [Django Documentation - Settings: DATABASES](https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-DATABASES)

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

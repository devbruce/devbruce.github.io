---
title: "[MLOps] Airflow Using PostgreSQL(with Docker)"
excerpt: 
last_modified_at: 2022-05-15

categories:
  - MLOps

tags:
  - mlops
  - airflow
  - sqlite
  - postgresql
  - docker
  - container

---

![airflow-version-2.3.0](https://img.shields.io/badge/airflow-v2.3.0-lightgreen.svg)
![postgresql-14.2](https://img.shields.io/badge/postgres-v14.2-skyblue.svg)

# Airflow Using PostgreSQL(with Docker)

Airflow 설정 및 실행시 **환경변수를 포함한 Runtime 환경이 설정값에서 진행되는 것이 맞는지 유의**
{: .notice--danger}

## Airflow DB 변경 목적

1. 기본 DB인 SQLite3는 Production 환경에 적합 X
2. Airflow의 기본 Executor는 `SequentialExecutor`로 병렬처리 불가  
Executor를 변경하기 위해서는 SQLite3가 아닌 다른 DB로 변경 필요

<br>

## 1. Install PostgreSQL with Docker

### 1-1. Pull Image

```bash
docker pull postgres
```

- 특정 버전이 필요할 경우, [PostgreSQL DockerHub](https://hub.docker.com/_/postgres)를 참고하여 `postgres:${TAG_NAME}`와 같이 명시  
(명시하지 않을 경우 `${TAG_NAME}`의 기본값은 `latest`이다.

<br>

### 1-2. Create Container

```bash
docker run -d \
    -v ${HOST_SRC_DIR}:/var/lib/postgresql/data \
    -u "$(id -u):$(id -g)" \
    -p ${HOST_PORT}:5432 \
    -e POSTGRES_PASSWORD=${Password} \
    --name ${ContainerName} \
    postgres
```

- `-d`: Detached mode. 컨테이너를 백그라운드로 생성
- `-v`: Volume mount 설정. 해당 값을 지정하지 않을 경우, container가 제거되었을 때 데이터가 초기화된다.
- `-u`: UID(format: `<name|uid>[:<group|gid>]`), 설정하지 않을 경우 Host와 Container간 권한문제로 인해 에러발생
- `-p`: Port-forwarding. PostgreSQL의 기본 port는 5432이므로 `5432:5432`가 일반적으로 사용된다.
- `-e`: 환경변수 설정. 자세한 내용은 [PostgreSQL DockerHub](https://hub.docker.com/_/postgres)의 Environment Variables 섹션 참고
- `--name`: Container 이름 설정

<br>

#### PostgreSQL Container 환경변수

- `POSTGRES_PASSWORD`로 Password값을 선언시,  
컨테이너 내부 PostgreSQL super user의 password 값으로 설정된다.  
- `POSTGRES_USER`: PostgreSQL super user 이름(미설정시 기본값: `postgres`)  
- `POSTGRES_DB `: PostgreSQL 기본 database 이름(미설정시 기본값: `POSTGRES_USER`설정값)

<br><br>

### 1-3. Execute Container

```bash
docker exec -it postgres /bin/bash
```

<br>

### 1-4. Connect to PostgreSQL

- Option `-U`: `--username`
- Option `-d`: `--dbname`

```bash
psql -U postgres
```

<br>

만약 컨테이너 생성시
`-e POSTGRES_USER=myuser -e POSTGRES_DB=mydb`  
와 같이 환경변수를 추가로 선언했을 경우 다음과 같이 접속 가능

```bash
psql -U myuser -d mydb
```

<br>

#### Basic PostgreSQL Command

- `\?`: Help
- `\l`(\=\=`\list`): List databases
- `\dt`: List tables
- `\du`: List users

- `\c`(\=\=`\connect`): Connect to specific database  
(ex. `\c postgres`)

<br>

### 1-5. CREATE Database and User

`psql`명령어를 통해 PostgreSQL에 접속하였다면,  
다음의 명령어를 통해 db와 user를 생성한다.

```sql
CREATE DATABASE ${AIRFLOW_DB_NAME};
CREATE USER ${AIRFLOW_USER_NAME} WITH PASSWORD ${AIRFLOW_USER_PASSWORD};
GRANT ALL PRIVILEGES ON DATABASE ${AIRFLOW_DB_NAME} TO ${AIRFLOW_USER_NAME};
```

Password는 `'`(Single Quote)로 감싸줘야한다.
{: .notice--warning}

<br><br>

## 2. Update Airflow Config File

### 2-1. Airflow initialize

```bash
airflow db init
```

Python 환경 및 환경변수(`AIRFLOW_HOME`) 설정 유의
{: .notice--danger}

<br>

### 2-2. config 파일 설정 변경

> config file path: `${AIRFLOW_HOME}/airflow.cfg`

- `[database]` 섹션: `sql_alchemy_conn`값을 다음과 같이 변경한다.  

```
# 기본값
sql_alchemy_conn = sqlite:////${AIRFLOW_HOME}/airflow.db

# PostgreSQL
sql_alchemy_conn = postgresql+psycopg2://${AIRFLOW_USER_NAME}:${AIRFLOW_USER_PASSWORD}@localhost/${AIRFLOW_DB_NAME}
```

<br>

- `[core]` 섹션: `executor`값을 다음과 같이 변경한다.  

```
# 기본값(병렬처리 불가)
executor = SequentialExecutor

# 병렬처리 가능(by PostgreSQL)
executor = LocalExecutor
```

<br>

### 2-3. Airflow 설정값 적용

```bash
airflow db init
```

Initialize 때와 동일하게 Python 환경 및 환경변수(`AIRFLOW_HOME`) 설정값 유의
{: .notice--danger}

<br><br>

## 3. Check Database

- Docker Container 접속

```bash
docker exec -it postgres /bin/bash
```

- PostgreSQL 접속

```bash
psql -U ${AIRFLOW_USER_NAME} -d ${AIRFLOW_DB_NAME}
```

- 접속한 DB의 Table List 조회

```
\dt
```

```
                    List of relations
 Schema |             Name              | Type  | Owner
--------+-------------------------------+-------+---------------------
 public | ab_permission                 | table | ${AIRFLOW_USER_NAME}
 public | ab_permission_view            | table | ${AIRFLOW_USER_NAME}
 public | ab_permission_view_role       | table | ${AIRFLOW_USER_NAME}
 public | ab_register_user              | table | ${AIRFLOW_USER_NAME}
 public | ab_role                       | table | ${AIRFLOW_USER_NAME}
 public | ab_user                       | table | ${AIRFLOW_USER_NAME}
 public | ab_user_role                  | table | ${AIRFLOW_USER_NAME}
 public | ab_view_menu                  | table | ${AIRFLOW_USER_NAME}
 public | alembic_version               | table | ${AIRFLOW_USER_NAME}
 public | callback_request              | table | ${AIRFLOW_USER_NAME}
 public | connection                    | table | ${AIRFLOW_USER_NAME}
 public | dag                           | table | ${AIRFLOW_USER_NAME}
 public | dag_code                      | table | ${AIRFLOW_USER_NAME}
 public | dag_pickle                    | table | ${AIRFLOW_USER_NAME}
 public | dag_run                       | table | ${AIRFLOW_USER_NAME}
 public | dag_tag                       | table | ${AIRFLOW_USER_NAME}
 public | import_error                  | table | ${AIRFLOW_USER_NAME}
 public | job                           | table | ${AIRFLOW_USER_NAME}
 public | log                           | table | ${AIRFLOW_USER_NAME}
 public | log_template                  | table | ${AIRFLOW_USER_NAME}
 public | rendered_task_instance_fields | table | ${AIRFLOW_USER_NAME}
 public | sensor_instance               | table | ${AIRFLOW_USER_NAME}
 public | serialized_dag                | table | ${AIRFLOW_USER_NAME}
 public | session                       | table | ${AIRFLOW_USER_NAME}
 public | sla_miss                      | table | ${AIRFLOW_USER_NAME}
 public | slot_pool                     | table | ${AIRFLOW_USER_NAME}
 public | task_fail                     | table | ${AIRFLOW_USER_NAME}
 public | task_instance                 | table | ${AIRFLOW_USER_NAME}
 public | task_map                      | table | ${AIRFLOW_USER_NAME}
 public | task_reschedule               | table | ${AIRFLOW_USER_NAME}
 public | trigger                       | table | ${AIRFLOW_USER_NAME}
 public | variable                      | table | ${AIRFLOW_USER_NAME}
 public | xcom                          | table | ${AIRFLOW_USER_NAME}
(33 rows)
```

위의 과정을 통해 Airflow의 DB가 PostgreSQL로 정상적으로 변경되었음을 확인할 수 있다.  

<br><br>

## Create User

airflow webserver 실행 전, airflow user 생성 선행해야 오류 발생 X

```bash
airflow users create \
    --email ${Email} \
    --firstname ${FirstName} \
    --lastname ${LastName} \
    --password ${Password} \
    --role ${Role} \
    --username ${UserName}
```

- `--role`: `Admin`, `User`, `Op`, `Viewer`, `Public`

```bash
airflow webserver
```

<br>

## Reference

- [Airflow Guide - Set up a Database Backend](https://airflow.apache.org/docs/apache-airflow/stable/howto/set-up-database.html#setting-up-a-postgresql-database)

- [DockerHub - PostgreSQL](https://hub.docker.com/_/postgres)

---
title: "[MLOps] Airflow Init"
excerpt: 
last_modified_at: 2022-05-14

categories:
  - MLOps

tags:
  - mlops
  - airflow

---

![airflow-version-2.3.0](https://img.shields.io/badge/airflow-v2.3.0-lightgreen.svg)

## Step 0\. Install

```bash
pip install apache-airflow
```

<br>

## Step 1\. Initialize DB

Default DB: SQLite3  
{: .notice--info}

DAG, Task 관리를 위한 DB 생성  
기본 DB인 SQLite3 사용시, SequentialExecutor만 사용가능하여 병렬처리 불가

```bash
airflow db init
```

`~/airflow`에 디렉토리가 생성된다.  
초기 DB 생성 Path는 `${AIRFLOW_HOME}`이지만, 해당 환경변수가 없는 경우 `${HOME}/airflow`가 기본값이 된다.  
변경을 희망한다면 다음과 같이 환경변수를 임의로 선언이 필요하다.

<br>

```bash
export AIRFLOW_HOME="${AnyPath}"
```

<br>

생성된 디렉토리의 구조는 다음과 같다.

```
${AIRFLOW_HOME}
├── airflow.cfg
├── airflow.db
├── logs
│   └── scheduler
│       ├── YYYY-MM-DD
│       └── latest
└── webserver_config.py
```

<br>

## Step 2\. Create User

```bash
airflow users create \
    --email ${EMAIL} \
    --firstname ${FIRST_NAME} \
    --lastname ${LAST_NAME} \
    --password ${PASSWORD} \
    --role ${ROLE} \
    --username ${USERNAME}
```

- `--role`: `Admin`, `User`, `Op`, `Viewer`, `Public`

<br>

## Step 3\. Run Airflow Scheduler

```bash
airflow scheduler
```

### Options

- `-D`: run as daemon

<br>

## Step 4\. Run Airflow Web Server

생성된 유저가 없을 경우 Web Server 실행시 오류가 발생하므로  
`Step 2. Create User`가 선행되어야 한다.  
{: .notice--warning}

```bash
airflow webserver
```

### Options

- `--port`(Default: 8080)

<br><br>

## CLI Commands

### Show Dag List

```bash
airflow dags list
```

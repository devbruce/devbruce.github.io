---
title: "[Python] Package Manager (pip)"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - pip
  - package manager
  - requirements

---

# Python Package Manager (pip)

<br>

## pip Command

- `$ pip install <Package Name>` : 패키지 설치

- `$ pip install <Package Name>==<VERSION>` : 특정 버전의 패키지 설치

- `$ pip install --upgrade <Package Name>` : 해당 패키지 Version Upgrade

- `$ pip uninstall <Package Name>` : 패키지 제거

- `$ pip show <Package Name>` : 해당 패키지 정보 확인

- `$ pip search <Keyword>` : 설치된 패키지 검색

- `$ pip list` : 버전과 함께 설치된 패키지 전체 확인

<br><br>

### Manage Dependency

- `$ pip freeze > requirements.txt` : requirements.txt 파일에 현재 설치된 패키지목록 기록 및 저장

- `$ pip install -r requirements.txt` : requirements.txt 파일에 기록된 패키지 설치

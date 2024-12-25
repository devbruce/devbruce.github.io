---
title: "[Python] datetime: naive vs aware"
author: devbruce
date: 2023-04-24
categories:
  - Python
tags:
  - python
  - datetime
  - naive
  - aware
  - pytz
  - timezone
---

![python-3.12](https://img.shields.io/badge/python-3.12-blue.svg)

## Naive object vs Aware object

- datetime tzinfo: <kbd>None</kbd> `==` naive object

```python
from datetime import datetime

dt_naive = datetime(2020, 3, 1)  # naive object
print(dt_naive.tzinfo)
```

```text
None
```

- datetime tzinfo 존재 → aware object

```python
from datetime import datetime

import pytz

dt_naive = datetime(2020, 3, 1)  # naive object
dt_aware_kst = pytz.timezone("Asia/Seoul").localize(dt_naive)  # aware object
print(dt_aware_kst.tzinfo)
print(dt_aware_kst)
```

```text
Asia/Seoul
2020-03-01 00:00:00+09:00
```

## Timezone Assigment to Naive Object

Naive object to Aware object

> `replace` 로 naive 객체를 `Asia/Seoul` timezone 을 가진 aware 객체로 변경시 KST 가 아닌 LMT 기준이 된다.  
{: .prompt-danger }

```python
from datetime import datetime

import pytz

# Naive object(=> No timezone)
dt_naive = datetime(2020, 3, 1)  # naive object

# Aware object(=> have timezone)
## naive -> aware (using pytz localize) => Recommended
dt_aware_kst = pytz.timezone("Asia/Seoul").localize(dt_naive)
## naive -> aware (using replace) => LMT(Not KST)
dt_aware_lmt = dt_naive.replace(tzinfo=pytz.timezone("Asia/Seoul"))

print(f"{dt_aware_kst=}")
print(f"{dt_aware_lmt=}")
```

```text
dt_aware_kst=datetime.datetime(2020, 3, 1, 0, 0, tzinfo=<DstTzInfo 'Asia/Seoul' KST+9:00:00 STD>)
dt_aware_lmt=datetime.datetime(2020, 3, 1, 0, 0, tzinfo=<DstTzInfo 'Asia/Seoul' LMT+8:28:00 STD>)
```

## Timezone Conversion

naive 객체에 astimezone 적용시, runtime 의 기본 timezone 기준으로 변경된다.  
(Runtime 환경에 따라 값이 달라질 수 있다.)  

> timezone 변경이 필요한 경우, timezone 이 명시적인 aware 객체로 변환 후, 새로운 timezone 변경하는 것을 권장
{: .prompt-info }

```python
import time
from datetime import datetime

import pytz

# Naive object
dt_naive = datetime(2020, 3, 1)  # naive object
dt_naive_to_utc = dt_naive.astimezone(pytz.timezone("UTC")) # !Notice

# Aware object
## naive -> aware (using pytz localize)
dt_aware_kst = pytz.timezone("Asia/Seoul").localize(dt_naive)
## KST -> UTC
dt_aware_utc = dt_aware_kst.astimezone(pytz.timezone("UTC"))

print(f"Default timezone: {time.tzname}")
print(f"{dt_naive_to_utc=}")
print(f"{dt_aware_kst=}")
print(f"{dt_aware_utc=}")
```

```text
Default timezone: ('KST', 'KST')
dt_naive_to_utc=datetime.datetime(2020, 2, 29, 15, 0, tzinfo=<UTC>)
dt_aware_kst=datetime.datetime(2020, 3, 1, 0, 0, tzinfo=<DstTzInfo 'Asia/Seoul' KST+9:00:00 STD>)
dt_aware_utc=datetime.datetime(2020, 2, 29, 15, 0, tzinfo=<UTC>)
```

- runtime 의 기본 timezone 이 "Asia/Seoul" 이기 때문에 `dt_naive_to_utc` 이 -9 hours 가 된 것을 확인할 수 있다.

## Comparison of datetime

datetime 객체의 대소비교는 동일한 종류의 객체끼리만 가능하다.  

- <kbd>naive</kbd> `<=` <kbd>naive</kbd>: Bool (가능)
- <kbd>aware</kbd> `<=` <kbd>aware</kbd>: Bool (가능)
- <kbd>naive</kbd> `<=` <kbd>aware</kbd>: Error (불가능)

### Case 1. Same objects

```python
from datetime import datetime

import pytz

naive1 = datetime(2020, 3, 1)  # naive object
naive2 = datetime(2020, 4, 1)  # naive object
print(dt1 < dt2)

aware1 = pytz.timezone("Asia/Seoul").localize(naive1)  # aware object
aware2 = pytz.timezone("UTC").localize(naive2)  # aware object
print(aware1 < aware2)
```

```text
True
True
```

### Case 2. Naive & Aware

```python
from datetime import datetime

import pytz

naive = datetime(2020, 3, 1)  # naive object
aware = pytz.timezone("Asia/Seoul").localize(naive)  # aware object
print(naive < aware)
```

```text
TypeError: can't compare offset-naive and offset-aware datetimes
```

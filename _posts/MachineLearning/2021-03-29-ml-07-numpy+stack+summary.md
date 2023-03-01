---
title: "[ML] Numpy Stack Summary"
excerpt: 
last_modified_at: 2021-03-29

categories:
  - MachineLearning

tags:
  - python
  - numpy

---

![numpy-version-1.18.5](https://img.shields.io/badge/numpy-1.18.5-yellow)

# Numpy Stack

```python
import numpy as np
```

**차원 추가 X**

- `np.vstack()`
- `np.hstack()`
- `np.concatenate()` \<\-\- General

<br>

**새로운 차원 추가**

- `np.stack()`

<br>

**Special Case**

- `np.dstack()`

<br><br>

## np.vstack

`np.concatenate(arr_list, axis=0)` 과 동일

```python
a = np.random.randint(0, 10, size=(2, 3))
b = np.random.randint(0, 10, size=(4, 3))


print(np.vstack([a, b]).shape)
>>> (6, 3)

print(np.concatenate([a, b], axis=0).shape)
>>> (6, 3)
```

<br><br>

## np.hstack

`np.concatenate(arr_list, axis=1)` 과 동일

```python
a = np.random.randint(0, 10, size=(2, 5))
b = np.random.randint(0, 10, size=(2, 3))


print(np.hstack([a, b]).shape)
>>> (2, 8)

print(np.concatenate([a, b], axis=1).shape)
>>> (2, 8)
```

<br><br>

## np.concatenate

```python
a = np.random.randint(0, 10, (5, 6, 7, 8, 9))
b = np.random.randint(0, 10, (5, 6, 7, 8, 9))


print(np.concatenate([a, b], axis=3).shape)
>>> (5, 6, 7, 16, 9)
```

<br><br>

## np.stack

지정한 axis 에 새로운 차원을 만들면서 쌓는다. (Default axis=0)

```python
a = np.random.randint(0, 10, (3, 5, 7, 12))
b = np.random.randint(0, 10, (3, 5, 7, 12))


print(np.stack([a, b]).shape)
>>> (2, 3, 5, 7, 12)

print(np.stack([a, b], axis=1).shape)
>>> (3, 2, 5, 7, 12)

print(np.stack([a, b], axis=-1).shape)
>>> (3, 5, 7, 12, 2)
```

<br><br>

## np.dstack

- `ndim == 2`: 가장 마지막 axis 에 새로운 차원을 만들면서 쌓는다. `np.stack(arr_list, axis=-1)` 과 동일
- `ndim > 2`: axis=2 기준으로 쌓는다. `np.concatenate(arr_list, axis=2)` 과 동일

<br>

### ndim \=\= 2

```python
a = np.random.randint(0, 10, size=(2, 3))
b = np.random.randint(0, 10, size=(2, 3))


print(np.dstack([a, b]).shape)
>>> (2, 3, 2)
```

<br>

### ndim \=\= 3

```python
a = np.random.randint(0, 10, size=(2, 3, 4))
b = np.random.randint(0, 10, size=(2, 3, 4))


print(np.dstack([a, b]).shape)
>>> (2, 3, 8)
```

<br>

### ndim \=\= 4

```python
a = np.random.randint(0, 10, size=(2, 3, 4, 5))
b = np.random.randint(0, 10, size=(2, 3, 4, 5))


print(np.dstack([a, b]).shape)
>>> (2, 3, 8, 5)
```

<br><br>

## Conclusion

- 새로운 차원을 생성하지 않고 array 를 쌓고 싶은 경우
  - `ndim == 2` 일 때, 즉 **2차원 Matrix** 를 다룰 때는 `np.vstack()`, `np.hstack()` 사용 권장
  - `ndim > 2` 인 경우, `np.concatenate()` 사용 권장
  
<br>

- 새로운 차원을 생성하며 array 를 쌓고 싶은 경우: `np.stack()`

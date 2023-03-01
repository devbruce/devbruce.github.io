---
title: "[Algorithm] Bubble Sort"
excerpt: 
last_modified_at: 2019-01-29

categories:
  - Algorithm

tags:
  - algorithm
  - sort
  - bubble sort
  - python

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Bubble Sort

- 시간복잡도: O(n<sup>2</sup>)

- Stable Sort

- 순회를 마칠 때 마다 하나의 요소가 자신의 위치를 찾는다.  
따라서 2 회차 순회부터는 비교연산 횟수가 하나씩 감소한다.  

<br>

## Example

`[9, 8, 6, 4, 1, 3]`  

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 9 | 8 | 6 | 4 | 1 | 3 |

<br>

### 순회 1 회차  

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | <font color='red'>9</font> | <font color='red'>8</font> | 6 | 4 | 1 | 3 |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 8 | <font color='red'>9</font> | <font color='red'>6</font> | 4 | 1 | 3 |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 8 | 6 | <font color='red'>9</font> | <font color='red'>4</font> | 1 | 3 |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 8 | 6 | 4 | <font color='red'>9</font> | <font color='red'>1</font> | 3 |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 8 | 6 | 4 | 1 | <font color='red'>9</font> | <font color='red'>3</font> |

#### Result

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 8 | 6 | 4 | 1 | 3 | `9` |

<br>

### 순회 2 회차

최대값에 해당하는 9 가 가장 마지막으로 이동하여 자신의 위치를  
찾았기 때문에 마지막 요소(9)는 고려할 필요없어진다.  

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | <font color='red'>8</font> | <font color='red'>6</font> | 4 | 1 | 3 | `9` |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 6 | <font color='red'>8</font> | <font color='red'>4</font> | 1 | 3 | `9` |


| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 6 | 4 | <font color='red'>8</font> | <font color='red'>1</font> | 3 | `9` |
 

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 6 | 4 | 1 | <font color='red'>8</font> | <font color='red'>3</font> | `9` |

#### Result

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 6 | 4 | 1 | 3 | `8` | `9` |

<br>

### 순회 3 회차

두번째로 큰 값 8 이 자신의 위치로 이동하였기 때문에  
9 와 두번째 마지막 요소(8)는 고려할 필요없어진다.  

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | <font color='red'>6</font> | <font color='red'>4</font> | 1 | 3 | `8` | `9` |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 4 | <font color='red'>6</font> | <font color='red'>1</font> | 3 | `8` | `9` |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 4 | 1 | <font color='red'>6</font> | <font color='red'>3</font> | `8` | `9` |

#### Result

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 4 | 1 | 3 | `6` | `8` | `9` |

<br>

### 순회 4 회차

세번째로 큰 값 6 이 자신의 위치로 이동하였기 때문에  
9, 8, 6 은 고려할 필요없어진다.  

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | <font color='red'>4</font> | <font color='red'>1</font> | 3 | `6` | `8` | `9` |

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 1 | <font color='red'>4</font> | <font color='red'>3</font> | `6` | `8` | `9` |

#### Result

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | 1 | 3 | `4` | `6` | `8` | `9` | 

<br>

### 순회 5 회차

정렬이 완료된 4, 6, 8, 9 를 제외한  
1 과 3 을 비교한다.

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | <font color='red'>1</font> | <font color='red'>3</font> | `4` | `6` | `8` | `9` | 

#### Result

| Index | 0 | 1 | 2 | 3 | 4 | 5 |
|-------|---|---|---|---|---|---|
| Value | `1` | `3` | `4` | `6` | `8` | `9` | 

정렬이 완료되었다.

<br><br>

## Python Implementation Code

- `swap` 변수를 활용하여 변화가 없을 시 바로 종료하도록 설정

```python
def bubble_sort(seq):
    l = len(seq)
    for i in range(l-1):
    	swap = False
        for j in range(l-1-i):
            if seq[j] > seq[j+1]:
                seq[j], seq[j+1] = seq[j+1], seq[j]
                swap = True
        if not swap:
            break
    return seq
```

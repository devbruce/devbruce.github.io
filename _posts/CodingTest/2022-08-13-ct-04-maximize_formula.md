---
title: "[CodingTest] 2020 카카오 인턴십 - 수식 최대화(Python)"
excerpt: 
last_modified_at: 2022-08-13

categories:
  - CodingTest

tags:
  - programmers
  - algorithm
  - python
  - divide and conquer
  - recursive

---

# 2020 카카오 인턴십 - 수식 최대화(Python)

문제 링크(프로그래머스): <https://school.programmers.co.kr/learn/courses/30/lessons/67257>  
{: .notice--info}

## 접근 방식

- 분할정복(Divid and Conquer)  
→ 재귀(Recursive)활용

### Step 1

주어진 expression에서 연산자(`*`, `+`, `-`)를 추출합니다. 문자열에서 숫자가 아닌 string을 추출하면 되기 때문에 정규표현식을 활용합니다. 추출한 연산자의 종류만 알면 되기 때문에 중복된 값은 필요없으니 `set`을 활용하여 중복된 값을 제거합니다.

#### Example

```python
import re


expression_1 = '100-200*300-500+20'
ops_1 = set(re.findall('\D', expression_1))
print(ops_1)

expression_2 = '50*6-3*2'
ops_2 = set(re.findall('\D', expression_2))
print(ops_2)
```
```
{'-', '+', '*'}
{'-', '*'}
```

<br>

### Step 2

추출한 연산자들로 발생하는 모든 우선순위를 고려해야합니다. 가능한 우선순위 리스트를 표현하기 위해서는 두가지 방법이 존재합니다.

#### Example 1. `itertools.permutations`

Python의 내장 라이브러리 itertools의 permutations를 활용한 방법입니다.

```python
from itertools import permutations


# ops_1 == {'-', '+', '*'}
# ops_2 == {'-', '*'}
priorities_list_1 = list(permutations(ops_1, r=len(ops_1)))
priorities_list_2 = list(permutations(ops_2, r=len(ops_2)))

print(priorities_list_1)
print('=' * 30)
print(priorities_list_2)
```
```
[('-', '+', '*'), ('-', '*', '+'), ('+', '-', '*'), ('+', '*', '-'), ('*', '-', '+'), ('*', '+', '-')]
==============================
[('-', '*'), ('*', '-')]
```

#### Example 2. Self Implementation with Back-tracking

Back-tracking(with DFS)을 활용한 직접 구현 방식입니다.

```python
def get_priorities_list(ops:set) -> list:
    used = set()
    priorities = []
    _backtrack(arr=[], ops=list(ops), depth=0, priorities=priorities, used=used)
    return priorities


def _backtrack(arr:list, priorities:list, used:set, ops:list, depth:int):
    if depth == len(ops):
        priorities.append(arr.copy())
        return
    
    for i in range(len(ops)):
        if i in used:
            continue
            
        op = ops[i]
        used.add(i)
        arr.append(op)
        _backtrack(arr=arr, ops=ops, depth=depth+1, priorities=priorities, used=used)
        used.remove(i)
        arr.pop()
        
        
# ops_1 == {'-', '+', '*'}
# ops_2 == {'-', '*'}
priorities_list_1 = get_priorities_list(ops_1)
priorities_list_2 = get_priorities_list(ops_2)

print(priorities_list_1)
print('=' * 30)
print(priorities_list_2)
```
```
[('-', '+', '*'), ('-', '*', '+'), ('+', '-', '*'), ('+', '*', '-'), ('*', '-', '+'), ('*', '+', '-')]
==============================
[('-', '*'), ('*', '-')]
```

<br>

### Step 3

- Step 1: expression의 연산자(operators) 종류 추출
- Step 2: 추출한 연사자들을 활용한 우선순위 리스트 세팅

위 2개의 step을 거쳐 마지막으로 각각의 우선순위 리스트로 expression을 계산하고, 연산결과값 중 가장 큰 절대값을 구하면 됩니다. 이를 위해서 고민해야할 중점은 이것입니다.  

"우선순위가 높은 연산자를 어떻게 가장 먼저 연산시키는가?  

방법은 여러가지가 있겠지만, 제가 접근한 방법은 다음과 같습니다.  

우선순위는 다음과 같다고 가정하겠습니다.  

- 1순위: `*`
- 2순위: `+`
- 3순위: `-`

1순위가 `*`이기 때문에 `*`연산자에 해당하는 수식을 가장 먼저 계산해야합니다. 그러기 위해선, expression에서 2순위인 `+`와 관련된 식을 분리하는 것이 우선되어야 합니다. 마찬가지로 2순위인 `+`에 해당하는 식을 계산하기 위해서는 3순위인 `-`와 관련된 식을 먼저 분리해야합니다.  

단계를 표현하면 다음과 같습니다.

1. 가장 낮은 3순위 `-`로 split합니다.
2. 이전단계에서 split된 각각의 수식을 2순위 `+`로 split합니다.<br><br>
3. 이전단계에서 split된 수식들은 최종적으로 1순위 `*`와 관련된 수식만 남게됩니다.  
더 이상 split할 필요가 없습니다. `eval`을 통해 계산하고 각각 반환합니다.<br><br>
4. 이전단계에서 각각 계산되어 반환된 값들을 2순위 `+`로 join하고, `eval`을 통해 연산한 뒤 반환합니다.
5. 이전단계에서 각각 계산되어 반환된 값들을 3순위 `-`로 join하고, `eval`을 통해 연산한 뒤 반환합니다.


식을 분리(분할)하고, 독립적으로 계산한 뒤 병합하는 과정임을 알 수 있습니다. 즉 분할정복(Divide and Conquer)에 해당하며, 재귀를 통해 간단하게 구현가능합니다. 위의 로직은 분할정복의 대표적인 예제인 병합정렬(Merge sort)과 유사함을 알 수 있습니다. :)

<br>

## Solution

```python
import re
from itertools import permutations


def solution(expression:str) -> int:
    ops = set(re.findall('\D', expression))
    priorities_list = list(permutations(ops, r=len(ops)))
    
    ans = 0
    for priorities in priorities_list:
        evaluated_val = int(eval_recursive(exp=expression, priorities=priorities))
        ans = max(ans, abs(evaluated_val))
    
    return ans


def eval_recursive(exp:str, priorities:list, depth:int=0) -> str:
    if depth == (len(priorities) - 1):
        return str(eval(exp))
    
    op = priorities[depth]
    evaluated = eval(
        op.join(
            [eval_recursive(e, priorities, depth=depth+1) for e in exp.split(op)]
        )
    )
    return str(evaluated)
```

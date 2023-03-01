---
title: "[CodingTest] 2021 카카오 채용연계형 인턴십 - 거리두기 확인하기(Python)"
excerpt: 
last_modified_at: 2022-08-07

categories:
  - CodingTest

tags:
  - programmers
  - algorithm
  - python
  - dfs
  - bfs

---

# 2021 카카오 채용연계형 인턴십 - 거리두기 확인하기(Python)

문제 링크(프로그래머스): <https://school.programmers.co.kr/learn/courses/30/lessons/81302>  
{: .notice--info}

해당 문제는 주어진 place에서 모든 응시자 P를 기준으로 맨해튼 거리값(Manhattan distance) 2 이내에 또다른 P의 존재유무를 파악하는 것이다.

- 임의의 P에 대해서 맨해튼 거리값 2 이내에 또다른 P가 존재한다.(거리두기 실패): 0 반환
- 임의의 P에 대해서 맨해튼 거리값 2 이내에 또다른 P가 존재하지 않는다.(거리두기 성공): 1 반환

이 때 `'X'`(파티션)가 응시자 P사이에 존재할 경우, 거리값 2 이내라고 할지라도 거리두기를 지킨 것으로 인정된다.

## Example

아래와 같은 place를 복수개 저장하고 있는 list type의 places가 input으로 주어진다.  

- place: `["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"]`

| No. | 0 | 1 | 2 | 3 | 4 |
|-----|---|---|---|---|---|
| 0   | P | O | O | O | P |
| 1   | O | X | X | O | X |
| 2   | O | P | X | P | X |
| 3   | O | O | X | O | X |
| 4   | P | O | X | X | P |

<br>

## 접근 방식

- DFS
- BFS

주어진 place가 거리두기를 **실패했다는 조건이 달성되면 다른 케이스에 대한 탐색을 종료**하는 형태로 DFS or BFS를 활용하여 탐색한다. Step은 다음과 같다.

- 주어진 place에서 모든 `'P'`의 좌표들(candidates)을 구한다.
- 각 `'P'` 좌표(candidate)에 대해 상(Up), 하(Down), 좌(Left), 우(Right) 탐색
  - Manhattan distance 거리값 2 이내에서 `'P'`발견시 거리두기 실패: `False`반환
  - Ignore: Out of index
  - Ignore: Already visited node
  - Ignore: `'X'`(파티션) 

<br>

## Solution

### DFS

```python
from typing import List


ROWS, COLS = 5, 5

#     상   하  좌  우
DC = [ 0, 0, -1, 1]
DR = [-1, 1,  0, 0]


def solution(places):
    global visited
    
    ans = []
    for place in places:
        candidates = get_candidates(place)
        is_distancing = True
        for candidate in candidates:
            visited = [[False] * COLS for _ in range(ROWS)]
            if not dfs(place, candidate):
                is_distancing = False
                break
                
        ans.append(int(is_distancing))
    return ans


def dfs(place:list, candidate:tuple, depth:int=0) -> bool:
    # depth == 현재 탐색 중인 거리값(Current distance)
    if depth > 2:
        return True
    
    r_idx, c_idx = candidate
    if r_idx < 0 or r_idx >= ROWS or c_idx < 0 or c_idx >= COLS:  # Ignore: Out of index
        return True
    if visited[r_idx][c_idx]:  # Ignore: Already visited node
        return True
    if place[r_idx][c_idx] == 'X':  # Ignore: 'X'(파티션)
        return True
    
    # 거리값 2 이내의 응시자 존재 --> Fail
    if depth != 0 and place[r_idx][c_idx] == 'P':
        return False
            
    visited[r_idx][c_idx] = True
    for d_idx in range(4):  # 상, 하, 좌, 우
        r = r_idx + DR[d_idx]
        c = c_idx + DC[d_idx]
        if not dfs(place, (r, c), depth=depth+1):
            return False
    return True


def get_candidates(place:list) -> List[tuple]:
    candidates = []
    for r_idx in range(ROWS):
        for c_idx in range(COLS):
            if place[r_idx][c_idx] == 'P':
                candidates.append((r_idx, c_idx))
    return candidates
```

<br>

### BFS

```python
from typing import List
from collections import deque


ROWS, COLS = 5, 5

#     상   하  좌  우
DC = [ 0, 0, -1, 1]
DR = [-1, 1,  0, 0]


def solution(places):
    ans = []
    for place in places:
        candidates = get_candidates(place)
        is_distancing = True
        for candidate in candidates:
            if not bfs(place, candidate):
                is_distancing = False
                break
                
        ans.append(int(is_distancing))
    return ans


def bfs(place:list, candidate:tuple) -> bool:
    queue = deque([candidate])
    visited = [[False] * COLS for _ in range(ROWS)]
    
    while queue:
        popped_idx = queue.popleft()
        if get_distance(candidate, popped_idx) >= 2:
            break
            
        r_idx, c_idx = popped_idx
        visited[r_idx][c_idx] = True
        for d_idx in range(4):  # 상, 하, 좌, 우
            r = r_idx + DR[d_idx]
            c = c_idx + DC[d_idx]
            if r < 0 or r >= ROWS or c < 0 or c >= COLS:  # Ignore: Out of index
                continue
            if visited[r][c]:  # Ignore: Already visited node
                continue
            if place[r][c] == 'X':  # Ignore: 'X'(파티션)
                continue
            
            # 거리값 2 이내의 응시자 존재 --> Fail
            if place[r][c] == 'P':
                return False  
            queue.append((r, c))
    
    return True


def get_candidates(place:list) -> List[tuple]:
    candidates = []
    for r_idx in range(ROWS):
        for c_idx in range(COLS):
            if place[r_idx][c_idx] == 'P':
                candidates.append((r_idx, c_idx))
    return candidates


def get_distance(p1:tuple, p2:tuple) -> int:
    x1, y1 = p1
    x2, y2 = p2
    distance = abs(x2 - x1) + abs(y2 - y1)
    return distance
```

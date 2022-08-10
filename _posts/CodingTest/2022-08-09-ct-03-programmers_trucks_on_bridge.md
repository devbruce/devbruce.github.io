---
title: "[CodingTest] 프로그래머스 - 다리를 지나는 트럭(Python)"
excerpt: 
last_modified_at: 2022-08-09

categories:
  - CodingTest

tags:
  - programmers
  - algorithm
  - python
  - queue
  - deque

---

# 프로그래머스 - 다리를 지나는 트럭(Python)

문제 링크: <https://school.programmers.co.kr/learn/courses/30/lessons/42583>  
{: .notice--info}

## Input data

- `bridge_length`: 다리의 길이
- `weight`: 다리가 견딜 수 있는 최대 무게
- `truck_weights`: 다리를 지나갈 예정인 트럭들의 무게 리스트

<br>

## 접근 방식

- Queue

Python에서 queue를 활용하기 위해 `collections`의 `deque`를 활용했습니다.  
{: .notice--info}

### 1\. Initializing

- bridge: `bridge_length` 양방향 데이터 I/O을 위해  `bridge_length` 길이의 deque를 0으로 채워 생성
- `truck_weights`: 양방향 I/O을 위해 deque으로 변환


### 2\. While Loop

**1초가 지날 때 마다 첫 번째 아이템(index 0)을 pop과 동시에 조건부로 0 또는 truck(무게값) 추가**  
  
- (현재 다리 위에있는 트럭 무게의 합 + 다리를 지나갈 예정인 트럭 무게)  >`weight`  
→ 현재 bridge가 무게초과 상태이기 때문에 0을 append  
(현재 다리위에 있는 truck이 먼저 지나가도록 대기합니다.)

- (현재 다리 위에있는 트럭 무게의 합 + 다리를 지나갈 예정인 트럭 무게) <= `weight`  
→ `truck_weights`에서 대기 중이던 트럭(첫 번째 Item)을 pop하여 다리에 append  
(대기 중이던 트럭이 다리 위로 올라가게 됩니다.)

- 이 과정을 `bridge`가 empty가 될 때까지 반복

<br>

### Example

- `bridge_length`: 2
- `weight`: 10
- `truck_weights`: `[7, 4, 5, 6]`

```
Time(sec): 0 | Bridge: [0, 0]
Time(sec): 1 | Bridge: [0, 7]
Time(sec): 2 | Bridge: [7, 0]
Time(sec): 3 | Bridge: [0, 4]
Time(sec): 4 | Bridge: [4, 5]
Time(sec): 5 | Bridge: [5, 0]
Time(sec): 6 | Bridge: [0, 6]
Time(sec): 7 | Bridge: [6]
Time(sec): 8 | Bridge: []  # End of Loop
```

- `bridge_length`: 4
- `weight`: 12
- `truck_weights`: `[2, 9, 4, 6, 7]`

```
Time(sec): 0 | Bridge: [0, 0, 0, 0]
Time(sec): 1 | Bridge: [0, 0, 0, 2]
Time(sec): 2 | Bridge: [0, 0, 2, 9]
Time(sec): 3 | Bridge: [0, 2, 9, 0]
Time(sec): 4 | Bridge: [2, 9, 0, 0]
Time(sec): 5 | Bridge: [9, 0, 0, 0]
Time(sec): 6 | Bridge: [0, 0, 0, 4]
Time(sec): 7 | Bridge: [0, 0, 4, 6]
Time(sec): 8 | Bridge: [0, 4, 6, 0]
Time(sec): 9 | Bridge: [4, 6, 0, 0]
Time(sec):10 | Bridge: [6, 0, 0, 0]
Time(sec):11 | Bridge: [0, 0, 0, 7]
Time(sec):12 | Bridge: [0, 0, 7]
Time(sec):13 | Bridge: [0, 7]
Time(sec):14 | Bridge: [7]
Time(sec):15 | Bridge: []  # End of Loop
```

### 추가 최적화

다리 위에 있는 트럭 무게의 합을 구할 때 `sum(bridge)`와 같이 sum 함수를 iteration마다 사용하게되면 `bridge_length`만큼의 시간복잡도(`O(n)`)가 발생하게 됩니다. 이러한 문제를 조금 더 최적화 시키기 위해서 반복문 외부에 `current_bridge_weight`를 정의하고, 이 값에 트럭의 무게를 더하고 빼는 방식(`O(1)`)으로 최적화를 시켰습니다.

<br>

## Solution

```python
from collections import deque


def solution(bridge_length:int, weight:int, truck_weights:list) -> int:
    ans = 0
    bridge = deque([0] * bridge_length)
    truck_weights = deque(truck_weights)
    current_bridge_weight = 0
    while bridge:
        current_bridge_weight -= bridge.popleft()
        if truck_weights:
            if current_bridge_weight + truck_weights[0] <= weight:
                current_truck = truck_weights.popleft()
                current_bridge_weight += current_truck
                bridge.append(current_truck)
            else:
                bridge.append(0)
        ans += 1
    return ans
```

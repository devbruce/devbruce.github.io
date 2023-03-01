---
title: "[Algorithm] Prime Number / Sieve of Eratosthenes"
excerpt: 소수 / 에라토스테네스의 체
last_modified_at: 2019-02-13

categories:
  - Algorithm

tags:
  - algorithm
  - prime
  - Sieve of Eratosthenes
  - python

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Prime Number (소수)

- 약수가 1 과 자기 자신밖에 없는 1보다 큰 양수  

양수 N 이 소수인지 확인하는 방법은 아래의 코드와 같이  
일일이 확인하는 방법이 있다.  

```python
def is_prime(num):
    if num <= 1: return False
    for i in range(2, num):
        if num % i == 0:
            return False
    return True
```

```python
print(is_prime(2))
print(is_prime(6))
print(is_prime(101))
```
\>\>\> `True`  
\>\>\> `False`  
\>\>\> `True`  

<br><br>

## Sieve of Eratosthenes

- 에라토스테네스의 체

| <span style="background-color:gray">1</span>  | 2  | 3  | 4  | 5  | 6  | 7  | 8  | 9  | 10 |
|----|----|----|----|----|----|----|----|----|----|
| 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 |
| 21 | 22 | 23 | 24 | 25 | 26 | 27 | 28 | 29 | 30 |
| 31 | 32 | 33 | 34 | 35 | 36 | 37 | 38 | 39 | 40 |
| 41 | 42 | 43 | 44 | 45 | 46 | 47 | 48 | 49 | 50 |

위와 같이 수를 나열하였을 때 모든 수를 소수라고 가정한다. (1은 제외)  

이제 2 부터 순서대로  
2 자신을 제외한 모든 2의 배수를 소수 목록에서 제거한다.  

| <span style="background-color:gray">1</span>  | 2  | 3  | <span style="background-color:yellow">4</span>  | 5  | <span style="background-color:yellow">6</span>  | 7  | <span style="background-color:yellow">8</span>  | 9  | <span style="background-color:yellow">10</span> |
|----|----|----|----|----|----|----|----|----|----|
| 11 | <span style="background-color:yellow">12</span> | 13 | <span style="background-color:yellow">14</span> | 15 | <span style="background-color:yellow">16</span> | 17 | <span style="background-color:yellow">18</span> | 19 | <span style="background-color:yellow">20</span> |
| 21 | <span style="background-color:yellow">22</span> | 23 | <span style="background-color:yellow">24</span> | 25 | <span style="background-color:yellow">26</span> | 27 | <span style="background-color:yellow">28</span> | 29 | <span style="background-color:yellow">30</span> |
| 31 | <span style="background-color:yellow">32</span> | 33 | <span style="background-color:yellow">34</span> | 35 | <span style="background-color:yellow">36</span> | 37 | <span style="background-color:yellow">38</span> | 39 | <span style="background-color:yellow">40</span> |
| 41 | <span style="background-color:yellow">42</span> | 43 | <span style="background-color:yellow">44</span> | 45 | <span style="background-color:yellow">46</span> | 47 | <span style="background-color:yellow">48</span> | 49 | <span style="background-color:yellow">50</span> |

<br>

2 의 배수를 모두 제거함에따라  
제거되지 않은 수 중 가장 작은 값은 3 이다.  
3 자신을 제외한 3 의 배수를 모두 제거한다.  

| <span style="background-color:gray">1</span>  | 2  | 3  | <span style="background-color:yellow">4</span>  | 5  | <span style="background-color:yellow">6</span>  | 7  | <span style="background-color:yellow">8</span>  | <span style="background-color:orange">9</span>  | <span style="background-color:yellow">10</span> |
|----|----|----|----|----|----|----|----|----|----|
| 11 | <span style="background-color:yellow">12</span> | 13 | <span style="background-color:yellow">14</span> | <span style="background-color:orange">15</span> | <span style="background-color:yellow">16</span> | 17 | <span style="background-color:yellow">18</span> | 19 | <span style="background-color:yellow">20</span> |
| <span style="background-color:orange">21</span> | <span style="background-color:yellow">22</span> | 23 | <span style="background-color:yellow">24</span> | 25 | <span style="background-color:yellow">26</span> | <span style="background-color:orange">27</span> | <span style="background-color:yellow">28</span> | 29 | <span style="background-color:yellow">30</span> |
| 31 | <span style="background-color:yellow">32</span> | <span style="background-color:orange">33</span> | <span style="background-color:yellow">34</span> | 35 | <span style="background-color:yellow">36</span> | 37 | <span style="background-color:yellow">38</span> | <span style="background-color:orange">39</span> | <span style="background-color:yellow">40</span> |
| 41 | <span style="background-color:yellow">42</span> | 43 | <span style="background-color:yellow">44</span> | <span style="background-color:orange">45</span> | <span style="background-color:yellow">46</span> | 47 | <span style="background-color:yellow">48</span> | 49 | <span style="background-color:yellow">50</span> |

<br>

2 와 3 의 배수를 제거함에따라  
제거되지 않은 수 중 가장 작은 값은 5 이다.  
5 자신을 제외한 5 의 배수를 모두 제거한다.  

| <span style="background-color:gray">1</span>  | 2  | 3  | <span style="background-color:yellow">4</span>  | 5  | <span style="background-color:yellow">6</span>  | 7  | <span style="background-color:yellow">8</span>  | <span style="background-color:orange">9</span>  | <span style="background-color:yellow">10</span> |
|----|----|----|----|----|----|----|----|----|----|
| 11 | <span style="background-color:yellow">12</span> | 13 | <span style="background-color:yellow">14</span> | <span style="background-color:orange">15</span> | <span style="background-color:yellow">16</span> | 17 | <span style="background-color:yellow">18</span> | 19 | <span style="background-color:yellow">20</span> |
| <span style="background-color:orange">21</span> | <span style="background-color:yellow">22</span> | 23 | <span style="background-color:yellow">24</span> | <span style="background-color:skyblue">25</span> | <span style="background-color:yellow">26</span> | <span style="background-color:orange">27</span> | <span style="background-color:yellow">28</span> | 29 | <span style="background-color:yellow">30</span> |
| 31 | <span style="background-color:yellow">32</span> | <span style="background-color:orange">33</span> | <span style="background-color:yellow">34</span> | <span style="background-color:skyblue">35</span> | <span style="background-color:yellow">36</span> | 37 | <span style="background-color:yellow">38</span> | <span style="background-color:orange">39</span> | <span style="background-color:yellow">40</span> |
| 41 | <span style="background-color:yellow">42</span> | 43 | <span style="background-color:yellow">44</span> | <span style="background-color:orange">45</span> | <span style="background-color:yellow">46</span> | 47 | <span style="background-color:yellow">48</span> | 49 | <span style="background-color:yellow">50</span> |

<br>

2, 3, 그리고 5 의 배수를 제거함에따라  
제거되지 않은 수 중 가장 작은 값은 7 이다.  
7 자신을 제외한 7 의 배수를 모두 제거한다.  

| <span style="background-color:gray">1</span>  | 2  | 3  | <span style="background-color:yellow">4</span>  | 5  | <span style="background-color:yellow">6</span>  | 7  | <span style="background-color:yellow">8</span>  | <span style="background-color:orange">9</span>  | <span style="background-color:yellow">10</span> |
|----|----|----|----|----|----|----|----|----|----|
| 11 | <span style="background-color:yellow">12</span> | 13 | <span style="background-color:yellow">14</span> | <span style="background-color:orange">15</span> | <span style="background-color:yellow">16</span> | 17 | <span style="background-color:yellow">18</span> | 19 | <span style="background-color:yellow">20</span> |
| <span style="background-color:orange">21</span> | <span style="background-color:yellow">22</span> | 23 | <span style="background-color:yellow">24</span> | <span style="background-color:skyblue">25</span> | <span style="background-color:yellow">26</span> | <span style="background-color:orange">27</span> | <span style="background-color:yellow">28</span> | 29 | <span style="background-color:yellow">30</span> |
| 31 | <span style="background-color:yellow">32</span> | <span style="background-color:orange">33</span> | <span style="background-color:yellow">34</span> | <span style="background-color:skyblue">35</span> | <span style="background-color:yellow">36</span> | 37 | <span style="background-color:yellow">38</span> | <span style="background-color:orange">39</span> | <span style="background-color:yellow">40</span> |
| 41 | <span style="background-color:yellow">42</span> | 43 | <span style="background-color:yellow">44</span> | <span style="background-color:orange">45</span> | <span style="background-color:yellow">46</span> | 47 | <span style="background-color:yellow">48</span> | <span style="background-color:purple">49</span> | <span style="background-color:yellow">50</span> |

<br>

2, 3, 5, 7 의 배수를 제거함에따라  
제거되지 않은 수 중 가장 작은 값은 11 이다.  
11 자신을 제외한 11 의 배수를 모두 제거한다.  
이 때 11 자신을 제외한 11 의 배수는 이미 제거되어 있으므로 패스한다.  

<br>

2, 3, 5, 7, 11 의 배수를 제거함에따라  
제거되지 않은 수 중 가장 작은 값은 13 이다.  
13 자신을 제외한 13 의 배수를 모두 제거한다.  
이 때 13 자신을 제외한 13 의 배수는 이미 제거되어 있으므로 패스한다.  

위와 같은 과정을 나열된 수의 가장 큰 값인 50 까지 반복하여  
최종적으로 남아있는 수가 소수가 된다.  

<br>

### Result

| <span style="background-color:gray">1</span>  | 2  | 3  | <span style="background-color:gray">4</span>  | 5  | <span style="background-color:gray">6</span>  | 7  | <span style="background-color:gray">8</span>  | <span style="background-color:gray">9</span>  | <span style="background-color:gray">10</span> |
|----|----|----|----|----|----|----|----|----|----|
| 11 | <span style="background-color:gray">12</span> | 13 | <span style="background-color:gray">14</span> | <span style="background-color:gray">15</span> | <span style="background-color:gray">16</span> | 17 | <span style="background-color:gray">18</span> | 19 | <span style="background-color:gray">20</span> |
| <span style="background-color:gray">21</span> | <span style="background-color:gray">22</span> | 23 | <span style="background-color:gray">24</span> | <span style="background-color:gray">25</span> | <span style="background-color:gray">26</span> | <span style="background-color:gray">27</span> | <span style="background-color:gray">28</span> | 29 | <span style="background-color:gray">30</span> |
| 31 | <span style="background-color:gray">32</span> | <span style="background-color:gray">33</span> | <span style="background-color:gray">34</span> | <span style="background-color:gray">35</span> | <span style="background-color:gray">36</span> | 37 | <span style="background-color:gray">38</span> | <span style="background-color:gray">39</span> | <span style="background-color:gray">40</span> |
| 41 | <span style="background-color:gray">42</span> | 43 | <span style="background-color:gray">44</span> | <span style="background-color:gray">45</span> | <span style="background-color:gray">46</span> | 47 | <span style="background-color:gray">48</span> | <span style="background-color:gray">49</span> | <span style="background-color:gray">50</span> |

제거되지않고 남은 수  

2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47  

가 소수가 된다.

에라토스테네스의 체를 Python 코드로 구현할 경우 아래와 같다.  

### Python Implementation Code

```python
def eratosthenes(n):
    e = [True for _ in range(n+1)]
    e[0], e[1] = False, False
    primes = list()
    for i in range(2, n+1):
        if e[i]:
            primes.append(i)
            for j in range(i*2, n+1, i):
                e[j] = False
    return primes
```

```python
print(eratosthenes(50))
```
\>\>\> `[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47]`

---
title: "[Python] Loop - for / while"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - loop
  - for
  - while
  - range
  - iterable

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Loop (반복문)

- for  

- while

<br>

## 1. for


```python
for i in range(3):
    print(i)
```
\>\>\> 0  
\>\>\> 1  
\>\>\> 2  

<br>

`range(3)` 이 들어간 자리에는 **iterable** 객체라면 무엇이든 들어갈 수 있다.  
iterable 객체의 대표적인 예시 : list, tuple, dictionary, set, string ...  
예시로 string 을 통해 for 반복문을 만들어보겠다.

```python
for i in "Python":
    print(i)
```
\>\>\> P  
\>\>\> y  
\>\>\> t  
\>\>\> h  
\>\>\> o  
\>\>\> n  

> dictionary 의 경우에는 Key 에 해당하는 값들이 반복문의 변수에 삽입된다.

<br><br>

### About range()

range 함수는 for 반복문만을 위해 있는 함수가 아니다.  
`range(n)` 의 의미는 0부터 n-1 까지의 수를 요소로 가지는 iterable 객체를 만든다.  
`range(3, 7)` 은 3부터 6까지의 수를 요소로 가지는 iterable 객체를 만든다.  

```python
range(3,10)
```
\>\>\> range(3, 10)

```python
list(range(3,10))
```
\>\>\> [3, 4, 5, 6, 7, 8, 9]

<br><br>

### for + else

for 반복문에서 in 뒤에 붙는 iterable 객체의 순환이 끝나면  
for 뒤에 이어지는 else 구문이 실행된다.  
하지만 iterable 객체의 순환이 끝나서가 아니라  
break 를 활용하여 반복문을 나가게 될 경우에는 for문과 연결된 else 구문도 탈출하게 된다.  

```python
for i in range(1, 5+1):
    print(i)
    
else:
    print("else!")

print("Out!")
```
\>\>\> 1  
\>\>\> 2  
\>\>\> 3  
\>\>\> 4  
\>\>\> 5  
\>\>\> else!  
\>\>\> Out!  

<br>

위의 예시코드는 for문의 iterable range객체의 순환이 끝난 뒤, else구문이 실행되고,  
반복문 밖에 해당되는 `print("Out!")` 구문이 출력되었다.  

<br>

이번엔 `break` 를 활용하여 위의 반복문을 탈출해보겠다.

```python
for i in range(1, 5+1):
    if i == 2:
        break
    print(i)
    
else:
    print("else!")
    
print("Out!")
```
\>\>\> 1  
\>\>\> Out!  

<br>

i 가 2 일 때 break 를 통해 반복문을 탈출하였기 때문에 else 구문도 탈출하게되어  
곧바로 반복문 밖 명령어 `print("Out!")` 가 출력되었음을 알 수 있다.

<br><br>

## 2. while

```python
while condition:
    print("Loop!")
```
while 옆의 condition 이라는 변수가 True 일 경우 무한한 반복을 한다.
그렇다면 while 의 반복을 끊는 방법은 없을까?

방법은 크게 두가지이다.

- 1st. `break` 를 사용한다.  


```python
n = 0
condition = True

while condition:
    print("Loop!")
    n += 1
    if n == 3:
        break
```
\>\>\> Loop!  
\>\>\> Loop!  
\>\>\> Loop!  

`condition` 을 `True` 로 정의하여 무한한 반복을 시켰지만,  
반복문 밖에서`n` 을 0으로 정의, 반복이 될 때마다 `n` 에 1을 더해주고,  
3이 될 경우 `break`명령어를 실행하도록 하여 반복을 중단시켰다.  

> 만약 n을 반복문 내에 정의할 경우 반복이 될 때마다 `n = 0`이 실행되어 `n += 1` 코드가 무의미해지고, 결과적으로 무한반복을 하게된다.

<br><br>

- 2nd. `condition`이 특정조건에서 `False`가 되게한다.  


```python
condition = 3

while condition > 0:
    print("Loop!")
    condition -= 1
```
\>\>\> Loop!  
\>\>\> Loop!  
\>\>\> Loop!  

위 코드에서는 `condition` 변수를 3으로 정의한 뒤, 0보다 클 경우 반복을 하도록 반복문을 정의하였다.  
반복문내에서 한번 코드를  실행할 때마다 `condition` 변수를 1씩 줄이게 되고,   
`condition` 이 0이 되는 순간 반복 조건은 `False` 가 되어 반복을 멈추게 되는 방식이다.

<br><br>

### while + else

for + else 와 크게 다르지 않다.  
마찬가지로 **break 를 통해 반복문을 탈출하게 되면 else 구문도 함께 탈출한다.**  
하지만 while 조건문이 `False` 가 되도록 하여 반복문을 탈출하게되면 else 구문은 실행된다.

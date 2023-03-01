---
title: "[Python] break / pass / continue / return"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - break
  - pass
  - continue
  - return

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# [Python] break / pass / continue / return

## break

`break` : **반복문 탈출(중단)**  
반복문을 벗어나서 아래에 위치한 코드를 계속해서 실행시킨다.

```python
def test_break():
    # ====== Loop ======
    for i in range(5):
        if i == 2:
            break
        print(f"[i=={i}] In the loop")
    # ====== ==== ======
    print("Out of the loop")


test_break()
print("Out of the function")
```
```
[i==0] In the loop
[i==1] In the loop
Out of the loop
Out of the function
```

`i == 0`, `i === 1` 코드가 실행되고, `i == 2`일 때 `break`가 적용되었다.  

이에따라 반복문을 곧바로 탈출(중단)하고  
`print("Out of the loop")`가 실행됨을 알 수 있다.  

<br><br>

## pass

`pass` : 아무것도 하지 않는다. 그리고 코드를 계속해서 실행한다.  
**함수나 반복문을 벗어나는 것이 아니다.**

```python
def test_pass():
    # ====== Loop ======
    for i in range(5):
        if i == 2:
            pass
        print(f"[i=={i}] In the loop")
    # ====== ==== ======
    print("Out of the loop")


test_pass()
print("Out of the function")
```
```
[i==0] In the loop
[i==1] In the loop
[i==2] In the loop
[i==3] In the loop
[i==4] In the loop
Out of the loop
Out of the function
```
  
`i == 2` 일 때 `pass`가 실행되었지만  
정의대로 아무것도 하지않고 코드를 계속해서 실행하는 것을 알 수 있다.

```
        if i == 2:
            pass
```

해당 코드가 없는 것과 동일하게 동작한다.

<br><br>

## continue

`continue` : 반복문의 처음으로 돌아간다.  
그리고 코드를 계속해서 실행해나간다.

```python
def test_continue():
    # ====== Loop ======
    for i in range(5):
        if i == 2:
            continue
        print(f"[i=={i}] In the loop")
    # ====== ==== ======
    print("Out of the loop")


test_continue()
print("Out of the function")
```
```
[i==0] In the loop
[i==1] In the loop
[i==3] In the loop
[i==4] In the loop
Out of the loop
Out of the function
```
  
`i == 2` 일 때 `continue`로 인해 반복문의 처음으로 돌아가고, 반복문이 계속해서 진행된다.
다시말해, `i == 2` 일 때 `print(f"[i=={i}] In the loop")`는 실행되지 않는다.

<br><br>

## return

`return` : **함수 즉시 탈출(중단) 및 반환**

```python
def test_return():
    # ====== Loop ======
    for i in range(5):
        if i == 2:
            return 'RETURN'  # 문자열 'RETURN' 외부로 반환
        print(f"[i=={i}] In the loop")
    # ====== ==== ======
    print("Out of the loop")


output = test_return()
print("Out of the function")
```
```
[i==0] In the loop
[i==1] In the loop
Out of the function
```

위 코드에서 `i = 2` 일 때 `return`이 실행되어 함수자체에서 벗어나서 중단되었음을 알 수 있다.  

```python
print(output)
```
```
RETURN
```

변수 `output`에 `return`을 통해 반환한 문자열 `'RETURN'`이 할당된 것을 확인할 수 있다.

<br><br>

## Summary

- Loops (반복문)
  - `break`
  - `continue`

- Function (함수)
  - `return`

- Anywhere!
  - `pass`

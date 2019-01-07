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

# break

`break` : 해당 반복문을 탈출한다. 그리고 코드를 계속해서 실행해나간다.  

반복문을 단순히 중지시키는 것이 아니라, 반복문을 벗어나서 반복문 아래에 위치한 코드를 계속해서 실행시킨다.

```python
def test_break():
    for i in range(5):
        
        if i == 2:
            break
        
        print("This is break test")
                    
    print("This code is below the loop.")
```
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This code is below the loop.'`  

위 코드를 보면 알 수 있겠지만, `i = 0`, `i = 1`, 코드가 실행되고, `i = 2`일 때 `break`가 적용되었다.  
그리고 for 반복문 아래에 위치한 `print("This code is below the loop.")`가 실행됨을 알 수 있다.

<br><br>

# pass

`pass` : 아무것도 하지 않는다. 그리고 코드를 계속해서 실행해나간다.  
함수나 반복문을 벗어나는 것이 아니다.

```python
def test_break():
    for i in range(5):
        
        if i == 2:
            pass
        
        print("This is break test")
                    
    print("This code is below the loop.")
```
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This code is below the loop.'`  
  
위 코드에서 `i = 2` 일 때 `pass`가 적용되었지만, 정의대로 아무것도 하지않고 코드실행을 계속해서 이어간다는 것을 알 수 있다.

<br><br>

# continue

`continue` : 반복문의 처음으로 돌아간다.  
그리고 코드를 계속해서 실행해나간다.

```python
def test_break():
    for i in range(5):
        
        if i == 2:
            continue
        
        print("This is break test")
                    
    print("This code is below the loop.")
```
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  
\>\>\> `'This code is below the loop.'`  
  
위 코드에서 `i = 2` 일 때, `continue`가 실행되어 반복문의 처음으로 돌아가서 계속해서 코드가 실행되었다.  
즉 `i == 2` 일 때는 `print("This is break test")`가 실행되지 않았음을 알 수 있다.

<br><br>

# return

`return` : **함수 즉시 탈출(중단)**, 함수 내부값 외부로 반환  

```python
def test_break():
    for i in range(5):
        
        if i == 2:
            return
        
        print("This is break test")
                    
    print("This code is below loops.")
```
\>\>\> `'This is break test'`  
\>\>\> `'This is break test'`  

위 코드에서 `i = 2` 일 때 `return`이 실행되어 함수자체에서 벗어나서 중단되었음을 알 수 있다.

<br><br>

# Summary

- Loops (반복문)
  - `break`
  - `continue`

- Function (함수)
  - `return`

- Anywhere!
  - `pass`

---
title: "[Python] Comprehension"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - list
  - set
  - dict
  - comprehension

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Comprehension

- List Comprehension

- Set Comprehension

- Dictionary Comprehension

<br>

## List Comprehension

- for 반복문을 활용한다.

- 리스트를 빠르고 간결하게 작성할 수 있다.

- 조건문을 사용가능하다.  

<br>

### Practice

```python
[i for i in range(1, 10+1)]
```
\>\>\> `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`  

```python
["string" for i in range(3)]
```
\>\>\> `['string', 'string', 'string']`  

```python
[i for i in range(1, 10+1) if i % 2 == 0]
```
\>\>\> `[2, 4, 6, 8, 10]`  

```python
[i for i in range(1, 10+1) if i % 2 == 0 or i % 7 == 0]
```
\>\>\> `[2, 4, 6, 7, 8, 10]`  
  
<br>

### Practice 2

2의 배수는 "k2", 7의 배수는 "k7" 라는 문자열로 바꿔서 가진 `b` 라는 새로운 리스트를 만들어보겠다.  

```python
b = [] # 빈 리스트 b를 선언해둔다.
for i in a :

    if i % 2 == 0:  # 2의 배수인 경우 'k2'을 리스트 b에 추가한다.
        b.append('k2')

    elif i % 7 == 0:  # 7의 배수인 경우 'k7'를 리스트 b에 추가한다.
        b.append('k7')

    else:  # 위의 조건을 제외한 모든 경우는 i 를 리스트 b에 추가한다.
        b.append(i)
print(b)
```
\>\>\> `[1, 'k2', 3, 'k2', 5, 'k2', 'k7', 'k2', 9, 'k2']`  

위의 코드를 List Comprehension 을 활용하여 변환해보겠다.

```python
['k2' if i % 2 == 0 else 'k7' if i % 7 == 0 else i for i in a]
```
\>\>\> `[1, 'k2', 3, 'k2', 5, 'k2', 'k7', 'k2', 9, 'k2']`  

이러한 조건에서의 List Comprehension 은 **삼항연산자**와 동일하다.  
다만 for 반복문이 맨뒤에 온다는 것만 유의하자.  

<br>

## 중첩 Comprehension

**Example**

```python
for i in range(1,3+1):
    for j in range(11, 13+1):
        print(i,j)
```
\>\>\> 1 11  
\>\>\> 1 12  
\>\>\> 1 13  
\>\>\> 2 11  
\>\>\> 2 12  
\>\>\> 2 13  
\>\>\> 3 11  
\>\>\> 3 12  
\>\>\> 3 13  

<br>

위와같은 이중 반복문을 아래와 같이 작성 가능하다.

```python
[print(i, j) for i in range(1, 3+1) for j in range(11, 13+1)]
```
이렇게 작성될 경우 가독성이 떨어질 수 있으므로 아래와 같이 줄바꿈을 줄 수 있다.

```python
[(i, j)
    for i in range(1, 3+1)
        for j in range(11, 13+1)]
```

<br><br>

## Set Comprehension
  
`{i for i in range(1,10 + 1)}` → `{1,2,3,4,5,6,7,8,9,10}`  

- 사용문법은 List Comprehension 과 동일하다. (대신 `{`.`}` 으로 자료를 감싼다.)  
- Set 자료형은 **unordered** → 각 요소들의 순서가 랜덤하게 리턴된다.  
- 중복값은 제외하여 리턴된다.  

<br><br>

## Dictionary Comprehension

```python
ex = {"name" : "Bruce", "class" : "A", "number" : "1", "hobby" : "Reading books"} # ex로 정의된 딕셔너리 자료형 작성
ex.items()  # ex 딕셔너리 자료형이 가지고 있는 Key, Value 값 모두 반환
```
\>\>\> `dict_items([('name', 'Bruce'), ('class', 'A'), ('number', '1'), ('hobby', 'Reading books')])`

```python
new = {val:key for key,val in ex.items()}  # Key와 Value가 서로 바뀐 new 딕셔너리 생성
new
```
\>\>\> `{'1': 'number', 'A': 'class', 'Bruce': 'name', 'Reading books': 'hobby'}`  

딕셔너리 ex 의 Key 와 Value가 반대가 되고, 순서가 랜덤하게 바뀌었다.(unordered)  
`,`가 쓰인 `key,val`는 `in` 뒤에 쓰인 값을 받는 값, `val:key`는 리턴할 값을 의미한다.  
즉 `ex.items()`의 값들을 `key,val` 이라는 리스트로 받아서 `val:key`로 리턴시켜 자료를 구성하는 것이다.  
`key`와 `val`은 다른 변수로 바꿔도 전혀 문제없다.  

```python
new = {b:a for a,b in ex.items()}
new
```
\>\>\> `{'1': 'number', 'A': 'class', 'Bruce': 'name', 'Reading books': 'hobby'}`  

만약 조금 응용해본다면 이런식으로도 바꿀 수 있다.

```python
new = {b:"example" for a,b in ex.items()}
new
```
\>\>\> `{'1': 'example','A': 'example','Bruce': 'example','Reading books': 'example'}`  

이번엔 `range()`함수를 활용해보겠다.

```python
{a : a * 10 for a in range(1, 10+1)}  # 1~10의 요소를 가진 range객체에서 변수 a로 하나씩 받아 a : a * 10 으로 리턴
```
\>\>\> `{1: 10, 2: 20, 3: 30, 4: 40, 5: 50, 6: 60, 7: 70, 8: 80, 9: 90, 10: 100}`  

range함수로 만들어진 iterable객체 요소를 순서대로 변수 `a`가 받아 Key값은 `a`로, Value 값은 `a * 10` 으로 나타난 것이다.

여기에 조건을 추가해보겠다.

```python
{a : a * 10 for a in range(1, 10+1) if a % 2 == 0}  # 2의 배수인 경우만 a : a * 10 리턴
```
\>\>\> `{2: 20, 4: 40, 6: 60, 8: 80, 10: 100}`

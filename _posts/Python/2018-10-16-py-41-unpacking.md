---
title: "[Python] Unpacking"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - unpacking
  - iterable

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Unpacking (언패킹)
**↔︎ Packing : 여러개의 값을 하나의 변수에 할당**

- Asterisk (`*`) 와 Comma (`,`) 를 통해 Iterable 데이터의 여러개의 요소를 가져오는 것

- Iterable Data 일 경우 모두 가능하다.  
ex) `List`, `Tuple`, `Dictionary`, `Set` . . .

- 함수호출시 인수(argument)에 값 전달시 활용
  - 위치인자로 Iterable 자료형을 언패킹할 경우 `*` 를 붙인다.
  - 키워드인자로 Dictionary 자료형을 언패킹할 경우 `**` 를 사용한다.

<br>

## Example

```python
data = (1, 2, 3, 4, 5,)  # packing
```

data 변수에 할당된 튜플을 다양한 방법으로 unpacking 해보자.

```python
a, b, c, d, e = data  # unpacking

print(a)
print(b)
print(c)
print(d)
print(e)
```
\>\>\> 1  
\>\>\> 2  
\>\>\> 3  
\>\>\> 4  
\>\>\> 5  

```python
a, b, *c = data

print(a)
print(b)
print(c)
```
\>\>\> 1  
\>\>\> 2  
\>\>\> [3, 4, 5]  

```python
a, *b, c = data

print(a)
print(b)
print(c)
```
\>\>\> 1  
\>\>\> [2, 3, 4]  
\>\>\> 5  

```python
a, *b, c, d = data

print(a)
print(b)
print(c)
print(d)
```
\>\>\> 1  
\>\>\> [2, 3]  
\>\>\> 4  
\>\>\> 5  

<br>

### Notice

- Asterisk (`*`) 를 적용한 변수에는 고정적으로 리스트 형태로 반환된다.  

- Dictionary 자료형의 경우에는 Key 만을 취급한다.  

<br>

## Example : For Loop

```python
fruits = (
    ('Apple', 'Red'),
    ('Banana', 'Yellow'),
    ('Melon', 'Green'),
)

for fruit, color in fruits:  # unpacking
    print(f'Name: {fruit} / Color: {color}')
```
\>\>\> Name: Apple / Color: Red  
\>\>\> Name: Banana / Color: Yellow  
\>\>\> Name: Melon / Color: Green  

<br>

## Example : Call Function

```python
def example(arg1, arg2, arg3):
    print(arg1)
    print(arg2)
    print(arg3)
```
위와 같은 함수를 정의한 뒤, 언패킹을 활용해보자.

<br>

### 위치인수 전달

```python
data = [1, 2, 3,]
```
data 변수에 리스트를 할당하였다.  
이제 언패킹을 통해 인수를 전달해보겠다.  

```python
example(*data)
```
\>\>\> 1  
\>\>\> 2  
\>\>\> 3  

위의 경우는 리스트 자료형인 data 가 언패킹(unpacking)되어 각 요소가 함수의 위치인수로 전달되었다.  
즉 아래와 같다.

```python
example(1, 2, 3)
```

<br>

### 키워드인수 전달

이번엔 Dictionary 자료형을 정의해보겠다.

```python
data_dict = {
    'arg1': 1,
    'arg2': 2,
    'arg3': 3,
}
```
정의된 `data_dict` 를 함수 인수에 언패킹하여 전달해보자.  
먼저, `*` 를 활용하여 언패킹을 시도해보겠다.

```python
example(*data_dict)
```
\>\>\> arg1  
\>\>\> arg2  
\>\>\> arg3  

위의 경우는, Asterisk(`*`) 를 하나만 사용했기 때문에 Dictionary 의 Key 만을 언패킹하여 함수의 위치인수로 전달하였다. 그 결과 Key 값들이 그대로 출력되었다. 즉, 아래와 같이 인수가 전달된 것이다.  

```python
example(arg1, arg2, arg3)
```

<br>

이번에는 `data_dict` 를 키워드인수로 전달하기 위해 `**` 를 활용해보겠다.  

```python
example(**data_dict)
```
\>\>\> 1  
\>\>\> 2  
\>\>\> 3  

위의 경우는, `**` 를 사용했기 때문에 키워드인수로 할당되었다.  
즉 아래와 같이 인수가 전달된 것이다.  

```python
examole(arg1=1, arg2=2, arg3=3)
```

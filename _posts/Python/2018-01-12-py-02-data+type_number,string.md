---
title: "[Python] Data type - Number / String"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - data type
  - number
  - complex number
  - int
  - float
  - str

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Number (숫자형)

- Integer (정수)
ex) `3`

- Floating-point (실수)
ex) `3.7`
> Floating-point 를 Integer 로 변환 : `int(3.7)` \>\>\> 3

- Complex number (복소수) : Python 에서는 허수부분을 i 가 아닌 **j** 로 표기한다.
ex) `1 + 2j`

    * 실수 부분 리턴 = `(1 + 2j).real` → `1`

    * 허수 부분 리턴 = `(1 + 2j).imag` → `2`

    * 켤레복소수 리턴 = `(1 + 2j).conjugate()` → `1 - 2j`

- Binary (2진수)
ex) `0b111` \=\= 7 (10진수) ↔ 10진수를 2진수로 변환 `bin(7)`

- Octal (8진수)
ex) `0o111` \=\= 73 (10진수) ↔ 10진수를 8진수로 변환 `oct(73)`

- Hexademical (16진수)
ex) `0x111` \=\= 273 (10진수) ↔ 10진수를 16진수로 변환 `hex(273)`

<br><br>

## 사칙연산

- `(+, -, *, /)` 더하기, 빼기, 곱하기, 나누기

- `x**y` : x의 y제곱을 나타낸다.

- `%` : 나눗셈 후 나머지를 반환하는 연산자

- `//` : 나눗셈 후 소수점 아랫자리를 버리는 연산자

<br><br>

# String (문자열)

> ordered, iterable, immutable

## 4가지 방식의 문자열

```python
"Hello World!\nNext Line!"
```

```python
'Hello World!\nNext Line!'
```

```python
"""
Hello World!
Next Line!
"""
```

```python
'''
Hello World!
Next Line!
'''
```

<br>

## 여러 방법이 존재하는 이유

- 문자열 안에 작은따옴표, 큰따옴표를 포함시키기 위해 두가지를 조합해서 사용한다.  
   `a = "This is Bruce's pen"`  
   추가적으로 \(백슬래시)를 활용하여 중복되는 따옴표를 포함시킬 수도 있다.  
   `a = 'This is Bruce\'s pen'`

<br>

- 여러줄의 문자열을 그대로 할당(이스케이프 코드 사용 X)하고 싶은 경우  
`"""String"""`, `'''String'''` 방식을 사용

```python
tmp_string = """Hello!
My name is Bruce.
Nice to meet you."""
```

<br>

## 문자열 연산

```python
a = "Hello"
b = "World"
```

변수 `a` 와 `b` 는 위와 같이 값이 할당되어 있음을 가정

<br>

### 더하기

`a + b` → `"HelloWorld"`

<br>

### 곱하기

`a * 2 ` → `"HelloHello"`

<br>

> **문자열 변환함수**  
> 
> ```python
> str(3)
> ```
> \>\>\> `'3'`

<br><br>

## 문자열 인덱싱과 슬라이싱

```python
a = 'abcd'
```

변수 `a` 는 위와 같이 할당되어 있음을 가정

<br>

### 인덱싱

- 문자열의 첫글자는 0, 그 뒤로 1, 2, 3 ... 으로 대응된다. (반대로 -1 은 마지막글자에 대응된다 )  

- `a[0]` → `"a"`

<br>

### 슬라이싱

- 인덱싱을 범위로 지정하여 보여준다.  

- `a[1:2+1]` → `"bc"`

<br>

### 문자열은 immutable 자료형

```python
a[0] = "Z"
```
\>\>\> `TypeError`  

위 코드를 보면 예상되는 결과는 `a = "Zbcd"` 이 되는 것이다.  
하지만 문자열은 immutable 자료형이기 때문에 요소값을 바꿀 수 없다.  
즉 에러가 발생한다.  
immutable 자료형의 다른 예시로는 Tuple(튜플)이 있다.

<br><br>

## 문자열 관련함수

```python
a = "hello World"
```

변수 `a` 는 위와 같이 할당되어 있음을 가정

<br>

- `a.upper()` : 소문자를 대문자로 바꾼값 반환  
`a.upper()` → `"HELLO WORLD"`

<br>

- `a.lower()` : 대문자를 소문자로 바꾼값 반환  
`a.lower()` → `"hello world"`

<br>

- `a.capitalize()` : 문자열의 첫글자를 대문자로, 나머지는 소문자로 변형한 뒤 반환  
`a.capitalize()` → `"Hello world"` 

<br>

- `a.title()` : 단어의 첫글자를 대문자로, 나머지는 소문자로 변형한 뒤 반환  
`a.title()` → `"Hello World"`

<br>

- `a.swapcase()` : 문자의 대문자를 소문자로, 소문자를 대문자로 변형한 뒤 반환  
`a.swapcase()` → `"HELLO wORLD"` 

<br>

- `a.count(value)` : value 값의 갯수를 반환  
`a.count('o')` → `2`반환

<br>

- `a.find(value)` : value 값이 처음나온 위치 반환 (value가 없을 경우 -1 반환) [대소문자 구분한다.]  
`a.find('e')` → `1`반환  
`a.find('z')` → `-1`반환

- `a.index(value)` : `find(value)` 와 동일하다.  
다른점은 value값이 없을 경우 -1이 아닌 에러반환

<br>

- `a.strip(value)` : value를 공백으로 둘 경우 양쪽 공백 제거한 값 반환   
(value값 입력시 입력된 양쪽의 중복된 값 제거)

<br>

- `a.lstrip(value)` : value를 공백으로 둘 경우 왼쪽 공백 제거 반환  
(value값 입력시 입력된 왼쪽의 중복된 값 제거)

<br>

- `a.rstrip(value)` : value를 공백으로 둘 경우 오른쪽 공백 제거 반환  
(value값 입력시 입력된 오른쪽의 중복된 값 제거)

<br>

- `a.split(value)` : value를 공백으로 둘 경우 공백(스페이스,탭,엔터 등)을 기준으로 문자열을 나누어 리스트로 반환  
`a.split()` → `['hello', 'World']`

<br>

- `a.replace(value1, value2)` : value1 의 값을 value2로 치환한 값을 반환  
`a.replace("World", "Bruce")` → `"hello Bruce"`

<br>

- `k = "!"`, `k.join(value)` : k를 정의된 value의 요소 사이마다 삽입한 값 반환  
(리스트에도 사용가능하다. 단, 리스트의 모든 요소가 문자열일 경우에만 해당)  
`k.join(a)` → `'h!e!l!l!o! !W!o!r!l!d'`

<br>

- `a.isdigit()` : 정수여부 판단 (`True` or `False` 반환)

<br>

- `a.isalpha()` : 영문자 여부 판단 (`True` or `False` 반환)

<br>

- `a.isspace()` : 공백 여부 판단 (`True` or `False` 반환)

<br>

- `a.isalnum()` : 숫자,영문자 여부 판단 (`True` or `False` 반환)

<br>

- `a.islower()` : 소문자 여부 판단 (`True` or `False` 반환)

<br>

- `a.isupper()` : 대문자 여부 판단 (`True` or `False` 반환)

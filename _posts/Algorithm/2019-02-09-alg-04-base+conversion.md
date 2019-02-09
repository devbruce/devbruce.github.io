---
title: "[Algorithm] Base Conversion"
excerpt: 진법 변환
last_modified_at: 2019-02-09

categories:
  - Algorithm

tags:
  - algorithm
  - base conversion
  - python
  - int
  - decimal
  - bin
  - oct
  - hex

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# Base Conversion (진법 변환)

## n 진법을 10 진법으로 변환

n 진법의 수를 10 진법으로 변경하는 방법을 코드로 나타나면 다음과 같다.  

```python
def convert_decimal(num, base):
    num_reversed = str(num)[::-1]
    
    result = 0
    for idx, n in enumerate(num_reversed):
        result += (base ** idx) * int(n)
    return result
```

위의 함수는 10 이상의 진법에 해당하는 수를 인식할 때 **한계점이 존재**한다.  
(각 자릿수를 한 자리 정수로만 인식한다.)  

예를 들어 12 진법의 수가 있을 때,  
해당 수는 각 자릿수가 한자리 수가 아닐 수 있다.  

- `9` : 16<sup>0</sup> 의 자릿수

- `11` : 16<sup>1</sup> 의 자릿수

- `10` : 16<sup>2</sup> 의 자릿수

`10` `11` `9` 로 표현 될 수 있다.  

> 이런 경우 한자리로 표현하기 위해 알파벳 대문자를 사용한다.  
> `10` `11` `9` 은 `A` `B` `9` 와 같이 표현될 수 있다.  

이런 경우를 대비한다면 입력되는 숫자가  
각 자릿수를 요소로 가지는 리스트로 받아서 처리한다.  

즉 다음과 같이 나타낼 수 있다.  

- `num_list[-1]` : base<sup>0</sup> 의 자릿수

- `num_list[-2]` : base<sup>1</sup> 의 자릿수

- `num_list[-3]` : base<sup>2</sup> 의 자릿수

- . . .

즉 위의 `10` `11` `9` 는 `[10, 11, 9]` 의 형식으로 데이터를 입력받는다.  
이제 해당 리스트를 10 진법으로 표현할 수 있도록 함수를 수정한다.  

```python
def convert_decimal(num_list, base):
    num_reversed = num_list[::-1]
    
    result = 0
    for idx, n in enumerate(num_reversed):
        result += (base ** idx) * n
    return result
```

```python
convert_decimal([10, 11, 9], base=12)
```
\>\>\> `1581`

<br>

### Python int Function

Python 의 기본 함수인 `int` 를 통해서 쉽게 가능하다.  

```python
int(num, base=n)
```

10 보다 큰 진법의 경우  
10 이상의 수를 알파벳 대문자로 인식한다.  
이 때 n 은 `2 <= n <= 36` 일 경우에만 가능하다.  

<br>

| N     | 10 | 11 | 12 | . . . | 33 | 34 | 35 |
|-------|----|----|----|-------|----|----|----|
| Value | A  | B  | C  | . . . | X  | Y  | Z  |

<br>

#### Example

```python
int('Z', base=36)
```
\>\>\> `35`  

```python
int('AB9', base=12)
```
\>\>\> `1581`  

<br><br>

## 10 진법을 n 진법으로 변환

10진법의 수 n 을 k 진법의 수로 변경하기 위해서는  
n 을 k 로 나누고 그 때의 나머지를 임의의 Iterable 변수에 하나의 요소로 추가한다.  

n 을 k 로 나누었을 때의 몫이 0 이 될 때까지 위 작업을 반복한 뒤  
나머지가 저장되어있던 Iterable 변수의 요소들을 역순으로 출력하면 k 진법으로 변환된 수가 출력된다.  

- `divmod(x, y)` 는 튜플 `(x//y, x%y)` 를 반환

<br>

```python
# num is decimal
def convert_base(num, base):
    result = ''
    while num != 0:
        num, rmd = divmod(num, base)
        if rmd > 9:
            result += chr(55+rmd)
        else:
            result += str(rmd)
    return result[::-1] or '0'
```

위의 코드도 대문자 알파벳 `Z` 를 넘어선 표현 (36 보다 큰 진법)을 하기에는 **한계점이 존재**한다.  

<br>

만약 결과값이 대문자 알파벳을 사용한 한자리가 아닌  
리스트로 구성된 (제한없는 표현) 을 사용하고자 한다면 다음과 같이 나타낼 수 있다.  

```python
# num is decimal
def convert_base(num, base):
    result = list()
    while num != 0:
        num, rmd = divmod(num, base)
        result.append(rmd)
    return result[::-1] or [0]
```

<br>

### 2 진법, 8 진법, 16 진법  

Python 에서는  
10 진수 n 을 2 진법, 8 진법, 16 진법으로 변경하는 함수를 제공한다.  

- 2 진법 : `bin(n)` : : 앞에 `0b` 가 붙은 문자열 반환

- 8 진법 : `oct(n)` : 앞에 `0o` 가 붙은 문자열 반환

- 16 진법 : `hex(n)` : 앞에 `0x` 가 붙은 문자열 반환

<br>

#### Example

```python
bin(123)
```
\>\>\> `'0b1111011'`

```python
oct(123)
```
\>\>\> `'0o173'`

```python
hex(123)
```
\>\>\> `'0x7b'`

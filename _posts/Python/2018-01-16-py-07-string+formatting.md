---
title: "[Python] String Formatting"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - string formatting
  - format

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# String Formatting (문자열 포매팅)

문자열 내의 특정값을 쉽게 변환가능하도록 해준다.  

- `%`
- `.format()`
- `f''`

<br><br>

## %

`"I have 2 red pens and 3 blue pens"`  
라는 문자열에서 정수부분인 2와 3을 변환가능하도록 해보겠다.

`"I have %d red pens and %d blue pens" % (2,3)`  
→ `"I have 2 red pens and 3 blue pens"`

| Code | Feature                |
|----|--------------------------|
| %d | Integer                  |
| %f | Floating Point           |
| %o | Octal Number             |
| %x | Hexadecimal              |
| %c | Character                |
| %s | String                   |

> `%` 자체를 나타내고 싶다면 `%%`

<br>

### Sorting

| Code    | Feature                               |
|---------|---------------------------------------|
| %15s    | 15칸 공간, 오른쪽 정렬                    |
| %-15s   | 15칸 공간, 왼쪽 정렬                      |
| %0.3f   | 소수점 3자리까지 나타내기                   |
| %15.3f  | 총 15칸, 소수점 3자리, 오른쪽 정렬           |
| %-15.3f | 총 15칸, 소수점 3자리, 왼쪽정렬             |

<br><br>

## .format()

- 기본예시
`"I have {} red pens and {} blue pens".format(2,3)`  
→ `"I have 2 red pens and 3 blue pens"`

<br>

- 인덱스(순서) 지정 가능 ( 0부터 시작 )  
`"I have {1} red pens and {0} blue pens".format(2,3)`  
→  `"I have 3 red pens and 2 blue pens"`

<br>

- 이름 지정 가능 (네이밍)  
`"I have {first} red pens and {second} blue pens".format(second=3, first=2)`  
→ `"I have 2 red pens and 3 blue pens"`  

<br>

### Sorting

| Code   | Feature                                  |
|--------|-----------------------------------------|
| :k>15  | 15칸 공간, 오른쪽 정렬, 공백 k로 채우기 |
| :k<15  | 15칸 공간, 왼쪽 정렬, 공백 k로 채우기   |
| :k^15  | 15칸 공간, 가운데정렬, 공백 k로 채우기  |
| :0.3f  | 소수점 3자리                            |
| :15.3f | 총 15칸, 소수점 3자리                   |

> `:k>n`  
**`:` : 네이밍 또는 인덱스 뒤 필수요소  
`k` : 공백에 채울문자 (k를 생략할 경우 공백으로 채운다.)  
`>` : 오른쪽정렬<br>
`<` : 왼쪽정렬<br>
`n` : 총 공간 수**  

<br>

중괄호(brace)`{}` 를 포함한 문자열 자체를 보여주고 싶다면 중괄호(brace)를 두번쓴다.

<br><br>

## f''

```python
a = 1
b = 2

print(f'First number is {a}, second number is {b}')
```
\>\>\> `'First number is 1, second number is 2'`

<br>

### Sorting

`.format()` 과 동일하다.

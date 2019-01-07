---
title: "[Python] Data type - List / Tuple"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - Python

tags:
  - python
  - data type
  - list
  - tuple

---

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)

# List (리스트)

> ordered, iterable

ex) `a = [1,2,3,4]`

<br>

## 빈 리스트 생성

`a = []` or `a = list()`

> ```python
> list("ab c d")  # Iterable 자료형만 가능
> ```
> \>\>\> `['a', 'b', ' ', 'c', ' ', 'd']`

<br>

## 더하기

- `+`

- `extend()` 메서드와 동일

```python
a = [1,2,3,4]
b = [1,2,3,4]
print(a + b)
```
\>\>\> `[1,2,3,4,1,2,3,4]`

<br>

## 곱하기

- `*`

```python
a = [1,2,3,4]
print(a * 3)
```
\>\>\> `[1,2,3,4,1,2,3,4,1,2,3,4]`

<br>

## 인덱싱

```python
a = [1,2,3,4]
print(a[0])  # Result : 1
print(a[-1])  # Result : 4
```
\>\>\> `1`  
\>\>\> `4`  

> 인덱스가 음수의 경우 `-0`이 아닌 `-1`부터 시작이다.

<br>

## 슬라이싱

- `[Start:End+1:Interval]`

```python
a = [1,2,3,4]
print(a[0:2])
print(a[::2])
```
\>\>\> `[1,2,3]`  
\>\>\> `[1,3]`    

<br><br>

## Notice

인덱싱과 슬라이싱을 활용하여 리스트를 수정시 주의해야할 점  

- `a[n] != a[n:n+1]` 


```python
a = [1,2,3]
a[1] = ['x','y','z']  # a[1] 요소를 ['x','y','z'] 이 자체로 변경
print(a)
```
\>\>\> `[1,['x','y','z'],3]`

```python
a = [1,2,3]
a[1:1+1] = ['x','y','z']
print(a)
```
\>\>\> `[1,'x','y','z',3]`  

위 코드의 경우에는 `a[1]` 부터 `a[2]`까지의 요소를 의미하는데, `a[2]`는 포함되지 않는다.  
즉 `a[1]`과 `a[2]` 사이의 리스트를 `['x','y','z']` 로 바꾼다는 의미가 되므로(범위)  
`a`의 결과값은 `[1,'x','y','z',3]` 가 되어진다.  
이 개념은 리스트의 요소를 제거할 때도 응용될 수 있다.

<br><br>

## 관련 메서드

`a = [1,2,3,4]` 으로 정의를 해두겠다.

<br>

- `a.append(value)` : 맨 뒤에 value 추가
`a.append(7)` → `a = [1,2,3,4,7]`

<br>

- `a.insert(index, value)` : index 자리에 value 삽입  
`a.insert(1,7)` → `a = [1,7,2,3,4]`  
> insert 메서드의 index 가 초과할 경우 마지막 인덱스에,  
> 음수를 줄 경우 첫번째 인덱스에 추가된다. (오류를 반환하지않는다.)

<br>

- `a.extend(iterable data)` : iterable 자료형을 더한다.  
`a.extend(range(7,9+1))` → `a = [1,2,3,4,7,8,9]`  

<br>

- `a.sort()` : 순서대로 정렬
> reverse = True 를 인자로 전달할 경우 반대로 정렬  
> key = f(x)를 전달할 경우 함수 f 를 기준으로 정렬한다.  

<br>

- `sorted(a)` : 순서대로 정렬시킨 a 반환 (Python 함수)
> reverse = True 를 인자로 전달할 경우 반대로 정렬  
> key = f(x)를 전달할 경우 함수 f 를 기준으로 정렬한다.

<br>

- `a.reverse()` : 리스트를 역순으로 뒤집기

<br>

- `a.index(value)` : 위치값 반환  
`a.index(3)` → `2` 반환

<br>

- `a.count(value)` : value 값 갯수 반환  
`a.count(3)` → `1` 반환

<br>

- `(String).join(list)` : 리스트에 String 을 요소마다 삽입하며 문자열로 변형시킨다.  
(리스트의 요소가 모두 문자열인 경우에만 가능하다)  
`b = ['a', 'b', 'c', 'd']`  
`'*'.join(b)` → `'a*b*c*d'`

<br>

- `a.copy()` : 리스트 `a` 를 복사하여 반환
> 내용을 카피하는 것이다. 해당 데이터를 할당받을 경우  
> 참조하는 메모리 주소는 다르다.

<br>

- `a.clear()` : 리스트 `a`를 빈 리스트로 만든다.

<br><br>

### 리스트 요소 삭제

> `a = [1,2,3,4,3]` 으로 정의를 해두겠다.

#### 슬라이싱 활용

`a[1:1+1] = []` → `a = [1,3,4,3]`

<br>

#### del 활용
  
`del a[2]` → `a = [1,2,4,3]`

<br>

#### remove 활용
  
`a.remove(3)` → `a = [1,2,4,3]`

<br>

#### pop 활용

`a.pop()` : 맨 끝값 제거 후 반환  
`a.pop()` → `a = [1,2,3,4]` , `3`반환  
인자로 index를 전달할 경우, 해당 index에 해당하는 값을 제거 후 반환

<br><br>

##### `del` 과 `remove` 차이점

- `del` : `del a[4]` 은 `a`의 4번 자리에 위치한 `3`에  곧바로 접근하여 제거한다.  

- `remove()` : 값에 해당하는 요소중에서 **가장 빠른 번호**에 위치한 요소부터 제거한다.

<br>

리스트 `a` 에는 `a[2]` 와 `a[4]` 에 `3`이라는 동일한 데이터가 저장되어있다.  
`a.remove(3)` 을 할 경우, `a[2]`가 낮은 인덱스를 가지고 있기 때문에 `a[2]`만 제거된다.  
즉 `a = [1,2,4,3]` 이라는 결과가 나오게 된다.(`a[4]`는 살아있다.)

<br>

Q: `a.remove(a[4])` 를 하면 `a[4]`를 곧바로 제거할 수 있지 않을까?    

결론부터 말하자면 **`a.remove(3)`와 같은 결과**가 나온다.  
`a.remove(a[4])`에서 `a[4]` == `3` 으로 인식되어 결과적으로 `a.remove(3)` 이 되기 때문이다.

<br><br>

# Tuple (튜플)

> ordered, interable, immutable

ex) `a = (1,2,3,4)`

튜플은 리스트와 매우 유사하다.

> 연산, 인덱싱, 슬라이싱 리스트와 동일하다.

<br><br>

## List 와의 차이점

- 정의된 값을 변경 및 삭제할 수 없다.
> 시도할 경우 에러가 발생한다.

- 괄호를 생략해도 된다.
  - List Comprehension 에서는 괄호 생략 주의

- 안정성, 메모리 효율성

<br><br>

# List(리스트) ↔ Tuple(튜플) 변환

- List → Tuple
`a = [1,2,3,4]`  
`tuple(a)` → `(1,2,3,4)`

<br>

- Tuple → List
`a = (1,2,3,4)`  
`list(a)` → `[1,2,3,4]`

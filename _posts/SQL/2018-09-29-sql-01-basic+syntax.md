---
title: "[SQL] Basic Syntax"
excerpt: 
last_modified_at: 2019-04-13

categories:
  - SQL

tags:
  - SQL
  - select
  - union
  - distinct
  - where
  - between
  - in
  - like
  - insert
  - null
  - update
  - delete
  - order by
  - asc
  - desc

---

# Basic Syntax of SQL

SQL: **Structured Query Language**

- 대소문자 구분 X

- 구문 끝에 `;` (Semicolon)을 붙여준다.  
> 여러개의 SQL 구문으로 서버에 동시에 요청할 때 각 구문을 분리하기 위해서 필요

<br><br>

## SELECT

**Column(열) 선택**

```sql
SELECT <ColName>, <ColName>
FROM <TableName>;
```

`FROM` 뒤에 명시한 `Table`에서 `SELECT` 뒤에 입력한 Column(열) 데이터를 가져온다. `<TableName.ColName>` 으로 Column 을 구체적으로 명시할 수도 있다.

```sql
SELECT * FROM <TableName>;
```

`Table`에서 모든 Column(열) 데이터를 가져온다.  

<br>

### Union

**Column(열)** 데이터를 합쳐준다.

```sql
SELECT col1, col2 FROM Table1
UNION
SELECT col3, col4 FROM Table2;
```

`Table1` 의 `col1`, `Table2` 의 `col3` 를 합쳐서 `col1` 이라는 이름을 가진 Column(열) 데이터와  
`Table1` 의 `col2` 과 `Table2` 의 `col4` 을 합쳐서 `col2` 이라는 이름을 가진 Column(열) 데이터를 가져온다.


- `UNION` 은 default 로 중복값을 제거한다.    
중복값 제거를 원치 않을 경우 `UNION ALL` 로 코드를 작성한다.
 
- 결과의 Column(열) 이름은 첫번째 `SELECT` 구문으로 선택된 Column(열) 이름으로 설정된다.

- `SELECT`, `UNION` 으로 선택된 Column(열)은 유사한 데이터 타입이어야한다.

<br>

### AS (Aliase)

**임시로 열의 이름을 새로 할당해주는 명령어**

```sql
SELECT <ColName> AS <NewName>, <ColName> AS <NewName>, ...
FROM <TableName>
WHERE <Condition>;
```
`Table1` 에서 `condition1`의 조건에 해당하는 `col1`과 `col2`를 가져와서  
`col1` 에는 `new_col1` 을, `col2` 에는 `new_col2` 라는 이름을 임시로 할당한다.

<br>

### SUM / AVG / MAX / MIN / COUNT

- SUM : Column(열) 데이터를 모두 더한값을 반환

- AVG : Column(열) 데이터의 평균 반환

- MAX : Column(열) 데이터의 최댓값 반환

- MIN : Column(열) 데이터의 최솟값 반환

- COUNT : Column(열) 데이터의 갯수 반환

<br>

```sql
SELECT FUNCTION(col1) AS Result
FROM Table1
WHERE condition1;
```
> `FUNCTION` 에는 SUM / AVG / MAX / MIN / COUNT 가 대입된다.
  
`Table1` 에서 `condition1` 의 조건에 해당하는 `col1` 을 가져와서 `FUNCTION`에 의해 연산되고, `Result` 라는 임시 Column(열) 이름을 가진 데이터로 반환된다.

<br>

### DISTINCT

**중복값 제거**  
Column(열) 데이터를 **중복값 없이** 가져온다.

```sql
SELECT DISTINCT <ColName>
FROM <TableName>;
```

<br><br>

## WHERE

**조건문**

```sql
SELECT <ColName>, <ColName>,...
FROM <TableName>
WHERE <Condition>;
```

- WHERE 조건문에는 `NOT`, `AND`, `OR` 등을 사용가능

<br>

### Comparison Operator (비교연산자)

- `=`, `>`, `>=`, `<`, `<=`

- `<>` or `!=` (Not Equal)

<br>

### BETWEEN A AND B

```sql
SELECT col3
FROM Table1
WHERE Age BETWEEN 25 AND 30;
```

`Table1` 에서 `Age` 가 25이상 30이하인 경우, `col3` 을 가져온다.  

- `<A>` 와 `<B>` 에는 숫자, 날짜, 텍스트 등이 들어갈 수 있다.

- `<A>`, `<B>` 자기자신도 범위에 포함된다.

<br>

### IN (val1, val2 ...)

`val1, val2...` 에 해당하는 값들을 필터링해준다.

```sql
SELECT col2
FROM Table1
WHERE Major IN (Computer Science, Business Administration);
```

`Table1` 에서 `Major`가 `Computer Science` 또는 `Business Administartion` 인 경우,  
`col2`만 가져온다.

<br>

### EXISTS (Sub Query)

**서브쿼리(Sub Query) 요청에 해당하는 레코드가 존재하면 True, 아니면 False**

```sql
SELECT col1
FROM Table1
WHERE EXISTS
(SELECT * From Table2 WHERE id = Table1.id);
```

`Table2` 의 `id` 값과 `Table1`의 `id` 값이 같은 경우의 데이터를 요청해서 레코드가 존재한다면  
서브쿼리는 `True` 를 반환하고, 이에따라 메인쿼리는 `Table1` 에서 `col1` 을 가져온다.

<br>

### LIKE 'Pattern'

패턴에 해당하는 문자를 필터링해준다.(정규표현식과 유사)

```sql
SELECT *
FROM Table1
WHERE Name Like '__u%';
```

`Table1` 에서 `Name` 이 3번째 글자가 `u` 인 경우, 모든 Column(열)을 가져온다.

<br>

#### Wildcard  

- `%` : 0개 이상의 모든 글자

- `_` : 글자 한개


<br><br>

## INSERT INTO ... VALUES ...

**Row(행) 추가**

```sql
INSERT INTO <TableName> (<ColName>, <ColName>, <ColName>, ...)
VALUES (<Value>, <Value>, <Value>, ...);
```

- Column(열) 데이터를 지정해주지 않은 값은 `null` 로 삽입된다.

<br>

### NULL

- 값이 비어있다.

- 비교연산자 사용불가

- `IS NULL` 또는 `IS NOT NULL` 을 통해 조건문으로 사용가능

<br>

#### Example

```sql
SELECT col1
FROM Table1
WHERE col3 IS NULL;
```

`Table1`에서 `col3`이 `null`값인 경우, `col1` 을 가져온다.

<br><br>

## DELETE FROM ... WHERE ...

**조건에 해당하는 Row값을 제거**

```sql
DELETE FROM <TableName>
WHERE <Condition>;
```

<br>

```sql
DELETE FROM Table1;
```

- 위와같이 WHERE (조건문) 없이 사용시 모든 Row가 제거될 수 있으므로 주의  
  - 테이블의 구조 및 속성 등은 그대로 남는다. (테이블을 제거하는 것이 아니기 때문)
  - `DELETE * FROM Table1` 과 동일

<br><br>

## UPDATE ... SET ... WHERE ...

**데이터 수정**

```sql
UPDATE <TableName>
SET <ColumnName> = <Value>, <ColumnName> = <Value>, ...
WHERE <Condition>;
```

- WHERE (조건문) 없이 사용하면 해당열의 모든값이 변경되므로 주의해야한다.

<br><br>

## ORDER BY ...

**오름차순, 내림차순 정렬**

```sql
SELECT col3
FROM Table1
ORDER BY col1 DESC, col2 ASC;
```

`col1`을 내림차순 정렬한 뒤, `col2` 오름차순으로 정렬한 순서로  
`col3`을 가져온다. 

- `ASC | DESC` 부분을 생략할시 Default는 `ASC`

<br><br>

# Reference

- [w3schools.com](https://www.w3schools.com/sql/default.asp)

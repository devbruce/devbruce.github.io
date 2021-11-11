---
title: "[HTML&CSS] Emmet"
excerpt: 
last_modified_at: 2019-01-04

categories:
  - webdev

tags:
  - web
  - html
  - css
  - emmet
  - plugin

---

# Emmet

**the essential toolkit for web-developers**  

- HTML, XML 등의 코드를 빠르게 작성, 편집할 수 있도록 자동완성 기능을 제공

- 보편적으로 Emmet 구문을 문법에 맞게 작성한 뒤 `TAB`을 통해 자동완성한다.  
> - 보편적으로 Default 는 `TAB` 이며 필요시 변경가능  
> - [Emmet-vim](https://github.com/mattn/emmet-vim) 의 경우 Default 가 `Ctrl` `y` `,` 이다.

<br><br>

## Abbreviation (HTML)

- `html:5` 또는 `!`  을 통해 **HTML Basic Architecture** 를 쉽게 작성 가능하다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```

- ID: `#`  

- Class: `.`  

- Grouping: `()`  

- Multiplication: `*`  

- Sibling: `+`  

- Child: `>`  

- Climb-up: `^` (사용한 갯수만큼 상위 태그로 이동)  

- Text: `{}`  

<br>

- Custom attributes: `[]`
  - ex) `[attr=val attr2=val2]`  (예외적 space 허용)

<br>

- Item numbering: `$` (with Multiplication`*`)  
  - `$$$` : 3자리수 (001, 002 ...), `n`개 사용시 `n`자리수 
  - `$@n` : `n`부터 시작해서 증가 (Default: 1부터 증가)
  - `$@-` : 감소 (마지막 값이 1)
  - `$@-n` : 감소, 마지막 숫자 == `n`

<br><br>

### Syntax Example

`ul[attr=1 attr2="custom"]#example>p#ex-id+li.$$*5^(ol>p.ex-cls+li{example}*3)*2`

<br>

**Result**

```html
<ul attr="1" attr2="custom" id="example">
    <p id="ex-id"></p>
    <li class="01"></li>
    <li class="02"></li>
    <li class="03"></li>
    <li class="04"></li>
    <li class="05"></li>
</ul>
<ol>
    <p class="ex-cls"></p>
    <li>example</li>
    <li>example</li>
    <li>example</li>
</ol>
<ol>
    <p class="ex-cls"></p>
    <li>example</li>
    <li>example</li>
    <li>example</li>
</ol>
```

<br><br>

## Abbreviation (CSS)

### Numeric Value Unit

- Default (단위 미지정) : `px`  
  - 단위가 없는 속성(Unit-less properties)의 경우는 value 그대로 입력된다.  
  - float일 경우 default 단위는 `em`이 된다.

- `p` : `%`

- `e` : `em`

- `x` : `ex`

<br>

### Color Value

- `#7` : `#777777`

- `#a0` : `#a0a0a0`

- `#abc` : `#aabbcc`

<br>

### Abbreviation

- `w` : `width: ;`

- `h` : `height: ;`

- `d` : `display: block;`

- `m` : `margin: ;`  
  - `m10` : `margin: 10px;`  
  - `m10-20` : `margin: 10px 20px;`  
  - `m10--20` : `margin: 10px -20px;`  
  - `m10p-20x` : `margin: 10% -20ex;`  
  (단위를 명시할 경우 `-` 두번쓰지 않아도 된다.)

- `p` : `padding ;`  

- `fz` : `font-size: ;`  

- `fw` : `font-weight: normal;`
  - `fw10` : `font-weight: 10;` (Unit-less properties)

- `lh` : `line-height: ;` (Unit-less properties)

- `c` : `color: #000;`

- `bg` : `background: #000;`

- `bgc` : `background-color: #fff;`
  - `bgc#7` : `background-color: #777777;`

- `bd` : `border: 1px solid #000;`
  - `bd5#7s` : `border: 5px #777777 solid;`

- **. . .**

<br>

### Operator \+

ex) `m10+p10` 의 결과값은 아래와 같다.  

```
margin: 10px;
padding: 10px;
```

<br>

### !important modifier : \!

`!` : **마지막**에 `!` 를 붙이게 되면 `!important` 가 붙게된다.  
> `m10!` : `margin: 10px !important;`

<br><br>

## Notice  

가독성을 위해 요소와 연산자 사이에 공백(space)를 삽입하길 원할 수 있다.  
그러나 emmet 에서는 이를 허용하지 않는다.  

HTML custom attribute 을 작성하는 경우는 예외적으로 허용된다.  
ex) `[space="available" between="attr"]`

<br><br>

## Reference

- [Emmet](https://emmet.io/)  

- [Emmet Documentation - Abbreviation (Syntax)](https://docs.emmet.io/abbreviations/syntax/)  

- [Emmet Documentation - CSS Abbreviations](https://docs.emmet.io/css-abbreviations/)

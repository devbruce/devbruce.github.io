---
title: "[HTML&CSS] CSS #2 Summary"
excerpt: 
last_modified_at: 2020-08-17

categories:
  - webdev

tags:
  - web
  - css
  - display
  - postiion

---

# CSS \#2 Summary

> - [드림코딩 by 엘리](https://www.youtube.com/channel/UC_4u-bXaba7yrRz_6x6kb_w): [CSS 레이아웃 정리 display, position 완성 | 프론트엔드 개발자 입문편: HTML, CSS, Javascript](https://www.youtube.com/watch?v=jWh3IbgMUPI&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=8)  
> <br><br>**Caution**<br><br>
> 강의를 듣고 제가 기억하기 쉽게 기록하여 변형된 내용이 있을 수 있습니다.  
> 오류가 있거나 해당 글에 문제가 있을 경우 피드백 부탁드립니다.  

<br><br>

## display

```css
.tmp {
    display: block;
    width: 30px;
    height: 30px;
    color: green;
    background: yellow;
    . . .
}
```

- `inline`: 한 줄에 여러개 배치가능 (contents 가 있어야만 지정한 속성값이 가시화된다.)  
(해당값을 default 로 가지는 대표적인 tag 는 `span` 이다.)

<br>

- `block`: 한 줄에 한개만 배치 가능  
(해당값을 default 로 가지는 대표적인 tag 는 `div` 이다.)

<br>

- `inline-block`: 한 줄에 여러개 배치가 가능한 `block`

<br>

- `flex`: 해당 tag 를 flexbox 로 지정

<br><br>

## position

```css
.tmp {
    position: static;
    left: 20px;
    top: 20px;
    . . .
}
```

- `static` **(Default)**: HTML 에 기재된 순서대로 browser 상에 자연스럽게 보이도록 하는 속성  
위치속성(ex. `left`, `top` . . .)이 적용되지 않는다.

<br>

- `sticky`:  원래 있어야할 자리에 위치하지만 (`position: static;`), **스크롤을 무시하고 위치가 고정**된다.  
위치속성 (ex. `left`, `top` . . .)이 적용되지 않는다.

<br>

- `relative`: 원래 있어야할 자리에 위치 (`position: static;`) 기준으로 위치 속성값 적용

<br>

- `absolute`: position 값이 `static` 이 아닌 (`relative`, `absolute`, `fixed`)  
가장 근접한 parent box 기준으로 위치 속성값 적용

<br>

- `fixed`: Web Page 전체를 기준으로 위치 속성값 적용

<br><br>

## CSS 속성값 브라우저 호환 여부

<https://caniuse.com/>

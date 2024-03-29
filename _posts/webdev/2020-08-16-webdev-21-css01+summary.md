---
title: "[HTML&CSS] CSS #1 Summary"
excerpt: 
last_modified_at: 2020-08-16

categories:
  - webdev

tags:
  - web
  - css

---

# CSS \#1 Summary

> - [드림코딩 by 엘리](https://www.youtube.com/channel/UC_4u-bXaba7yrRz_6x6kb_w): [CSS 셀렉터, 기초 이론 정리. 포트폴리오 만드는 날까지! | 프론트엔드 개발자 입문편: HTML, CSS, Javascript](https://www.youtube.com/watch?v=gGebK7lWnCk&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=7)
> <br><br>**Caution**<br><br>
> 강의를 듣고 제가 기억하기 쉽게 기록하여 변형된 내용이 있을 수 있습니다.  
> 오류가 있거나 해당 글에 문제가 있을 경우 피드백 부탁드립니다.  

<br><br>

## Cascading 의미

정의된 세부적인 정의가 있다면 그것을 쓰고,  
없다면 다음으로 지정된 기본값으로 넘어가는 것을 의미

<br>

1. Author Style (직접 작성한 Style)
2. User Style (Dark mode, Font size, . . .)
3. Browser (Browser default style)

<br><br>

## CSS Selector

- CSS Selector 는 구체적일수록 priority 가 높다.
- `!important` 는 cascading 의 연결고리를 끊어 가장 높은 priority 를 가지게 한다. (사용권장 X)

<br>

- Universal: `*`
- type: `tag_name`
- ID: `#` + id_name
- Class: `.` + class_name
- State `:`: ex) `button:hover`
- Attribute `[]`

<br><br>

### Attribute Using Examples

- `a` 태그 중 `href` attribute 가 있는 elements select

```css
a[href] {
    color: green;
}
```

<br>

- `a` 태그 중 `href` 값이 `google.com` 인 elements select

```css
a[href="google.com"] {
    color: green;
}
```

<br>

- `a` 태그 중 `href` 값이 `google.com` 으로 시작하는 elements select

```css
a[href^="google.com"] {
    color: green;
}
```

<br>

- `a` 태그 중 `href` 값이 `google.com` 으로 끝나는 elements select

```css
a[href$=".com"] {
    color: green;
}
```

<br><br>

## padding, margin, border

### padding, margin

- `padding` 은 top 에서부터 시계방향으로 한 줄로 할당가능

```css
* {
    padding: 1px 2px 3px 4px;
}
```

> 위의 코드는 아래 코드와 동일

```css
* {
	padding-top: 1px
	padding-right: 2px;
	padding-bottom: 3px;
	padding-left: 4px;
}
```

<br>

- 2개의 값을 한번에 할당할 경우 (top, bottom), (left, right) 값을 주게 된다.

```css
* {
    padding: 1px 2px;
}
```

> 위의 코드는 아래 코드와 동일

```css
* {
	padding-top: 1px
	padding-right: 2px;
	padding-bottom: 1px;
	padding-left: 2px;
}
```

<br>

- `margin` 도 `padding` 과 동일한 형태로 값을 할당할 수 있다.

<br><br>

### border

```css
* {
	border: 2px solid blue;
}
```

> 위의 코드는 아래 코드와 동일

```css
* {
	border-width: 2px;
	border-style: solid;
	border-color: blue;
}
```

<br><br>

## CSS Selector Practice

**<https://flukeout.github.io/>**

---
title: "[HTML&CSS] CSS Units"
excerpt: 
last_modified_at: 2020-08-27

categories:
  - webdev

tags:
  - css
  - unit
  - px
  - em
  - rem
  - viewport

---

# CSS Units

> - [드림코딩 by 엘리](https://www.youtube.com/channel/UC_4u-bXaba7yrRz_6x6kb_w): [프론트엔드 필수 반응형 CSS 단위 총정리 (EM과 REM) | Responsive CSS Units](https://www.youtube.com/watch?v=7Z3t1OWOpHo)  
> <br><br>**Caution**<br><br>
> 강의를 듣고 제가 기억하기 쉽게 기록하여 변형된 내용이 있을 수 있습니다.  
> 오류가 있거나 해당 글에 문제가 있을 경우 피드백 부탁드립니다.

<br>

```css
html {
    font-size: 100%;  /* Browser 에서 지정된 font-size 를 따라감 */
}
```

- `font-size` 는 브라우저의 default 값이 보통 16px 이다.

- `font-size` 를 100% 가 아닌 Absolute Unit 으로 주게될 경우  
브라우저의 `font-size` 를 변경해도 반응형으로 변하지 않는다.

<br><br>

## Absolute length units

- `px`: 모니터 위에서 화면에 나타낼 수 있는 가장 작은 단위

<br><br>

## Relative length units

- `%`: parent related

- `vw`, `vh`, `vmin`, `vmax`: viewport related (parent element 무시)

- `vmin`: ex) `50vmin` \-\-\> 브라우저의 width 와 height 중 작은 값의 50%

- `ex`: 지정된 font-family 의 소문자 x 의 height 를 나타낸다. (잘안씀)

- `ch`: 지정된 font-family 의 숫자 0 의 width 를 나타낸다. (잘안씀)

- `lh`: line height (아직 브라우저에서 지원하지 않음) 

- **`em`, `rem`**

<br>

### em

- `font-size` 를 나타내는 단위

- 1em \=\= 16px (16px \=\= Browser default font-size)

- 부모의 `font-size` 에 상대적으로 계산된다.

```css
.parent {
    font-size: 8em;  /*  16px * 8 = 128px */
}

.child {
    font-size: 0.5em;  /*  128px * 0.5 = 64px */
}
```

`em` 으로 표현한 위의 코드는 아래와 같이 `%` 로 표현이 가능하다.

```css
.parent {
    font-size: 800%;
}

.child {
    font-size: 50%;
}
```

<br><br>

### rem

- `rem`: r 은 root 를 나타낸다. relative to **root** element.  
`em`: relative to parent element  

```css
.parent {
    font-size: 8rem;  /*  16px * 8 = 128px */
}

.child {
    font-size: 0.5rem;  /*  16px * 0.5 = 8px */
}
```

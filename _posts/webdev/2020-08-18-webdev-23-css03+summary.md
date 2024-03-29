---
title: "[HTML&CSS] CSS #3 Summary"
excerpt: 
last_modified_at: 2020-08-18

categories:
  - webdev

tags:
  - web
  - css
  - display
  - postiion
  - viewport
  - flexbox

---

# CSS \#3 Summary

> - [드림코딩 by 엘리](https://www.youtube.com/channel/UC_4u-bXaba7yrRz_6x6kb_w): [CSS Flexbox 완전 정리. 포트폴리오 만드는 날까지! | 프론트엔드 개발자 입문편: HTML, CSS, Javascript](https://www.youtube.com/watch?v=7neASrWEFEM&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=9)  
> <br><br>**Caution**<br><br>
> 강의를 듣고 제가 기억하기 쉽게 기록하여 변형된 내용이 있을 수 있습니다.  
> 오류가 있거나 해당 글에 문제가 있을 경우 피드백 부탁드립니다.  

<br><br>

## Intro (float Attribute)

- `float`: Image 와 Text 배치를 설정하기 위해 나타났다.  
ex) `float: left;`: Image 가 왼쪽에 위치하고, 텍스트가 감싸면서 배치된다.  

<br>

과거 CSS 에는 Layout 기능이 많이 없어서 box 를 `float` 속성을 통해 배치하였다.  
현재는 `flexbox` 가 등장하여 본래의 목적에 맞게 사용된다.

<br><br>

## 100% vs 100vh

> vh: Viewport Height

- 100%: Parent Box 를 기준으로 비율값 지정

```css
body, html {
    height: 100%;
}

.container {
    background: beige;
    height:100%;
}
```

<br>

- 100vh: Viewport Height 기준으로 비율값 지정 (Parent Box 와 상관 X)

```css
.container {
    background: beige;
    height: 100vh;
}
```

<br><br>

## flexbox

```css
.container {
    background: beige;
    height: 100vh;
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
}
```

-  box 에 적용되는 속성값,  각각의 item 에 적용될 속성값이 따로 존재한다.

- 위의 예시 코드와 같이 `display: flex;` 를 통해 설정가능

- container 에만 `display: flex;` 적용 (item 에는 `display` 값 적용필요 없음)

<br>

- 중심 축 (main axis) 와 반대 축 (cross axis) 가 존재한다.  
수평을 중심 축 (main axis) 으로 지정한다면, 반대 축은 수직 축 (cross axis) 이 된다.
  - 중심 축 (main axis) 수직 \=\= 위에서 아래로 정렬
  - `flex-direction`: `row` \-\-\> 중심 축은 수평 축이 된다.

<br><br>

### Container (Box) Attributes

#### flex-direction

item 순서와 배치를 설정한다.
    
- `row` (Left \-\-\> Right)  
**(왼쪽에 붙어서 배치되며 item 순서는 그대로 유지)**  

<br>

- `row-reverse` (Right \-\-\> Left)  
**(오른쪽에 붙어서 배치되며 item 순서도 반대로 변환)**

<br>

- `column` (top \-\-\> bottom)  
**(위에 붙어서 배치되며 item 순서는 그대로 유지)**

<br>

- `column-reverse` (bottom \-\-\> top)  
**(아래에 붙어서 배치되며 item 순서는 반대로 변환)**

<br><br>

#### flex-wrap

- `nowrap`: Default. 모두 한 줄 배치
- `wrap`: 한 줄이 꽉차면 item 들이 다음 줄로 자동 이동
- `wrap-reverse`: 한 줄이 꽉차면 item 들이 다음 줄로 자동 이동 (방향 반대)

<br><br>

#### flex-flow

`flex-direction` 과 `flex-wrap` 을 한꺼번에 지정하는 속성값

```css
.container {
    flex-flow: row nowrap;
}
```

> 위의 코드는 아래와 동일

```css
.container {
    flex-direction: row;
    flex-wrap: nowrap;
}
```

<br><br>

#### justify-content

**main axis 의 item 배치 설정 (item 의 순서는 유지)**

> 아래의 설명은 `flex-direction: row;` 를 전제

- `flex-start`: item 들이 왼쪽에 붙어서 배치

- `flex-end`: item 들이 오른쪽에 붙어서 배치

- `center`: item 들이 중앙에 배치

- `space-around`: item 주변에 동일한 space 를 부여 (margin 부여)  
이에따라 라인의 첫번째 item 의 왼쪽 부분과 마지막 item 의 오른쪽 부분은 margin 이 절반이 되어  
동일한 space 를 가지지는 않는다. (item 사이의 간격은 각 item 의 margin 이 중복되기 때문)

- `space-evenly`: item 모두에게 동일한 간격을 부여

- `space-between`: 라인의 첫번째 item 과 마지막 item 을 끝부분에 배치한 뒤  
 나머지 item 들에게 동일한 간격 부여

<br><br>

#### align-items

**cross axis 의 item 배치 설정 (item 순서는 유지)**  

> 아래의 설명은 `flex-direction: row;` 를 전제

- `center`: 수직 중앙 배치

- `baseline`: text 를 기준으로 균등하게 item 을 배치

<br><br>

#### align-content

**cross axis 의 item 배치 설정 (item 순서는 유지)**  

- `flex-wrap: wrap;` 으로 설정되어 item 들이 여러개의 라인으로 구성될 때 적용가능

> 아래의 설명은 `flex-direction: row;` 를 전제

`justify-content` 의 속성값을 동일하게 사용가능하며,  
다른 점은 cross axis 를 기준으로 한다.  
따라서 `flex-direction: row;` 를 전제하므로 수직 축 기준으로 속성값을 적용한다.

<br><br>

### Item Attributes

#### order

> Default 0 (HTML 에 정의된 순서대로 보여진다.)

- 각각의 item 들에게 order 값을 지정하면 해당 값 순서대로 순서가 바뀐다.

<br><br>

#### flex-grow

- container 가 커졌을 때 어떻게 행동할지 결정

<br>

- Default 0 (자기자신의 고유 크기를 유지하려고 한다.)  
  - 브라우저 창을 늘리더라도 늘어난 크기만큼 메우려고 하지 않는다.

<br>

- 1 을 줄 경우 늘어난 크기만큼 창을 메우려고 한다.  

<br>

- item 모두에게 1을 할당하면 동일한 비율로 늘어난 창을 메우려고 한다.  

<br>

- item 3개가 있고, 2, 1, 1 으로 할당하게 되면 숫자의 비율만큼 더 많이 커지게 된다.

<br><br>

#### flex-shrink

- container 가 작아졌을 때 어떻게 행동할지 결정

- Default 0 (자기자신의 고유 크기를 유지하려고 한다.)  

- item 3개가 있고, 2, 1, 1 으로 할당하게 되면 숫자의 비율만큼 더 작아지게 된다.

<br><br>

#### flex-basis

- item 들이 공간을 얼마나 차지해야할지 세부적으로 명시

- Default: `auto`  (`flex-grow` 와 `flow-shrink` 지정된 값에 따라 변형)

```css
.item1 {
    flex-basis: 60%;
}

.item2 {
    flex-basis: 40%;
}
```

<br><br>

#### align-self

- **cross axis 기준**

- 해당 item 만 해당하는 정렬

- `center`: 해당 item 을 중앙에 배치

<br><br>

## flexbox practice

- Flexbox Froggy: <https://flexboxfroggy.com/#ko>

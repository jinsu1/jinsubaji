---
layout: post
title: CSS3
date: 2024-08-05
description: CSS # Add post description (optional)
img: /postImg/css3.jpg # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [css, css3]
---
## CSS

### 1. 구체성단위
-CSS는 점수가 같다면 더 아래에 입력한 스타일이 먹지만 점수가 다르다면 점수가 큰 쪽의 스타일이 적용된다.
 - id: 100점   
 - class: 10점   
 - tag: 1점   
 > ex) #container .div span => 111점

### 2. 계층선택자

#### html
```html
<div class="main">
    <div class="sub">
        <div class="content red">content</div>
    </div>
</div>
```
#### css
```css
.main { color:yellow;

    .sub {color: blue;
        .content{
            &.red {color:red;}
        }
    }
}
```
-css에서 부모선택자의 속성을 주고 그 부모선택자의 자식을 선언해야할 경우 부모선택자의 중괄호 안에서 선언할 수 있음   

-class="content red"의 경우 .content.red{}로 선언할 수도 있지만 위 코드와 같이 &를 붙이면 .content안에서도 선언할 수있음. &를 붙이지 않으면 .content의 동급이아닌 자식 중에 .red를 찾음 &:hover, &:active, &:after 등에 많이 사용

### 3. attachment 속성

-fixed

-뷰포트에 배경을 고정하여 스크롤해도 배경은 움직이지 않음 이미지를 css내에서 background-image 속성을 이용해서 넣어야 적용할 수 있음  

-3개의 section이있고 header와 footer에 배경이미지를 넣음


#### html
```html
<div class="header"></div>
<div class="section num1"></div>
<div class="section num2"></div>
<div class="section num3"></div>
<div class="footer"></div>
```
#### css
```css
body {
    padding: 0;
    margin: 0;
    }

    .section {
    height: 968px;

        &.num1 {
            background-color: yellow;
        }

        &.num2 {
            background-color: blue;
        }

        &.num3 {
            background-color: lightblue;
        }
    }

    .header, .footer {
        height: 500px;
    }

    .header {
        background-image:url(img/bg1.jpg);
        background-position: center center;
        background-size: cover;
        background-repeat: no-repeat;
        background-attachment: fixed;
    }

    .footer {
        background-image:url(img/bg2.jpg);
        background-position: center center;
        background-size: cover;
        background-repeat: no-repeat;
        background-attachment: fixed;
    }
```   

### 4. 접근성을 위한 편법?
-배경이미지에 텍스트를 포함한 이미지롤 넣고 싶은데 text를 안넣으면 접근성 때문에 웹표준에 맞지않는다. 
> text-indent: -10000px;   

를 이용해서 텍스트를 화면 밖으로 날려버리면 음성이 텍스트를 읽으면서 접근성이 해결되면서 원하는 이미지를 넣을 수 있다.   

-이미지저장을 막고싶을 대 이미지를 배경으로 넣으면 오른쪽클릭으로 이미지를 저장할 수 없지만 개발자모드에서는 저장할 수 있다.   

-모바일, 테블릿 등에서 a태그에 배경이미지 repeat을 사용하고 display:block 을 사용해서 배경이 차지할 너비를 먹고 텍스트는 날려버리면 반응형으로 만들지않아도 반응형처럼 보일 수 있다 물론 배경만 repeat하고 list는 no-repeat하고 왼쪽 고정.   

-가로반복은 1px만 있어도 되지만 세로반복은 높이를 맞춘 이미지를 가져와야한다.

#### html
```html
<nav class="navi">
    <a href="#">웹표준/웹디자인</a>
    <a href="#">소셜미디어마케팅</a>
    <a href="#">IT/모바일</a>
    <a href="#">QA</a>
</nav>
```
#### css
```css
.navi {
        background: url(img/list_right.png) repeat-y right top,
                    url(img/menu.png) no-repeat left top,
                    url(img/list_bg.png) repeat;

        a{
            display:block;
            width:auto;
            height:45px;
            text-indent: -10000px;
        }
    }
```

### aspect-ratio 속성

-aspect-ratio를 이용하여 width / height 비율을 지정할 수 있다.
1/3이라고 지정하면, 너비는 1, 높이는 3의 비율을 가지게 된다.

```css
width: 100px;
aspect-ratio: 1 / 3;
```

-이미지 깨짐을 방지하면서 종횡비를 지정하고 싶다면, object-fit 속성을 같이 써보자
img의 부모에 aspect-ratio를 지정하고, img에 object-fit: cover를 하면 이미지가 깨지지 않으면서 원하는 종횡비로 지정할 수 있다.

-inherit은 부모것을 상속한다는 의미   

### -webkit-line-clamp 안먹을 때

-아래 4줄이 셋트로 들어가야 먹는다.

```css
display:-webkit-box;
-webkit-box-orient:vertical;
overflow:hidden;
-webkit-line-clamp:2;
```

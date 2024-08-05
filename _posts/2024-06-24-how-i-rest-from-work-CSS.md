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

### 구체성단위
-CSS는 점수가 같다면 더 아래에 입력한 스타일이 먹지만 점수가 다르다면 점수가 큰 쪽의 스타일이 적용된다.
 - id: 100점   
 - class: 10점   
 - tag: 1점   
 > ex) #container .div span => 111점

### 계층선택자

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
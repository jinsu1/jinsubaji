---
layout: post
title: Module
date: 2024-08-29
description: Module for javascript # Add post description (optional)
img: /postImg/javascript.jpeg # Add image post (optional)
fig-caption: Module for javascript # Add figcaption (optional)
tags: [javascript, css, html, tab, menu, dropdown, accordion]
---
## <span style="color:#1E90FF; font-weight:bold;">TabMenu</span>

### HTML

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="tab-container">
        <div class="tab-button-group">
            <a href="#newyork" class="tab-button active">NewYork</a>
            <a href="#london" class="tab-button">London</a>
            <a href="#paris" class="tab-button">Paris</a>
            <a href="#seoul" class="tab-button">Seoul</a>
        </div>

        <div id="newyork" class="tab-page active">
            <h3>NewYork</h3>
            <p>NewYork is the capital city of US.</p>
        </div>

        <div id="london" class="tab-page">
            <h3>London</h3>
            <p>London is the capital of England.</p>
        </div>

        <div id="paris" class="tab-page">
            <h3>Paris</h3>
            <p>Paris is the capital of France.</p>
        </div>

        <div id="seoul" class="tab-page">
            <h3>Seoul</h3>
            <p>Seoul is the capital of Korea.</p>
        </div>
    </div>
</body>
</html>
```

### CSS

```css
 body {
            font-family: Arial;
        }

        .tab-button-group{
            overflow: hidden;
            border: 1px solid #ccc;
            background-color: #f1f1f1;
            display:flex;

            .tab-button {
                display:block;
                background-color: inherit;
                min-width: 100px;
                box-sizing: border-box;
                border: none;
                outline: none;
                padding: 14px 16px;
                font-size: 17px;
                color: #222;
                text-align: center;
                text-decoration: none;
                cursor: pointer;
                transition: 0.3s;

                &:hover {
                    background-color: #ddd;
                }

                &.active {
                    background-color: #ccc;
                }
            }
        }

        .tab-page {
            display: none;
            padding: 6px 12px;
            border: 1px solid #ccc;
            border-top: none;

            &.active {
                display: block;
            }
        }
```
### javascript

```javascript
document.querySelectorAll('.tab-button').forEach((v, i) => {
            v.addEventListener('click', (e) => {
                const currentIndex = i;
                const href = e.currentTarget.getAttribute("href");

                document.querySelectorAll('.tab-button').forEach((v1, i1) => {
                    if(currentIndex == i1) {
                        v1.classList.add('active');
                    } else {
                        v1.classList.remove('active');
                    }
                });

            document.querySelectorAll('.tab-page').forEach((v2, i2) => {
                v2.classList.remove('active');
            });

            document.querySelector(href).classList.add('active');
        });
    });
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">Dropdown</span>

### HTML
```html
<ul class="menu-container">
    <li class="menu-item">
        <a href="#">Frontend</a>
        <ul class="sub">
            <li>
                <a href="#">HTML + CSS</a>
            </li>
            <li>
                <a href="#">Javascript</a>
            </li>
            <li>
                <a href="#">jQuery</a>
            </li>
        </ul>
    </li>
    <li class="menu-item">
        <a href="#">Backend</a>
        <ul class="sub">
            <li>
                <a href="#">PHP</a>
            </li>
            <li>
                <a href="#">JSP</a>
            </li>
            <li>
                <a href="#">Node.js</a>
            </li>
        </ul>
    </li>
    <li class="menu-item">
        <a href="#">Mobile</a>
        <ul class="sub">
            <li>
                <a href="#">iOS</a>
            </li>
            <li>
                <a href="#">Android</a>
            </li>
            <li>
                <a href="#">Hybrid</a>
            </li>
        </ul>
    </li>
</ul>

<h1>Hello World</h1>

```
### CSS

```css
 body {
    background-color:#dad9d9;
}
.menu-container{
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;

    a {
        display: block;
        width: 179px;
        height: 48px;
        background-color: white;
        line-height: 48px;
        text-align: center;
        font-weight: bold;
        color: black;
        text-decoration: none;

        &:hover {
            background-color: #aaaaaa;
        }
    }

    .menu-item {
        flex: 0 0;
        position: relative;

        .sub {
            list-style: none;
            margin: 0;
            padding: 0;
            position: absolute;
            border-radius: 5px;
            z-index: 1000;
            max-height: 0;
            overflow: hidden;
            transition: max-height 180ms ease-out;
        }
    }
}
```
### Javascript

```javascript
document.querySelectorAll('.menu-item').forEach((v, i) => {
    v.addEventListener('mouseover', (e) => {
        const current = e.currentTarget;
        const sub = current.querySelector('.sub');
        sub.style.maxHeight = sub.scrollHeight + 'px';
    });

    v.addEventListener('mouseout', (e) => {
        const current = e.currentTarget;
        const sub = current.querySelector('.sub');
        sub.style.maxHeight = '0px';
    });
});
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">Accordion</span>

### HTML

```html
<h2>Animated Collapsibles</h2>
    <p>A Collpasible:</p>
    <div class="collapse">
        <h1 class="collapsible-title">Open Collapsible</h1>
        <div class="content">
            <p>
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Doloribus officia voluptatum magni dolore optio vitae corporis, voluptate repellat suscipit quod incidunt illum eum dolor eveniet ab nostrum voluptates laboriosam vero?
            </p>
        </div>
    </div>
    <div class="collapse">
        <h1 class="collapsible-title">Open Collapsible</h1>
        <div class="content">
            <p>
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Doloribus officia voluptatum magni dolore optio vitae corporis, voluptate repellat suscipit quod incidunt illum eum dolor eveniet ab nostrum voluptates laboriosam vero?
            </p>
        </div>
    </div>
```
### CSS

```css
 .collapsible-title {
    background-color: #777;
    color: white;
    cursor: pointer;
    padding: 18px;
    width: 100%;
    border: none;
    font-weight: normal;
    margin: 0;
    text-align: left;
    outline: none;
    font-size: 15px;

    &.active, &:hover {
        background-color: #555;
    }
}

.content {
    padding: 0 18px;
    max-height:0;
    overflow: hidden;
    transition: max-height 0.2s ease-out;
    background-color: #f1f1f1;
}
```
### Javacript

```javascript
 document.querySelectorAll(".collapsible-title").forEach((v, i) => {
    v.addEventListener('click', (e) => {
        document.querySelectorAll('.content').forEach((w, j) => {
            w.style.maxHeight = null;
        });
        const current = e.currentTarget;
        
        current.classList.toggle("active");


        document.querySelectorAll('.collapsible-title').forEach((w,j) => {
            if( w !== current) {
                w.classList.remove('active');
            }
        });

        const parent = current.closest(".collapse");
        const content = parent.querySelector(".content");
        
        if( !current.classList.contains('active')) {
            content.style.maxHeight = null;
        } else {
            content.style.maxHeight = content.scrollHeight + 'px';
        }
    });
});
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">주민번호</span>

### HTML

-앞자리는 6자까지만 입력가능, 앞자리 모두 입력 후 뒷자리 입력 칸으로 focus

```html
<form id="myform">
    <h3>주민번호를 입력하세요.</h3>
    <input type="text"  name="jumin1" id="jumin1" class="jumin" />
    ---
    <input type="password"  name="jumin2" id="jumin2" class="jumin" />
</form>
```

```javascript
document.querySelectorAll(".jumin").forEach((v, i) => {
    v.addEventListener("focus", e => {
        e.currentTarget.style.backgroundColor = '#006';
        e.currentTarget.style.color = '#fff';
    });

    v.addEventListener("blur", e => {
        e.currentTarget.style.backgroundColor = '#fff';
        e.currentTarget.style.color = "#000";
    });
});

document.querySelector("#jumin1").addEventListener('keyup', e => {
    const value = e.currentTarget.value;

    if(value.length >= 6) {
        e.currentTarget.value = value.substring(0, 6);
        document.querySelector("#jumin2").focus();
    }
});
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">팝업창 띄우기</span>

-window.open('주소', '창이름', '속성'); 으로 구성 속성은 대부분 공통으로 들어간다.
-팝업창이름을 같게하면 같은 창을 재활용한다.
-interval에 창 띄우기를 적용하면 설정한 시간마다 창을 띄울 수 있다.

```javascript
document.querySelector('#link3').addEventListener('click', e =>  {
    e.preventDefault();

    window.open('https://www.naver.com', 'mypop', 'width=500, height=500, scrollbars=no, toolbar=no, menubar=no, status=no');
});
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">웹 브라우저, 모바일 식별</span>

```html

<h1 id="browser"></h1>
<h1 id="mobile"></h1>

<script>
    function getWebBrowserName() {
        //전부 소문자로 변경해야 용이함
        //uset Agent == HTTP 요청을 보내는 디바이스와 브라우저 등 사용자 소프트웨어의 식별 정보
        let agt = navigator.userAgent.toLowerCase();

        //indexOf는 해당 문자열이 있는 위치의 시작점을 반환하며 없으면 -1을 반환
        if(agt.indexOf('edge') != -1) {
            return 'Edge';
        } else if (agt.indexOf('chrome') != -1) {
            return 'Chrome';
        } else if (agt.indexOf('netscape') != -1 ) {
            return 'Netscape';
        } else {
            return 'Unknown';
        }
    }

    function isMobile() {
        let tmpUser = navigator.userAgent.toLowerCase();
        let isMobile = false;

        if(tmpUser.indexOf("iphone") > -1 || tmpUser.indexOf("ipad") > -1 || tmpUser.indexOf("ipod") >  -1 || tmpUser.indexOf("android") > -1) {
            isMobile = true;
        }

        return isMobile;
    }

    document.getElementById("browser").innerHTML = getWebBrowserName();
    document.getElementById("mobile").innerHTML = isMobile();
</script>
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">이전 페이지 이동</span>

```javascript
document.querySelector("#link1").addEventListner('click', e => {
    e.preventDefault();
    history.back(); // history.forward();
});
```
<br>

## <span style="color:#1E90FF; font-weight:bold;">페이지 이동</span>

```javascript
location.href = 'https://www.naver.com'; // 페이지 및 파일 이동
location.reload(); // 페이지 새로고침
```

<br>
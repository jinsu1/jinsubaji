---
layout: post
title: Module
date: 2024-08-29
description: Module for javascript # Add post description (optional)
img: /postImg/javascript.jpeg # Add image post (optional)
fig-caption: Module for javascript # Add figcaption (optional)
tags: [javascript, css, html, tab, menu, dropdown, accordion]
---
## TabMenu

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

## Dropdown

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

## accordion

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

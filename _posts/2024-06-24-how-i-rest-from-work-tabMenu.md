---
layout: post
title: TabMenu
date: 2024-08-29
description: TabMenu for javascript # Add post description (optional)
img: /postImg/javascript.jpeg # Add image post (optional)
fig-caption: tabMenu for javascript # Add figcaption (optional)
tags: [javascript, css, html, tab, menu]
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
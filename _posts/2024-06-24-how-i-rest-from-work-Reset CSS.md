---
layout: post
title: Reset & Nomalize CSS
date: 2024-08-05
description: css reset & nomalize # Add post description (optional)
img: /postImg/reset.PNG # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [reset , nomalize, css, cdn]
---
## Reset CSS
### Reset CSS - CDN
 ```html
 <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reset-css@4.0.1/reset.min.css"/>
 ```

### reset.css
```css
@import "./reset.css"
```
>css파일에 import 하여 사용 .html에 link로 reset.css를 연결하면 인식하지 못하는 경우가 있다.

```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
 ```

### Nomalize CSS - CDN

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/normalize/8.0.1/normalize.min.css"/>
```

최신 cdn은 [cdnjs](https://cdnjs.com/libraries/normalize)여기에서 복사 할 수 있다.

### Nomalize CSS - npm

```
npm i normalize.css
```
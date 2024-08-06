---
layout: post
title: Swiper
date: 2024-08-06
description: Swiper API # Add post description (optional)
img: /postImg/swiper.png # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [swiper, api, slide]
---
## Swiper

### Swiper Demo

- https://swiperjs.com/demos

### 하나의 javascript로 여러개의 swiper 실행

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Swiper demo</title>
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, minimum-scale=1, maximum-scale=1"
    />
    <!-- Link Swiper's CSS -->
    <link href="https://cdn.jsdelivr.net/npm/swiper@8.4.5/swiper-bundle.min.css" rel="stylesheet"/>

    <!-- Demo styles -->
    <style>
        html,
        body {
            position: relative;
            height: 100%;
        }

        body {
            background: #eee;
            font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
            font-size: 14px;
            color: #000;
            margin: 0;
            padding: 0;
        }

        .swiper2{
            margin-left: auto;
            margin-right: auto;
            position: relative;
            overflow: hidden;
            list-style: none;
            padding: 0;
            z-index: 1;
        }

        .swiper, .swiper2 {
            width: 100%;
            height: 50%;
        }

        .swiper-slide, swiper-slide2 {
            text-align: center;
            font-size: 18px;
            background: #fff;

            /* Center slide text vertically */
            display: -webkit-box;
            display: -ms-flexbox;
            display: -webkit-flex;
            display: flex;
            -webkit-box-pack: center;
            -ms-flex-pack: center;
            -webkit-justify-content: center;
            justify-content: center;
            -webkit-box-align: center;
            -ms-flex-align: center;
            -webkit-align-items: center;
            align-items: center;
        }

        .swiper-slide2 {
            flex-shrink: 0;
            width: 100%;
            height: 100%;
            position: relative;
            transition-property: transform;
        }

        .swiper-slide img, .swiper-slide2 img {
            display: block;
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        [scroll]{
            min-height: 100vh;
            background: gray;
        }
    </style>
  </head>

  <body>
    <h1>Loop true (Dynamic)</h1>
    <div class="swiper mySwiper">
      <div class="swiper-wrapper">
        <div class="swiper-slide">Slide 1</div>
        <div class="swiper-slide">Slide 2</div>    
        <div class="swiper-slide">Slide 3</div>
        <div class="swiper-slide">Slide 4</div>
      </div>
      <div class="swiper-pagination"></div>
    </div>
    <br><br>
      <h1>Loop2(Dynamic)</h1>
      <div class="swiper mySwiper">
        <div class="swiper-wrapper">
          <div class="swiper-slide">Slide 1</div>
          <div class="swiper-slide">Slide 2</div>    
          <div class="swiper-slide">Slide 3</div>
          <div class="swiper-slide">Slide 4</div>
        </div>
        <div class="swiper-pagination"></div>
      </div>
    <!-- Swiper JS -->
    <script src="https://cdn.jsdelivr.net/npm/swiper@8/swiper-bundle.min.js"></script>

    <!-- Initialize Swiper -->
    <script>
        let loop_slider = true;
        const num_of_slides = 4;
        let swiper_sliders = document.querySelectorAll(".swiper");

        // forEach method, could be shipped as part of an Object Literal/Module
        // for class
        for (const this_slider of swiper_sliders){
        //work as per usual
        let num_of_slides_before_init = this_slider.querySelectorAll(".swiper-slide").length;
        if(num_of_slides_before_init < num_of_slides){
            loop_slider = false;
        }

        var swiper = new Swiper(this_slider, {
            slidesPerView: 3,
            loop: loop_slider,
            spaceBetween: 30,
            pagination: {
            el: ".swiper-pagination",
            clickable: true,
            },
        });

        };
    </script>
  </body>
</html>

```

### 두 개의 swiper를 하나의 트리거로 실행

-아래의 코드로 실행하면 '서브에서 동작시 메인이 함께 동작' 밖에 되지않는다. SubSwiper를 선언하지 않았는데 윗 줄 컨트롤에서 호출했기 때문

```javascript
var MainSwiper = new Swiper('.main_swiper .swiper-container', {
  controller: {
    control: SubSwiper,
  },
});
var SubSwiper = new Swiper('.sub_swiper .swiper-container', {
  controller: {
    control: MainSwiper
  },
});
```

-아래의 코드를 각각의 swiper를 작성하고 밑에 사용하면 양쪽 모두 동작 한다.

```javascript
MainSwiper.controller.control = SubSwiper;
SubSwiper.controller.control = MainSwiper;
```

### error

- loop속성을 사용하면 swipe가 작동하지않음
> 상단에 let loop_slider = ture를 선언하고 loop:loop_slider 속성을 넣으니 동작함
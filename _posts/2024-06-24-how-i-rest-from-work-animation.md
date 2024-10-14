---
layout: post
title: css, js Libaray 모음
date: 2024-09-04
description: css, js Libaray 모음 # Add post description (optional)
img: /postImg/openAPI.png # Add image post (optional)
fig-caption: Libaray # Add figcaption (optional)
tags: [libray, api, animation, javascript]
---
#### 모음 정리 사이트
https://inpa.tistory.com/category/Library/JS%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EB%AA%A8%EC%9D%8C?page=1

## <span style="color:#1E90FF; font-weight:bold;">AOS</span>

>https://michalsnik.github.io/aos/

### Setting
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.css" />
<script src="https://unpkg.com/aos@next/dist/aos.js"></script>
```
### HTML
```html
<ul>
    <li>data-aos : 애니메이션 종류 문자열 (필수)</li>
    <li>data-aos-duration: 애니메이션 속도 ms</li>
    <li>data-aos-easing: 애니메이션 재생 옵션 (css의 transition 속성값을 따름)</li>
    <li>data-aos-offset: 대상 요소가 화면 하단으로부터 떨어진 거리 (이 거리에 도달하면 애니메이션이 작동함)</li>
    <li>data-aos-anchor-placement="박스위치-화면위치"<br />
        -대상 요소의 top, center, bottom이 브라우저의 top, center, bottom에 도달할 경우 애니메이션 시작</li>
</ul>

   <div class="box" data-aos="fade-up" data-aos-anchor-placement="center-bottom" data-aos-easing="ease-in-sine" data-aos-duration="600">AOS</div>

```
### CSS

```css
 .box {
    width: 300px;
    height: 200px;
    font-size: 40px;
    color: white;
    background-color: #f60;
    text-align: center;
    line-height: 200px;
    margin: 250px auto;
}
```
### Javascript
```javascript
AOS.init();
```

## <span style="color:#1E90FF; font-weight:bold;">Pageable(Full Page)</span>

### Setting

```html
<!--pageable-->
<link rel="stylesheet" href="https://unpkg.com/pageable@latest/dist/pageable.min.css">
<script src="https://unpkg.com/pageable@latest/dist/pageable.min.js"></script>

<!--aos-->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/aos/2.3.4/aos.js"></script>
```

### HTML

```html
<div class="container">
    <div data-anchor="Page 1">
        <div class="page page1 video-background">
            <video src="assets/media/intro.mp4" autoplay muted loop></video>
            <div class="video-overray">
                <h1 data-aos="fade-up" data-aos-duration="500">Hello Wrold</h1>
                <p data-aos="fade-up" data-aos-duration="500" data-aos-delay="300">Video Background Examplee</p>
            </div>
        </div>
    </div>
    <div data-anchor="Page 2">
        <div class="page page2">
            <h1>Page2</h1>
        </div>
    </div>
    <div data-anchor="Page 3">
        <div class="page page3">
            <h1>Page3</h1>
        </div>
    </div>
</div>
```

### CSS

```css
*{
    padding: 0;
    margin: 0;
    box-sizing: border-box;
}

/* box-sizin: border-box;를 사용할 경우 
가로 스크롤바가 생성되기 때문에
라이브러리의 css를 강제로 덮어씌어야 함*/

.pg-wrapper {
    box-sizing: content-box;
}

.page {
    box-sizing: border-box;
    width:100%;
    height:100%;

    display:flex;
    justify-content: center;
    align-items: center;
}

.page h1 {
    color: #fff;
    font-size: 64px;
}

.page1 {
    background-color: #3f51b5;
}

.page2 {
    background-color: #673ab7;
}

.page3 {
    background-color: #9c27b0;
}

.video-background {
    background-color: #06f2;
    position: relative;
    width:100%;
    height:100%;
}

.video-background video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.video-background .video-overray {
    position: absolute;
    left:0;
    top:0;
    z-index: 100;
    width:100%;
    height:100%;

    display:flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size:48px;
    color:#fff;
    text-shadow: 0 1px #000;
}

.video-background .video-overray h1 {
    margin: 0;
}

.video-background .video-overray p {
    border-top: 1px solid #fff;
    margin:0;
}
```

### Javscript

```javascript
new Pageable(".container");
AOS.init();
```

## <span style="color:#1E90FF; font-weight:bold;">Fslightbox(이미지 뷰어)</span>

### Setting

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/fslightbox/3.0.9/index.min.js"></script>
```

### HTML

```html
<h1>fslightbox</h1>
<h2>Single Gallery</h2>
<a data-fslightbox="my-single" href="assets/img/img1.png">
    <img src="assets/img/img1.png" width="200" />
</a>

<h2>Multi Gallery</h2>
<!-- data-fslight의 값을 동일하게 지정 -->
<a data-fslightbox="my-multi" href="assets/img/img2.png">
    <img src="assets/img/img2.png" width="200" />
</a>
<a data-fslightbox="my-multi" href="assets/img/img3.png">
    <img src="assets/img/img3.png" width="200" />
</a>
<a data-fslightbox="my-multi" href="assets/img/img4.png">
    <img src="assets/img/img4.png" width="200" />
</a>
```

## <span style="color:#1E90FF; font-weight:bold;">sweetalert(modal)</span>

### Setting

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/sweetalert2/11.13.1/sweetalert2.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/sweetalert2/11.13.1/sweetalert2.all.min.js"></script>
```

### HTML

```html
 <button id="btn1" type="button">button1</button>
<button id="btn2" type="button">button2</button>
<button id="btn3" type="button">button3</button>
<button id="btn4" type="button">button4</button>
<button id="btn5" type="button">button5</button>
```

### CSS

```css
 .title {
    color: white;
    font-size: 36px;
    text-shadow: 0 1px #000;
}

.content {
    color: white;
    font-size: 18px;
    text-shadow: 0 1px #000;
}

.swal_contents {
    width: 300px;
    margin-bottom: 15px;
}

.swal_textarea{
        width: 300px;
        height: 100px;
    }
```

### Javscript

```javascript
 document.querySelector("#btn1").addEventListener('click', e => {
    new Swal('성공적으로 처리되었습니다.');
});

document.querySelector("#btn2").addEventListener('click', e => {
    // (제목, 내용, 아이콘)
    // 아이콘 --> 'success', 'warning', 'info', 'question', 'error'
    new Swal("탈퇴확인", "정말 탈퇴하시겠습니까?", "question");
});

document.querySelector("#btn3").addEventListener('click', e => {
    new Swal({
        title: '<font color="red">에러</font>', //제목
        html:"요청하신 처리에 실패하였습니다. <br/>관리자에게 문의 바랍니다.", // 내용
        icon: "error", //아이콘
        showCloseButton: true, // 오른쪽 상단 x 버튼 유무
        confirmButtonText: "확인", // 확인버튼 문구
        showCancelButton: true, // 취소버튼 유무
        cancelButtonText: "취소", // 취소버튼 문구
        cancelButtonColor: "#f60", //취소버튼 색상
    }).then( (result => {
        // 버튼이 눌러졌을 경우 콜백 연결 console.log(result)로 찍어보면 어떤 값들이 변경되는지 볼 수 있음
        if(result.value) {
            // 확인 버튼이 눌러진 경우
            new Swal({
                title: "문의하기", 
                html: '<input type="text" class="swal_contents" placeholder="작성자" /></label><br/><textarea type="text" class="swal_textarea" placeholder="문의내용"></textarea>', 
                icon: 'info',
                showCloseButton: true,
                confirmButton: "전송",
                showCancelButton: true,
                }).then( e => {
                    if(e.value) {
                        
                        new Swal('성공적으로 처리되었습니다.');
                    } else {
                        new Swal("취소", "취소되었습니다.", "error");            
                    }
                });
        } else if (result.dismiss === "cancel") {
            //취소버튼이 눌러진 경우
            new Swal("취소", "취소되었습니다.", "error");
        }
    }));
});

document.querySelector("#btn4").addEventListener("click", e => {
    new Swal({
        title: "Thank you!",
        html: "성원에 감사드립니다.",
        imageUrl: "assets/img/img5.png", //이미지 넣기
        imageWidth: 480,
        imageHeight: 240
    });
});


document.querySelector("#btn5").addEventListener("click", e => {
    new Swal({
    title: '<span class="title">Thank you!</span>',
    html: '<span class="content">성원에 감사드립니다.</span>',
    width: 600,
    padding: "100px",
    background: "#fff url(assets/img/img5.png) center center/cover no-repeat",
    });
});
```

## <span style="color:#1E90FF; font-weight:bold;">Chart</span>

### Setting

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
```

### HTML

```html
<h1>Chart.js</h1>

<div class="conainer">
    <div class="subplot">
        <h2>기본 선 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart1"></canvas>
        </div>
    </div>
    
    <div class="subplot">
        <h2>기본 막대 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart2"></canvas>
        </div>
    </div>

    <div class="subplot">
        <h2>다중 막대 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart3"></canvas>
        </div>
    </div>

    <div class="subplot">
        <h2>파이 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart4"></canvas>
        </div>
    </div>

    <div class="subplot">
        <h2>Polar Area Chart</h2>
        <div class="subplot-item">
            <canvas id="mychart5"></canvas>
        </div>
    </div>

    <div class="subplot">
        <h2>도넛 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart6"></canvas>
        </div>
    </div>

    <div class="subplot">
        <h2>산점도 그래프</h2>
        <div class="subplot-item">
            <canvas id="mychart7"></canvas>
        </div>
    </div>
</div>

```

### CSS

```css
*{
    box-sizing: border-box;
}

.container {
    display: flex;
    flex-wrap: wrap;
}

.subplot {
    width: 50%;
    padding: 10px;
}

.subplot-item {
    width:100%;
    height: 400px;
}
```

### Javascript

```javascript
 const names = ["철수", "영희", "민수", "수현", "호영"];
const kor = [98, 78, 64, 72, 82];
const eng = [53, 62, 85, 94, 70];
const math = [99, 62, 76, 21, 67];

//그래프가 표시될 캔버스 영역
const mychar1 = document.getElementById("mychart1");
const mychar2 = document.getElementById("mychart2");
const mychar3 = document.getElementById("mychart3");
const mychar4 = document.getElementById("mychart4");
const mychar5 = document.getElementById("mychart5");
const mychar6 = document.getElementById("mychart6");
const mychar7 = document.getElementById("mychart7");

//선그래프
new Chart(mychart1, {
    //그래프 종료 (line, bar, pie, polarArea, doughnut, catter)
    type: "line",
    //데이터 영역
    data: {
        //x축
        // labels: ["철수", "영희", "민수", "수현", "호영"],
        labels: names,
        datasets: [
            {
                label: "국어",
                data: kor,
                borderWidth: 1,
                borderColor: "#ff6600",
            },
            {
                label: "영어",
                data: eng,
                borderWidth:1,
                borderColor: "#ff00ff",
            },
        ],
    },

    //그래프 옵션
    options: {
        //그래프의 가로, 세로 비율 유지(기본값 true) => 부모 div의 넓이에만 맞추고 높이는 무시함
        //false로 설정할 경우 부모의 width, height에 크기를 맞춤 
        maintainAspectRatio: false,
    },
});

//기본 막대
new Chart(mychart2, {
    type: "bar",
    data: {
        labels: names,
        datasets: [
            {
                label: "국어",
                data: kor,
                borderWidth: 0.5,

                //색상 처리 팁: border와 background를 동일 색상으로 처리하되, border는 불투명, background는 반투명
                //단일 문자열인 경우 모든 막대가 같은 색상.
                //데이터의 수 만큼 배열로 색상을 나열할 경우 각 막대별로 다른 색상
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255,99,132,,0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
        ],
    },
});

//다중 막대 그래프
new Chart(mychart3, {
    type: "bar",
    data: {
        labels: names,
        datasets: [
            {
                label: "국어",
                data: kor,
                borderColor: "rgba(54, 162, 235, 1)",
                backgroundColor: "rgba(54, 162, 235, 0.2)",
            },
            {
                label: "영어",
                data: eng,
                borderWidth: 0.5,
                borderColor: "rgba(255, 99, 132, 1)",
                backgroundColor: "rgba(255, 99, 132, 0.2)",
            },
        ],
    },
    options: {
        maintainAspectRatio: false,
    },
});

//파이 그래프
new Chart(mychart4, {
    type: "pie",
    data: {
        labels: names,
        datasets: [
            {
                label: "국어",
                data: kor,
                borderWidth: 0.5,
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255, 99, 132, 0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
        ],
    },
    options: {
        maintainAspectRatio: false,
        plugins: {
            //범주에 대한 설정
            legend: {
                position: "left",
            },
            title: {
                display: true,
                text: "학생별 점수 비율",
            },
        },
    },
});

//polar Area 그래프
new Chart(mychart5, {
    type: "polarArea",
    data: {
        labels: names,
        datasets: [
            {
                label: "국어",
                data: kor,
                borderWidth: 0.5,
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255, 99, 132, 0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
        ],
    },
    options: {
        maintainAspectRatio: false,
        plugins: {
            legend: {
                position: "left",
            },
            title: {
                display: true,
                text: "학생별 점수 비율",
            },
        },
    },
});

//도넛 그래프
new Chart(mychart6, {
    type: "doughnut",
    data: {
        labels: names,
        datasets: [
            {
                data: kor,
                borderWidth: 0.5,
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255, 99, 132, 0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
        ],
    },
    options: {
        maintainAspectRatio: false,
        plugins: {
            legend: {
                position: "left",
            },
            title: {
                display: true,
                text: "학생별 점수 비율",
            },
        },
    },
    
});

//산점도 그래프
new Chart(mychart7, {
    type: "scatter",
    data: {
        labels: kor,
        datasets: [
            {
                label: "국어점수에 따른 영어 점수",
                data: eng,
                borderWidth: 0.5,
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255, 99, 132, 0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
            {
                label: "국어점수에 따른 수학 점수",
                data: math,
                borderWidth: 0.5,
                borderColor: ["rgba(255,99,132,1)", "rgba(54, 162, 235, 1)", "rgba(255, 206, 86, 1)", "rgba(153, 102, 255, 1)"],
                backgroundColor: ["rgba(255, 99, 132, 0.2)", "rgba(54, 162, 235, 0.2)", "rgba(255, 206, 86, 0.2)", "rgba(153, 102, 255, 0.2)"],
            },
        ],
    },
    options: {
        maintainAspectRatio: false,
        plugins: {
            legend: {
                position: "left",
            },
            title: {
                display: true,
                text: "학생별 점수 비율",
            },
        },
    },
});
```


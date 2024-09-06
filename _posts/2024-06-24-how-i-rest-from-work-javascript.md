---
layout: post
title: javascript
date: 2024-08-16
description: javascript # Add post description (optional)
img: /postImg/javascript.jpeg # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [javascript]
---
## javacript
[Javascript 시작하기.pdf](https://github.com/user-attachments/files/16631995/Javascript.pdf)   

### Extentions
-ESLint(Javascript 구문 검사 기능)   
-Code Runner (설치후 Alt+Ctrl+N 단축키로 현재 소스코드 실행. 환경설정에서 Code-runner: Clear Previous Output 항목 체크)

### Array
-Array 클래스를 사용한 할당
```javascript
let myArr = new Array(1, 2, "A");
```
-Array 클래스로 할당할 시 1개의 원소만 할당하는데 그 원소가 숫자일 경우 그 숫자 값 만큼의 빈 칸을 갖는 배열이 생성된다.
```javascript
let myArr = new Array(3);  // 1개의 배열에 3이 들어가는게아니라 3개의 빈 배열을 생성
let myArr = new Array("3"); // 이렇게 1개의 배열을 생성 할 수 있지만 string 타입으로 생성되니 주의
```

-구조분해
```javascript
let myArr = [100, 200, 300];
const [a, b, c] = myArr
```
> 배열의 원소가 각 const a, b, c의 변수에 순서대로 저장된다.


```javascript
let myArr = [100, 200, 300, 1, 2, 3, 4];
const [a, b, ...c];
```
> ...은 나머지 원소를 모두 const c에 담는다.

-존재하지 않는 원소에 값을 할당할 경우 그 인덱스에 대한 값이 새로 생성된다. 
-아래 코드와 같이 값을 할당 할 경우 myArr[3]에는 undefined가 된다.
```javascript
const myArr = [1, 2, 3];
myArr[4] = 5;
```
-배열의 역순정렬
```javascript
const data = [1, 5, 2, 4, 3];
console.log(data);

let official = data.length % 2 == 0 ? data.length / 2 : (data.length-1) / 2;

for(let i=0; i < official; i++) {
    let target = data.length-1-i;

    let temp = data[i];
    data[i] = data[target];
    data[target] = temp;
}
```

-선택정렬과 버블정렬 (i와 data.length값 변화에 중점)
```javascript
//선택정렬
const data = [ 3, 5, 1, 4, 2 ];
console.log(data);

for(let i=0; i < data.length-1; i++) {
    for(let j=i+1; j < data.length; j++) {
        if(data[i] > data[j]) {
            let temp = data[i];
            data[i] = data[j];
            data[j] = temp;
        }
    }
}
```
```javascript
//버블정렬
const data = [3, 5, 1, 4, 2];
console.log(data);

for(let i=0; i < data.length -1; i++) {
    for(let j=0; j < data.length -1 -i; j++) {
        if(data[j] > data[j+1]) {
            let temp = data[j];
            data[j] = data[j+1];
            data[j+1] = temp;
        }
    }
}
console.log(data);
```
[배열 연습문제 14문제](https://github.com/user-attachments/files/16688963/_._compressed.pdf)   
[배열 연습문제 풀이](https://github.com/user-attachments/files/16688844/javascript_06_array.pdf)


### JSON

-JSON은 type이 String인 반면 Javascript Object는 Object 타입으로 데이터 타입이 다르다.   
-JSON에서는 모든 property를 따옴표로 묶어 사용하지만 Javascirpt Object는 따옴표가 없이 사용 가능하다.
-JSON에서는 함수를 할당할 수 없다.   

-key값의 데이터에 배열 또는 JSON을 넣을 수 있다.
```javascript
const json1 = { ... };
const json2 = { ... };
const json3 = {
 data1: json1,
 data2: json2
};
```
-위 코드처럼 참조하지 않고 직접 할당도 가능하다.
```javascript
const myjson = {
 data1: {
 ...
 },
 data2: {
 ...
 }
};

```
-JSON 데이터 추가 및 수정

1) 방법1 : 한 가지의 키 값을 추가 할 때 이용

```javascript
const foo = {
    name: "자바스크립트",
    age: 19
};

foo.email = "hello@world.com";
console.log(foo);
```

2) 방법2 : 여러 개의 키 값과 데이터를 입력할 때 이용


```javascript
let origin = {name: 'javascript', age: 25};
origin = {...origin, gender: 'M'};
console.log(origin);

const newdata2 = {...origin, age: 30, gender: 'F'};
console.log(newdata2);
```
>...변수명을 통해 기존 json의 데이터를 불러오고 key를 추가할 수 있다. 불러오지않고하면 전의 내용을 덮어씌워 사라져버리니 주의 할 것. 새로운 변수명에 데이터를 추가해서 저장하는 것도 가능

-참조복사와 일반복사   

```javascript
let a = 100;
let b = a;
console.log(`a= ${a}, b= ${b}`);

a++;
console.log(`a= ${a}, b=${b}`);
```
>일반복사는 a는 수정되어도 b까지 수정되지 않지만 배열과 json은 복사한 쪽과 원본 둘 중 하나라도 수정하면 둘 다 수정된다.

-옵셔널체이닝

```javascript
const user = { name: "박진수"};
console.log(user?.name?.age);
```
>if문의 축양형. user가있다면? user.name출력. 그것도 있다면? user.name.age를 출력 (한 칸의 자식요소까지 없다면 undefined를 출력하지만 두 칸의 자식요소가 없다면 에러가 발생)

### 함수
-x 부분을 매개변수 => 파라미터(parameter or params)라고 한다

```javascript
function (x) {}
```

-return은 함수호출자리에 반환하는 값이다. return하지않으면 함수가 진행되고 아무값도 반환되지않고 지나간다.

-화살표 함수(함수의 축약형)
```javascript
//let e = (x) => {} 
//let e = function (x) {}

const foo = (x) => {
    for(let i=0; i<x; i++) {
        console.log("hello world");
    }
};

foo(7);

const bar = x => {
    for(let i=0; i<x; i++) {
        console.log("hello");
    }
};

bar(3);

const hello = (x, y) => x+y;
// const hello = (x, y) => {
//      return x+y;
// }
console.log(hello(100,200));
```
> 파라미터가 한 개 일 때 소괄호를 생략가능하고 처리 로직이 한 줄일 경우 return도 생략가능하다.

-콜백함수(파라미터에 함수명을 넣어 함수명(pram, pram)을 만드는 구조로 또 다른 함수를 호출하여 연산결과를 리턴값으로 받아온다. => 함수 안의 함수)

```javascript
function something(x, y, cb) {
    const result = cb(x, y);
    console.log(cb);
    console.log(`${x}와${y}의 연산 결과는 ${result}`);
}

function plus(a,b) {
    return a+b;
}

let minus = (a, b) => a-b;

something(100, 50, plus);
something(100, 50, minus);
console.log(plus);

```
> 함수명을 출력하면 [Function: plus] 이런식으로 출력됨.

-익명함수 형태의 콜백함수

```javascript
something(200, 100, function(a, b) {
    return a * b;
});


something(5, 7, (a, b) => {
    for(let i=a; i<b; i++) {
     return i*7;
    }

});

something(5, 7, (a, b) => {
    return a + b;
});

something(5, 7, (a, b) => a + b );

------------------------------------
function something(x, y, cb) {
    const result = cb(x, y);
    cb(x, y);
}

something(5, 7, (a, b) => {
    for(let i=a; i<b; i++) {
      console.log(`7 x ${i} = ${i*7}`);
    }
    return 1;
});

```

### Event
-keyEvent를 사용할 때에는 keyup을 주로 사용
> keypress는 한글이나 특수키에 반응을 안하고 x, keydown은 중복실행 위험성이 있음

-keyCode값을 이용하여 특정 키에대한 이벤트를 실행할 수 있다.   
[keyCode 표 보기](https://blog.munilive.com/posts/keyboard-keycode-value.html)   

-scroll event
1) window.scrollY: 현재 화면의 최상단 y좌표 값
2) window.screen.availHeight: 현재 보고있는 화면의 y좌표 길이
3) document.body.scrollHeight: 현재 페이지의 전체 길이

-클릭 시 클래스 추가, 삭제 이벤트 (단일)
```javascript
document.querySelector(".btn").addEventListener("click", (e) => {
    if(!(e.currentTarget.classList.contains("active"))) {
        e.currentTarget.classList.add("active");
    } else {
        e.currentTarget.classList.remove("active");
    }
});
```
> e.currentTarget은 이벤트가 발생한 개체를 의미  

-name.dataset.이름 = "값";로 data-이름 속성을 html태그에 읽거나 수정, 삽입할 수 있음
> name.이름.value로  접근할 수 있는건 json이고 html태그의 data-속성에 접근하려면 dataset을 꼭 해야함 너 헷갈려 하더라   



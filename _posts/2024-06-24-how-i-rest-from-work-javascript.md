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

[Javascript 시작하기.pdf](https://github.com/user-attachments/files/16631995/Javascript.pdf)

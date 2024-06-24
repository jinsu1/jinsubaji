---
layout: post
title: C언어 - 배열
date: 2024-06-24
description: description # Add post description (optional)
img: /postImg/C_Thumbnail.png # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [C언어 ,배열]
---
## Array
### Array_1
```c
#include <stdio.h>
#include <windows.h>

int main() {
    SetConsoleOutputCP(CP_UTF8);
    /*배열의 생성과 데이터 저장의 개별처리*/
    int dooly[3];

    dooly[0] = 75;
    dooly[1] = 82;
    dooly[2] = 91;

    /*배열의 생성과 데이터 저장의 일괄처리*/
    int douneo[3] = { 88, 64, 50};

    /*데이터를 일괄 저장할 경우 배열의 크기 지정은 생략 가능함*/
    int ddochy[] = { 100, 100, 90};

    /*부족한 자리는 자동으로 0으로 채워진다*/
    int micol[3] = { 70 };
    
    /*int형 변수 3개가 모였기 때문에 배열의 크기는 12 그러므로 배열의 크기에서 int의 크기를 나누면 배열의 길이가 된다.*/
    int length1 = sizeof(dooly) / sizeof(int);
    int length2 = sizeof(douneo) / sizeof(int);
    int length3 = sizeof(ddochy) / sizeof(int);
    int length4 = sizeof(micol) / sizeof(int);

    int sum1=0, sum2=0, sum3=0, sum4=0;

    for (int i = 0; i < length1; i++) {
        sum1 += dooly[i];
    }

    for (int i = 0; i < length2; i++) {
        sum2 += douneo[i];
    }

    for (int i = 0; i < length3; i++) {
        sum3 += ddochy[i];
    }

    for (int i = 0; i < length4; i++) {
        sum4 += micol[i];
    }
    
    /*배열의 전체 길이로 나누어 평균을 구할 수 있다.*/
    double avg1 = (double) sum1 / length1;
    double avg2 = (double) sum2 / length2;
    double avg3 = (double) sum3 / length3;
    double avg4 = (double) sum4 / length4;

    printf("[둘리] 총점 %d, 평균 %0.2lf\n", sum1, avg1);
    printf("[도우너] 총점 %d, 평균 %0.2lf\n", sum2, avg2);
    printf("[또치] 총점 %d, 평균 %0.2lf\n", sum3, avg3);
    printf("[마이콜] 총점 %d, 평균 %0.2lf\n", sum4, avg4);

    return 0;
}
```
### 실행결과
![result]({{site.baseurl}}/assets/img/postImg/array1.PNG)

### Array_2
```c
#include <stdio.h>
#include <windows.h>

int main() {
    SetConsoleOutputCP(CP_UTF8);
    
    /*글자들이 모여서 이룬 배열을 문자열(String)이라고 한다*/
    /*배열의 마지막에는 문장의 끝을 알리는 NULL 문자(\0)가 저장되어야 한다.*/
    char str1[] = {'H','e','l','l','o','\0'};

    /*배열의 길이를 얻고자 하는 경우, char는 1byte이므로 null문자의 길이까지 포함되므로 총 6byte가 된다.*/
    int length1 = sizeof(str1);

    /*문자열을 한번에 출력할 경우 %s를 사용한다.*/
    printf("'%s'의 글자 수=%d\n", str1, length1);

    /*문자열을 얻는 또 다른 방법 --> 문자열의 긑을 알기 위해서 맨 마지막에 \0이 자동으로 포함된다.*/
    char str2[] = "World";
    int length2 = sizeof(str2);

    printf("'%s'의 글자 수=%d\n", str2, length2);

    /*문자열은 char형의 배열이므로, 반복문으로 한 글자씩 처리가 가능하다.
    --> 마지막 Null문자를 제외하기 위해서 조건식에 -1을 한다.*/
    for (int i=0; i<length2-1; i++) {
        printf("%c\n", str2[i]);
    }
    

    return 0;
}
```
### 실행결과
![result]({{site.baseurl}}/assets/img/postImg/array2.PNG)



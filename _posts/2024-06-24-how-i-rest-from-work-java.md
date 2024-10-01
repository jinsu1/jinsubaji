---
layout: post
title: Java
date: 2024-09-09
description: Java note # Add post description (optional)
img: /postImg/java.png # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [java]
---
## Setting

-jdk 설치 (oracle에서 다운받는건 유료버전이라 비추) https://jdk.java.net/archive/ 17.0.2버전 이하는 괜찮지만 그 이후 버전은 비추

-환경변수 설정 => JAVA_HOME 변수 생성, jdk경로 입력   
-path에 jdk bin 경로 입력   

-Extension Pack for Java (자바 개발에 필요한 필수 익스텐션 모음   )   
-PlantUML (클래스 다이어그램 생성 도구)   
-PlantUML Previewer (PlantUML로 작성된 다이어그램 뷰어)   
-PlantUML Syntax (PlantUML 문법 강조 도구)   

## String 관련

```java
String msg = "Life is too short. You need Java!";
System.out.println("문자열: " + msg);

int len = msg.length();
System.out.println("문자열의 길이: " + len);

char str2nd = msg.charAt(2);
System.out.println("두 번째 글자: " + str2nd);

int p1 = msg.indexOf("f");
System.out.println("'f'가 처음 나타나는 위치: " + p1);

// 없음 : -1
System.out.println("'z'가 처음 나타나는 위치: " + msg.indexOf("z"));

System.out.println("'short'가 처음 나타나는 위치: " + msg.indexOf("short"));

//indexOf에 파라미터가 두 개일 경우 두번째 숫자 값은 첫번째 파라미터의 글자를 찾기 시작하는 위치
int p2 = msg.indexOf("i");
int p3 = msg.indexOf("i", p2 + 1);
System.out.println("'i'가 첫 번째로 나타나는 위치 : " + p2);
System.out.println("'i'가 두 번째로 나타나는 위치 : " + p3);

//뒤에서 부터 찾는게 아니라 마지막 위치임
int p4 = msg.lastIndexOf("a");
System.out.println("'a'의 마지막 위치: " + p4);

if (msg.indexOf("Hello") > -1 ) {
    System.out.println("Hello가 포함됨");
} else {
    System.out.println("Hello가 포함되지 않음");
}

//0번째부터 지정된 위치의 전까지 잘라내서 출력
String substring1 = msg.substring(0, 18);
System.out.println("문자열 자르기 :" + substring1);

//파라미터가 하나일 경우 지정된 위치부터 끝까지 잘라내서 출력
String substring2 = msg.substring(19);
System.out.println("문자열 자르기: " + substring2);

String up = msg.toUpperCase();
System.out.println("모든 글자의 대문자 변환 : " + up);

String low = msg.toLowerCase();
System.out.println("모든 글자의 소문자 변환: " + low);

//문자열의 앞, 뒤 만의 공백 지우기
String src1 = " Hello World ";
String src2 = src1.trim();
System.out.printf("src1=[%s]\n", src1);
System.out.printf("src2=[%s]\n", src2);

String txt = "HTML,CSS,Javascript";
String[] arr = txt.split(",");

for(int i=0; i < arr.length; i++) {
    System.out.println(arr[i]);
}

String txt2 = txt.replace(",", "$");
System.out.println(txt2);

String txt3 = "Hello";
System.out.println(txt3.equals("hello"));

if(txt3.equals("Hello")) {
    System.out.println("txt3는 Hello 입니다.");
} else {
    System.out.println("txt3는 Hello가 아닙니다.");        
}
```

## 날짜

-현재 시간 구하기
```java
//package + 폴더 경로;
package kr.hossam.helpers;
import java.util.Calendar;

public class DatePrinter {
    public static void printDateTime(Calendar cal) {
        int yy = cal.get(Calendar.YEAR);
        int mm = cal.get(Calendar.MONTH) + 1;
        int dd = cal.get(Calendar.DAY_OF_MONTH);
        int hh = cal.get(Calendar.HOUR_OF_DAY);
        int mi = cal.get(Calendar.MINUTE);
        int ss = cal.get(Calendar.SECOND);

        System.out.printf("%04d년 %02d월 %02d일 %02d시 %02d분 %02d초\n", yy, mm, dd, hh, mi, ss);
    }
}

```

-요일 구하기
```java
//일: 1, 토: 7 JS= 일: 0 ,토: 6
//일주일 중 몇 번째 일인지 구하기
int day = cal.get(Calendar.DAY_OF_WEEK);

String[] day_name = {"일", "월", "화", "수", "목", "금", "토"};
//1부터시작, 일요일부터 시작이고 배열은 0번째 부터시작이므로 -1
System.out.println("요일=" + day_name[day - 1]);
```

-날짜 연산
```java
//100일 후
cal.add(Calendar.DAY_OF_MONTH, 100);
DatePrinter.printDateTime(cal);
//75년 전
cal.add(Calendar.YEAR, -75);
DatePrinter.printDateTime(cal);
```

## 파일 

```java
import java.io.File;

public class Ex02_File2 {
    public static void main(String[] args) {
        File f2 = new File("a/b/c/target");
        System.out.println("isFile: " + f2.isFile());
        System.out.println("isDirectory: " + f2.isDirectory());
        System.out.println("isHidden: " + f2.isHidden());
        System.out.println("절대경로: " + f2.getAbsolutePath());
        System.out.println("존재여부: " + f2.exists());

        f2.mkdirs();

        //마지막 "/" 이후 단어를(파일이나 폴더이름) 리턴
        System.out.println(f2.getName());

        //처음부터 마지막 "/" 직전까지 리턴
        System.out.println(f2.getParent());
        
        System.out.println("---------------------");
    }
}
```

## Gson(Maven)
-maven시작하기 [Gson(Maven).pdf](https://github.com/user-attachments/files/17161137/29-Gson.Maven.pdf)   

## Gradle(lombok)
-gradle시작하기 [Lombok(Gradle).pdf](https://github.com/user-attachments/files/17183113/30-Lombok.Gradle.pdf)   

-java project 생성  
-Gradle 선택    
-저장할 폴더 생성   
-프로젝트 이름 지정   
-main>java>test 폴더 삭제해도 무방   
-app.java package kr.jinsu.프로젝트명로 변경 후 ctrl + . 으로 Move 선택 -> app.java파일 이동 후 원래 있던 폴더 삭제   
-build.gradle 자바버전 변경, dependences 추가할 것 추가   
>maven repository 사이트에서 라이브러리 dependencies 복사   

-jinsu폴더 안에 model, service, helper, exception 등 폴더 추가   

### Error
-내가 설치한 자바버전에 문제가 없는데 버전오류가 뜬다면 build.gradle에서 아래 코드를 찾아 기본설정(21)값을 내 자바 버전으로 바꿔준다.   
```java
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
```


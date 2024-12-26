---
layout: post
title: "[Spring] 배포"
date: 2024-12-22
description: 카페24 호스팅 # Add post description (optional)
img: /postImg/hosting.jpg # Add image post (optional)
fig-caption: hosting # Add figcaption (optional)
tags: [springboot, hosting]
---
# <span style="color:#616161; font-weight:bold;">스프링부트 배포</span>
*jar => 독립실행형

*war => 별도의 웹서버(Tomcat)에 탑재
<br />

## <span style="color:#ffa59c; font-weight:bold;">서버</span>

1) 포트번호 설정 : 8080 => 80 (포트번호 안붙여도 접속할 수 있음)
2) 로그경로
3) 파일업로드 경로

#### 서버의 종류
1) Web 서버 : 업로드되는 파일이 많은 경우 파일서버(HDD 더미)를 별도로 연결함. NAS(나스)라고도 부름
2) DB 서버
3) SSH 서버 : 터미널 원격 접속
4) FTP 서버 : 파일 전송

## <span style="color:#ffa59c; font-weight:bold;">Tomcat</span>

-> tomcat download 검색   

-> 64bit zip 다운로드   

-> 적당한 위치에 압축해제   

-> bin 폴더 내에서 cmd   

-> start.bat   

-> localhost:8080 접속해서 tomcat 내장 페이지가 뜬다면 접속 성공

-> apache 폴더의 webapps   

-> ROOT 폴더 
> 여기 있는 파일들이 tomcat 내장 페이지이고 여기 파일들을 지우고 html 및 asset파일들을 넣으면 8080접속 시 html파일이 열린다   

-> 배포할 spring 프로젝트의 build.gradle에서 plugins { id 'war' } 추가, dependencies 에서 아래 코드 추가 

```
/** Tomcat 배포 */
    provideRuntime 'org.springframework.boot:spring-boot-starter-tomcat'

    bootWar {
        archiveBaseName = 'myshop'
        archiveFileName = 'myshop.war'
        archiveVersion = "0.0.1"
    }

    bootWar.enable = true
    war.enabled = false
```
-> main class(폴더명Application.java)에 아래 코드 추가 : SpringBootServletInitializer 상속 및 재정의

```java

public class MyshopApplication extends SpringBootServletInitializer {

@Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(MyshopApplication.class);
    }
}
```

-> 왼쪽 탭에서 gradle(코끼리 아이콘) 클릭   

-> Tasks > bulid > bootWar 실행   

-> 프로젝트에서 bulid > lips > myshop.war 파일이 생겼는지 확인   

-> war 압축 파일을 tomcat > webapps에 붙여넣기하면 자동으로 압축이 풀림(직접 압축풀기 하지 말 것)   

-> url에 설정한 (myshop/account) 입력하면 접속 가능   

## <span style="color:#ffa59c; font-weight:bold;">카페24에서 호스팅하기</span>
-> 카페24 로그인   

-> footer에서 개별언어별 호스팅    

-> 비즈니스형 ( 트래픽 용량 생각해서 결정. 트래픽 초과되면 홈페이지 접근 불가)   

-> 톰켓 버전, jdk 버전 등 확인 후 결제   

-> 프로젝트로 돌아와서 build.gradle에서 archiveBaseName, archiveFileName을 ROOT로 설정 ( 프로젝트 여러개를 호스팅할거라면 파일이름을 지어주는게 좋음 ROOT는 파일명 없이 바로 접속 가능)   

-> ssh 아이디@주소 -p포트번호   
> 기본포트인 경우 -p포트번호 생략 가능

-> 나의 서비스 관리에   

-> cmd에서 ssh jinsu.cafe24.com 주소 입력 후 서버 비밀번호 입력   

-> Linux 접속 완료   

-> ls -al : 모든 파일 보기 => 파일 앞에 d는 디렉토리 rws rws r-- 이런식으로 수식이 붙음 ( rws r= 읽기 w= 쓰기 s= ?) 첫번째 rws는 나, 두번째는 그룹, 세번째는 그 외 다른사람으로 권한을 나타냄 - 는 권한 없음

-> 폴더경로 바꿔주고 권한부여 해주어야함

-> mkdir myupload   

-> mkdir mylog   

-> logback-spring.xml 뭔가수정...   

-> 폴더경로 : upload.dir=/jinsu/myupload 경로 바꿔주지 않으면 다 404 뜸   

-> zip 폴더 tomcat > webapps에 추가   

-> 데이터베이스도 sql 백업 파일 추가해야함 : mysqldump -uroot -p myschool 

-> 파일질라 홈페이지에서 클라이언트 다운로드 

-> 호스트, 사용자명, 비밀번호, FTP포트 후 빠른연결   

-> 데이터베이스 sql파일, war파일 붙여넣기   

-> Linux에 ls -l 입력해보면 새로 추가한 파일들이 생겼을 것이다.   

-> 백업한 sql파일 들어가서 주석처럼 보이는 줄에서 SET NAMES 삭제   

-> COLLATE=utf8mb4_0900_ai_ci 문자열 삭제   

-> CREATE문의 DEFAULT CHARSET 값을 utfmb8 로 변경   

-> spring_session_attributes 테이블의 복합 PK를 변경 : PRIMARY KEY (`SESSION_PRIMARY_ID`, `ATTRIBUTE_NAME`), =>  PRIMARY KEY (`SESSION_PRIMARY_ID`),   

-> 대소문자를 가리므로 spring_session 테이블의 create문을 대문자로 바꾸어서 추가해야한다.   

-> sql파일 복원 : mysql -u jinsu -p jinsu < DB파일명.sql   

-> mysql을 여기서도 접근 가능해진다.   

-> 주소 url 을 입력하면 접속이 가능한데 자동으로 접근권한 오류가 뜬다면 https가 자동으로 붙었는지 확인 후 제거한다.   

-> cat error





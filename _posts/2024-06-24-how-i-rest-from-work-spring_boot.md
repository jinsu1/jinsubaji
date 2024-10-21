---
layout: post
title: Spring Boot
date: 2024-10-14
description: Spring Boot # Add post description (optional)
img: /postImg/spring.png # Add image post (optional)
fig-caption: Spring Boot # Add figcaption (optional)
tags: [spring, boot, java]
---
# <span style="color:#616161; font-weight:bold;">Spring Boot</span>

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>
#### extention 설치
-Spring Boot Extension Pack   

#### project 생성
-명령창 > spring initialze: create Gradle Project   

-spring 버전 선택 (첫 번째가 보통 안전화된 버전)   

-패키지 이름 지정, 실행파일 이름 지정   

-배포 타입 지정 : jar   

-java Version 선택  : 17 선택 다른 버전이 깔려있다면 17로 다시 설치를 권장 (강요)   

-dependency 선택 :   

    1)Spring Web
    2)Spring Boot DevTools   
    3)Spring Boot Acturator Ops   
    4)Thymeleaf Template Engines  
    5)Lombok   
    6)Java Mail Sender   
    7)JDBC API   
    8)MySQL Driver   
    9)MyBatis Framework   
    직접추가)  : implementation 'com.github.ua-parser:uap-java:1.6.1'   
                 testCompileOnly 'org.projectlombok:lombok'             //test에서 lombok인식이 가능하게 함   
                 testAnnotationProcessor 'org.projectlombok:lombok'   


-setting.json 에 항목추가
    
    1) "java.debug.settings.hotCodeReplace": "auto",  > 디버그시 파일 저장 후 자동으로 코드 반영
    2) "spring.dashboard.openWith": "external",       > 기본 웹브라우저 사용
    3) "spring-boot.ls.java.vmargs": [
        "-Djava.net.preferIPv4Stack=true"
        ]                                             > VSCode가 자바 컴파일 후 프로그램을 실행할 때 JVM에 전달하는 파라미터   



-실행 할 때는 왼쪽 탭의 Spring Boot Dashboard의 app에서 디버그를 이용해서 실행 (위에서 설치했던 extention)   

#### resource/logback-spring.xml   

-logger태그의 name속성에 패키지명을 제대로 입력하지 않으면 로그가 안나오거나 에러가 발생할 수 있음   


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 60초마다 설정 파일의 변경을 확인 하여 변경시 갱신 -->
<configuration scan="true" scanPeriod="60 seconds">
    <!-- log file path -->
    <property name="LOG_PATH" value="logs" />
    <!-- log file name -->
    <property name="LOG_FILE_NAME" value="log" />
    <!-- err log file name -->
    <property name="ERR_LOG_FILE_NAME" value="err_log" />
    <!-- pattern -->
    <property name="LOG_PATTERN" value="[%-5level] %d{yy-MM-dd HH:mm:ss}
    [%logger{0}:%line] - %msg%n" />
 
    <!-- Console Appender -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>
 
    <!-- File Appender -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 파일경로 설정 -->
        <file>${LOG_PATH}/${LOG_FILE_NAME}.log</file>
        <!-- 출력패턴 설정-->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <!-- Rolling 정책 -->
        
        <rollingPolicy
        class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->
            <fileNamePattern>${LOG_PATH}/${LOG_FILE_NAME}.%d{yyyy-MMdd}_%i.log</fileNamePattern>
            <!-- 파일당 최고 용량 kb, mb, gb -->
            <maxFileSize>10MB</maxFileSize>
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로
            제거-->
            <maxHistory>30</maxHistory>
         </rollingPolicy>
    </appender>
    <!-- 에러의 경우 파일에 로그 처리 -->
    <appender name="Error"
    class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>

        <file>${LOG_PATH}/${ERR_LOG_FILE_NAME}.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>UTF-8</charset>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <!-- Rolling 정책 -->
        <rollingPolicy
        class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->
            <fileNamePattern>${LOG_PATH}/${ERR_LOG_FILE_NAME}.%d{yyyy-MMdd}_%i.log</fileNamePattern>
            <!-- 파일당 최고 용량 kb, mb, gb -->
            <maxFileSize>10MB</maxFileSize>
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로
            제거-->
            <maxHistory>60</maxHistory>
        </rollingPolicy>
    </appender>
    <!-- root레벨 설정 -->
    <root level="WARN">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </root>
   
    <!-- 특정패키지 로깅레벨 설정 -->
    <logger name="org.apache.ibatis" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="kr.jinsu.logger" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
</configuration>
```
#### application.properties

```
spring.application.name=database

#-------------------------------------------------
# DATABASE 접속 정보
#-------------------------------------------------
# 기본 구성
#spring.datasource.url=jdbc:mysql://127.0.0.1:3306/myschool?characterEncoding=UTF8
#spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# logback 연동 구성
#spring.datasource.url=jdbc:log4jdbc:mysql://127.0.0.1:3306/myschool?characterEncoding=UTF8
#spring.datasource.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/mytest?characterEncoding=UTF8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=123qwe!@#
```

## <span style="color:#ffa59c; font-weight:bold;">Error</span>
-project 생성 후 왼쪽 하단 상태표시줄에 java Error 오류가 뜬다면 jdk의 경로를 찾지 못하는 것이므로 setting.json에 들어가서 [ "java.import.gradle.java.home": "C:\\jdk-17.0.2" ] 를 추가하고 restart 해주자. java ready가 떠야한다.   

## <span style="color:#ffa59c; font-weight:bold;">Tips</span>

#### common

-read를 제외한 create, update, delete는 뒤로가기가 적용되면 안된다.(history를 남기면 안된다.) 중복 실행 위험성이 있다.   

-historyback()을 사용하여 되돌아가면 controller를 거치지않기 때문에 새로 저장된 내용(쿠키값)이 반영되지 않는다.   

-@Component : 아래 클래스는 bean임을 스프링에게 알려줌. 자동으로 싱글톤패턴 생성. 멤버변수에 @Autowired 어노테이션 해줘야함. 이제 getInstance()는 싱글톤을 안쓰므로 String ipAddr = utilHelper.getClientIp(request);로 instance부분을 ClientIP()로 바꾸어줌   

-test에서 lombok인식되도록하려면 dependencis를 추가해야한다.   

```java
testAnnotationProcessor 'org.projectlombok:lombok'
testCompileOnly 'org.projectlombok:lombok'
```


#### 파일 생성 순서
    1) build.gradle
    2) resources/logback-spring.xml
    3) static/favicon,robot
    4) exceptions
    5) helpers
    6) interceptors
    7) services
    8) serviceImpl
    9) controllers
    10) templates/index.html

#### cookie

1) 유효 도메신 www.naver.com인경우 naver.com으로 설정   

2) 유효시간 설정(0이하면 즉시 삭제, 초 단위)   

3) 설정하지 않을 경우 브라우저 닫기 전까지 유지됨   

#### controller
1) controller에서 mapping 한 url은 접속할 때 사용할 url을 설정하는 것이고 return값이 있다면 그 문자열을 가지고 return 값.html을 찾아감 

2) return 값이 없을 경우 mapping한 url을 파일이름으로 간주하고 url.html을 찾아감

3) @ResponseBody 어노테이션 : return은 하되, mapping한 url을 파일이름으로 간주하고 찾아가고 싶을 때 사용한다. return은 하고싶으나 return값을 url로 사용하고 싶지 않을 때 사용하면 될 것 같다. 


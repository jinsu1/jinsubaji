---
layout: post
title: "[Spring Boot] 실습 예제"
date: 2024-10-23
description: Spring Boot # Add post description (optional)
img: /postImg/spring.png # Add image post (optional)
fig-caption: Spring Boot # Add figcaption (optional)
tags: [spring, boot, java, database]
---
# <span style="color:#616161; font-weight:bold;">Spring Boot 실습 예제</span>

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

#### build.gradle

```
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.3.4'
	id 'io.spring.dependency-management' version '1.1.6'
}

group = 'kr.jinsu'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17)
	}
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-actuator'
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	implementation 'org.springframework.boot:spring-boot-starter-mail'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.3'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	runtimeOnly 'com.mysql:mysql-connector-j'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter-test:3.0.3'
	testRuntimeOnly 'org.junit.platform:-platform-launcher'
    implementation 'com.github.ua-parser:uap-java:1.6.1'
    // https://mvnrepository.com/artifact/org.bgee.log4jdbc-log4j2/log4jdbc-log4j2-jdbc4.1
    implementation 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16'


    //test에서 lombok인식되도록 추가
    testAnnotationProcessor 'org.projectlombok:lombok'
    testCompileOnly 'org.projectlombok:lombok'
}

tasks.named('test') {
	useJUnitPlatform()
}

```

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
    <property name="LOG_PATTERN" value="[%-5level] %d{yy-MM-dd HH:mm:ss} [%logger{0}:%line] - %msg%n" />

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
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->
            <fileNamePattern>${LOG_PATH}/${LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.log</fileNamePattern>
            <!-- 파일당 최고 용량 kb, mb, gb -->
            <maxFileSize>10MB</maxFileSize>
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- 에러의 경우 파일에 로그 처리 -->
    <appender name="Error" class="ch.qos.logback.core.rolling.RollingFileAppender">
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
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->
            <fileNamePattern>${LOG_PATH}/${ERR_LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.log</fileNamePattern>
            <!-- 파일당 최고 용량 kb, mb, gb -->
            <maxFileSize>10MB</maxFileSize>
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->
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
    <!-- 프로젝트 패키지 이름을 지정해야 함 -->
    <logger name="kr.jinsu.database" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>

    <!-- 데이터베이스 관련 패키지에 대한 로그 레벨 설정 -->
    <logger name="jdbc.sqlonly" level="INFO" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="jdbc.sqltiming" level="OFF">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="jdbc.audit" level="OFF">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="jdbc.resultset" level="ERROR">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="jdbc.resultsettable" level="DEBUG" additivity="false">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="jdbc.connection" level="ERROR">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
        <appender-ref ref="Error" />
    </logger>
    <logger name="log4jdbc.debug" level="ERROR">
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
spring.datasource.url=jdbc:log4jdbc:mysql://127.0.0.1:3306/myschool?characterEncoding=UTF8
spring.datasource.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
#spring.datasource.url=jdbc:mysql://127.0.0.1:3306/mytest?characterEncoding=UTF8
#spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=123qwe!@#

```

## <span style="color:#ffa59c; font-weight:bold;">파일구조 정리</span>

-package : kr/jinsu/database/   

-package/<a href="MyWebConfig" style="color:orange">MyWebConfig.java</a>   

-package/controllers/<a href="#controller" style="color:orange">Departmentcontroller.java</a>   

-package/exceptions/<a href="#ServiceNoResultException" style="color:orange">ServiceNoResultException.java</a>   
-package/exceptions/<a href="#StringFormatException" style="color:orange">StringFormatException.java</a>   

-package/helpers/<a href="#WebHelper" style="color:orange">WebHelper.java</a>   

-package/interceptors/<a href="#MyInterceptor" style="color:orange">MyInterceptor.java</a>   

-package/mappers/<a href="#DepartmentMapper" style="color:orange">DepartmentMapper.java</a>   

-package/models/<a href="#Department" style="color:orange">Department.java</a>   

-package/services/<a href="#DepartmentService" style="color:orange">DepartmentService.java</a>   
-package/services/impl/<a href="#DepartmentServiceImpl" style="color:orange">DepartmentServiceImpl.java</a>   

-resources/static/assets/css/<a href="#common" style="color:orange">common.css<span>   
-resources/static/assets/css/<a href="#header" style="color:orange">header.css</a>   

-resources/<a href="#logbackSpring" style="color:orange">logback-spring.xml</a>   

-resources/templates/department/<a href="#add" style="color:orange">add.html</a>   
-resources/templates/department/<a href="#detail" style="color:orange">detail.html</a>   
-resources/templates/department/<a href="#edit" style="color:orange">edit.html</a>   
-resources/templates/department/<a href="#index" style="color:orange">index.html</a>   

-resources/templates/fragments/<a href="#header" style="color:orange">header.html</a>   


## <span id="MyWebConfig" style="color:#ffa59c; font-weight:bold;">MyWebConfig.java</span>

```java
package kr.jinsu.database;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import kr.jinsu.database.interceptors.MyInterceptor;

@Configuration
@SuppressWarnings("null")
public class MyWebConfig implements WebMvcConfigurer {
    
    // MyInterceptor 객체를 자동 주입 받는다
    // 이 과정에서 myInterceptor안에 @Autowired로 선언된 UtilHelper 객체도 자동 주입된다.
    @Autowired
    private MyInterceptor myInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 직접 정의한 MyInterceptor를 Spring에 등록
        InterceptorRegistration ir = registry.addInterceptor(myInterceptor);
        // 해당 경로는 인터셉터가 가로채지 않는다.
        // url /hello 있음.
        ir.excludePathPatterns("/error","/robots.txt", "/favicon.ico","/assets/**");
    }
}

```
## <span id="controller" style="color:#ffa59c; font-weight:bold;">DepartmentConroller.java</span>

```java
package kr.jinsu.database.controllers;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;

import kr.jinsu.database.exceptions.ServiceNoResultException;
import kr.jinsu.database.helpers.WebHelper;
import kr.jinsu.database.models.Department;
import kr.jinsu.database.services.DepartmentService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import jakarta.servlet.http.HttpServletRequest;

import org.springframework.web.bind.annotation.PostMapping;







@Controller
public class DepartmentController {
    /** 학과 관리 서비스 객체 주입 */
    @Autowired
    private DepartmentService departmentService;

    /** WebHelper 주입 */
    @Autowired
    private WebHelper webHelper;

    /**
     * 학과 목록 화면
     * @param model 모델
     * @return  학과 목록 화면을 구현한 View 경로
     */
    @GetMapping("/department")
    public String index(Model model) {
        List<Department> departments = null;

        try {
            departments = departmentService.getList(null);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
            return null;
        } catch (Exception e) {
            webHelper.serverError(e);
            return null;
        }
        
        model.addAttribute("departments", departments);
        return "/department/index";
    }

    @GetMapping("/department/detail/{deptno}")
    public String detail(Model model,
        @PathVariable("deptno") int deptno) {
        
        //조회 조건에 사용할 변수를 Beans에 저장
        Department params = new Department();
        params.setDeptno(deptno);

        //조회 결과를 저장할 객체 선언
        Department department = null;

        try {
            //데이터 조회
            department = departmentService.getItem(params);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
        } catch (Exception e) {
            webHelper.serverError(e);
        }
        
        //View에 데이터 전달
        model.addAttribute("department", department);
        return "/department/detail";
    }
    
    /**
     * 학과 등록 화면
     * @return  학과 등록 화면을 구현한 View 경로
     */
    @GetMapping("department/add")
    public String add() {
        return "/department/add";
    }
    
    @ResponseBody
    @PostMapping("/department/add_ok")
    public void addOk(HttpServletRequest request,
        @RequestParam("dname") String dname,
        @RequestParam("loc") String loc) {

        //정상적인 경로로 접근한 경우 이전 페이지 주소는
        //1) http://localhost/department
        //2) http://localhost/department/detail/학과번호
        //두 가지 경우가 있다.
        String referer = request.getHeader("referer");

        if(referer == null || !referer.contains("/department")) {
            webHelper.badRequest("올바르지 않은 접근입니다.");
            return;
        }

        //저장한 값들을 Beans에 담는다.
        Department department = new Department();
        department.setDname(dname);
        department.setLoc(loc);

        try {
            departmentService.addItem(department);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
        } catch (Exception e) {
            webHelper.serverError(e);
        }

        //insert, update, delete 처리를 수행하는 경우에는 리다이렉트로 이동
        //insert 결과를 확인할 수 있는 상세 페이지로 이동
        // 상세 페이지에 조회 대상의 pk값을 전달해야한다.
        webHelper.redirect("/department/detail/" + department.getDeptno(), "등록되었습니다.");
    }

    @ResponseBody
    @GetMapping("/department/delete/{deptno}")
    public void delete ( HttpServletRequest request,
        @PathVariable("deptno") int deptno) {
        
        String referer = request.getHeader("referer");

        if(referer == null || !referer.contains("/department")) {
            webHelper.badRequest("올바르지 않은 접근입니다.");
            return;
        }

        Department department = new Department();
        department.setDeptno(deptno);

        try {
            departmentService.deleteItem(department);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
        } catch (Exception e) {
            webHelper.serverError(e);
        }

        webHelper.redirect("/department", "삭제되었습니다.");
    }

    /**
     * 학과 수정 페이지
     * @param model - Model 객체
     * @param deptno - 학과 번호
     * @return  View 페이지의 경로
     */
    @GetMapping("/department/edit/{deptno}")
    public String edit(Model model,
        @PathVariable("deptno") int deptno) {
        
        //파라미터로 받은 PK값을 beans객체에 담는다.
        // -> 검색 조건으로 사용하기 위함
        Department params = new Department();
        params.setDeptno(deptno);

        //수정할 데이터의 현재 값을 조회
        Department department = null;

        try {
            department = departmentService.getItem(params);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
        } catch (Exception e) {
            webHelper.serverError(e);
        }

        //View에 데이터 전달
        model.addAttribute("department", department);


        return "/department/edit";
    }

    @ResponseBody
    @PostMapping("/department/edit_ok/{deptno}")
    public void edit_ok(
        @PathVariable("deptno") int deptno,
        @RequestParam("dname") String dname,
        @RequestParam("loc") String loc) {
        
        Department department = new Department();
        department.setDeptno(deptno);
        department.setDname(dname);
        department.setLoc(loc);

        try {
            departmentService.editItem(department);
        } catch (ServiceNoResultException e) {
            webHelper.serverError(e);
        } catch (Exception e) {
            webHelper.serverError(e);
        }

        // 수정 결과를 확인하기 위해서 상세 페이지로 이동
        webHelper.redirect("/department/detail/" + department.getDeptno(), "수정되었습니다.");
    }
}

```

## <span id="ServiceNoResultException" style="color:#ffa59c; font-weight:bold;">ServiceNoResultException.java</span>

```java
package kr.jinsu.database.exceptions;

public class ServiceNoResultException extends Exception {
    public ServiceNoResultException (String message) {
        super(message);
    }
}

```

## <span id="StringFormatException" style="color:#ffa59c; font-weight:bold;">StringFormatException.java</span>

```java
package kr.jinsu.database.exceptions;

public class StringFormatException extends Exception {
    public StringFormatException(String message) {
        super(message);
    }
}
```

## <span id="WebHelper" style="color:#ffa59c; font-weight:bold;">WebHelper.java</span>

```java
package kr.jinsu.database.helpers;

import java.io.IOException;
import java.io.PrintWriter;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Component // 스프링에게 이 클래스가 빈Bean임을 알려줌
public class WebHelper {

    @Autowired
    private HttpServletRequest request;

    @Autowired
    private HttpServletResponse response;

    /**
     * 클라이언트의 IP 주소를 가져오는 메서드
     * 
     * @return        - IP 주소
     */
    public String getClientIp() {
        /** 접근한 웹 브라우저의 IP */
        // 출처: https://velog.io/@chullll/spring-boot-IPv4-%EC%84%A4%EC%A0%95 
        String ip = request.getHeader("X-Forwarded-For"); 
        if (ip == null) { 
            ip = request.getHeader("Proxy-Client-IP"); 
            
        } 
        if (ip == null) { 
            ip = request.getHeader("WL-Proxy-Client-IP"); // 웹로직 
            
        } 
        if (ip == null) { 
            ip = request.getHeader("HTTP_CLIENT_IP"); 
           
        } 
        if (ip == null) { 
            ip = request.getHeader("HTTP_X_FORWARDED_FOR"); 
           
        } 
        if (ip == null) { 
            ip = request.getRemoteAddr(); 
        }

        return ip;
    }

    /**
     *  쿠키값을 저장
     * @param name      -   쿠키 이름
     * @param value     -   쿠키 값
     * @param maxAge    -   쿠키 유효 시간 (0이면 지정 안함, 음수일 경우 즉시 삭제)
     * @param domain    -   쿠키 도메인
     * @param path      -   쿠키 경로
     */
    public void writeCookie (String name, String value, int maxAge, String domain, String path) {
        if(value != null && !value.equals("")) {
            try {
                value = URLEncoder.encode(value, "utf-8");
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }

        Cookie cookie = new Cookie(name, value);
        cookie.setPath(path);

        if(domain != null) {
            cookie.setDomain(domain);
        }

        if(maxAge != 0) {
            cookie.setMaxAge(maxAge);
        }

        response.addCookie(cookie);
    }

    /**
     * 쿠키값을 저장 path값을 "/"로 강제 설정한다.
     * @param name      -   쿠키 이름
     * @param value     -   쿠키 값
     * @param maxAge    -   쿠키 유효 시간 (0이면 지정 안함, 음수일 경우 즉시 삭제)
     * @param domain    -   쿠키 도메인
     * 
     * @see #writeCookie(String, String, int, String, String)
     */
    public void writeCookie (String name, String value, int maxAge, String domain) {
        this.writeCookie(name, value, maxAge, domain, "/");
    }

     /**
     * 쿠키값을 저장 path값을 "/"로, domain을 null로 강제 설정한다.
     * @param name      -   쿠키 이름
     * @param value     -   쿠키 값
     * @param maxAge    -   쿠키 유효 시간 (0이면 지정 안함, 음수일 경우 즉시 삭제)
     * 
     * @see #writeCookie(String, String, int, String, String)
     */
    public void writeCookie (String name, String value, int maxAge) {
        this.writeCookie(name, value, maxAge, null, "/");
    }

     /**
     * 쿠키값을 저장 path값을 "/"로, domain을 null로, maxAge를 0으로 강제 설정한다.
     * @param name      -   쿠키 이름
     * @param value     -   쿠키 값
     * 
     * @see #writeCookie(String, String, int, String, String)
     */
    public void writeCookie (String name, String value) {
        this.writeCookie(name, value, 0, null, "/");
    }

    /**
     * 쿠키값을 삭제
     * @param response 
     * @param name      - 쿠키 이름
     */
    public void deleteCookie(String name) {
        this.writeCookie(name, null, 0, null, "/");
    }

    public void redirect(int statusCode, String url, String message) {
        /**
         * 알림 메세지 표시 후 바로 이전 페이지로 이동
         * HTTP 403 Forbidden 클라이언트 오류 상태 응답 코드는 서버에 요청이 전달되었지만
         * 권한 때문에 거절되었다는 것을 의미
         */
        response.setStatus(statusCode);
        response.setContentType("text/html; charset=UTF-8");

        PrintWriter out = null;
        try {
            out = response.getWriter();
        } catch (IOException e) {
            log.error("응답 출력 스트림 생성 실패", e);
            return;
        }

        if(message != null && !message.equals("")) {
            out.print("<script>");
            out.print("alert('" + message + "');");
            out.print("</script>");
        }

        if(url != null && !url.equals("")) {
            out.println("<meta http-equiv='refresh' content= '0; url=" + url + "' />");
        } else {
            out.println("<script>");
            out.println("history.back()");
            out.println("</script>");
        }

        out.flush();
    }

    /**
     * HTTP 상태 코드를 200으로 설정하고 메세지를 출력한 후, 지정된 페이지로 이동한다.
     * @param url   - 이동할 URL
     * @param message   - 출력할 메세지
     */
    public void redirect(String url, String message) {
        this.redirect(200, url, message);
    }

    /**
     * HTTP 상태 코드를 200으로 설정하고 메세지 출력 없이, 지정된 페이지로 이동한다.
     * @param url   - 이동할 URL
     */
    public void redirect(String url) {
        this.redirect(200, url, null);
    }

    /**
     * HTTP 상태 코드를 설정하고 메세지를 출력 없이 지정된 페이지로 이동한다,
     * @param statusCode    - HTTP 상태 코드
     * @param url   - 이동할 URL
     */
    public void redirect(int statusCode, String url) {
        this.redirect(statusCode, url, null);
    }

    /**
     * 파라미터가 잘못된 경우에 호출할 이전 페이지 이동 기능
     * @param e - 에러정보를 담고 있는 객체. Eception으로 선언했으므로 어떤 하위 객체가 전달되더라도 형변환되어 받는다.
     */
    public void badRequest(Exception e) {
        this.redirect(403, null, e.getMessage());
    }

    /**
     * 파라밈터가 잘못된 경우에 호출할 이전 페이지 이동 기능
     * @param message   - 개발자가 직접 전달하는 에러 메세지
     */
    public void badRequest(String message) {
        this.redirect(403, null, message);
    }

    /**
     * JAVA혹은 SQL쪽에서 잘못된 경우에 호출할 이전 페이지 이동 기능
     * @param e - 에러정보를 담고 있는 객체
     */
    public void serverError(Exception e) {
        String message = e.getMessage().trim().replace("'", "\\'").split(System.lineSeparator())[0];
        this.redirect(500, null, message);
    }

    /**
     * JAVA혹은 SQL쪽에서 잘못된 경우에 호출할 이전 페이지 이동 기능
     * @param message   - 개발자가 직접 전달하는 에러 메세지
     */
    public void serverError(String message) {
        this.redirect(500, null, message);
    }
}

```

## <span id="MyInterceptor" style="color:#ffa59c; font-weight:bold;">MyInterceptor.java</span>
```java
package kr.jinsu.database.interceptors;

import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import kr.jinsu.database.helpers.WebHelper;
import lombok.extern.slf4j.Slf4j;
import ua_parser.Client;
import ua_parser.Parser;


@Slf4j
@Component
@SuppressWarnings("null")
public class MyInterceptor implements HandlerInterceptor {
     /** 페이지의 실행 시작 시각을 저장할 변수 */
     long startTime = 0;

     /** 페이지의 실행 완료 시각을 저장할 변수 */
     long endTime = 0;

     /** WebHelper 객체를 자동 주입 받는다. */
     @Autowired
     private WebHelper webHelper;
     /**
      * Controller 실행 전에 수행되는 메서드
      * 클라이언트(웹브라우저)의 요청을 컨트롤러에 전달 하기 전에 호출된다.
      * return 값으로 boolean 값을 전달하는데 false 인 경우
      * controller를 실행 시키지 않고 요청을 종료한다.
      * 보통 이곳에서 각종 체크작업과 로그를 기록하는 작업을 진행한다.
      */
     @Override
     public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
               throws Exception {
          // log.debug("MyInterceptor.preHandle 실행됨");
          
          log.info("-------- new client connect ----------");

          /** 1) 페이지의 실행 시작 시각을 구한다. */
          startTime = System.currentTimeMillis();

          /** 2) 접속한 클라이언트 정보 확인하기 */
          String ua = request.getHeader("user-agent");
          Parser uaParser = new Parser();
          Client c = uaParser.parse(ua);

          String fmt = "[Client] %s, %s, %s %s, %s %s";

        //   String ipAddr = WebHelper.getInstance().getClientIp(request);
          String ipAddr = webHelper.getClientIp();
          String osVersion = c.os.major + (c.os.minor != null ? "." + c.os.minor : "");
          String uaVersion = c.userAgent.major + (c.userAgent.minor != null ? "." + c.userAgent.minor : "");
          String clientInfo = String.format(fmt, ipAddr, c.device.family, c.os.family, osVersion, c.userAgent.family, uaVersion);

          log.info(clientInfo);

          /** 3) 클라이언트의 요청 정보(URL) 확인하기 */
          // 현재 URL 획득
          String url = request.getRequestURL().toString();

          // GET방식인지, POST방식인지 조회한다.
          String methodName = request.getMethod();

          // URL에서 "?" 이후에 전달되는 GET파라미터 문자열을 모두 가져온다.
          String queryString = request.getQueryString();

          // 가져온 값이 있다면 URL과 결합하여 완전한 URL을 구성한다.
          if(queryString != null) {
               url = url + "?" + queryString;
          }

          // 획득한 정보를 로그로 표시한다.
          log.info(String.format("[%s] %s", methodName, url));

          /** 3) 클라이언트가 전달한 모든 파라미터 확인하기 */
          Map<String, String[]> params = request.getParameterMap();

          for(String key : params.keySet()) {
               String[] value = params.get(key);
               log.info(String.format("(param) <-- %s = %s", key, String.join(",", value)));
          }

          /** 4) 클라이언트가 머물렀던 이전 페이지 확인하기 */
          String referer = request.getHeader("referer");

          // 이전에 머물렀던 페이지가 존재한다면?
          // --> 직전 종료시간과 이번 접속의 시작시간 과의 차이는 이전 페이지에 머문 시간을 의미한다.
          if(referer != null && endTime > 0) {
               log.info(String.format(" - REFERER : time = %d, url = %s ", startTime - endTime, referer));
          }

          return HandlerInterceptor.super.preHandle(request, response, handler);
     }

     /**
      * view 단으로 forward 되기 전에 수행.
      * 컨트롤러 로직이 실행된 이후 호출된다.
      * 컨트롤러 단에서 에러 발생 시 해당 메서드는 수행되지 않는다.
      * request로 넘어온 데이터 가공 시 많이 사용된다.
      */
     @Override
     public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
               @Nullable ModelAndView modelAndView) throws Exception {
          // log.debug("MyInterceptor.postHandle 실행됨");

          /** 1) 컨트롤러의 실행 종료 시각을 가져온다. */
          endTime = System.currentTimeMillis();

          /** 2) 컨트롤러가 실행하는데 걸린 시각을 구한다. */
          log.info(String.format("running time: %d(ms)", endTime - startTime));

          HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
     }

     /**
      * 컨트롤러 종료 후 view가 정상적으로 랜더링 된 후 제일 마지막에 실행이 되는 메서드.
      * (잘 사용 안함)
      */
     @Override
     public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
               @Nullable Exception ex) throws Exception {
          // log.debug("MyInterceptor.afterCompletion 실행됨");
          HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
     }
}
```
## <span id="DepartmentMapper" style="color:#ffa59c; font-weight:bold;">DepartmentMapper.java</span>

```java
package kr.jinsu.database.mappers;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Options;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.ResultMap;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import kr.jinsu.database.models.Department;

@Mapper
public interface DepartmentMapper {
    
    /**
     * 학과 정보를 입력한다.
     * PK값은 파라미터로 전달된 input 객체에 참조로 처리된다.
     * @param input - 입력할 학과 정보에 대한 모델 객체
     * @return  입력된 데이터 수
     */
    @Insert("insert into department (dname, loc) values (#{dname}, #{loc})")
    // insert 문에서 필요한 PK에 대한 옵션 정의
    // --> useGeneratedKeys : auto_increment가 적용된 테이블인 경우 사용
    // --> keyProperty : 파라미터로 전달되는 model 객체에서 PK에 대응되는 멤버변수
    // --> keyColumn : 테이블의 PK 컬럼 명
    @Options(useGeneratedKeys = true, keyProperty = "deptno", keyColumn = "deptno")
    int insert(Department input);

    /**
     * 
     * @param input -   수정할 데이터에 대한 모델 객체
     * @return  수정된 데이터 수
     */
    @Update("update department set dname=#{dname}, loc=#{loc} where deptno=#{deptno}")
    int update(Department input);


    @Delete("delete from department where deptno=#{deptno}")
    int delete(Department input);

    @Select("select deptno, dname, loc from department where deptno=#{deptno}")
    /** 조회 결과와 리턴할 model 객체를 연결하기 위한 규칙 정의
     * --> property : model 객체의 멤버변수 이름
     * --> column : select 문에 명시된 필드 이름 (AS 옵션을 사용할 경우 별칭으로 명시)
     */
    @Results(id="departmentMap", value={
        @Result(property = "deptno", column = "deptno"),
        @Result(property = "dname", column = "dname"),
        @Result(property = "loc", column = "loc")
    })
    Department selectItem(Department input);

    @Select("select deptno, dname, loc from department " +
            "order by deptno desc")
    @ResultMap("departmentMap")
    List<Department> selectList(Department input);
}

```

## <span id="Department" style="color:#ffa59c; font-weight:bold;">Department.java</span>

```java
package kr.jinsu.database.models;

import lombok.Data;

@Data
public class Department {
    private int deptno;
    private String dname;
    private String loc;
}

```
## <span id="DepartmentService" style="color:#ffa59c; font-weight:bold;">DepartmentService.java</span>

```java
package kr.jinsu.database.services;

import java.util.List;

import kr.jinsu.database.exceptions.MyBatisException;
import kr.jinsu.database.exceptions.ServiceNoResultException;
import kr.jinsu.database.models.Department;

/**
 * 하나의 처리를 위해서 두 개 이상의 Mapper 기능을 호출할 필요가 있을 경우
 * 이 인터페이스 구현체(Impl)을 통해서 처리한다.
 * 
 * 1) delete 기능의 경우 삭제된 데이터의 수를 의미하는 int를 리턴
 * 2) 목록 조회 기능의 경우 List<DTO클래스>를 리턴
 * 3) 입력, 수정, 상세조회의 경우는 DTO 클래스를 리턴
 */


public interface DepartmentService {
    /**
     * 학과 정보를 새로 저장하고 저장된 정보를 조회하여 리턴한다.
     * @param params    - 저장할 정보를 담고 있는 Beans
     * @return  Department - 저장된 데이터
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 저장된 데이터가 없는 경우
     */
    public Department addItem(Department params) throws ServiceNoResultException, Exception;

    /**
     *  학과 정보를 수정하고 수정된 정보를 조회하여 리턴한다. 
     * @param params    - 수정할 정보를 담고 있는 Beans
     * @return  Department - 수정된 데이터
     * @throws MyBatisException  - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 수정된 데이터가 없는 경우
     */
    public Department editItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 학과 정보를 삭제한다
     * @param params    - 삭제할 정보를 담고 있는 Beans
     * @return int  - 삭제할 정보의 개수
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 삭제된 데이터가 없는 경우
     */
    public int deleteItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 단일 학과 정보 조회
     * @param params
     * @return
     * @throws MyBatisException
     * @throws ServiceNoResultException
     */
    public Department getItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 전체 학과 정보 조회
     * @param params
     * @return
     * @throws ServiceNoResultException
     * @throws Exception
     */
    public List<Department> getList(Department params) throws ServiceNoResultException, Exception;
} 

```

## <span id="DepartmentServiceImpl" style="color:#ffa59c; font-weight:bold;">DepartmentServiceImpl.java</span>

```java
package kr.jinsu.database.services.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import kr.jinsu.database.exceptions.ServiceNoResultException;
import kr.jinsu.database.mappers.DepartmentMapper;
import kr.jinsu.database.mappers.ProfessorMapper;
import kr.jinsu.database.mappers.StudentMapper;
import kr.jinsu.database.models.Department;
import kr.jinsu.database.models.Professor;
import kr.jinsu.database.models.Student;
import kr.jinsu.database.services.DepartmentService;

/**
 * 학과 관리 기능과 관련된 MyBatis Mapper를 간접적으로 호출하기 위한 기능 명세
 * 
 * (1) 모든 메서드를 재정의 한 직후 리턴값 먼저 정의
 */
@Service
public class DepartmentServiceImpl implements DepartmentService {

    @Autowired
    private DepartmentMapper departmentMapper;

    @Autowired 
    private ProfessorMapper professorMapper;

    @Autowired
    private StudentMapper studentMapper;
    
    @Override
    public Department addItem(Department input) throws ServiceNoResultException, Exception {
        int rows = departmentMapper.insert(input);
        

        if(rows == 0) {
            throw new ServiceNoResultException("저장된 데이터가 없습니다.");
        }

        return departmentMapper.selectItem(input);
    }

    @Override
    public Department editItem(Department input) throws ServiceNoResultException, Exception {
        int rows = departmentMapper.update(input);

        if(rows == 0) {
            throw new ServiceNoResultException("수정된 데이터가 없습니다.");
        }

        return departmentMapper.selectItem(input);
    }

    @Override
    public int deleteItem(Department input) throws ServiceNoResultException, Exception {
        //학과 데이터 삭제를 위해 참조관계에 있는 자식 데이터를 순서대로 삭제
        Student student = new Student();
        student.setDeptno(input.getDeptno());
        studentMapper.deleteByDeptno(student);

        Professor professor = new Professor();
        professor.setDeptno(input.getDeptno());
        professorMapper.deleteByDeptno(professor);

        // delete문 수행 --> 리턴되는 값은 수정된 데이터의 수
        int rows = departmentMapper.delete(input);

        if(rows == 0) {
            throw new ServiceNoResultException("삭제된 데이터가 없습니다.");
        }

        return rows;
    }

    @Override
    public Department getItem(Department input) throws ServiceNoResultException, Exception {
        Department output = departmentMapper.selectItem(input);

        if(output == null) {
            throw new ServiceNoResultException("조회된 데이터가 없습니다.");
        }

        return output;
    }

    @Override
    public List<Department> getList(Department input) throws ServiceNoResultException, Exception {
        return departmentMapper.selectList(input);
    }
}

```

## <span id="common" style="color:#ffa59c; font-weight:bold;">common.css</span>
```css
.mytable {
    border-collapse: collapse;
    border-top: 3px solid #168;
    font-size: 14px;
    text-align: center;
    margin: auto;
    width: 100%;

    th{
        color: #168;
        background: #f0f6f9;
        padding: 10px;
        border: 1px solid #ddd;

        &:first-child {
            border-left: 0;
        }

        &:last-child {
            border-right: 0;
        }
    }

    td {
        padding: 10px;
        border: 1px solid #ddd;

        &:first-child {
            border-left: 0;
        }

        &:last-child {
            border-right: 0;
        }

        &.field {
            padding: 0;
            position: relative;

            input {
                box-sizing: border-box;
                padding: 5px 10px;
                border: 0;

                position:absolute;
                left: 0;
                top: 0;
                width: 100%;
                height: 100%;
            }
        }
    }
}

.text-left {
    text-align: left;
}

.button-container {
    display: flex;
    justify-content: center;
    margin: 15px 0;
}

.mybutton {
    border:none;
    color: white;
    padding: 5px 15px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 12px;
    transition: all .2s ease-in-out;

    &:hover {
        filter: brightness(70%);
    }

    &.green {
        background-color: #04aa6d;
    }

    &.blue {
        background-color: #008cba;
    }

    &.red {
        background-color: #f44336;
    }

    &.black {
        background-color: #555;
    }

    &.gray {
        background-color: #e7e7e7;
        color: black;
    }
}
```

## <span id="header" style="color:#ffa59c; font-weight:bold;">header.css</span>
```css
.gnb {
    width: 100%;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e9ecef;

    a {
        font-size: 20px;
        cursor: pointer;
        text-decoration: none;
        padding-bottom: 2px;
        color: #222;

        &:hover {
            color: #22b8cf;
        }

        &:after {
            content: '|';
            display: inline-block;
            padding: 0 7px;
            color: #ccc;
        }

        &:last-child {
            &:after {
                color: #fff;
            }
        }

        &.active {
            text-decoration: underline;
            color: #22b8cf;
        }
    }
}
```

## <span id="add" style="color:#ffa59c; font-weight:bold;">add.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" th:href="@{/assets/css/common.css}" />
</head>
<body>
    <div th:replace="~{fragments/header :: my-header}"></div>

    <h2>학과관리</h2>
    <hr />
    <form method="POST" th:action="@{/department/add_ok}">
        <table class="mytable">
            <colgroup>
                <col width="150" />
                <col />
            </colgroup>
            <tbody>
                <tr>
                    <th>학과이름</th>
                    <td class="text-left field">
                        <input type="text" name="dname" />
                    </td>
                </tr>
                <tr>
                    <th>학과위치</th>
                    <td class="text-left field">
                        <input type="text" name="loc" />
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="button-container">
            <button type="submit" class="mybutton green">등록</button>
            <a th:href="@{/department}" class="mybutton red">취소</a>
        </div>
    </form>
</body>
</html>
```

## <span id="detail" style="color:#ffa59c; font-weight:bold;">detail.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" th:href="@{/assets/css/common.css}" />
</head>
<body>
    <div th:replace="~{fragments/header :: my-header}"></div>

    <h2>학과관리</h2>
    <hr />
    <table class="mytable">
        <colgroup>
            <col width="150" />
            <col />
        </colgroup>
        <tbody>
            <tr>
                <th>학과번호</th>
                <td class="text-left" th:text="${department.deptno}"></td>
            </tr>
            <tr>
                <th>학과이름</th>
                <td class="text-left" th:text="${department.dname}"></td>
            </tr>
            <tr>
                <th>학과위치</th>
                <td class="text-left" th:text="${department.loc}"></td>
            </tr>
        </tbody>
    </table>
    <div class="button-container">
        <a th:href="@{/department}" class="mybutton green">목록보기</a>
        <a th:href="@{/department/edit/{deptno}(deptno=${department.deptno})}" class="mybutton blue">수정</a>
        <a th:href="@{/department/delete/{deptno}(deptno=${department.deptno})}" class="mybutton red">삭제</a>
    </div>
    
</body>
</html>
```
## <span id="edit" style="color:#ffa59c; font-weight:bold;">edit.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" th:href="@{/assets/css/common.css}" />
</head>
<body>
    <div th:replace="~{fragments/header :: my-header}"></div>

    <h2>학과관리</h2>
    <hr />
    <form method="POST" th:action="@{/department/edit_ok/{deptno}(deptno=${department.deptno})}">
        <table class="mytable">
            <colgroup>
                <col width="150" />
                <col />
            </colgroup>
            <tbody>
                <tr>
                    <th>학과이름</th>
                    <td class="text-left field">
                        <input type="text" name="dname" th:value="${department.dname}" />
                    </td>
                </tr>
                <tr>
                    <th>학과위치</th>
                    <td class="text-left field">
                        <input type="text" name="loc" th:value="${department.loc}" />
                    </td>
                </tr>
            </tbody>
        </table>
        <div class="button-container">
            <button type="submit" class="mybutton green">등록</button>
            <a th:href="@{/department}" class="mybutton red">취소</a>
        </div>
    </form>
</body>
</html>
```

## <span id="index" style="color:#ffa59c; font-weight:bold;">index.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" th:href="@{/assets/css/common.css}" />
</head>
<body>
    <div th:replace="~{fragments/header :: my-header}"></div>

    <h2>학과관리</h2>
    <hr />
    <div class="button-container">
        <a th:href="@{/department/add}" class="mybutton blue">새 학과 등록</a>
    </div>
    <table class="mytable">
        <thead>
            <tr>
                <th>학과번호</th>
                <th>학과이름</th>
                <th>학과위치</th>
                <th>-</th>
            </tr>
        </thead>
        <tbody>
            <tr th:each="department : ${departments}">
                <td th:text="${department.deptno}"></td>
                <!--
                    QueryString 파라미터 설정
                    @{/hello/(param1=${변수1}, params2=#{변수2})}
                    => /hello/?param1=변수1&param2=변수2

                    path 파라미터 설정
                    @{/hello/{param1}/{param2}(param1=${변수1}, param2=${변수2})}
                    => /hello/변수1/변수2

                    path 파라미터 + 쿼리 파라미터가 포함된 URL
                    @{/hello/{param1}(param1=${변수1}, param2=${변수2})}
                    => /hello/변수1?param2=변수2
                -->
                <td><a th:href="@{/department/detail/{deptno}(deptno=${department.deptno})}"
                    th:text="${department.dname}" /></td>
                <td th:text="${department.loc}"></td>
                <td>
                    <a th:href="@{/department/edit/{deptno}(deptno=${department.deptno})}">수정</a>                    
                    <a th:href="@{/department/delete/{deptno}(deptno=${department.deptno})}">삭제</a>                    
                </td>
            </tr>
        </tbody>
    </table>
</body>
</html>
```

## <span id="header" style="color:#ffa59c; font-weight:bold;">header.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello Spring</title>
</head>
<body>
    <header th:fragment="my-header">
        <link rel="stylesheet" th:href="@{/assets/css/header.css}">
        <h1>08-Database</h1>
        <nav class="gnb">
            <a th:href= "@{/department}">학과관리</a>
            <a th:href= "@{/professor}">교수관리</a>
            <a th:href= "@{/student}">학생관리</a>

        </nav>
    </header>
</body>
</html>
```

## <span style="color:#ffa59c; font-weight:bold;">Error</span>

-value값이 null허용인 경우 controller에서 required=false 옵션을 추가하면 null이 허용되고 defaultValue="0"을하면 빈 값일 때 0이 들어간다.   

-숫자인 경우 Integer로 선언해야하며 required로 null을 넣어도 문자열로 인식해서 setComm에서 null일 경우에대한 if문으로 예외처리를 해야한다.

```java
@RequestParam(value = "comm", required=false) Integer comm,
```

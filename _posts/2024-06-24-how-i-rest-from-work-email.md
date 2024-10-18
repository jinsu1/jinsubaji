---
layout: post
title: "[Spring Boot] email 발송"
date: 2024-10-18
description: email 발송 # Add post description (optional)
img: /postImg/mail.png # Add image post (optional)
fig-caption: email 발송 # Add figcaption (optional)
tags: [spring, boot, java, email]
---
# <span style="color:#616161; font-weight:bold;">Email 발송하기</span>

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>


#### dependencies   
```java
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
implementation 'org.springframework.boot:spring-boot-starter-web'
compileOnly 'org.projectlombok:lombok'
developmentOnly 'org.springframework.boot:spring-boot-devtools'
annotationProcessor 'org.projectlombok:lombok'
testImplementation 'org.springframework.boot:spring-boot-starter-test'
testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
implementation 'com.github.ua-parser:uap-java:1.6.1'
implementation 'org.springframework.boot:spring-boot-starter-mail'
```


#### logback-spring.xml 
```xml
<logger name="kr.jinsu.mailer" level="DEBUG" additivity="false">
```

#### application.properties
-gmail 패스키 발급 받기 : 계정 관리 > 보안 > 패스키 및 보안키   

-host 및 port 확인하기 : 계정 관리 > 오른쪽 상단 도움말 > smtp port, host 등 검색   


-password : gmail에서 발급받은 키 (키를 발급받으려면 2차 인증 설정)   

```
spring.application.name=mail

spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=jinsu4205@gmail.com
spring.mail.password=pzhuwxlbckczugrc   
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

-외 필요한 기본 파일들 생성

## <span style="color:#ffa59c; font-weight:bold;">MailController.java</span>
```java
package kr.jinsu.mailer.controllers;

import java.io.UnsupportedEncodingException;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import jakarta.mail.MessagingException;
import jakarta.mail.internet.InternetAddress;
import jakarta.mail.internet.MimeMessage;
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Controller
public class MailController {
    
    @Autowired
    JavaMailSender javaMailSender;
    @GetMapping("/")
    public String index() {
        return "index";
    }

    @PostMapping("/sendmail")
    public String postMethodName(
        @RequestParam("sender-name") String senderName,
        @RequestParam("sender-email") String senderEmail,
        @RequestParam("receiver-name") String receiverName,
        @RequestParam("receiver-email") String receiverEmail,
        @RequestParam("subject") String subject,
        @RequestParam("content") String content
        ) {
            //1)메일 발송 정보 로그 확인
            log.debug("------------------------------------");
            log.debug(String.format("SenderName: %s", senderName));
            log.debug(String.format("SenderEmail: %s", senderEmail));
            log.debug(String.format("receiverName: %s", receiverName));
            log.debug(String.format("receiverEmail: %s", receiverEmail));
            log.debug(String.format("subject: %s", subject));
            log.debug(String.format("content: %s", content));
            log.debug("------------------------------------");
            
            //2)Java Mail 라이브러리를 활용한 메일 발송
            MimeMessage message = javaMailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message);

            //제목, 내용, 수신자 설정
            try {
                helper.setSubject(subject);
                helper.setText(content, true);
                helper.setTo(new InternetAddress(receiverEmail, receiverName, "UTF-8"));
                helper.setFrom(new InternetAddress(receiverEmail, receiverName, "UTF-8"));
            } catch (MessagingException e) {
                // 에러가 발생했음을 사용자에게 alert로 알리고 전 페이지로 이동하는 처리가 필요.
                e.printStackTrace();
            } catch ( UnsupportedEncodingException e) {
                // 에러가 발생했음을 사용자에게 alert로 알리고 전 페이지로 이동하는 처리가 필요.
                e.printStackTrace();
            }

            //메일 보내기
            javaMailSender.send(message);

        return "redirect:/";
    }
}
```
## <span style="color:#ffa59c; font-weight:bold;">index.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h2>Java 메일 발송 연습</h2>
    <form method="post" th:action="@{/sendmail}">
        <div>
            <label for="name">발신인: </label>
            <input type="text" name="sender-name" id="sender-name" />
        </div>
        <div>
            <label for="sender-email">발신이메일: </label>
            <input type="email" name="sender-email" id="sender-email" />
        </div>
        <div>
            <label for="receiver-name">수신인: </label>
            <input type="text" name="receiver-name" id="receiver-name" />
        </div>
        <div>
            <label for="receiver-email">수신이메일: </label>
            <input type="email" name="receiver-email" id="receiver-email" />
        </div>
        <div>
            <label for="subject">메일제목: </label>
            <input type="text" name="subject" id="subject" />
        </div>
        <hr />
        <textarea name="content" id="content"></textarea>
        <hr />
        <input type="submit" value="메일보내기" />
    </form>
    <script src="https://cdn.ckeditor.com/ckeditor5/37.0.1/classic/ckeditor.js"></script>
    <script>
        ClassicEditor.create( document.querySelector('#content') );
    </script>
</body>
</html>
```

## <span style="color:#ffa59c; font-weight:bold;">Error</span>
-userName 인증 실패 : application.properties에서 설정한 본인 이메일로만 전송가능  



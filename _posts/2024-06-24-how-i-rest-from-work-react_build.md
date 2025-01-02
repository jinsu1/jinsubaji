---
layout: post
title: "[React] 빌드 및 스프링 병합"
date: 2024-11-15
description: React&Spring # Add post description (optional)
img: /postImg/react.png # Add image post (optional)
fig-caption: reactJS # Add figcaption (optional)
tags: [react, reactJS, spring]
---
# <span style="color:#616161; font-weight:bold;">Spring에 React에서 만든 대시보드 넣기</span>

<br />

## <span style="color:#ffa59c; font-weight:bold;">1. [Spring] 대시보드를 위한 Contorller 생성</span>
-대시보드 맵핑

```java
package kr.co.sonystore.controllers;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class DashboardController {
    @GetMapping("/dashboard")
    public String dashboard() {
        return "dashboard";
    }
    
}

```

## <span style="color:#ffa59c; font-weight:bold;">2. [React] package.json 수정</span>
-package.json파일에 기존 json데이터를 가져오기 위해 추가했던 `"proxy" : localhost:8080,` 를  "homepage": "/dashboard" 로 수정   

-yarn start로 페이지 정상적으로 작동하는지 확인 ( 데이터는 안나오는게 정상 )

## <span style="color:#ffa59c; font-weight:bold;">3. [React] yarn build</span>

-bulid폴더가 생성되고 그 안에 index.html를 맵핑한 이름으로 바꿔줌. dashboard.html   

## <span style="color:#ffa59c; font-weight:bold;">4. [Spring] templates 추가</span>
-dashboard.html만 복사   

## <span style="color:#ffa59c; font-weight:bold;">5. [Spring] static에 dashboard 폴더 추가</span>

-dashboard.html를 제외한 나머지 파일을 dashboard폴더에 추가   

## <span style="color:#ffa59c; font-weight:bold;">6. [Spring] 재가동</span>


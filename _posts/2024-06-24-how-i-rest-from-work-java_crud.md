---
layout: post
title: "[Java] CRUD"
date: 2024-10-01
description: Java note # Add post description (optional)
img: /postImg/java.png # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [java, create, read, update, delete, crud]
---

# <span style="color:#616161; font-weight:bold;">JAVA CRUD</span>

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>
-json파일 서버 업로드 및 .bat실행

-build.grade dependencies 설정
```java
dependencies {
    // Use JUnit Jupiter for testing.
    testImplementation libs.junit.jupiter

    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'

    // This dependency is used by the application.
    implementation libs.guava

    // https://mvnrepository.com/artifact/com.google.code.gson/gson
    implementation 'com.google.code.gson:gson:2.11.0'

    // https://mvnrepository.com/artifact/org.projectlombok/lombok
    compileOnly 'org.projectlombok:lombok:1.18.34'

    annotationProcessor 'org.projectlombok:lombok:1.18.34'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/retrofit
    implementation 'com.squareup.retrofit2:retrofit:2.11.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/converter-gson
    implementation 'com.squareup.retrofit2:converter-gson:2.11.0'
}

// Apply a specific Java toolchain to ease working on different environments.
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
```
<br/>

## <span style="color:#ffa59c; font-weight:bold;">exception</span>
#### StringFormatException.java
```java
package kr.jinsu.exceptions;

public class StringFormatException extends Exception {
    public StringFormatException(String message) {
        super(message);
    }
}
```
<br/>

## <span style="color:#ffa59c; font-weight:bold;">helpers</span>
```java
package kr.jinsu.helpers;

import okhttp3.OkHttpClient;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class RetrofitHelper {
    private static RetrofitHelper current;

    private OkHttpClient httpClient;

    public static RetrofitHelper getInstance() {
        if (current == null) {
            current = new RetrofitHelper();
        }

        return current;
    }

    public static void freeInstance() {
        current = null;
    }

    private RetrofitHelper() {
        super();
    }
    public Retrofit getRetrofit(String baseUrl) {
         //통신 객체를 생성하기 위한 builder 객체 정의 
        OkHttpClient.Builder httpClientBuilder = new OkHttpClient.Builder();

        //Builder를 통해 통신객체 생성
        OkHttpClient httpClient = httpClientBuilder.build();

        //Retrofit을 생성하는 기능을 수행하는 객체 정의
        Retrofit.Builder builder = new Retrofit.Builder();

        //기본 주소를 지정
        builder.baseUrl(baseUrl);

        //통신 결과를 Gson 객체를 통해 처리하도록 Gson연결 == > JSON 파싱 자동화
        builder.addConverterFactory(GsonConverterFactory.create());

        //통신객체를 연결
        builder.client(httpClient);

        //모든 설정이 반영된 Retrofit 객체 생성
        Retrofit retrofit = builder.build();
        return retrofit;
    }

    public void shutdown() {
        if(httpClient != null) {
            httpClient.dispatcher().executorService().shutdown();
        }
    }
}

```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">model</span>

<br/>

## <span style="color:#ffa59c; font-weight:bold;">professor</span>

<br/>

## <span style="color:#ffa59c; font-weight:bold;">service</span>
 

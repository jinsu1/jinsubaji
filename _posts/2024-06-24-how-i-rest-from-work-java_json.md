---
layout: post
title: Java json데이터 불러오기
date: 2024-09-30
description: Java note # Add post description (optional)
img: /postImg/java.png # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [java, json, retrofit]
---
## Setting
-json 파일준비   

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

## helper
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

## model
-@Data는 lombok을 이용해 getter setter toString 등 자동생성
```java
package kr.jinsu.model;

import lombok.Data;

@Data
public class Titanic {
    private int id;
    private boolean survived;
    private int pclass;
    private String name;
    private String sex;
    private double age;
    private int sibsp;
    private int parch;
    private String ticket;
    private double fare;
    private String cabin;
    private String embarked;
}
```

## service
```java
package kr.jinsu.service;

import java.util.List;

import kr.jinsu.model.Traffic_acc;
import kr.jinsu.model.Titanic;
import kr.jinsu.model.Grade;
import kr.jinsu.model.News;
import retrofit2.Call;
import retrofit2.http.GET;


public interface MyDataService {

    @GET("/news")
    Call<List<News>> getNews();

    @GET("/titanic")
    Call<List<Titanic>> getTitanic();

    @GET("/traffic_acc")
    Call<List<Traffic_acc>> getTraffic_acc();

    @GET("/grade")
    Call<List<Grade>> getGrade();
}
```

## app.java
```java
package kr.jinsu.retrofit_ex;

import java.util.List;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Traffic_acc;
import kr.jinsu.service.MyDataService;
import okhttp3.OkHttpClient;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App03 {
    public static void main(String[] args) {
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit("http://localhost:3001");

        MyDataService service = retrofit.create(MyDataService.class);

        Call<List<Traffic_acc>> call = service.getTraffic_acc();

        call.enqueue(new Callback<List<Traffic_acc>>() {
            @Override
            public void onResponse(Call<List<Traffic_acc>> call, Response<List<Traffic_acc>> response) {
                List<Traffic_acc> list = response.body();

                for(Traffic_acc d : list) {
                    System.out.println(d);
                }
            }

            @Override
            public void onFailure(Call<List<Traffic_acc>> call, Throwable t) {
                System.out.println("[통신 에러 발생~!!]");
                System.out.println(call.toString());
                t.printStackTrace();
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}
```
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
#### RetrofitHelper.java
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
#### Professor.java
```java
package kr.jinsu.model;

import lombok.Data;

@Data
public class Professor {
    private int id;
    private String name;
    private String userid;
    private String position;
    private int sal;
    private String hiredate;
    private int comm;
    private int deptno;
    
}
```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">professor</span>
#### app01 (Create)
```java
package kr.jinsu.professor;

import java.util.Scanner;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Professor;
import kr.jinsu.service.ProfessorService;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App01 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        //문자열 -> 숫자 순으로 입력 받아야한다
        System.out.print("이름: ");
        String name = scanner.nextLine();

        System.out.print("아이디: ");
        String userid = scanner.nextLine();

        System.out.print("직위: ");
        String position = scanner.nextLine();

        System.out.print("입사일: ");
        String hiredate = scanner.nextLine();

        System.out.print("월급: ");
        int sal = scanner.nextInt();

        System.out.print("수수료: ");
        int comm = scanner.nextInt();

        System.out.print("강의실: ");
        int deptno = scanner.nextInt();

        scanner.close();

        //retrofit 객체 생성
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit(ProfessorService.BASE_URL);

        //Post 전송을 위한 서비스 객체 호출 --> 데이터 저장
        ProfessorService ProfessorService = retrofit.create(ProfessorService.class);
        Call<Professor> call = ProfessorService.addProfessor(name, userid, position, sal, hiredate, comm, deptno);

        call.enqueue(new Callback<Professor>() {

           @Override
            public void onResponse(Call<Professor> call, Response<Professor> response) {
                if(response.isSuccessful()) {
                    Professor Professor = response.body();
                    System.out.println("저장된 학과 정보: " + Professor);
                } else {
                    System.out.println("저장 실패");
                }
            }

            @Override
            public void onFailure(Call<Professor> call, Throwable t) {
                System.out.println("서버 연결 실패: " + t.getMessage());
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}
```
<br/>

#### app02 (Read)
```java
package kr.jinsu.professor;

import java.util.Scanner;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Professor;
import kr.jinsu.service.ProfessorService;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App02 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        //문자열 -> 숫자 순으로 입력 받아야한다
        System.out.print("조회할 교수 번호: ");
        int id = scanner.nextInt();

        scanner.close();

        //retrofit 객체 생성
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit(ProfessorService.BASE_URL);

        ProfessorService ProfessorService = retrofit.create(ProfessorService.class);
        Call<Professor> call = ProfessorService.getProfessor(id);

        call.enqueue(new Callback<Professor>() {

           @Override
            public void onResponse(Call<Professor> call, Response<Professor> response) {
                if(response.isSuccessful()) {
                    Professor Professor = response.body();
                    System.out.println("조회된 교수 정보: " + Professor);
                } else {
                    System.out.println("조회 실패");
                }
            }

            @Override
            public void onFailure(Call<Professor> call, Throwable t) {
                System.out.println("서버 연결 실패: " + t.getMessage());
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}
```
<br/>

#### app03 (keyword를 포함한 목록 Read)
```java
package kr.jinsu.professor;

import java.util.List;
import java.util.Scanner;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Professor;
import kr.jinsu.service.ProfessorService;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App03 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("검색할 직위 이름: ");
        String keyword = scanner.nextLine();

        scanner.close();

        //retrofit 객체 생성
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit(ProfessorService.BASE_URL);

        //Post 전송을 위한 서비스 객체 호출 --> 데이터 저장
        ProfessorService professorService = retrofit.create(ProfessorService.class);
        Call<List<Professor>> call = professorService.listProfessor(keyword);

        call.enqueue(new Callback<List<Professor>>() {

           @Override
            public void onResponse(Call<List<Professor>> call, Response<List<Professor>> response) {
                if(response.isSuccessful()) {
                    List<Professor> professor = response.body();

                    for (Professor d : professor) {
                        System.out.println(d);
                    }
                } else {
                    System.out.println("조회 실패");
                }
            }

            @Override
            public void onFailure(Call<List<Professor>> call, Throwable t) {
                System.out.println("서버 연결 실패: " + t.getMessage());
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}

```
<br/>

#### app04 (Update)
```java
package kr.jinsu.professor;

import java.util.Scanner;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Professor;
import kr.jinsu.service.ProfessorService;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App04 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        //문자열 -> 숫자 순으로 입력 받아야한다
        System.out.print("이름: ");
        String name = scanner.nextLine();

        System.out.print("아이디: ");
        String userid = scanner.nextLine();

        System.out.print("직위: ");
        String position = scanner.nextLine();

        System.out.print("입사일: ");
        String hiredate = scanner.nextLine();

        System.out.print("월급: ");
        int sal = scanner.nextInt();

        System.out.print("수수료: ");
        int comm = scanner.nextInt();

        System.out.print("강의실: ");
        int deptno = scanner.nextInt();

        System.out.print("수정할 교수 번호: ");
        int id = scanner.nextInt();

        scanner.close();

        //retrofit 객체 생성
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit(ProfessorService.BASE_URL);

        ProfessorService ProfessorService = retrofit.create(ProfessorService.class);
        Call<Professor> call = ProfessorService.updateProfessor(id, name, userid, position, sal, hiredate, comm, deptno);

        call.enqueue(new Callback<Professor>() {

           @Override
            public void onResponse(Call<Professor> call, Response<Professor> response) {
                if(response.isSuccessful()) {
                    Professor Professor = response.body();
                    System.out.println("수정된 교수 정보: " + Professor);
                } else {
                    System.out.println("수정 실패");
                }
            }

            @Override
            public void onFailure(Call<Professor> call, Throwable t) {
                System.out.println("서버 연결 실패: " + t.getMessage());
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}
```
<br/>

#### app05 (Delete)
```java
package kr.jinsu.professor;

import java.util.Scanner;

import kr.jinsu.helpers.RetrofitHelper;
import kr.jinsu.model.Professor;
import kr.jinsu.service.ProfessorService;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;

public class App05 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("삭제할 교수 번호: ");
        int id = scanner.nextInt();

        scanner.close();

        //retrofit 객체 생성
        Retrofit retrofit = RetrofitHelper.getInstance().getRetrofit(ProfessorService.BASE_URL);

        //Post 전송을 위한 서비스 객체 호출 --> 데이터 저장
        ProfessorService ProfessorService = retrofit.create(ProfessorService.class);
        Call<Professor> call = ProfessorService.deleteProfessor(id);

        call.enqueue(new Callback<Professor>() {

           @Override
            public void onResponse(Call<Professor> call, Response<Professor> response) {
                if(response.isSuccessful()) {
                    System.out.println("삭제 성공");
                } else {
                    System.out.println("삭제 실패");
                }
            }

            @Override
            public void onFailure(Call<Professor> call, Throwable t) {
                System.out.println("서버 연결 실패: " + t.getMessage());
            }
        });

        RetrofitHelper.getInstance().shutdown();
    }
}
```

## <span style="color:#ffa59c; font-weight:bold;">service</span>

#### ProfesserService.java
```java
package kr.jinsu.service;

import java.util.List;

import kr.jinsu.model.Professor;
import retrofit2.Call;
import retrofit2.http.DELETE;
import retrofit2.http.Field;
import retrofit2.http.FormUrlEncoded;
import retrofit2.http.GET;
import retrofit2.http.POST;
import retrofit2.http.PUT;
import retrofit2.http.Path;
import retrofit2.http.Query;

public interface ProfessorService {
    public static final String BASE_URL = "http://localhost:3001";


    /**
     * 학과 정보 저장
     * @param name      학과이름
     * @param userid    닉네임
     * @param position  직위
     * @param sal       월급
     * @param hiredate  입사일
     * @param comm      수수료
     * @param deptno    강의실번호
     * @return          Professor 객체
     */
    @FormUrlEncoded         //post, put, delete 방식 전송일 경우 명시
    @POST("/professor")    //데이터 저장 요청
    Call<Professor> addProfessor(@Field(value = "name", encoded = true) String name,
                                 @Field(value = "userid", encoded = true) String userid,
                                 @Field(value = "position", encoded = true) String position,
                                 @Field(value = "sal", encoded = true) int sal,
                                 @Field(value = "hiredate", encoded = true) String hiredate,
                                 @Field(value = "comm", encoded = true) int comm,
                                 @Field(value = "deptno", encoded = true) int deptno);


    /**
     * 교수 정보 조회
     * @param id        조회할 교수 번호
     * @param userid    닉네임
     * @param position  직위
     * @param sal       월급
     * @param hiredate  입사일
     * @param comm      수수료
     * @param deptno    강의실번호
     * @return          Professor 객체
     */                        
    @GET("/professor/{id}")
    Call<Professor> getProfessor(@Path("id") int id);

    /**
     * 교수 정보 조회 (keyword를 포함하는 목록조회)
     * @param position  직위이름
     * @return  professor List 객체
     */
    @GET("/professor")
    Call<List<Professor>> listProfessor(@Query("position_like") String position);

    /**
     * 교수 정보 수정
     * @param id    
     * @param name
     * @param userid
     * @param position
     * @param sal
     * @param hiredate
     * @param comm
     * @param deptno
     * @return
     */
    @FormUrlEncoded
    @PUT("/professor/{id}")
    Call<Professor> updateProfessor(@Path("id") int id,
                                    @Field("name") String name,
                                    @Field("userid") String userid,
                                    @Field("position") String position,
                                    @Field("sal") int sal,
                                    @Field("hiredate") String hiredate,
                                    @Field("comm") int comm,
                                    @Field("deptno") int deptno);
    /**
     * 학과 정보 삭제
     * @param id    학과번호
     * @return      professor 객체
     */
    @DELETE("/professor/{id}")
    Call<Professor> deleteProfessor(@Path("id") int id);
}

```
 

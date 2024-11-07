---
layout: post
title: spring + js 회원가입 적합성 검사 및 head,script 모듈화
date: 2024-11-07
description: spring + js 회원가입 적합성 검사 # Add post description (optional)
img: /postImg/spring.png # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [javascript ,signup, java, spring, module]
---
# <span style="color:#616161; font-weight:bold;">회원가입 적합성 검사</span>
-입력공란확인   
-최소 글자 수 확인   
-최대 글자 수 확인   
-한글만, 영어만, 숫자만, 영어+숫자만, 한글+숫자만 입력 가능   
-두 입력값 일치 확인   
-이메일 형식 확인   
-전화번호 형식 확인   
-radio, checkbox 선택 확인 및 최소, 최대 갯수 제한   

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>

[12_myshop.zip](https://github.com/user-attachments/files/17656842/12_myshop.zip)

build.gradle   


-controllers >    
AccountController.java   
AccountRestController.java   

-exceptions >   
MyBatisException.java   
StringFormatException.java   

-helpers >
DBHelper.java   
FileHelper.java     
RegexHelper.java   
RestHelper.java   
WebHelper.java   

-interceptors >   
MyInterceptor.java   

-mappers >
MembersMapper.java   

-models >
Members.java   
UploadItem.java   

-services >
MembersService.java   

-services > impl   
MembersServiceImpl.java   

package >   
MyWebconfig.java   

resource > static > assets > css >   
account.css   
reset.css   

js >   
AxiosHelpers.js   
RegexHelper.js   
UtilHelper.js   

templates > _fragments >   
_head.html   
_script.html   

templates > account >   
join.html   

resource >   
application.properties   
log4jdbc.log4j2.properties   
logback-spring.xml   


## <span style="color:#ffa59c; font-weight:bold;">_head.html</span>

-SpringBoot를 이용한 head 모듈화   

```html
<th:block th:fragment="my-head(title)">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title th:text="${title}" />
    <link rel="stylesheet" th:href="@{/assets/css/reset.css}" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/sweetalert2/11.13.1/sweetalert2.min.css" />
</th:block>

```

## <span style="color:#ffa59c; font-weight:bold;">_scripts.html</span>

-SpringBoot를 이용한 footer 모듈화   

```html
<th:block th:fragment="my-scripts">
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/sweetalert2/11.13.1/sweetalert2.all.min.js"></script>
    <script th:src="@{/assets/js/AxiosHelpers.js}"></script>
    <script th:src="@{/assets/js/RegexHelper.js}"></script>
    <script th:src="@{/assets/js/UtilHelper.js}"></script>
</th:block>
```



## <span style="color:#ffa59c; font-weight:bold;">join.html</span>

```html
<!DOCTYPE html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <th:block th:replace="~{/_fragments/_head :: my-head('회원가입')}" />
    <link rel="stylesheet" th:href="@{/assets/css/account.css}" />
</head>
<body>

    <div class="account-container">
        <h2 class="page-title">회원가입</h2>
        <form id="signup-form">
            <!-- 아이디 -->
            <div class="input-container">
                <input type="text" id="user_id" class="input-field" placeholder="아이디" required />
                <button type="button" class="inline-btn" id="id-unique-check">중복 검사</button>
                <input type="hidden" id="id-check" value="N" />
            </div>

            <!-- 비밀번호 -->
            <div class="input-container">
                <input type="password" id="user_pw" class="input-field" placeholder="비밀번호" required />
            </div>

            <!-- 이름 -->
            <div class="input-container">
                <input type="text" id="user_name" class="input-field" placeholder="이름" required />
            </div>

            <!-- 이메일 -->
            <div class="input-container">
                <input type="email" id="email" class="input-field" placeholder="이메일" required />
                <button type="button" class="inline-btn" id="email-unique-check">중복 검사</button>
                <input type="hidden" id="email-check" value="N" />
            </div>

            <!-- 전화번호 -->
            <div class="input-container">
                <input type="tel" id="phone" class="input-field" placeholder="전화번호" required />
            </div>

            <!-- 생년월일 -->
            <div class="input-container">
                <input type="date" id="birthday" class="input-field" placeholder="생년월일" required />
            </div>

            <!-- 성별 (라디오 버튼 형태) -->
            <div class="input-container gender-container">
                <span>성별:</span>
                <label class="gender-label">
                    <input type="radio" name="gender" value="M" class="gender-input" required />
                    <span class="gender-custom"></span>남성
                </label>
                <label class="gender-label">
                    <input type="radio" name="gender" value="F" class="gender-input" />
                    <span class="gender-custom"></span>여성
                </label>
            </div>

            <!-- 우편번호 -->
            <div class="input-container">
                <input type="text" id="postcode" class="input-field" placeholder="우편번호" required readonly />
                <button type="button" class="inline-btn" id="find-postcode">우편번호 찾기</button>
            </div>

            <!-- 주소 -->
            <div class="input-container">
                <input type="text" id="addr1" class="input-field" placeholder="주소" required  readonly />
            </div>
            <div class="input-container">
                <input type="text" id="addr2" class="input-field" placeholder="상세 주소" />
            </div>

            <!-- 사진 -->
            <div class="input-container">
                <input type="file" id="photo" class="input-field" />
            </div>

            <!-- 회원가입 버튼 -->
            <button type="submit" class="btn">회원가입</button>
        </form>

        <div class="form-footer">
            <p>이미 계정이 있으신가요? <a th:href="@{/account/login}">로그인</a></p>
        </div>
    </div>

    <th:block th:replace="~{/_fragments/_scripts :: my-scripts}" />
</body>
</html>
```
## <span style="color:#ffa59c; font-weight:bold;">account.css</span>

```css
@charset "utf-8";

:root {
    /* 색상 변수 */
    --background-color: #f4f6f9;
    --container-bg-color: #fff;
    --box-shadow-color: rgba(0, 0, 0, 0.1);
    --primary-color: #4CAF50;
    --primary-color-hover: #45a049;
    --border-color: #ccc;
    --text-color: #333;
    --link-color: #4CAF50;

    /* 크기 및 패딩 변수 */
    --container-padding: 40px;
    --container-border-radius: 10px;
    --input-padding: 12px;
    --button-padding: 14px;
    --border-radius-small: 4px;
    --border-radius-large: 10px;
    --gap-size: 20px;
    --margin-bottom: 20px;

    /* 글꼴 크기 변수 */
    --font-size-base: 16px;
    --font-size-heading: 24px;

    /* 미디어 쿼리 */
    --media-container-padding: 20px;
}

/* 기본 설정 */
body {
    font-family: 'Arial', sans-serif;
    background-color: var(--background-color);
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
}

.account-container {
    background-color: var(--container-bg-color);
    padding: var(--container-padding);
    border-radius: var(--container-border-radius);
    box-shadow: 0 6px 15px var(--box-shadow-color);
    width: 100%;
    max-width: 400px;
    box-sizing: border-box;
    padding-left: var(--media-container-padding);
    padding-right: var(--media-container-padding);

    /* 공통 - 페이지 제목 */
    .page-title {
        text-align: center;
        color: var(--primary-color);
        font-size: var(--font-size-heading);
        margin-bottom: var(--margin-bottom);
    }

    /* 공통 - 하단부 */
    .form-footer {
        text-align: center;
        margin-top: var(--margin-bottom);

        a {
            color: var(--link-color);
            text-decoration: none;

            &:hover {
                text-decoration: underline;
            }
        }
    }

    /* 공통 - 버튼 */
    .btn {
        width: 100%;
        padding: var(--button-padding);
        background-color: var(--primary-color);
        color: white;
        border: none;
        border-radius: var(--border-radius-small);
        cursor: pointer;
        font-size: var(--font-size-base);
        margin-top: var(--margin-bottom);

        &:hover {
            background-color: var(--primary-color-hover);
        }
    }

    /* before.html, after.html - 로그인 전,후 화면 */
    &.account {
        text-align: center;
        padding: 20px;

        .btn-container {
            display: flex;
            gap: 10px;
            justify-content: center;
            margin-top: 20px;
        }
    }

    /* join.html */
    .input-container {
        position: relative;
        margin-bottom: 15px;
        display: flex;

        /* 입력 필드 */
        .input-field {
            flex: 1;
            padding: var(--input-padding);
            border: 1px solid var(--border-color);
            border-radius: var(--border-radius-small) 0 0 var(--border-radius-small);
            box-sizing: border-box;
            font-size: var(--font-size-base);

            &:focus {
                border-color: var(--primary-color);
                outline: none;
            }
        }

        .inline-btn {
            padding: var(--input-padding);
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 0 var(--border-radius-small) var(--border-radius-small) 0;
            cursor: pointer;
            font-size: var(--font-size-base);

            &:hover {
                background-color: var(--primary-color-hover);
            }
        }

        /* 성별 라디오 버튼 스타일 */
        &.gender-container {
            display: flex;
            align-items: center;
            gap: var(--gap-size);
            padding: 10px 0;

            .gender-label {
                display: flex;
                align-items: center;
                cursor: pointer;
                font-size: var(--font-size-base);
                color: var(--text-color);

                .gender-input {
                    display: none;
                }

                .gender-input + .gender-custom {
                    display: inline-block;
                    width: 16px;
                    height: 16px;
                    margin-right: 8px;
                    border: 2px solid var(--border-color);
                    border-radius: 50%;
                    position: relative;
                }

                .gender-input:checked + .gender-custom {
                    border-color: var(--primary-color);
                    background-color: var(--primary-color);
                }
            }
        }
    }
}


/* 미디어 쿼리: 화면 너비가 400px 이하일 때 */
@media (max-width: 400px) {
    .container {
        width: 100%;
        padding-left: var(--media-container-padding);
        padding-right: var(--media-container-padding);
    }
}

```

## <span style="color:#ffa59c; font-weight:bold;">RegexHelper.js</span>

```javascript
class StringFormatException extends Error {
    //입력 요소에 대한 selector
    #selector;

    constructor(msg = '잘못된 요청 입니다.', selector = undefined) {
        super(msg);
        super.name = 'StringFormatException';
        this.#selector = selector;
    }

    //입력요소의 selector에 대한 getter
    get selector() {
        return this.selector;
    }
    
    //입력요소의 selector에 해당하는 HTMLElement 객체 반환
    get element() {
        const el = this.#selector !== null ? document.querySelector(this.#selector) : undefined;
        return el;
    }
}

/**
 * 정규표현식을 기반으로 입력값에 대한 유효성 검사를 수행하는 클래스.
 * HTML 문서에서 사용하기 위해 input selector에 대한 입력값을 검사한다.
 */

class RegexHelper {
    /**
     * 값의 존재 여부를 검사한다.
     * @param {string} selector  검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg       값이 없을 경우 표시할 메세지 내용
     * 
     * ex) regexHelper.value('#user_id', '아이디를 입력하세요.');
     */
    value(selector, msg) {
        const content = document.querySelector(selector).value;

        if(content === undefined || content ===  null || (typeof content === 'string' && content.trim().length === 0)) {
            throw new StringFormatException(msg, selector);
        }

        return true;
    }

    /**
     * 입력값이 지정된 글자수를 초과했는지 검사한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {int} len            최대 글자 수
     * @param {string} msg         값이 없을 경우 표시될 메세지
     */
    maxLength(selector, len, msg) {
        this.value(selector, msg);

        const content = document.querySelector(selector).value;

        if(content.trim().length > len) {
            throw new StrignFormatException(msg, selector);
        }
        return true;
    }

     /**
     * 입력값이 지정된 글자 수 미만인지 검사한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {int} len            최소 글자 수
     * @param {string} msg         값이 없을 경우 표시될 메세지
     */
     minLength(selector, len, msg) {
        this.value(selector, msg);

        const content = document.querySelector(selector).value;

        if(content.trim().length < len) {
            throw new StringFormatException(msg, selector);
        }
        return true;
    }

    /**
     * 두 값이 동일한지 확인한다(비밀번호 확인)
     * @param {string} origin   원본에 대한 <input>요소의 selector
     * @param {string} compare  검사 대상에 대한 <input>요소의 selector
     * @param {string} msg      검사에 실패할 경우 표시할 메세지
     * @returns 
     */
    compareTo(origin, compare, msg) {
        this.value(origin, msg);
        this.value(compare, msg);

        let src = document.querySelector(origin).value.trim(); //원본값
        let dsc = document.querySelector(compare).value.trim(); // 비교할 값

        if (src !== dsc) {
            throw new StringFormatException(msg,origin);
        }
        return true;
    }

    /**
     * 라디오나 체크박스가 선택된 항목인지 확인한다.
     * @param {string} selector  검사할 checkbox에 대한 selector
     * @param {string} msg       검사에 실패할 경우 표시할 메세지
     */
    check(selector, msg) {
        const elList = document.querySelectorAll(selector);
        const checkedItem = Array.from(elList).filter((v, i) => v.checked);

        if(checkedItem == 0) {
            throw new StringFormatException(msg, selector);
        }
    }

    /**
     * 라디오나 체크박스의 최소 선택 갯수를 제한한다.
     * @param {string} selector  검사할 checkbox에 대한 selector
     * @param {string} msg 검사에 실패할 경우 표시할 메세지
     */
    checkMin(selector, len, msg) {
        const elList = document.querySelectorAll(selector);
        const checkedItem = Array.from(elList).filter((v, i) => v.checked);

        if(checkedItem.length < len ) {
            throw new StringFormatException(msg, selector);
        }
    }

    /**
     * 라디오나 체크박스의 최대 선택 갯수를 제한한다.
     * @param {string} selector  검사할 checkbox에 대한 selector
     * @param {string} msg 검사에 실패할 경우 표시할 메세지
     */
    checkMax(selector, len, msg) {
        const elList = document.querySelectorAll(selector);
        const checkedItem = Array.from(elList).filter((v, i) => v.checked);

        if(checkedItem.length > len ) {
            throw new StringFormatException(msg, selector);
        }
    }

    /**
     * 입력값이 정규표현식을 충족하는지 검사한다.
     * @param {string} selector  검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg       표시할 메세지
     * @param {object} regexExpr 검사할 정규표현식
     * @returns 
     */
    selector(selector, msg, regexExpr) {
        this.value(selector, msg);

        //입력값에 대한 정규표현식 검사가 실패라면?
        if(!regexExpr.test(document.querySelector(selector). value.trim())) {
            throw new StringFormatException(msg, selector);
        }
        return true;
    }

    /**
     * 숫자로만 이루어 졌는지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     * @returns 
     */
    num(selector, msg) {
        return this.selector(selector, msg, /^[0-9]*$/);
    }

    /**
     * 영문으로만 이루어 졌는지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    eng(selector, msg) {
        return this.selector(selector, msg, /^[a-zA-Z]*$/);
    }

    /**
     * 한글로만 이루어 졌는지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    kor(selector, msg) {
        return this.selector(selector, msg, /^[ㄱ-ㅎ가-힣]*$/);
    }

    /**
     * 영문과 숫자로 이루어 졌는지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    engNum(selector, msg) {
        return this.selector(selector, msg, /^[a-zA-Z0-9]*$/);
    }

    /**
     * 한글과 숫자로 이루어 졌는지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    korNum(selector, msg) {
        return this.selector(selector, msg, /^[ㄱ-ㅎ가-힣0-9]*$/);
    }

    /**
     * 이메일주소 형식인지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    email(selector, msg) {
        return this.selector(selector, msg, /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i);
    }

    /**
     * 핸드폰 번호 형식인지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    cellphone(selector, msg) {
        return this.selector(selector, msg, /^01(?:0|1|[6-9])(?:\d{3}|\d{4})\d{4}$/);
    }

     /**
     * 집전화 형식인지 검사하기 위해 selector()를 간접적으로 호출한다.
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
     telphone(selector, msg) {
        return this.selector(selector, msg, /^\d{2,3}\d{3,4}\d{4}$/);
    }

    /**
     * 핸드폰번호 형식과 집전화번호 형식 둘 중 하나를 충족하는지 검사
     * @param {string} selector    검사할 대상에 대한 <input>요소의 selector
     * @param {string} msg         표시할 메세지
     */
    phone(selector, msg) {
        this.value(selector, msg);

        const content = document.querySelector(selector).value.trim();
        let check1 = /^01(?:0|1|[6-9])(?:\d{3}|\d{4})\d{4}$/; //핸드폰 형식
        let check2 = /^\d{2,3}\d{3,4}\d{4}$/; //집전화 형식

        //핸드폰 형식이아니고 집전화 형식도 아니라면?
        if(!check1.test(content) && !check2.test(content)) {
            throw new StringFormatException(msg, selector);
        }
        return true;
    }
}

const regexHelper = new RegexHelper();
```

## <span style="color:#ffa59c; font-weight:bold;">MembersMapper.java</span>

-중복검사를 위한 count SQL문 추가   

```java
package kr.jinsu.myshop.mappers;

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

import kr.jinsu.myshop.models.Members;

@Mapper
public interface MembersMapper {
    
    /**
     * 회원 정보를 입력한다.
     * PK값은 파라미터로 전달된 input 객체에 참조로 처리된다.
     * @param input - 입력할 학과 정보에 대한 모델 객체
     * @return  입력된 데이터 수
     */
    @Insert("insert into members (" +
            "user_id, user_pw, user_name, email, phone, " +
            "birthday, gender, postcode, addr1, addr2, " + 
            "photo, is_out, is_admin, login_date, reg_date, " + 
            "edit_date " +
            ") values (" +
            "#{userId}, MD5(#{userPw}), #{userName}, #{email}, #{phone}, " +
            "#{birthday}, #{gender}, #{postcode}, #{addr1}, #{addr2}, " + 
            "#{photo}, 'N', 'N', now(), now(), " + 
            "now())")
    // insert 문에서 필요한 PK에 대한 옵션 정의
    // --> useGeneratedKeys : auto_increment가 적용된 테이블인 경우 사용
    // --> keyProperty : 파라미터로 전달되는 model 객체에서 PK에 대응되는 멤버변수
    // --> keyColumn : 테이블의 PK 컬럼 명
    @Options(useGeneratedKeys = true, keyProperty = "id", keyColumn = "id")
    int insert(Members input);

    /**
     * 
     * @param input -   수정할 데이터에 대한 모델 객체
     * @return  수정된 데이터 수
     */
    @Update("update members set " +
            "user_id=#{userId}, " + 
            "user_pw=#{userPw}, " +
            "user_name=#{userName}, " +
            "email=#{email}, " + 
            "phone=#{phone}, " + 
            "birthday=#{birthday}, " + 
            "gender=#{gender}, " + 
            "postcode=#{postcode}, " + 
            "addr1=#{addr1}, " + 
            "addr2=#{addr2}, " + 
            "photo=#{photo}, " + 
            "edit_date=now() " + 
            "where id=#{id}")
    int update(Members input);


    @Delete("delete from members where id=#{id}")
    int delete(Members input);

    @Select("select " + 
            "user_id, user_pw, user_name, email, phone, " + 
            "birthday, gender, postcode, addr1, addr2, " + 
            "photo, is_out, is_admin, login_date, reg_date, " + 
            "edit_date " + 
            "from members " + 
            "where id=#{id}")
    /** 조회 결과와 리턴할 model 객체를 연결하기 위한 규칙 정의
     * --> property : model 객체의 멤버변수 이름
     * --> column : select 문에 명시된 필드 이름 (AS 옵션을 사용할 경우 별칭으로 명시)
     */
    @Results(id="membersMap", value={
        @Result(property = "id", column = "id"),
        @Result(property = "userId", column = "user_id"),
        @Result(property = "userPw", column = "user_pw"),
        @Result(property = "userName", column = "user_name"),
        @Result(property = "email", column = "email"),
        @Result(property = "phone", column = "phone"),
        @Result(property = "birthday", column = "birthday"),
        @Result(property = "gender", column = "gender"),
        @Result(property = "postcode", column = "postcode"),
        @Result(property = "addr1", column = "addr1"),
        @Result(property = "addr2", column = "addr2"),
        @Result(property = "photo", column = "photo"),
        @Result(property = "isOut", column = "is_out"),
        @Result(property = "isAdmin", column = "is_admin"),
        @Result(property = "loginDate", column = "login_date"),
        @Result(property = "regDate", column = "reg_date"),
        @Result(property = "editDate", column = "edit_date")
    })
    Members selectItem(Members input);

    @Select("select " + 
            "id, " +
            "user_id, user_pw, user_name, email, phone, " + 
            "birthday, gender, postcode, addr1, addr2, " + 
            "photo, is_out, is_admin, login_date, reg_date, " + 
            "edit_date " + 
            "from members " +
            "order by id desc")
    @ResultMap("membersMap")
    List<Members> selectList(Members input);

    @Select("<script>" +
            "select count(*) from members\n" +
            "<where>\n" +
            "<if test='userId != null'>user_id = #{userId}</if>\n" +
            "<if test='email != null'>email = #{email}</if>\n" +
            "</where>\n" +
            "</script>")
    public int selectCount(Members input);
}

```

## <span style="color:#ffa59c; font-weight:bold;">MembersService.java</span>

-중복검사 내용 추가   

```java
package kr.jinsu.myshop.services;

import java.util.List;

import kr.jinsu.myshop.exceptions.MyBatisException;
import kr.jinsu.myshop.exceptions.ServiceNoResultException;
import kr.jinsu.myshop.models.Members;

/**
 * 하나의 처리를 위해서 두 개 이상의 Mapper 기능을 호출할 필요가 있을 경우
 * 이 인터페이스 구현체(Impl)을 통해서 처리한다.
 * 
 * 1) delete 기능의 경우 삭제된 데이터의 수를 의미하는 int를 리턴
 * 2) 목록 조회 기능의 경우 List<DTO클래스>를 리턴
 * 3) 입력, 수정, 상세조회의 경우는 DTO 클래스를 리턴
 */


public interface MembersService {
    /**
     * 회원 정보를 새로 저장하고 저장된 정보를 조회하여 리턴한다.
     * @param params    - 저장할 정보를 담고 있는 Beans
     * @return  Members - 저장된 데이터
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 저장된 데이터가 없는 경우
     */
    public Members addItem(Members params) throws ServiceNoResultException, Exception;

    /**
     *  회원 정보를 수정하고 수정된 정보를 조회하여 리턴한다. 
     * @param params    - 수정할 정보를 담고 있는 Beans
     * @return  Members - 수정된 데이터
     * @throws MyBatisException  - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 수정된 데이터가 없는 경우
     */
    public Members editItem(Members params) throws ServiceNoResultException, Exception;

    /**
     * 회원 정보를 삭제한다
     * @param params    - 삭제할 정보를 담고 있는 Beans
     * @return int  - 삭제할 정보의 개수
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 삭제된 데이터가 없는 경우
     */
    public int deleteItem(Members params) throws ServiceNoResultException, Exception;

    /**
     * 단일 회원 정보 조회
     * @param params
     * @return
     * @throws MyBatisException
     * @throws ServiceNoResultException
     */
    public Members getItem(Members params) throws ServiceNoResultException, Exception;

    /**
     * 전체 회원 정보 조회
     * @param params
     * @return
     * @throws ServiceNoResultException
     * @throws Exception
     */
    public List<Members> getList(Members params) throws ServiceNoResultException, Exception;

    public void isUniqueUserId(String userId) throws Exception;

    public void isUniqueEmail(String email) throws Exception;
} 

```


## <span style="color:#ffa59c; font-weight:bold;">MembersServiceImpl.java</span>

```java
package kr.jinsu.myshop.services.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import kr.jinsu.myshop.exceptions.ServiceNoResultException;
import kr.jinsu.myshop.mappers.MembersMapper;
import kr.jinsu.myshop.models.Members;
import kr.jinsu.myshop.services.MembersService;
import lombok.extern.slf4j.Slf4j;

/**
 * 회원 관리 기능과 관련된 MyBatis Mapper를 간접적으로 호출하기 위한 기능 명세
 * 
 * (1) 모든 메서드를 재정의 한 직후 리턴값 먼저 정의
 */
@Slf4j
@Service
public class MembersServiceImpl implements MembersService {

    @Autowired
    private MembersMapper membersMapper;

    @Override
    public Members addItem(Members input) throws ServiceNoResultException, Exception {
        int rows = membersMapper.insert(input);
        

        if(rows == 0) {
            throw new ServiceNoResultException("저장된 데이터가 없습니다.");
        }

        return membersMapper.selectItem(input);
    }

    @Override
    public Members editItem(Members input) throws ServiceNoResultException, Exception {
        int rows = membersMapper.update(input);

        if(rows == 0) {
            throw new ServiceNoResultException("수정된 데이터가 없습니다.");
        }

        return membersMapper.selectItem(input);
    }

    @Override
    public int deleteItem(Members input) throws ServiceNoResultException, Exception {
        // delete문 수행 --> 리턴되는 값은 수정된 데이터의 수
        int rows = membersMapper.delete(input);

        if(rows == 0) {
            throw new ServiceNoResultException("삭제된 데이터가 없습니다.");
        }

        return rows;
    }

    @Override
    public Members getItem(Members input) throws ServiceNoResultException, Exception {
        Members output = membersMapper.selectItem(input);

        if(output == null) {
            throw new ServiceNoResultException("조회된 데이터가 없습니다.");
        }

        return output;
    }

    @Override
    public List<Members> getList(Members input) throws ServiceNoResultException, Exception {
        return membersMapper.selectList(input);
    }

    @Override
    public void isUniqueUserId(String userId) throws Exception {
        Members input = new Members();
        input.setUserId(userId);

        int output = 0;

        try{
            output = membersMapper.selectCount(input);

            if (output > 0) {
                throw new Exception("사용할 수 없는 아이디 입니다.");
            }
        } catch (Exception e) {
            log.error("아이디 중복 검사에 실패했습니다.", e);
            throw e;
        }
    }

    @Override
    public void isUniqueEmail(String email) throws Exception {
        Members input = new Members();
        input.setEmail(email);

        int output = 0;

        try{
            output = membersMapper.selectCount(input);

            if (output > 0) {
                throw new Exception("사용할 수 없는 이메일 입니다.");
            }
        } catch (Exception e) {
            log.error("이메일 중복 검사에 실패했습니다.", e);
            throw e;
        }
    }
}

```

## <span style="color:#ffa59c; font-weight:bold;">AccountController.java</span>

```java
package kr.jinsu.myshop.controllers;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;


@Controller
public class AccountController {
    @GetMapping("/account/after")
    public String after() {
        return "account/after";
    }

    @GetMapping("/account/before")
    public String before() {
        return "account/before";
    }
    
    @GetMapping("/account/find_id")
    public String findId() {
        return "account/find_id";
    }

    @GetMapping("/account/join")
    public String join() {
        return "account/join";
    }

    @GetMapping("/account/login")
    public String login() {
        return "account/login";
    }

    @GetMapping("/account/out")
    public String out() {
        return "account/out";
    }

    @GetMapping("/account/reset_pw")
    public String reset_pw() {
        return "account/reset_pw";
    }
}

```

## <span style="color:#ffa59c; font-weight:bold;">AccountRestController.java</span>

```java
package kr.jinsu.myshop.controllers;

import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RestController;

import kr.jinsu.myshop.helpers.RestHelper;
import kr.jinsu.myshop.services.MembersService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;


@RestController
public class AccountRestContoller {
    @Autowired
    private RestHelper restHelper;

    @Autowired
    private MembersService membersService;

    @GetMapping("/api/account/id_unique_check")
    public Map<String, Object> idUniqueCheck( @RequestParam("user_id") String userId) {
        try {
            membersService.isUniqueUserId(userId);
        } catch (Exception e) {
            return restHelper.badRequest(e);
        }

        return restHelper.sendJson();
    }
    
    @GetMapping("/api/account/email_unique_check")
    public Map<String, Object> emailUniqueCheck( @RequestParam("email") String email) {
        try {
            membersService.isUniqueEmail(email);
        } catch (Exception e) {
            return restHelper.badRequest(e);
        }

        return restHelper.sendJson();
    }

    
}

```
---
layout: post
title: 자바스크립트 회원가입 적합성 검사
date: 2024-09-04
description: 자바스크립트 회원가입 적합성 검사 # Add post description (optional)
img: /postImg/javascript.jpeg # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [javascript ,signup]
---
## 자바스크립트 회원가입 적합성 검사 
-입력공란확인   
-최소 글자 수 확인   
-최대 글자 수 확인   
-한글만, 영어만, 숫자만, 영어+숫자만, 한글+숫자만 입력 가능   
-두 입력값 일치 확인   
-이메일 형식 확인   
-전화번호 형식 확인   
-radio, checkbox 선택 확인 및 최소, 최대 갯수 제한   

```html
<form class="form-horizontal" name="join_form" id="join_form">
    <div class="field-container">
        <label for="user_id" class="field-title">아이디 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="text" name="user_id" id="user_id" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="user_pw" class="field-title">비밀번호 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="password" name="user_pw" id="user_pw" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="user_pw" class="field-title">비밀번호 확인 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="password" name="user_pw_re" id="user_pw_re" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="user_name" class="field-title">이름 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="text" name="user_name" id="user_name" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="email" class="field-title">이메일 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="email" name="email" id="email" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="tel" class="field-title">연락처 <span class="identify">*</span></label>
        <div class="field-content">
            <input type="tel" name="tel" id="tel" class="form-control" />
        </div>
    </div>
    <div class="field-container">
        <label for="gender" class="field-title">성별 <span class="identify">*</span></label>
        <div class="field-content">
            <label><input type="radio" name="gender" id="gender1" value="M" />남자</label>
            <label><input type="radio" name="gender" id="gender2" value="F" />여자</label>
        </div>
    </div>
    <div class="field-container">
        <label for="subject" class="field-title">수강과목 <span class="identify">*</span></label>
        <div class="field-content">
            <label><input type="checkbox" name="subject" id="subject1" value="html" />HTML</label>
            <label><input type="checkbox" name="subject" id="subject2" value="css" />CSS</label>
            <label><input type="checkbox" name="subject" id="subject3" value="js" />Javascript</label>
            <label><input type="checkbox" name="subject" id="subject4" value="jquery" />jQuery</label>
            <label><input type="checkbox" name="subject" id="subject5" value="php" />PHP</label>
        </div>
    </div>
    <div class="button-container">
        <button type="subject">가입하기</button>
        <button type="reset">다시작성</button>
    </div>
</form>
```

```css
.form-horizontal {
    border: 5px solid #d5d5d5;
    width: 500px;
    margin: auto;
    padding: 0 10px;
    font-size: 14px;
}

.field-container {
    display: flex;
    width: 100%;
    margin: 15px 0;
    align-items: center;
}

.field-title {
    width: 25%;
    display: block;
}

.field-content {
    width: 75%;
    display: block;
}

.field-content .form-control {
    width: 100%;
    padding: 5px 0;
    font-size: 14px;
    border: 1px solid #eee;
}

.field-content input[type='checkbox'], .field-content inputp[type='radio'] {
    margin-left: 10px;
}

.identify{
    font-size: 16px;
    color: #f00;
    font-weight: 500;
    vertical-align: middle;

}

.button-container {
    margin: 20px auto;
    text-align: center;
}
```

### RegexHelper.js

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

```javascript
<script src="js/RegexHelper.js"></script>

document.querySelector("#join_form").addEventListener("submit", e => {
    e.preventDefault();

    try {
        /** 아이디 검사 */
        regexHelper.value('#user_id', '아이디를 입력하세요.');
        regexHelper.minLength('#user_id', 4, '아이디는 최소 4글자 이상 입력 가능합니다.');
        regexHelper.maxLength('#user_id', 20, '아이디는 최대 20글자까지만 입력 가능합니다.');
        regexHelper.engNum('#user_id', '아이디는 영어+숫자로만 입력하세요.');

        regexHelper.value('#user_pw', '비밀번호를 입력하세요.');
        regexHelper.minLength('#user_pw', 4, '비밀번호는 최소 4글자 이상 입력 가능합니다.');
        regexHelper.maxLength('#user_pw', 20, '비밀번호는 최대 20글자까지만 입력 가능합니다.');

        regexHelper.value('#user_pw_re', '비밀번호 확인값을 입력하세요.');
        regexHelper.compareTo('#user_pw', '#user_pw_re', '비밀번호 확인이 일치하지 않습니다.');

        regexHelper.value('#user_name', '이름을 입력하세요.');
        regexHelper.minLength('#user_name', 2, '이름은 최소 2글자 이상 입력 가능합니다.');
        regexHelper.maxLength('#user_name', 10, '이름은 최대 20글자까지만 입력 가능합니다.');
        regexHelper.kor('#user_name', '이름은 한글로만 입력 가능합니다.');
        
        regexHelper.value('#email', '이메일을 입력하세요.');
        regexHelper.email('#email', '이메일 형식이 잘못되었습니다..');

        regexHelper.value('#tel', '연락처를 입력하세요.');
        regexHelper.phone('#tel', '연락처 형식이 잘못되었습니다.');

        regexHelper.check('input[name="gender"]', '성별을 선택하세요.');

        regexHelper.check('input[name="subject"]', '수강과목을 선택하세요.');
        regexHelper.checkMin('input[name="subject"]', 2, '수강과목은 최소 2개 이상 선택해야 합니다.');
        regexHelper.checkMax('input[name="subject"]', 4, '수강과목은 최대 4개까지만 선택 가능합니다.');
    
    } catch(e) {
        alert(e.message);
        console.error(e);
        e.element.value = "";
        e.element.focus();
        return;
    }

    alert("입력형식 검사 완료");
});
```

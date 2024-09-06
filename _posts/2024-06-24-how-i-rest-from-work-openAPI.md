---
layout: post
title: openAPi 사용 예시
date: 2024-09-05
description: openAPi 사용 예시 # Add post description (optional)
img: /postImg/site.jpg # Add image post (optional)
fig-caption:  # Add figcaption (optional)
tags: [ api, javascript, openAPI, json
 ]
---
-openAPI를 사용하려면 보편적으로 데이터에 접근할 수 있는 KEY를 발급받아야한다.   
-url은 xml과 json형식으로 받는데 json이 용량이 더 적다. 가능하면 json으로 받자.    
-필수요청변수인지 확인해서 KEY와 함께 변수와 값을 url와함게 입력해야 데이터에 접근할 수 있다.
-아래 예시들에는 공통적으로 데이터를 받아오는 동안 Loading.gif가 나오도록 했다.

### Setting - common
```html
    <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

### CSS - common

```css
#loading {
    width: 100px;
    height: 100px;
    background: url(img/loading.gif) no-repeat center/cover;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, 50%);
    z-index: 999999999;
    display:none;
}

#loading.active {
    display:block;
}
```

## <span style="color:#1E90FF; font-weight:bold;">영화진흥위원회 API</span>

### HTML

```html
<div id="loading"></div>
<h1>영화진흥위원회 박스오피스 순위</h1>

<input type="date" id="targetDt">

<table border="1">
    <thead>
        <tr>
            <th>순위</th>
            <th>영화제목</th>
            <th>관객수</th>
            <th>개봉일</th>
        </tr>
    </thead>
    <tbody id="list-body"></tbody>
</table>
```
### Javascript

```javascript
 document.querySelector("#targetDt").addEventListener("change", async e => {

    const loading = document.querySelector("#loading");
    loading.classList.add("active");

    let response = null;
    //targetDt=20240904
    //key=abeb353499360f3b88fdd6b34a1ef123
    try {
        response = await axios.get("http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json", {
            params: {
                key: "abeb353499360f3b88fdd6b34a1ef123",
                //날짜데이터에 - 를 모두 공백으로 변경
                targetDt: e.currentTarget.value.replaceAll('-', ''),
            },
        });
        console.log(response.data);
    } catch (error) {
        console.error(`[Error Code] ${error.code}`);
        console.error(`[Error message] ${error.message}`);
        let alertMsg = error.message;

        //http 상태메세지가 포함되어 있다면 해당 내용을 에러 문자열에 추가
        if(error.response !== undefined) {
            const errorMsg = `${error.response.status} error - ${error.response.statusText}`;
            console.error(`[HTTP Status] ${errorMsg}`);
            alertMsg += `\n${errorMsg}`;
        }

        alert(alertMsg);
        return;
    } finally {
        loading.classList.remove('active');
    }
    const listBody = document.querySelector("#list-body");
    //기존 데이터를 화면에서 지움
    //Array.from(listBody.getElementByTagName("tr")).forEach((v, i) => { listBody.removeChild(v) });
    //listBody.innerHTML = "";와 같은 기능인데 좀 더 세부적으로 다룰 때 필요할 수 있을거같다 
    listBody.innerHTML = "";
    console.log(response.data.boxOfficeResult.dailyBoxOfficeList)
    response.data.boxOfficeResult.dailyBoxOfficeList.forEach( (v, i) => {
        const tr = document.createElement("tr");
        listBody.appendChild(tr);

        const td1 = document.createElement("td");
        td1.innerHTML = v.rank;
        tr.appendChild(td1);

        const td2 = document.createElement("td");
        td2.innerHTML = v.audiCnt;
        tr.appendChild(td2);

        const td3 = document.createElement("td");
        td3.innerHTML = v.movieNm;
        tr.appendChild(td3);

        const td4 = document.createElement("td");
        td4.innerHTML = v.openDt;
        tr.appendChild(td4);
    });
});
```

## <span style="color:#1E90FF; font-weight:bold;">Kakao API</span>

### HTML

-Kakao openAPI를 이용해서 daum의(블로그, 카페) 검색 기능 구현   
-blog인지 cafe인지 select박스에서 선택, value에 각각 url입력  => #url.value 받아서 selectedURL변수에 넣어서 axios.get(selectedURL, { } )함수로 response.data를 받아옴.   
-javascript로 html구조를 만들때에는 반복되는 구조에 사용, 예시하나 띄워놓고 부모tag부터 하나하나하면 할만하다. const img = document.createElement('img');로 변수에 태그담고 img.setAttribute('속성명', 받아올json값);으로 속성담고 css먹일 classList.add와 innerHTML 잘쓰고 부모.appendChild(들어갈 놈)하면 된다.   

```html
<div id="loading"></div>

<div id="header">
    <h1>Kakao Blog / Cafe</h1>

    <form action="" id="searchForm">
        <fieldset>
            <select id="url">
                <option value="https://dapi.kakao.com/v2/search/blog">블로그 검색</option>
                <option value="https://dapi.kakao.com/v2/search/cafe">카페 검색</option>
            </select>
            <input type="search" id="query" placeholder="Search..."/>
            <button type="submit">검색</button>
        </fieldset>
    </form>
</div>

<!-- 검색 결과가 표시될 목록 -->
<ul id="list"></ul>
```

### CSS

```css
 *{
    box-sizing: border-box;
}

body {
    font-family: Arial;
    padding-top: 127px;
}

#header {
    position: fixed;
    top:0;
    left: 0;
    width: 100%;
    background-color: #fff;
    z-index: 1000;
    padding: 0 10px 5px 10px;
    box-sizing: border-box;
}

#list {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-wrap: wrap;
    align-content: flex-start;
    
    li {
        width: 100%;

        a {
            display: flex;
            text-decoration: none;
            color:#000;
            border-top: 1px dotted #ccc;
            padding:5px;

            &:hover{
                background-color: #ff03;
            }

            img {
                display:block;
                width: 80px;
                height: 80px;
                object-fit: cover;
            }
        }
        .content_box {
            display:flex;
            width:100%;
            overflow:hidden;
            flex-direction: column;
            font-size:14px;
            padding: 0 10px;
            text-align: center;
            height: 80px;
            justify-content: center;
            margin-left:3px;

            .title {
                font-size: 17px;
                display:flex;
                justify-content: flex-start;
                font-weight:bold;
                text-align: center;
                line-height: 17px;
                margin-bottom:3px;
            }

            .desc {
                font-size: 14px;
                width:100%;
                white-space: nowrap;
                overflow:hidden;
                text-overflow: ellipsis;
            }
        }
    }   
}
```

### Javscript

```javascript
/* kakao key */
const KAKAO_REST_KEY = '48300ed01d5822f850073b1b4134187d';

//페이지 번호
let currentPage = 1;

//블로그, 카페 선택시 넣을 url
let selectedUrl = null;

//검색어
let queryKeyword = null;

//마지막 페이지인지 검사
let isEnd = false;

//검색폼의 submit 이벤트 - 신규엄색
document.querySelector("#searchForm").addEventListener('submit', (e) => {
    e.preventDefault();

    //cafe, blog 선택한 url     
    selectedUrl = document.querySelector('#url').value;

    //입력된 검색어를 가져온다.
    const queryField = document.querySelector('#query');
    queryKeyword = queryField.value.trim();

    //검색어가 입력되지 않은 경우 예외처리
    if(!queryKeyword) {
        alert('검색어를 입력하세요.');
        queryField.focus();
        return;
    }

    //신규검색
    currentPage = 1;
    get_image_search();
});

//스크롤 이벤트 - 추가검색
window.addEventListener('scroll', e => {
    //마지막 페이지이거나 이미 로딩바가 표시되고있다면(중복호출방지) 처리 중단
    if(isEnd || document.querySelector('#loading').classList.contains('active')) {
        return;
    }

    //현재 화면 스크롤바의 최상단 Y좌표
    const scrollTop = window.scrollY;
    //현재 보고있는 웹 브라우저의 창 길이 스크롤이 끝났을 때 scrollY와 availHeight를 더하면 전체 Y의 길이가 나옴
    const windowHeight = window.screen.availHeight;
    //HTML 문서의 전체높이
    const documentHeight = document.body.scrollHeight;

    //스크롤바가 끝났을 때 반동효과(보편적으로 스마트폰 드래그 시)를 고려해서 scrollTop + windowHeight가 실제 화면 길이보다 길어질 수 있다.
    if(scrollTop + windowHeight >= documentHeight){
        // 2페이지 이후는 추가 검색
        currentPage++;
        get_image_search();
    }
});

//ajax요청 후 결과를 화면에 HTML로 출력하는 함수
async function get_image_search() {
    const loading = document.querySelector("#loading");

    loading.classList.add('active');
    //검색 결과가 표시될 영역
    const list = document.querySelector('#list');

    //1페이지에 대한 요청일 경우 기존에 표시되고 있던 검색결과가 있다면 삭제한다.
    //list.innerHTML = "";
    if(currentPage == 1) {
        Array.from(list.getElementsByTagName('li')).forEach((v, i) => {
            list.removeChild(v);
        });
    }
    let response = null;
    try {
        response = await axios.get(selectedUrl, {
            params: {
                query: queryKeyword,
                page: currentPage,
            },
            headers: {
                Authorization: `KakaoAK ${KAKAO_REST_KEY}`,
            },
        });
        //응답결과 확인
            console.log(response.data);
    } catch (error) {
        console.error(`[Error Code] ${error.code}`);
        console.error(`[Error message] ${error.message}`);
        let alertMsg = error.message;

        //http 상태메세지가 포함되어 있다면 해당 내용을 에러 문자열에 추가
        if(error.response !== undefined) {
            const errorMsg = `${error.response.status} error - ${error.response.statusText}`;
            console.error(`[HTTP Status] ${errorMsg}`);
            alertMsg += `\n${errorMsg}`;
        }

        alert(alertMsg);
        return;
    } finally {
        loading.classList.remove('active');
    }

    // 다음 페이지를 요청할 수 있는지를 판단하기 위한 값
    isEnd = response.data.meta.is_end;

    response.data.documents.forEach((v, i) => {
        const li = document.createElement('li');

        const a = document.createElement('a');
        a.setAttribute('href', v.url);
        a.setAttribute('target', '_blank');
        a.setAttribute('title', v.title);

        const img = document.createElement('img');
        img.setAttribute('src', v.thumbnail);

        const title = document.createElement('div');
        title.classList.add('title');
        title.innerHTML = v.title;

        const desc = document.createElement('div');
        desc.classList.add('desc');
        desc.innerHTML = v.contents;

        const div = document.createElement('div');
        div.classList.add('content_box');

        list.appendChild(li);
        li.appendChild(a);
        a.appendChild(img);
        a.appendChild(div);
        div.appendChild(title);
        div.appendChild(desc);

        
    });
}
```
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

## <span style="color:#1E90FF; font-weight:bold;">주소기반산업지원서비스 API</span>
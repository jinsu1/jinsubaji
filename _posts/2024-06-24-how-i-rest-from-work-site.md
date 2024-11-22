---
layout: post
title: 유용한 사이트 모음
date: 2024-08-01
description: Avaliable_Site # Add post description (optional)
img: /postImg/site.jpg # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [font, img, icon, etc]
---
# <span style="color:#616161; font-weight:bold;">유용한 사이트 모음 </span>

## <span style="color:#ffa59c; font-weight:bold;">font, icon, img</span>
-google fonts   
-fontAwesome   
- chrome web store의image  downloader - imageye (화면의 이미지 전체 다운로드)   

## <span style="color:#ffa59c; font-weight:bold;">color</span>
-color picker   

## <span style="color:#ffa59c; font-weight:bold;">reperence</span>
-GDWeb  
-pinterest  
-rwdb.kr  

## <span style="color:#ffa59c; font-weight:bold;">HTML, CSS, javascript</span>
-MDN   
-w3school   

## <span style="color:#ffa59c; font-weight:bold;">ppt</span>
-https://www.canva.com/   

## <span style="color:#ffa59c; font-weight:bold;">반복되는 기초 공사 코드 자동생성</span>

-https://snippet-generator.app/   

#### react components js 파일 

-${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/g} : 파일이름이 들어가고 스네이크 표기를 카멜표기로 바꾸어줌   

-설정 -> 코드조각 (snipet) 에서 javascript.json 에 이미지엔 없지만 우측에 만들어진 코드를 입력   

-우측 상단 my_react_component가 자동완성 키워드가 됨   

![snipet](../assets/img/postImg/snipet.PNG)

```javascript
import React, {memo} from 'react';

import styled from 'styled-components';

const ${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/g}Container = styled.div`

`;

const ${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/g} = memo(() => {
    return (
        <${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/g}Container>

        </${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/g}Container>
    );
});

export default ${TM_FILENAME_BASE};

```

-${TM_DIRECTORY/^.+\\(.*)/${1:/pascalcase}/g} 로 바꾸어 주면 파일이름 대신 폴더이름이 들어간다. 주로 index.js는 폴더마다 들어가서 이걸로 사용   

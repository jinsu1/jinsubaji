---
layout: post
title: "[ReactJS] 시작 및 설정"
date: 2024-11-15
description: Spring Boot # Add post description (optional)
img: /postImg/react.png # Add image post (optional)
fig-caption: reactJS # Add figcaption (optional)
tags: [react, reactJS, setting]
---
# <span style="color:#616161; font-weight:bold;">ReactJS 시작</span>

<br />

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>
-nodeJS 설치   
-npm install -g yarn   

> 설치에 실패한 경우 수동 설치 해야함 ==>  https://classic.yarnpkg.com/latest.msi   

-yarn add styled-components : styled.ul` css 기본 문법 `; styled.div 등 사용 가능   
> import styled, {css} from 'styled-components';

#### extention 설치

1) Color Highlight   
2) ESLint   
3) Image preview   
4) npm Intellisense   
5) Prettier   
6) vscode-styled-components   

<br />

## <span style="color:#ffa59c; font-weight:bold;">프로젝트 생성<span>

1 ) 프로젝트 만들 폴더안에서 cmd 실행    

2 ) yarn create react-app 프로젝트이름   

3 ) 만들어진 프로젝트 이름 폴더 안에들어가서 다시 cmd 실행 후 yarn set version berry 입력   

> node-modules의 용량이크고 파일양이 많아 압축 관리해주는 berry 사용 하겠다는 명령어   

4 ) yarn install   

5 ) ".yarnrc.yml" 파일 열어서 node-modules 를 pnp로 변경   

6 ) package.json 열어서 eslintConfig 설정 부분을 지우거나 이름을 변경해서 인식하지 못하도록 수정   

7 ) yarn install 다시 적용   

8 ) 자동 생성된 .git과 .gitignore 제거 

> 수동으로 해도되고 명령어로 해도 됨. 명령어 : rmdir /q /s .git, derl .gitignore   

9 ) git 사용할 경우 처음 프로젝트 최상위 폴더로 이동하여 .gitignore 생성 후 아래 코드 입력 (안하면 git push할 때 엄청난 쓸모없는 파일이 올라감)   

```
node_modules/
.vscode/

.yarn/*
!.yarn/cache
!.yarn/patches
!.yarn/plugins
!.yarn/releases
!.yarn/sdks
!.yarn/versions
```

10 ) yarn start 및 vscode 실행   

-react는 git에서 push 받고 난 후 yarn -install을 하고나서 yarn start해야한다.   

<br />

## <span style="color:#ffa59c; font-weight:bold;">파일 정리<span>

-public 폴더는 localhost:3000/ 의 root경로이다.   

-src 폴더에서 자동으로 만들어진 파일들을 App.js, index.js를 제외하고는 삭제해도 된다.   

-index.js 에서 React.StrictMode태그는 지운다.   


## <span style="color:#ffa59c; font-weight:bold;">문법<span>

-모든 js 파일 첫 줄에는 'import React from "react";' 리액트의 기본을 구성하는 패키지를 참조 해준다.    

-새로만든 js 파일 마지막에는 export를 해줘야 다른 js파일에서 import할 수 있다.   

> import Hello from "/MyComponent"; <-- import 구문 Hello 부분은 MyComponent에서 export한 클래스 및 파일명과 관계없이 이름을 지을 수 있고 이 이름은 태그로 사용된다.<br/> ex) ```<hello></hello>```

-html태그들은 문법을 정확히 지켜야한다.   
> ```<br>```은 에러, ```<br/>```로 써야한다.   

-class는 className으로 대체해야만한다.   

-return 에는 하나의 태그만 return가능하므로 여러개가 들어가야한다면 전체를 감싸는 태그로 하나의 덩어리로 만들어야한다.   

-주석은 "{/* Hello와 World 라는 이름의 컴포넌트 출력 */}" 처럼 중괄호로 묶어서 사용해야 인식함   

-```<React.StricMode>``` 이 적용되어 있는 경우, 선언만 하고 사용되지 않는 변수들에 대한 경고 메세지가 브라우저 콘솔에 표시된다. console.log를 통한 출력문이 2중으로 표시되는 단점이 있으므로 삭제하는 것을 권장하며 개발용이므로 최종 빌드시에는 반드시 제거하는 것이 좋다.

-대문자로 시작하는 태그는 import해온 태그이고 소문자는 html 코드인 것으로 구별   

-inline css는 json형식으로 변수에 담아서 사용하며 카멜표기법을 사용해야한다.   

```java
 const myStyle = {
        backgroundColor: '#f60',
        fontSize: '20px',
        color: '#0f6',
        fontWeight: 'bold',
        padding: '10px 25px',
        marginBottom: '10px'
    };

    return ( 
        <div style={myStyle}>Hello React Css (1)</div>
    );
```
-styled-component 사용법

```javascript
const MyGridContainer = styled.ul`
    list-style :none;
    padding: 0;
    margin: 0;
    width: 1024px;
    border: 5px solid #cc0;
    padding: 10px;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
`;

const MyGridItem = styled.li`
    width: ${props => props.width};
`;


 <MyGridContainer />
```


```javascript
const MyGridItem = styled.li`
    width: ${props => props.width};
`;


 <MyGridItem width={'30%'}>
    <MyBox>Item1</MyBox>
</MyGridItem>
```
-```<NavLink></NavLink>```를 사용하면 현 주소와 일치하는 요소에 자동으로 active className을 주기 때문에 CSS만 설정해 놓아도 active효과를 줄 수 있다.   
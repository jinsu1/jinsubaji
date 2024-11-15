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

> 수동으로 해도되고 명령어로 해도 됨. 명령어 : remdir /q /s /git, del .gitignore   

9 ) git 사용할 경우 처음 프로젝트 최상위 폴더로 이동하여 .gitignore 생성 후 아래 코드 입력 (안하면 git push할 때 엄청난 쓸모없는 파일이 올라감)   

```
/node_modules
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

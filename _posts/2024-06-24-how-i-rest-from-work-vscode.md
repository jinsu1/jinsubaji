---
layout: post
title: VScode setting
date: 2024-07-26
description: VScode setting # Add post description (optional)
img: /postImg/vscode_icon.jpg # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [VScode , Extentions, Setting]
---
## VSCode extentions

-Auto Close Tag   
-Auto Rename Tag   
-Korean Language Pack for visual studio code   
-live server   
-Markdown All in one   
-Markdown PDF   
-Markdown Preview Github Styling   
-Material Icon Theme   
-Font Awesome Gallery (cdn ex:<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.6.0/css/all.min.css">)   
-FontSize Shortcuts   
-prettier code formatter:자동 들여쓰기 (명령창: ctrl+shift+p 에 format 입력 후 기본 포멧설정을 프리티어로 해야함)
-vscode-pdf   
-git history   
-color highlight
-highlight machingTag
-Eslint (cmd 사용자경로에서 npm install -g eslint 수행 필요)
-Code Runner (환경설정에서 Code-runner: Clear Previous Output)

## setting

-Terminal Default Profile -> Command Prompt   
-설정 - editor: Detect Indentation (tab 사이즈 변경 후 재부팅해도 유지하도록하려면 체크 해제)   
-에멧 자동 줄넘김 your setting (json)에들어가서 코드를 추가해준다. ,로 코드 간 구별을 꼭 해주어야한다   
    
    "emmet.syntaxProfiles": {

    "html": {

    "inline_break": 1

        }

    }

## emmet
-반복 *   
-병렬 +   
-자식 >   
-클래스 .className or div.className   
-id #  
-그룹화 ( )   
-속성 [ ]   
-텍스트(내용) { }   
-넘버링 $   


ex) div>div.user_id#user_id{내용}+(ul>li*2)+div[class="user_name" id="user_name"]{내용}+button.button\$*3{확인\$}   
>![emmet실행결과](https://jinsu1.github.io/jinsubaji/assets/img/postImg/emmetResult.PNG)   

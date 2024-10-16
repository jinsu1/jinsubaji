---
layout: post
title: Git
date: 2024-08-01
description: git # Add post description (optional)
img: /postImg/git.png # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [git, guide]
---
## Git 사용법

-인증서 생성하기(shh) : 사용 할 컴퓨터마다 적용
> clone할 때 SHH와 HTTP의 차이점 =? http는 push, pull할 때 계정 아이디,암호를 물어봄(예전에는 매번 물어봤으나 최근엔 자동으로 자격 증명이 등록되어 최초 1번만 인증) shh는 pc에 인증서가 있기때문에 인증을 하지않음. 결론 = <span style="color:ffd33d">ssh사용을 권장</span>하는 이유는 shh가 업로드 및 다운로드 속도가 훨씬 빠르며 github 설정에서 key를 삭제해 원하는 컴퓨터의 권한을 원격으로 박탈할 수 있지만 https는 컴퓨터에 직접가서 일반 자격 증명을 지워야함. 이미 https로 인증을 한 컴퓨터가 외부에 있다면 레포지토리의 https주소를 바꾸는 방법도 있음

```
$ ssh-keygen -t ed25519 -C "본인메일@gmail.com"
```
>완료 기호들 뜰 때까지 엔터사용(비밀번호 미설정) 열어서 github의 setting ->shh메뉴에 new shh key 생성 후 title 입력하고 사용자폴더에 pub파일열어서 내용 복사 붙여넣기하여 생성. 사용하지 않는 컴퓨터는 삭제

-git config --global user.name(git계정인증)   
-git config --global user.email(global[전역]계정등록 삭제하고 싶은 경우 windows 자격 증명 관리에서 삭제할 수 있음)   
-git config --local user.name(프로젝트[지역]한에서 계정 등록)  
-git config --local user.email   
>공용으로 사용하는 컴퓨터에서 계정등록을하되(계정등록하지않으면 깃허브에 잔디생성x) 푸쉬할때 2차 인증 할 방법? 

-git config user.name (현재 로그인 계정 확인)   
-git config --global --unset user.name(전역으로 설정된 config 삭제)   
-git config --global --unset user.email   
-git init (깃허브에서 리포를 만들어 clone하지않고 컴퓨터에서 작업을 먼저 시작했을 때 저장소를 생성한다.)   
-git clone 리포주소   
-git branch -M main     
-git add .   
-git commit -m '커밋내용'   
-git remote add origin 리포주소  
-git push origin main   
-git push -u origin main(-u옵션 : 최초 입력 이후 origin main을 자동으로 등록해 이후에 git push만 입력해도됨)
> -u == --force --set-upstream 축약형인가?

-git push -f origin main(-f옵션 : 리포내용과 관계없이 내 프로젝트를 강제로 업로드함. 팀원이 있을 경우 팀원이 푸쉬할 때 충돌이 발생할 수 있으니 개인이나 꼭 필요한 상황에만 사용)   
-git pull origin main
 
[Git사용설명서.PDF](https://github.com/user-attachments/files/16408329/05-Github.pdf)   

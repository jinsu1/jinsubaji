---
layout: post
title: jekyll & Git & MarkDown
date: 2024-07-26
description: description # Add post description (optional)
img: /postImg/jekyll_logo.png # Add image post (optional)
fig-caption: caption # Add figcaption (optional)
tags: [jekyll ,ruby, blog, git, markdown]
---
## jekyll
### ruby
-ruby 다운로드   
-경로 : [ruby download](https://rubyinstaller.org/downloads/)   
-ruby -v   

### jekyll
-jekyll bundler 설치 (기본경로에 설치)   
-gem install jekyll bundler   
-jekyll -v   
-bundle init (gemfile 위치 확인)   
-gemfile 위치에서 bundle install   


### Connect & Git & MarkDown
-인증서 생성하기(shh) 사용할 컴퓨터마다 적용
> clone할 때 SHH와 HTTP의 차이점 =? http는 push, pull할 때 계정 아이디,암호를 물어봄(예전에는 매번 물어봤으나 최근엔 자동으로 자격 증명이 등록되어 최초 1번만 인증) shh는 pc에 인증서가 있기때문에 인증을 하지않음. 결론 = 예전에는 필수 였겠지만 지금은 별 차이 없음
```
$ ssh-keygen -t ed25519 -C "leekh4232@gmail.com"
```
>완료 기호들 뜰 때까지 엔터사용(비밀번호 미설정) 열어서 github의 setting ->shh메뉴에 new shh key 생성 후 title 입력하고 사용자폴더에 pub파일열어서 내용 복사 붙여넣기하여 생성. 사용하지 않는 컴퓨터는 삭제

-github에 리포생성   
-원하는 위치에 git clone 받을 폴더 생성    
-생성한 폴더 경로에서 cmd 실행
-git config --global user.name(git계정인증)   
-git config --global user.email(global[전역]계정등록 삭제하고 싶은 경우 windows 자격 증명 관리에서 삭제할 수 있음)   
-git config --local user.name(프로젝트[지역]한에서 계정 등록)
-git config --local user.email   
>공용으로 사용하는 컴퓨터에서 계정등록을하되(계정등록하지않으면 깃허브에 잔디생성x) 푸쉬할때 2차 인증 할 방법 개선 필요

-git config user.name (현재 로그인 계정 확인)   
-git config --global --unset user.name(전역으로 설정된 config 삭제)   
-git config --global --unset user.email   
-git init   
-git branch -M main   
-git add .   
-git commit -m '커밋내용'   
-git remote add origin 리포주소
-git push -u origin main   
-bundle install   
-bundle exec jekyll serve 로컬서버 띄우기 (git push없이 바로 변경사항 확인)   
[Git사용설명서.PDF](https://github.com/user-attachments/files/16408329/05-Github.pdf)   
[Markdown문법.PDF](https://github.com/user-attachments/files/16408354/04-.pdf)

### theme
-[jekyll theme download](http://jekyllthemes.org/) 에서 테마 다운로드 후 로컬 폴더에 압축풀기   
-_config url 나의 github url로 변경 (baseurl 수정안 할 시 css가 안먹을 수 있음)   
-baseurl: "/jinsubaji.github.io/"   
-url: "https://jinsu1.github.io/jinsubaji.github.io/"   


### error 
-LF will be replaced by CRLF the next time Git touches it   
 => git config --global core.autocrlf true   
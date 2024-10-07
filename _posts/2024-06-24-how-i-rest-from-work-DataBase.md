---
layout: post
title: "[DataBase] MySQL"
date: 2024-10-07
description: DataBase(MySQL) # Add post description (optional)
img: /postImg/mysql.png # Add image post (optional)
fig-caption: MySQL # Add figcaption (optional)
tags: [database, mysql, crud, setting]
---

# <span style="color:#616161; font-weight:bold;">DataBase (MySQL)</span>

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>
-Microsoft Visual C++ 설치 https://learn.microsoft.com/ko-kr/cpp/windows/latest-supported-vc-redist?view=msvc-170 에서 재배포 가능 패키지 버전 64bit를 설치힌다.      

-MySQL 설치 https://dev.mysql.com/downloads/mysql/   

-Go to Download Page에서 인스털 버전의 경우 설치에 실패하는 경우가 많다. Windows (x84, 32 & 64-bit), ZIP Archive 압축 패키치를 다운로드받아 수동 설치가 실패하는 경우가 거의 없다.   

-로그인 과정 생략 => No thanks, just start my download. 를 선택   

-원하는 위치에 압축을 푼다(폴더 경로 한글 금지)   

-bin폴더 밑에 data 폴더 생성, 빈 텍스트 파일 생성 후 my.ini로 확장자까지 변경   

-my.ini 에 아래 내용 입력   

```html
[mysqld]
basedir=C:\mysql-8.0.20.winx64
datadir=C:\mysql-8.0.20-winx64\data
port=3306
```
>basedir=(MySQL이 설치된 경로), datadir=(새로만든 data폴더 경로)   

-환경 변수 설정  

-새로만들기 => 변수 이름 MYSQL_HOME, 변수 값 C:\mysql-8.0.20.winx64(MySQL이 설치된 경로)   

-시스템 변수 Path에 bin경로 추가 (%MYSQL_HOME%\bin)   

-MySQL 초기화 명령 수행 => cmd 확인 버튼으로 열지말고 Ctrl+Shift+Enter로 관리자 권한으로 실행   
-mysqld --initialize 입력 => data폴더 안에 파일들이 생성되면 성공   

-MySQL을 윈도우 서비스로 등록 => mysqld --install  

-등록된 윈도우 서비스를 구동 => net start MySQL   

-data폴더안에 컴퓨터이름.err 형식의 파일을 메모장으로 확인 => root@localhost: 임시 비밀번호임   

-명령 프롬프트를 다시 일반 사용자 권한으로 실행 후 mysql 접속 명령 => mysql -uroot -p   
> -u : user의 이름을 입력하기 위한 옵션. 여기서 user이름은 root
> -p : password를 입력하기 위한 옵션. 엔터 후 위의 임시 비밀번호 붙여넣기   

-관리자 계정의 비밀번호 변경하기 => alter user 'root'@'localhost' identified with mysql_native_password by '사용할 비밀번호';   

-mysql재접속하며 변경한 비밀번호 확인   


# <span style="color:#616161; font-weight:bold;">데이터베이스 관리</span>

## <span style="color:#ffa59c; font-weight:bold;">SQL 명령어</span>
-show databases;   

-use 데이터베이스이름;   

-show tables;   

-select * from 테이블이름;   

-desc 테이블이름;   
>테이블 구조확인   

-create database 데이터베이스이름;   



## <span style="color:#ffa59c; font-weight:bold;">테이블 생성</span>

```sql
CREATE TABLE `members` (
    `id` int not null auto_increment comment '일련번호',
    `user_id` varchar(30) not null comment '아이디',
    `user_pw` varchar(255) not null comment '비밀번호(암호화저장)',
    `user_name` varchar(30) not null comment '회원이름',
    `email` varchar(255) not null comment '이메일',
    `phone` varchar(20) not null comment '연락처',
    `birthday` date not null comment '생년월일',
    `gender` enum('M','F') not null comment '성별(M=남자,F=여자)',
    `postcode` char(5) not null comment '우편번호',
    `addr1` varchar(255) not null comment '검색된 주소',
    `addr2` varchar(255) not null comment '나머지 주소',
    `photo` blob null comment '프로필사진 정보',
    `is_out` enum('Y','N') not null comment '탈퇴여부(Y/N)',
    `is_admin` enum('Y','N') not null comment '관리자 여부(Y/N)',
    `login_date` datetime not null comment '마지막 로그인 일시',
    `reg_date` datetime not null comment '등록일시',
    `edit_date` datetime not null comment '변경일시',
    primary key(`id`)
) ENGINE=InnoDB DEFAULT CHARSET=`utf8` COMMENT='회원';
```

> edit_data는 모든 DB에 추가하는게 좋다. 회원정보를 아무것도 수정하지 않고 저장 버튼을 누를경우 수정된 행이 0개로 return값이 전송되는데 이를 자바에서 에러로 예외처리를 할 수 있는데 그걸 방지하기 위해서 현재시간을 변경일시로 넣어서 항상 최소 1개이상의 수정내용이 생기게 하는 방법이다.


## <span style="color:#ffa59c; font-weight:bold;">테이블 수정</span>
-alter table `테이블이름` rename  => 테이블의 이름 변경   
-alter table `테이블이름` add => 컬럼이나 제약조건을 추가한다.   
-alter table `테이블이름` change => 컬럼을 수정한다.   
-alter table `테이블이름` drop => 컬럼이나 제약조건을 삭제한다.   
   
-auto_increment는 기본키에만 설정할 수 있는 속성이다. 이미 기본키로 설정된 값을 없애려면 auto_increment를 지워야 가능하다.   
-alter table `mysql` drop `edit_data` edit_date 컬럼을 삭제한다.

## <span style="color:#ffa59c; font-weight:bold;">데이터베이스 백업/복구</span>
-MySQL의 백업과 복구는 명렴프롬프트에서 MySQL의 실행파일이 위치하는 경로로 이동한 후에 수행해야 한다.   

-백업하기 (MySQL 접속 상태가아닌 일반 명령프롬프트 상태에서 실행)   
```sql
mysqldump -u계정아이디 -p 백업할DB이름 > 백업파일경로 

mysqldump -uroot -p mytest > C:\sample-data\back_up\mytest_back_up.sql
```
<br/>
-복구하기 (MySQL 접속 상태가아닌 일반 명령프롬프트 상태에서 실행)   
-database 만들고 나서 백업 (create database mytest;)   

```sql
mysql -u계정아이디 -p 복구할DB이름 < 백업파일경로

mysql -uroot -p mytest < C:\sample-data\back_up\mytest_back_up.sql
(mytest 데이터베이스 안에 내용을 백업하겠다는 뜻)
```

## <span style="color:#ffa59c; font-weight:bold;">DataBase 및 MySQL 정리 파일</span>
-[개념이해,설치,살펴보기,기본조회.pdf](https://github.com/user-attachments/files/17275767/MySQL.01-.pdf)   

-[연산자,정렬+부분조회,함수.pdf](https://github.com/user-attachments/files/17275768/MySQL.02-.%2B.pdf)   

-[그룹조회,조인,서브쿼리,입력+수정+삭제.pdf](https://github.com/user-attachments/files/17275770/MySQL.03-.%2B.%2B.pdf)   

-[관리,workbench,ERD_compressed.pdf](https://github.com/user-attachments/files/17275854/MySQL.04-.workbench.ERD_compressed.pdf)   


password : mysql123!@#   

출처 : youtube 호쌤코딩클럽   

## <span style="color:#ffa59c; font-weight:bold;">연습 문제</span>
<hr />

### 1번
-아이디가 'jun123'인 학생과 같은 학년인 학생의 학번, 이름, 학년을 조회 하시오.

```sql
SELECT studno, name, grade 
FROM student
WHERE grade=(
    SELECT grade FROM student WHERE userid='jun123'
    );
```

```sql
+--------+--------+-------+
| studno | name   | grade |
+--------+--------+-------+
|  10101 | 전인하 |     4 |
|  10107 | 이광훈 |     4 |
|  10202 | 오유석 |     4 |
+--------+--------+-------+
```

<hr/>

### 2번
-101번 학과 학생들의 평균 몸무게보다 몸무게가 적은 학생의 이름, 학과번호, 몸무게를 조회하시오.

```sql
SELECT name, deptno, weight
FROM student
WHERE deptno='101' AND weight < (
    SELECT AVG(weight) FROM student
);
```

```sql
+--------+--------+--------+
| name   | deptno | weight |
+--------+--------+--------+
| 박미경 |    101 |     52 |
| 지은경 |    101 |     42 |
| 임유진 |    101 |     54 |
+--------+--------+--------+
```

### 3번 
-'이광훈 학생'과 같은 학과의 학생들에 대한 평균 몸무게보다 몸무게가 적게 나가는 학생들의 이름, 몸무게, 소속학과이름, 담당교수 이름을 조회하시오.(담당교수가 없는 학생은 출력되지 않습니다.)

```sql
SELECT s.name, weight, dname, p.name
FROM student s, professor p, department d
WHERE s.deptno=d.deptno AND s.deptno=(SELECT deptno FROM student WHERE name='이광훈') AND weight < (SELECT AVG(weight) FROM student
) AND s.profno=p.profno;
```

```sql
SELECT s.name, s.weight, d.dname, p.name FROM student s
INNER JOIN department d ON s.deptno=d.deptno
INNER JOIN professor p ON s.profno=p.profno
WHERE s.weight < (
    SELECT AVG(weight) FROM student WHERE deptno=(
        SELECT deptno FROM student WHERE name='이광훈'
        )
);
```
```sql
+--------+--------+----------------+--------+
| name   | weight | dname          | name   |
+--------+--------+----------------+--------+
| 지은경 |     42 | 컴퓨터공학과   | 전은지 |
| 임유진 |     54 | 컴퓨터공학과   | 전은지 |
| 김진영 |     48 | 멀티미디어학과 | 권혁일 |
| 김진경 |     51 | 전자공학과     | 이재우 |
+--------+--------+----------------+--------+
```

## 입력, 수정, 삭제 연습문제

### 1번
-다음의 학과를 신설하기 위한 데이터를 입력하고 결과를 조회

```sql
insert into department values (103, '인터넷정보과', '공학관');
```

```sql
+--------+----------------+--------+
| deptno | dname          | loc    |
+--------+----------------+--------+
|    101 | 컴퓨터공학과   | 1호관  |
|    102 | 멀티미디어학과 | 2호관  |
|    103 | 인터넷정보과   | 공학관 |
|    201 | 전자공학과     | 3호관  |
|    202 | 기계공학과     | 4호관  |
+--------+----------------+--------+
```

### 2번
-공학관에 위치한 학과의 위치를 '5호관'으로 수정하시오.

```sql
update department set loc='5호관' where loc='공학관';
```

```sql
+--------+----------------+-------+
| deptno | dname          | loc   |
+--------+----------------+-------+
|    101 | 컴퓨터공학과   | 1호관 |
|    102 | 멀티미디어학과 | 2호관 |
|    103 | 인터넷정보과   | 5호관 |
|    201 | 전자공학과     | 3호관 |
|    202 | 기계공학과     | 4호관 |
+--------+----------------+-------+
```

### 3번
-학과번호가 300번 이상인 학과를 삭제하시오.
```sql
delete from department where deptno > 300;
```

---
layout: post
title: "[Java] DB연동(MySQL)"
date: 2024-10-08
description: Java note # Add post description (optional)
img: /postImg/java.png # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [java, insert, update, search, delete, mysql, sql]
---

# <span style="color:#616161; font-weight:bold;">JAVA DB연동하기(MySQL)</span>

- int와 integer의 차이   
    - int   
    1)산술 연산 가능   
    2)null로 초기화 불가능   
    3)저장공간이 4Byte로 작음   
    
    - integer   
    1)Unboxing하지 않을 시 산술 연산이 불가능   
    2)null 값으로 처리 가능   
    3)저장공간이 큼   
    4)null값으로 처리가 가능해 SQL에 용이하게 쓰인다.   

## <span style="color:#ffa59c; font-weight:bold;">Setting</span>

-build.grade dependencies 설정
```java
dependencies {
    // Use JUnit Jupiter for testing.
    testImplementation libs.junit.jupiter

    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'

    // This dependency is used by the application.
    implementation libs.guava

    // https://mvnrepository.com/artifact/com.mysql/mysql-connector-j
    implementation 'com.mysql:mysql-connector-j:9.0.0'
}

// Apply a specific Java toolchain to ease working on different environments.
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

application {
    // Define the main class for the application.
    mainClass = 'kr.jinsu.prepared_statement_ex'
}
```
<br/>

## <span style="color:#ffa59c; font-weight:bold;">exception</span>
#### StringFormatException.java
```java
package kr.jinsu.exceptions;

public class StringFormatException extends Exception {
    public StringFormatException(String message) {
        super(message);
    }
}
```
<br/>

## <span style="color:#ffa59c; font-weight:bold;">helpers</span>
#### DBHelper.java
```java
package kr.jinsu.helpers;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DBHelper {
    private static final String db_hostname = "127.0.0.1"; 
    private static final int db_portnumber = 3306;
    private static final String db_database = "myschool";
    private static final String db_charset = "utf8";
    private static final String db_username = "root";
    private static final String db_password = "123qwe!@#";

    //접속 객체 선언
    private Connection conn = null;

    //싱글톤 객체
    private static DBHelper current;

    public static DBHelper getInstance() {
        if (current == null) {
            current = new DBHelper();
        }
        return current;
    }

    /** 싱글톤 객체를 삭제한다. */
    public static void freeInstance() {
        current = null;
    }

    /** 생성자 */
    private DBHelper() {
    }

    /** 데이터베이스에 접속 후, 접속 객체를 리턴한다. */
    public Connection open() {
        // 접속 주소 구성
        String urlFormat = "jdbc:mysql://%s:%d/%s?characterEncoding=%s&serverTimeZone=UTC";
        String url = String.format(urlFormat, db_hostname, db_portnumber, db_database, db_charset);
        // System.out.println(url);

        //<MySQL JDBC의 드라이버 클래스를 로딩해서 DriverManager클래스에 등록한다,

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");

            //DriverManager 객체를 사용하여 DB에 접속한다.
            // --> 접속 URL, 아이디, 비밀번호를 전달
            // --> DriverManager에 등록된 Driver 객체를 사용하여 DB에 접속 후,
            // Connection 객체를 리턴받는다.
            // --> import java.sql.DriverManager 필요함
            conn = DriverManager.getConnection(url, db_username, db_password);
        } catch (ClassNotFoundException e) {
            System.out.println("===== MySQL Driver Loading Fail =====");
            e.printStackTrace();
        } catch (SQLException e) {
            System.out.println("===== MySQL Connection Fail ======");
            e.printStackTrace();
        }  

        return conn;
    }

    /** 데이터베이스의 접속을 해제한다. */
    public void close() {
        if(conn != null) {
            try {
                conn.close();
            } catch (Exception e) {
                System.out.println("===== MySQL Disconnection Fail =====");
                System.out.println(e.getMessage());
            }
        }
    }
}

```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">prepare_statement_ex</span>
#### app01 (insert into)
```java
package kr.jinsu.prepared_statement_ex;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Scanner;

import kr.jinsu.helpers.DBHelper;

public class App01 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("학과명: ");
        String dname = scanner.nextLine();

        System.out.print("위치: ");
        String loc = scanner.nextLine();

        scanner.close();

        /** SQL구문 정의하기 */
        // department 테이블에 컴퓨터정보과를 추가하기 위한 SQL의 템플릿
        // deptno 컬럼은 auto_increment 이므로 insert에서 명시하지 않아도 된다.
        String sql = "INSERT INTO department (dname, loc) VALUE (?, ?)";

        Connection conn = DBHelper.getInstance().open();

        // SLQ문의 템플릿을 사용하여 쿼리 실행을 준비하는 객체
        PreparedStatement pstmt = null;

        ResultSet rs = null;

        // 결과값 (저장된 데이터의 수)
        int result = 0;

        // 결과값 (생성된 auto_increment 값)
        int autoGeneratedID = 0;

        try {
            // pstmt 객체 할당 --> auto_increment 조회 옵션 사용함
            pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);

            // SQL문의 ? 자리에 데이터를 바인딩
            pstmt.setString(1, dname);
            pstmt.setString(2, loc);

            // SQL문 실행하기 ==> 결과 행 리턴됨 ( 주의 : 파라미터 없음)
            result = pstmt.executeUpdate();

            //생성된 auto_increment값 얻기
            rs = pstmt.getGeneratedKeys();
            rs.next();
            autoGeneratedID = rs.getInt(1);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //SQL을 수행하는 과정에서 생성한 객체는 반드시 생성 역순으로 닫아야 함
            if(rs != null) {
                //객체 닫기
                try {
                    rs.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            } 

            if (pstmt != null) {
                try {
                    pstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }

        System.out.println(result + " Record Insert");
        System.out.println("New Deptno=" + autoGeneratedID);

        DBHelper.getInstance().close();
    }
}

```
<br/>

#### app02 (delete)
```java
package kr.jinsu.prepared_statement_ex;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.Scanner;

import kr.jinsu.helpers.DBHelper;

public class App02 {
    public static void main(String[] args) {
        // 삭제할 학생의 학번 입력받기
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("학번: ");
        int studno = scanner.nextInt();

        scanner.close();

        /** SQL구문 정의하기 */
        String sql = "delete from student where studno=?";

        Connection conn = DBHelper.getInstance().open();

        // SLQ문의 템플릿을 사용하여 쿼리 실행을 준비하는 객체
        PreparedStatement pstmt = null;

        // 결과값 (저장된 데이터의 수)
        int result = 0;

        try {
            // pstmt 객체 할당 --> auto_increment 조회 옵션 사용함
            pstmt = conn.prepareStatement(sql);

            // SQL문의 ? 자리에 데이터를 바인딩
            pstmt.setInt(1, studno);

            // SQL문 실행하기 ==> 결과 행 리턴됨 ( 주의 : 파라미터 없음)
            result = pstmt.executeUpdate();

        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //SQL을 수행하는 과정에서 생성한 객체는 반드시 생성 역순으로 닫아야 함
            if(pstmt != null) {
                //객체 닫기
                try {
                    pstmt.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            } 
        }

        System.out.println(result + " Record Delete");

        DBHelper.getInstance().close();
    }
}

```
<br/>

#### app03 (조회: select[preparedStatement]) - 검색해서 조회하기
```java
package kr.jinsu.prepared_statement_ex;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Scanner;

import kr.jinsu.helpers.DBHelper;

public class App03 {
    public static void main(String[] args) {
        //조회할 교수 이름 입력받기
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("교수 이름: ");
        String pname = scanner.nextLine();

        scanner.close();

        /** SQL구문 정의하기 */
        // department 테이블에 컴퓨터정보과를 추가하기 위한 SQL의 템플릿
        // deptno 컬럼은 auto_increment 이므로 insert에서 명시하지 않아도 된다.
        String sql = "select profno, name, userid, position, sal, "
                + "hiredate, comm, deptno from professor "
                + "where name like concat('%', ? , '%')";

        Connection conn = DBHelper.getInstance().open();

        // SLQ문의 템플릿을 사용하여 쿼리 실행을 준비하는 객체
        PreparedStatement pstmt = null;

        ResultSet rs = null;

        try {
            // pstmt 객체 할당
            pstmt = conn.prepareStatement(sql);

            // SQL문의 ? 자리에 데이터를 바인딩
            pstmt.setString(1, pname);

            // SQL문 실행하기 ==> 결과 행 리턴됨 ( 주의 : 파라미터 없음)
            rs = pstmt.executeQuery();

            while(rs.next()) {
                int profNo = rs.getInt("profNo");
                String name = rs.getString("name");
                String userId = rs.getString("userid");
                String position = rs.getString("position");
                int sal = rs.getInt("sal");
                String hiredate = rs.getString("hiredate");
                int comm = rs.getInt("comm");
                int deptno = rs.getInt("deptno");

                System.out.printf("교수번호: %d\n", profNo);
                System.out.printf("이름: %s\n", name);
                System.out.printf("아이디: %s\n", userId);
                System.out.printf("직급: %s\n", position);
                System.out.printf("급여: %d\n", sal);
                System.out.printf("입사일: %s\n", hiredate);
                System.out.printf("보직수당: %d\n", comm);
                System.out.printf("학과번호: %d\n", deptno);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //SQL을 수행하는 과정에서 생성한 객체는 반드시 생성 역순으로 닫아야 함
            if(rs != null) {
                //객체 닫기
                try {
                    rs.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            } 

            if (pstmt != null) {
                try {
                    pstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
        DBHelper.getInstance().close();
    }
}

```
<br/>

#### app04 (조회: select [statement]) - 검색해서 조회하기
-아이디(userid)와 비밀번호(idnum)를 입력해서 정보를 조회하는데 statement는 비밀번호 입력란에 [' or '' = ']를 입력하여 값을 무저건 true로 반환시켜 보안성이 취약하다 \를 끼워넣는 방법으로 예방할 수 있지만 위의 PreparedStatement를 사용하면 자동으로 방지할 수 있다.
```java
package kr.jinsu.jdbc_ex;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Scanner;

import kr.jinsu.helpers.DBHelper;

public class app04 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("아이디를 입력하세요: ");
        String userId = scanner.nextLine();

        System.out.print("비밀번호를 입력하세요: ");
        String idnum = scanner.nextLine();

        scanner.close();

        Connection conn = DBHelper.getInstance().open();

        // '김도훈' 교수의 정보 조회하기
        String sql = "SELECT studno, s.name, grade, birthdate, tel, height, weight, dname, p.name "
        + "FROM student s "
        + "INNER JOIN department d ON s.deptno = d.deptno "
        + "INNER JOIN professor p ON s.profno = p.profno "
        + "WHERE s.userid='" + userId + "' AND idnum='" + idnum + "'";

        // SQL문을 실행할 수 있는 객체
        Statement stmt = null;

        // 결과값 (저장된 데이터의 수)
        ResultSet rs = null;

        try {
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);
            
            while(rs.next()) {
                // select절에 명시한 컬럼 이름을 사용하여 데이터 추출
                int studNo = rs.getInt("studno");
                String name = rs.getString("s.name");
                int grade = rs.getInt("grade");
                String birthdate = rs.getString("birthdate");
                String tel = rs.getString("tel");
                int height = rs.getInt("height");
                int weight = rs.getInt("weight");
                String dname = rs.getString("dname");
                String profName = rs.getString("p.name");

                //결과출력
                System.out.println("학번: " + studNo);
                System.out.println("이름: " + name);
                System.out.println("학년: " + grade);
                System.out.println("생년월일: " + birthdate);
                System.out.println("전화번호: " + tel);
                System.out.println("키: " + height);
                System.out.println("몸무게: " + weight);
                System.out.println("학과: " + dname);
                System.out.println("지도교수: " + profName);
                System.out.println();

            }
        } catch (SQLException e) {
            System.out.println("===== Query Error =====");
            e.printStackTrace();
        } finally {
            // SQL 구문의 에러 여부에 상관없이 stmt 객체를 닫기 위하여 finally 블록 사용.
            // --> 객체의 유효범위 설정을 위해서 stmt 객체는 try 블록 밖에 선언되었다.
            if(stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }  
            }
        }
        DBHelper.getInstance().close();
    }
}

```
<br/>

#### app05 (update)
```java
package kr.jinsu.jdbc_ex;

import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

import kr.jinsu.helpers.DBHelper;

public class app05 {
    public static void main(String[] args) {
        Connection conn = DBHelper.getInstance().open();

        String sql = "UPDATE department SET dname='hello', loc='world' WHERE deptno >= 103";

        Statement stmt = null;
        int result = 0;

        try {
            stmt = conn.createStatement();
            result = stmt.executeUpdate(sql);
        } catch (SQLException e) {
            System.out.println("===== Query Error =====");
            e.printStackTrace();
        } finally {
            if(stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }  
            }
        }

        //결과 출력
        System.out.println(result + "개의 데이터가 수정됨");

        /** 데이터베이스의 접속을 해제한다. */
        DBHelper.getInstance().close();
    }
}

```

<br/>

#### app05 (select[statement]) - 지정된 목록 조회하기
```java
package kr.jinsu.jdbc_ex;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import kr.jinsu.helpers.DBHelper;

public class Ex06_SelectEx {
    public static void main(String[] args) {
        Connection conn = DBHelper.getInstance().open();

        String sql = "SELECT deptno, dname, loc FROM department ORDER BY deptno ASC";

        // SQL문을 실행할 수 있는 객체
        Statement stmt = null;
        // 결과값 (저장된 데이터의 수)
        ResultSet rs = null;

        try {
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);

            while (rs.next()) {
                int deptNo = rs.getInt("deptno");
                String dname = rs.getString("dname");
                String loc = rs.getString("loc");

                System.out.printf("학과번호: %d\n", deptNo);
                System.out.printf("학과이름: %s\n", dname);
                System.out.printf("위치: %s\n", loc);
                System.out.println();
            }
        } catch (SQLException e) {
            System.out.println("===== Query Error =====");
            e.printStackTrace();
        } finally {
            // SQL 구문의 에러 여부에 상관없이 stmt 객체를 닫기 위하여 finally 블록 사용.
            // --> 객체의 유효범위 설ㅈ정을 위해서 stmt 객체는 try 블록 밖에 선언되었다.
            if(stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }  
            }
        }
        DBHelper.getInstance().close();
    }
}


```


<br/>

#### app05 (select[statement]) - 함수 사용하여 조건부 조회하기
```java
package kr.jinsu.jdbc_ex;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import kr.jinsu.helpers.DBHelper;

public class Ex07_CountEx {
    public static void main(String[] args) {
        Connection conn = DBHelper.getInstance().open();

        // 4학년 학생의 수
        String sql = "SELECT COUNT(*) cnt FROM student WHERE grade=4";

        // SQL문을 실행할 수 있는 객체
        Statement stmt = null;

        // 결과값 (저장된 데이터의 수)
        ResultSet rs = null;

        try {
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);

            //조회 결과의 첫 번째 줄로 이동
            boolean first = rs.next();
            
            if(first) {
                //select절에 명시된 `1`번째 항목을 int 형으로 추출하기
                //int count = rs.getInt(1);

                //select절에 명시된 `cnt`컬럼을 int형으로 추출하기
                int count = rs.getInt("cnt");

                System.out.println("4학년 학생은 " + count + "명 입니다.");
            }
        } catch (SQLException e) {
            System.out.println("===== Query Error =====");
            e.printStackTrace();
        } finally {
            // SQL 구문의 에러 여부에 상관없이 stmt 객체를 닫기 위하여 finally 블록 사용.
            // --> 객체의 유효범위 설ㅈ정을 위해서 stmt 객체는 try 블록 밖에 선언되었다.
            if(stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }  
            }
        }
        DBHelper.getInstance().close();
    }
}

```

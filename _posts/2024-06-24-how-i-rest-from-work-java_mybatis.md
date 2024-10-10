---
layout: post
title: "[Java] MyBatis를 이용한 DB연동(MySQL)"
date: 2024-10-08
description: Java note # Add post description (optional)
img: /postImg/mybatis.jpg # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [mybatis ,java, insert, update, search, delete, mysql, sql]
---

# <span style="color:#616161; font-weight:bold;">[JAVA] MyBatis 사용해서 DB연동하기(MySQL)</span>
-유연성: SQL 쿼리를 직접 작성할 수 있으므로 매우 유연하다. 또한, MyBatis는 동적 쿼리를 작성할 수 있다.   

-간결성: MyBatis는 SQL 쿼리와 프로그래밍 언어 코드를 분리하기 때문에 코드가 간결해져 유지보수에 용이   

-성능: MyBatis는 캐시 기능을 제공하여 데이터베이스 연산 속도를 높일 수 있다.   

-다양한 데이터베이스 지원: MyBatis는 다양한 데이터베이스에 대한 지원을 제공합니다.  


## <span style="color:#ffa59c; font-weight:bold;">Setting</span>

-build.grade dependencies 설정
```java
dependencies {
    // Use JUnit Jupiter for testing.
    testImplementation libs.junit.jupiter

    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'

    // This dependency is used by the application.
    implementation libs.guava

    // https://mvnrepository.com/artifact/com.google.code.gson/gson
    implementation 'com.google.code.gson:gson:2.11.0'

    // https://mvnrepository.com/artifact/org.projectlombok/lombok
    compileOnly 'org.projectlombok:lombok:1.18.34'

    annotationProcessor 'org.projectlombok:lombok:1.18.34'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/retrofit
    implementation 'com.squareup.retrofit2:retrofit:2.11.0'

    // https://mvnrepository.com/artifact/com.squareup.retrofit2/converter-gson
    implementation 'com.squareup.retrofit2:converter-gson:2.11.0'

    // https://mvnrepository.com/artifact/com.mysql/mysql-connector-j
    implementation 'com.mysql:mysql-connector-j:9.0.0'

    // https://mvnrepository.com/artifact/org.mybatis/mybatis
    implementation 'org.mybatis:mybatis:3.5.16'

    // https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-core
    implementation 'org.apache.logging.log4j:log4j-core:2.24.1'

    // https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api
    implementation 'org.apache.logging.log4j:log4j-api:2.24.1'
}

// Apply a specific Java toolchain to ease working on different environments.
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}
application {
    // Define the main class for the application.
    mainClass = 'kr.jinsu.mybatis_ex'
}
```
<br />

#### MyBatisConnectionFactory.java
-파일위치 확인 잘할 것

```java
package kr.jinsu;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class MyBatisConnectionFactory {
    /** 데이터베이스 접속 객체 */    
    private static SqlSessionFactory sqlSessionFactory;

    /** XML에 명시된 접속 정보를 읽어들인다. */
    // 클래스 초기화 블럭 : 클래스 변수의 복잡한 초기화에 사용된다.
    // 클래스가 처음 로딩될 때 한번만 수행된다.
    static {
        //접속 정보를 명시하고 있는 XML의 경로 읽기
        try {
            Reader reader = Resources.getResourceAsReader("config.xml");

            // sqlSessionFactory가 존재하지 않는다면 생성한다.
            if(sqlSessionFactory == null) {
               sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /** 데이터베이스 접속 객체를 통해 DATABASE에 접속한 세션을 리턴한다 */
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}

```

## <span style="color:#ffa59c; font-weight:bold;">resources/log4j2.xml</span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xml>
<Configuration status="INFO">
    <Appenders>
        <RollingFile name="RollingFile"
        fileName="log/log.log" filePattern="log/log-%d{yyyy-MM-dd}.log"
        append="false">
            <PatternLayout charset="UTF-8" pattern="%d %5p [%c] %m%n" />
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true" />
            </Policies>
        </RollingFile>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyy-MM-dd HH:mm:ss} [%-5level] %logger -
            %msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Root level="DEBUG" additivity="false">
           <AppenderRef ref="Console" />
           <AppenderRef ref="RollingFile" />
        </Root>
        <logger name="org.apache.ibatis" level="DEBUG" additivity="false">
            <AppenderRef ref="Console" />
            <AppenderRef ref="RollingFile" />
        </logger>
    </Loggers>
</Configuration>
```
<br/>

## <span style="color:#ffa59c; font-weight:bold;">resources/config.xml</span>

-table당 mapper를 하나씩 만들어 각각 mapper resource를 추가해야한다.   

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
 <!-- MySQL 접속 정보를 지정한다. -->
    <properties>
        <property name="hostname" value="127.0.0.1" />
        <property name="portnumber" value="3306" />
        <property name="database" value="myschool" />
        <property name="username" value="root" />
        <property name="password" value="123qwe!@#" />
    </properties>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver" />
                <property name="url"
                value="jdbc:mysql://${hostname}:${portnumber}/${database}?
                characterEncoding=UTF8&amp;serverTimezone=UTC" />
                <property name="username" value="${username}" />
                <property name="password" value="${password}" />
            </dataSource>
        </environment>
    </environments>
    <!-- 실행할 SQL문을 정의한 Mapper XML의 경로를 지정한다. -->
    <mappers>
        <mapper resource="mappers/DepartmentMapper.xml" />
        <mapper resource="mappers/ProfessorMapper.xml" />
        <mapper resource="mappers/StudentMapper.xml" />
    </mappers>
</configuration>
```
<br/>



## <span style="color:#ffa59c; font-weight:bold;">resource/mappers/파일이름mapper.xml</span>

#### ProfessorMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="ProfessorMapper">
    <!-- Beans 클래스의 객체이름(id)과 클래스이름(type)을 명시한다. -->
    <resultMap type="kr.jinsu.models.Professor" id="professor">
        <!-- Beans의 멤버변수(property)이름과 대상 테이블의 컬럼(column)을 연결한다.
        -->
        <result property="profno" column="profno" />
        <result property="name" column="name" />
        <result property="userid" column="userid" />
        <result property="position" column="position" />
        <result property="sal" column="sal" />
        <result property="hiredate" column="hiredate" />
        <result property="comm" column="comm" />
        <result property="deptno" column="deptno" />

    </resultMap>
    <!-- 데이터 저장을 위한 기능 정의 -->
    <insert id="insert"
        parameterType="kr.jinsu.models.Professor"
        useGeneratedKeys="true"
        keyProperty="profno">
        insert into professor (name, userid, position, sal, hiredate, comm, deptno) values (#{name}, #{userid}, #{position}, #{sal}, #{hiredate}, #{comm}, #{deptno});
    </insert>

    <!-- 데이터 삭제를 위한 기능 정의 -->
    <delete id="delete"
        parameterType="kr.jinsu.models.Professor">
        delete from professor where profno=#{profno};
    </delete>
    
    <!-- 데이터 갱신을 위한 기능 정의 -->
    <update id="update"
        parameterType="kr.jinsu.models.Professor">
        update professor set name=#{name}, userid=#{userid}, position=#{position}, sal=#{sal}, hiredate=#{hiredate}, comm=#{comm}, deptno=#{deptno} where profno=#{profno};
    </update>

    <!-- 단일행 조회를 위한 기능 정의 -->
    <select id="selectItem"
        parameterType="kr.jinsu.models.Professor"
        resultMap="professor">
        select profno, name, userid, position, sal, hiredate, comm, deptno from professor where profno=#{profno};
    </select>

    <!-- 다중행 조회를 위한 기능 정의 -->
    <select id="selectList"
        resultMap="professor">
        select profno, name, userid, position, sal, hiredate, comm, deptno from professor;
    </select>
</mapper>

```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">models</span>

#### Professor.java

```java
package kr.jinsu.models;

import lombok.Data;

@Data
public class Professor {
    private int profno;
    private String name;
    private String userid;
    private String position;
    private int sal;
    private String hiredate;
    private Integer comm;
    private int deptno;
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

## <span style="color:#ffa59c; font-weight:bold;">mybatis_ex</span>

#### App01_prof (insert into)

```java
package kr.jinsu.mybatis_ex;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.models.Professor;

public class App01_prof {
    public static void main(String[] args) {
        /** (new) Log4j2 객체 생성 */ 
        //Logger logger = LogManager.getLogger("내가만든거");
        Logger logger = LogManager.getLogger(App01_prof.class);

        /** 1) 저장할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("교수이름: ");
        String name = scanner.nextLine();

        System.out.print("아이디: ");
        String userid = scanner.nextLine();

        System.out.print("직위: ");
        String position = scanner.nextLine();

        System.out.print("입사일: ");
        String hiredate = scanner.nextLine();

        System.out.print("월급: ");
        int sal = scanner.nextInt();

        System.out.print("추가수당: ");
        Integer comm= scanner.nextInt();

        System.out.print("학과번호: ");
        int deptno = scanner.nextInt();

        scanner.close();

        //입력값을 로그로 기록함
        logger.debug("입력된 데이터: " + name + ", " + userid + ", " + userid + ", " + position + ", " + sal + ", " + hiredate + ", " + comm + ", " + deptno);

        /** 2) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 3) insert를 수행할 데이터 생성 */
        // --> Beans에 생성자를 사용하지 않으면 필요한 데이터만 setter로 추가할 수 있다.
        Professor professor = new Professor();
        professor.setName(name);
        professor.setUserid(userid);
        professor.setPosition(position);
        professor.setSal(sal);
        professor.setHiredate(hiredate);
        professor.setComm(comm);
        professor.setDeptno(deptno);

        // 4) 데이터 저장
        int result = 0;

        try {
            sqlSession.insert("ProfessorMapper.insert", professor);
            //이 때, 저장할 데이터를 담고 있는 Beans를 파라미터로 전달하고, 자동으로 생성된 PK는 Beans에 저장된다.
            result = professor.getProfno();
        } catch (Exception e) {
            logger.error(e.getMessage());
        }

        /** 5) 결과판별 */
        // --> 리턴값이 0이라면 내부적으로 예외가 발생된 상황으로 간주해야 한다.
        logger.info(result + "번 데이터 저장됨");

        /** 6) DB 접속 해제 */
        // 페이지 종료 전에 데이터의 변경사항을 저장(commit)하고 데이터베이스 접속  해제하기
        sqlSession.commit();
        sqlSession.close();
    }
}

```
<br/>

#### App02_prof (delete)
```java
package kr.jinsu.mybatis_ex;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.models.Professor;

public class App02_prof {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App02_prof.class);

        /** 2) 저장할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("삭제할 교수 번호: ");
        int profno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) delete를 수행할 데이터 생성 */
        Professor model = new Professor();
        model.setProfno(profno);

        /** 5) 데이터 삭제 */
        int result = 0;
        try {
            result = sqlSession.delete("ProfessorMapper.delete", model);
        } catch (Exception e) {
            logger.error(e.getMessage());
        }

        /** 6) 결과 판별 */
        logger.debug(result + "개의 데이터 삭제됨");

        /** 7) DB 접속 해제 */
        sqlSession.commit();
        sqlSession.close();
    }
}

```
<br/>

#### App03_prof (update)
```java
package kr.jinsu.mybatis_ex;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.models.Professor;

public class App03_prof {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App03_prof.class);

        /** 2) 수정할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("수정할 교수이름: ");
        String name = scanner.nextLine();

        System.out.print("수정할 아이디: ");
        String userid = scanner.nextLine();

        System.out.print("수정할 직위: ");
        String position = scanner.nextLine();

        System.out.print("수정할 입사일: ");
        String hiredate = scanner.nextLine();

        System.out.print("수정할 월급: ");
        int sal = scanner.nextInt();

        System.out.print("수정할 추가수당: ");
        Integer comm= scanner.nextInt();

        System.out.print("수정할 학과번호: ");
        int deptno = scanner.nextInt();

        System.out.print("수정사항을 적용할 교수번호: ");
        int profno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) update를 수행할 데이터 생성 */
        Professor model = new Professor();
        model.setName(name);
        model.setUserid(userid);
        model.setPosition(position);
        model.setSal(sal);
        model.setHiredate(hiredate);
        model.setComm(comm);
        model.setDeptno(deptno);
        model.setProfno(profno);


        /** 5) 데이터 수정 */
        int result = 0;
        try {
            result = sqlSession.update("ProfessorMapper.update", model);

            if (result == 0) {
                throw new Exception("수정된 데이터가 없습니다.");
            }
        } catch (Exception e) {
            logger.error(e.getMessage());
        }

        /** 6) 결과 판별 */
        logger.debug(result + "개의 데이터 수정됨");

        /** 7) DB 접속 해제 */
        sqlSession.commit();
        sqlSession.close();
    }
}

```
<br/>

#### App04_prof (select) - 검색해서 조회하기

```java
package kr.jinsu.mybatis_ex;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.models.Professor;

public class App04_prof {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App04_prof.class);

        /** 2) 조회할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("조회할 교수 번호: ");
        int profno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) select를 수행할 데이터 생성 */
        Professor model = new Professor();
        model.setProfno(profno);

        /** 5) 데이터 조회 */
        Professor result = null;

        try {
            result = sqlSession.selectOne("ProfessorMapper.selectItem", model);
        } catch (Exception e) {
            logger.error(e.getMessage());
        }

        /** 6) 결과 판별 */
        if (result == null) {
            logger.debug("조회결과 없음");
        } else {
            logger.debug(result.toString());
        }

        /** 7) DB 접속 해제 */
        sqlSession.commit();
        sqlSession.close();
    }
}


```

>selectOne 단일검색

<br/>

#### App05_prof (select) - 전체 조회하기
```java
package kr.jinsu.mybatis_ex;

import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.models.Professor;

public class App05_prof {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App05_prof.class);

        /** 2) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 3) 데이터 조회 */
        List<Professor> result = null;

        try {
            // ProfessorMapper라는 namespace를 갖는 XML에서 id값이 selectList인 <select> 태그를 호출한다.
            // 이 때, 조회할 데이터를 담고 있는 Beans를 파라미터로 전달하고, 결과를 List<Beans>로 받는다.
            // 전달할 파라미터가 없는 경우 beans를 null로 설정한다.
            result = sqlSession.selectList("ProfessorMapper.selectList", null);
        } catch (Exception e) {
            logger.error(e.getMessage());
        }

        /** 4) 결과 판별 */
        if (result == null) {
            logger.debug("조회결과 없음");
        } else {
            for (int i = 0; i < result.size(); i++) {
                Professor item = result.get(i);

                logger.debug("교수번호: " + item.getProfno());
                logger.debug("교수명: " + item.getName());
                logger.debug("아이디: " + item.getUserid());
                logger.debug("직위: " + item.getPosition());
                logger.debug("월급: " + item.getSal());
                logger.debug("입사일: " + item.getHiredate());
                logger.debug("추가수당: " + item.getComm());
                logger.debug("학과번호: " + item.getDeptno());
                logger.debug(" ");
            }
        }

        /** 7) DB 접속 해제 */
        sqlSession.close();
    }
}

```

>selectList 전체검색

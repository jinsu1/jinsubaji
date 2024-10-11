---
layout: post
title: "[Java] service+Impl을 추가한 MyBatis를 이용한 DB연동(MySQL)"
date: 2024-10-11
description: Java note # Add post description (optional)
img: /postImg/java.png # Add image post (optional)
fig-caption: java note # Add figcaption (optional)
tags: [service,Impl, mybatis,java, insert, update, search, delete, mysql, sql]
---

# <span style="color:#616161; font-weight:bold;">[JAVA] service, impl 추가</span>
-

## <span style="color:#ffa59c; font-weight:bold;">Service/파일이름.java</span>

#### DepartmentService.java

-하나의 처리를 위해서 두 개 이상의 Mapper 기능을 호출할 필요가 있을 경우 인터페이스 구현체(Impl)을 통해서 처리한다.   

-delete 기능의 경우 삭제된 데이터의 수를 의미하는 int를 리턴   

-목록 조회 기능의 경우 List<DTO클래스>를 리턴   

-입력, 수정, 상세조회의 경우는 DTO 클래스를 리턴   

-service에서 정의해놓고 impl에서 override해서 재정의해서 사용

-professor과 student도 인터페이스명과 params앞의 이름만 다르고 내용은 같다   


```java
package kr.jinsu.services;

import java.util.List;

import kr.jinsu.exceptions.MyBatisException;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;

public interface DepartmentService {
    /**
     * 학과 정보를 새로 저장하고 저장된 정보를 조회하여 리턴한다.
     * @param params    - 저장할 정보를 담고 있는 Beans
     * @return  Department - 저장된 데이터
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 저장된 데이터가 없는 경우
     */
    public Department addItem(Department params) throws ServiceNoResultException, Exception;

    /**
     *  학과 정보를 수정하고 수정된 정보를 조회하여 리턴한다. 
     * @param params    - 수정할 정보를 담고 있는 Beans
     * @return  Department - 수정된 데이터
     * @throws MyBatisException  - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 수정된 데이터가 없는 경우
     */
    public Department editItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 학과 정보를 삭제한다
     * @param params    - 삭제할 정보를 담고 있는 Beans
     * @return int  - 삭제할 정보의 개수
     * @throws MyBatisException - SQL처리에 실패한 경우
     * @throws ServiceNoResultException - 삭제된 데이터가 없는 경우
     */
    public int deleteItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 단일 학과 정보 조회
     * @param params
     * @return
     * @throws MyBatisException
     * @throws ServiceNoResultException
     */
    public Department getItem(Department params) throws ServiceNoResultException, Exception;

    /**
     * 전체 학과 정보 조회
     * @param params
     * @return
     * @throws ServiceNoResultException
     * @throws Exception
     */
    public List<Department> getList(Department params) throws ServiceNoResultException, Exception;
} 

```
<br />


## <span style="color:#ffa59c; font-weight:bold;">service/impl/파일이름.java</span>

-delete부분은 department테이블이 professor테이블과 student테이블이 참조하고 있으므로 지우려면 참조테이블의 데이터를 먼저 지워야한다. department의 deptno에 관한 정보를 지우려면 deparment.deptno를 참조하는 student.deptno과 professor.deptno에 해당하는 데이터를 순서대로 지워야 한다. StudentMapper의 deleteByDeptno를 호출해서 student테이블의 deptno을 지우고 ProfessorMapper의 deleteByDeptno를 호출해서 professor테이블의 deptno을 지우고 있다.

#### DepartmentServiceImpl.java
```java
package kr.jinsu.services.impl;

import java.util.List;

import org.apache.ibatis.session.SqlSession;

import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.models.Professor;
import kr.jinsu.models.Student;
import kr.jinsu.services.DepartmentService;

/**
 * 학과 관리 기능과 관련된 MyBatis Mapper를 간접적으로 호출하기 위한 기능 명세
 * 
 * (1) 모든 메서드를 재정의 한 직후 리턴값 먼저 정의
 */
public class DepartmentServiceImpl implements DepartmentService {

    private SqlSession sqlSession;

    public DepartmentServiceImpl (SqlSession sqlSession) {
        this.sqlSession = sqlSession;
    }

    @Override
    public Department addItem(Department params) throws ServiceNoResultException, Exception {
        Department result = null;

        int rows = sqlSession.insert("DepartmentMapper.insert", params);

        if(rows == 0) {
            throw new ServiceNoResultException("저장된 데이터가 없습니다.");
        }

        result = sqlSession.selectOne("DepartmentMapper.selectItem", params);

        return result;
    }

    @Override
    public Department editItem(Department params) throws ServiceNoResultException, Exception {
        Department result = null;

        int rows = sqlSession.update("DepartmentMapper.update", params);

        if(rows == 0) {
            throw new ServiceNoResultException("수정된 데이터가 없습니다.");
        }

        result = sqlSession.selectOne("DepartmentMapper.selectItem", params);

        return result;
    }

    @Override
    public int deleteItem(Department params) throws ServiceNoResultException, Exception {
        int result = 0;

        //학과 데이터 삭제를 위해 참조관계에 있는 자식 데이터를 순서대로 삭제
        Student student = new Student();
        student.setDeptno(params.getDeptno());
        sqlSession.delete("StudentMapper.deleteByDeptno", student);

        Professor professor = new Professor();
        professor.setDeptno(params.getDeptno());
        sqlSession.delete("ProfessorMapper.deleteByDeptno", professor);

        // delete문 수행 --> 리턴되는 값은 수정된 데이터의 수
        result = sqlSession.delete("DepartmentMapper.delete", params);

        if(result == 0) {
            throw new ServiceNoResultException("삭제된 데이터가 없습니다.");
        }

        return result;
    }

    @Override
    public Department getItem(Department params) throws ServiceNoResultException, Exception {
        Department result = null;

        result = sqlSession.selectOne("DepartmentMapper.selectItem", params);

        if(result == null) {
            throw new ServiceNoResultException("조회된 데이터가 없습니다.");
        }

        return result;
    }

    @Override
    public List<Department> getList(Department params) throws ServiceNoResultException, Exception {
        return sqlSession.selectList("DepartmentMapper.selectList", params);
    }
}

```
#### ProfessorServiceImpl.java

-마찬가지로 professor테이블의 profno은 student의 profno가 참조하고 있으므로 professor.profno를 참조하는 student.profno의 값에 해당하는 데이터를 지워야한다. StudentMapper의 deleteByProfno를 호출해서 student.profno를 지우고 있다.

-studnetServiceImpl은 참조하는 테이블이 없으므로 생략한다.

```java
package kr.jinsu.services.impl;

import java.util.List;

import org.apache.ibatis.session.SqlSession;

import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Professor;
import kr.jinsu.models.Student;
import kr.jinsu.services.ProfessorService;

/**
 * 학과 관리 기능과 관련된 MyBatis Mapper를 간접적으로 호출하기 위한 기능 명세
 * 
 * (1) 모든 메서드를 재정의 한 직후 리턴값 먼저 정의
 */
public class ProfessorServiceImpl implements ProfessorService {

    private SqlSession sqlSession;

    public ProfessorServiceImpl (SqlSession sqlSession) {
        this.sqlSession = sqlSession;
    }

    @Override
    public Professor addItem(Professor params) throws ServiceNoResultException, Exception {
        Professor result = null;

        int rows = sqlSession.insert("ProfessorMapper.insert", params);

        if(rows == 0) {
            throw new ServiceNoResultException("저장된 데이터가 없습니다.");
        }

        result = sqlSession.selectOne("ProfessorMapper.selectItem", params);

        return result;
    }

    @Override
    public Professor editItem(Professor params) throws ServiceNoResultException, Exception {
        Professor result = null;

        int rows = sqlSession.update("ProfessorMapper.update", params);

        if(rows == 0) {
            throw new ServiceNoResultException("수정된 데이터가 없습니다.");
        }

        result = sqlSession.selectOne("ProfessorMapper.selectItem", params);

        return result;
    }

    @Override
    public int deleteItem(Professor params) throws ServiceNoResultException, Exception {
        int result = 0;

        //학과 데이터 삭제를 위해 참조관계에 있는 자식 데이터를 순서대로 삭제
        Student student = new Student();
        student.setProfno(params.getProfno());
        sqlSession.delete("StudentMapper.deleteByProfno", student);

        // delete문 수행 --> 리턴되는 값은 수정된 데이터의 수
        result = sqlSession.delete("ProfessorMapper.delete", params);

        if(result == 0) {
            throw new ServiceNoResultException("삭제된 데이터가 없습니다.");
        }

        return result;
    }

    @Override
    public Professor getItem(Professor params) throws ServiceNoResultException, Exception {
        Professor result = null;

        result = sqlSession.selectOne("ProfessorMapper.selectItem", params);

        if(result == null) {
            throw new ServiceNoResultException("조회된 데이터가 없습니다.");
        }

        return result;
    }

    @Override
    public List<Professor> getList(Professor params) throws ServiceNoResultException, Exception {
        return sqlSession.selectList("ProfessorMapper.selectList", params);
    }
}

```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">resource/mappers/파일이름.xml</span>

#### DepartmentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="DepartmentMapper">
    <!-- Beans 클래스의 객체이름(id)과 클래스이름(type)을 명시한다. -->
    <resultMap type="kr.jinsu.models.Department" id="department">
        <!-- Beans의 멤버변수(property)이름과 대상 테이블의 컬럼(column)을 연결한다.
        -->
        <result property="deptno" column="deptno" />
        <result property="dname" column="dname" />
        <result property="loc" column="loc" />
    </resultMap>
    <!-- 데이터 저장을 위한 기능 정의 -->
    <insert id="insert"
        parameterType="kr.jinsu.models.Department"
        useGeneratedKeys="true"
        keyProperty="deptno">
        insert into department (dname, loc) values (#{dname}, #{loc});
    </insert>

    <!-- 데이터 삭제를 위한 기능 정의 -->
    
    <delete id="delete"
        parameterType="kr.jinsu.models.Department">
        delete from department where deptno=#{deptno};
    </delete>
    
    <!-- 데이터 갱신을 위한 기능 정의 -->
    <update id="update"
        parameterType="kr.jinsu.models.Department">
        update department set dname=#{dname}, loc=#{loc} where deptno=#{deptno};
    </update>

    <!-- 단일행 조회를 위한 기능 정의 -->
    <select id="selectItem"
        parameterType="kr.jinsu.models.Department"
        resultMap="department">
        select deptno, dname, loc from department where deptno=#{deptno};
    </select>

    <!-- 다중행 조회를 위한 기능 정의 -->
    <select id="selectList"
        resultMap="department">
        select deptno, dname, loc from department;
    </select>
</mapper>

```

<br/>

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
    <delete id="deleteByDeptno"
        parameterType="kr.jinsu.models.Student">
        delete from professor where deptno=#{deptno};
    </delete>

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

#### StuduentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="StudentMapper">
    <!-- Beans 클래스의 객체이름(id)과 클래스이름(type)을 명시한다. -->
    <resultMap type="kr.jinsu.models.Student" id="student">
        <!-- Beans의 멤버변수(property)이름과 대상 테이블의 컬럼(column)을 연결한다.
        -->
        <result property="name" column="name" />
        <result property="userid" column="userid" />
        <result property="grade" column="grade" />
        <result property="idnum" column="idnum" />
        <result property="birthdate" column="birthdate" />
        <result property="tel" column="tel" />
        <result property="height" column="height" />
        <result property="weight" column="weight" />
        <result property="deptno" column="deptno" />
        <result property="profno" column="profno" />


    </resultMap>
    <!-- 데이터 저장을 위한 기능 정의 -->
    <insert id="insert"
        parameterType="kr.jinsu.models.Student"
        useGeneratedKeys="true"
        keyProperty="studno">
        insert into student (name, userid, grade, idnum, birthdate, tel, height, weight, deptno, profno) values (#{name}, #{userid}, #{grade}, #{idnum}, #{birthdate}, #{tel}, #{height}, #{weight}, #{deptno}, #{profno});
    </insert>

    <!-- 데이터 삭제를 위한 기능 정의 -->
    <!-- ResultMap은 select 기능을 수행하지 않는다면 필요 없음 -->
    <!-- 특정 학과에 소속된 모든 학생의 삭제를 위한 기능 정의 -->
    <delete id="deleteByDeptno"
        parameterType="kr.jinsu.models.Student">
        delete from student where deptno=#{deptno};
    </delete>

    <delete id="deleteByProfno"
        parameterType="kr.jinsu.models.Student">
        delete from student where profno=#{profno};
    </delete>

    <delete id="delete"
        parameterType="kr.jinsu.models.Student">
        delete from student where studno=#{studno};
    </delete>
    
    <!-- 데이터 갱신을 위한 기능 정의 -->
    <update id="update"
        parameterType="kr.jinsu.models.Student">
        update student set name=#{name}, userid=#{userid}, grade=#{grade}, idnum=#{idnum}, birthdate=#{birthdate}, tel=#{tel}, height=#{height}, weight=#{weight}, deptno=#{deptno}, profno=#{profno} where studno=#{studno};
    </update>

    <!-- 단일행 조회를 위한 기능 정의 -->
    <select id="selectItem"
        parameterType="kr.jinsu.models.Student"
        resultMap="student">
        select studno, name, userid, grade, idnum, birthdate, tel, height, weight, deptno, profno from student where studno=#{studno};
    </select>

    <!-- 다중행 조회를 위한 기능 정의 -->
    <select id="selectList"
        resultMap="student">
        select studno, name, userid, grade, idnum, birthdate, tel, height, weight, deptno, profno from student;
    </select>
</mapper>

```

<br/>

## <span style="color:#ffa59c; font-weight:bold;">Service_layer</span>

#### App01 (insert into)

```java
/*
 * This source file was generated by the Gradle 'init' task
 */
package kr.jinsu.service_layer;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.services.DepartmentService;
import kr.jinsu.services.impl.DepartmentServiceImpl;

public class App01 {
    public static void main(String[] args) {
        
        /** 처리 결과를 기록할 Log3J 객체 색성 */
        // 파라미터는 현재 클래스의 이름(String)
        Logger logger = LogManager.getFormatterLogger(App01.class);

        Scanner scanner = new Scanner(System.in);

        System.out.print("학과명: ");
        String dname = scanner.nextLine();

        System.out.print("위치: ");
        String loc = scanner.nextLine();

        scanner.close();

        //입력값을 로그로 기록함
        logger.debug("입력된 데이터: " + dname + ", " + loc);

        /** 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        Department dept = new Department();
        dept.setDname(dname);
        dept.setLoc(loc);

        /** 데이터 처리 수행 */
        //비즈니스 로직을 위한 Service 객체 생성
        // --> Mapper에 정의된 SQL을 호출하기 위해 SqlSession 객체를 주입
        DepartmentService departmentService = new DepartmentServiceImpl(sqlSession);
        //저장된 결과를 리턴받기 위한 Beans 객체
        Department result = null;

        //저장된 결과를 리턴받기 위한 Beans 객체
        try {
            result = departmentService.addItem(dept);
        } catch (ServiceNoResultException e) {
            sqlSession.rollback();
            logger.error("[저장된 데이터가 없습니다.]");
            logger.error(e.getMessage());
        } catch (Exception e) {
            sqlSession.rollback();
            logger.error("[SQL문 처리에 실패했습니다. Mapper를 확인하세요.]");
            logger.error(e.getMessage());
        } finally {
            sqlSession.commit();
        }

        /** 처리 결과 출력 */
        if( result != null ) {
            logger.debug("==========================");
            logger.debug(result.toString());
            logger.debug("==========================");
        } else {
            logger.error("저장된 데이터가 없습니다.");
        }

        sqlSession.close();
    }
}

```
<br/>

#### App02 (delete)
```java
package kr.jinsu.service_layer;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.services.DepartmentService;
import kr.jinsu.services.impl.DepartmentServiceImpl;

public class App02 {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App02.class);

        /** 2) 저장할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("삭제할 학과 번호: ");
        int deptno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) delete를 수행할 데이터 생성 */
        Department dept = new Department();
        dept.setDeptno(deptno);

        DepartmentService departmentService = new DepartmentServiceImpl(sqlSession);

        /** 5) 데이터 삭제 */
        int result = 0;

        try {
            result = departmentService.deleteItem(dept);
        }catch (ServiceNoResultException e) {
            sqlSession.rollback();
            logger.error("[삭제된 데이터가 없습니다.]");
            logger.error(e.getMessage());
        } catch (Exception e) {
            sqlSession.rollback();
            logger.error("[SQL문 처리에 실패했습니다. Mapper를 확인하세요.]");
            logger.error(e.getMessage());
        } finally {
            sqlSession.commit();
        }

        /** 처리 결과 출력 */
        logger.debug(result + "====================");
        logger.debug(result + "개의 데이터 삭제 완료");
        logger.debug(result + "====================");
        sqlSession.close();
    }
}
```
<br/>

#### App03_prof (update)
```java
package kr.jinsu.service_layer;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.services.DepartmentService;
import kr.jinsu.services.impl.DepartmentServiceImpl;

public class App03 {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App03.class);

        /** 2) 수정할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("수정할 학과 이름: ");
        String dname = scanner.nextLine();

        System.out.print("수정할 위치: ");
        String loc = scanner.nextLine();

        System.out.print("수정할 학과 번호: ");
        int deptno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) update를 수행할 데이터 생성 */
        Department model = new Department();
        model.setDeptno(deptno);
        model.setDname(dname);
        model.setLoc(loc);

        DepartmentService departmentService = new DepartmentServiceImpl(sqlSession);
        Department result = null;

    
        try {
            result = departmentService.editItem(model);
        } catch (ServiceNoResultException e) {
            sqlSession.rollback();
            logger.error("[수정된 데이터가 없습니다.]");
            logger.error(e.getMessage());
        } catch (Exception e) {
            sqlSession.rollback();
            logger.error("[SQL문 처리에 실패했습니다. Mapper를 확인하세요.]");
            logger.error(e.getMessage());
        } finally {
            sqlSession.commit();
        }

        /** 6) 결과 판별 */
        logger.debug("==========================");

        if(result != null) {
            logger.debug("학과 번호: " + result.getDeptno());
            logger.debug("학과 이름: " + result.getDname());
            logger.debug("위치: " + result.getLoc());
        } else {
            logger.debug("수정된 데이터가 없습니다.");
        }

        logger.debug("==========================");

        /** 7) DB 접속 해제 */
        sqlSession.close();
    }
}

```
<br/>

#### App04 (select) - 검색해서 조회하기

```java
package kr.jinsu.service_layer;

import java.util.Scanner;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.services.DepartmentService;
import kr.jinsu.services.impl.DepartmentServiceImpl;

public class App04 {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App04.class);

        /** 2) 조회할 데이터 입력받기 */
        Scanner scanner = new Scanner(System.in);

        System.out.print("조회할 학과 번호: ");
        int deptno = scanner.nextInt();

        scanner.close();

        /** 3) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        /** 4) select를 수행할 데이터 생성 */
        Department model = new Department();
        model.setDeptno(deptno);

        DepartmentService departmentService = new DepartmentServiceImpl(sqlSession);

        /** 5) 데이터 조회 */
        Department result = null;

        try {
            result = departmentService.getItem(model);
        }  catch (ServiceNoResultException e) {
            sqlSession.rollback();
            logger.error("[조회된 데이터가 없습니다.]");
            logger.error(e.getMessage());
        } catch (Exception e) {
            sqlSession.rollback();
            logger.error("[SQL문 처리에 실패했습니다. Mapper를 확인하세요.]");
            logger.error(e.getMessage());
        } finally {
            sqlSession.commit();
        }
        /** 6) 결과 판별 */
        logger.debug("==========================");

        if(result != null) {
            logger.debug("학과 번호: " + result.getDeptno());
            logger.debug("학과 이름: " + result.getDname());
            logger.debug("위치: " + result.getLoc());
        } else {
            logger.debug("조회된 데이터가 없습니다.");
        }

        logger.debug("==========================");

        /** 7) DB 접속 해제 */
        sqlSession.close();
     }
 }
```

<br/>

#### App05 (select) - 전체 조회하기
```java
package kr.jinsu.service_layer;

import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import kr.jinsu.MyBatisConnectionFactory;
import kr.jinsu.exceptions.ServiceNoResultException;
import kr.jinsu.models.Department;
import kr.jinsu.services.DepartmentService;
import kr.jinsu.services.impl.DepartmentServiceImpl;

public class App05 {
    public static void main(String[] args) {
        /** 1) Log4j2 객체 생성 */
        Logger logger = LogManager.getLogger(App05.class);

        /** 2) 데이터베이스 접속 */
        SqlSession sqlSession = MyBatisConnectionFactory.getSqlSession();

        DepartmentService departmentService = new DepartmentServiceImpl(sqlSession);

        /** 3) 데이터 조회 */
        List<Department> result = null;

        try {
            result = departmentService.getList(null);
        } catch (ServiceNoResultException e) {
            sqlSession.rollback();
            logger.error("[조회된 데이터가 없습니다.]");
            logger.error(e.getMessage());
        } catch (Exception e) {
            sqlSession.rollback();
            logger.error("[SQL문 처리에 실패했습니다. Mapper를 확인하세요.]");
            logger.error(e.getMessage());
        } finally {
            sqlSession.commit();
        }

        /** 4) 결과 판별 */
        if (result == null) {
            logger.debug("조회결과 없음");
        } else {
            for (int i = 0; i < result.size(); i++) {
                Department item = result.get(i);

                logger.debug("학과번호: " + item.getDeptno());
                logger.debug("학과명: " + item.getDname());
                logger.debug("위치: " + item.getLoc());

                if( i+1 < result.size()) {
                    logger.debug("---------------------");
                }
            }
        }

        /** 7) DB 접속 해제 */
        sqlSession.close();
    }
}

```

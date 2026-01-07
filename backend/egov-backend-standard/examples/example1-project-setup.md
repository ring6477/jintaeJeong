# Example 1: 전자정부 프로젝트 초기 구조 생성

## Input

```
요청: 행안부 표준에 맞는 사용자 관리 시스템 백엔드 프로젝트를 생성해주세요

프로젝트 정보:
- 프로젝트명: user-management-system
- 기관명(agency): mois
- 시스템명(system): ums
- 데이터베이스: PostgreSQL
- Spring 버전: 5.3.x
- Java 버전: 11
```

## Output

```markdown
# eGov 사용자 관리 시스템 프로젝트 구조 생성 완료

## 1. 생성된 프로젝트 구조

```
user-management-system/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── egovframework/
│   │   │       └── mois/
│   │   │           └── ums/
│   │   │               ├── web/
│   │   │               │   └── UserController.java
│   │   │               ├── service/
│   │   │               │   ├── UserService.java
│   │   │               │   └── impl/
│   │   │               │       └── UserServiceImpl.java
│   │   │               ├── dao/
│   │   │               │   └── UserDAO.java
│   │   │               ├── vo/
│   │   │               │   └── UserVO.java
│   │   │               └── common/
│   │   │                   ├── util/
│   │   │                   │   ├── StringUtil.java
│   │   │                   │   └── DateUtil.java
│   │   │                   ├── exception/
│   │   │                   │   ├── EgovException.java
│   │   │                   │   └── UserNotFoundException.java
│   │   │                   └── interceptor/
│   │   │                       └── LoggingInterceptor.java
│   │   ├── resources/
│   │   │   ├── egovframework/
│   │   │   │   ├── spring/
│   │   │   │   │   ├── context-common.xml
│   │   │   │   │   ├── context-datasource.xml
│   │   │   │   │   ├── context-mapper.xml
│   │   │   │   │   ├── context-security.xml
│   │   │   │   │   └── context-transaction.xml
│   │   │   │   └── sqlmap/
│   │   │   │       └── user/
│   │   │   │           └── User_SQL.xml
│   │   │   ├── log4j2.xml
│   │   │   └── application.properties
│   │   └── webapp/
│   │       ├── WEB-INF/
│   │       │   ├── jsp/
│   │       │   │   └── user/
│   │       │   │       ├── list.jsp
│   │       │   │       └── detail.jsp
│   │       │   ├── web.xml
│   │       │   └── config/
│   │       │       └── springmvc-servlet.xml
│   │       └── resources/
│   │           ├── css/
│   │           ├── js/
│   │           └── images/
│   └── test/
│       ├── java/
│       │   └── egovframework/
│       │       └── mois/
│       │           └── ums/
│       │               └── service/
│       │                   └── UserServiceTest.java
│       └── resources/
│           └── context-test.xml
├── pom.xml
└── README.md
```

## 2. 생성된 주요 파일

### pom.xml (의존성 설정)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <groupId>egovframework.mois</groupId>
    <artifactId>user-management-system</artifactId>
    <version>1.0.0</version>
    <packaging>war</packaging>

    <properties>
        <java.version>11</java.version>
        <spring.version>5.3.27</spring.version>
        <egovframe.version>4.0.0</egovframe.version>
    </properties>

    <dependencies>
        <!-- eGovFrame -->
        <dependency>
            <groupId>org.egovframe.rte</groupId>
            <artifactId>org.egovframe.rte.ptl.mvc</artifactId>
            <version>${egovframe.version}</version>
        </dependency>

        <!-- Spring Framework -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- MyBatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.13</version>
        </dependency>

        <!-- PostgreSQL Driver -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.6.0</version>
        </dependency>
    </dependencies>
</project>
```

### context-datasource.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans">

    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
        <property name="driverClassName" value="org.postgresql.Driver"/>
        <property name="url" value="jdbc:postgresql://localhost:5432/ums_db"/>
        <property name="username" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
        <property name="maxTotal" value="20"/>
        <property name="maxIdle" value="10"/>
        <property name="minIdle" value="5"/>
    </bean>

</beans>
```

### UserController.java (샘플)

```java
package egovframework.mois.ums.web;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import egovframework.mois.ums.service.UserService;
import egovframework.mois.ums.vo.UserVO;

import javax.annotation.Resource;
import java.util.List;

/**
 * 사용자 관리 컨트롤러
 * @author eGov Framework
 * @since 2026-01-07
 */
@Controller
@RequestMapping("/user")
public class UserController {

    @Resource(name = "userService")
    private UserService userService;

    /**
     * 사용자 목록 조회
     */
    @GetMapping("/list")
    public String getUserList(Model model) throws Exception {
        List<UserVO> userList = userService.selectUserList(new UserVO());
        model.addAttribute("userList", userList);
        return "user/list";
    }

    /**
     * 사용자 상세 조회
     */
    @GetMapping("/{userId}")
    @ResponseBody
    public UserVO getUser(@PathVariable String userId) throws Exception {
        return userService.selectUser(userId);
    }
}
```

### UserService.java (인터페이스)

```java
package egovframework.mois.ums.service;

import egovframework.mois.ums.vo.UserVO;
import java.util.List;

/**
 * 사용자 관리 서비스 인터페이스
 */
public interface UserService {
    UserVO selectUser(String userId) throws Exception;
    List<UserVO> selectUserList(UserVO searchVO) throws Exception;
    void insertUser(UserVO userVO) throws Exception;
    void updateUser(UserVO userVO) throws Exception;
    void deleteUser(String userId) throws Exception;
}
```

### User_SQL.xml (MyBatis Mapper)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="egovframework.mois.ums.dao.UserDAO">

    <select id="selectUser" parameterType="string" resultType="egovframework.mois.ums.vo.UserVO">
        SELECT user_id      AS userId
             , user_nm      AS userNm
             , email        AS email
             , reg_dt       AS regDt
          FROM tb_user
         WHERE user_id = #{userId}
           AND del_yn = 'N'
    </select>

    <select id="selectUserList" parameterType="egovframework.mois.ums.vo.UserVO"
            resultType="egovframework.mois.ums.vo.UserVO">
        SELECT user_id      AS userId
             , user_nm      AS userNm
             , email        AS email
             , reg_dt       AS regDt
          FROM tb_user
         WHERE del_yn = 'N'
        <if test="userNm != null and userNm != ''">
           AND user_nm LIKE CONCAT('%', #{userNm}, '%')
        </if>
         ORDER BY reg_dt DESC
    </select>

</mapper>
```

## 3. 개발 표준 문서

### 📄 docs/coding-standards.md
- Java 코딩 컨벤션
- 명명 규칙
- 주석 작성 가이드

### 📄 docs/architecture-guide.md
- 레이어별 역할 및 책임
- 데이터 흐름
- 트랜잭션 관리

### 📄 docs/security-checklist.md
- XSS/CSRF 방지 체크리스트
- 입력값 검증 규칙
- 세션 보안 설정

## 4. 다음 단계

1. **데이터베이스 스키마 생성**
   ```sql
   CREATE TABLE tb_user (
       user_id VARCHAR(50) PRIMARY KEY,
       user_nm VARCHAR(100) NOT NULL,
       email VARCHAR(200),
       password VARCHAR(256) NOT NULL,
       del_yn CHAR(1) DEFAULT 'N',
       reg_dt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       reg_id VARCHAR(50),
       upd_dt TIMESTAMP,
       upd_id VARCHAR(50)
   );
   ```

2. **application.properties 설정**
   - DB 접속 정보
   - 로깅 레벨
   - 파일 업로드 경로

3. **개발 시작**
   - Service 구현
   - DAO 구현
   - 비즈니스 로직 작성

## 5. 검증 체크리스트

- [x] 프로젝트 구조가 eGovFrame 표준을 따름
- [x] Spring 설정 파일이 모두 생성됨
- [x] MyBatis 매퍼 설정이 완료됨
- [x] 패키지 명명 규칙 준수
- [x] 샘플 코드에 Javadoc 포함
- [x] 보안 설정 파일 생성
- [x] 로깅 설정 완료
- [x] 테스트 디렉토리 구조 생성

프로젝트 초기 구조가 성공적으로 생성되었습니다!
```

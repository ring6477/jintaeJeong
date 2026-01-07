# Example 3: Spring Security 보안 표준 설정

## Input

```
요청: 전자정부 프레임워크에서 보안 요구사항을 만족하는 Spring Security 설정을 생성해주세요

보안 요구사항:
- 사용자 인증 및 권한 관리
- XSS 방지
- CSRF 보호
- SQL Injection 방지
- 비밀번호 암호화
- 세션 보안
- 입력값 검증
```

## Output

```markdown
# eGov Spring Security 보안 표준 구현

## 1. Spring Security 설정

### SecurityConfig.java

```java
package egovframework.mois.ums.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.csrf.CookieCsrfTokenRepository;
import org.springframework.security.web.header.writers.frameoptions.XFrameOptionsHeaderWriter;

import javax.annotation.Resource;

/**
 * Spring Security 보안 설정
 * - 인증/인가
 * - CSRF 보호
 * - XSS 방지
 * - 세션 관리
 *
 * @author eGov Framework
 * @since 2026-01-07
 */
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Resource(name = "customUserDetailsService")
    private CustomUserDetailsService customUserDetailsService;

    /**
     * 비밀번호 암호화 (BCrypt)
     */
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    /**
     * 인증 관리자 설정
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(customUserDetailsService)
            .passwordEncoder(passwordEncoder());
    }

    /**
     * HTTP 보안 설정
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            // CSRF 보호 (Cookie 기반)
            .csrf()
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            .and()

            // 인증 규칙
            .authorizeRequests()
                .antMatchers("/", "/login", "/logout", "/public/**", "/resources/**").permitAll()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/api/v1/users/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            .and()

            // 로그인 설정
            .formLogin()
                .loginPage("/login")
                .loginProcessingUrl("/login")
                .usernameParameter("userId")
                .passwordParameter("password")
                .defaultSuccessUrl("/main", true)
                .failureUrl("/login?error=true")
                .permitAll()
            .and()

            // 로그아웃 설정
            .logout()
                .logoutUrl("/logout")
                .logoutSuccessUrl("/")
                .invalidateHttpSession(true)
                .deleteCookies("JSESSIONID")
                .permitAll()
            .and()

            // 세션 관리
            .sessionManagement()
                .maximumSessions(1)                    // 동시 세션 1개 제한
                .maxSessionsPreventsLogin(true)        // 추가 로그인 차단
                .expiredUrl("/login?expired=true")
            .and()
                .sessionFixation().changeSessionId()   // 세션 고정 공격 방지
            .and()

            // 보안 헤더 설정
            .headers()
                .contentTypeOptions()                  // X-Content-Type-Options
            .and()
                .xssProtection()                       // X-XSS-Protection
            .and()
                .cacheControl()                        // Cache-Control
            .and()
                .httpStrictTransportSecurity()         // HSTS
            .and()
                .addHeaderWriter(new XFrameOptionsHeaderWriter(
                    XFrameOptionsHeaderWriter.XFrameOptionsMode.SAMEORIGIN))  // Clickjacking 방지
            .and()

            // 예외 처리
            .exceptionHandling()
                .accessDeniedPage("/error/403");
    }
}
```

### CustomUserDetailsService.java

```java
package egovframework.mois.ums.config;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import egovframework.mois.ums.service.UserService;
import egovframework.mois.ums.vo.UserVO;

import javax.annotation.Resource;
import java.util.ArrayList;
import java.util.List;

/**
 * 사용자 인증 정보 로드 서비스
 */
@Service("customUserDetailsService")
public class CustomUserDetailsService implements UserDetailsService {

    @Resource(name = "userService")
    private UserService userService;

    @Override
    public UserDetails loadUserByUsername(String userId) throws UsernameNotFoundException {
        try {
            UserVO user = userService.selectUser(userId);

            if (user == null) {
                throw new UsernameNotFoundException("사용자를 찾을 수 없습니다: " + userId);
            }

            // 권한 설정
            List<GrantedAuthority> authorities = new ArrayList<>();
            authorities.add(new SimpleGrantedAuthority("ROLE_" + user.getUserRole()));

            return new User(
                user.getUserId(),
                user.getPassword(),
                authorities
            );

        } catch (Exception e) {
            throw new UsernameNotFoundException("사용자 인증 실패", e);
        }
    }
}
```

## 2. XSS 방지

### XssFilter.java (XSS 필터)

```java
package egovframework.mois.ums.common.filter;

import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * XSS 공격 방지 필터
 */
@Component
public class XssFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        XssRequestWrapper wrappedRequest = new XssRequestWrapper(request);
        filterChain.doFilter(wrappedRequest, response);
    }
}
```

### XssRequestWrapper.java

```java
package egovframework.mois.ums.common.filter;

import org.apache.commons.text.StringEscapeUtils;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;

/**
 * XSS 방지를 위한 Request Wrapper
 * 모든 파라미터 값을 HTML 이스케이프 처리
 */
public class XssRequestWrapper extends HttpServletRequestWrapper {

    public XssRequestWrapper(HttpServletRequest request) {
        super(request);
    }

    @Override
    public String[] getParameterValues(String parameter) {
        String[] values = super.getParameterValues(parameter);

        if (values == null) {
            return null;
        }

        int count = values.length;
        String[] encodedValues = new String[count];

        for (int i = 0; i < count; i++) {
            encodedValues[i] = cleanXSS(values[i]);
        }

        return encodedValues;
    }

    @Override
    public String getParameter(String parameter) {
        String value = super.getParameter(parameter);
        return cleanXSS(value);
    }

    @Override
    public String getHeader(String name) {
        String value = super.getHeader(name);
        return cleanXSS(value);
    }

    /**
     * XSS 패턴 제거
     */
    private String cleanXSS(String value) {
        if (value == null) {
            return null;
        }

        // HTML Entity 인코딩
        value = StringEscapeUtils.escapeHtml4(value);

        // 위험한 패턴 제거
        value = value.replaceAll("<", "&lt;")
                     .replaceAll(">", "&gt;")
                     .replaceAll("\\(", "&#40;")
                     .replaceAll("\\)", "&#41;")
                     .replaceAll("'", "&#39;")
                     .replaceAll("eval\\((.*)\\)", "")
                     .replaceAll("[\\\"\\'][\\s]*javascript:(.*)[\\\"\\']", "\"\"")
                     .replaceAll("script", "");

        return value;
    }
}
```

## 3. SQL Injection 방지

### UserDAO.java (MyBatis 사용)

```java
package egovframework.mois.ums.dao;

import egovframework.mois.ums.vo.UserVO;
import egovframework.rte.psl.dataaccess.mapper.Mapper;

import java.util.List;

/**
 * 사용자 DAO
 * - MyBatis 사용으로 SQL Injection 자동 방지
 */
@Mapper("userDAO")
public interface UserDAO {

    /**
     * PreparedStatement 자동 사용 (#{} 파라미터)
     */
    UserVO selectUser(String userId);

    List<UserVO> selectUserList(UserVO searchVO);

    void insertUser(UserVO userVO);

    void updateUser(UserVO userVO);

    void deleteUser(String userId);
}
```

### User_SQL.xml (안전한 SQL)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="egovframework.mois.ums.dao.UserDAO">

    <!--
        #{userId} 사용 - PreparedStatement 자동 변환 (안전)
        ${userId} 사용 금지 - String 직접 치환 (위험)
    -->
    <select id="selectUser" parameterType="string" resultType="egovframework.mois.ums.vo.UserVO">
        SELECT user_id      AS userId
             , user_nm      AS userNm
             , email        AS email
          FROM tb_user
         WHERE user_id = #{userId}    /* 안전: PreparedStatement */
           AND del_yn = 'N'
    </select>

    <!-- 동적 SQL도 안전하게 처리 -->
    <select id="selectUserList" parameterType="egovframework.mois.ums.vo.UserVO"
            resultType="egovframework.mois.ums.vo.UserVO">
        SELECT user_id      AS userId
             , user_nm      AS userNm
             , email        AS email
          FROM tb_user
         WHERE del_yn = 'N'
        <if test="userNm != null and userNm != ''">
           AND user_nm LIKE CONCAT('%', #{userNm}, '%')  /* 안전 */
        </if>
        <if test="email != null and email != ''">
           AND email = #{email}
        </if>
         ORDER BY reg_dt DESC
    </select>

</mapper>
```

## 4. 비밀번호 암호화

### PasswordService.java

```java
package egovframework.mois.ums.service;

import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

/**
 * 비밀번호 암호화 서비스
 */
@Service("passwordService")
public class PasswordService {

    @Resource
    private PasswordEncoder passwordEncoder;

    /**
     * 비밀번호 암호화 (BCrypt)
     */
    public String encryptPassword(String rawPassword) {
        return passwordEncoder.encode(rawPassword);
    }

    /**
     * 비밀번호 검증
     */
    public boolean matches(String rawPassword, String encodedPassword) {
        return passwordEncoder.matches(rawPassword, encodedPassword);
    }
}
```

### UserServiceImpl.java (사용 예시)

```java
@Service("userService")
public class UserServiceImpl implements UserService {

    @Resource(name = "userDAO")
    private UserDAO userDAO;

    @Resource(name = "passwordService")
    private PasswordService passwordService;

    @Override
    @Transactional(readOnly = false, rollbackFor = Exception.class)
    public void insertUser(UserVO userVO) throws Exception {
        // 비밀번호 암호화
        String encryptedPassword = passwordService.encryptPassword(userVO.getPassword());
        userVO.setPassword(encryptedPassword);

        userDAO.insertUser(userVO);
    }
}
```

## 5. 입력값 검증

### ValidationUtil.java

```java
package egovframework.mois.ums.common.util;

import org.springframework.stereotype.Component;

import java.util.regex.Pattern;

/**
 * 입력값 검증 유틸리티
 */
@Component
public class ValidationUtil {

    private static final Pattern EMAIL_PATTERN =
        Pattern.compile("^[A-Za-z0-9+_.-]+@(.+)$");

    private static final Pattern PHONE_PATTERN =
        Pattern.compile("^010-\\d{4}-\\d{4}$");

    private static final Pattern USER_ID_PATTERN =
        Pattern.compile("^[a-zA-Z0-9_]{4,50}$");

    /**
     * 이메일 형식 검증
     */
    public boolean isValidEmail(String email) {
        if (email == null) return false;
        return EMAIL_PATTERN.matcher(email).matches();
    }

    /**
     * 휴대폰 번호 검증
     */
    public boolean isValidPhone(String phone) {
        if (phone == null) return false;
        return PHONE_PATTERN.matcher(phone).matches();
    }

    /**
     * 사용자 ID 검증
     */
    public boolean isValidUserId(String userId) {
        if (userId == null) return false;
        return USER_ID_PATTERN.matcher(userId).matches();
    }

    /**
     * SQL Injection 패턴 검사
     */
    public boolean hasSqlInjection(String input) {
        if (input == null) return false;

        String[] sqlKeywords = {
            "SELECT", "INSERT", "UPDATE", "DELETE", "DROP", "CREATE",
            "ALTER", "EXEC", "UNION", "--", ";", "/*", "*/"
        };

        String upperInput = input.toUpperCase();
        for (String keyword : sqlKeywords) {
            if (upperInput.contains(keyword)) {
                return true;
            }
        }
        return false;
    }
}
```

## 6. 세션 보안 설정

### web.xml

```xml
<!-- 세션 타임아웃 (30분) -->
<session-config>
    <session-timeout>30</session-timeout>
    <cookie-config>
        <http-only>true</http-only>
        <secure>true</secure>
    </cookie-config>
</session-config>
```

## 7. 보안 체크리스트

### 필수 보안 항목

- [x] **인증/인가**: Spring Security로 사용자 인증 및 권한 관리
- [x] **CSRF 방지**: CookieCsrfTokenRepository 사용
- [x] **XSS 방지**: XssFilter로 입력값 HTML 이스케이프
- [x] **SQL Injection 방지**: MyBatis #{} 파라미터 사용
- [x] **비밀번호 암호화**: BCrypt 알고리즘 사용
- [x] **세션 보안**: HttpOnly, Secure 쿠키, 타임아웃 설정
- [x] **입력값 검증**: Validation API 및 커스텀 검증
- [x] **보안 헤더**: X-Frame-Options, X-XSS-Protection, HSTS
- [x] **동시 세션 제한**: maximumSessions(1)
- [x] **세션 고정 공격 방지**: sessionFixation().changeSessionId()

### 추가 권장 사항

- [ ] HTTPS 강제 사용
- [ ] 로그인 실패 횟수 제한
- [ ] 2FA (이중 인증) 구현
- [ ] 민감 데이터 암호화 (AES-256)
- [ ] 접근 로그 기록
- [ ] 정기적인 보안 패치 적용

## 8. 테스트

### 로그인 테스트
```bash
curl -X POST http://localhost:8080/login \
  -d "userId=admin&password=password123" \
  -c cookies.txt
```

### CSRF 토큰 포함 요청
```bash
curl -X POST http://localhost:8080/api/v1/users \
  -H "X-CSRF-TOKEN: your-csrf-token" \
  -b cookies.txt \
  -d '{"userId":"user001","userNm":"홍길동"}'
```

보안 설정이 완료되었습니다!
```

# Example 2: RESTful API 표준 설정

## Input

```
요청: 전자정부 프레임워크에서 RESTful API를 구현하기 위한 표준 설정과 예제 코드를 만들어주세요

요구사항:
- JSON 기반 요청/응답
- 표준 에러 응답 포맷
- 입력값 검증
- 페이징 처리
- CORS 설정
```

## Output

```markdown
# eGov RESTful API 표준 구현 가이드

## 1. REST Controller 구조

### UserRestController.java

```java
package egovframework.mois.ums.web;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import egovframework.mois.ums.service.UserService;
import egovframework.mois.ums.vo.UserVO;
import egovframework.mois.ums.common.response.ApiResponse;
import egovframework.mois.ums.common.response.PaginationResponse;

import javax.annotation.Resource;
import javax.validation.Valid;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 사용자 관리 REST API 컨트롤러
 * @author eGov Framework
 * @since 2026-01-07
 */
@RestController
@RequestMapping("/api/v1/users")
@Validated
public class UserRestController {

    @Resource(name = "userService")
    private UserService userService;

    /**
     * 사용자 목록 조회 (페이징)
     *
     * @param userNm 사용자명 검색어 (선택)
     * @param pageIndex 페이지 번호 (0부터 시작)
     * @param pageSize 페이지당 항목 수
     * @return 페이징된 사용자 목록
     */
    @GetMapping
    public ResponseEntity<ApiResponse<PaginationResponse<UserVO>>> getUserList(
            @RequestParam(required = false) String userNm,
            @RequestParam(defaultValue = "0") int pageIndex,
            @RequestParam(defaultValue = "10") int pageSize) throws Exception {

        UserVO searchVO = new UserVO();
        searchVO.setUserNm(userNm);
        searchVO.setPageIndex(pageIndex);
        searchVO.setPageSize(pageSize);

        List<UserVO> list = userService.selectUserList(searchVO);
        int totalCount = userService.selectUserListTotalCount(searchVO);

        PaginationResponse<UserVO> pagination = new PaginationResponse<>(
            list, totalCount, pageIndex, pageSize
        );

        return ResponseEntity.ok(
            ApiResponse.success(pagination, "사용자 목록을 조회했습니다.")
        );
    }

    /**
     * 사용자 단건 조회
     *
     * @param userId 사용자 ID
     * @return 사용자 정보
     */
    @GetMapping("/{userId}")
    public ResponseEntity<ApiResponse<UserVO>> getUser(
            @PathVariable String userId) throws Exception {

        UserVO user = userService.selectUser(userId);
        return ResponseEntity.ok(
            ApiResponse.success(user, "사용자 정보를 조회했습니다.")
        );
    }

    /**
     * 사용자 등록
     *
     * @param userVO 사용자 정보 (Validation 적용)
     * @return 생성 결과
     */
    @PostMapping
    public ResponseEntity<ApiResponse<Map<String, Object>>> createUser(
            @RequestBody @Valid UserVO userVO) throws Exception {

        userService.insertUser(userVO);

        Map<String, Object> result = new HashMap<>();
        result.put("userId", userVO.getUserId());

        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(ApiResponse.success(result, "사용자가 등록되었습니다."));
    }

    /**
     * 사용자 수정
     *
     * @param userId 사용자 ID
     * @param userVO 수정할 사용자 정보
     * @return 수정 결과
     */
    @PutMapping("/{userId}")
    public ResponseEntity<ApiResponse<Void>> updateUser(
            @PathVariable String userId,
            @RequestBody @Valid UserVO userVO) throws Exception {

        userVO.setUserId(userId);
        userService.updateUser(userVO);

        return ResponseEntity.ok(
            ApiResponse.success(null, "사용자 정보가 수정되었습니다.")
        );
    }

    /**
     * 사용자 삭제 (논리 삭제)
     *
     * @param userId 사용자 ID
     * @return 삭제 결과
     */
    @DeleteMapping("/{userId}")
    public ResponseEntity<ApiResponse<Void>> deleteUser(
            @PathVariable String userId) throws Exception {

        userService.deleteUser(userId);

        return ResponseEntity.ok(
            ApiResponse.success(null, "사용자가 삭제되었습니다.")
        );
    }
}
```

## 2. 표준 응답 포맷

### ApiResponse.java (공통 응답 래퍼)

```java
package egovframework.mois.ums.common.response;

import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.Getter;
import lombok.Setter;

/**
 * API 표준 응답 포맷
 * @param <T> 데이터 타입
 */
@Getter
@Setter
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ApiResponse<T> {

    private boolean success;
    private T data;
    private String message;
    private ErrorDetail error;

    private ApiResponse(boolean success, T data, String message, ErrorDetail error) {
        this.success = success;
        this.data = data;
        this.message = message;
        this.error = error;
    }

    /**
     * 성공 응답 생성
     */
    public static <T> ApiResponse<T> success(T data, String message) {
        return new ApiResponse<>(true, data, message, null);
    }

    /**
     * 실패 응답 생성
     */
    public static <T> ApiResponse<T> error(String code, String message) {
        return new ApiResponse<>(false, null, null, new ErrorDetail(code, message));
    }

    /**
     * 에러 상세 정보
     */
    @Getter
    @Setter
    public static class ErrorDetail {
        private String code;
        private String message;

        public ErrorDetail(String code, String message) {
            this.code = code;
            this.message = message;
        }
    }
}
```

### PaginationResponse.java (페이징 응답)

```java
package egovframework.mois.ums.common.response;

import lombok.Getter;
import lombok.Setter;
import java.util.List;

/**
 * 페이징 응답 포맷
 * @param <T> 데이터 타입
 */
@Getter
@Setter
public class PaginationResponse<T> {

    private List<T> items;
    private PaginationInfo pagination;

    public PaginationResponse(List<T> items, int totalCount, int pageIndex, int pageSize) {
        this.items = items;
        this.pagination = new PaginationInfo(totalCount, pageIndex, pageSize);
    }

    @Getter
    @Setter
    public static class PaginationInfo {
        private int totalCount;
        private int pageIndex;
        private int pageSize;
        private int totalPages;

        public PaginationInfo(int totalCount, int pageIndex, int pageSize) {
            this.totalCount = totalCount;
            this.pageIndex = pageIndex;
            this.pageSize = pageSize;
            this.totalPages = (int) Math.ceil((double) totalCount / pageSize);
        }
    }
}
```

## 3. 입력값 검증 (Validation)

### UserVO.java (검증 어노테이션 추가)

```java
package egovframework.mois.ums.vo;

import lombok.Getter;
import lombok.Setter;

import javax.validation.constraints.*;
import java.util.Date;

/**
 * 사용자 VO (입력값 검증 포함)
 */
@Getter
@Setter
public class UserVO {

    @NotBlank(message = "사용자 ID는 필수입니다.")
    @Size(min = 4, max = 50, message = "사용자 ID는 4-50자여야 합니다.")
    @Pattern(regexp = "^[a-zA-Z0-9_]+$", message = "사용자 ID는 영문, 숫자, 밑줄만 가능합니다.")
    private String userId;

    @NotBlank(message = "사용자명은 필수입니다.")
    @Size(max = 100, message = "사용자명은 최대 100자입니다.")
    private String userNm;

    @NotBlank(message = "이메일은 필수입니다.")
    @Email(message = "올바른 이메일 형식이 아닙니다.")
    private String email;

    @NotBlank(message = "비밀번호는 필수입니다.")
    @Size(min = 8, max = 100, message = "비밀번호는 8자 이상이어야 합니다.")
    private String password;

    @Pattern(regexp = "^010-\\d{4}-\\d{4}$", message = "휴대폰 번호 형식이 올바르지 않습니다.")
    private String mobileNo;

    // 페이징 관련 필드
    private int pageIndex = 0;
    private int pageSize = 10;

    // 시스템 필드
    private String delYn;
    private Date regDt;
    private String regId;
    private Date updDt;
    private String updId;
}
```

## 4. 전역 예외 처리

### GlobalRestExceptionHandler.java

```java
package egovframework.mois.ums.common.exception;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import egovframework.mois.ums.common.response.ApiResponse;

/**
 * REST API 전역 예외 핸들러
 */
@RestControllerAdvice
public class GlobalRestExceptionHandler {

    private static final Logger LOGGER = LoggerFactory.getLogger(GlobalRestExceptionHandler.class);

    /**
     * Validation 실패 처리
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiResponse<Void>> handleValidationException(
            MethodArgumentNotValidException ex) {

        FieldError fieldError = ex.getBindingResult().getFieldError();
        String message = fieldError != null ? fieldError.getDefaultMessage() : "입력값이 올바르지 않습니다.";

        LOGGER.warn("Validation error: {}", message);

        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(ApiResponse.error("VALIDATION_ERROR", message));
    }

    /**
     * 비즈니스 예외 처리
     */
    @ExceptionHandler(EgovBusinessException.class)
    public ResponseEntity<ApiResponse<Void>> handleBusinessException(
            EgovBusinessException ex) {

        LOGGER.error("Business exception occurred", ex);

        return ResponseEntity
            .status(HttpStatus.BAD_REQUEST)
            .body(ApiResponse.error(ex.getMessageKey(), ex.getMessage()));
    }

    /**
     * 일반 예외 처리
     */
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiResponse<Void>> handleGenericException(Exception ex) {
        LOGGER.error("Unexpected exception occurred", ex);

        return ResponseEntity
            .status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(ApiResponse.error("INTERNAL_ERROR", "시스템 오류가 발생했습니다."));
    }
}
```

## 5. CORS 설정

### WebConfig.java

```java
package egovframework.mois.ums.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * Web MVC 설정 (CORS 포함)
 */
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://localhost:3000", "https://your-domain.go.kr")
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .allowedHeaders("*")
                .allowCredentials(true)
                .maxAge(3600);
    }
}
```

## 6. API 응답 예시

### 성공 응답

```json
{
  "success": true,
  "data": {
    "userId": "user001",
    "userNm": "홍길동",
    "email": "hong@example.com"
  },
  "message": "사용자 정보를 조회했습니다."
}
```

### 페이징 응답

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "userId": "user001",
        "userNm": "홍길동",
        "email": "hong@example.com"
      }
    ],
    "pagination": {
      "totalCount": 100,
      "pageIndex": 0,
      "pageSize": 10,
      "totalPages": 10
    }
  },
  "message": "사용자 목록을 조회했습니다."
}
```

### 에러 응답

```json
{
  "success": false,
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "사용자를 찾을 수 없습니다."
  }
}
```

### Validation 에러 응답

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "이메일은 필수입니다."
  }
}
```

## 7. 테스트 (cURL)

### 목록 조회
```bash
curl -X GET "http://localhost:8080/api/v1/users?pageIndex=0&pageSize=10"
```

### 단건 조회
```bash
curl -X GET "http://localhost:8080/api/v1/users/user001"
```

### 등록
```bash
curl -X POST http://localhost:8080/api/v1/users \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "user001",
    "userNm": "홍길동",
    "email": "hong@example.com",
    "password": "password123!",
    "mobileNo": "010-1234-5678"
  }'
```

### 수정
```bash
curl -X PUT http://localhost:8080/api/v1/users/user001 \
  -H "Content-Type: application/json" \
  -d '{
    "userNm": "김철수",
    "email": "kim@example.com"
  }'
```

### 삭제
```bash
curl -X DELETE http://localhost:8080/api/v1/users/user001
```

## 8. 체크리스트

- [x] REST Controller 구현 (`@RestController`)
- [x] 표준 응답 포맷 적용 (`ApiResponse`)
- [x] 페이징 처리 구현
- [x] 입력값 검증 (`@Valid`, `@NotBlank` 등)
- [x] 전역 예외 처리 (`@RestControllerAdvice`)
- [x] CORS 설정 완료
- [x] HTTP 메서드별 적절한 상태 코드 반환
- [x] 에러 응답 표준화

RESTful API 표준 설정이 완료되었습니다!
```

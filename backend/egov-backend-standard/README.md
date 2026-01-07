# eGov Backend Standard

## Overview

행안부 전자정부 표준프레임워크(eGovFrame) 기반의 Java 백엔드 개발 표준 체계를 수립하는 전문 에이전트입니다. 정부 IT 프로젝트에 필요한 프로젝트 구조, 코딩 컨벤션, 아키텍처 패턴, 보안 요구사항을 자동으로 구성하고 가이드를 제공합니다.

## Category

Backend / Development Standards

## Key Features

- **표준 프로젝트 구조**: eGovFrame 규격에 맞는 디렉토리 및 패키지 구조 자동 생성
- **Spring Framework 설정**: MVC, Security, Transaction, MyBatis 통합 설정
- **레이어드 아키텍처**: Presentation, Business, Persistence 계층 명확한 분리
- **코딩 컨벤션**: 클래스/메서드/변수 명명 규칙 및 Java 코드 스타일 가이드
- **보안 표준**: XSS, SQL Injection, CSRF 방지 등 정부 보안 요구사항 준수
- **MyBatis 패턴**: SQL 매퍼 작성 규칙 및 데이터 접근 계층 표준
- **예외 처리**: 표준 예외 계층 구조 및 전역 예외 핸들러
- **로깅 표준**: Log4j2 설정 및 로깅 레벨 가이드라인
- **API 설계**: RESTful API 규격 및 표준 응답 포맷
- **문서화**: 개발 표준 가이드, 코드 예제, 템플릿 제공

## eGovFrame Architecture Layers

| Layer | Package | Responsibilities |
|-------|---------|------------------|
| **Presentation** | `web` | Controller, Request/Response handling, Validation |
| **Business** | `service`, `service.impl` | Business logic, Transaction management |
| **Persistence** | `dao` | Data access, MyBatis SQL execution |
| **VO/DTO** | `vo` | Data transfer objects |
| **Common** | `common` | Utilities, Exceptions, Interceptors |

## Standard Naming Conventions

| Component | Naming Pattern | Example |
|-----------|----------------|---------|
| Controller | `*Controller.java` | `UserController.java` |
| Service Interface | `*Service.java` | `UserService.java` |
| Service Impl | `*ServiceImpl.java` | `UserServiceImpl.java` |
| DAO | `*DAO.java` | `UserDAO.java` |
| VO/DTO | `*VO.java`, `*DTO.java` | `UserVO.java` |
| SQL Mapper | `*_SQL.xml` | `User_SQL.xml` |
| Spring Config | `context-*.xml` | `context-security.xml` |

## CRUD Method Naming

- `select*`: 단건 조회 (e.g., `selectUser`)
- `select*List`: 목록 조회 (e.g., `selectUserList`)
- `insert*`: 등록 (e.g., `insertUser`)
- `update*`: 수정 (e.g., `updateUser`)
- `delete*`: 삭제 (e.g., `deleteUser`)

## Usage

### As Standalone Agent

프로젝트 초기 구조 및 표준 수립:

```
Task({
  subagent_type: "egov-backend-standard",
  prompt: "행안부 표준에 맞는 사용자 관리 시스템 백엔드 구조를 생성해줘. 프로젝트명: user-management, 기관: mois, 시스템: ums",
  description: "Generate eGov project structure"
})
```

특정 표준 문서 생성:

```
Task({
  subagent_type: "egov-backend-standard",
  prompt: "MyBatis SQL 매퍼 작성 가이드와 예제를 만들어줘",
  description: "Create MyBatis guide"
})
```

보안 설정 구성:

```
Task({
  subagent_type: "egov-backend-standard",
  prompt: "Spring Security 설정과 XSS/CSRF 방지 코드를 생성해줘",
  description: "Setup security configuration"
})
```

## Output Format

### Project Structure
```
project-root/
├── src/main/java/egovframework/[agency]/[system]/
│   ├── web/          # Controllers
│   ├── service/      # Business layer
│   ├── dao/          # Data access
│   ├── vo/           # Value objects
│   └── common/       # Common components
├── src/main/resources/
│   ├── egovframework/spring/  # Spring configs
│   └── egovframework/sqlmap/  # SQL mappers
└── pom.xml
```

### Standard Documents Generated
1. **개발 표준 가이드**: 전체 개발 규칙 및 컨벤션
2. **코드 예제**: Controller, Service, DAO, VO 샘플 코드
3. **Spring 설정 파일**: context-*.xml 파일들
4. **MyBatis 매퍼**: SQL 작성 예제
5. **보안 체크리스트**: 필수 보안 항목
6. **API 설계 가이드**: RESTful API 규격

## Spring Configuration Files

| File | Purpose |
|------|---------|
| `context-common.xml` | Common beans, component scan |
| `context-datasource.xml` | Database connection pool |
| `context-mapper.xml` | MyBatis SqlSessionFactory |
| `context-security.xml` | Spring Security settings |
| `context-transaction.xml` | Transaction management |

## Security Checklist

- [ ] XSS Prevention: JSTL `<c:out>` or HTML escaping
- [ ] SQL Injection Prevention: PreparedStatement/MyBatis
- [ ] CSRF Protection: CSRF tokens enabled
- [ ] Session Security: Timeout and secure flags
- [ ] Password Encryption: BCrypt or SHA-256
- [ ] Input Validation: All user inputs validated
- [ ] Authorization: Method-level permission checks

## Integration

### Development Workflow
```
1. egov-backend-standard (프로젝트 구조 생성)
   ↓
2. 개발팀 (표준 가이드에 따라 기능 개발)
   ↓
3. Code Review (컨벤션 준수 확인)
   ↓
4. Deployment (표준 설정 기반 배포)
```

### Works Well With
- **PRD Task Generator**: PRD를 작업으로 분해 후 백엔드 구조 생성
- **Code Review Agents**: 코딩 컨벤션 준수 자동 검증
- **Documentation Agents**: API 문서 자동 생성

## Best Practices

### Project Initialization
1. 프로젝트 정보 수집 (이름, 기관명, 시스템명, DB 종류)
2. 표준 구조 생성
3. Spring 설정 파일 구성
4. 공통 컴포넌트 생성
5. 개발 가이드 문서화

### Code Development
1. 레이어별 역할 명확히 구분
2. 명명 규칙 일관성 유지
3. 트랜잭션 적절히 관리
4. 예외 처리 표준화
5. 로깅 체계적으로 기록

### Quality Assurance
1. 보안 체크리스트 검증
2. 코드 리뷰로 컨벤션 확인
3. SQL 성능 최적화
4. 단위 테스트 작성

## Limitations

- Spring Boot가 아닌 전통적인 Spring Framework 기반
- JSP 뷰 템플릿 사용 (React/Vue 등 별도 설정 필요)
- XML 기반 설정 (Java Config 대신)
- 한국 정부 프로젝트 특화 (일반 프로젝트는 조정 필요)

## Version History

- **v1.0.0** (2026-01-07): Initial release
  - eGovFrame 3.x/4.x 지원
  - Spring 5.x 기반
  - MyBatis 3.x 통합
  - Spring Security 5.x

## Author

Custom Backend Standards

## Related Resources

- [전자정부 표준프레임워크 포털](https://www.egovframe.go.kr/)
- [Spring Framework Documentation](https://spring.io/projects/spring-framework)
- [MyBatis Documentation](https://mybatis.org/mybatis-3/)
- [행안부 SW 개발보안 가이드](https://www.mois.go.kr/)

# eGov Frontend Standard

## Overview

행안부 전자정부 표준프레임워크 기반의 프론트엔드 개발 표준 체계를 수립하는 전문 에이전트입니다. JSP + jQuery를 사용하여 웹 접근성(WCAG 2.1, 한국형 웹 접근성 지침)과 웹 표준(HTML5, CSS3)을 준수하는 정부 웹사이트를 구축합니다.

## Category

Frontend / Development Standards

## Key Features

- **JSP 표준 구조**: eGovFrame 규격에 맞는 JSP 페이지 템플릿 및 레이아웃
- **웹 접근성**: WCAG 2.1, KWCAG 2.1 (한국형 웹 콘텐츠 접근성 지침) 준수
- **웹 표준**: HTML5, CSS3, 시맨틱 마크업
- **ARIA 속성**: 스크린 리더 지원 및 키보드 내비게이션
- **jQuery 패턴**: 정부 프로젝트에 적합한 jQuery 사용법
- **JSTL 활용**: JSP Standard Tag Library 표준 사용
- **크로스 브라우징**: IE11+, Chrome, Firefox, Edge 지원
- **반응형 디자인**: 모바일/태블릿/데스크톱 대응
- **폼 검증**: 클라이언트/서버 사이드 검증
- **성능 최적화**: 리소스 최소화, 지연 로딩

## Web Accessibility Standards

| Standard | Level | Description |
|----------|-------|-------------|
| **WCAG 2.1** | AA | 국제 웹 접근성 표준 |
| **KWCAG 2.1** | 인증 | 한국형 웹 콘텐츠 접근성 지침 |
| **장애인차별금지법** | 준수 | 법적 의무사항 |

### 4대 핵심 원칙

1. **인식의 용이성** (Perceivable)
   - 대체 텍스트 제공
   - 자막/수화 제공
   - 명확한 구분

2. **운용의 용이성** (Operable)
   - 키보드 접근성
   - 충분한 시간 제공
   - 광과민성 발작 예방

3. **이해의 용이성** (Understandable)
   - 가독성
   - 예측 가능성
   - 콘텐츠의 논리성

4. **견고성** (Robust)
   - 웹 브라우저 호환성
   - 보조기술 호환성

## JSP Page Structure

### Standard Layout
```
webapp/
├── WEB-INF/
│   ├── jsp/
│   │   ├── layout/
│   │   │   ├── header.jsp       # 공통 헤더
│   │   │   ├── footer.jsp       # 공통 푸터
│   │   │   ├── gnb.jsp          # 전역 내비게이션
│   │   │   └── lnb.jsp          # 로컬 내비게이션
│   │   ├── user/                # 사용자 관리
│   │   │   ├── list.jsp
│   │   │   ├── detail.jsp
│   │   │   └── form.jsp
│   │   ├── common/              # 공통 페이지
│   │   │   ├── error.jsp
│   │   │   └── notFound.jsp
│   │   └── main/
│   │       └── index.jsp        # 메인 페이지
│   └── tiles/
│       └── tiles-config.xml     # Tiles 레이아웃 설정
└── resources/
    ├── css/
    │   ├── common.css           # 공통 스타일
    │   ├── layout.css           # 레이아웃
    │   └── accessibility.css    # 접근성 스타일
    ├── js/
    │   ├── common.js            # 공통 스크립트
    │   ├── validation.js        # 폼 검증
    │   └── accessibility.js     # 접근성 스크립트
    └── images/
```

## HTML5 Semantic Tags

| Tag | Purpose | Example |
|-----|---------|---------|
| `<header>` | 페이지/섹션 헤더 | 사이트 상단 영역 |
| `<nav>` | 내비게이션 메뉴 | GNB, LNB |
| `<main>` | 주요 콘텐츠 | 본문 영역 |
| `<article>` | 독립적인 콘텐츠 | 게시글, 뉴스 |
| `<section>` | 콘텐츠 섹션 | 주제별 구분 |
| `<aside>` | 부가 콘텐츠 | 사이드바 |
| `<footer>` | 페이지/섹션 푸터 | 하단 정보 |

## ARIA Attributes

### Common ARIA Roles
```html
<nav role="navigation" aria-label="주 메뉴">
<main role="main">
<button role="button" aria-pressed="false">
<div role="alert" aria-live="polite">
<input type="text" aria-required="true" aria-invalid="false">
```

### ARIA States & Properties
- `aria-label`: 요소 레이블
- `aria-labelledby`: 레이블 참조
- `aria-describedby`: 설명 참조
- `aria-hidden`: 스크린 리더에서 숨김
- `aria-expanded`: 확장 상태
- `aria-selected`: 선택 상태
- `aria-live`: 동적 콘텐츠 알림

## jQuery Best Practices

### 1. DOM Ready
```javascript
$(document).ready(function() {
    // 초기화 코드
});

// 또는 축약형
$(function() {
    // 초기화 코드
});
```

### 2. 이벤트 위임
```javascript
// 좋은 예 - 이벤트 위임
$('#userList').on('click', '.btn-delete', function() {
    // 삭제 로직
});

// 나쁜 예 - 직접 바인딩
$('.btn-delete').on('click', function() {
    // 동적 요소에서 작동 안 함
});
```

### 3. 체이닝
```javascript
$('#myElement')
    .addClass('active')
    .fadeIn(300)
    .text('새로운 내용');
```

## JSTL Usage

### Core Tags
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<c:if test="${not empty userList}">
    <c:forEach var="user" items="${userList}">
        <tr>
            <td><c:out value="${user.userNm}" /></td>
        </tr>
    </c:forEach>
</c:if>

<c:choose>
    <c:when test="${user.role == 'ADMIN'}">관리자</c:when>
    <c:otherwise>일반 사용자</c:otherwise>
</c:choose>
```

### Format Tags
```jsp
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>

<fmt:formatDate value="${user.regDt}" pattern="yyyy-MM-dd HH:mm:ss" />
<fmt:formatNumber value="${product.price}" pattern="#,##0" />
```

## Accessibility Checklist

### 필수 항목

- [ ] **대체 텍스트**: 모든 이미지에 `alt` 속성
- [ ] **폼 레이블**: 모든 입력 필드에 `<label>` 연결
- [ ] **키보드 접근**: Tab 키로 모든 기능 접근 가능
- [ ] **포커스 표시**: 키보드 포커스 시각적으로 명확
- [ ] **색상 대비**: 텍스트와 배경 명도 대비 4.5:1 이상
- [ ] **제목 구조**: `<h1>`~`<h6>` 순서대로 사용
- [ ] **링크 텍스트**: "여기" 대신 구체적인 텍스트
- [ ] **건너뛰기 링크**: 본문으로 바로가기
- [ ] **표 구조**: `<th>`, `<caption>`, `scope` 사용
- [ ] **에러 메시지**: 명확하고 이해하기 쉬운 안내

### 권장 항목

- [ ] ARIA 속성 적용
- [ ] 반응형 디자인
- [ ] 고대비 모드 지원
- [ ] 텍스트 크기 조절 가능
- [ ] 자동재생 동영상 제어 가능

## Cross-Browser Support

### Supported Browsers
- Internet Explorer 11+
- Chrome (latest)
- Firefox (latest)
- Edge (latest)
- Safari (latest - macOS)

### Polyfills
```html
<!-- IE11 지원 -->
<!--[if lt IE 9]>
    <script src="/resources/js/html5shiv.min.js"></script>
    <script src="/resources/js/respond.min.js"></script>
<![endif]-->

<script src="/resources/js/polyfill.min.js"></script>
```

## Form Validation

### Client-side (jQuery)
```javascript
function validateForm() {
    var userId = $('#userId').val();

    if (userId.trim() === '') {
        alert('사용자 ID를 입력하세요.');
        $('#userId').focus();
        return false;
    }

    return true;
}
```

### Server-side (Spring Validation)
```java
@Valid UserVO userVO
```

## Usage

### As Standalone Agent

JSP 페이지 생성:
```
Task({
  subagent_type: "egov-frontend-standard",
  prompt: "웹 접근성을 만족하는 사용자 목록 페이지를 JSP로 생성해줘",
  description: "Generate accessible user list page"
})
```

접근성 개선:
```
Task({
  subagent_type: "egov-frontend-standard",
  prompt: "기존 JSP 페이지에 ARIA 속성과 키보드 접근성을 추가해줘",
  description: "Improve accessibility"
})
```

반응형 레이아웃:
```
Task({
  subagent_type: "egov-frontend-standard",
  prompt: "정부 사이트 메인 페이지를 반응형으로 만들어줘",
  description: "Create responsive main page"
})
```

## Output Format

### Standard JSP Template
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>페이지 제목</title>
    <link rel="stylesheet" href="/resources/css/common.css">
</head>
<body>
    <div id="skip-nav">
        <a href="#content">본문 바로가기</a>
    </div>

    <header role="banner">
        <%@ include file="/WEB-INF/jsp/layout/header.jsp" %>
    </header>

    <nav role="navigation">
        <%@ include file="/WEB-INF/jsp/layout/gnb.jsp" %>
    </nav>

    <main role="main" id="content">
        <!-- 페이지 콘텐츠 -->
    </main>

    <footer role="contentinfo">
        <%@ include file="/WEB-INF/jsp/layout/footer.jsp" %>
    </footer>

    <script src="/resources/js/jquery-3.6.0.min.js"></script>
    <script src="/resources/js/common.js"></script>
</body>
</html>
```

## CSS Guidelines

### Naming Convention (BEM)
```css
/* Block */
.user-list { }

/* Element */
.user-list__item { }
.user-list__title { }

/* Modifier */
.user-list--grid { }
.user-list__item--active { }
```

### Responsive Breakpoints
```css
/* Mobile First */
.container { width: 100%; }

/* Tablet */
@media (min-width: 768px) {
    .container { width: 750px; }
}

/* Desktop */
@media (min-width: 1200px) {
    .container { width: 1170px; }
}
```

## Performance Optimization

### Image Optimization
- 적절한 이미지 포맷 사용 (JPEG, PNG, WebP)
- 이미지 압축 및 최적화
- Lazy Loading 적용
- Sprite 이미지 사용

### Resource Minification
```html
<!-- 배포 시 -->
<link rel="stylesheet" href="/resources/css/common.min.css">
<script src="/resources/js/bundle.min.js"></script>
```

### CDN Usage
```html
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
```

## Integration

### With Backend
```
Backend (Spring MVC Controller)
    ↓ Model Data
JSP View (egov-frontend-standard)
    ↓ Render HTML
User Browser
```

### Works Well With
- **egov-backend-standard**: 백엔드 API와 연동
- **Accessibility Testing Tools**: WAVE, Lighthouse
- **Code Quality Tools**: JSHint, CSSLint

## Limitations

- jQuery 중심 (Vue/React 등 모던 프레임워크 미사용)
- 서버 사이드 렌더링 (CSR 대신 SSR)
- IE11 지원으로 인한 최신 JS 기능 제한
- JSP 기반으로 빌드 프로세스 복잡도 낮음

## Version History

- **v1.0.0** (2026-01-07): Initial release
  - JSP + jQuery 기반
  - WCAG 2.1 AA 준수
  - KWCAG 2.1 준수
  - HTML5/CSS3 표준
  - 크로스 브라우저 지원

## Author

Custom Frontend Standards

## Related Resources

- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [한국형 웹 콘텐츠 접근성 지침 2.1](https://www.wah.or.kr/)
- [전자정부 표준프레임워크 포털](https://www.egovframe.go.kr/)
- [jQuery Documentation](https://api.jquery.com/)
- [JSTL Tag Reference](https://docs.oracle.com/javaee/5/jstl/1.1/docs/tlddocs/)
- [웹 접근성 연구소](https://www.wah.or.kr/)

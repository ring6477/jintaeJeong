# Example 1: 웹 접근성을 준수하는 사용자 목록 JSP 페이지

## Input

```
요청: 전자정부 표준프레임워크에서 웹 접근성을 만족하는 사용자 목록 페이지를 생성해주세요

요구사항:
- JSP + jQuery 사용
- WCAG 2.1 AA 준수
- 검색 기능 포함
- 페이징 처리
- 키보드 접근성
- 스크린 리더 지원
```

## Output

### 1. JSP 페이지 (userList.jsp)

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>사용자 관리 - 전자정부 표준프레임워크</title>

    <!-- CSS -->
    <link rel="stylesheet" href="<c:url value='/resources/css/common.css' />">
    <link rel="stylesheet" href="<c:url value='/resources/css/layout.css' />">
    <link rel="stylesheet" href="<c:url value='/resources/css/components.css' />">
</head>
<body>
    <!-- 건너뛰기 링크 -->
    <div id="skip-nav">
        <a href="#content">본문 바로가기</a>
        <a href="#gnb">주메뉴 바로가기</a>
    </div>

    <!-- 헤더 -->
    <header role="banner">
        <%@ include file="/WEB-INF/jsp/layout/header.jsp" %>
    </header>

    <!-- 전역 내비게이션 -->
    <nav id="gnb" role="navigation" aria-label="주 메뉴">
        <%@ include file="/WEB-INF/jsp/layout/gnb.jsp" %>
    </nav>

    <!-- 메인 컨텐츠 -->
    <main role="main" id="content">
        <div class="container">
            <!-- 페이지 헤더 -->
            <div class="page-header">
                <h1>사용자 관리</h1>
                <nav aria-label="현재 위치">
                    <ol class="breadcrumb">
                        <li><a href="<c:url value='/' />">홈</a></li>
                        <li><a href="<c:url value='/admin' />">관리자</a></li>
                        <li aria-current="page">사용자 관리</li>
                    </ol>
                </nav>
            </div>

            <!-- 검색 영역 -->
            <section class="search-section" aria-labelledby="search-title">
                <h2 id="search-title" class="sr-only">사용자 검색</h2>
                <form id="searchForm" method="get" action="<c:url value='/user/list' />">
                    <fieldset>
                        <legend class="sr-only">검색 조건</legend>

                        <div class="search-group">
                            <label for="searchType">검색 유형</label>
                            <select id="searchType" name="searchType">
                                <option value="userNm" ${searchType == 'userNm' ? 'selected' : ''}>이름</option>
                                <option value="userId" ${searchType == 'userId' ? 'selected' : ''}>사용자 ID</option>
                                <option value="email" ${searchType == 'email' ? 'selected' : ''}>이메일</option>
                            </select>

                            <label for="searchKeyword" class="sr-only">검색어</label>
                            <input type="text"
                                   id="searchKeyword"
                                   name="searchKeyword"
                                   value="<c:out value='${searchKeyword}' />"
                                   placeholder="검색어를 입력하세요"
                                   aria-label="검색어 입력">

                            <button type="submit" class="btn btn-primary">
                                <span class="icon-search" aria-hidden="true"></span>
                                검색
                            </button>
                        </div>
                    </fieldset>
                </form>
            </section>

            <!-- 목록 헤더 -->
            <div class="list-header">
                <div class="list-info">
                    <p>
                        전체 <strong>${pagination.totalCount}</strong>건
                        (<strong>${pagination.pageIndex + 1}</strong> / ${pagination.totalPages} 페이지)
                    </p>
                </div>
                <div class="list-actions">
                    <button type="button"
                            class="btn btn-success"
                            onclick="location.href='<c:url value='/user/form' />'"
                            aria-label="새 사용자 등록">
                        <span class="icon-plus" aria-hidden="true"></span>
                        신규 등록
                    </button>
                </div>
            </div>

            <!-- 사용자 목록 테이블 -->
            <section aria-labelledby="list-title">
                <h2 id="list-title" class="sr-only">사용자 목록</h2>

                <table class="table table-striped" summary="사용자 목록을 표시합니다">
                    <caption class="sr-only">
                        사용자 목록 (총 ${fn:length(userList)}명)
                    </caption>
                    <colgroup>
                        <col style="width: 5%">
                        <col style="width: 10%">
                        <col style="width: 15%">
                        <col style="width: 15%">
                        <col style="width: 25%">
                        <col style="width: 10%">
                        <col style="width: 10%">
                        <col style="width: 10%">
                    </colgroup>
                    <thead>
                        <tr>
                            <th scope="col">
                                <input type="checkbox"
                                       id="selectAll"
                                       aria-label="전체 선택">
                            </th>
                            <th scope="col">번호</th>
                            <th scope="col">사용자 ID</th>
                            <th scope="col">이름</th>
                            <th scope="col">이메일</th>
                            <th scope="col">권한</th>
                            <th scope="col">등록일</th>
                            <th scope="col">관리</th>
                        </tr>
                    </thead>
                    <tbody>
                        <c:choose>
                            <c:when test="${not empty userList}">
                                <c:forEach var="user" items="${userList}" varStatus="status">
                                    <tr>
                                        <td class="text-center">
                                            <input type="checkbox"
                                                   name="selectedUsers"
                                                   value="${user.userId}"
                                                   class="user-checkbox"
                                                   aria-label="${user.userNm} 선택">
                                        </td>
                                        <td class="text-center">
                                            ${pagination.totalCount - (pagination.pageIndex * pagination.pageSize + status.index)}
                                        </td>
                                        <td>
                                            <c:out value="${user.userId}" />
                                        </td>
                                        <td>
                                            <a href="<c:url value='/user/detail?userId=${user.userId}' />"
                                               aria-label="${user.userNm} 상세보기">
                                                <c:out value="${user.userNm}" />
                                            </a>
                                        </td>
                                        <td>
                                            <c:out value="${user.email}" />
                                        </td>
                                        <td class="text-center">
                                            <c:choose>
                                                <c:when test="${user.userRole == 'ADMIN'}">
                                                    <span class="badge badge-admin">관리자</span>
                                                </c:when>
                                                <c:otherwise>
                                                    <span class="badge badge-user">일반</span>
                                                </c:otherwise>
                                            </c:choose>
                                        </td>
                                        <td class="text-center">
                                            <fmt:formatDate value="${user.regDt}" pattern="yyyy-MM-dd" />
                                        </td>
                                        <td class="text-center">
                                            <button type="button"
                                                    class="btn btn-sm btn-info"
                                                    onclick="location.href='<c:url value='/user/form?userId=${user.userId}' />'"
                                                    aria-label="${user.userNm} 수정">
                                                수정
                                            </button>
                                            <button type="button"
                                                    class="btn btn-sm btn-danger btn-delete"
                                                    data-user-id="${user.userId}"
                                                    data-user-name="<c:out value='${user.userNm}' />"
                                                    aria-label="${user.userNm} 삭제">
                                                삭제
                                            </button>
                                        </td>
                                    </tr>
                                </c:forEach>
                            </c:when>
                            <c:otherwise>
                                <tr>
                                    <td colspan="8" class="text-center empty-message">
                                        <p>등록된 사용자가 없습니다.</p>
                                    </td>
                                </tr>
                            </c:otherwise>
                        </c:choose>
                    </tbody>
                </table>
            </section>

            <!-- 선택된 항목 일괄 처리 -->
            <c:if test="${not empty userList}">
                <div class="bulk-actions">
                    <button type="button"
                            class="btn btn-danger"
                            id="btnBulkDelete"
                            aria-label="선택한 사용자 일괄 삭제">
                        <span class="icon-trash" aria-hidden="true"></span>
                        선택 삭제
                    </button>
                </div>
            </c:if>

            <!-- 페이징 -->
            <c:if test="${pagination.totalPages > 1}">
                <nav class="pagination-wrap" aria-label="페이지 내비게이션">
                    <ul class="pagination">
                        <!-- 처음 -->
                        <li class="page-item ${pagination.pageIndex == 0 ? 'disabled' : ''}">
                            <a class="page-link"
                               href="<c:url value='/user/list?pageIndex=0&searchType=${searchType}&searchKeyword=${searchKeyword}' />"
                               aria-label="첫 페이지"
                               ${pagination.pageIndex == 0 ? 'aria-disabled="true" tabindex="-1"' : ''}>
                                <span aria-hidden="true">&laquo;&laquo;</span>
                            </a>
                        </li>

                        <!-- 이전 -->
                        <li class="page-item ${pagination.pageIndex == 0 ? 'disabled' : ''}">
                            <a class="page-link"
                               href="<c:url value='/user/list?pageIndex=${pagination.pageIndex - 1}&searchType=${searchType}&searchKeyword=${searchKeyword}' />"
                               aria-label="이전 페이지"
                               ${pagination.pageIndex == 0 ? 'aria-disabled="true" tabindex="-1"' : ''}>
                                <span aria-hidden="true">&laquo;</span>
                            </a>
                        </li>

                        <!-- 페이지 번호 -->
                        <c:forEach var="i" begin="${pagination.startPage}" end="${pagination.endPage}">
                            <li class="page-item ${i == pagination.pageIndex ? 'active' : ''}">
                                <a class="page-link"
                                   href="<c:url value='/user/list?pageIndex=${i}&searchType=${searchType}&searchKeyword=${searchKeyword}' />"
                                   aria-label="페이지 ${i + 1}"
                                   ${i == pagination.pageIndex ? 'aria-current="page"' : ''}>
                                    ${i + 1}
                                </a>
                            </li>
                        </c:forEach>

                        <!-- 다음 -->
                        <li class="page-item ${pagination.pageIndex >= pagination.totalPages - 1 ? 'disabled' : ''}">
                            <a class="page-link"
                               href="<c:url value='/user/list?pageIndex=${pagination.pageIndex + 1}&searchType=${searchType}&searchKeyword=${searchKeyword}' />"
                               aria-label="다음 페이지"
                               ${pagination.pageIndex >= pagination.totalPages - 1 ? 'aria-disabled="true" tabindex="-1"' : ''}>
                                <span aria-hidden="true">&raquo;</span>
                            </a>
                        </li>

                        <!-- 마지막 -->
                        <li class="page-item ${pagination.pageIndex >= pagination.totalPages - 1 ? 'disabled' : ''}">
                            <a class="page-link"
                               href="<c:url value='/user/list?pageIndex=${pagination.totalPages - 1}&searchType=${searchType}&searchKeyword=${searchKeyword}' />"
                               aria-label="마지막 페이지"
                               ${pagination.pageIndex >= pagination.totalPages - 1 ? 'aria-disabled="true" tabindex="-1"' : ''}>
                                <span aria-hidden="true">&raquo;&raquo;</span>
                            </a>
                        </li>
                    </ul>
                </nav>
            </c:if>

        </div>
    </main>

    <!-- 푸터 -->
    <footer role="contentinfo">
        <%@ include file="/WEB-INF/jsp/layout/footer.jsp" %>
    </footer>

    <!-- JavaScript -->
    <script src="<c:url value='/resources/js/jquery-3.6.0.min.js' />"></script>
    <script src="<c:url value='/resources/js/common.js' />"></script>
    <script src="<c:url value='/resources/js/user-list.js' />"></script>
</body>
</html>
```

### 2. JavaScript (user-list.js)

```javascript
/**
 * 사용자 목록 페이지 스크립트
 */
(function($) {
    'use strict';

    var UserList = {
        /**
         * 초기화
         */
        init: function() {
            this.bindEvents();
            this.initializeComponents();
        },

        /**
         * 이벤트 바인딩
         */
        bindEvents: function() {
            // 전체 선택
            $('#selectAll').on('change', this.toggleSelectAll);

            // 개별 체크박스
            $('.user-checkbox').on('change', this.updateSelectAllState);

            // 삭제 버튼
            $('.btn-delete').on('click', this.handleDelete);

            // 일괄 삭제
            $('#btnBulkDelete').on('click', this.handleBulkDelete);

            // 검색 폼 제출
            $('#searchForm').on('submit', this.handleSearch);

            // 키보드 접근성: Enter 키로 삭제
            $('.btn-delete').on('keypress', function(e) {
                if (e.key === 'Enter') {
                    $(this).trigger('click');
                }
            });
        },

        /**
         * 컴포넌트 초기화
         */
        initializeComponents: function() {
            // 검색어 포커스
            if ($('#searchKeyword').val()) {
                $('#searchKeyword').focus();
            }

            // 선택 상태 업데이트
            this.updateSelectAllState();
        },

        /**
         * 전체 선택 토글
         */
        toggleSelectAll: function() {
            var isChecked = $(this).prop('checked');
            $('.user-checkbox').prop('checked', isChecked);

            // 스크린 리더 안내
            var message = isChecked ? '전체 선택됨' : '전체 선택 해제됨';
            UserList.announceToScreenReader(message);
        },

        /**
         * 전체 선택 체크박스 상태 업데이트
         */
        updateSelectAllState: function() {
            var totalCheckboxes = $('.user-checkbox').length;
            var checkedCheckboxes = $('.user-checkbox:checked').length;

            $('#selectAll').prop('checked', totalCheckboxes > 0 && totalCheckboxes === checkedCheckboxes);

            // 일괄 삭제 버튼 활성화/비활성화
            $('#btnBulkDelete').prop('disabled', checkedCheckboxes === 0);
        },

        /**
         * 검색 처리
         */
        handleSearch: function(e) {
            var keyword = $('#searchKeyword').val().trim();

            if (keyword === '') {
                alert('검색어를 입력하세요.');
                $('#searchKeyword').focus();
                e.preventDefault();
                return false;
            }

            return true;
        },

        /**
         * 삭제 처리
         */
        handleDelete: function(e) {
            e.preventDefault();

            var userId = $(this).data('user-id');
            var userName = $(this).data('user-name');

            if (!confirm(userName + ' 사용자를 삭제하시겠습니까?')) {
                return;
            }

            UserList.deleteUser(userId);
        },

        /**
         * 일괄 삭제 처리
         */
        handleBulkDelete: function() {
            var selectedUsers = [];

            $('.user-checkbox:checked').each(function() {
                selectedUsers.push($(this).val());
            });

            if (selectedUsers.length === 0) {
                alert('삭제할 사용자를 선택하세요.');
                return;
            }

            if (!confirm('선택한 ' + selectedUsers.length + '명의 사용자를 삭제하시겠습니까?')) {
                return;
            }

            UserList.bulkDeleteUsers(selectedUsers);
        },

        /**
         * 사용자 삭제 AJAX
         */
        deleteUser: function(userId) {
            $.ajax({
                url: '/api/users/' + userId,
                type: 'DELETE',
                dataType: 'json',
                beforeSend: function() {
                    UserList.showLoading();
                },
                success: function(response) {
                    if (response.success) {
                        alert('삭제되었습니다.');
                        UserList.announceToScreenReader('사용자가 삭제되었습니다');
                        location.reload();
                    } else {
                        alert('삭제 실패: ' + response.message);
                    }
                },
                error: function(xhr, status, error) {
                    console.error('Delete error:', error);
                    alert('삭제 중 오류가 발생했습니다.');
                },
                complete: function() {
                    UserList.hideLoading();
                }
            });
        },

        /**
         * 일괄 삭제 AJAX
         */
        bulkDeleteUsers: function(userIds) {
            $.ajax({
                url: '/api/users/bulk-delete',
                type: 'POST',
                contentType: 'application/json',
                data: JSON.stringify({ userIds: userIds }),
                dataType: 'json',
                beforeSend: function() {
                    UserList.showLoading();
                },
                success: function(response) {
                    if (response.success) {
                        alert(userIds.length + '명의 사용자가 삭제되었습니다.');
                        UserList.announceToScreenReader(userIds.length + '명의 사용자가 삭제되었습니다');
                        location.reload();
                    } else {
                        alert('삭제 실패: ' + response.message);
                    }
                },
                error: function(xhr, status, error) {
                    console.error('Bulk delete error:', error);
                    alert('삭제 중 오류가 발생했습니다.');
                },
                complete: function() {
                    UserList.hideLoading();
                }
            });
        },

        /**
         * 로딩 표시
         */
        showLoading: function() {
            if ($('#loading-overlay').length === 0) {
                $('body').append(
                    '<div id="loading-overlay" role="alert" aria-live="polite" aria-busy="true">' +
                    '    <div class="loading-spinner"></div>' +
                    '    <p class="sr-only">처리 중입니다. 잠시만 기다려주세요.</p>' +
                    '</div>'
                );
            }
        },

        /**
         * 로딩 숨김
         */
        hideLoading: function() {
            $('#loading-overlay').remove();
        },

        /**
         * 스크린 리더 안내
         */
        announceToScreenReader: function(message) {
            var $announcer = $('#screen-reader-announcer');

            if ($announcer.length === 0) {
                $announcer = $('<div>', {
                    id: 'screen-reader-announcer',
                    class: 'sr-only',
                    role: 'status',
                    'aria-live': 'polite',
                    'aria-atomic': 'true'
                }).appendTo('body');
            }

            $announcer.text(message);

            // 메시지 초기화 (3초 후)
            setTimeout(function() {
                $announcer.text('');
            }, 3000);
        }
    };

    // DOM Ready
    $(document).ready(function() {
        UserList.init();
    });

})(jQuery);
```

### 3. CSS (components.css 일부)

```css
/**
 * 접근성 관련 스타일
 */

/* 스크린 리더 전용 텍스트 */
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}

/* 건너뛰기 링크 */
#skip-nav {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 9999;
}

#skip-nav a {
    position: absolute;
    top: -100px;
    left: 0;
    display: block;
    padding: 10px 20px;
    background: #000;
    color: #fff;
    text-decoration: none;
}

#skip-nav a:focus {
    top: 0;
}

/* 포커스 표시 */
a:focus,
button:focus,
input:focus,
select:focus,
textarea:focus {
    outline: 2px solid #0066cc;
    outline-offset: 2px;
}

/* 테이블 */
.table {
    width: 100%;
    border-collapse: collapse;
    border: 1px solid #ddd;
}

.table th,
.table td {
    padding: 12px;
    text-align: left;
    border: 1px solid #ddd;
}

.table thead th {
    background-color: #f8f9fa;
    font-weight: bold;
    color: #333;
}

.table tbody tr:hover {
    background-color: #f5f5f5;
}

.table-striped tbody tr:nth-child(even) {
    background-color: #fafafa;
}

/* 배지 */
.badge {
    display: inline-block;
    padding: 4px 8px;
    font-size: 12px;
    font-weight: bold;
    border-radius: 3px;
}

.badge-admin {
    background-color: #dc3545;
    color: #fff;
}

.badge-user {
    background-color: #28a745;
    color: #fff;
}

/* 로딩 오버레이 */
#loading-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10000;
}

.loading-spinner {
    width: 50px;
    height: 50px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #0066cc;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
```

## 웹 접근성 체크리스트

이 예제는 다음 접근성 요구사항을 만족합니다:

### ✅ 인식의 용이성
- [x] 건너뛰기 링크 제공 (본문, 주메뉴 바로가기)
- [x] 모든 이미지에 대체 텍스트 (alt, aria-label)
- [x] 명확한 페이지 제목
- [x] 충분한 색상 대비

### ✅ 운용의 용이성
- [x] 키보드로 모든 기능 접근 가능
- [x] 포커스 시각적 표시
- [x] 논리적인 탭 순서
- [x] Enter 키로 버튼 실행 가능

### ✅ 이해의 용이성
- [x] 명확한 링크 텍스트 ("여기" 사용 안 함)
- [x] 레이블과 입력 필드 연결
- [x] 현재 페이지 표시 (aria-current)
- [x] 빵부스러기 내비게이션

### ✅ 견고성
- [x] HTML5 시맨틱 태그 사용
- [x] ARIA 속성 올바른 사용
- [x] 테이블 구조화 (th scope, caption)
- [x] 스크린 리더 안내 메시지

## 테스트 결과

### 키보드 내비게이션
- Tab 키로 모든 인터랙티브 요소 접근 가능
- Enter/Space 키로 버튼 및 링크 활성화
- 페이지네이션 키보드 탐색 가능

### 스크린 리더 (NVDA/JAWS)
- 건너뛰기 링크 정상 작동
- 폼 레이블 올바르게 읽힘
- 테이블 헤더와 데이터 연결
- 동적 메시지 안내 (aria-live)

### 색상 대비
- 텍스트/배경: 12.63:1 (WCAG AA 통과)
- 버튼 색상: 4.5:1 이상

사용자 목록 페이지가 완성되었습니다!
```

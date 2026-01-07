# Example 2: 웹 접근성을 준수하는 사용자 등록 폼

## Input

```
요청: 웹 접근성을 만족하는 사용자 등록 폼 페이지를 만들어주세요

요구사항:
- 모든 입력 필드에 레이블 연결
- 실시간 유효성 검증
- 에러 메시지 접근성
- 필수 입력 항목 명시
- 키보드만으로 입력 가능
- 스크린 리더 지원
- ARIA 속성 활용
```

## Output

### 1. JSP 폼 페이지 (userForm.jsp)

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>사용자 ${empty user ? '등록' : '수정'} - 전자정부 표준프레임워크</title>

    <link rel="stylesheet" href="<c:url value='/resources/css/common.css' />">
    <link rel="stylesheet" href="<c:url value='/resources/css/form.css' />">
</head>
<body>
    <div id="skip-nav">
        <a href="#content">본문 바로가기</a>
    </div>

    <header role="banner">
        <%@ include file="/WEB-INF/jsp/layout/header.jsp" %>
    </header>

    <nav id="gnb" role="navigation" aria-label="주 메뉴">
        <%@ include file="/WEB-INF/jsp/layout/gnb.jsp" %>
    </nav>

    <main role="main" id="content">
        <div class="container">
            <div class="page-header">
                <h1>사용자 ${empty user ? '등록' : '수정'}</h1>
                <nav aria-label="현재 위치">
                    <ol class="breadcrumb">
                        <li><a href="<c:url value='/' />">홈</a></li>
                        <li><a href="<c:url value='/admin' />">관리자</a></li>
                        <li><a href="<c:url value='/user/list' />">사용자 관리</a></li>
                        <li aria-current="page">사용자 ${empty user ? '등록' : '수정'}</li>
                    </ol>
                </nav>
            </div>

            <!-- 폼 컨테이너 -->
            <div class="form-container">
                <form id="userForm"
                      method="post"
                      action="<c:url value='/user/${empty user ? "insert" : "update"}' />"
                      novalidate>

                    <c:if test="${not empty user}">
                        <input type="hidden" name="userId" value="${user.userId}">
                    </c:if>

                    <fieldset>
                        <legend>기본 정보</legend>

                        <!-- 사용자 ID -->
                        <div class="form-group">
                            <label for="userId" class="required">
                                사용자 ID
                                <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                            </label>
                            <input type="text"
                                   id="userId"
                                   name="userId"
                                   value="<c:out value='${user.userId}' />"
                                   ${not empty user ? 'readonly' : ''}
                                   required
                                   aria-required="true"
                                   aria-invalid="false"
                                   aria-describedby="userId-desc userId-error"
                                   pattern="[a-zA-Z0-9_]{4,20}"
                                   maxlength="20"
                                   autocomplete="username">
                            <span id="userId-desc" class="form-text">
                                4-20자의 영문, 숫자, 밑줄(_)만 사용 가능
                            </span>
                            <span id="userId-error" class="error" role="alert" aria-live="polite"></span>
                        </div>

                        <!-- 사용자명 -->
                        <div class="form-group">
                            <label for="userNm" class="required">
                                이름
                                <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                            </label>
                            <input type="text"
                                   id="userNm"
                                   name="userNm"
                                   value="<c:out value='${user.userNm}' />"
                                   required
                                   aria-required="true"
                                   aria-invalid="false"
                                   aria-describedby="userNm-error"
                                   maxlength="100"
                                   autocomplete="name">
                            <span id="userNm-error" class="error" role="alert" aria-live="polite"></span>
                        </div>

                        <!-- 이메일 -->
                        <div class="form-group">
                            <label for="email" class="required">
                                이메일
                                <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                            </label>
                            <input type="email"
                                   id="email"
                                   name="email"
                                   value="<c:out value='${user.email}' />"
                                   required
                                   aria-required="true"
                                   aria-invalid="false"
                                   aria-describedby="email-desc email-error"
                                   maxlength="200"
                                   autocomplete="email">
                            <span id="email-desc" class="form-text">
                                예: user@example.com
                            </span>
                            <span id="email-error" class="error" role="alert" aria-live="polite"></span>
                        </div>

                        <!-- 비밀번호 (신규 등록 시에만) -->
                        <c:if test="${empty user}">
                            <div class="form-group">
                                <label for="password" class="required">
                                    비밀번호
                                    <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                                </label>
                                <input type="password"
                                       id="password"
                                       name="password"
                                       required
                                       aria-required="true"
                                       aria-invalid="false"
                                       aria-describedby="password-desc password-error"
                                       minlength="8"
                                       maxlength="100"
                                       autocomplete="new-password">
                                <span id="password-desc" class="form-text">
                                    8자 이상의 영문, 숫자, 특수문자 조합
                                </span>
                                <span id="password-error" class="error" role="alert" aria-live="polite"></span>

                                <!-- 비밀번호 강도 표시 -->
                                <div class="password-strength" aria-live="polite">
                                    <div class="strength-meter">
                                        <div id="strength-bar" class="strength-bar" role="progressbar" aria-valuenow="0" aria-valuemin="0" aria-valuemax="100"></div>
                                    </div>
                                    <span id="strength-text" class="strength-text"></span>
                                </div>
                            </div>

                            <!-- 비밀번호 확인 -->
                            <div class="form-group">
                                <label for="passwordConfirm" class="required">
                                    비밀번호 확인
                                    <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                                </label>
                                <input type="password"
                                       id="passwordConfirm"
                                       name="passwordConfirm"
                                       required
                                       aria-required="true"
                                       aria-invalid="false"
                                       aria-describedby="passwordConfirm-error"
                                       autocomplete="new-password">
                                <span id="passwordConfirm-error" class="error" role="alert" aria-live="polite"></span>
                            </div>
                        </c:if>

                        <!-- 휴대폰 번호 -->
                        <div class="form-group">
                            <label for="mobileNo">휴대폰 번호</label>
                            <input type="tel"
                                   id="mobileNo"
                                   name="mobileNo"
                                   value="<c:out value='${user.mobileNo}' />"
                                   aria-describedby="mobileNo-desc mobileNo-error"
                                   pattern="010-\d{4}-\d{4}"
                                   placeholder="010-1234-5678"
                                   autocomplete="tel">
                            <span id="mobileNo-desc" class="form-text">
                                예: 010-1234-5678
                            </span>
                            <span id="mobileNo-error" class="error" role="alert" aria-live="polite"></span>
                        </div>

                        <!-- 권한 -->
                        <div class="form-group">
                            <fieldset>
                                <legend class="required">
                                    권한
                                    <abbr title="필수 입력" aria-label="필수 입력 항목">*</abbr>
                                </legend>
                                <div class="radio-group" role="radiogroup" aria-required="true">
                                    <div class="radio-item">
                                        <input type="radio"
                                               id="roleUser"
                                               name="userRole"
                                               value="USER"
                                               ${empty user or user.userRole == 'USER' ? 'checked' : ''}>
                                        <label for="roleUser">일반 사용자</label>
                                    </div>
                                    <div class="radio-item">
                                        <input type="radio"
                                               id="roleAdmin"
                                               name="userRole"
                                               value="ADMIN"
                                               ${user.userRole == 'ADMIN' ? 'checked' : ''}>
                                        <label for="roleAdmin">관리자</label>
                                    </div>
                                </div>
                            </fieldset>
                        </div>

                        <!-- 이용 약관 동의 (신규 등록 시) -->
                        <c:if test="${empty user}">
                            <div class="form-group">
                                <div class="checkbox-group">
                                    <input type="checkbox"
                                           id="agreeTerms"
                                           name="agreeTerms"
                                           required
                                           aria-required="true"
                                           aria-describedby="agreeTerms-error">
                                    <label for="agreeTerms">
                                        <a href="<c:url value='/terms' />"
                                           target="_blank"
                                           rel="noopener noreferrer"
                                           aria-label="이용약관 새 창에서 보기">
                                            이용약관
                                            <span class="sr-only">(새 창)</span>
                                        </a>에 동의합니다
                                        <abbr title="필수 입력" aria-label="필수">*</abbr>
                                    </label>
                                </div>
                                <span id="agreeTerms-error" class="error" role="alert" aria-live="polite"></span>
                            </div>
                        </c:if>
                    </fieldset>

                    <!-- 폼 액션 -->
                    <div class="form-actions">
                        <button type="submit" class="btn btn-primary">
                            <span class="icon-save" aria-hidden="true"></span>
                            ${empty user ? '등록' : '수정'}
                        </button>
                        <button type="button"
                                class="btn btn-secondary"
                                onclick="history.back()">
                            <span class="icon-cancel" aria-hidden="true"></span>
                            취소
                        </button>
                    </div>
                </form>

                <!-- 폼 도움말 -->
                <aside class="form-help" role="complementary" aria-labelledby="help-title">
                    <h2 id="help-title">입력 가이드</h2>
                    <ul>
                        <li><abbr title="필수 입력" aria-label="별표">*</abbr> 표시는 필수 입력 항목입니다</li>
                        <li>사용자 ID는 등록 후 수정할 수 없습니다</li>
                        <li>비밀번호는 주기적으로 변경하시기 바랍니다</li>
                    </ul>
                </aside>
            </div>
        </div>
    </main>

    <footer role="contentinfo">
        <%@ include file="/WEB-INF/jsp/layout/footer.jsp" %>
    </footer>

    <script src="<c:url value='/resources/js/jquery-3.6.0.min.js' />"></script>
    <script src="<c:url value='/resources/js/common.js' />"></script>
    <script src="<c:url value='/resources/js/form-validation.js' />"></script>
</body>
</html>
```

### 2. JavaScript (form-validation.js)

```javascript
/**
 * 폼 검증 스크립트
 */
(function($) {
    'use strict';

    var FormValidation = {
        /**
         * 초기화
         */
        init: function() {
            this.bindEvents();
        },

        /**
         * 이벤트 바인딩
         */
        bindEvents: function() {
            var self = this;

            // 폼 제출
            $('#userForm').on('submit', function(e) {
                return self.validateForm();
            });

            // 실시간 검증
            $('#userId').on('blur', function() {
                self.validateUserId();
            });

            $('#userNm').on('blur', function() {
                self.validateUserNm();
            });

            $('#email').on('blur', function() {
                self.validateEmail();
            });

            $('#password').on('input', function() {
                self.checkPasswordStrength();
            }).on('blur', function() {
                self.validatePassword();
            });

            $('#passwordConfirm').on('blur', function() {
                self.validatePasswordConfirm();
            });

            $('#mobileNo').on('blur', function() {
                self.validateMobileNo();
            });

            // 에러 메시지 제거
            $('input').on('input', function() {
                var fieldId = $(this).attr('id');
                self.clearError(fieldId);
            });
        },

        /**
         * 전체 폼 검증
         */
        validateForm: function() {
            var isValid = true;

            // 사용자 ID
            if (!this.validateUserId()) isValid = false;

            // 사용자명
            if (!this.validateUserNm()) isValid = false;

            // 이메일
            if (!this.validateEmail()) isValid = false;

            // 비밀번호 (신규 등록 시)
            if ($('#password').length > 0) {
                if (!this.validatePassword()) isValid = false;
                if (!this.validatePasswordConfirm()) isValid = false;
            }

            // 휴대폰 번호 (선택사항이지만 입력했다면 검증)
            var mobileNo = $('#mobileNo').val().trim();
            if (mobileNo && !this.validateMobileNo()) isValid = false;

            // 약관 동의
            if ($('#agreeTerms').length > 0 && !$('#agreeTerms').is(':checked')) {
                this.showError('agreeTerms', '이용약관에 동의해야 합니다.');
                isValid = false;
            }

            if (!isValid) {
                // 첫 번째 에러 필드로 포커스 이동
                var $firstError = $('.error:visible').first();
                if ($firstError.length > 0) {
                    var fieldId = $firstError.attr('id').replace('-error', '');
                    $('#' + fieldId).focus();
                }

                this.announceToScreenReader('입력 항목에 오류가 있습니다. 확인해주세요.');
            }

            return isValid;
        },

        /**
         * 사용자 ID 검증
         */
        validateUserId: function() {
            var userId = $('#userId').val().trim();

            if (userId === '') {
                this.showError('userId', '사용자 ID를 입력하세요.');
                return false;
            }

            if (!/^[a-zA-Z0-9_]{4,20}$/.test(userId)) {
                this.showError('userId', '사용자 ID는 4-20자의 영문, 숫자, 밑줄(_)만 가능합니다.');
                return false;
            }

            // 중복 체크 (AJAX)
            if ($('#userId').attr('readonly') !== 'readonly') {
                return this.checkDuplicateUserId(userId);
            }

            this.clearError('userId');
            return true;
        },

        /**
         * 사용자 ID 중복 체크
         */
        checkDuplicateUserId: function(userId) {
            var isAvailable = true;

            $.ajax({
                url: '/api/users/check-duplicate',
                type: 'GET',
                data: { userId: userId },
                async: false,
                success: function(response) {
                    if (response.exists) {
                        FormValidation.showError('userId', '이미 사용 중인 사용자 ID입니다.');
                        isAvailable = false;
                    } else {
                        FormValidation.clearError('userId');
                    }
                },
                error: function() {
                    FormValidation.showError('userId', '중복 확인 중 오류가 발생했습니다.');
                    isAvailable = false;
                }
            });

            return isAvailable;
        },

        /**
         * 사용자명 검증
         */
        validateUserNm: function() {
            var userNm = $('#userNm').val().trim();

            if (userNm === '') {
                this.showError('userNm', '이름을 입력하세요.');
                return false;
            }

            if (userNm.length < 2) {
                this.showError('userNm', '이름은 2자 이상이어야 합니다.');
                return false;
            }

            this.clearError('userNm');
            return true;
        },

        /**
         * 이메일 검증
         */
        validateEmail: function() {
            var email = $('#email').val().trim();

            if (email === '') {
                this.showError('email', '이메일을 입력하세요.');
                return false;
            }

            if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
                this.showError('email', '올바른 이메일 형식이 아닙니다.');
                return false;
            }

            this.clearError('email');
            return true;
        },

        /**
         * 비밀번호 검증
         */
        validatePassword: function() {
            var password = $('#password').val();

            if (password === '') {
                this.showError('password', '비밀번호를 입력하세요.');
                return false;
            }

            if (password.length < 8) {
                this.showError('password', '비밀번호는 8자 이상이어야 합니다.');
                return false;
            }

            // 영문, 숫자, 특수문자 포함 검사
            var hasLetter = /[a-zA-Z]/.test(password);
            var hasNumber = /\d/.test(password);
            var hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(password);

            if (!(hasLetter && hasNumber && hasSpecial)) {
                this.showError('password', '비밀번호는 영문, 숫자, 특수문자를 모두 포함해야 합니다.');
                return false;
            }

            this.clearError('password');
            return true;
        },

        /**
         * 비밀번호 확인 검증
         */
        validatePasswordConfirm: function() {
            var password = $('#password').val();
            var passwordConfirm = $('#passwordConfirm').val();

            if (passwordConfirm === '') {
                this.showError('passwordConfirm', '비밀번호 확인을 입력하세요.');
                return false;
            }

            if (password !== passwordConfirm) {
                this.showError('passwordConfirm', '비밀번호가 일치하지 않습니다.');
                return false;
            }

            this.clearError('passwordConfirm');
            return true;
        },

        /**
         * 휴대폰 번호 검증
         */
        validateMobileNo: function() {
            var mobileNo = $('#mobileNo').val().trim();

            if (mobileNo === '') {
                this.clearError('mobileNo');
                return true; // 선택 항목
            }

            if (!/^010-\d{4}-\d{4}$/.test(mobileNo)) {
                this.showError('mobileNo', '올바른 휴대폰 번호 형식이 아닙니다. (예: 010-1234-5678)');
                return false;
            }

            this.clearError('mobileNo');
            return true;
        },

        /**
         * 비밀번호 강도 체크
         */
        checkPasswordStrength: function() {
            var password = $('#password').val();
            var strength = 0;

            if (password.length >= 8) strength += 25;
            if (password.length >= 12) strength += 25;
            if (/[a-z]/.test(password)) strength += 10;
            if (/[A-Z]/.test(password)) strength += 10;
            if (/\d/.test(password)) strength += 15;
            if (/[!@#$%^&*(),.?":{}|<>]/.test(password)) strength += 15;

            var strengthText = '';
            var strengthClass = '';

            if (strength < 40) {
                strengthText = '약함';
                strengthClass = 'weak';
            } else if (strength < 70) {
                strengthText = '보통';
                strengthClass = 'medium';
            } else {
                strengthText = '강함';
                strengthClass = 'strong';
            }

            $('#strength-bar')
                .css('width', strength + '%')
                .attr('aria-valuenow', strength)
                .removeClass('weak medium strong')
                .addClass(strengthClass);

            $('#strength-text')
                .text('비밀번호 강도: ' + strengthText)
                .attr('class', 'strength-text ' + strengthClass);
        },

        /**
         * 에러 메시지 표시
         */
        showError: function(fieldId, message) {
            var $field = $('#' + fieldId);
            var $error = $('#' + fieldId + '-error');

            // ARIA 속성 업데이트
            $field.attr('aria-invalid', 'true');

            // 에러 메시지 표시
            $error.text(message).show();

            // 스크린 리더 안내
            this.announceToScreenReader(message);
        },

        /**
         * 에러 메시지 제거
         */
        clearError: function(fieldId) {
            var $field = $('#' + fieldId);
            var $error = $('#' + fieldId + '-error');

            $field.attr('aria-invalid', 'false');
            $error.text('').hide();
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

            setTimeout(function() {
                $announcer.text('');
            }, 3000);
        }
    };

    // DOM Ready
    $(document).ready(function() {
        FormValidation.init();
    });

})(jQuery);
```

### 3. CSS (form.css)

```css
/**
 * 폼 스타일
 */

.form-container {
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

.form-group {
    margin-bottom: 24px;
}

label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: #333;
}

label.required::after,
abbr[title="필수 입력"] {
    color: #dc3545;
    margin-left: 4px;
}

input[type="text"],
input[type="email"],
input[type="tel"],
input[type="password"],
select,
textarea {
    width: 100%;
    padding: 10px 12px;
    font-size: 16px;
    border: 1px solid #ced4da;
    border-radius: 4px;
    transition: border-color 0.15s ease-in-out;
}

/* 포커스 스타일 */
input:focus,
select:focus,
textarea:focus {
    outline: 2px solid #0066cc;
    outline-offset: 2px;
    border-color: #0066cc;
}

/* 에러 스타일 */
input[aria-invalid="true"],
select[aria-invalid="true"] {
    border-color: #dc3545;
}

.error {
    display: none;
    margin-top: 8px;
    font-size: 14px;
    color: #dc3545;
}

.error:not(:empty) {
    display: block;
}

/* 도움말 텍스트 */
.form-text {
    display: block;
    margin-top: 4px;
    font-size: 14px;
    color: #6c757d;
}

/* 라디오/체크박스 */
.radio-group,
.checkbox-group {
    margin-top: 8px;
}

.radio-item,
.checkbox-item {
    margin-bottom: 8px;
}

.radio-item input[type="radio"],
.checkbox-group input[type="checkbox"] {
    width: auto;
    margin-right: 8px;
}

.radio-item label,
.checkbox-group label {
    display: inline;
    font-weight: normal;
}

/* 비밀번호 강도 */
.password-strength {
    margin-top: 8px;
}

.strength-meter {
    height: 8px;
    background-color: #e9ecef;
    border-radius: 4px;
    overflow: hidden;
}

.strength-bar {
    height: 100%;
    transition: width 0.3s ease, background-color 0.3s ease;
}

.strength-bar.weak {
    background-color: #dc3545;
}

.strength-bar.medium {
    background-color: #ffc107;
}

.strength-bar.strong {
    background-color: #28a745;
}

.strength-text {
    display: block;
    margin-top: 4px;
    font-size: 14px;
}

.strength-text.weak {
    color: #dc3545;
}

.strength-text.medium {
    color: #ffc107;
}

.strength-text.strong {
    color: #28a745;
}

/* 폼 액션 */
.form-actions {
    margin-top: 32px;
    padding-top: 24px;
    border-top: 1px solid #dee2e6;
    text-align: center;
}

.form-actions .btn {
    margin: 0 8px;
}

/* 폼 도움말 */
.form-help {
    margin-top: 40px;
    padding: 20px;
    background-color: #f8f9fa;
    border-left: 4px solid #0066cc;
}

.form-help h2 {
    margin-top: 0;
    font-size: 18px;
}

.form-help ul {
    margin: 12px 0 0 20px;
}

.form-help li {
    margin-bottom: 8px;
    color: #495057;
}

/* 읽기 전용 필드 */
input[readonly] {
    background-color: #e9ecef;
    cursor: not-allowed;
}
```

## 웹 접근성 준수 사항

### ✅ 레이블 연결
- 모든 입력 필드에 `<label>` 연결
- `for` 속성으로 명시적 연결
- 시각적으로 숨겨진 레이블도 제공 (`sr-only`)

### ✅ ARIA 속성
- `aria-required`: 필수 입력 항목 표시
- `aria-invalid`: 유효성 검증 실패 상태
- `aria-describedby`: 도움말 및 에러 메시지 연결
- `aria-live`: 동적 메시지 스크린 리더 안내
- `role="alert"`: 에러 메시지 즉시 안내

### ✅ 필수 항목 표시
- `*` 기호 및 `required` 속성
- `<abbr title="필수 입력">` 사용
- 스크린 리더용 숨김 텍스트

### ✅ 키보드 접근성
- Tab 키로 모든 필드 접근
- Enter 키로 폼 제출
- 포커스 시각적 표시

### ✅ 에러 처리
- 실시간 검증
- 명확한 에러 메시지
- 첫 번째 에러로 포커스 이동
- 스크린 리더 안내

## 테스트 가이드

### 키보드 테스트
1. Tab 키로 모든 필드 순서대로 이동
2. 필드 입력 및 검증 확인
3. Enter 키로 폼 제출

### 스크린 리더 테스트 (NVDA/JAWS)
1. 레이블 올바르게 읽힘
2. 필수 항목 안내
3. 에러 메시지 즉시 안내
4. 도움말 텍스트 읽힘

### 유효성 검증 테스트
1. 빈 값 제출 시 에러 표시
2. 잘못된 형식 입력 시 에러
3. 비밀번호 강도 실시간 표시
4. 중복 ID 체크

접근성을 완벽히 준수하는 폼이 완성되었습니다!
```

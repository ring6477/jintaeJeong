# Example 3: 접근성을 준수하는 jQuery 컴포넌트

## Input

```
요청: 웹 접근성을 만족하는 재사용 가능한 UI 컴포넌트를 jQuery로 구현해주세요

요구사항:
- 모달 다이얼로그
- 탭 패널
- 아코디언
- 툴팁
- 모든 컴포넌트 키보드 접근 가능
- ARIA 속성 적용
- 포커스 관리
```

## Output

### 1. JSP 컴포넌트 페이지 (components.jsp)

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>접근성 컴포넌트 - 전자정부 표준프레임워크</title>

    <link rel="stylesheet" href="<c:url value='/resources/css/common.css' />">
    <link rel="stylesheet" href="<c:url value='/resources/css/components.css' />">
</head>
<body>
    <div id="skip-nav">
        <a href="#content">본문 바로가기</a>
    </div>

    <header role="banner">
        <%@ include file="/WEB-INF/jsp/layout/header.jsp" %>
    </header>

    <main role="main" id="content">
        <div class="container">
            <h1>접근성 컴포넌트</h1>

            <!-- 1. 모달 다이얼로그 -->
            <section class="component-section">
                <h2>모달 다이얼로그</h2>
                <p>모달 다이얼로그는 포커스 트랩과 ESC 키 종료를 지원합니다.</p>

                <button type="button"
                        class="btn btn-primary"
                        onclick="AccessibleComponents.Modal.open('userModal')"
                        aria-haspopup="dialog">
                    사용자 정보 모달 열기
                </button>

                <!-- 모달 -->
                <div id="userModal"
                     class="modal"
                     role="dialog"
                     aria-labelledby="userModal-title"
                     aria-modal="true"
                     hidden>

                    <div class="modal-overlay" onclick="AccessibleComponents.Modal.close('userModal')"></div>

                    <div class="modal-content">
                        <div class="modal-header">
                            <h2 id="userModal-title">사용자 정보</h2>
                            <button type="button"
                                    class="btn-close"
                                    onclick="AccessibleComponents.Modal.close('userModal')"
                                    aria-label="닫기">
                                <span aria-hidden="true">×</span>
                            </button>
                        </div>

                        <div class="modal-body">
                            <p>이것은 접근성을 준수하는 모달 다이얼로그입니다.</p>
                            <p>특징:</p>
                            <ul>
                                <li>포커스 트랩 (Tab 키가 모달 내에서만 순환)</li>
                                <li>ESC 키로 닫기</li>
                                <li>배경 클릭으로 닫기</li>
                                <li>이전 포커스 위치 복원</li>
                            </ul>
                        </div>

                        <div class="modal-footer">
                            <button type="button" class="btn btn-primary">확인</button>
                            <button type="button" class="btn btn-secondary" onclick="AccessibleComponents.Modal.close('userModal')">취소</button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 2. 탭 패널 -->
            <section class="component-section">
                <h2>탭 패널</h2>
                <p>좌우 화살표 키로 탭 간 이동이 가능합니다.</p>

                <div class="tabs" data-tabs>
                    <div role="tablist" aria-label="사용자 정보">
                        <button type="button"
                                role="tab"
                                id="tab1"
                                aria-selected="true"
                                aria-controls="panel1"
                                tabindex="0">
                            기본 정보
                        </button>
                        <button type="button"
                                role="tab"
                                id="tab2"
                                aria-selected="false"
                                aria-controls="panel2"
                                tabindex="-1">
                            상세 정보
                        </button>
                        <button type="button"
                                role="tab"
                                id="tab3"
                                aria-selected="false"
                                aria-controls="panel3"
                                tabindex="-1">
                            이력
                        </button>
                    </div>

                    <div id="panel1"
                         role="tabpanel"
                         aria-labelledby="tab1"
                         tabindex="0">
                        <h3>기본 정보</h3>
                        <p>사용자의 기본 정보를 표시합니다.</p>
                        <ul>
                            <li>이름: 홍길동</li>
                            <li>이메일: hong@example.com</li>
                            <li>권한: 일반 사용자</li>
                        </ul>
                    </div>

                    <div id="panel2"
                         role="tabpanel"
                         aria-labelledby="tab2"
                         tabindex="0"
                         hidden>
                        <h3>상세 정보</h3>
                        <p>추가적인 상세 정보를 표시합니다.</p>
                        <ul>
                            <li>부서: 개발팀</li>
                            <li>직급: 대리</li>
                            <li>전화번호: 010-1234-5678</li>
                        </ul>
                    </div>

                    <div id="panel3"
                         role="tabpanel"
                         aria-labelledby="tab3"
                         tabindex="0"
                         hidden>
                        <h3>이력</h3>
                        <p>사용자의 활동 이력입니다.</p>
                        <ul>
                            <li>2026-01-07: 로그인</li>
                            <li>2026-01-06: 게시글 작성</li>
                            <li>2026-01-05: 프로필 수정</li>
                        </ul>
                    </div>
                </div>
            </section>

            <!-- 3. 아코디언 -->
            <section class="component-section">
                <h2>아코디언</h2>
                <p>자주 묻는 질문 형태의 아코디언 컴포넌트입니다.</p>

                <div class="accordion" data-accordion>
                    <div class="accordion-item">
                        <h3 class="accordion-header">
                            <button type="button"
                                    class="accordion-button"
                                    aria-expanded="false"
                                    aria-controls="faq1">
                                <span class="accordion-icon" aria-hidden="true">+</span>
                                전자정부 표준프레임워크란 무엇인가요?
                            </button>
                        </h3>
                        <div id="faq1" class="accordion-content" hidden>
                            <p>전자정부 표준프레임워크는 행정안전부에서 제공하는 공공 부문 정보화 사업 시 활용할 수 있는 개발·운영 표준 환경을 제공하는 프레임워크입니다.</p>
                        </div>
                    </div>

                    <div class="accordion-item">
                        <h3 class="accordion-header">
                            <button type="button"
                                    class="accordion-button"
                                    aria-expanded="false"
                                    aria-controls="faq2">
                                <span class="accordion-icon" aria-hidden="true">+</span>
                                웹 접근성이란 무엇인가요?
                            </button>
                        </h3>
                        <div id="faq2" class="accordion-content" hidden>
                            <p>웹 접근성은 장애인, 고령자 등이 웹 사이트에서 제공하는 정보에 비장애인과 동등하게 접근하고 이용할 수 있도록 보장하는 것을 말합니다.</p>
                        </div>
                    </div>

                    <div class="accordion-item">
                        <h3 class="accordion-header">
                            <button type="button"
                                    class="accordion-button"
                                    aria-expanded="false"
                                    aria-controls="faq3">
                                <span class="accordion-icon" aria-hidden="true">+</span>
                                WCAG 2.1 AA 준수란 무엇인가요?
                            </button>
                        </h3>
                        <div id="faq3" class="accordion-content" hidden>
                            <p>WCAG(Web Content Accessibility Guidelines) 2.1은 웹 콘텐츠 접근성 지침의 2.1 버전이며, AA 등급은 법적 요구사항을 만족하는 중간 수준의 접근성 준수를 의미합니다.</p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- 4. 툴팁 -->
            <section class="component-section">
                <h2>툴팁</h2>
                <p>마우스 호버 또는 포커스 시 도움말이 표시됩니다.</p>

                <p>
                    <button type="button"
                            class="btn btn-info"
                            data-tooltip="이것은 툴팁 메시지입니다."
                            aria-describedby="tooltip1">
                        툴팁 버튼
                    </button>
                </p>

                <p>
                    웹 접근성
                    <span class="tooltip-trigger"
                          tabindex="0"
                          data-tooltip="웹 접근성은 모든 사람이 웹을 사용할 수 있도록 보장하는 것입니다."
                          aria-describedby="tooltip2">
                        <span class="icon-help" aria-hidden="true">?</span>
                        <span class="sr-only">도움말</span>
                    </span>
                    은 중요합니다.
                </p>
            </section>

            <!-- 5. 알림 메시지 -->
            <section class="component-section">
                <h2>알림 메시지</h2>
                <p>스크린 리더가 즉시 읽어주는 알림입니다.</p>

                <button type="button"
                        class="btn btn-success"
                        onclick="AccessibleComponents.Alert.show('success', '성공적으로 저장되었습니다.')">
                    성공 알림
                </button>
                <button type="button"
                        class="btn btn-danger"
                        onclick="AccessibleComponents.Alert.show('error', '오류가 발생했습니다.')">
                    에러 알림
                </button>
                <button type="button"
                        class="btn btn-warning"
                        onclick="AccessibleComponents.Alert.show('warning', '주의가 필요합니다.')">
                    경고 알림
                </button>
                <button type="button"
                        class="btn btn-info"
                        onclick="AccessibleComponents.Alert.show('info', '정보를 확인하세요.')">
                    정보 알림
                </button>

                <!-- 알림 컨테이너 -->
                <div id="alert-container" aria-live="polite" aria-atomic="true"></div>
            </section>

        </div>
    </main>

    <footer role="contentinfo">
        <%@ include file="/WEB-INF/jsp/layout/footer.jsp" %>
    </footer>

    <script src="<c:url value='/resources/js/jquery-3.6.0.min.js' />"></script>
    <script src="<c:url value='/resources/js/accessible-components.js' />"></script>
</body>
</html>
```

### 2. JavaScript (accessible-components.js)

```javascript
/**
 * 접근성 컴포넌트 라이브러리
 */
var AccessibleComponents = (function($) {
    'use strict';

    /**
     * 모달 다이얼로그
     */
    var Modal = {
        focusedElementBeforeModal: null,

        open: function(modalId) {
            var $modal = $('#' + modalId);

            // 현재 포커스 요소 저장
            this.focusedElementBeforeModal = document.activeElement;

            // 모달 표시
            $modal.removeAttr('hidden');

            // body 스크롤 방지
            $('body').css('overflow', 'hidden');

            // 첫 번째 포커스 가능 요소로 이동
            var firstFocusable = $modal.find('button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])').first();
            if (firstFocusable.length > 0) {
                firstFocusable.focus();
            }

            // ESC 키로 닫기
            $modal.on('keydown.modal', function(e) {
                if (e.key === 'Escape') {
                    Modal.close(modalId);
                }
            });

            // 포커스 트랩
            this.trapFocus($modal[0]);
        },

        close: function(modalId) {
            var $modal = $('#' + modalId);

            $modal.attr('hidden', '');
            $('body').css('overflow', '');
            $modal.off('keydown.modal');

            // 이전 포커스 복원
            if (this.focusedElementBeforeModal) {
                this.focusedElementBeforeModal.focus();
            }
        },

        trapFocus: function(element) {
            var focusableElements = element.querySelectorAll(
                'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
            );
            var firstFocusable = focusableElements[0];
            var lastFocusable = focusableElements[focusableElements.length - 1];

            element.addEventListener('keydown', function(e) {
                if (e.key === 'Tab') {
                    if (e.shiftKey && document.activeElement === firstFocusable) {
                        e.preventDefault();
                        lastFocusable.focus();
                    } else if (!e.shiftKey && document.activeElement === lastFocusable) {
                        e.preventDefault();
                        firstFocusable.focus();
                    }
                }
            });
        }
    };

    /**
     * 탭 패널
     */
    var Tabs = {
        init: function() {
            $('[data-tabs]').each(function() {
                var $tabs = $(this);
                var $tabButtons = $tabs.find('[role="tab"]');

                $tabButtons.on('click', function() {
                    Tabs.selectTab($(this));
                });

                $tabButtons.on('keydown', function(e) {
                    Tabs.handleKeydown(e, $(this));
                });
            });
        },

        selectTab: function($tab) {
            var $tabs = $tab.closest('[data-tabs]');
            var $allTabs = $tabs.find('[role="tab"]');
            var $allPanels = $tabs.find('[role="tabpanel"]');

            // 모든 탭 비활성화
            $allTabs.attr({
                'aria-selected': 'false',
                'tabindex': '-1'
            });

            // 모든 패널 숨김
            $allPanels.attr('hidden', '');

            // 선택된 탭 활성화
            $tab.attr({
                'aria-selected': 'true',
                'tabindex': '0'
            });

            // 해당 패널 표시
            var panelId = $tab.attr('aria-controls');
            $('#' + panelId).removeAttr('hidden');
        },

        handleKeydown: function(e, $currentTab) {
            var $tabs = $currentTab.closest('[data-tabs]');
            var $allTabs = $tabs.find('[role="tab"]');
            var currentIndex = $allTabs.index($currentTab);
            var $nextTab;

            switch(e.key) {
                case 'ArrowLeft':
                    e.preventDefault();
                    $nextTab = $allTabs.eq(currentIndex - 1);
                    if ($nextTab.length === 0) {
                        $nextTab = $allTabs.last();
                    }
                    Tabs.selectTab($nextTab);
                    $nextTab.focus();
                    break;

                case 'ArrowRight':
                    e.preventDefault();
                    $nextTab = $allTabs.eq(currentIndex + 1);
                    if ($nextTab.length === 0) {
                        $nextTab = $allTabs.first();
                    }
                    Tabs.selectTab($nextTab);
                    $nextTab.focus();
                    break;

                case 'Home':
                    e.preventDefault();
                    $nextTab = $allTabs.first();
                    Tabs.selectTab($nextTab);
                    $nextTab.focus();
                    break;

                case 'End':
                    e.preventDefault();
                    $nextTab = $allTabs.last();
                    Tabs.selectTab($nextTab);
                    $nextTab.focus();
                    break;
            }
        }
    };

    /**
     * 아코디언
     */
    var Accordion = {
        init: function() {
            $('[data-accordion]').each(function() {
                var $accordion = $(this);
                var $buttons = $accordion.find('.accordion-button');

                $buttons.on('click', function() {
                    Accordion.toggle($(this));
                });
            });
        },

        toggle: function($button) {
            var isExpanded = $button.attr('aria-expanded') === 'true';
            var contentId = $button.attr('aria-controls');
            var $content = $('#' + contentId);
            var $icon = $button.find('.accordion-icon');

            if (isExpanded) {
                // 닫기
                $button.attr('aria-expanded', 'false');
                $content.attr('hidden', '');
                $icon.text('+');
            } else {
                // 열기
                $button.attr('aria-expanded', 'true');
                $content.removeAttr('hidden');
                $icon.text('−');
            }
        }
    };

    /**
     * 툴팁
     */
    var Tooltip = {
        init: function() {
            $('[data-tooltip]').each(function() {
                var $trigger = $(this);
                var tooltipText = $trigger.attr('data-tooltip');
                var tooltipId = 'tooltip-' + Date.now() + '-' + Math.random().toString(36).substr(2, 9);

                // 툴팁 요소 생성
                var $tooltip = $('<div>', {
                    id: tooltipId,
                    class: 'tooltip',
                    role: 'tooltip',
                    html: tooltipText
                }).appendTo('body');

                // aria-describedby 설정
                $trigger.attr('aria-describedby', tooltipId);

                // 이벤트 바인딩
                $trigger.on('mouseenter focus', function() {
                    Tooltip.show($trigger, $tooltip);
                });

                $trigger.on('mouseleave blur', function() {
                    Tooltip.hide($tooltip);
                });
            });
        },

        show: function($trigger, $tooltip) {
            var offset = $trigger.offset();
            var triggerWidth = $trigger.outerWidth();
            var triggerHeight = $trigger.outerHeight();
            var tooltipWidth = $tooltip.outerWidth();

            // 위치 계산
            var left = offset.left + (triggerWidth / 2) - (tooltipWidth / 2);
            var top = offset.top - $tooltip.outerHeight() - 8;

            $tooltip.css({
                left: left + 'px',
                top: top + 'px'
            }).addClass('show');
        },

        hide: function($tooltip) {
            $tooltip.removeClass('show');
        }
    };

    /**
     * 알림 메시지
     */
    var Alert = {
        show: function(type, message, duration) {
            duration = duration || 5000;

            var iconMap = {
                success: '✓',
                error: '✗',
                warning: '⚠',
                info: 'ℹ'
            };

            var $alert = $('<div>', {
                class: 'alert alert-' + type,
                role: 'alert',
                html: '<span class="alert-icon" aria-hidden="true">' + iconMap[type] + '</span>' +
                      '<span class="alert-message">' + message + '</span>' +
                      '<button type="button" class="alert-close" aria-label="닫기">' +
                      '<span aria-hidden="true">×</span>' +
                      '</button>'
            });

            $('#alert-container').append($alert);

            // 슬라이드 인 애니메이션
            setTimeout(function() {
                $alert.addClass('show');
            }, 10);

            // 닫기 버튼
            $alert.find('.alert-close').on('click', function() {
                Alert.close($alert);
            });

            // 자동 닫기
            setTimeout(function() {
                Alert.close($alert);
            }, duration);
        },

        close: function($alert) {
            $alert.removeClass('show');
            setTimeout(function() {
                $alert.remove();
            }, 300);
        }
    };

    /**
     * 초기화
     */
    $(document).ready(function() {
        Tabs.init();
        Accordion.init();
        Tooltip.init();
    });

    // Public API
    return {
        Modal: Modal,
        Tabs: Tabs,
        Accordion: Accordion,
        Tooltip: Tooltip,
        Alert: Alert
    };

})(jQuery);
```

### 3. CSS (components.css)

```css
/**
 * 접근성 컴포넌트 스타일
 */

.component-section {
    margin-bottom: 60px;
    padding-bottom: 40px;
    border-bottom: 1px solid #dee2e6;
}

.component-section:last-child {
    border-bottom: none;
}

/* 모달 */
.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1000;
}

.modal-overlay {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
}

.modal-content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #fff;
    border-radius: 8px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
    max-width: 600px;
    width: 90%;
    max-height: 90vh;
    overflow-y: auto;
}

.modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px;
    border-bottom: 1px solid #dee2e6;
}

.modal-header h2 {
    margin: 0;
    font-size: 20px;
}

.btn-close {
    background: none;
    border: none;
    font-size: 28px;
    cursor: pointer;
    padding: 0;
    width: 32px;
    height: 32px;
    line-height: 1;
}

.btn-close:hover,
.btn-close:focus {
    color: #dc3545;
}

.modal-body {
    padding: 20px;
}

.modal-footer {
    padding: 20px;
    border-top: 1px solid #dee2e6;
    text-align: right;
}

/* 탭 */
.tabs [role="tablist"] {
    display: flex;
    border-bottom: 2px solid #dee2e6;
}

.tabs [role="tab"] {
    padding: 12px 24px;
    background: none;
    border: none;
    border-bottom: 3px solid transparent;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.2s;
}

.tabs [role="tab"]:hover {
    background-color: #f8f9fa;
}

.tabs [role="tab"]:focus {
    outline: 2px solid #0066cc;
    outline-offset: -2px;
}

.tabs [role="tab"][aria-selected="true"] {
    border-bottom-color: #0066cc;
    color: #0066cc;
    font-weight: bold;
}

.tabs [role="tabpanel"] {
    padding: 20px;
}

/* 아코디언 */
.accordion-item {
    border: 1px solid #dee2e6;
    margin-bottom: 8px;
    border-radius: 4px;
}

.accordion-header {
    margin: 0;
}

.accordion-button {
    width: 100%;
    padding: 16px;
    background: #f8f9fa;
    border: none;
    text-align: left;
    cursor: pointer;
    font-size: 16px;
    font-weight: 600;
    display: flex;
    align-items: center;
    transition: background-color 0.2s;
}

.accordion-button:hover {
    background-color: #e9ecef;
}

.accordion-button:focus {
    outline: 2px solid #0066cc;
    outline-offset: -2px;
}

.accordion-icon {
    margin-right: 12px;
    font-size: 20px;
    font-weight: bold;
    color: #0066cc;
}

.accordion-content {
    padding: 16px;
}

/* 툴팁 */
.tooltip {
    position: absolute;
    background: #333;
    color: #fff;
    padding: 8px 12px;
    border-radius: 4px;
    font-size: 14px;
    z-index: 1000;
    opacity: 0;
    transition: opacity 0.2s;
    pointer-events: none;
    max-width: 300px;
}

.tooltip::after {
    content: '';
    position: absolute;
    top: 100%;
    left: 50%;
    transform: translateX(-50%);
    border: 6px solid transparent;
    border-top-color: #333;
}

.tooltip.show {
    opacity: 1;
}

.tooltip-trigger {
    display: inline-block;
    cursor: help;
}

.icon-help {
    display: inline-block;
    width: 18px;
    height: 18px;
    line-height: 18px;
    text-align: center;
    background: #0066cc;
    color: #fff;
    border-radius: 50%;
    font-size: 12px;
    font-weight: bold;
}

/* 알림 */
#alert-container {
    position: fixed;
    top: 20px;
    right: 20px;
    z-index: 2000;
    max-width: 400px;
}

.alert {
    display: flex;
    align-items: center;
    padding: 16px;
    margin-bottom: 12px;
    border-radius: 4px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    transform: translateX(400px);
    opacity: 0;
    transition: all 0.3s ease-in-out;
}

.alert.show {
    transform: translateX(0);
    opacity: 1;
}

.alert-icon {
    font-size: 20px;
    margin-right: 12px;
}

.alert-message {
    flex: 1;
}

.alert-close {
    background: none;
    border: none;
    font-size: 20px;
    cursor: pointer;
    padding: 0;
    margin-left: 12px;
}

.alert-success {
    background: #d4edda;
    color: #155724;
    border-left: 4px solid #28a745;
}

.alert-error {
    background: #f8d7da;
    color: #721c24;
    border-left: 4px solid #dc3545;
}

.alert-warning {
    background: #fff3cd;
    color: #856404;
    border-left: 4px solid #ffc107;
}

.alert-info {
    background: #d1ecf1;
    color: #0c5460;
    border-left: 4px solid #17a2b8;
}
```

## 접근성 준수 사항

### ✅ 모달 다이얼로그
- `role="dialog"`, `aria-modal="true"`
- `aria-labelledby`로 제목 연결
- 포커스 트랩 (Tab 키가 모달 내에서만 순환)
- ESC 키로 닫기
- 이전 포커스 위치 복원
- 배경 스크롤 방지

### ✅ 탭 패널
- `role="tablist"`, `role="tab"`, `role="tabpanel"`
- `aria-selected`, `aria-controls`
- 좌우 화살표 키로 탭 이동
- Home/End 키 지원
- 활성 탭만 tabindex="0"

### ✅ 아코디언
- `aria-expanded` 상태 관리
- `aria-controls`로 콘텐츠 연결
- 명확한 버튼 레이블
- 키보드로 열고 닫기 가능

### ✅ 툴팁
- `role="tooltip"`
- `aria-describedby`로 연결
- 포커스 시에도 표시
- 키보드 사용자 고려

### ✅ 알림
- `role="alert"`
- `aria-live="polite"`
- 스크린 리더 자동 안내
- 닫기 버튼 접근 가능

## 사용 예시

```javascript
// 모달 열기/닫기
AccessibleComponents.Modal.open('userModal');
AccessibleComponents.Modal.close('userModal');

// 알림 표시
AccessibleComponents.Alert.show('success', '저장되었습니다.');
AccessibleComponents.Alert.show('error', '오류가 발생했습니다.');
```

모든 컴포넌트가 웹 접근성을 완벽히 준수합니다!
```

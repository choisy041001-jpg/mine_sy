# MY인사 모바일 — 결정 사항

프로젝트 진행 중 내려진 주요 결정과 그 배경을 기록한다.
AI가 다음 세션에서 맥락 없이 작업하더라도 이 파일을 통해 의도를 파악할 수 있어야 한다.

---

## 아키텍처

### 단일 HTML 파일 구조 유지
- **결정**: 빌드 도구 없이 `index_m.html` 단일 파일로 운영
- **이유**: 프로토타입 단계. 빠른 확인과 공유가 목적이며 빌드 환경 구성 비용이 불필요

### CSS 외부 파일 분리
- **결정**: 스타일을 `css/myinsa-mobile.css`로 분리
- **이유**: 인라인/내부 스타일이 혼재하여 유지보수가 어려웠음. 클래스화하여 재사용성 확보

### display:none 인라인 스타일 유지
- **결정**: JS로 제어하는 `style="display:none"` 속성은 인라인으로 유지
- **이유**: JS가 `element.style.display`를 직접 조작하는 구조이므로 CSS 클래스로 이전하면 동작이 깨질 수 있음. 의도적으로 남겨둔 것임

### 오버레이 ID 셀렉터 사용
- **결정**: `#boardFilterSheet`, `#certModal`, `#postActionSheet`, `#approvalLinePopup`, `#datetimePickerOverlay` 등 오버레이 요소의 고유값(position, background, z-index)은 CSS에서 ID 셀렉터로 관리
- **이유**: 각 오버레이마다 z-index와 배경 투명도가 달라 공통 클래스로 흡수하기 어려움. `display:none`만 인라인에 두고 나머지(`position:absolute;inset:0;align-items;justify-content` 등)는 CSS ID 셀렉터로 분리. JS가 `el.style.display = 'flex'`로 열면 CSS 레이아웃 속성이 자동 적용됨

---

## CSS 아키텍처

> 🟦 **기획 작업자 규칙 — 인라인·CSS 둘 다 금지**
> 화면을 수정·생성하는 기획 작업자는 다음을 지킨다.
> 1. `myinsa-mobile.css` 등 어떤 CSS 파일도 수정하지 않는다.
> 2. 인라인 `style=""`을 **새로 추가하지 않는다 — JS 템플릿 리터럴 안에서도 마찬가지다.** (기존 인라인은 그대로 둔다.)
> 3. 화면은 **기존 클래스·컴포넌트만 조합**해서 만든다.
> 4. **STOP 조건** — 기존 클래스만으로 시각·동작이 완성되지 않으면(새 스타일이 필요하거나, 모달 열기·화면 전환 등 JS 결선이 필요하면) **작업을 멈추고 퍼블리싱 파트에 필요한 클래스·결선을 요청**한다. 임의로 클래스를 지어내거나 인라인으로 우회하지 않는다.
> ※ `rules_DECISIONS.md`의 "인라인 허용" 규칙은 **퍼블리싱 전용**이다. 기획 작업자에게는 위 1·2가 우선한다.

### `.panel-header` 그라디언트 스코핑
- **결정**: `.panel-header` 그라디언트는 `.info-panel .panel-header`로 스코핑. 화면 단위의 `.panel-header`는 그라디언트 없음
- **이유**: 원래 `.panel-header` 하나에 그라디언트를 선언하고 `[data-screen="X"] .panel-header{background:none}` 예외를 4개 추가하는 방식이었음. CSS 선택자 특이성(specificity)을 이용해 상위 컨텍스트(`.info-panel`)로 스코핑하면 예외 규칙 4개가 불필요해짐
- **주의**: 사이드바 인포패널 내부 헤더만 그라디언트 필요. 새 화면에 `.panel-header`를 추가할 때 그라디언트가 자동 적용되지 않음 — 의도된 동작

### 인라인 스타일 이전 원칙
- **결정**: 마크업에 남아 있던 인라인 스타일을 CSS로 이전. 아래 두 경우만 인라인 유지
  1. `display:none` — JS가 `element.style.display`를 직접 조작
  2. JS가 상태에 따라 직접 값을 쓰는 스타일 (예: `dtHourTab`, `dtMinTab`의 border/background/color)
- **이유**: 인라인 스타일이 혼재하면 CSS만 봐서는 전체 레이아웃 파악이 불가능. JS 템플릿 리터럴(`dtRenderCal`, `dtRenderClock`, `_renderComments`) 내부 인라인 스타일은 JS 리팩터링이 필요하므로 이번 범위 외

### JS 상태 제어 요소의 인라인 스타일 유지
- **결정**: `#dtHourTab`, `#dtMinTab` 버튼의 border/background/color는 인라인 스타일 유지
- **이유**: JS가 시간/분 탭 전환 시 `el.style.border`, `el.style.background`, `el.style.color`를 직접 덮어쓰는 구조. CSS로 분리하면 JS의 직접 조작이 인라인 > CSS 우선순위로 항상 이김 — 문제는 없으나, 어떤 상태가 "기본"인지가 CSS에도 인라인에도 양쪽에 나뉘어 혼란스러워짐. 원래 구조를 유지하는 게 안전

### 프로토타입 잔존 인라인 — 수용된 예외 (이전 안 함)
- **결정**: 아래 정적 인라인 스타일은 프로토타입 단계의 수용된 예외로, CSS 클래스로 이전하지 않는다. AI는 "인라인 정리"를 이유로 `index_m.html`을 수정하지 않는다.
  - 영수증 이미지 표시 크기 `width:100%;max-width:200px;max-height:300px;height:auto;display:block` (imgview·영수증 본문)
  - 오버레이 배경·z-index `display:none;background:rgba(...);z-index;overflow` — 오버레이마다 값이 달라 인라인 유지(`display:none` 정책과 동일 맥락)
  - 단속성 유틸 `cursor:pointer` · `pointer-events:none` · `width:100%`
- **이유**: 단일 HTML 모놀리식 파일 특성상 인라인 정리 편집은 회귀(재생성) 위험 대비 가치가 낮다. 자동 검사가 이들을 "OTHER"로 표기해도 의도된 수용 예외다.

---

## 레이아웃

### 실제 폰 전체화면 구조
- **결정**: `html`, `body`, `.phone` 모두 `height:100%`. 고정 크기(375×812) 프레임 제거
- **이유**: 실제 모바일 기기에서 확인하는 방식으로 전환. 데스크탑 프리뷰용 프레임(.phone .notch, box-shadow 등) 불필요

### 탭바 하단 고정
- **결정**: `.tabbar`는 `position:absolute;bottom:0`. 콘텐츠 영역에 하단 여백 필수
- **이유**: `.app`이 `position:relative`인 flex 컨테이너이므로 탭바가 absolute로 올라탐. 이를 모르면 콘텐츠가 탭바에 가려짐
- **클래스 상세**: `rules_LAYOUT.md` — 하단 여백 유틸리티 참고

---

## 컴포넌트

### 버튼 태그 (mobile vs PC)
- **결정**: 모바일은 `<button>` 태그 사용. PC 스타일 가이드의 `<a href="#none">` 규칙을 따르지 않음
- **이유**: 모바일 폼/액션에서 button 태그가 의미적으로 올바르고 접근성에 유리

### 바텀시트 토글 방식
- **결정**: `.sheet-backdrop`에 `.show` 클래스를 JS로 추가/제거하여 열고 닫음
- **이유**: 오버레이와 달리 `display:none`이 인라인에 없으므로 classList 조작만으로 충분. 구현 패턴은 `rules_LAYOUT.md` — 바텀시트 참고

### 결재선 (approval-line) 컴포넌트
- **결정**: 휴가신청/계획/취소 3개 화면은 아바타 열 방식(`.approval-line-wrap`), 비용처리 화면은 콤팩트 행 방식(`.approval-compact-row`) 사용
- **이유**: 비용처리 화면은 결재자가 3명(신청자 → 1차 → 2차)이고 공간이 좁아 아바타 방식보다 인라인 텍스트 나열이 적합

### 게시글 액션시트 (action-sheet-*)
- **결정**: `.action-sheet-body`, `.action-sheet-handle`, `.action-sheet-item`, `.action-sheet-item-danger`, `.action-sheet-cancel` 클래스로 구성
- **이유**: `#postActionSheet`는 overlay이므로 position/background는 ID 셀렉터, 내부 레이아웃은 클래스로 분리

### 결재선 변경 팝업 (alp-*)
- **결정**: `alp-option`, `alp-option-radio`, `alp-footer`, `alp-btn-cancel`, `alp-btn-confirm` 프리픽스 사용
- **이유**: `.approval-*` 프리픽스가 결재선 표시 컴포넌트에 이미 사용 중이므로 충돌 방지를 위해 단축 프리픽스(`alp-`) 사용

### 사용일시 선택 팝업 (dt-*)
- **결정**: `dt-picker-*`, `dt-cal-*`, `dt-dow-*`, `dt-day-*`, `dt-time-*`, `dt-clock-*`, `dt-actions`, `dt-btn-*` 프리픽스 사용
- **이유**: datetime picker는 캘린더 + 시계 두 파트로 구성된 복합 컴포넌트. `dt-` 네임스페이스로 묶어 타 컴포넌트와 명확히 구분
- **주의**: `dtRenderCal()`, `dtRenderClock()` JS 함수 내부의 인라인 스타일은 이전하지 않음 — JS 템플릿 리터럴이라 CSS 클래스화 시 함수 전체 리팩터링 필요

### 기존 클래스 재활용 우선
- **결정**: 새 컴포넌트 마크업 작성 시 신규 클래스 생성 전에 기존 CSS에서 재활용 가능한 클래스 먼저 탐색
- **이유**: CSS 파일이 커지는 것을 억제. 실제로 재활용한 클래스: `.pf-result-overlay`, `.result-overlay-sm`, `.popup-header`, `.popup-list`, `.input-row-card`, `.icon-btn-sq-44`, `.tag-row`, `.link-sm-accent`, `.info-card-blue`, `.text-note`

### 프로토타입 구현 범위 (상태 저장 미구현 목록)

이 프로젝트는 프로토타입 단계로, 아래 요소들은 시각적 동작만 구현되어 있다.
상태 저장·복원·서버 연동은 구현 범위 밖이며, AI가 임의로 추가하지 않는다.
실제 구현이 필요한 경우 별도 지시로 범위를 명확히 한 뒤 작업한다.

| 요소 | 화면 | 유형 | 비고 |
|------|------|------|------|
| 휴가 종류 토글 (종일/오전반차/오후반차) | `leave-apply` | 토글 | `selectToggle()` .active 교체. 선택값 미저장이나, `_halfDayActive()`가 .active를 읽어 반차 선택 시 캘린더 단일선택 제한(범위 드래그 차단)에 사용 |
| 사유 텍스트에리어 | `leave-apply` | 입력 | 제출 시 값 미읽음 |
| 비상 연락망 input | `leave-apply` | 입력 | JS 참조 없음 |
| 취소할 휴가 종류 토글 (종일/오전반차/오후반차) | `leave-cancel` | 토글 | `selectToggle()` .active 교체. 선택값 미저장이나, `_halfDayActive()`가 .active를 읽어 반차 선택 시 캘린더 단일선택 제한(범위 드래그 차단)에 사용 |
| 취소 사유 선택 토글 (일정변경/업무일정/개인사정/기타) | `leave-cancel` | 토글 | `selectToggle()` CSS .active 교체만, 선택값 미저장 |
| 취소 사유 직접입력 텍스트에리어 | `leave-cancel` | 입력 | JS 참조 없음 |
| 계획 사유/메모 텍스트에리어 | `leave-plan` | 입력 | 제출 시 값 미읽음 |
| 푸시 알림 받기 토글 스위치 | `notif-settings` | 토글 | `toggleNotifSwitch()` CSS .active 교체만, 설정값 미저장 |
| 연차/휴가 알림 토글 스위치 | `notif-settings` | 토글 | 동일 |
| 증명서 알림 토글 스위치 | `notif-settings` | 토글 | 동일 |
| 영수증/결의서 알림 토글 스위치 | `notif-settings` | 토글 | 동일 |
| 게시판/공지 알림 토글 스위치 | `notif-settings` | 토글 | 동일 |
| 방해금지 모드 토글 스위치 | `notif-settings` | 토글 | CSS .active + 시간 섹션 표시/숨김만, 활성값 미저장 |
| 방해금지 시작 시간 시·분 select | `notif-settings` | 입력 | JS 참조 없음 |
| 방해금지 종료 시간 시·분 select | `notif-settings` | 입력 | JS 참조 없음 |
| 용도 select | `receipt-form` | 입력 | 제출 시 값 미읽음 |
| 내용 텍스트에리어 | `receipt-form` | 입력 | 제출 시 값 미읽음 |
| 사업자번호 input (사용처 직접입력) | `receipt-form` | 입력 | JS 참조 없음 |
| 지급처 기타 직접입력 input | `receipt-form` | 입력 | JS 참조 없음 |
| 결의서 제목 input | `expense-form` | 입력 | 제출 시 값 미읽음 |
| 결의서 내용 텍스트에리어 | `expense-form` | 입력 | 제출 시 값 미읽음 |
| 댓글 등록/수정/삭제 | `post-detail` | 입력·버튼 | `addComment`·`saveCommentEdit`·`confirmDeleteComment` 구현. `_POST_DETAIL[id].comments` 세션 배열만 조작 + `_renderComments()` 재렌더 — 서버 영속만 범위 밖 |
| 자동 로그인 체크박스 | `login` | 체크박스 | 로그인 로직에서 체크 상태 미읽음 |
| 결재선 선택 라디오 (기본/임원) | 오버레이 (`#approvalLinePopup`) | 라디오 | `selectApprovalLine()` 배경색 변경만, 선택값 폼에 미반영 |

> **AI 작업 기준**
> - 위 요소에 기능을 추가하거나 상태 로직을 연결하지 않는다
> - UI 수정(마크업·스타일) 요청 시에도 기존 동작 방식을 유지한다
> - "완성해줘" 또는 "기능을 붙여줘" 지시가 명시적으로 있을 때만 해당 항목을 구현한다

---

## 반응형

### 태블릿 분할 레이아웃 (≥900px)
- **결정**: `@media (min-width:900px)` 블록에 분할 레이아웃 구현. JS는 상태 클래스(`has-detail`, `has-faq`, `has-faq-detail`)만 관리하고, CSS 미디어 쿼리가 레이아웃 발동 조건을 결정
- **이유**: 클래스 추가/제거로 레이아웃을 트리거하면 `window.innerWidth` 체크와 CSS 규칙이 이중으로 관리되어 혼란. 미디어 쿼리 안에 규칙을 두면 너비 조건은 CSS 한 곳에서만 관리됨
- **분할 패턴**:
  - `has-detail` — 게시판(좌) + 게시글 상세(우)
  - `has-faq` — 더보기(좌) + 인사 FAQ 목록(우)
  - `has-faq-detail` — 더보기(좌) + 인사 FAQ 상세(우)
- **탭바/FAB**: 태블릿 분할 시 `width:50%`로 좌측 패널에만 고정. FAB는 `right:calc(50% + 18px)`로 좌측 패널 오른쪽 끝에 위치
- **comment-input**: `left:50%;bottom:0`으로 우측 패널 하단에만 고정
- **goBack() 처리**: `has-faq-detail` 상태에서 뒤로가기 클릭 시 `has-faq`로 복원 (일반 `showScreen()` 흐름 우회)
- **분할 해제 조건**: `showScreen()` 또는 `goTab()` 호출 시 모든 분할 클래스 제거

---

## 문서화

### MD 파일 구성
- **결정**: `rules_README.md`(프로젝트 개요) + `rules_DECISIONS.md`(결정 맥락) + `rules_LAYOUT.md`(레이아웃 레퍼런스) + `rules_COMPONENTS.md`(컴포넌트 레퍼런스) + `rules_FLOWMAP.md`(화면 플로우맵) + `checklist_new_screen_mobile.md`(신규 화면 생성 절차) 6개 유지
- **이유**: 기획 파트에서 Claude가 읽는 컨텍스트로 사용. `mobile-style-guide.html`은 실제 렌더링 확인용이고, MD는 Claude가 코드 작성 시 참조하는 레퍼런스
- **스타일 가이드 업데이트 시**: `mobile-style-guide.html`과 `rules_COMPONENTS.md`를 반드시 동시에 수정
> 신규 화면·컴포넌트 생성은 퍼블 파트가 담당하며 `checklist_new_screen_mobile.md`를 단일 출처로 따른다.
> 기획 파트는 기존 컴포넌트 조립·편집만 하고, 없는 것은 만들지 말고 퍼블에 요청한다.

---

### 고립 화면 처리 기준 (2026.06.01 확정)

| data-screen | 상태 | 처리 방향 |
|---|---|---|
| `leave-history` | 기획 보류 | 연차 화면 개편 시 재검토 |
| `cert-status` | 기획 보류 | 증명서 탭 UX 개편 시 재검토 |
| `cert-form` | 기획 보류 | certModal 발급 플로우 안정화 후 재검토 |
| `faq-write` | 기획 보류 | 인사 FAQ 관리자 기능 기획 확정 후 재검토 |
| `faq-detail` | 기획 보류 | faq-write 연결 후 함께 활성화 |
| `faq` | 기획 보류 | faq-write/faq-detail 활성화 시 함께 연결 |

> 위 화면들은 마크업을 삭제하지 말고 유지할 것.  
> AI가 새 기능을 추가할 때 이 화면들을 참조하거나 수정하지 않는다.

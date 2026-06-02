---
name: rules_myinsa_pc_components_core
description: MY인사 PC 모든 화면에 공통 필요한 핵심 컴포넌트 — 버튼·뱃지·툴바·테이블·패널·모달·폼·토스트·유틸 HTML 예시+클래스+JS
metadata: 
  node_type: memory
  type: project
  originSessionId: c4cb22ee-5bf5-4a66-91cc-1113065fd4e6
---

**Why:** MY인사_PC_v31.html + my_cont.css 기반 핵심 컴포넌트. 모든 화면에 필요.
**How to apply:** 신규 화면 제작 시 이 파일 우선 참조. 레이아웃 골격은 [[rules_myinsa_pc_layout]], 화면별 골격은 [[rules_myinsa_pc_pages]], 페이지 전용 컴포넌트는 [[rules_myinsa_pc_components_page]] 참조.

---

## 필수 규칙

- **버튼 태그 금지** — 모든 버튼은 `<a href="#none" class="btn">`. `<button>` 사용 불가
- **인라인 CSS 금지** — 모든 스타일은 my_cont.css 클래스로. `style=""` 사용 불가. **아래 4종만 예외 허용, 그 외 모든 속성(`color`, `margin`, `padding`, `font-size`, `flex`, `background` 등) 인라인 사용 절대 금지:**
  - 열 너비: `<th style="width:120px">`, `<col style="width:...">` — 테이블 레이아웃 전용
  - 이중 스크롤 높이: `style="max-height:calc(100vh - 260px);overflow-y:auto;..."` — 연차 테이블 전용 (다른 페이지는 CSS 클래스 `.scroll-body` 사용)
  - 동적 % 채움: `style="width:26.7%"` — JS가 계산한 수치를 프로그레스바에 직접 주입하는 경우만 허용
  - 표시 토글: `style="display:none"` — 모드 전환 초기 숨김 (JS `.style.display` 제어 대상에 한함)
  - ⚠️ 위 4종 외에 `style="color:red"`, `style="margin:10px"` 등은 신규 CSS 클래스를 만들어서 처리
- **a 태그 기본** — `<a>` 는 `text-decoration:none; color:inherit` 전역 적용됨
- **`my_tokens.css` 수정 금지** — PC·index 두 화면 토큰이 공존. 변경 시 양쪽 모두 깨짐. 새 변수 필요 시 `my_cont.css` 상단에 `--` 로컬 변수로 정의
- **`:root` 재정의 금지** — `my_cont.css` 내에서 `:root` 재선언 불가
- **index.html 전용 토큰 사용 금지** — `--primary-*`, `--label-*`, `--bg-normal`, `--radius-card-lg`, `--t-*`, `--z-modal-overlay`, `--shadow-popup` 등은 index.html 전용. PC 화면에서 참조 시 z-index 스케일 파괴(1000→8000) 등 레이아웃 붕괴 위험

### 잘못된 패턴 예시

| ❌ 금지 | ✅ 올바른 방법 |
|---|---|
| `<button onclick="goPage(this)">메뉴</button>` | `<a href="#none" class="nav-item" onclick="goPage(this)">메뉴</a>` |
| `<a href="#">닫기</a>` | `<a href="#none" class="btn">닫기</a>` |
| `<div style="color:red">오류</div>` | `<div class="text-danger">오류</div>` |
| `<div style="margin-top:20px">` | CSS 클래스 `.mt-5` 또는 신규 클래스 추가 |
| `<span style="font-size:12px">` | CSS 클래스 또는 `--fs-sm` 토큰으로 클래스 작성 |
| `color: #2363a8` (하드코딩) | `color: var(--accent)` |
| 새 CSS를 HTML `<style>` 태그에 작성 | `my_cont.css`에 클래스 추가 후 HTML에서 클래스명 참조 |

### 수정 범위

| 파일 | 수정 여부 | 메모 |
|---|---|---|
| `_common/my_tokens.css` | ❌ 읽기 전용 | 공통 토큰 — 절대 수정 금지 |
| `pc/my_cont.css` | ✅ 수정 가능 | PC 전용 레이아웃 + 컴포넌트 CSS |
| `pc/MY인사_PC_v31.html` | ✅ 수정 가능 | — |

---

## 동적 상태 클래스

JS `classList`로 추가/제거되는 클래스 목록.

| 클래스 | 적용 대상 | 동작 |
|---|---|---|
| `.active` | `.nav-item`, `.page`, `.tab-pane`, `.emp-tab-btn`, `.faq-cat-btn`, `.split-list-row` | 활성 항목 강조 |
| `.show` | `.modal-backdrop`, `.toast-v7`, `.sbp-dropdown` | 표시 (`display:flex` 또는 `display:block`) |
| `.open` | `.sb-profile` | 사이드바 프로필 드롭다운 열림 — 화살표 180° rotate |
| `.selected` | `.sbp-option` | 역할 스위처에서 현재 선택된 역할 체크 표시 |
| `.editing` | `.editable` | 인라인 편집 모드 활성화 |
| `.dragging` | `.col-resizer` | 열 너비 조절 중 (accent 색) |

> **`.active` vs `.selected`:** `.faq-cat-btn`은 `.active`, `.sbp-option`은 `.selected` 사용 — 컴포넌트마다 원래 클래스를 그대로 유지, 혼용 금지.

---

## 1. 버튼

```html
<!-- 기본 버튼 -->
<a href="#none" class="btn">기본</a>

<!-- 강조(Primary) -->
<a href="#none" class="btn btn-primary">저장</a>

<!-- 소형 -->
<a href="#none" class="btn btn-sm">소형</a>

<!-- 위험 -->
<a href="#none" class="btn btn--danger">삭제</a>

<!-- 텍스트형 위험 -->
<a href="#none" class="btn btn--link-danger">반려</a>

<!-- 카카오 -->
<a href="#none" class="btn btn-kakao">카카오 발송</a>

<!-- 버튼 그룹 -->
<div class="btn-group">
  <a href="#none" class="btn">취소</a>
  <a href="#none" class="btn btn-primary">저장</a>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.btn` | 기본 버튼 (height:34px, border:1.5px solid --border, --surface bg) |
| `.btn-primary` | 강조 버튼 (accent 그라디언트 bg, 흰 글자, shadow) |
| `.btn-sm` | 소형 (height:28px, padding 0 10px, --fs-sm) |
| `.btn--danger` | 삭제·위험 (--danger bg, 흰 글자) |
| `.btn--link-danger` | 텍스트형 위험 (color:--danger만, bg 없음) |
| `.btn-kakao` | 카카오 모달 버튼 modifier (#FEE500 bg, 진갈색 글자). `.btn`과 함께 사용: `<a class="btn btn-kakao">` |
| `.kakao-btn` | **`.btn` 체계와 무관한 완전 독립 클래스** (카카오 브랜드 배경, `padding:6px 12px`, `--fs-sm`). `.btn` 불필요. 모바일 안내 설치 현황 테이블 행에서 JS가 렌더링. `.btn-kakao`와 혼용 금지. 아래 `btn-xxx` 네이밍 규칙 대상이 아님. |
| `.btn-group` | 버튼 묶음 (flex, gap:--sp-3) |
| `.cert-type-btn` | 증명서 유형 선택 버튼 (justify:flex-start, height:56px). **반드시 `.btn`과 함께 사용**: `<a class="btn cert-type-btn">` |

**`.btn` 수식어 네이밍 규칙**

| 패턴 | 형식 | 의미 | 예시 |
|---|---|---|---|
| `btn-xxx` | 단일 대시 | 외형 변형 (색상·크기·브랜드) | `btn-primary`, `btn-sm`, `btn-kakao` |
| `btn--xxx` | 이중 대시 | 의미론적 상태 (위험·링크형) | `btn--danger`, `btn--link-danger` |

> 이 패턴은 기존 CSS에 이미 정의된 규칙이므로 **신규 modifier 추가 시 동일 패턴을 따른다.** 외형 변형이면 단일 대시(`btn-xxx`), 의미·상태 변형이면 이중 대시(`btn--xxx`).

**조합 규칙:**

```html
<!-- 크기 + 색상 조합 가능 -->
<a href="#none" class="btn btn-sm btn-primary">소형 강조</a>

<!-- 의미론적 modifier는 단독 사용 -->
<a href="#none" class="btn btn--danger">삭제</a>

<!-- .kakao-btn은 .btn 없이 독립 사용 (테이블 JS 렌더링 전용) -->
<!-- .btn-kakao는 반드시 .btn과 함께 사용 (모달 내 버튼) -->
```

### 특수 기능 버튼 처리 패턴

일반 `<a href="#none" class="btn">` 외에, 아래 특수 버튼은 별도 패턴을 따른다.

| 버튼 유형 | 클래스 | onclick 패턴 | 사용 페이지 |
|---|---|---|---|
| 카카오 발송 (모달) | `btn btn-kakao` | `openKakaoSingle(id)` / `openKakaoBulk()` | cert, onboard |
| 카카오 발송 (테이블 행) | `kakao-btn` | JS가 렌더링 — HTML 직접 작성 금지 | onboard |
| 엑셀 다운로드 | `btn` | JS 다운로드 함수 또는 `<a href="파일경로" download>` | cert |
| PDF 발급 | `btn btn-primary` | `openCertIssueModal(name,kind,purpose,date)` | cert |
| QR 다운로드 | `btn` | JS 다운로드 함수 | onboard |
| 인사정보 수정 | `icon-btn hr-edit-btn` | `openHrEditModal(type,...)` | emp-profile |

> `.kakao-btn`과 `.btn-kakao`는 **혼용 금지** — 전자는 테이블 행 독립 버튼, 후자는 모달 내 `.btn` 위 수식어.

---

## 2. 뱃지

```html
<!-- 상태 뱃지 -->
<span class="badge s">승인</span>   <!-- success: 초록 -->
<span class="badge w">대기</span>   <!-- warning: 노랑 -->
<span class="badge d">반려</span>   <!-- danger: 빨강 -->
<span class="badge i">정보</span>   <!-- info: 파랑 -->
<span class="badge muted">-</span>  <!-- 회색 -->

<!-- FAQ 카테고리 뱃지 -->
<span class="badge cert">증명서</span>
<span class="badge pay">급여</span>
<span class="badge etc">기타</span>

<!-- 증명서 종류 뱃지 (테이블 내) -->
<span class="cert-badge cert-badge-emp">재직증명서</span>
<span class="cert-badge cert-badge-exp">경력증명서</span>
<span class="cert-badge cert-badge-other">기타증명서</span>
```

| 클래스 | 설명 |
|---|---|
| `.badge` | 공통 (inline-flex, border-radius:20px, --fs-xs, --fw-bold) |
| `.badge.s` | 성공 (--success + --success-bg) |
| `.badge.w` | 경고 (--warning + --warning-bg) |
| `.badge.d` | 위험 (--danger + --danger-bg) |
| `.badge.i` | 정보 (--info + --info-bg) |
| `.badge.muted` | 중립 (--text-tertiary + --bg) |
| `.badge.cert` | 증명서 (#deeef8 bg + #2363a8) |
| `.badge.pay` | 급여 (#fef9c3 bg + #854d0e) |
| `.badge.etc` | 기타 (#f3f4f6 bg + #4b5563) |
| `.cert-badge` | 증명서 신청 뱃지 공통 |
| `.cert-badge-emp` | 재직 (--badge-emp-bg/fg) |
| `.cert-badge-exp` | 경력 (--badge-exp-bg/fg) |
| `.cert-badge-other` | 기타 (--badge-other-bg/fg) |

---

## 3. 아이콘 버튼 / 아바타

```html
<!-- 아이콘 버튼 (26×26) -->
<a href="#none" class="icon-btn"><svg>...</svg></a>

<!-- 아이콘 아바타 (36×36) — 글자 이니셜 -->
<div class="icon-avatar icon-avatar--accent">한</div>

<!-- 아이콘 아바타 — SVG 아이콘 -->
<div class="icon-avatar icon-avatar--cert">
  <svg>...</svg>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.icon-btn` | 아이콘 버튼 (26×26, border-radius:4px, hover:--accent-light) |
| `.icon-avatar` | 아바타 박스 (36×36, border-radius:--radius-lg) |
| `.icon-avatar--accent` | 브랜드색 (--accent-light bg, --accent fg) |
| `.icon-avatar--cert` | 증명서 (--badge-emp-bg) |
| `.icon-avatar--career` | 경력 (#fce7f3) |
| `.icon-avatar--other` | 기타 (--badge-other-bg) |

---

## 4. 검색 / 필터 / 툴바

```html
<div class="toolbar">
  <div class="filters">
    <input class="search-input" placeholder="검색" />
    <select class="filter-input"><option>전체</option></select>
    <input class="filter-input" type="date" />
    <span class="hint-info">
      <svg>...</svg>
      안내 텍스트
    </span>
  </div>
  <div class="btn-group">
    <a href="#none" class="btn btn-primary">추가</a>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.toolbar` | 검색+버튼 행 래퍼 (flex, space-between, mb:--sp-6) |
| `.filters` | 검색·필터 묶음 (flex, gap:--sp-3) |
| `.search-input` | 검색 입력 (240×34px, 돋보기 아이콘 CSS bg-image) |
| `.filter-input` | 필터 입력 (height:34px, --radius-md, focus 시 accent 테두리) |
| `.hint-info` | 힌트 텍스트 (flex, --fs-sm, --info 색) |

---

## 5. 테이블

```html
<!-- 기본 구조 -->
<div class="table-wrap">
  <div class="scroll-body">
    <table class="tbl">
      <thead>
        <tr>
          <th style="width:60px">번호</th>
          <th>이름</th>
          <th class="num">금액</th>
          <th class="center">상태</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>1</td>
          <td><strong>홍길동</strong></td>
          <td class="num">1,000</td>
          <td class="center"><span class="badge s">승인</span></td>
        </tr>
      </tbody>
    </table>
  </div>
  <!-- 페이지네이션 (선택) -->
  <div class="pagi">
    <span>총 100건</span>
    <div class="controls">
      <a href="#none">«</a>
      <a href="#none" class="active">1</a>
      <a href="#none">»</a>
    </div>
  </div>
</div>

<!-- 연차 관리 — 이중 스크롤 구조 주의 -->
<div class="table-wrap leave-table">
  <div class="ov-x-auto">                 <!-- 가로 스크롤 -->
    <div class="scroll-body" id="leaveScroll"
      style="max-height:calc(100vh - 260px);overflow-y:auto;overflow-x:visible">  <!-- 세로 스크롤 -->
      <table class="tbl" style="min-width:1100px">...</table>
    </div>
  </div>
</div>
```

**⚠️ 이중 스크롤 구조 규칙:**  
`.ov-x-auto`(가로) 안에 `.scroll-body`(세로)를 **중첩**하는 구조. "분리"가 아니라 **부모-자식 관계**.  
동일 요소에 overflow-x·y를 함께 지정하면 sticky thead가 깨지는 브라우저 버그를 우회하기 위해 래퍼를 나눈 것.  
→ **이 순서(가로 밖 > 세로 안)를 반드시 유지. 역순 시 sticky 헤더 깨짐.**

| 클래스 | 설명 |
|---|---|
| `.table-wrap` | 테이블 외곽 (--surface bg, --radius-xl, --shadow-card, overflow:clip) |
| `table.tbl` | 기본 테이블 (width:100%, border-collapse:collapse, table-layout:fixed) |
| `table.tbl.tbl--auto` | 자동 너비 테이블 (table-layout:auto) |
| `.scroll-body` | 세로 스크롤 래퍼. **높이 규칙:** CSS 클래스 기본값 `max-height:calc(100vh - 280px)`. 연차 테이블(`leaveScroll`)만 toolbar 높이 차이로 인라인 `max-height:calc(100vh - 260px)` 재정의 사용. **다른 모든 페이지는 인라인 재정의 없이 CSS 클래스 그대로 사용(280px).** |
| `.ov-x-auto` | 가로 스크롤 래퍼 (overflow-x:auto) |
| `.num` | 숫자 셀 (tabular-nums, text-align:right) |
| `.center` | 중앙 정렬 셀 |
| `.wrap` | 줄바꿈 허용 td |
| `.col-sticky-l` | 왼쪽 sticky 열 |
| `.col-sticky-r` | 오른쪽 sticky 열 (그림자 포함) |
| `.editable` | 더블클릭 인라인 편집 가능 셀 |
| `.row-actions` | 행 액션 버튼 묶음 (flex, gap:--sp-1) |
| `.pagi` | 페이지네이션 바 (flex, space-between, border-top) |
| `.leave-table` | 연차 관리 테이블 변형 — 이중 스크롤 구조용. `.month-h`·`.used`·`.remain` 수식어 포함 |
| `.leave-table .month-h` | 월별 헤더 열 (--fw-medium, --text-secondary bg) |
| `.leave-table .used` | 사용 열 (--text-secondary) |
| `.leave-table .remain` | 잔여 열 (--fw-semibold, --text) |
| `.leave-total-cell` | 편집 가능한 총 연차 셀 (--accent-light bg, --leave-total-fg, 더블클릭 편집 → `editTotal(this)`) |
| `.emp-tbl-wrap` | 직원 목록 테이블 래퍼 (overflow-y:auto, width:100%) |
| `.emp-tbl` | 직원 목록 테이블 (table-layout:fixed, thead/tbody 별도 padding 규격) |
| `.scroll-loader` | 무한스크롤 로더 행 (padding:14px, text-center, --text-tertiary, border-top) |
| `.scroll-loader .spin` | 로딩 스피너 (14×14px, border-top:--accent, animation:spin .8s) |
| `.col-resizer` | 열 너비 조절 핸들. **JS가 `thead th`마다 자동 주입 — HTML에 직접 작성 금지.** position:absolute; right:-3px; width:8px. 평소 투명, hover 시 `::before` 구분선(1px) 표시, drag 시 --accent 색 강조. 마지막 열(`th:last-child`)은 `display:none` 자동 처리. `.dragging` = 드래그 중 상태 |
| `.empty` | 빈 상태 텍스트 (text-center, padding:60px 20px, --text-tertiary) |

---

## 6. 모달

```html
<div class="modal-backdrop" id="xxxModalBg">
  <div class="modal modal--md">
    <div class="modal-head">
      <h3>모달 제목</h3>
      <div class="close" onclick="document.getElementById('xxxModalBg').classList.remove('show')">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
        </svg>
      </div>
    </div>
    <div class="modal-body">
      <!-- 내용 -->
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="document.getElementById('xxxModalBg').classList.remove('show')">취소</a>
      <a href="#none" class="btn btn-primary">확인</a>
    </div>
  </div>
</div>
```

**모달 크기 수식어**

| 클래스 | 너비 | 용도 |
|---|---|---|
| `.modal--xs` | 400px | 확인 다이얼로그 |
| `.modal--sm` | 480px | 단순 폼 |
| `.modal--md` | 520px | 중간 폼 |
| (기본) | 560px | 기본 |
| `.modal--lg` | 680px | 대형 콘텐츠 |
| `.modal--xl` | 700px | 초대형 |

**모달 내부 클래스**

| 클래스 | 설명 |
|---|---|
| `.modal-backdrop` | 딤 배경 (fixed, inset:0, rgba(0,0,0,.4)). `.show` → display:flex |
| `.modal` | 모달 박스 (--surface, --radius-xl, flex-column) |
| `.modal-head` | 헤더 (flex, space-between, padding 16px 20px, border-bottom) |
| `.modal-body` | 본문 (padding:20px, overflow-y:auto, flex:1) |
| `.modal-body--flush` | 본문 padding 없음 |
| `.modal-body--list` | 리스트형 본문 (p-0, 아이템 간 구분선) |
| `.modal-foot` | 하단 버튼 행 (flex-end, border-top) |
| `.modal-foot--between` | 하단 버튼 space-between |
| `.modal-section` | 본문 내 구분 섹션 (--bg, padding, border-bottom) |
| `.modal-section--sep` | 구분 섹션 (--surface bg) |
| `.modal-section--plain` | 구분 없는 섹션 |
| `.modal-banner` | 강조 배너 행 (--accent-light bg) |
| `.modal-notice` | 안내 박스 (--info-bg, --radius-md, flex) |
| `.modal-summary` | 요약 박스 (--bg, border, --radius-md) |
| `.modal-reason-box` | 반려사유 박스 (--danger-bg, border) |
| `.modal-scroll-list` | 스크롤 가능한 목록 영역 |

---

## 7. 패널 카드

```html
<div class="panel">
  <div class="panel-head">
    <h3>패널 제목</h3>
    <a href="#none" class="btn btn-sm btn-primary">추가</a>
  </div>
  <div class="panel-body">
    <!-- 내용 -->
  </div>
</div>

<!-- 패딩 없는 패널 본문 (테이블 등) -->
<div class="panel-body p-0">...</div>

<!-- 패널 내부 구분 섹션 -->
<div class="section-card">
  <div class="panel-section-hd">소제목</div>
  <div class="info-row">
    <span class="text-secondary">레이블</span>
    <strong>값</strong>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.panel` | 패널 박스 (--surface, --radius-xl, --shadow-card, overflow:hidden) |
| `.panel--outlined` | 테두리 강조 변형 |
| `.panel--start` | 상단 정렬 변형 |
| `.panel-head` | 헤더 (flex, space-between, padding 14px 18px, border-bottom) |
| `.panel-body` | 본문 (padding:--sp-8) |
| `.panel-body--lg` | 넓은 패딩 변형 |
| `.panel-body--center` | 중앙 정렬 변형 |
| `.panel-inner` | 패널 내부 패딩 영역 |
| `.section-card` | 내부 구분 카드 (border, --radius-lg) |
| `.panel-section-hd` | 섹션 소제목 (--fs-xs, uppercase, border-bottom) |
| `.info-row` | 레이블+값 행 (flex, space-between) |

---

## 8. 폼

```html
<div class="form-row">
  <div class="field">
    <label>이름 <span class="req">*</span></label>
    <input type="text" placeholder="입력" />
  </div>
  <div class="field">
    <label>부서 <span class="label-opt">(선택)</span></label>
    <select>...</select>
  </div>
</div>
<div class="field">
  <label class="label-sm">메모</label>
  <textarea class="textarea" rows="5" placeholder="입력해주세요"></textarea>
  <div class="field-hint">💡 안내 텍스트</div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.form-row` | 폼 행 (flex, gap:--sp-5, mb:--sp-5). 자식 `.field` → flex:1 |
| `.field` | 필드 래퍼. `label` → uppercase, --fs-xs, mb:--sp-2 |
| `.field input/select/textarea` | 공통 (padding 9px 12px, --radius-md, focus:accent 테두리) |
| `.label-sm` | 중간 레이블 (--fs-sm, --fw-semibold, --text-secondary) |
| `.label-opt` | "(선택)" 부가 표기 (--fs-2xs, --text-tertiary) |
| `.textarea` | 독립 textarea 클래스 (resize:vertical) |
| `.info-box` | 읽기 전용 값 박스 (--bg, --radius-md, border) |
| `.field-hint` | 필드 힌트 (--fs-xs, --text-tertiary) |
| `.code-chip` | 인라인 코드 칩 (--bg, border, --radius-sm, monospace) |

---

## 9. 토스트 / 빈 상태

```html
<!-- 토스트 — HTML에 한 번만 선언 -->
<div class="toast-v7" id="toastV7"></div>

<!-- 빈 상태 -->
<div class="empty-state">
  <svg>...</svg>
  <p>데이터가 없습니다</p>
</div>

<!-- 빈 상태 메시지 (테이블 내) -->
<tr><td colspan="6"><div class="empty-msg">등록된 항목이 없습니다.</div></td></tr>
```

| 클래스 | 설명 |
|---|---|
| `.toast-v7` | 토스트 컨테이너 (fixed, bottom:24px, center, --radius-lg). `.show` → 표시 |
| `.empty-state` | 빈 상태 (text-center, --text-tertiary, padding) |
| `.empty-msg` | 간략 빈 메시지 (--fs-sm, --text-tertiary, text-center) |

---

## 10. 유틸리티 클래스

| 클래스 | 설명 |
|---|---|
| `.section-label` | 섹션 소제목 (--fs-xs, uppercase, letter-spacing) |
| `.item-title` | 항목 제목 (--fs-body, --fw-semibold) |
| `.item-name` | 항목명 (--fs-sm, --fw-semibold) |
| `.item-bold` | 굵은 본문 (--fs-body, --fw-bold) |
| `.item-md` | 중간 강조 (--fs-md, --fw-bold) |
| `.item-meta` | 메타 텍스트 (--fs-xs, --text-tertiary) |
| `.item-meta-t` | 메타 텍스트 변형 (상단 간격 추가) |
| `.item-sub` | 보조 텍스트 (--fs-sm, --text-secondary, mt:2px) |
| `.list-row` | 카드형 행 (flex, border:1.5px, --radius-md, --surface) |
| `.split` | 좌우 분할 (grid, 300px 1fr) |
| `.split-list-row` | 분할 목록 행 (border-bottom). `.active` → accent-light + 3px 선 |
| `.flex-col` | 세로 flex (gap:--sp-7) |
| `.flex-1` | flex:1 |
| `.flex-1-min` | flex:1, min-width:0 (truncate 조합용) |
| `.flex-center` | 수평 중앙 (align-center, gap:--sp-3) |
| `.row-between` | 양끝 정렬 (flex, space-between) |
| `.push-right` | 오른쪽 밀기 (margin-left:auto) |
| `.text-left` | 왼쪽 정렬 |
| `.truncate` | 말줄임 (overflow:hidden, text-overflow:ellipsis, nowrap) |
| `.empty-state` | 빈 상태 메시지 |
| `.note` | 주석 텍스트 (--fs-sm, --text-tertiary) |
| `.req` | 필수 표기 `*` (color:--danger) |
| `.text-opt` | 선택 표기 (--fs-xs, --text-tertiary) |
| `.text-accent` | 강조 색 (color:--accent) |
| `.text-danger` | 위험 색 (color:--danger) |
| `.text-secondary` | 보조 색 (color:--text-secondary) |
| `.val-accent` | 수치 강조 색 |
| `.accent-link` | 링크형 강조 (--accent, cursor:pointer) |
| `.mb-2/5/6/9` | margin-bottom (--sp-2/5/6/9) |
| `.mt-3/4` | margin-top (--sp-3/4) |
| `.p-0` | padding:0 |
| `.text-muted` | 흐린 보조 텍스트 (--fs-sm, --text-tertiary) |
| `.text-sm-secondary` | 작은 보조 텍스트 (--fs-sm, --text-secondary) |
| `.tab-pane` | 탭 콘텐츠 패널 (display:none 기본). `.active` → display:block |
| `.ml-1` | margin-left:--sp-1 |
| `.section-body` | section-card 내 패딩 영역 (--sp-4 --sp-6) |
| `.section-content` | section-card 내 본문 (padding:--sp-6 --sp-7, --fs-body, line-height:1.6) |
| `.stat-row` | 통계 행 (display:flex, align-items:center, gap:--sp-3) |
| `.stat-label` | 통계 레이블 텍스트 (--fs-sm, --text-tertiary) |
| `.chip-bar` | 칩 형태 탭 바 (flex, gap:--sp-2, border-bottom). `.btn.active` → --accent bg |
| `.split-list-row .name` | 분할 목록 항목 이름 (flex:1, --fs-body, --fw-medium) |
| `.split-list-row .cnt` | 분할 목록 항목 카운트 (--fs-xs, --text-tertiary) |
| `.url-chip` | URL 칩 (--fs-xs, --accent fg, --accent-light bg, border-radius:20px) — `.onb` 내에서만 사용 |
| `.onb-actions` | onb 페이지 하단 액션 버튼 행 (flex, gap:--sp-3, margin-top:--sp-9) |
| `.mobile-preview` | 모바일 미리보기 패널 (fixed, bottom:24px right:24px, 280×480px, display:none) |

---

## 11. HTML 파일 구조

```
MY인사_PC_v31.html
├── <head> — my_tokens.css + my_cont.css 링크
├── <body>
│   ├── .layout
│   │   ├── .sidebar (역할 스위처·nav 포함)
│   │   ├── .resizer
│   │   └── .main
│   │       ├── .topbar
│   │       └── .content
│   │           ├── .page[data-page="dashboard"]
│   │           ├── .page[data-page="leave"]
│   │           ├── .page[data-page="cert"]
│   │           ├── .page[data-page="faq"]
│   │           ├── .page[data-page="intro"]
│   │           ├── .page[data-page="onboard"]
│   │           ├── .page[data-page="emp-dashboard"]
│   │           ├── .page[data-page="emp-cert"]
│   │           └── .page[data-page="emp-profile"]
│   ├── [모달] .modal-backdrop × 14개
│   ├── [사이드 패널] .leave-side-overlay + .leave-side-panel
│   ├── .toast-v7#toastV7
│   └── <script> 블록 × N
```

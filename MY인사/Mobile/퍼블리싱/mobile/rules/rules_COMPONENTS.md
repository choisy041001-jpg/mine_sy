# MY인사 모바일 — 컴포넌트 레퍼런스

`myinsa-mobile.css` 기준. 실제 렌더링 확인은 `mobile-style-guide.html`을 열 것.

---

## 목차

- [버튼](#버튼)
- [뱃지·상태](#뱃지상태)
- [탑바](#탑바)
- [탭바](#탭바)
- [바텀시트](#바텀시트)
- [폼 필드](#폼-필드)
- [토글 그룹](#토글-그룹)
- [탭 필터](#탭-필터)
- [폼 칩](#폼-칩)
- [목록 행](#목록-행)
- [홈 카드](#홈-카드)
- [FAB](#fab)
- [알림 행](#알림-행)
- [로그인](#로그인)
- [빈 상태](#빈-상태)
- [결재선](#결재선)
- [게시글 액션시트](#게시글-액션시트)
- [결재선 변경 팝업](#결재선-변경-팝업)

---

## 버튼

기본 버튼. 나란히 배치할 때는 `.bottom-action.flex-gap-8`으로 감싸고 각 버튼에 `.flex-1`을 추가한다.

### 기본 / 프라이머리

```html
<div class="bottom-action flex-gap-8">
  <button class="btn flex-1">기본 버튼</button>
  <button class="btn btn-primary flex-1">신청하기</button>
</div>
```

| 클래스 | 설명 |
|--------|------|
| `.btn` | 기본 아웃라인 버튼 |
| `.btn-primary` | `.btn`에 추가. 파란 그라디언트, 그림자 포함 |
| `.flex-1` | 남은 공간을 균등하게 채움 |
| `.flex-1-4` | flex:1.4. 3버튼 중 강조 버튼에 사용 |
| `.bottom-action` | 화면 하단 고정 버튼 영역 (position:absolute;bottom:64px) |
| `.flex-gap-8` | display:flex;gap:8px. `.bottom-action`과 함께 사용 |

### 전폭 버튼 (바텀시트·폼 하단)

```html
<button class="btn btn-primary btn-full-lg">제출하기</button>
```

### 아이콘 정방형 버튼

```html
<button class="icon-btn-sq">…아이콘…</button>

<!-- 뱃지 닷 포함 -->
<button class="icon-btn-sq-rel">
  …아이콘…
  <span class="badge-dot-abs"></span>
</button>
```

### 링크형 · 해제 버튼

```html
<button class="link-accent-btn">더보기</button>
<button class="dismiss-btn">닫기</button>
```

### FAB (Floating Action Button)

```html
<!-- .app (position:relative) 안에서 absolute 배치 -->
<button class="fab-bottom-right">…SVG…</button>
```

---

## 뱃지·상태

결재 상태, 문서 분류 등에 사용하는 상태 배지.

### 결재 상태 배지

```html
<span class="status-badge wait">진행중</span>
<span class="status-badge done">승인</span>
<span class="status-badge reject">반려</span>
<!-- 소형: .badge-sm 추가 (font-size:10px) -->
<span class="status-badge done badge-sm">발급완료</span>
```

| 클래스 | 색상 | 용도 |
|--------|------|------|
| `.wait` | warning | 진행중, 결재 대기 |
| `.done` | success | 승인, 발급완료, 상신완료 |
| `.reject` | danger | 반려 |
| `.badge-sm` | — | 소형 배지 (font-size:10px). 목록 행 내 사용 |

### 상태 칩 (탑바 오른쪽)

```html
<div class="right status-chip-warning">진행중</div>
<div class="right status-chip-success">승인</div>
<div class="right status-chip-danger">반려</div>
```

---

## 상태 배너

탑바 바로 아래 전폭으로 표시되는 상태 안내 배너. 결재 진행중·승인·반려 3가지 상태에 대응한다.

```html
<div class="status-banner-warning">
  <div class="status-banner-icon-warning">…SVG…</div>
  <div>
    <div class="text-body-warning">결재 진행중</div>
    <div class="note-warning-dark">결재자 김팀장 확인 전입니다</div>
  </div>
</div>
```

| 클래스 | 상태 | 설명 |
|--------|------|------|
| `.status-banner-warning` | warning | 진행중 배너. 배경·하단 구분선 warning 색상 |
| `.status-banner-success` | success | 승인 배너 |
| `.status-banner-danger` | danger | 반려 배너 |
| `.status-banner-icon-*` | — | 36×36 원형 아이콘 래퍼. 상태별 접미사 일치 필요 |

---

## 탑바

화면 상단 네비게이션 바. 루트 화면 기본형과 서브 화면 뒤로가기형 두 패턴이 있다.

### 기본형 (루트 화면)

```html
<div class="topbar">
  <h1>전자결재</h1>
  <div class="sub">총 12건 대기 중</div>
</div>
```

### 뒤로가기형 (서브 화면)

```html
<div class="topbar with-back">
  <button class="back">…SVG…</button>
  <span class="title">연차 신청</span>
  <button class="right primary">제출</button>
</div>
```

| 클래스 | 설명 |
|--------|------|
| `.topbar` | 루트 화면용. h1 + .sub 구조 |
| `.topbar.with-back` | 서브 화면용. flex 레이아웃으로 변환 |
| `.back` | 34×34 뒤로가기 버튼 |
| `.title` | flex:1 화면 제목 |
| `.right` | 오른쪽 액션. 텍스트 버튼 |
| `.right.primary` | 강조 액션 (accent 색상, 배경) |
| `.topbar-board` | `board` 탑바 — 태블릿에서 `min-height:69px` |
| `.topbar-more` | `more` 탑바 — 태블릿에서 `min-height:68px; padding-top:21px` |
| `.topbar-leave` | `leave-approval` 탑바 — 태블릿에서 `min-height:70px; border-bottom:2px solid var(--border)` |

---

## 탭바

화면 하단 고정 내비게이션. `.app`(position:relative) 기준으로 `position:absolute;bottom:0`으로 배치된다.

> **주의**: 탭바 위 콘텐츠 영역에 반드시 `.pb-tabbar`(padding-bottom:80px)를 추가해야 탭바에 가려지지 않는다.

```html
<div class="tabbar">
  <div class="tab active">
    …SVG…
    <span class="label">게시판</span>
  </div>
  <div class="tab">…</div>
  <!-- 탭 순서: 게시판 / 연차 / 영수증 / 증명서 / 더보기 -->
</div>
```

---

## 바텀시트

화면 하단에서 올라오는 오버레이 패널. `.sheet-backdrop`에 `.show` 클래스를 토글해 열고 닫는다.

> `.sheet-backdrop`은 `.app`(position:relative) 기준으로 `position:absolute;inset:0`. `display:flex;align-items:flex-end`로 `.sheet`를 하단 정렬한다.

```html
<!-- .app 안에서 backdrop 배치 -->
<div class="sheet-backdrop" id="mySheet">
  <div class="sheet">
    <div class="sheet-handle"></div>
    <div class="sheet-head">
      <span class="title">제목</span>
      <button class="close">…</button>
    </div>
    <div class="sheet-body">…</div>
    <div class="sheet-foot">…</div>
  </div>
</div>

<!-- JS: 열기 -->
document.getElementById('mySheet').classList.add('show');
```

---

## 폼 필드

입력 폼 구성 요소. `.form-section` 안에 `.form-label` + 인풋을 배치한다.

### 기본 입력 필드

```html
<div class="form-section">
  <label class="form-label">사유 <span class="req">*</span></label>
  <input class="form-input" type="text" placeholder="입력하세요">
  <div class="form-hint">최대 200자</div>
</div>

<!-- select -->
<div class="form-section">
  <label class="form-label">유형</label>
  <select class="form-select"><option>연차</option></select>
</div>

<!-- textarea -->
<div class="form-section no-border-b">
  <label class="form-label">상세 내용</label>
  <textarea class="form-textarea" rows="3" placeholder="입력하세요"></textarea>
</div>
```

### 클릭 가능한 폼 행 (날짜 선택 등)

```html
<div class="form-input form-row-clickable" onclick="openDatetimePicker()">
  <span id="datetimeDisplay" class="c-text">2026.04.24 19:30</span>
  …chevron SVG…
</div>
```

### 읽기 전용 박스

```html
<div class="form-readonly-box">읽기 전용 내용</div>
```

| 클래스 | 설명 |
|--------|------|
| `.form-section` | 폼 항목 래퍼. 하단 border-bottom 포함 |
| `.form-section.no-border-b` | 마지막 항목. 하단 구분선 제거 |
| `.form-label` | 항목 레이블 |
| `.form-input` | 텍스트 입력 필드 |
| `.form-select` | 셀렉트 박스 |
| `.form-textarea` | 여러 줄 입력 |
| `.form-hint` | 입력 필드 아래 힌트 텍스트 |
| `.req` | 필수 항목 표시 `*` (accent 색상) |
| `.form-row-clickable` | display:flex;justify-content:space-between;cursor:pointer |
| `.form-readonly-box` | 배경:var(--bg), 테두리, 읽기 전용 스타일 |

---

## 토글 그룹

상호 배타적 선택지를 그리드형 버튼으로 표시.

```html
<!-- 2열 (기본) -->
<div class="toggle-group">
  <button class="toggle-btn active">오전</button>
  <button class="toggle-btn">오후</button>
</div>

<!-- 3열 -->
<div class="toggle-group toggle-3col">…</div>
```

---

## 탭 필터

상태·카테고리 필터에 사용하는 가로 스크롤 탭 목록. 연차·증명서 화면에서 사용.

```html
<div class="tabs-filter">
  <span class="tab-pill active">전체</span>
  <span class="tab-pill" onclick="filterCertStatus(this,'wait')">진행중</span>
</div>
```

---

## 폼 칩

폼 내 상호 배타적 선택지를 버튼 형태로 표시. 영수증 등록의 사용처·지급처 선택에 사용.  
탭 필터(`.tabs-filter`)와 다른 별개 컴포넌트.

```html
<div class="form-chip-row">
  <button class="form-chip active" onclick="selectChip(this)">직접입력</button>
  <button class="form-chip" onclick="selectChip(this)">거래처</button>
</div>
```

---

## 목록 행

게시판, 메뉴 등 목록형 UI.

### 기본 목록 행

```html
<div class="list">
  <div class="list-row">
    <span class="name">항목 이름</span>
    <svg class="chev" viewBox="0 0 24 24" …>…</svg>
  </div>
</div>
```

### 메뉴 그룹 행 (더보기 화면)

```html
<div class="menu-group">
  <div class="menu-row">
    <span class="label">알림 설정</span>
    <svg class="chev" …>…</svg>
  </div>
  <div class="menu-row">
    <span class="label">언어</span>
    <span class="value">한국어</span>
    <svg class="chev" …>…</svg>
  </div>
</div>
```

---

## 플랫 행

게시판 목록과 이력 목록에 사용되는 고밀도 행 패턴. 기본 `.list-row`보다 정보량이 많고 구분선 기반으로 나열된다.

### 게시글 행

```html
<div class="flat-post-row">
  <div class="u-flex-fill">
    <div class="board-new-title">오늘 점심 같이 드실 분 구해요 🍜<span class="post-new-badge">N</span></div>
    <div class="board-new-meta">자유게시판 · 홍길동</div>
  </div>
  <div class="row-end-8">
    <div class="bbs-new-count">3</div>
    …chevron SVG…
  </div>
</div>
```

### 이력 행

```html
<div class="flat-hist-row">
  <div class="u-flex-fill">
    <div class="item-title">휴가신청서</div>
    <div class="text-sm-sub">2026.05.04 (월) — 종일 1일</div>
    <div class="text-hint">상신 4월 29일 · 결재자 김팀장</div>
  </div>
  <div class="row-end-8">
    <span class="status-badge wait">진행중</span>
    …chevron SVG…
  </div>
</div>
```

| 클래스 | 설명 |
|--------|------|
| `.flat-post-row` | 게시판 글 목록 행. 제목+메타+카운트 구조 |
| `.flat-hist-row` | 이력 목록 행. 제목+날짜+힌트+상태배지 구조 |
| `.no-border-b` | 마지막 행의 하단 구분선 제거 |

---

## 홈 카드

홈 화면의 연차 현황, 활동 내역 등 카드형 컴포넌트.

### 연차 요약 카드

```html
<div class="leave-summary-card">
  <div class="lsc-top">
    <div>
      <div class="lsc-remain-label">잔여 연차</div>
      <div class="lsc-remain-big">12<span>일</span></div>
    </div>
    <div class="lsc-chip-row">
      <div class="lsc-chip"><span>15</span>총</div>
      <div class="lsc-chip"><span>3</span>사용</div>
    </div>
  </div>
  <div class="lsc-bar-wrap">
    <div class="lsc-bar-track"><div class="lsc-bar-fill"></div></div>
    <div class="lsc-bar-labels">
      <span>사용 3일</span><span>잔여 12일 / 총 15일</span>
    </div>
  </div>
</div>
```

### 활동 내역

```html
<div class="section">
  <div class="section-title">업데이트 내역</div>
  <div class="activity">
    <div class="row">
      <div class="marker info"></div>  <!-- .info = 강조 마커 -->
      <div class="body">
        <div class="title fw-600">v5.1.0 — 2026.04.29</div>
        <div class="meta activity-meta">…</div>
      </div>
    </div>
  </div>
</div>
```

---

## FAB

화면 우하단 고정 플로팅 버튼. 게시판은 단순 화면 전환, 연차·비용처리는 `.sfab-actions`로 하위 액션을 제공.

### 단순 FAB (게시판)

```html
<!-- .app 안, position:absolute;right:20px;bottom:96px -->
<div class="fab" onclick="showScreen('compose')">…SVG…</div>
```

### 확장 FAB (연차·비용처리)

```html
<div class="sfab-actions" id="leaveActions">
  <div class="sfab-backdrop" onclick="toggleLeaveFab()"></div>
  <div class="sfab-list">
    <div class="sfab-row" onclick="toggleLeaveFab();showScreen('leave-apply')">
      <div class="sic">…SVG…</div>
      <div class="sbody">
        <div class="st">연차 신청</div>
        <div class="sd">휴가신청서 작성</div>
      </div>
    </div>
  </div>
</div>
```

`.sfab-actions`에 `.show` 클래스를 추가/제거해 열고 닫는다.

---

## 알림 행

알림 화면의 개별 알림 행. 읽지 않은 항목에 `.unread`를 추가한다.

```html
<div class="notif-row unread">
  <div class="notif-icon-wrap" style="background:#eff5fa">…SVG…</div>
  <div class="notif-body">
    <div class="notif-title">연차 신청이 승인되었습니다</div>
    <div class="notif-desc">2025-06-01 연차 1일 · 김부장</div>
    <div class="notif-time">방금 전</div>
  </div>
  <div class="notif-unread-dot"></div>
</div>
```

> `.notif-unread-dot`은 `.unread`일 때만 표시. 읽은 항목에는 포함하지 않아도 된다.

---

## 로그인

앱 진입 로그인 화면. `.login-screen` 전체가 flex 컬럼 중앙 정렬된다.

```html
<div class="login-screen">
  <div class="center-mb32">
    <div class="login-logo">MY</div>
    <div class="heading-xxl-mb4">MY인사</div>
    <div class="text-body-tertiary">중소기업용 통합 HR 솔루션</div>
  </div>
  <div class="flex-col-gap-10-mb14">
    <input class="login-input mb-0" type="text" placeholder="이메일 또는 사번">
    <div class="login-input-wrap mb-0">
      <input class="login-input has-icon mb-0" type="password" placeholder="비밀번호">
      <button class="login-eye">…</button>
    </div>
  </div>
  <label class="login-check">
    <input type="checkbox">
    <span>자동 로그인</span>
  </label>
  <button class="login-btn">로그인</button>
  <div class="login-sub-links">
    <span class="login-sub-link">아이디 찾기</span>
    <div class="login-sub-divider"></div>
    <span class="login-sub-link">비밀번호 변경</span>
  </div>
</div>
```

---

## 빈 상태

데이터가 없거나 보조 안내 텍스트가 필요할 때 사용.

```html
<!-- 푸터·캡션 텍스트 (font-size:11px, 중앙 정렬) -->
<div class="empty-state-note">© 2026 Webcash. MY인사 v5.0.0</div>

<!-- 빈 상태 안내 (font-size:13px, 중앙 정렬) -->
<div class="empty-section-sm">전환 가능한 다른 사업장이 없습니다</div>
```

---

## 결재선

결재 흐름을 표시하는 레이아웃. 화면에 따라 두 가지 패턴이 있다.

### 아바타 열 방식 (휴가 신청·계획·취소)

```html
<div class="form-section no-border-b">
  <label class="form-label">결재선</label>
  <div class="approval-line-wrap">
    <div class="approver-col">
      <div class="approver-avatar-primary">홍</div>
      <div class="text-xs-strong">홍길동</div>
      <div class="text-caption">신청자</div>
    </div>
    <div class="approver-arrow">…chevron SVG…</div>
    <div class="approver-col">
      <div class="approver-avatar">김</div>
      <div class="text-xs-strong">김팀장</div>
      <div class="text-caption">1차 결재</div>
    </div>
  </div>
  <div class="approval-hint">
    <span class="row-gap-5">…info SVG… 결재선은 경리나라를 기준으로 자동 지정됩니다</span>
    <span class="approval-change-link" onclick="openApprovalLinePopup()">결재선 변경</span>
  </div>
</div>
```

### 콤팩트 행 방식 (비용처리 — 결재자 3명 이상)

```html
<div class="form-section no-border-b">
  <div class="row-between-mb6">
    <label class="form-label mb0">결재선 <span class="fw-400-tertiary">(경리나라 자동 지정)</span></label>
    <span class="approval-change-link" onclick="openApprovalLinePopup()">결재선 변경</span>
  </div>
  <div class="approval-compact-row">
    <span class="text-xs-muted">신청자</span>
    <span class="fw-500">홍길동</span>
    …chevron SVG…
    <span class="fw-500">김팀장</span>
    …chevron SVG…
    <span class="fw-500">이대표</span>
  </div>
</div>
```

| 클래스 | 설명 |
|--------|------|
| `.approval-line-wrap` | 아바타 열 방식 컨테이너 |
| `.approver-col` | 아바타 + 이름 + 역할 수직 열 |
| `.approver-avatar-primary` | 신청자 아바타 (accent 배경) |
| `.approver-avatar` | 결재자 아바타 (gray 배경) |
| `.approver-arrow` | 화살표 구분자 |
| `.approval-hint` | 힌트 텍스트 + 변경 링크 행 |
| `.approval-change-link` | "결재선 변경" 링크 스타일 |
| `.approval-compact-row` | 콤팩트 행 방식 컨테이너 |
| `.text-xs-muted` | 11px, var(--text-tertiary) |

---

## 게시글 액션시트

게시글 더보기(⋯) 버튼으로 여는 액션시트.  
`#postActionSheet`에 `style="display:none"`을 두고 JS로 `el.style.display='flex'`로 열고 닫는다.  
position/background/z-index는 CSS ID 셀렉터(`#postActionSheet`)가 담당.

```html
<div id="postActionSheet" style="display:none" onclick="closePostActionSheet()">
  <div class="action-sheet-body" onclick="event.stopPropagation()">
    <div class="action-sheet-handle"></div>
    <div class="action-sheet-item" onclick="…">수정하기</div>
    <div class="action-sheet-item-danger" onclick="…">삭제하기</div>
    <div class="action-sheet-cancel" onclick="closePostActionSheet()">취소</div>
  </div>
</div>

<!-- JS: 열기 -->
document.getElementById('postActionSheet').style.display = 'flex';
<!-- JS: 닫기 -->
document.getElementById('postActionSheet').style.display = 'none';
```

| 클래스 | 설명 |
|--------|------|
| `.action-sheet-body` | 하단 올라오는 흰 패널. `border-radius:20px 20px 0 0` |
| `.action-sheet-handle` | 상단 손잡이 바 |
| `.action-sheet-item` | 일반 액션 행 (font-size:15px) |
| `.action-sheet-item-danger` | 위험 액션 행 (danger 색상) |
| `.action-sheet-cancel` | 취소 행 (text-secondary, 중앙 정렬) |

---

## 결재선 변경 팝업

경리나라에 등록된 결재선 중 하나를 선택하는 모달 팝업.  
`#approvalLinePopup`에 `style="display:none"`을 두고 JS로 열고 닫는다.  
내부 컨테이너는 기존 `.result-overlay-sm` 재활용.

```html
<div id="approvalLinePopup" style="display:none">
  <div class="result-overlay-sm">
    <div class="popup-header">
      <div class="label-caps-alt">결재선 선택</div>
      <div class="heading-alt">결재선을 선택하세요</div>
      <div class="text-sm-sub-mt4">경리나라에 등록된 결재선 중 하나를 선택하세요</div>
    </div>
    <div class="popup-list">
      <label class="alp-option active" onclick="selectApprovalLine(this)">
        <input type="radio" name="approvalLine" checked class="alp-option-radio">
        <div>
          <div class="text-body-strong">기본 결재선</div>
          <div class="text-hint">홍길동 → 김팀장</div>
        </div>
      </label>
      <label class="alp-option" onclick="selectApprovalLine(this)">
        <input type="radio" name="approvalLine" class="alp-option-radio">
        <div>
          <div class="text-body-strong">임원 결재선</div>
          <div class="text-hint">홍길동 → 김팀장 → 이대표</div>
        </div>
      </label>
    </div>
    <div class="alp-footer">
      <button onclick="closeApprovalLinePopup()" class="alp-btn-cancel">취소</button>
      <button onclick="closeApprovalLinePopup();showToast('✅ 결재선이 변경되었습니다')" class="alp-btn-confirm">확인</button>
    </div>
  </div>
</div>
```

| 클래스 | 설명 |
|--------|------|
| `.alp-option` | 결재선 선택지 행 |
| `.alp-option.active` | 선택된 옵션 (accent-light 배경) |
| `.alp-option-radio` | `accent-color:var(--accent)` 라디오 버튼 |
| `.alp-footer` | 하단 취소/확인 버튼 영역 |
| `.alp-btn-cancel` | 취소 버튼 (bg:var(--bg), 테두리) |
| `.alp-btn-confirm` | 확인 버튼 (bg:var(--accent), 흰 글씨) |

---

## 댓글 (post-detail)

댓글 목록은 `_renderComments()`가 `#postDetailComments`에 동적으로 주입한다.  
댓글 수정 중에는 `_editingCommentIdx` 인덱스 행에만 `<textarea>`가 렌더링된다.

| 클래스 | 설명 |
|--------|------|
| `.comments` | 댓글 영역 전체 컨테이너. 상단 `<h3>` 헤더 포함. `padding:0 20px 20px` |
| `.comment` | 댓글 단일 행. 하단 구분선(`border-bottom:1px solid var(--border-light)`). 자식: `.who` / `.text` / `.when` |
| `.comment-input` | 댓글 입력 바. `position:absolute;bottom:64px` 하단 고정. 입력창 + 전송 버튼 포함 |
| `.comment-who-row` | 작성자 이름 + 더보기(···) 버튼 가로 배치. `display:flex;justify-content:space-between` |
| `.comment-edit-textarea` | 댓글 수정용 `<textarea>`. 최대 2줄(66px), `resize:none`, 둥근 테두리 |
| `.comment-edit-actions` | 댓글 인라인 수정 시 취소/저장 버튼 묶음 래퍼. `display:flex;gap:8px;margin-top:8px`. 내부 버튼(`height:34px;font-size:13px`)은 `.pf-btn-cancel` / `.pf-btn-primary` 재사용 |

---

## 글쓰기 (compose)

`compose` 화면 전용 컴포넌트. `_composeMode('create'|'edit')` 에 따라 타이틀·버튼 텍스트가 달라진다.

| 클래스 | 설명 |
|--------|------|
| `.compose` | 글쓰기 화면 루트 컨테이너. `padding:0;background:#ffffff` |
| `.compose-nav` | 상단 네비게이션 바. 취소 / 타이틀 / 등록 버튼 포함. `height:52px` |
| `.compose-cancel-btn` | 취소 버튼 (좌측, text-secondary, border 없음) |
| `.compose-title-span` | 화면 타이틀 텍스트 (`id="composeTitleSpan"`로 JS 교체) |
| `.compose-submit-btn` | 등록/저장 버튼 (우측, 기본 비활성, `checkComposeReady()`로 색상 전환) |
| `.compose-row` | 게시판 선택·항목 입력 행. `height:50px`, 하단 border |
| `.compose-label` | 행 레이블 (44px 고정 너비, text-secondary) |
| `.compose-select-wrap` | 게시판 드롭다운 감싸는 relative 컨테이너 |
| `.compose-select` | 게시판 `<select>` (`appearance:none`, 100% 너비) |
| `.compose-select-arrow` | 드롭다운 화살표 아이콘 (absolute, `pointer-events:none`) |
| `.compose-title-wrap` | 제목 입력 행 영역 (`padding:0 16px`) |
| `.compose-title-input` | 제목 입력 필드 (border 없음, `height:52px`) |
| `.compose-body-input` | 본문 입력 필드 (border 없음, `height:260px`, `resize:none`) |
| `.compose-toolbar` | 하단 툴바 (border-top, 첨부 버튼 포함) |
| `.compose-attach-btn` | 첨부 버튼 (pill 형태, border, text-secondary) |

---

## 비밀번호 찾기 (pw-find)

아이디 찾기 / 비밀번호 변경 화면. `pfShowPanel(name)`으로 패널 전환.  
패널 ID: `pfPanel_id`, `pfPanel_pw`, `pfPanel_newpw`, `pfPanel_result`

| 클래스 | 설명 |
|--------|------|
| `.pf-tab-bar` | 아이디찾기 / 비밀번호변경 상단 탭 바 |
| `.pf-tab-active` | 선택된 탭 (white bg, accent 글씨) |
| `.pf-tab-inactive` | 비선택 탭 (transparent bg, text-tertiary) |
| `.pf-input-group` | 입력 필드 묶음 (border, `border-radius:4px`) |
| `.pf-input` | 일반 입력 행 (`height:48px`, 하단 border) |
| `.pf-input-last` | 마지막 입력 행 (하단 border 없음) |
| `.pf-btn-row` | 버튼 행 (`display:flex`, `gap:10px`) |
| `.pf-btn-primary` | 주요 버튼 (accent bg, 흰 글씨) |
| `.pf-btn-cancel` | 보조 버튼 (btn-muted-bg, text-secondary) |
| `.pf-phone-row` | 전화번호 입력 행 (국가코드 + 번호 + 전송 버튼 flex 배치) |
| `.pf-country-code` | 국가 코드 선택 영역 (`min-width:80px`, `border-right`) |
| `.pf-input-flex` | 플렉스 입력 필드 (border 없음, `flex:1`) |
| `.pf-input-flex-sm` | 좁은 플렉스 입력 필드 (`padding:0 12px`) |
| `.pf-send-btn` | 전송 버튼 (accent bg, `flex-shrink:0`) |
| `.pf-verify-btn` | 인증 버튼 (accent bg) |
| `.pf-captcha-wrap` | 캡차 영역 (dark gradient bg, `height:64px`) |
| `.pf-captcha-inner` | 캡차 내부 flex 컨테이너 |
| `.pf-captcha-text` | 캡차 텍스트 (monospace, 비틀림 효과) |
| `.pf-captcha-lines` | 캡차 배경 선 레이어 |
| `.pf-captcha-refresh` | 캡차 새로고침 버튼 |
| `.pf-result-overlay` | 결과 팝업 패널 (white bg, rounded, shadow) |
| `.pf-error-ul` | 에러 목록 `<ul>` (`margin-top:10px`, 들여쓰기) |
| `.pf-alert-msg` | 알림 팝업 메시지 (`text-align:center`, `line-height:1.6`) |
| `.pf-alert-overlay-bg` | 알림 팝업 배경 딤 (`rgba(0,0,0,.5)`, `z-index:400`) |

---

## 전자결재 토글 (expense-form)

| 클래스 | 설명 |
|--------|------|
| `.btn-eapproval-toggle` | 전자결재 ON/OFF 토글 버튼. `border:1px solid var(--border)` |

---

## 영수증 이미지 · 이미지 뷰어

| 클래스 | 설명 |
|--------|------|
| `.receipt-img-wrap` | 영수증 썸네일 컨테이너. `max-width:240px`, border, `border-radius:10px`, `cursor:pointer` |
| `.receipt-img-center` | 영수증 이미지 중앙 정렬 래퍼 (`padding:6px 0 10px`) |
| `.receipt-img-center-sm` | 작은 영수증 이미지 중앙 정렬 래퍼 (`padding:4px 0`) |
| `.imgview-img` | 이미지 뷰어 내 확대 이미지. `width:calc(100vw - 72px)`, `max-height:78vh` |
| `.imgview-close-btn` | 이미지 뷰어 닫기 버튼 (우상단 circle, rgba 반투명 배경) |
| `.imgview-save-btn` | 이미지 뷰어 저장 버튼 (우하단 pill, white bg) |

---

## 이미지 선택 팝업 (imgPickModal)

영수증 이미지 추가 방식(카메라 / 앨범)을 선택하는 바텀시트 팝업.

| 클래스 | 설명 |
|--------|------|
| `.img-pick-modal` | 팝업 루트 컨테이너 (fixed, full-screen dim, `display:none` → flex + fadeIn) |
| `.img-pick-box` | 흰 바텀시트 패널 (border-radius 상단, 하단 padding) |
| `.img-pick-handle` | 손잡이 바 (`width:36px`, `height:4px`, 둥근 모서리) |
| `.img-pick-title` | 팝업 타이틀 (`font-size:16px`, `font-weight:800`) |
| `.img-pick-sub` | 부제 (text-tertiary, 하단 padding) |
| `.img-pick-btns` | 2-컬럼 그리드 버튼 영역 |
| `.img-pick-btn` | 각 선택 버튼 (border, rounded, 내부 아이콘+텍스트) |
| `.img-pick-cancel` | 취소 버튼 (전체 너비, 하단) |

---

## JS 함수 레퍼런스

> `index_m.html` `<script>` 기준. 새 함수 추가 시 이 목록도 함께 업데이트할 것.  
> 총 **133개** 함수 (미호출 12개 포함). *신규 기능 함수 33개 추가 (2026.06 — 댓글·글쓰기·비밀번호찾기·전자결재·영수증·헬퍼)*

### 화면 전환

| 함수 | 파라미터 | 용도 |
|------|---------|------|
| `showScreen` | `name, _skipHistory` | 지정 화면을 active로 전환. 히스토리 스택 관리·탭 동기화·분할 클래스 초기화 포함 |
| `goBack` | — | 히스토리 스택에서 이전 화면으로 복귀. `has-faq-detail` 상태는 `has-faq` 복원으로 우회 |
| `goTab` | `name` | 하단 탭 클릭 시 탭 active 전환 및 TAB_SCREEN 매핑에 따라 루트 화면으로 이동 |
| `syncTab` | `screenName` | 현재 화면명에 맞는 탭 버튼을 active로 동기화 |
| `goPostList` | `boardName, isFaqBoard, newCount` | 게시글 목록 화면으로 이동. FAQ/일반 구분, NEW 뱃지, 태블릿 분할 처리 포함 |
| `openPost` | `postId` | 게시글 상세 렌더링 후 post-detail 이동. 태블릿 `has-faq` 상태면 `has-faq-detail`로 전환 |
| `openPostFromBoard` | `postId, boardName, newCount` | 게시판 메인에서 게시글 클릭. 태블릿이면 `has-detail` 추가, 모바일이면 화면 전환 |
| `openPostEdit` | — | 현재 게시글 데이터를 수정 폼에 채우고 `post-edit` 화면으로 이동 |
| `showLeaveDetail` | `status` | 연차 상태(wait/done/reject)에 따라 해당 상세 화면으로 이동 |
| `goPwFind` | `tab` | 로그인에서 아이디찾기/비밀번호변경 링크 클릭 시 `pw-find` 화면으로 이동하고 탭 초기화 |
| `handleNotifClick` | `screen` | 알림 항목 클릭 시 해당 화면으로 이동 |
| `selectWorkplace` | `id` | 사업장 선택 완료 시 프로필 업데이트 후 게시판으로 이동 |
| `switchToWorkplace` | `id` | 사업장 전환 팝업에서 다른 사업장 선택 시 프로필 업데이트 및 토스트 표시 |
| `openComposeCreate` | — | FAB 클릭 시 `_composeMode='create'`·`_editPostId=null`로 초기화하고 `compose` 화면으로 이동 |

### 오버레이 제어

| 함수 | 파라미터 | 용도 |
|------|---------|------|
| `openBoardFilterSheet` / `closeBoardFilterSheet` | — | 게시판 필터 바텀시트 표시/숨김 |
| `openCertModal` / `closeCertModal` | `type` / — | 증명서 신청 팝업 열기/닫기. 종류별 가이드·입력 필드 초기화 포함 |
| `submitCertModal` | — | 증명서 신청 모달 제출. 유효성 검사 후 `cert-status-detail`로 이동 |
| `openEmailSheet` / `closeEmailSheet` | — | 이메일 전송 바텀시트 표시/숨김 |
| `openImgView` / `closeImgView` | — | 첨부 이미지 확대 오버레이 표시/숨김 |
| `openDeleteConfirm` / `closeDeleteConfirm` | — | 영수증 삭제 확인 다이얼로그 표시/숨김 |
| `openPostActionSheet` / `closePostActionSheet` | — | 게시글 더보기 액션시트 표시/숨김 |
| `openPostDeleteConfirm` / `closePostDeleteConfirm` | — | 게시글 삭제 확인 다이얼로그 표시/숨김 |
| `openApprovalLinePopup` / `closeApprovalLinePopup` | — | 결재선 변경 팝업 표시/숨김 |
| `openDatetimePicker` / `closeDatetimePicker` | — | 사용일시 선택 팝업 열기/닫기. 캘린더·시계 초기화 포함 |
| `confirmDatetimePicker` | — | 선택한 날짜·시간을 포맷하여 화면 필드에 반영하고 팝업 닫기 |
| `openImgPickModal` / `closeImgPickModal` | — | 영수증 이미지 선택 팝업 표시/숨김 |
| `openWorkplacePopup` / `closeWorkplacePopup` | — | 로그인 후 사업장 선택 팝업 빌드·표시/숨김 |
| `openSwitchWorkplacePopup` / `closeSwitchWorkplacePopup` | — | 더보기 사업장 전환 팝업 빌드·표시/숨김 |
| `closeSheet` | `name` | 지정 이름의 `.sheet-backdrop`을 `.show` 제거로 닫기 |
| `openCommentActions` / `closeCommentActionSheet` | `idx` / — | 내 댓글 더보기(···) 클릭 시 `commentActionSheet` 표시. 대상 인덱스를 `_commentActionIdx`에 저장 / 숨김 |
| `openCommentDeleteConfirm` / `closeCommentDeleteConfirm` | — | 댓글 삭제 확인 오버레이 표시 / 숨김 |
| `openComposeAttachSheet` / `closeComposeAttachSheet` | — | 글쓰기 첨부 방식 선택 바텀시트 표시 / 숨김 |
| `closeComposeCancelConfirm` | — | 글쓰기 취소 확인 오버레이 닫기 |
| `showPfAlert` / `pfAlertConfirm` | `msg, onConfirm` / — | 비밀번호 찾기 전용 안내 팝업 표시·콜백 저장 / 팝업 닫기 및 콜백 실행 |

### 폼·입력 처리

| 함수 | 파라미터 | 용도 |
|------|---------|------|
| `selectBoardFilter` | `el` | 게시판 필터 선택 후 해당 게시글만 표시하고 시트 닫기 |
| `onSubmitPost` | — | 글 작성 폼 제출 시 유효성 검사 후 등록 처리 및 `board`로 이동 |
| `checkComposeReady` | — | 제목·내용 입력 여부에 따라 등록 버튼 활성/비활성 업데이트 (input 이벤트) |
| `selectToggle` | `el` | 토글 버튼 그룹에서 클릭된 버튼만 active 선택 |
| `selectPayType` | `num` | 지급수단 카드 선택 시 해당 카드만 active 스타일 적용 |
| `filterCertStatus` | `el, status` | 증명서 이력 탭 선택 시 해당 상태 항목만 표시 |
| `enterSelectMode` / `exitSelectMode` | — | 영수증 선택 모드 진입/종료. 체크박스·하단 버튼·FAB 위치 제어 |
| `updateSelectCount` | — | 선택된 체크박스 수 집계 및 결의서 작성 버튼 텍스트·활성화 업데이트 |
| `handleReceiptClick` | `event, id` | 선택 모드 여부에 따라 체크박스 토글 또는 상세 이동 분기 |
| `submitSelectedReceipts` | — | 선택 영수증으로 `expense-form`으로 이동하고 선택 모드 종료 |
| `onUserSearch` / `selectUser` / `closeUserDropdown` | `query` / `name, dept, role` / — | 직원 검색 드롭다운 제어 |
| `selectPlaceChip` / `selectPayeeChip` | `type` | 사용처·지급처 칩 선택 시 해당 입력 영역만 표시 |
| `onBizSearch` / `selectBiz` / `closeBizDropdown` | `query` / `name, bizNo` / — | 거래처 검색 드롭다운 제어 |
| `selectApprovalLine` | `label` | 결재선 팝업 라디오 선택 시 accent 배경 강조 |
| `toggleLoginPw` | — | 로그인 비밀번호 표시/숨기기 토글 및 눈 아이콘 전환 |
| `calNav` | `calId, delta` | 캘린더 이전/다음 월 이동 |
| `calSelect` | `el, calId, startId, endId` | 캘린더 날짜 단일 클릭 선택 처리 |
| `calMultiSelect` | `el, calId` | 사용계획서 캘린더 날짜 토글 선택 처리 |
| `planMonthNav` | `delta` | 연차사용계획서 월 네비게이션 |
| `switchPfTab` | `mode, tab` | 아이디찾기/비밀번호변경 서브 탭 전환 |
| `pfFindId` / `pfChangePw` | — | 아이디 찾기·비밀번호 변경 버튼 처리 |
| `dtSelectDay` | `d` | 날짜 피커 날짜 선택 |
| `dtPrevMonth` / `dtNextMonth` | — | 날짜 피커 월 이동 |
| `dtSwitchToHour` / `dtSwitchToMin` | — | 시간 피커 시·분 모드 전환 |
| `onCommentInput` | — | 댓글 입력 시 `commentSubmitBtn` 활성화 상태 업데이트 |
| `addComment` | — | 댓글 등록. `_nowYMDHM()` 타임스탬프와 함께 `_POST_DETAIL` 댓글 배열에 추가 후 재렌더링 |
| `startEditComment` | — | 댓글 수정 모드 진입. `_editingCommentIdx = _commentActionIdx` 설정 후 댓글 재렌더링 |
| `cancelCommentEdit` | — | 댓글 수정 모드 취소. `_editingCommentIdx = -1` 후 재렌더링 |
| `saveCommentEdit` | — | `#commentEditInput` 값을 `_POST_DETAIL` 댓글 배열에 반영 후 재렌더링 |
| `confirmDeleteComment` | — | `_commentDeleteIdx` 댓글 삭제 실행 후 확인 오버레이 닫기 및 재렌더링 |
| `onComposeCancel` | — | 글쓰기 취소 클릭. 제목·내용 dirty 여부에 따라 확인 오버레이 표시 또는 `goBack()` 직접 호출 |
| `confirmComposeCancel` | — | 취소 확정. 폼 초기화 + `_composeMode='create'` + `_editPostId=null` + `goBack()` |
| `pfShowPanel` | `name` | 비밀번호 찾기 패널 전환 (`id`/`pw`/`newpw`/`result`). 상단 타이틀도 업데이트 |
| `pfValidate` | `mode` | 현재 탭의 필수 항목을 순서대로 검사. 첫 미입력 항목 `showPfAlert`로 안내 후 `false` 반환 |
| `pfSubmitNewPw` | — | 새 비밀번호 설정. 두 필드 일치 검사 후 완료 알림 및 로그인 화면으로 이동 |
| `toggleEapproval` | — | `_eapprovalOn` 플래그 반전 후 `applyEapprovalState()` 호출 |
| `applyEapprovalState` | — | `_eapprovalOn` 상태에 따라 타이틀·제출버튼 텍스트, 결재선 섹션 표시, 토글 버튼 텍스트 업데이트 |
| `submitExpense` | — | `_eapprovalOn` 상태에 따라 상신/저장 토스트 표시 후 `expense-hub`로 이동 |
| `openReceiptCreate` | — | `_receiptMode='create'` 설정, 타이틀·버튼 텍스트를 '등록'으로 변경 후 `receipt-form`으로 이동 |
| `openReceiptEdit` | — | `_receiptMode='edit'` 설정, 타이틀·버튼 텍스트를 '수정'으로 변경 후 `receipt-form`으로 이동 |
| `onSubmitReceipt` | — | 영수증 제출. 사용일시·금액·사용처 유효성 검사(`_josaIGa` 메시지) 후 `expense-hub`로 이동 |

### 데이터 렌더링

| 함수 | 파라미터 | 용도 |
|------|---------|------|
| `_renderPostDetail` | `postId, boardName` | 게시글 데이터 조회 후 상세 본문·댓글 영역 HTML 렌더링 |
| `showReceiptDetail` | `id` | 영수증 ID로 데이터 조회 후 상세 화면 필드 채우기 및 `receipt-detail` 이동 |
| `toggleFAQ` | `el` | FAQ 항목 클릭 시 펼침/닫힘 토글 (단일 오픈) |
| `dtRenderCal` | — | 날짜 피커 캘린더 그리드를 현재 연/월로 재렌더링 |
| `dtRenderClock` | — | 시/분 모드에 맞게 아날로그 시계 SVG 렌더링 |
| `dtUpdateClockHand` | — | 현재 시간 모드에 따라 SVG 시계 바늘 끝 좌표 계산·업데이트 |
| `dtSetHour` / `dtSetMin` | `h` / `m` | 시계에서 시·분 선택 처리 및 모드 전환 |
| `dtUpdateTabs` | — | 시/분 탭 버튼 강조 스타일과 안내 레이블 업데이트 |
| `dtUpdateDisplay` | — | 선택 날짜·시:분을 피커 상단 표시 텍스트에 반영 |
| `buildWorkplaceItem` | `wp, onClick, showBadge` | 사업장 선택/전환 팝업 카드 HTML 생성 반환 |
| `updateMoreProfile` | — | 더보기 화면 상단 회사명·직급/부서 텍스트를 현재 사업장 데이터로 업데이트 |
| `renderPanel` | `screenId` | *(no-op — 패널 제거 후 잔재. showScreen에서 호출만 됨)* no-op, 정리 예정 |
| `_renderComments` | — | 현재 게시글(`_currentPostId`) 댓글 목록 HTML 렌더링. 내 댓글 행에 수정·삭제 더보기(···) 버튼 표시 |

### 유틸리티

| 함수 | 파라미터 | 용도 |
|------|---------|------|
| `showToast` | `msg` | 화면 하단 토스트 메시지 2.2초간 표시 |
| `markAllNotifRead` | — | 모든 알림 항목 읽음 처리 및 N 뱃지 숨김 |
| `toggleNotifSwitch` | `id` | 알림 설정 토글 상태 반전. 전체 알림 끔 시 하위 항목 dim 처리 |
| `toggleDNDTime` | — | 방해금지 시간 토글 상태에 따라 시간 설정 섹션 표시/숨김 |
| `toggleLeaveFab` | — | 연차 화면 FAB 하위 액션 메뉴 토글 |
| `toggleExpenseFab` | — | 영수증 화면 FAB 하위 액션 메뉴 토글 |
| `updateFabPosition` | `screenEl` | 화면 내 하단 액션 바 높이에 따라 FAB 위치 동적 조정 |
| `_paintRange` | `calId, fromEl, toEl` | 캘린더 두 셀 사이 범위를 selected/range 클래스로 하이라이트 |
| `_updateInputs` | `calId, startId, endId` | 드래그 선택 완료 후 날짜 입력 필드와 선택 레이블 업데이트 |
| `_bindCalDrag` | `calId, startId, endId` | 특정 캘린더에 마우스·터치 드래그 이벤트 등록 |
| `initAllCalDrags` | — | 앱 초기 로드 시 3개 캘린더 드래그 이벤트 일괄 등록 (DOMContentLoaded) |
| `_dayNum` | `el` | 날짜 셀에서 숫자 추출 반환. muted 셀은 null |
| `_escapeHtml` | `s` | HTML 특수문자(`&`, `<`, `>`) 이스케이프. 댓글 렌더링 시 XSS 방지 |
| `_josaIGa` | `word` | 단어 받침 유무에 따라 조사 `'가'` 또는 `'이'` 반환. 유효성 메시지 생성 시 사용 |
| `_nowYMDHM` | — | 현재 시각을 `YYYY.MM.DD HH:MM` 형식으로 반환. 댓글 등록 시각 기록용 |
| `_halfDayActive` | `calId` | 캘린더 ID로 연결된 화면의 반차 토글 활성 여부 반환. `_bindCalDrag` 범위 드래그 차단 조건 |
| `_pfVal` | `id` | ID로 요소를 찾아 `.value` 반환. 비밀번호 찾기 내부 입력 값 참조 헬퍼 |

### 미호출 함수

| 함수 | 파라미터 | 비고 |
|------|---------|------|
| `goCertForm` | `type` | `cert-form` 화면 기획 보류 — onclick 미연결 |
| `filterFAQ` | `el, cat` | `faq` 화면 기획 보류 |
| `switchFaqTab` | `el, tab` | `faq` 화면 기획 보류 |
| `filterFaqCommon` | `el, cat` | `faq` 화면 기획 보류 |
| `openSheet` | `name` | 열기는 화면별 전용 함수로 분산 — `closeSheet`와 의도적 비대칭 |
| `selectCancelItem` | `el` | 연차 취소 라디오 선택 UI 미완성 |
| `_calDayIndex` | `grid, el` | 캘린더 그리드 헬퍼. 내부에서도 미사용 |
| `renderFlowDiagram` | `screenId` | 패널 제거 후 잔재 (no-op) |
| `toggleHomeActions` | — | 홈 화면 FAB 미구현 |
| `switchPwFindMainTab` | `tab` | `switchPfTab`으로 대체된 것으로 추정 |
| `pfSetEmailDomain` | `mode, val` | 이메일 도메인 드롭다운 미구현 |
| `filterExpenseTab` | `el, status` | 영수증 탭 필터 UI 미구현 |

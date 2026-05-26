# MY인사 PC — 레이아웃 규칙

> 기준 파일: `MY인사_PC_v31.html`  
> 경리나라(common.css 기반)와 완전히 다른 자체 인라인 CSS 시스템 — 혼용 금지.

---

## 1. 디자인 토큰 (CSS 변수)

```css
:root {
  /* 색상 */
  --bg: #f7f7fa;
  --surface: #ffffff;
  --surface-hover: #f2f2f7;
  --text: #111118;
  --text-secondary: #4a4a5a;
  --text-tertiary: #9898a8;
  --border: #e8e8ef;
  --border-strong: #d0d0dc;

  /* 액센트 (파란 계열) */
  --accent: #2363a8;
  --accent-light: #eff5fa;
  --accent-mid: #558ec3;
  --accent-fg: #ffffff;

  /* 상태 색상 */
  --success: #16a34a;      --success-bg: #f0fdf4;
  --warning: #d97706;      --warning-bg: #fffbeb;
  --danger: #dc2626;       --danger-bg: #fef2f2;
  --info: #2563eb;         --info-bg: #eff6ff;

  /* 그림자 */
  --shadow-sm: 0 1px 3px rgba(0,0,0,.06), 0 1px 2px rgba(0,0,0,.04);
  --shadow-md: 0 4px 12px rgba(35,99,168,.08), 0 2px 4px rgba(0,0,0,.04);
  --shadow-card: 0 2px 8px rgba(0,0,0,.06), 0 0 0 1px rgba(0,0,0,.04);

  /* 반지름 */
  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 10px;
  --radius-xl: 14px;

  /* 레이아웃 */
  --sidebar-w: 240px;
}
```

---

## 2. HTML 파일 기본 골격 템플릿

새 화면 파일 생성 시 아래 구조를 그대로 복사해 시작한다. `<head>` 구조·경로·속성을 임의로 변경하지 않는다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MY인사 — 페이지명</title>
<link rel="stylesheet" href="my_cont.css">
</head>
<body>

<div class="layout">
  <!-- SIDEBAR, RESIZER, MAIN 순서 유지 -->
</div>

<!-- ★ 모달·사이드패널은 반드시 여기(</body> 직전)에 선언 -->

</body>
</html>
```

> **고정 규칙**
> - `lang="ko"` / `charset="UTF-8"` 변경 금지
> - CSS 링크는 `my_cont.css` 단일 파일, 경로 변경 금지
> - 폰트 별도 `<link>` 불필요 — `@font-face` 는 `my_cont.css` 내부에 선언됨

---

## 3. 전체 레이아웃 골격

```
┌──────────────┬──┬───────────────────────────────────┐
│   .sidebar   │  │            .main                  │
│  (240px)     │R │  .topbar (58px)                   │
│              │E │───────────────────────────────────│
│              │S │                                   │
│              │I │  .content (flex:1, overflow-y:auto│
│              │Z │  padding: 24px)                   │
│              │E │                                   │
│              │R │                                   │
└──────────────┴──┴───────────────────────────────────┘
```

```html
<body>
<div class="layout">            <!-- display:flex; height:100vh -->

  <aside class="sidebar">
    <!-- 브랜드 / 프로필 스위처 / nav / 하단 고정 메뉴 -->
  </aside>

  <div class="resizer" id="resizer"></div>   <!-- 드래그 리사이즈 핸들 -->

  <main class="main">           <!-- flex:1; display:flex; flex-direction:column -->
    <header class="topbar">...</header>
    <div class="content">
      <!-- .page 들이 여기에 위치 -->
    </div>
  </main>

</div>
</body>
```

- **폰트**: `'Pretendard', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`
- **기본 폰트 크기**: `14px`
- `html, body`: `height:100vh; overflow:hidden`

### 웹폰트 로딩 구조

폰트 파일은 `fonts_Pretendard/` 폴더에 위치. `my_cont.css` 최상단에서 `@font-face` 선언.

| weight | 파일명 | 포맷 |
|--------|--------|------|
| 100 | Pretendard-Thin | woff |
| 200 | Pretendard-ExtraLight | woff |
| 300 | Pretendard-Light | woff |
| 400 | Pretendard-Regular | woff2 + woff |
| 500 | Pretendard-Medium | woff2 + woff |
| 600 | Pretendard-SemiBold | woff2 + woff |
| 700 | Pretendard-Bold | woff2 + woff |
| 800 | Pretendard-ExtraBold | woff2 + woff |
| 900 | Pretendard-Black | woff |

- `font-display:swap` 적용 — 로딩 전 시스템 폰트로 fallback, 로드 완료 후 교체
- `rel="preload"` 는 HTML 전용 힌트로 CSS 대체 불가 — 이 프로젝트는 내부 도구이므로 생략 (유지보수 부담 > 50~100ms 이득)
- HTML에는 `<link rel="stylesheet" href="my_cont.css">` 한 줄만 추가하면 됨
- CDN 의존 없음 — 오프라인·인트라넷 환경에서도 폰트 정상 표시

---

## 4. 사이드바 (.sidebar) 구조

사이드바 내부는 위→아래 순서로 4개 블록:

```
.brand            ← 로고 + 앱명
.sb-profile       ← 역할 스위처 (드롭다운 포함)
.nav              ← 메뉴 그룹들 (flex:1, overflow-y:auto)
#navBottom        ← 하단 고정 메뉴 (관리자 모드에서만 표시)
```

### 3-1. 브랜드 (.brand)

```html
<div class="brand">
  <div class="logo">MY</div>
  <div><div class="name">MY인사</div></div>
</div>
```

- `.logo`: 34×34px, `border-radius:10px`, accent 그라디언트 배경, 흰 글자
- `.name`: `font-size:15px`, `font-weight:800`

### 3-2. 역할 스위처 (.sb-profile)

```html
<div class="sb-profile" id="sbProfile" onclick="toggleSbDropdown()">
  <div class="sbp-avatar" id="sbpAvatar">한</div>
  <div class="sbp-info">
    <div class="sbp-name" id="sbpName">한관리자</div>
    <div class="sbp-role" id="sbpRole">인사팀 · 팀장 · 관리자</div>
  </div>
  <svg class="sbp-arrow" id="sbpArrow" ...>...</svg>

  <!-- 드롭다운 (position:absolute, top:calc(100%+4px)) -->
  <div class="sbp-dropdown" id="sbpDropdown" onclick="event.stopPropagation()">
    <div class="sbp-option selected" id="sbpOptAdmin" onclick="switchRole('admin')">
      <div class="sbo-avatar">한</div>
      <div class="sbo-info">
        <div class="sbo-name">한관리자</div>
        <div class="sbo-sub">인사팀 · 팀장 · 관리자 계정</div>
      </div>
      <svg class="sbo-check" ...>...</svg>   <!-- selected일 때만 표시 -->
    </div>
    <div class="sbp-option" id="sbpOptEmp" onclick="switchRole('emp')">
      ...홍길동 / 직원 계정...
    </div>
  </div>
</div>
```

- `.sbp-dropdown.show` → `display:block` (토글)
- `.sbp-option.selected` → `background:var(--accent-light)`, `.sbo-check` 표시
- `.sbp-arrow`: 드롭다운 열리면 `rotate(180deg)`

### 3-3. 메뉴 그룹 (.nav)

```html
<nav class="nav">              <!-- flex:1; padding:12px 8px; overflow-y:auto -->

  <!-- 관리자용 그룹 -->
  <div class="nav-group" id="navAdmin">
    <div class="nav-label">관리자용</div>
    <div class="nav-item active" data-page="dashboard" onclick="goPage(this)">
      <svg .../>
      대시보드
    </div>
    <div class="nav-item" data-page="leave" onclick="goPage(this)">
      <svg .../>
      연차 관리
      <span class="count">4</span>   <!-- 뱃지 카운트 (선택적) -->
    </div>
    <!-- cert, faq ... -->
  </div>

  <!-- 구분선 (관리자 모드에서만 표시) -->
  <div id="navDivider" style="height:1px;background:var(--border);margin:6px 8px 8px"></div>

  <!-- 직원용 그룹 -->
  <div class="nav-group" id="navEmp">
    <div class="nav-label">직원용</div>
    <div class="nav-item" data-page="emp-dashboard" onclick="goPage(this)">...</div>
    <div class="nav-item" data-page="emp-cert"      onclick="goPage(this)">...</div>
    <div class="nav-item" data-page="emp-profile"   onclick="goPage(this)">...</div>
  </div>

</nav>
```

| 클래스 | 스타일 |
|--------|--------|
| `.nav-item` | `padding:8px 12px`, `border-radius:var(--radius-md)`, `font-size:13px` |
| `.nav-item:hover` | `background:var(--accent-light)`, `color:var(--accent)` |
| `.nav-item.active` | `background:var(--accent-light)`, `color:var(--accent)`, `font-weight:700` |
| `.nav-item svg` | `16×16px`, `flex-shrink:0` |
| `.count` | `font-size:11px`, `background:var(--accent-light)`, `color:var(--accent)`, `padding:1px 7px`, `border-radius:20px` |

### 3-4. 하단 고정 메뉴 (#navBottom)

```html
<div id="navBottom" style="padding:8px 8px 0;border-top:1px solid var(--border)">
  <div class="nav-item" data-page="intro"   onclick="goPage(this)">MY인사 소개</div>
  <div class="nav-item" data-page="onboard" onclick="goPage(this)">모바일 안내</div>
</div>
```

관리자 모드: 표시 / 직원 모드: `display:none`

---

## 5. 리사이즈 핸들 (.resizer)

```html
<div class="resizer" id="resizer"></div>
```

- `width:5px`, `cursor:col-resize`, 드래그 시 `.dragging` 클래스 추가
- hover / dragging 시 `background:var(--accent)`

---

## 6. 상단바 (.topbar)

```html
<header class="topbar">
  <div>
    <h1 id="pageTitle">대시보드</h1>
    <div class="crumb" id="pageCrumb">관리자 / 대시보드</div>
  </div>
  <div class="topbar-actions">
    <input class="search-input" placeholder="직원·게시글·증명서 검색" />
  </div>
</header>
```

- `height:58px`, `padding:0 24px`, `background:var(--surface)`, `border-bottom:1px solid var(--border)`
- `h1`: `font-size:17px`, `font-weight:800`
- `.crumb`: `font-size:11px`, `color:var(--text-tertiary)`
- `.search-input`: `width:240px`, `height:34px`, `padding:0 12px 0 34px`, `background:var(--bg)`
  - 돋보기 아이콘은 CSS `background-image` 인라인 SVG로 처리 — 별도 `<svg>` 태그 없음
  - focus 시 `border-color:var(--accent)`, `box-shadow:0 0 0 3px var(--accent-light)`

---

## 7. 콘텐츠 영역 + 페이지 시스템

```html
<div class="content">   <!-- flex:1; overflow-y:auto; padding:24px; background:var(--bg) -->

  <div class="page active" data-page="dashboard">...</div>
  <div class="page"        data-page="leave">...</div>
  <div class="page"        data-page="cert">...</div>
  <div class="page"        data-page="faq">...</div>
  <div class="page"        data-page="intro">...</div>
  <div class="page"        data-page="onboard">...</div>
  <div class="page"        data-page="emp-dashboard">...</div>
  <div class="page"        data-page="emp-cert">...</div>
  <div class="page"        data-page="emp-profile">...</div>

</div>
```

- `.page` 기본: `display:none`
- `.page.active`: `display:block`
- 페이지 전환: `goPage(navItem)` 호출 → `data-page` 매칭으로 `.active` 토글

---

## 8. 역할 전환 시스템

| 모드 | navAdmin | navDivider | navEmp | navBottom | 기본 진입 페이지 |
|------|:--------:|:----------:|:------:|:---------:|:---------------:|
| admin | 표시 | 표시 | 표시 | 표시 | `dashboard` |
| emp | 숨김 | 숨김 | 표시 | 숨김 | `emp-dashboard` |

```js
const SIDEBAR_PROFILES = {
  admin: { avatar:'한', name:'한관리자', role:'인사팀 · 팀장 · 관리자' },
  emp:   { avatar:'홍', name:'홍길동',   role:'마케팅팀 · 대리 · 직원' },
};

// 역할 전환 시 사이드바 아바타/이름/역할 텍스트 갱신 후
// admin → goPage('dashboard'), emp → goPage('emp-dashboard')
```

---

## 9. PAGE_META (페이지 타이틀 / 크럼브)

```js
const PAGE_META = {
  dashboard:       { title:'대시보드',       crumb:'관리자 / 대시보드' },
  leave:           { title:'연차 관리',       crumb:'관리자 / 연차 관리' },
  cert:            { title:'업무신청관리',    crumb:'관리자 / 업무신청관리' },
  faq:             { title:'FAQ 답변 관리',   crumb:'관리자 / FAQ 답변 관리' },
  onboard:         { title:'모바일 안내',     crumb:'관리자 / 모바일 안내' },
  intro:           { title:'MY인사 소개',     crumb:'관리자 / MY인사 소개' },
  'emp-dashboard': { title:'대시보드',        crumb:'직원 / 대시보드' },
  'emp-cert':      { title:'업무신청',        crumb:'직원 / 업무신청' },
  'emp-profile':   { title:'프로필 관리',     crumb:'직원 / 프로필 관리' },
};
```

페이지 전환 시 `#pageTitle`, `#pageCrumb` 요소에 자동 반영.

---

## 10. 스크롤바 스타일 (전역)

```css
::-webkit-scrollbar { width:6px; height:6px }
::-webkit-scrollbar-track { background:transparent }
::-webkit-scrollbar-thumb { background:var(--border-strong); border-radius:3px }
::-webkit-scrollbar-thumb:hover { background:var(--text-tertiary) }
```

---

## 11. 신규 화면 추가 체크리스트

새 페이지를 추가할 때 아래 4단계를 순서대로 처리한다.

### 1단계 — HTML `.page` 블록 생성

`.content` 안에 추가한다.

```html
<div class="page" data-page="new-page">
  ...
</div>
```

> `id="screen-*"` 는 특수한 CSS 처리가 필요한 경우에만 붙인다 (예: `id="screen-intro"`).

### 2단계 — `PAGE_META` 등록

```js
const PAGE_META = {
  // 기존 항목들...
  'new-page': { title:'페이지명', crumb:'관리자 / 페이지명' },
};
```

### 3단계 — 사이드바 `nav-item` 추가

```html
<!-- 관리자용: #navAdmin 안에 / 직원용: #navEmp 안에 -->
<div class="nav-item" data-page="new-page" onclick="goPage(this)">
  <svg .../>
  페이지명
</div>
```

### 4단계 — 페이지 이름 규칙

| 유형 | 패턴 | 예시 |
|------|------|------|
| 관리자 전용 | 단어 또는 하이픈 | `leave`, `cert`, `faq` |
| 직원 전용 | `emp-` 프리픽스 | `emp-dashboard`, `emp-cert` |
| 공통 안내 | 단어 | `intro`, `onboard` |

> **모달·사이드패널은 반드시 `</body>` 직전에 배치한다** — z-index 스택 오류 방지.

# MY인사 PC — 레이아웃 규칙

> 기준 파일: `MY인사_PC_v31.html`  
> CSS 파일: `_common/my_tokens.css` + `pc/my_cont.css` (2개 링크 필수)  
> 경리나라(common.css 기반)와 완전히 다른 자체 CSS 시스템 — 혼용 금지. **HTML `style=""` 인라인 CSS 사용 금지** (예외 4종만 허용 → §components_core 필수 규칙).

---

## CSS 파일 구조

```
my인사/
├── _common/
│   └── my_tokens.css   ← :root 블록 2개 포함 (읽기 전용)
│                          블록1: MY인사 PC 전용 토큰 (--accent, --bg, --z-modal:1000 등)
│                          블록2: index.html 전용 토큰 (--primary-*, --label-*, --z-modal-overlay:8000 등)
└── pc/
    ├── MY인사_PC_v31.html
    ├── my_cont.css     ← PC 화면 전용 레이아웃 + 컴포넌트
    └── rules_myinsa_pc_*.md
```

### HEAD 순서 (MY인사_PC_v31.html)

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MY인사</title>
<link rel="stylesheet" href="../_common/my_tokens.css">
<link rel="stylesheet" href="my_cont.css">
```

`my_tokens.css` — **내용 수정 금지.** 이 파일을 변경하면 PC·index 양쪽이 모두 깨짐.  
`my_cont.css` — 블록1 토큰(`var(--accent)`, `var(--bg)` 등)만 참조. `:root` 재정의 금지.  
**⚠️ 블록2(index.html 전용) 토큰 참조 금지** — 유사 이름이 많으나 z-index 스케일이 다르므로 절대 혼용 불가.

### my_cont.css 섹션 구조 (스타일 추가 위치 기준)

신규 CSS를 추가할 때 아래 섹션 순서를 따른다. **기존 섹션 이름과 중복 선언 금지.**

```
/* ── PC 전용 로컬 토큰 ── */       1행  — --hr-*-bg 등 my_tokens.css 미정의 토큰
/* ── 사이드바 ── */                24행  — sidebar, brand, nav, resizer, sbp-*
/* ── 메인 ── */                   102행  — main, topbar, content, page 전환, nav-item
/* ── 공통 컴포넌트 ── */          ~200행  — btn, badge, tbl, modal, panel, toast, alert, toolbar ...
/* ── [관리자] 대시보드 ── */       396행  — kpi-grid, kpi-*, dash-grid
/* ── [관리자] 연차 관리 ── */      433행  — leave-table, leave-tooltip, lsp-*
/* ── [관리자] FAQ / 업무신청 ── */ 520행  — faq-cat-btn, cert-*
/* ── [관리자] 모바일 안내 ── */    559행  — onb-*, kakao-btn, chip-bar
/* ── [직원] 대시보드 ── */         583행  — emp-tab-btn, emp-tbl, emp-dash-grid
/* ── [직원] 프로필 관리 ── */      604행  — profile-*, hr-*
/* ── [공통] MY인사 소개 ── */      630행  — lux-*, intro, onboard
/* ── @keyframes ── */             맨 끝  — 모든 애니메이션 keyframes
```

**신규 스타일 추가 위치 결정:**
- 전체 화면에서 쓰이는 공통 컴포넌트 → `/* ── 공통 컴포넌트 ── */` 섹션 내부
- 특정 페이지 전용 → 해당 페이지 섹션 내부 (없으면 `@keyframes` 바로 앞에 새 섹션 추가)
- 새 섹션 형식: `/* ── [역할] 페이지명 ── */`

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
  --accent-dk: #1b4d82;         /* hover/강조용 짙은 액센트 */

  /* 상태 색상 */
  --success: #16a34a;      --success-bg: #f0fdf4;
  --warning: #d97706;      --warning-bg: #fffbeb;
  --danger: #dc2626;       --danger-bg: #fef2f2;
  --danger-dk: #b91c1c;        /* 위험 hover */
  --info: #2563eb;         --info-bg: #eff6ff;

  /* 그림자 */
  --shadow-sm: 0 1px 3px rgba(0,0,0,.06), 0 1px 2px rgba(0,0,0,.04);
  --shadow-md: 0 4px 12px rgba(35,99,168,.08), 0 2px 4px rgba(0,0,0,.04);
  --shadow-card: 0 2px 8px rgba(0,0,0,.06), 0 0 0 1px rgba(0,0,0,.04);
  --shadow-accent: rgba(35,99,168,.3);   /* btn-primary box-shadow */
  --shadow-accent-up: rgba(35,99,168,.4); /* btn-primary:hover box-shadow */

  /* 반지름 */
  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 10px;
  --radius-xl: 14px;

  /* 레이아웃 */
  --sidebar-w: 240px;

  /* 폰트 크기 */
  --fs-2xs: 10px;  --fs-xs: 11px;  --fs-sm: 12px;  --fs-body: 13px;
  --fs-md: 14px;   --fs-lg: 15px;  --fs-xl: 17px;

  /* 폰트 굵기 */
  --fw-normal: 400;  --fw-medium: 500;  --fw-semibold: 600;
  --fw-bold: 700;    --fw-extrabold: 800;

  /* z-index */
  --z-sticky-body: 1;   /* tbody sticky 셀 */
  --z-sticky: 2;        /* thead sticky */
  --z-sticky-col: 3;    /* thead sticky + col-sticky 교차 */
  --z-dropdown: 200;
  --z-overlay: 500;     /* leave-side-overlay */
  --z-panel: 501;       /* leave-side-panel */
  --z-modal: 1000;
  --z-toast: 9999;

  /* 트랜지션 */
  --transition: .15s;
  --transition-md: .2s;
  --transition-panel: .25s cubic-bezier(.4,0,.2,1);

  /* 스페이싱 (4px 단위) */
  --sp-1: 4px;   --sp-2: 6px;   --sp-3: 8px;   --sp-4: 10px;  --sp-5: 12px;
  --sp-6: 14px;  --sp-7: 16px;  --sp-8: 18px;  --sp-9: 20px;  --sp-10: 24px;

  /* KPI 카드 테마 색상 */
  --kpi-leave: #1a54a8;   --kpi-leave-lt: #4d8fd4;   --kpi-leave-shadow: rgba(26,84,168,.18);
  --kpi-faq: #6b35c9;     --kpi-faq-lt: #9b6de0;     --kpi-faq-shadow: rgba(107,53,201,.18);
  --kpi-cert: #0d8f6e;    --kpi-cert-lt: #2bbf9a;    --kpi-cert-shadow: rgba(13,143,110,.18);
  --kpi-total: #c95f0a;   --kpi-total-lt: #f08030;   --kpi-total-shadow: rgba(201,95,10,.18);

  /* 연차 전용 */
  --leave-track: rgba(35,99,168,.12);   /* 연차 진행바 배경 */
  --leave-cell-hover: #dbeafe;          /* 월별 셀 hover */
  --leave-total-fg: #1b4d82;            /* 총연차 셀 텍스트 */
  --leave-total-border: #bfd9ef;        /* 총연차 셀 좌우 보더 */
  --leave-used-dark: #0f3870;           /* month-used-nonzero 텍스트 */

  /* 증명서 뱃지 */
  --badge-emp-bg: #dbeafe;   --badge-emp-fg: #1e40af;
  --badge-exp-bg: #fce7f3;   --badge-exp-fg: #9d174d;
  --badge-other-bg: #d1fae5; --badge-other-fg: #065f46;

  /* ⚠️ hr-item-icon 전용 로컬 토큰
     위치: my_tokens.css가 아닌 my_cont.css 파일 맨 위 :root {} 블록에 단독 선언
     login/index 화면에서는 이 토큰 없음 — PC 전용 */
  --hr-cert-bg: #dbeafe;   /* 자격증 아이콘 배경 (파랑) */
  --hr-edu-bg:  #f3e8ff;   /* 학력 아이콘 배경 (보라) */
  --hr-lang-bg: #fef9c3;   /* 어학 아이콘 배경 (노랑) */
  /* 신규 hr 아이콘 배경 추가 시 → my_cont.css 상단 :root에 --hr-[타입]-bg: #hex 추가 */

  /* 소개 페이지 lux 테마 (--accent 와 별개의 인디고 계열) */
  --lux-accent: #4F46E5;   --lux-accent-lt: #6366F1;
  --lux-text: #0f172a;     --lux-muted: #4b5563;    --lux-border: #e5e5e5;
  --lux-dark: #1f2937;     --lux-subtle: #9ca3af;
  --lux-base: #fafafa;     --lux-divide: #f0f0f0;

  /* 기타 */
  --toast-bg: #18181b;
}
```

### ⚠️ PC 전용 토큰 vs index.html 전용 토큰 — 혼용 금지

`my_tokens.css`에는 이름이 유사하지만 **스케일이 전혀 다른 토큰이 두 벌 공존**한다.  
PC 화면(`my_cont.css`)은 왼쪽 열만 사용. 오른쪽 열은 `index.html` 전용이므로 절대 참조 금지.

| MY인사 PC (✅ 써야 함) | index.html (❌ 쓰면 안 됨) |
|---|---|
| `--accent` (#2363a8) | `--accent-blue` (#005EEB) |
| `--bg` (#f7f7fa 페이지 배경) | `--bg-normal` (#FFFFFF) |
| `--radius-xl` (14px) | `--radius-card-lg` (16px) / `--radius-popup` (12px) |
| `--transition` / `--transition-md` | `--t-fast` / `--t-base` |
| `--z-modal` (1000) | `--z-modal-overlay` (**8000** — 스케일 완전히 다름) |
| `--shadow-card` / `--shadow-md` | `--shadow-modal` / `--shadow-popup` |

> z-index가 섞이면 레이어 순서가 깨진다. `--primary-*`, `--label-*`, `--line-*`, `--fill-*`, `--tag-*`, `--header-h` 등도 모두 index.html 전용 — PC 화면에 쓰지 않는다.

---

## 2. HTML 파일 기본 골격 템플릿

새 화면 파일 생성 시 아래 구조를 그대로 복사해 시작한다. `<head>` 구조·경로·속성을 임의로 변경하지 않는다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MY인사 — 페이지명</title>  <!-- 실제 파일 title은 "MY인사 v11 — 관리자/직원 통합 PC" — 신규 파일 작성 시 위 형식 사용 -->
<link rel="stylesheet" href="../_common/my_tokens.css">
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
> - CSS 링크 2개 순서 고정 — `my_tokens.css` → `my_cont.css`, 경로·순서 변경 금지
> - 폰트 별도 `<link>` 불필요 — `@font-face` 는 `my_tokens.css` 에 선언됨 (my_cont.css 아님)

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

폰트 파일은 `_common/fonts_Pretendard/` 폴더에 위치. **`@font-face`는 `my_tokens.css` 최상단에서 선언** (my_cont.css가 아님).

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
- HTML에는 `my_tokens.css` + `my_cont.css` 2개 링크면 충분 (폰트는 `my_tokens.css`에 포함됨)
- CDN 의존 없음 — 오프라인·인트라넷 환경에서도 폰트 정상 표시

---

## 4. 사이드바 (.sidebar) 구조

사이드바 내부는 위→아래 순서로 5개 블록:

```
.brand            ← 로고 + 앱명
.sb-profile       ← 역할 스위처 (드롭다운 포함)
.nav              ← 메뉴 그룹들 (flex:1, overflow-y:auto)
#navBottom        ← 하단 고정 메뉴 (관리자 모드에서만 표시)
.user-card        ← 하단 사용자 카드 (직원 모드에서만 표시) ⚠️ 미구현
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

| 클래스 | 설명 |
|---|---|
| `.sbp-avatar` | 스위처 아바타 (32×32px, border-radius:9px, --accent-light bg) |
| `.sbp-info` | 이름+역할 묶음 (flex:1, min-width:0) |
| `.sbp-name` | 이름 텍스트 (--fs-body, --fw-bold, 말줄임 처리) |
| `.sbp-role` | 역할 텍스트 (--fs-xs, --text-tertiary, margin-top:1px) |
| `.sbp-arrow` | 드롭다운 화살표 SVG (--text-tertiary). `.sb-profile.open` 시 rotate(180deg) |
| `.sbp-dropdown` | 드롭다운 패널 (absolute, --radius-lg, `box-shadow:0 8px 24px rgba(0,0,0,.12)`). `.show` → display:block |
| `.sbp-option` | 드롭다운 항목 (padding:10px 14px, flex). `.selected` → --accent-light bg |
| `.sbo-avatar` | 항목 아바타 (28×28px, --radius-md, --accent-light bg) |
| `.sbo-info` | 항목 이름+부제 묶음 |
| `.sbo-name` | 항목 이름 (--fs-body, --fw-semibold) |
| `.sbo-sub` | 항목 부제 (--fs-xs, --text-tertiary) |
| `.sbo-check` | 선택 체크 SVG (margin-left:auto, --accent). `.selected` 상태일 때만 display:block |

### 3-3. 메뉴 그룹 (.nav)

```html
<nav class="nav">              <!-- flex:1; padding:12px 8px; overflow-y:auto -->

  <!-- 관리자용 그룹 -->
  <div class="nav-group" id="navAdmin">
    <div class="nav-label">
      <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>
      관리자용
    </div>
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
  <div id="navDivider" class="nav-divider"></div>

  <!-- 직원용 그룹 -->
  <div class="nav-group" id="navEmp">
    <div class="nav-label">
      <svg width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M20 21v-2a4 4 0 00-4-4H8a4 4 0 00-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>
      직원용
    </div>
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

| 클래스 | 설명 |
|---|---|
| `.nav-group` | 메뉴 그룹 래퍼 (margin-bottom:--sp-3) |
| `.nav-divider` | 그룹 구분선 (height:1px, --border bg, margin:6px 8px 8px). 관리자 모드만 표시 |
| `.nav-bottom` | 하단 고정 메뉴 래퍼 (padding:8px 8px 0, border-top:1px). 관리자 모드만 표시 |
| `.nav-label` | 그룹 레이블 텍스트 (--fs-2xs, uppercase, letter-spacing:0.08em, --text-tertiary) |

### 3-4. 하단 고정 메뉴 (#navBottom)

```html
<div id="navBottom" class="nav-bottom">
  <div class="nav-item" data-page="intro" onclick="goPage(this)">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>
    MY인사 소개
  </div>
  <div class="nav-item" data-page="onboard" onclick="goPage(this)">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.6"><rect x="5" y="2" width="14" height="20" rx="2"/><line x1="12" y1="18" x2="12.01" y2="18"/></svg>
    모바일 안내
  </div>
</div>
```

> **`nav-bottom`**: CSS 클래스로 `padding:8px 8px 0; border-top:1px solid var(--border)` 처리 — 인라인 스타일 금지.

관리자 모드: 표시 / 직원 모드: `display:none`

### 3-5. 하단 사용자 카드 (.user-card)

> ⚠️ **현재 미구현** — CSS(`my_cont.css`)와 HTML 예시만 존재. 실제 `MY인사_PC_v31.html`에 마크업 없음, `switchRole()`에서 토글 처리도 없음. 직원 모드 전환 시 카드가 표시되지 않는다. 추후 구현 예정이거나 미사용 상태임.

직원 모드로 전환 시 사이드바 최하단에 표시되는 사용자 정보 카드 (미구현).  
기본값 `display:none` — 구현 시 `switchRole('emp')` 호출 시 표시 예정.

```html
<div class="user-card" id="sidebarUserCard" style="display:none">
  <div class="avatar" id="sidebarAvatar">한</div>
  <div class="info">
    <div class="name" id="sidebarName">홍길동</div>
    <div class="meta" id="sidebarMeta">마케팅팀 · 대리</div>
  </div>
</div>
```

```css
.user-card { padding:14px 12px; border-top:1px solid var(--border); display:flex; align-items:center; gap:var(--sp-4); background:linear-gradient(135deg,var(--accent) 0%,var(--accent-mid) 100%) }
.user-card .avatar { width:34px; height:34px; border-radius:10px; background:rgba(255,255,255,.25); color:#fff; display:grid; place-items:center; font-size:13px; font-weight:800; border:1.5px solid rgba(255,255,255,.3) }
.user-card .info .name { font-size:13px; font-weight:700; color:#fff }
.user-card .info .meta { font-size:11px; color:rgba(255,255,255,.7) }
```

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

### 페이지 전환 함수 사용 기준

| 함수 | 시그니처 | 언제 사용 |
|---|---|---|
| `goPage(el)` | nav-item 엘리먼트 | **사이드바 nav-item 클릭** — `onclick="goPage(this)"`. nav-item의 `.active` 표시도 함께 처리 |
| `goPageByName(name)` | 페이지명 문자열 | **nav-item 외부에서 이동** — KPI 카드, 버튼, 로직 내부 등. 내부적으로 `goPage(navItem)` 위임 |

```html
<!-- nav-item: goPage(this) -->
<div class="nav-item" data-page="leave" onclick="goPage(this)">연차 관리</div>

<!-- KPI 카드, 버튼 등: goPageByName('페이지명') -->
<div class="kpi" onclick="goPageByName('leave')">...</div>
```

> `goPageByName(name)` 은 해당 name의 nav-item을 찾아 `goPage(navItem)` 을 위임하는 래퍼 함수. nav-item이 존재하지 않는 페이지명은 전환 불가.

---

## 8. 역할 전환 시스템

| 모드 | navAdmin | navDivider | navEmp | navBottom | 기본 진입 페이지 |
|------|:--------:|:----------:|:------:|:---------:|:---------------:|
| admin | 표시 | 표시 | 표시 | 표시 | `dashboard` |
| emp | 숨김 | 숨김 | 표시 | 숨김 | `emp-dashboard` |

### `switchRole(role)` 함수 동작 상세

```js
switchRole('admin') // 또는 switchRole('emp')
```

호출 시 **순서대로** 아래 3가지가 실행된다:

1. **사이드바 프로필 업데이트** — 아바타 텍스트·이름·역할 텍스트를 `SIDEBAR_PROFILES[role]` 데이터로 교체
2. **nav 요소 표시/숨김** — 위 표 기준으로 `style.display` 토글 (`''` / `'none'`)
3. **페이지 자동 전환** — `goPage(navItem)` 호출
   - admin → `data-page="dashboard"` nav-item으로 이동
   - emp → `data-page="emp-dashboard"` nav-item으로 이동

**주의 사항:**
- 역할 전환은 **사이드바 스위처에서만** 가능. 직원 페이지로 이동해도 역할이 자동 전환되지 않음.
- localStorage 저장 없음 — 페이지 새로고침 시 admin으로 초기화됨.
- `switchRole()`은 `goPage()`를 내부 호출하므로 중복 호출 불필요.

### 역할별 컴포넌트 숨김 패턴

**메뉴 레벨 숨김** — `switchRole()`이 처리 (위 표 참조)

**페이지 내 컴포넌트 숨김** — 기본적으로 역할별 제어 없음. 아래 예외 경우만 존재:

| 컴포넌트 | 숨김 조건 | 제어 방식 |
|---|---|---|
| `#profileEditBtn` (수정 버튼) | 수정 모드(`edit`)에서는 숨김 | `setProfileMode()` |
| `#profileSaveBtn`, `#profileCancelBtn` | 조회 모드(`view`)에서는 숨김 | `setProfileMode()` |
| `.hr-edit-btn` | 조회 모드에서는 숨김 | `setProfileMode()` |

**신규 컴포넌트를 역할별로 숨겨야 할 경우:**
```js
// ✅ 올바른 패턴 — switchRole() 내부에서 직접 제어
function switchRole(role) {
  // ... 기존 로직 ...
  const isAdmin = (role === 'admin');
  document.getElementById('adminOnlyBtn').style.display = isAdmin ? '' : 'none';
}
```

> **규칙:** 역할별 숨김이 필요한 신규 버튼은 반드시 `switchRole()` 함수 내에 `style.display` 토글을 추가한다.  
> `style="display:none"` 초기값은 인라인 CSS 허용 예외 ④에 해당하므로 사용 가능.

---

## 9. 페이지 타이틀 / 크럼브

페이지 전환 시 `#pageTitle`, `#pageCrumb` 요소에 자동 반영. 페이지 목록과 타이틀/크럼브 값은 `rules_myinsa_pc_pages.md` Section 1 참고.

> **가상 페이지** (`cert-issue`, `cert-other`, `cert-history`): `.page` 블록 없이 모달을 직접 오픈하는 방식. `nav-item`에 연결되지 않고 테이블 행 버튼에서 호출됨.

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

새 페이지를 추가할 때 아래 6단계를 순서대로 처리한다.

### 1단계 — HTML `.page` 블록 생성

`.content` 안에 추가한다.

```html
<div class="page" data-page="new-page">
  ...
</div>
```

> `id="screen-*"` 는 특수한 CSS 처리가 필요한 경우에만 붙인다 (예: `id="screen-intro"`).

### 2단계 — `PAGE_META` 등록

`MY인사_PC_v31.html` 내 `PAGE_META` 객체에 신규 항목 추가.  
형식: `'new-page': { title:'페이지명', crumb:'관리자 / 페이지명' }`

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

### 5단계 — `my_cont.css` 스타일 추가 (필요 시)

페이지 전용 CSS가 필요한 경우 `my_cont.css` 파일 **맨 끝 `@keyframes` 블록 바로 앞**에 섹션 추가.

```css
/* ── [페이지명] 페이지 ── */
.page[data-page="new-page"] .some-class { ... }
```

> 공통 클래스(`.toolbar`, `.tbl`, `.btn` 등)는 이미 정의돼 있으므로 추가 불필요. **페이지 고유 스타일만** 추가한다.

### 6단계 — 초기 `.active` 상태

- 새 페이지 `.page` 블록에 `.active` 클래스를 **붙이지 않는다.**
- `.active`는 JS `goPage()` 가 관리. HTML에서는 첫 진입 페이지(`dashboard`)만 `class="page active"`로 시작.
- 새 페이지를 첫 진입 페이지로 바꾸려면 기존 `dashboard`의 `.active`를 제거하고 새 페이지에 추가.

> **모달·사이드패널 배치 규칙** → `rules_myinsa_pc_popups.md` 참고.

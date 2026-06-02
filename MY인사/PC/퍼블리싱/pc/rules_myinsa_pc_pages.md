---
name: rules-myinsa-pc-pages
description: "MY인사 PC v31 각 페이지별 HTML 골격 구조, data-page 값, 레이아웃 패턴, 특수 클래스 정리 — 신규 페이지 추가·수정 시 참조"
metadata: 
  node_type: memory
  type: project
  originSessionId: c4cb22ee-5bf5-4a66-91cc-1113065fd4e6
---

# MY인사 PC — 페이지 규칙

## 0. 페이지별 필수 JS 함수 매핑

신규 페이지 추가 시 아래 표를 참고해 필요한 JS 함수를 누락 없이 연결한다.  
함수 구현은 `MY인사_PC_v31.html` 내 `<script>` 블록 하단(팝업 열기/닫기 함수군)에 추가한다.

| data-page | 필수 JS 함수 | 용도 |
|---|---|---|
| `dashboard` | `goPageByName('페이지명')` | KPI 카드 클릭 시 페이지 이동 |
| `leave` | `editTotal(td)`, `attachLeaveTooltips()`, `openLeavePanel(name,dept,total,used,remain,status)`, `filterEmpStatus(el,status)` | 인라인 편집·툴팁·사이드패널·필터 |
| `cert` | `openCertIssueModal(name,kind,purpose,date)`, `openCertHistoryModal(name,kind,date)`, `openCertRejectModal(name,kind)`, `openCertReasonModal(name,reason)` | 상태별 모달 열기 |
| `faq` | `filterFaqCommonTable(el,cat)`, `openFaqDetailModal(name,mode)`, `openFaqEdit(cat,q,a,btn)`, `confirmFaqDelete()` | 카테고리 필터·답변 작성·수정·삭제 |
| `intro` | — | 정적 콘텐츠 (JS 불필요) |
| `onboard` | `setInstFilter(type)`, `openKakaoBulk()`, `openKakaoSingle(id)` | 설치 현황 필터·카카오 발송 |
| `emp-dashboard` | `filterEmpStatus(el,status)` | 직원 상태 필터 |
| `emp-cert` | `openEmpCertModal()`, `openEmpCertStep2(type)` | 2단계 증명서 신청 |
| `emp-profile` | `setProfileMode('edit'\|'view')`, `openHrInfoModal()`, `openHrEditModal(type,v1,v2,v3,v4)` | 조회/수정 모드 전환·HR 정보 관리 |

> **신규 페이지 추가 시:** 표에 행을 추가하고, 해당 페이지에서 필요한 함수를 `<script>` 블록에 구현한다.

---

## 1. 페이지 블록 조립 패턴

신규 페이지는 아래 4가지 패턴 중 가장 유사한 것을 템플릿으로 사용한다.

**패턴 A — 리스트 페이지** (leave, cert, faq, onboard)
```
.page
  .toolbar          ← 검색·필터·액션 버튼
  .table-wrap       ← 테이블 (또는 .panel 내 콘텐츠)
  .panel (선택)     ← 보조 패널 또는 빈 상태(.empty)
```

**패턴 B-1 — 관리자 대시보드** (dashboard) — KPI 있음
```
.page
  .kpi-grid         ← KPI 카드 4개 (관리자 전용, 직원 대시보드에는 없음)
  .dash-grid        ← 2열 그리드
    .panel          ← 좌측 콘텐츠
    .panel          ← 우측 콘텐츠
```

**패턴 B-2 — 직원 대시보드** (emp-dashboard) — KPI 없음
```
.page
  .dash-grid        ← 2열 그리드 (.kpi-grid 없이 바로 시작)
    .panel          ← 좌측 콘텐츠
    .panel          ← 우측 콘텐츠
```

> ⚠️ emp-dashboard에 `.kpi-grid`를 추가하면 안 됨 — 직원은 KPI 권한 없음.

**패턴 C — 프로필/조회 페이지** (emp-profile)
```
.page
  .profile-actions  ← 수정/저장/취소 버튼 행
  .profile-layout   ← 2열 또는 단일 패널 묶음
    .hr-section × N ← 섹션 반복
```

**패턴 D — 마케팅/소개 페이지** (intro, onboard)
```
.page  (또는 id="screen-intro")
  .lux-hero         ← 히어로 영역
  .lux-feature × N ← 기능 소개 반복
  .lux-pricing      ← 가격 (intro만)
```

> 기존 페이지와 구조가 다를 경우 새 패턴을 만들되, 이 문서에 패턴 E로 추가한다.

---

## 2. 페이지 시스템 기본

```html
<!-- 모든 페이지는 .main > .content 내부의 .page 로 감싼다 -->
<div class="page" data-page="[페이지ID]">...</div>
<!-- 초기 활성 페이지만 .active 추가 -->
<div class="page active" data-page="dashboard">...</div>
```

**페이지 전환:** `goPage(navItemEl)` / `goPageByName('leave')`  
**신규 페이지 추가 시 PAGE_META 등록 필수:** `MY인사_PC_v31.html` 내 `PAGE_META` 객체에 추가.  
형식: `'new-page': {title:'페이지명', crumb:'그룹 / 페이지명'}`

### `data-page` 신규 값 결정 — 중복 방지 체크리스트

1. **기존 목록 확인** — 이 파일 `## 1. 페이지 전체 목록` 표에서 `data-page` 열 검색
2. **명명 규칙 확인**
   - 관리자 전용: 단어 또는 하이픈 (예: `leave`, `hr-manage`)
   - 직원 전용: `emp-` 프리픽스 필수 (예: `emp-payroll`)
   - 공통/안내: 단어 (예: `intro`, `help`)
3. **camelCase 금지** — `newPage` ❌, `new-page` ✅
4. **등록 후 표 업데이트** — 이 파일의 `## 1. 페이지 전체 목록` 표에 행 추가

**사이드바 nav-item도 추가 필요:**
```html
<div class="nav-item" data-page="new-page" onclick="goPage(this)">
  <!-- SVG 아이콘 -->
  페이지명
</div>
```

## 1. 페이지 전체 목록

| data-page | 이름 | 역할 | HTML 위치(행) |
|---|---|---|---|
| dashboard | 대시보드 | 관리자 | 136 |
| leave | 연차 관리 | 관리자 | 359 |
| cert | 업무신청관리 | 관리자 | 509 |
| faq | FAQ 답변 관리 | 관리자 | 621 |
| intro | MY인사 소개 | 공통 | 691 |
| onboard | 모바일 안내 | 관리자 | 876 |
| emp-dashboard | 직원 대시보드 | 직원 | 960 |
| emp-cert | 직원 업무신청 | 직원 | 1120 |
| emp-profile | 직원 프로필 관리 | 직원 | 1202 |

**⚠️ 가상 페이지 — `.page` 블록 없음, 일반 페이지와 다름:**

| data-page | 실체 | 작동 방식 |
|---|---|---|
| `cert-issue` | `.page` 없음 | 테이블 행 버튼 → `openCertIssueModal()` 직접 호출 |
| `cert-history` | `.page` 없음 | 테이블 행 버튼 → `openCertHistoryModal()` 직접 호출 |
| `cert-other` | `.page` 없음 | 테이블 행 버튼 → 해당 모달 직접 호출 |

> AI 주의: 이 3개는 `PAGE_META`에도 등록하지 않고, `nav-item`도 없다. 모달만 존재한다. 신규 페이지 추가 시 일반 페이지와 혼동하면 안 됨.

## 2. 페이지별 HTML 골격

### 2-1. dashboard (관리자 대시보드)

```html
<div class="page active" data-page="dashboard">
  <div class="kpi-grid">
    <div class="kpi kpi-leave" onclick="goPageByName('leave')">
      <div class="label">대기중 연차 결재</div>
      <div class="value">4</div>
    </div>
    <div class="kpi kpi-faq" onclick="goPageByName('faq')">...</div>
    <div class="kpi kpi-cert" onclick="goPageByName('cert')">...</div>
    <div class="kpi kpi-total">...</div>
  </div>

  <div class="dash-grid">
    <!-- 미처리 업무 패널 -->
    <div class="panel">
      <div class="panel-head"><h3>미처리 업무 (10)</h3></div>
      <div class="p-0">
        <div class="alert-row" onclick="[모달/페이지이동]">
          <div class="alert-icon [faq|cert|leave]"><!-- SVG --></div>
          <div class="alert-body">
            <div class="alert-title">...</div>
            <div class="alert-meta"><strong>이름</strong> · 부서 · 시간</div>
          </div>
          <div class="alert-actions" onclick="event.stopPropagation()">
            <a href="#none" class="btn btn-sm btn-primary">액션</a>
          </div>
        </div>
      </div>
    </div>

    <!-- 직원 현황 패널 -->
    <div class="panel panel--start">
      <div class="panel-head"><h3>직원 현황</h3></div>
      <div class="emp-tab-bar">
        <a href="#none" class="emp-tab-btn active" onclick="filterEmpStatus(this,'all')" data-status="all">전체 <span class="emp-tab-count">9</span></a>
        <!-- 재직/휴직/퇴직 탭 추가 -->
      </div>
      <div class="emp-tbl-wrap">
        <table class="tbl emp-tbl" id="empStatusTable">
          <thead>...</thead>
          <tbody>
            <tr data-emp-status="재직">
              <td><strong>이름</strong></td>
              <!-- 부서/직급/입사일/연락처 -->
              <td class="center"><span class="badge s">재직</span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
```

**KPI 타입:** `kpi-leave`(연차), `kpi-faq`(FAQ), `kpi-cert`(증명서), `kpi-total`(총직원)
**alert-icon 타입:** `faq`(FAQ), `cert`(증명서), `leave`(연차)
**주의:** `.alert-actions`에 `onclick="event.stopPropagation()"` 필수 — 버블 방지

### 2-2. leave (연차 관리) — 이중 스크롤 주의

```html
<div class="page" data-page="leave">
  <div class="toolbar">
    <div class="filters">
      <select class="filter-input">...</select>
      <select class="filter-input">...</select>
      <input class="filter-input" placeholder="직원명 검색" />
      <span class="hint-info">
        <svg ...></svg>
        툴팁 안내 텍스트
      </span>
    </div>
    <div class="btn-group">
      <a href="#none" class="btn">버튼1</a>
      <a href="#none" class="btn">버튼2</a>
    </div>
  </div>

  <div class="table-wrap leave-table">
    <div class="ov-x-auto">                             <!-- 가로 스크롤 -->
      <div class="scroll-body" id="leaveScroll"
        style="max-height:calc(100vh - 260px);overflow-y:auto;overflow-x:visible">  <!-- 세로 스크롤 -->
        <table class="tbl" style="min-width:1100px">
          <thead>
            <tr>
              <th class="col-sticky-l" style="width:120px">직원</th>
              <th style="width:90px">부서</th>
              <th class="num" style="width:80px">총 연차</th>
              <th class="num" style="width:60px">사용</th>
              <th class="num month-h" style="width:52px">1월</th>
              <!-- 2~12월 동일 패턴 -->
              <th class="num remain col-sticky-r" style="width:60px">잔여</th>
            </tr>
          </thead>
          <tbody id="leaveTbody">
            <tr>
              <td class="col-sticky-l">
                <strong class="accent-link" onclick="openLeavePanel(...)">이름</strong>
              </td>
              <td>부서</td>
              <td class="num editable leave-total-cell" ondblclick="editTotal(this)" data-emp="이름">15</td>
              <td class="num used">2</td>
              <td class="num">1</td> <!-- 월별 값 -->
              <td class="num remain col-sticky-r">13</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</div>
```

**이중 스크롤:** `.ov-x-auto`(가로) + `.scroll-body`(세로) — 순서 바꾸면 스크롤 깨짐
**고정 열:** `col-sticky-l`(직원명 좌측), `col-sticky-r`(잔여 우측)
**더블클릭 편집:** `editable leave-total-cell` + `ondblclick="editTotal(this)"`
**JS 자동 처리:** `attachLeaveTooltips()` — DOMContentLoaded에서 0이 아닌 셀에 `month-used-nonzero` 추가

### 2-3. cert (업무신청관리)

```html
<div class="page" data-page="cert">
  <div class="toolbar">
    <div class="filters">
      <select class="filter-input"><!-- 종류 --></select>
      <select class="filter-input"><!-- 상태 --></select>
      <input class="filter-input" type="date" value="2026-04-01" />
      <span class="text-muted">~</span>
      <input class="filter-input" type="date" value="2026-04-30" />
      <input class="filter-input" placeholder="신청자 이름 또는 사번" />
    </div>
    <a href="#none" class="btn">엑셀 다운로드</a>
  </div>

  <div class="table-wrap">
    <div class="scroll-body" id="certScroll">
      <table class="tbl">
        <thead>
          <tr>
            <th style="width:140px">신청일</th>
            <th style="width:120px">신청자</th>
            <th style="width:130px">증명서 종류</th>
            <th class="center" style="width:60px">부수</th>
            <th>제출처 / 용도</th>
            <th class="center" style="width:100px">상태</th>
            <th style="width:160px"></th>
          </tr>
        </thead>
        <tbody id="certTbody">
          <tr>
            <td>2026.04.29 09:14</td>
            <td><strong>홍길동</strong></td>
            <td><span class="cert-badge cert-badge-emp">재직증명서</span></td>
            <td class="center">1</td>
            <td>은행 대출 신청용</td>
            <td class="center"><span class="badge w">대기</span></td>
            <td><div class="row-actions">
              <!-- 대기: btn-primary 발급처리 -->
              <!-- 발급완료: 이력보기 -->
              <!-- 반려: 사유보기 -->
            </div></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</div>
```

**증명서 종류 뱃지:** `cert-badge-emp`(재직), `cert-badge-exp`(경력), `cert-badge-other`(기타)
**상태별 액션 버튼:** 대기→`openCertIssueModal(...)` / 발급완료→`openCertHistoryModal(...)` / 반려→`openCertReasonModal(...)`

### 2-4. faq (FAQ 답변 관리)

```html
<div class="page" data-page="faq">
  <div class="toolbar">
    <div class="filters">
      <!-- 카테고리 탭 형식 (select 아님) -->
      <a href="#none" class="faq-cat-btn active" onclick="filterFaqCommonTable(this,'all')">전체</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'연차')">연차</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'증명서')">증명서</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'급여')">급여</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'기타')">기타</a>
    </div>
    <a href="#none" class="btn btn-primary btn-sm" onclick="document.getElementById('addFaqModal').classList.add('show')">
      <!-- + SVG -->
      FAQ 등록
    </a>
  </div>

  <div class="table-wrap">
    <table class="tbl tbl--auto" id="faqCommonTable">
      <thead>
        <tr>
          <th style="width:100px">카테고리</th>
          <th style="width:260px">질문</th>
          <th>답변 요약</th>
          <th class="center" style="width:80px">노출 상태</th>
          <th class="center" style="width:72px"></th>
        </tr>
      </thead>
      <tbody>
        <tr data-faq-cat="연차">
          <td><span class="badge i">연차</span></td>
          <td>질문 내용</td>
          <td class="text-secondary">답변 요약...</td>
          <td class="center"><span class="badge s">노출 중</span></td>
          <td class="center"><a href="#none" class="btn btn-sm" onclick="openFaqEdit(...)">수정</a></td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```

**카테고리 뱃지:** `badge i`(연차), `badge cert`(증명서), `badge pay`(급여), `badge etc`(기타)
**노출상태:** `badge s`(노출 중), `badge muted`(비노출)
**필터 속성:** `data-faq-cat="연차|증명서|급여|기타"`

### 2-5. intro (MY인사 소개)

툴바 없음 — 전체 페이지 스크롤 콘텐츠

```html
<div class="page" id="screen-intro" data-page="intro">
  <!-- 히어로 -->
  <div class="lux-hero">
    <div class="lux-hero-eyebrow">FOR EXECUTIVES</div>
    <h1 class="lux-hero-title">메인 타이틀</h1>
    <p class="lux-hero-sub">서브 텍스트</p>
  </div>

  <!-- 기능 소개 (텍스트-이미지 교대 배치) -->
  <div class="lux-feature">
    <div class="lux-feature-content">
      <div class="lux-feature-num">01</div>
      <div class="lux-feature-eyebrow">CALENDAR</div>
      <h2 class="lux-feature-title">기능 제목</h2>
      <p class="lux-feature-desc">설명</p>
      <ul class="lux-feature-list">
        <li>항목 1</li>
      </ul>
    </div>
    <div class="lux-feature-visual">
      <div class="lux-phone">
        <div class="lux-phone-notch"></div>
        <div class="lux-phone-screen">
          <div class="lux-ph-status"><span>9:41</span><span>📶 🔋</span></div>
          <div class="lux-ph-header">📅 탭명</div>
          <div class="lux-ph-content">...</div>
        </div>
      </div>
    </div>
  </div>

  <!-- 좌우 반전: .lux-feature.reverse -->
  <div class="lux-feature reverse">...</div>

  <!-- 가격 정책 -->
  <div class="lux-pricing">
    <div class="lux-pricing-eyebrow">PRICING</div>
    <h2 class="lux-pricing-title">가격 제목</h2>
    <p class="lux-pricing-sub">설명</p>
    <div class="lux-price-grid">
      <div class="lux-price-card">
        <div class="lux-price-label">도입비</div>
        <div class="lux-price-value">면 제</div>
        <div class="lux-price-tag">No setup fee</div>
      </div>
      <!-- 주요 카드: .lux-price-card.lux-price-card-main -->
      <div class="lux-price-card lux-price-card-main">
        <div class="lux-price-value">10,000<span class="price-unit">원</span></div>
      </div>
    </div>
  </div>
</div>
```

**주의:** `id="screen-intro"` — data-page와 id 모두 지정 (다른 페이지에는 id 없음)
**CSS 특수 처리:** `#screen-intro { margin:-24px -24px; overflow-y:auto }` — `.content`의 `padding:24px`를 상쇄하여 전체 너비로 렌더링. **intro 전용, 다른 페이지에 적용 금지.**
**폰 화면 배경색 변형:** `lux-phone-screen--bg`
**상태바 흰색:** `lux-ph-status--white`

#### lux-* 클래스 목록

| 클래스 | 설명 |
|---|---|
| `.lux-hero` | 히어로 섹션 (gradient bg, padding:120px 80px, text-center, border-bottom) |
| `.lux-hero-eyebrow` | 히어로 아이브로 (--fs-sm, letter-spacing:4px, --lux-accent, uppercase) |
| `.lux-hero-title` | 히어로 메인 타이틀 (64px, --fw-extrabold, --lux-text, letter-spacing:-2px) |
| `.lux-hero-sub` | 히어로 서브 (18px, --lux-muted, max-width:720px, line-height:1.6) |
| `.lux-feature` | 기능 소개 섹션 (grid 1fr 1fr, gap:80px, padding:100px 80px, border-bottom) |
| `.lux-feature.reverse` | 좌우 반전 변형 (content order:2, visual order:1) |
| `.lux-feature-content` | 텍스트 콘텐츠 영역 (max-width:520px, justify-self:end) |
| `.lux-feature-num` | 기능 번호 ("01", --fs-md, letter-spacing:4px, --lux-subtle) |
| `.lux-feature-eyebrow` | 기능 아이브로 (--fs-xs, letter-spacing:4px, --lux-accent, uppercase) |
| `.lux-feature-title` | 기능 제목 (44px, --fw-extrabold, --lux-text, letter-spacing:-1.5px) |
| `.lux-feature-desc` | 기능 설명 (16px, --lux-muted, line-height:1.7, mb:28px) |
| `.lux-feature-list` | 기능 항목 리스트 (list-style:none). `li`는 체크마크 ::before 적용 |
| `.lux-feature-visual` | 폰 목업 시각화 영역 (flex, center) |
| `.lux-phone` | 폰 목업 외곽 (280×580px, --lux-dark bg, border-radius:36px, shadow) |
| `.lux-phone-notch` | 노치 (absolute, top, width:100px, --lux-dark bg) |
| `.lux-phone-screen` | 폰 화면 (white bg, border-radius:28px, overflow:hidden, flex-column) |
| `.lux-phone-screen--bg` | 화면 배경색 변형 (--bg) |
| `.lux-ph-status` | 상태바 (padding:14px 22px 8px, flex, space-between) |
| `.lux-ph-status--white` | 상태바 흰 배경 변형 |
| `.lux-ph-header` | 화면 헤더 (--sp-8 22px, --fs-xl, --fw-extrabold, border-bottom) |
| `.lux-ph-content` | 화면 콘텐츠 영역 (flex:1, padding:--sp-8 16px, overflow:hidden) |
| `.lux-ph-leave-card` | 연차 카드 (gradient --lux-accent, --radius-xl, padding:--sp-8 18px, white) |
| `.lux-ph-card-label` | 카드 레이블 (--fs-sm, opacity:.9, --fw-semibold) |
| `.lux-ph-card-num` | 카드 큰 숫자 (48px, --fw-extrabold, letter-spacing:-2px) |
| `.lux-ph-card-unit` | 카드 단위 (18px, ml:--sp-1) |
| `.lux-ph-card-bar` | 프로그레스 바 트랙 (height:5px, rgba(255,255,255,.25)) |
| `.lux-ph-card-bar-fill` | 채워진 바 (height:100%, white bg) |
| `.lux-ph-card-meta` | 카드 메타 (--fs-xs, opacity:.85, mt:--sp-2) |
| `.lux-ph-section-label` | 화면 내 섹션 레이블 (--fs-xs, --fw-bold, #666, mt:14px) |
| `.lux-ph-item` | 화면 내 리스트 아이템 (flex, space-between, --lux-base bg, --radius-md, mt:--sp-3) |
| `.lux-ph-item-title` | 아이템 제목 (--fs-sm, --fw-bold) |
| `.lux-ph-item-meta` | 아이템 메타 (--fs-2xs, #666, mt:2px) |
| `.lux-ph-item-badge` | 아이템 뱃지 (--fs-2xs, --fw-bold, --radius-md, --badge-other-bg) |
| `.lux-ph-item-badge--proc` | 처리중 변형 (--badge-emp-bg) |
| `.lux-ph-item-badge--wait` | 대기 변형 (#fef3c7, #854d0e) |
| `.lux-ph-notice` | 화면 내 공지 (--radius-md, #eef2ff bg, #3730a3, --fs-xs, line-height:1.5) |
| `.lux-ph-scan-zone` | QR 스캔 존 (border-dashed, --radius-lg, padding:24px 12px, text-center) |
| `.lux-ph-scan-icon` | 스캔 아이콘 (font-size:42px) |
| `.lux-ph-scan-title` | 스캔 타이틀 (--fs-body, --fw-extrabold, #111, mt:--sp-3) |
| `.lux-ph-scan-sub` | 스캔 서브 (--fs-2xs, #666, mt:3px) |
| `.lux-ph-titlebar` | 화면 타이틀바 (white bg, padding:10px 14px, border-bottom, flex) |
| `.lux-ph-back-btn` | 뒤로가기 버튼 (26×26px, --surface-hover bg, border-radius:7px) |
| `.lux-ph-titlebar-text` | 타이틀바 텍스트 (--fs-md, --fw-bold) |
| `.lux-ph-tabbar` | 화면 탭바 (white bg, flex, gap:5px, padding:8px 10px, border-bottom) |
| `.lux-ph-tab` | 탭 버튼 (--fs-2xs, --fw-semibold, --surface-hover bg, border-radius:20px). `.active` → --accent |
| `.lux-ph-list` | 리스트 영역 (flex:1, overflow:hidden, padding:8px 10px, flex-column, gap) |
| `.lux-ph-fab-wrap` | FAB 버튼 래퍼 (padding:0 10px 12px, justify-content:flex-end) |
| `.lux-ph-fab` | FAB 버튼 (36×36px, gradient --accent, border-radius:50%, shadow) |
| `.lux-pricing` | 가격 정책 섹션 (padding:120px 80px, text-center, gradient bg) |
| `.lux-pricing-eyebrow` | 가격 아이브로 (--fs-sm, letter-spacing:4px, --lux-accent, uppercase) |
| `.lux-pricing-title` | 가격 제목 (48px, --fw-extrabold, letter-spacing:-1.5px) |
| `.lux-pricing-sub` | 가격 서브 (--fs-xl, --lux-muted, mb:56px) |
| `.lux-price-grid` | 가격 카드 그리드 (grid, repeat(3,1fr), max-width:800px, mx:auto) |
| `.lux-price-card` | 가격 카드 (white bg, border-radius:16px, padding:36px 24px, hover:translateY(-2px)) |
| `.lux-price-card-main` | 주요 가격 카드 (gradient --lux-accent, 흰 글자) |
| `.lux-price-card-bonus` | 보너스 가격 카드 (노랑 gradient, #92400e) |
| `.lux-price-label` | 가격 카드 레이블 (--fs-body, --fw-bold, #6b7280) |
| `.lux-price-value` | 가격 수치 (36px, --fw-extrabold, letter-spacing:-1px) |
| `.lux-price-tag` | 가격 태그 (--fs-xs, --lux-subtle, uppercase, letter-spacing:1px) |

### 2-6. onboard (모바일 안내)

**레이아웃 구조:** 위에 `.onb` 패널 (QR + 안내텍스트), 아래에 직원 설치 현황 패널

```html
<div class="page" data-page="onboard">

  <!-- ① QR + 안내 텍스트 패널 -->
  <div class="onb">
    <div class="qr-side">
      <div class="qr-box" id="qrBox">
        <svg><!-- QR SVG --></svg>
        <div class="logo-overlay">MY</div>
      </div>
      <div class="qr-hint">스마트폰 카메라로 스캔</div>
      <div class="url-chip">my-insa.app/install</div>
    </div>

    <div class="info-side">  <!-- ← onb-content 아님, info-side 사용 -->
      <h2>모바일 앱 설치 안내</h2>
      <p>안내 텍스트</p>
      <div class="step"><span class="n">1</span><div class="t">단계 설명</div></div>
      <!-- 2, 3 단계 동일 패턴 -->
      <div class="onb-actions">
        <a href="#none" class="btn">QR 다운로드</a>
        <a href="#none" class="btn">링크 복사</a>
      </div>
    </div>
  </div>

  <!-- ② 직원 설치 현황 패널 (chip-bar + 테이블) -->
  <div class="panel">
    <div class="panel-head">
      <h3>직원 설치 현황</h3>
      <div class="stat-row">
        <span class="stat-label">미설치 <strong class="text-danger" id="notInstCount">0</strong>명</span>
      </div>
    </div>
    <!-- 필터 칩 -->
    <div class="chip-bar">
      <a href="#none" class="btn btn-sm active" id="ifilter-all" onclick="setInstFilter('all')">전체</a>
      <a href="#none" class="btn btn-sm" id="ifilter-not" onclick="setInstFilter('not')">미설치만</a>
      <a href="#none" class="btn btn-sm" id="ifilter-done" onclick="setInstFilter('done')">설치 완료만</a>
    </div>
    <table class="tbl" id="installTable">
      <thead>
        <tr>
          <th style="width:110px">이름</th>
          <th style="width:110px">부서</th>
          <th style="width:90px">직급</th>
          <th style="width:140px">연락처</th>
          <th class="center" style="width:110px">설치 상태</th>
          <th class="center" style="width:110px">최근 활동</th>
          <th class="center" style="width:150px">카카오톡 발송</th>
        </tr>
      </thead>
      <tbody id="installTbody"><!-- JS로 렌더 — 각 행 카카오 발송 버튼에 .kakao-btn 사용 --></tbody>
    </table>
  </div>
</div>
```

> **`.kakao-btn` 사용 위치:** `installTbody` 행은 JS로 렌더링되며, 각 행의 발송 버튼에 `.kakao-btn` 클래스 사용. `.onb .info-side` 안에는 kakao 버튼 없음.

#### onb 클래스 목록

| 클래스 | 설명 |
|---|---|
| `.onb` | 모바일 안내 컨테이너 (flex, --surface, --radius-xl, overflow:hidden, mb:--sp-9) |
| `.onb .qr-side` | QR 사이드 (text-center, padding:32px 28px, border-right, width:260px) |
| `.onb .info-side` | 안내 텍스트 사이드 (flex:1, padding:32px). **`onb-content` 아님** |
| `.onb .step` | 설치 단계 항목 (flex, gap:--sp-4, mb:--sp-4, align-items:flex-start) |
| `.onb .step .n` | 단계 번호 원형 (20×20px, border-radius:50%, --bg, --fs-xs, border) |
| `.onb .step .t` | 단계 텍스트 (--fs-body, line-height:1.4) |
| `.onb .qr-hint` | QR 하단 힌트 텍스트 (--fs-sm, --text-secondary, mb:--sp-3) |
| `.onb .url-chip` | URL 칩 (--fs-xs, --accent, --accent-light bg, padding:4px 12px, border-radius:20px) |
| `.onb-actions` | 액션 버튼 행 (flex, gap:--sp-3, mt:--sp-9) |
| `.chip-bar` | 필터 칩 바 (flex, gap:--sp-2, padding:--sp-4 --sp-8, border-bottom). `.btn.active` → --accent bg |

### 2-7. emp-dashboard (직원 대시보드)

```html
<div class="page" data-page="emp-dashboard">
  <!-- 상단: 연차 현황 카드 -->
  <div class="panel panel--outlined mb-9">
    <div class="panel-body panel-body--lg">
      <div class="leave-remain-grid">
        <!-- 좌: 수치 + 프로그레스 -->
        <div>
          <div class="leave-card-hd">
            <div>
              <div class="leave-card-label">잔여 연차</div>
              <div class="leave-num-row">
                <span class="leave-num-big">11</span>
                <span class="leave-num-unit">일</span>
              </div>
            </div>
            <div class="leave-stat-chips">
              <div class="leave-stat-chip"><span class="leave-stat-num">15</span>총</div>
              <div class="leave-stat-chip"><span class="leave-stat-num">4</span>사용</div>
            </div>
          </div>
          <div class="leave-progress">
            <div class="leave-progress-fill" style="width:26.7%"></div>
          </div>
          <div class="leave-progress-meta">
            <span>사용 4일</span>
            <span>잔여 11일 / 총 15일</span>
          </div>
        </div>
        <!-- 우: QR -->
        <div class="qr-card">
          <div class="qr-box">
            <svg><!-- QR SVG --></svg>
            <div class="qr-badge">MY</div>
          </div>
          <div>
            <div class="qr-title">모바일 앱 설치</div>
            <div class="qr-desc">QR 스캔으로 언제 어디서나<br>연차·증명서 신청</div>
            <div class="qr-link">my-insa.app/install</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- 중간: 2열 그리드 -->
  <div class="emp-dash-grid">
    <!-- 연차 사용 이력 -->
    <div class="panel">
      <div class="panel-head"><h3>연차 사용 이력</h3></div>
      <div class="p-0">
        <table class="tbl">
          <colgroup>
            <col style="width:148px">
            <col style="width:96px">
            <col style="width:76px">
            <col>
            <col style="width:80px">
          </colgroup>
          <thead>
            <tr><th>날짜</th><th>구분</th><th class="center">일수</th><th>사유</th><th class="center">상태</th></tr>
          </thead>
          <tbody>
            <tr>
              <td>2026.05.04 (월)</td>
              <td>연차</td>
              <td class="center val-accent">1일</td>
              <td>가족 행사</td>
              <td class="center"><span class="badge w">대기</span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- 증명서 관리 -->
    <div class="panel">
      <div class="panel-head">
        <h3>증명서 관리</h3>
        <a href="#none" class="btn btn-sm btn-primary" onclick="openEmpCertModal()">
          <!-- + SVG --> 증명서 신청
        </a>
      </div>
      <div class="panel-body p-0">
        <div class="panel-inner">
          <div class="section-label">최근 신청 내역</div>
          <div class="cert-row">
            <div>
              <div class="item-name">재직증명서</div>
              <div class="item-meta">2026.04.29 · 은행 대출</div>
            </div>
            <span class="badge w">대기</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

**`val-accent`** — 수치 강조 텍스트 색상
**`panel--outlined`** — 테두리 강조 패널 변형
**`leave-progress-fill`** — `style="width:XX%"` 인라인으로 사용률 표시

### 2-8. emp-cert (직원 업무신청)

```html
<div class="page" data-page="emp-cert">
  <div class="toolbar">
    <div class="filters">
      <select class="filter-input"><!-- 종류 --></select>
      <select class="filter-input"><!-- 상태 --></select>
    </div>
    <a href="#none" class="btn btn-primary" onclick="openEmpCertModal()">
      <!-- + SVG --> 증명서 신청
    </a>
  </div>

  <div class="table-wrap">
    <table class="tbl">
      <thead>
        <tr>
          <th style="width:160px">신청일</th>
          <th style="width:150px">증명서 종류</th>
          <th>제출처 / 용도</th>
          <th class="center" style="width:60px">부수</th>
          <th class="center" style="width:110px">상태</th>
          <th style="width:130px"></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>날짜</td>
          <td><span class="cert-badge cert-badge-emp">재직증명서</span></td>
          <td>용도</td>
          <td class="center">1</td>
          <td class="center"><span class="badge w">대기</span></td>
          <td>
            <!-- 대기: 신청취소 -->
            <!-- 발급완료: 다운로드 (btn-primary) -->
            <!-- 반려: 사유보기 -->
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```

### 2-9. emp-profile (직원 프로필 관리)

툴바 대신 `profile-actions` 사용 — 수정/저장/취소 버튼

```html
<div class="page" data-page="emp-profile">
  <!-- 툴바 대체 버튼 영역 -->
  <div class="profile-actions" id="profileActions">
    <a href="#none" id="profileEditBtn" class="btn" onclick="setProfileMode('edit')"><!-- 편집 SVG --> 수정</a>
    <a href="#none" id="profileCancelBtn" class="btn" style="display:none" onclick="setProfileMode('view')">취소</a>
    <a href="#none" id="profileSaveBtn" class="btn btn-primary" style="display:none" onclick="saveProfile()"><!-- 체크 SVG --> 저장</a>
  </div>

  <div class="profile-layout">
    <!-- 좌측 열 -->
    <div class="flex-col">
      <!-- 프로필 사진 패널 -->
      <div class="panel">
        <div class="panel-head"><h3>프로필 사진</h3></div>
        <div class="panel-body panel-body--center">
          <div class="profile-avatar-wrap">
            <div class="profile-avatar">홍</div>
            <a href="#none" id="profilePhotoEditBtn" class="profile-photo-btn" style="display:none"
              onclick="document.getElementById('profilePhotoInput').click()">
              <!-- 카메라 SVG -->
            </a>
            <input type="file" id="profilePhotoInput" accept="image/*" style="display:none"
              onchange="if(this.files&&this.files.length){showToastV7('✅ 프로필이 설정되었습니다.');this.value='';}">
          </div>
          <div class="profile-name">홍길동</div>
          <div class="profile-dept">마케팅팀 · 대리</div>
        </div>
      </div>

      <!-- 인사 기본 정보 패널 (읽기 전용) -->
      <div class="panel">
        <div class="panel-head"><h3>인사 기본 정보</h3></div>
        <div class="panel-body p-0">
          <div class="hr-info-row"><span class="text-secondary">사번</span><strong>EMP-2103</strong></div>
          <div class="hr-info-row"><span class="text-secondary">입사일</span><strong>2021.03.02</strong></div>
          <!-- 부서/직급/고용형태 동일 패턴 -->
        </div>
      </div>
    </div>

    <!-- 우측 열 -->
    <div class="flex-col">
      <!-- 개인 정보 패널 -->
      <div class="panel">
        <div class="panel-head"><h3>개인 정보</h3></div>
        <div class="panel-body">
          <!-- 조회 모드 -->
          <div id="profileViewMode">
            <div class="profile-view-grid">
              <div class="profile-view-cell">
                <div class="profile-view-label">이름</div>
                <div class="item-title">홍길동</div>
              </div>
              <!-- 영문이름/이메일/연락처 동일 패턴 -->
            </div>
          </div>
          <!-- 수정 모드 (기본 숨김) -->
          <div id="profileEditMode" style="display:none">
            <div class="form-row">
              <div class="field"><label>이름 <span class="req">*</span></label><input type="text" value="홍길동" /></div>
              <div class="field"><label>영문 이름</label><input type="text" /></div>
            </div>
          </div>
        </div>
      </div>

      <!-- 내 인사 정보 패널 -->
      <div class="panel">
        <div class="panel-head">
          <h3>내 인사 정보</h3>
          <a href="#none" id="hrInfoAddBtn" class="btn btn-sm" style="display:none" onclick="openHrInfoModal()">
            <!-- + SVG --> 항목 추가
          </a>
        </div>
        <div class="panel-body p-0">
          <!-- 자격증 섹션 -->
          <div class="hr-section">
            <div class="section-label">자격증</div>
            <div class="hr-item-list">
              <div class="hr-item">
                <div class="hr-item-inner">
                  <div class="hr-item-icon hr-item-icon--cert"><!-- SVG --></div>
                  <div>
                    <div class="item-title">정보처리기사</div>
                    <div class="item-meta">한국산업인력공단 · 2020.06.05</div>
                  </div>
                </div>
                <!-- 수정 모드에서만 표시 -->
                <a href="#none" class="icon-btn hr-edit-btn" style="display:none"
                  onclick="openHrEditModal('cert',...)"><!-- 편집 SVG --></a>
              </div>
            </div>
          </div>
          <!-- 학력(.hr-item-icon--edu), 어학(.hr-item-icon--lang) 동일 구조 -->
        </div>
      </div>
    </div>
  </div>
</div>
```

**모드 전환 JS:** `setProfileMode('edit'|'view')`
**hr-item-icon 색상:** `--cert`(파랑), `--edu`(보라), `--lang`(갈색)
**`profile-actions`** — `.toolbar` 자리에 위치, 페이지 내 첫 번째 요소

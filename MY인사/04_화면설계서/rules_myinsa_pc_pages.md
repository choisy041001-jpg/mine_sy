# MY인사 PC — 화면별 패턴 규칙

> 기준 파일: `MY인사_PC_v31.html`  
> 공통 컴포넌트는 `rules_myinsa_pc_components.md` 참고.  
> 모든 화면은 `.content` 안의 `.page[data-page="..."]` 단위로 구성.

---

## 관리자 화면

---

### P-01. 관리자 대시보드 (`data-page="dashboard"`)

#### 레이아웃 구성

```
[ KPI 카드 × 4 ]          .kpi-grid (repeat(4,1fr), gap:12px)
─────────────────────────────────────────────────
[ 미처리 업무 패널 ]  [ 직원 현황 패널 ]
   1fr                  1.6fr
          .dash-grid
```

```html
<div class="page active" data-page="dashboard">

  <!-- KPI 4개 -->
  <div class="kpi-grid">
    <div class="kpi kpi-leave" onclick="goPageByName('leave')">...</div>
    <div class="kpi kpi-faq"   onclick="goPageByName('faq')">...</div>
    <div class="kpi kpi-cert"  onclick="goPageByName('cert')">...</div>
    <div class="kpi kpi-total">...</div>
  </div>

  <!-- 2단 그리드 -->
  <div class="dash-grid">   <!-- CSS .dash-grid 클래스 사용 — 인라인 스타일 불필요 -->

    <!-- 좌: 미처리 업무 패널 -->
    <div class="panel">
      <div class="panel-head"><h3>미처리 업무 (10)</h3></div>
      <div style="padding:0">
        <div class="alert-row" onclick="..." style="cursor:pointer">...</div>
        <!-- alert-row 반복 -->
      </div>
    </div>

    <!-- 우: 직원 현황 패널 -->
    <div class="panel" style="align-self:start">
      <div class="panel-head"><h3>직원 현황</h3></div>
      <!-- 탭 필터 -->
      <div style="border-bottom:1px solid var(--border);display:flex;gap:0">
        <a href="#none" class="emp-tab-btn active" onclick="filterEmpStatus(this,'all')"  data-status="all" >전체 <span class="emp-tab-count">9</span></a>
        <a href="#none" class="emp-tab-btn"        onclick="filterEmpStatus(this,'재직')" data-status="재직">재직 <span class="emp-tab-count">8</span></a>
        <a href="#none" class="emp-tab-btn"        onclick="filterEmpStatus(this,'휴직')" data-status="휴직">휴직 <span class="emp-tab-count">1</span></a>
        <a href="#none" class="emp-tab-btn"        onclick="filterEmpStatus(this,'퇴사')" data-status="퇴사">퇴직 <span class="emp-tab-count">1</span></a>
      </div>
      <!-- 직원 테이블 -->
      <div class="emp-tbl-wrap">
        <table class="tbl emp-tbl" id="empStatusTable">
          <thead>
            <tr>
              <th style="width:100px">이름</th>
              <th style="width:110px">부서</th>
              <th style="width:100px">직급</th>
              <th style="width:110px">입사일</th>
              <th style="width:130px">연락처</th>
              <th class="center" style="width:70px">상태</th>
            </tr>
          </thead>
          <tbody>
            <tr data-emp-status="재직">
              <td><strong>홍길동</strong></td>
              <td>마케팅팀</td><td>대리</td>
              <td>2024.03.04</td><td>010-1234-5678</td>
              <td class="center"><span class="badge s">재직</span></td>
            </tr>
            <!-- data-emp-status="휴직" → badge.w / "퇴사" → badge.d -->
          </tbody>
        </table>
      </div>
    </div>

  </div>
</div>
```

#### 직원 현황 탭 버튼 스타일

```css
.emp-tab-btn {
  padding:10px 16px 11px; font-size:13px; font-weight:500;
  color:var(--text-secondary); cursor:pointer;
  border:none; background:transparent;
  border-bottom:2px solid transparent; margin-bottom:-1px;
  transition:.15s; font-family:inherit;
  display:inline-flex; align-items:center; gap:5px;
}
.emp-tab-btn:hover  { color:var(--text) }
.emp-tab-btn.active { color:var(--accent); border-bottom-color:var(--accent); font-weight:700 }
.emp-tab-count { font-size:11px; font-weight:700; color:inherit; opacity:.75 }
```

#### 탭 필터 JS 패턴

```js
function filterEmpStatus(btn, status) {
  // 탭 active 전환
  document.querySelectorAll('.emp-tab-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  // 행 표시/숨김
  document.querySelectorAll('#empStatusTable tbody tr').forEach(tr => {
    tr.style.display = (status === 'all' || tr.dataset.empStatus === status) ? '' : 'none';
  });
}
```

---

### P-02. 연차 관리 (`data-page="leave"`)

#### 레이아웃 구성

```
[ 툴바: 연도 필터 + 직원명 검색 + 안내 문구 | 불러오기 버튼 + 엑셀 다운로드 ]
[ 월별 연차 테이블 (가로 스크롤 + 세로 스크롤) ]
```

```html
<div class="page" data-page="leave">

  <div class="toolbar">
    <div class="filters">
      <select class="filter-input" id="leaveYearFilter"><option>2026년</option>...</select>
      <!-- 부서 필터 미제공: 경리나라에 부서 공식 등록 메뉴 없음 (테이블 부서 컬럼은 표시용 유지) -->
      <input  class="filter-input" placeholder="직원명 검색" />
      <!-- 안내 문구 -->
      <span style="display:flex;align-items:center;gap:5px;font-size:12px;color:var(--info);white-space:nowrap">
        <svg .../>
        총연차를 클릭하시면 연차를 등록할 수 있습니다.
      </span>
    </div>
    <div style="display:flex;gap:8px">
      <a href="#none" class="btn" onclick="openImportEmpModal()">+ 일반근로자 불러오기</a>
      <a href="#none" class="btn">엑셀 다운로드</a>
    </div>
  </div>

  <!-- X/Y 스크롤 분리 구조 — 동일 요소에 양방향 overflow 지정 시 sticky 헤더 깨짐 방지 -->
  <div class="table-wrap leave-table">
    <div style="overflow-x:auto">
    <div class="scroll-body" id="leaveScroll" style="max-height:calc(100vh - 260px);overflow-y:auto;overflow-x:visible">
    <table class="tbl" style="min-width:1100px">
      <thead>
        <tr>
          <!-- 직원 컬럼: sticky left — .col-sticky-l 클래스 사용 -->
          <th class="col-sticky-l" style="width:120px">직원</th>
          <th style="width:90px">부서</th>
          <!-- 총 연차: 클릭 편집 영역 -->
          <th class="num" style="width:80px">총 연차</th>
          <th class="num" style="width:60px">사용</th>
          <!-- 월별 헤더 (1월~12월) -->
          <th class="num month-h" style="width:52px">1월</th>
          ...
          <th class="num month-h" style="width:52px">12월</th>
          <!-- 잔여 컬럼: sticky right — .col-sticky-r 클래스 사용 -->
          <th class="num remain col-sticky-r" style="width:60px">잔여</th>
        </tr>
      </thead>
      <tbody id="leaveTbody">
        <tr>
          <!-- 직원명: sticky left, 클릭 시 사이드 패널 열기 — .col-sticky-l 클래스 사용 -->
          <td class="col-sticky-l">
            <!-- openLeavePanel(empName, dept, total, used, remain, status) -->
            <!-- status: 'wait'(대기 있음) | 'none'(대기 없음) -->
            <strong class="accent-link"
              onclick="openLeavePanel('홍길동','마케팅팀',15,2,13,'wait')">홍길동</strong>
          </td>
          <td>마케팅팀</td>
          <!-- 총연차: 더블클릭 인라인 편집 — .leave-total-cell 클래스 사용 -->
          <td class="num editable leave-total-cell" ondblclick="editTotal(this)" data-emp="홍길동">15</td>
          <td class="num used">2</td>
          <!-- 1월~12월: 0이 아닌 값은 month-used-nonzero 클래스 자동 적용 -->
          <td class="num">1</td><td class="num">0</td>...
          <!-- 잔여: sticky right — .col-sticky-r 클래스 사용 -->
          <td class="num col-sticky-r">13</td>
        </tr>
      </tbody>
    </table>
    </div>
    </div>
  </div>

</div>
```

#### 연차 테이블 컬럼 클래스

| 클래스 | 적용 위치 | 스타일 |
|--------|-----------|--------|
| `.month-h` | 월별 헤더 th (`1월`~`12월`) | `font-weight:500`, `color:var(--text-secondary)`, `background:var(--bg)` |
| `.used` | 사용 연차 td | `color:var(--text-secondary)` (흐린 텍스트) |
| `.remain` | 잔여 연차 td | `font-weight:600`, `color:var(--text)` (강조 텍스트) |

> 세 클래스 모두 `.leave-table` 범위 내에서만 적용됨 (`.leave-table .month-h` 선택자).

#### 총연차 셀 스타일 (클릭 편집 식별용)

```css
/* 총연차 셀 — 인라인 편집 가능 표시 */
background: #eff5fa;
color: #1b4d82;
font-weight: 600;
cursor: pointer;
border-left: 1px solid #bfd9ef;
border-right: 1px solid #bfd9ef;
```

#### 월별 0이 아닌 값 셀 (.month-used-nonzero)

```css
.month-used-nonzero {
  color: #0f3870 !important;
  font-weight: 600 !important;
  cursor: pointer;
}
.month-used-nonzero:hover {
  text-decoration: underline;
  text-underline-offset: 3px;
  text-decoration-color: #0f3870;
}
.month-used-nonzero.tt-active {
  text-decoration: underline;
  text-underline-offset: 3px;
  background: var(--accent-light);
}
```

#### 월별 툴팁 데이터 구조

```js
const LEAVE_MONTH_DETAILS = {
  '홍길동': {
    1: ['01월 08일 연차(1일)'],
    3: ['03월 12일 오전반차(0.5일)'],
    4: ['04월 15일 오후반차(0.5일)'],
  },
  // 직원명: { 월번호: ['날짜 종류(일수)', ...] }
};
```

---

### P-03. 업무신청관리 (`data-page="cert"`)

#### 레이아웃 구성

```
[ 툴바: 종류/상태/날짜범위/신청자 필터 | 엑셀 다운로드 ]
[ 증명서 신청 목록 테이블 ]
```

```html
<div class="page" data-page="cert">

  <div class="toolbar">
    <div class="filters">
      <select class="filter-input"><option>전체 종류</option>...</select>
      <select class="filter-input"><option>전체 상태</option>...</select>
      <input class="filter-input" type="date" value="2026-04-01" />
      <span style="color:var(--text-tertiary);font-size:12px">~</span>
      <input class="filter-input" type="date" value="2026-04-30" />
      <input class="filter-input" placeholder="신청자 이름" />
    </div>
    <!-- 엑셀 다운로드 버튼 없음 — PC 관리자 목록에 미존재(v2.1) -->
  </div>

  <div class="table-wrap">
    <div class="scroll-body" id="certScroll">
    <table class="tbl" style="table-layout:fixed">
      <thead>
        <tr>
          <th style="width:140px">신청일</th>
          <th style="width:120px">신청자</th>
          <th style="width:130px">증명서 종류</th>
          <!-- 부수(발급 매수) 컬럼 없음 — 발급 매수 기능 제외(v2.1) -->
          <th>제출처 / 용도</th>
          <th class="center" style="width:100px">상태</th>
          <th style="width:160px"></th>
        </tr>
      </thead>
      <tbody id="certTbody">
        <!-- 대기 -->
        <tr>
          <td>2026.04.29 09:14</td>
          <td><strong>홍길동</strong></td>
          <td><span class="cert-badge cert-badge-emp">재직증명서</span></td>
          <td>은행 대출 신청용</td>
          <td class="center"><span class="badge w">대기</span></td>
          <td><div class="row-actions">
            <a href="#none" class="btn btn-sm btn-primary"
              onclick="openCertIssueModal('홍길동','재직증명서','은행 대출 신청용','2026.04.29 09:14')">발급처리</a>
          </div></td>
        </tr>
        <!-- 발급완료 -->
        <tr>
          ...
          <td class="center"><span class="badge s">발급완료</span></td>
          <td><div class="row-actions">
            <a href="#none" class="btn btn-sm" onclick="openCertHistoryModal(...)">이력보기</a>
          </div></td>
        </tr>
        <!-- 반려 -->
        <tr>
          ...
          <td class="center"><span class="badge d">반려</span></td>
          <td><div class="row-actions">
            <a href="#none" class="btn btn-sm" onclick="openCertReasonModal(...)">사유보기</a>
          </div></td>
        </tr>
      </tbody>
    </table>
    </div>
  </div>

</div>
```

#### 상태별 액션 버튼 규칙

| 상태 | 뱃지 | 버튼 | onclick |
|------|------|------|---------|
| 대기 | `.badge.w` | `btn-primary` "발급처리" | `openCertIssueModal(...)` |
| 발급완료 | `.badge.s` | `.btn` "이력보기" | `openCertHistoryModal(...)` |
| 반려 | `.badge.d` | `.btn` "사유보기" | `openCertReasonModal(...)` |

#### 모달 ID 및 함수 시그니처

| 함수 | 모달 ID | 인자 |
|------|---------|------|
| `openCertIssueModal(name, kind, purpose, date)` | `#certIssueModalBg` | 직원명, 증명서종류, 용도, 신청일시 |
| `openCertHistoryModal(name, kind, date)` | `#certHistoryModalBg` | 직원명, 증명서종류, 발급일 |
| `openCertReasonModal(name, reason)` | `#certReasonModalBg` | 직원명, 반려사유 |

---

### P-04. FAQ 관리 (`data-page="faq"`)

#### 레이아웃 구성

```
[ 카테고리 칩 필터 (전체/연차/증명서/급여/기타) | FAQ 등록 버튼 ]
[ FAQ 목록 테이블 ]
```

```html
<div class="page" data-page="faq">

  <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px;margin-bottom:14px">
    <div style="display:flex;align-items:center;gap:6px;flex-wrap:wrap">
      <a href="#none" class="faq-cat-btn active" onclick="filterFaqCommonTable(this,'all')">전체</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'연차')">연차</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'증명서')">증명서</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'급여')">급여</a>
      <a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'기타')">기타</a>
    </div>
    <a href="#none" class="btn btn-primary btn-sm"
      onclick="document.getElementById('addFaqModal').classList.add('show')">
      <svg .../>  FAQ 등록
    </a>
  </div>

  <div class="table-wrap">
    <table class="tbl" id="faqCommonTable" style="table-layout:auto">
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
          <td>연차는 언제부터 사용할 수 있나요?</td>
          <td style="color:var(--text-secondary)">입사 후 1개월 이상 근무 시 월 1일씩 발생…</td>
          <td class="center"><span class="badge s">노출 중</span></td>
          <td class="center"><a href="#none" class="btn btn-sm" onclick="openFaqEdit(...)">수정</a></td>
        </tr>
        <!-- data-faq-cat="증명서" / "급여" / "기타" -->
        <!-- 노출 상태: badge.s "노출 중" / badge.muted "비노출" -->
      </tbody>
    </table>
  </div>

</div>
```

#### FAQ 카테고리 칩 버튼 스타일

```css
.faq-cat-btn {
  height:30px; padding:0 14px; font-size:12px; font-weight:500;
  border-radius:var(--radius-md); cursor:pointer;
  border:1px solid var(--border);
  background:var(--surface); color:var(--text-secondary);
  transition:.15s; font-family:inherit;
}
.faq-cat-btn:hover  { border-color:var(--accent); color:var(--accent) }
.faq-cat-btn.active { background:var(--accent); color:#fff; border-color:var(--accent); font-weight:600 }
```

#### 카테고리별 뱃지 색상

| 카테고리 | 뱃지 클래스 | 색상 |
|---------|-----------|------|
| 연차 | `.badge.i` | 파랑(info) |
| 증명서 | 인라인 스타일 | `background:#deeef8; color:#2363a8` |
| 급여 | 인라인 스타일 | `background:#fef9c3; color:#854d0e` |
| 기타 | 인라인 스타일 | `background:#f3f4f6; color:#4b5563` |

#### 수정 모달 함수 시그니처

`openFaqEdit(cat, q, a, btnEl)` → `#editFaqModal` 오픈

| 인자 | 설명 |
|------|------|
| `cat` | 카테고리명 (예: `'연차'`) |
| `q` | 질문 텍스트 |
| `a` | 답변 텍스트 |
| `btnEl` | 클릭된 버튼 요소 (수정 후 해당 행 업데이트에 사용) |

---

### P-05. MY인사 소개 (`data-page="intro"`)

#### 특징

`.content` 기본 `padding:24px`를 제거하고 전체 너비를 사용하는 랜딩 페이지 스타일.  
`data-page="intro"` 페이지는 `id="screen-intro"`를 함께 붙인다.  
CSS의 `#screen-intro { margin:-24px -24px; overflow-y:auto }` 가 padding을 상쇄하므로 인라인 스타일 불필요.  
다른 페이지와 달리 액센트 색상을 `#4F46E5`(인디고)로 사용 — `var(--accent)` 파란 계열과 다름.

#### 레이아웃 구성

```
[ .lux-hero          — 히어로 섹션 ]
[ .lux-feature       — 01 연차 ]
[ .lux-feature.reverse — 02 증명서 ]
[ .lux-feature       — 03 경비 ]
[ .lux-feature.reverse — 04 FAQ (읽기 전용 FAQ 목록 목업) ]
```

> 가격 정책 섹션(`.lux-pricing`)은 **노출하지 않는다**(제거됨). 증명서(02) 블록은 앱·PC 다운로드 수령 기준(이메일 발송 아님).

#### HTML 골격

```html
<div class="page" id="screen-intro" data-page="intro">
<!-- CSS #screen-intro { margin:-24px -24px; overflow-y:auto } 로 처리 — 인라인 스타일 불필요 -->

  <!-- 히어로 -->
  <div class="lux-hero">
    <div class="lux-hero-eyebrow">FOR EXECUTIVES</div>
    <h1 class="lux-hero-title">하나의 앱으로,<br>인사의 모든 순간</h1>
    <p class="lux-hero-sub">연차, 증명서, 경비, FAQ — 직원의 손끝에서 시작되어 0.3초 만에 결재됩니다</p>
  </div>

  <!-- 기능 소개 (홀수 섹션: 기본 / 짝수 섹션: .reverse) -->
  <div class="lux-feature">          <!-- 01 연차 -->
    <div class="lux-feature-content">
      <div class="lux-feature-num">01</div>
      <div class="lux-feature-eyebrow">CALENDAR</div>
      <h2 class="lux-feature-title">연차, 더 이상 결재서가 아닙니다</h2>
      <p class="lux-feature-desc">캘린더 위에서 단 두 번의 탭으로...</p>
      <ul class="lux-feature-list">
        <li>직관적 캘린더 인터페이스</li>
        <li>실시간 결재 진행 알림</li>
        <li>팀 일정과 자동 연동</li>
      </ul>
    </div>
    <div class="lux-feature-visual">
      <div class="lux-phone">
        <div class="lux-phone-notch"></div>
        <div class="lux-phone-screen">
          <div class="lux-ph-status"><span>9:41</span><span>📶 🔋</span></div>
          <div class="lux-ph-header">📅 연차</div>
          <div class="lux-ph-content">
            <div class="lux-ph-leave-card">잔여 연차 12일...</div>
            <div class="lux-ph-item">4월 8-9일 · 승인</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- 02 증명서 (.reverse): 수령 방식 = 앱·PC 다운로드(이메일 발송 아님) -->
  <!-- 03 경비, 04 FAQ (.reverse) — 동일 구조 반복 -->
  <!-- 04 FAQ 목업 = 읽기 전용 FAQ 목록(질문 카드 + 답변 요약). '내 질문' 탭·카테고리·답변 상태·FAB 없음 -->

  <!-- 가격 정책 섹션은 노출하지 않음 (.lux-pricing 제거됨) -->

</div>
```

#### lux-* CSS 클래스 요약

| 클래스 | 역할 | 주요 스타일 |
|--------|------|------------|
| `.lux-hero` | 히어로 섹션 | `padding:120px 80px 100px`, 텍스트 중앙 정렬 |
| `.lux-hero-title` | 메인 타이틀 | `font-size:64px`, `font-weight:800`, `color:#0f172a` |
| `.lux-hero-eyebrow` | 상단 라벨 | `font-size:12px`, `letter-spacing:4px`, `color:#4F46E5` |
| `.lux-feature` | 기능 소개 행 | `grid 1fr 1fr`, `gap:80px`, `padding:100px 80px` |
| `.lux-feature.reverse` | 좌우 반전 | content `order:2`, visual `order:1` |
| `.lux-feature-title` | 기능 타이틀 | `font-size:44px`, `font-weight:800`, `color:#0f172a` |
| `.lux-feature-list li` | 기능 목록 | `padding:10px 0 10px 32px`, `::before` 체크마크 |
| `.lux-phone` | 목업 폰 프레임 | `280×580px`, `background:#1f2937`, `border-radius:36px` |
| `.lux-phone-screen` | 폰 화면 영역 | `background:#fff`, `border-radius:28px` |
| `.lux-ph-leave-card` | 연차 현황 카드 | `background:linear-gradient(135deg,#4F46E5,#6366F1)`, 흰 텍스트 |
| `.lux-ph-item` | 목록 항목 행 | `background:#fafafa`, `border-radius:8px`, `padding:11px 13px` |

> `.lux-pricing` / `.lux-price-*`(가격 정책) 클래스는 **현재 화면에서 미사용**(가격 정책 섹션 제거). 스타일시트에 정의가 남아 있어도 P-005에서는 렌더링하지 않는다.

---

### P-06. 모바일 안내 (`data-page="onboard"`)

#### 레이아웃 구성

```
[ QR + 설치 안내 카드 (.onb) ]
[ 직원 설치 현황 패널 (.panel) ]
  └ 필터 칩 (전체/미설치만/설치 완료만)
  └ 설치 현황 테이블 (카카오톡 발송 포함)
```

```html
<div class="page" data-page="onboard">

  <!-- QR 안내 카드 -->
  <div class="onb">
    <div class="qr-side">
      <div class="qr-box" id="qrBox">
        <svg .../>   <!-- SVG QR 코드 -->
        <div class="logo-overlay">MY</div>
      </div>
      <div style="font-size:12px;color:var(--text-secondary);margin-bottom:8px">스마트폰 카메라로 스캔</div>
      <div style="font-size:11px;color:var(--accent);font-weight:600;background:var(--accent-light);padding:4px 12px;border-radius:20px">my-insa.app/install</div>
    </div>
    <div class="info-side">
      <h2>모바일 앱 설치 안내</h2>
      <p>...</p>
      <div class="step"><span class="n">1</span><div class="t">스마트폰으로 QR 스캔 또는 카카오톡 링크 클릭</div></div>
      <div class="step"><span class="n">2</span><div class="t">앱 설치 (또는 웹 모바일 진입)</div></div>
      <div class="step"><span class="n">3</span><div class="t">사번으로 로그인 → 바로 시작</div></div>
      <div style="display:flex;gap:8px;margin-top:20px">
        <a href="#none" class="btn">링크 복사</a>
      </div>
    </div>
  </div>

  <!-- 직원 설치 현황 -->
  <div class="panel">
    <div class="panel-head">
      <h3>직원 설치 현황</h3>
      <span style="font-size:12px;color:var(--text-tertiary)">
        미설치 <strong style="color:var(--danger)" id="notInstCount">4</strong>명
      </span>
    </div>
    <!-- 필터 칩 -->
    <div style="padding:10px 18px;display:flex;gap:6px;border-bottom:1px solid var(--border)">
      <a href="#none" class="btn btn-sm" id="ifilter-all"  onclick="setInstFilter('all')"  style="background:var(--accent);color:#fff;border-color:var(--accent)">전체</a>
      <a href="#none" class="btn btn-sm" id="ifilter-not"  onclick="setInstFilter('not')">미설치만</a>
      <a href="#none" class="btn btn-sm" id="ifilter-done" onclick="setInstFilter('done')">설치 완료만</a>
    </div>
    <!-- 설치 현황 테이블 (tbody는 JS로 렌더) -->
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
      <tbody id="installTbody"><!-- JS 렌더 --></tbody>
    </table>
  </div>

</div>
```

#### .onb 카드 구조

```css
.onb {
  display:flex; gap:0;
  background:var(--surface); border:none;
  border-radius:var(--radius-xl); margin-bottom:20px;
  box-shadow:var(--shadow-card); overflow:hidden;
}
.onb .qr-side {
  text-align:center; padding:32px 28px;
  border-right:1px solid var(--border);
  flex-shrink:0; width:260px;
  display:flex; flex-direction:column; align-items:center; justify-content:center;
}
.onb .info-side { flex:1; padding:32px 32px }
.onb .step { display:flex; gap:10px; margin-bottom:10px; align-items:flex-start }
.onb .step .n {
  width:20px; height:20px; border-radius:50%;
  background:var(--bg); border:1px solid var(--border);
  font-size:11px; font-weight:700; display:grid; place-items:center; flex-shrink:0;
}
```

---

## 직원 화면

---

### P-07. 직원 대시보드 (`data-page="emp-dashboard"`)

#### 레이아웃 구성

```
[ 연차 현황 카드 (잔여 수치 + QR 블록) ]         .panel
──────────────────────────────────────────────
[ 연차 사용 이력 테이블 ]  [ 증명서 관리 패널 ]
      1.4fr                    1fr
```

```html
<div class="page" data-page="emp-dashboard">

  <!-- ① 연차 현황 카드 -->
  <div class="panel" style="margin-bottom:20px;border:1.5px solid var(--border);box-shadow:none">
    <div class="panel-body" style="padding:20px 24px">
      <div style="display:grid;grid-template-columns:1fr auto;gap:24px;align-items:center">

        <!-- 좌: 잔여 연차 수치 + 프로그레스 바 -->
        <div>
          <div style="font-size:11px;font-weight:700;color:var(--accent-mid);text-transform:uppercase;letter-spacing:0.05em;margin-bottom:6px">잔여 연차</div>
          <div style="display:flex;align-items:baseline;gap:4px">
            <span style="font-size:52px;font-weight:700;color:var(--accent);line-height:1;letter-spacing:-0.04em;font-variant-numeric:tabular-nums">11</span>
            <span style="font-size:18px;font-weight:600;color:var(--accent-mid);margin-left:2px">일</span>
          </div>
          <!-- 총/사용 칩 -->
          <div style="display:flex;align-items:center;gap:8px;margin-top:6px">
            <div style="background:#fff;border:1px solid var(--accent-mid);border-radius:20px;padding:5px 14px;font-size:13px;font-weight:500;color:var(--text-secondary)">
              <span style="font-size:15px;font-weight:700;margin-right:3px;color:var(--accent)">15</span>총
            </div>
            <div style="background:#fff;border:1px solid var(--accent-mid);border-radius:20px;padding:5px 14px;font-size:13px;font-weight:500;color:var(--text-secondary)">
              <span style="font-size:15px;font-weight:700;margin-right:3px;color:var(--accent)">4</span>사용
            </div>
          </div>
          <!-- 프로그레스 바 -->
          <div style="height:7px;background:rgba(35,99,168,.12);border-radius:4px;overflow:hidden;border:1px solid rgba(35,99,168,.12);margin-top:16px">
            <div style="width:26.7%;height:100%;background:linear-gradient(90deg,var(--accent),var(--accent-mid));border-radius:4px"></div>
          </div>
          <div style="display:flex;justify-content:space-between;margin-top:6px;font-size:11px;color:var(--text-tertiary)">
            <span>사용 4일</span>
            <span>잔여 11일 / 총 15일</span>
          </div>
        </div>

        <!-- 우: 모바일 QR 블록 -->
        <div style="display:flex;align-items:center;gap:14px;background:linear-gradient(135deg,var(--accent) 0%,var(--accent-mid) 100%);border-radius:var(--radius-xl);padding:16px 18px;min-width:260px">
          <div style="width:72px;height:72px;background:#fff;border-radius:9px;flex-shrink:0;display:grid;place-items:center;position:relative">
            <svg .../>   <!-- QR SVG -->
            <div style="position:absolute;...">MY</div>
          </div>
          <div>
            <div style="font-size:12px;font-weight:800;color:#fff;margin-bottom:3px">모바일 앱 설치</div>
            <div style="font-size:10px;color:rgba(255,255,255,.8);line-height:1.5;margin-bottom:6px">QR 스캔으로 언제 어디서나<br>연차·증명서 신청</div>
            <div style="font-size:10px;font-weight:700;background:rgba(255,255,255,.2);color:#fff;padding:2px 8px;border-radius:20px;display:inline-block">my-insa.app/install</div>
          </div>
        </div>

      </div>
    </div>
  </div>

  <!-- ② 연차 이력 + 증명서 관리 -->
  <div style="display:grid;grid-template-columns:1.4fr 1fr;gap:16px;margin-bottom:20px">

    <!-- 연차 사용 이력 -->
    <div class="panel">
      <div class="panel-head"><h3>연차 사용 이력</h3></div>
      <div style="padding:0">
        <table class="tbl" style="table-layout:fixed;width:100%">
          <colgroup>
            <col style="width:148px"><!-- 날짜 -->
            <col style="width:96px"> <!-- 구분 -->
            <col style="width:76px"><!-- 일수 -->
            <col>                    <!-- 사유 -->
            <col style="width:80px"><!-- 상태 -->
          </colgroup>
          <thead>
            <tr>
              <th>날짜</th><th>구분</th>
              <th style="text-align:center">일수</th>
              <th>사유</th>
              <th class="center">상태</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>2026.05.04 (월)</td>
              <td>연차</td>
              <td class="center" style="font-weight:700;color:var(--accent)">1일</td>
              <td>가족 행사</td>
              <td class="center"><span class="badge w">대기</span></td>
            </tr>
            <!-- 상태: badge.w 대기 / badge.s 승인 / badge.d 반려 -->
          </tbody>
        </table>
      </div>
    </div>

    <!-- 증명서 관리 -->
    <div class="panel">
      <div class="panel-head">
        <h3>증명서 관리</h3>
        <!-- [증명서 신청] → 직원 업무신청(emp-cert / P-011) 화면으로 이동. 2단계 신청 모달은 P-011에서 동작 -->
        <button class="btn btn-sm btn-primary" onclick="goPageByName('emp-cert')">
          <svg .../> 증명서 신청
        </button>
      </div>
      <div class="panel-body" style="padding:0">
        <div style="padding:12px 18px">
          <div style="font-size:11px;font-weight:700;color:var(--text-tertiary);text-transform:uppercase;letter-spacing:0.06em;margin-bottom:10px">최근 신청 내역</div>
          <!-- 신청 항목 행 -->
          <div style="display:flex;align-items:center;justify-content:space-between;padding:9px 0;border-bottom:1px solid var(--border)">
            <div>
              <div style="font-size:12px;font-weight:600">재직증명서</div>
              <div style="font-size:11px;color:var(--text-tertiary)">2026.04.29 · 은행 대출</div>
            </div>
            <span class="badge w">대기</span>
          </div>
        </div>
      </div>
    </div>

  </div>
</div>
```

---

### P-08. 직원 업무신청 (`data-page="emp-cert"`)

#### 레이아웃 구성

```
[ 툴바: 종류/상태 필터 | 증명서 신청 버튼 ]
[ 내 증명서 신청 이력 테이블 ]
```

```html
<div class="page" data-page="emp-cert">

  <div class="toolbar">
    <div class="filters">
      <select class="filter-input"><option>전체 종류</option>...</select>
      <select class="filter-input"><option>전체 상태</option>...</select>
    </div>
    <a href="#none" class="btn btn-primary" onclick="openEmpCertModal()">
      <svg .../>  증명서 신청
    </a>
  </div>

  <div class="table-wrap">
    <table class="tbl" style="table-layout:fixed">
      <thead>
        <tr>
          <th style="width:160px">신청일</th>
          <th style="width:150px">증명서 종류</th>
          <th>제출처 / 용도</th>
          <th class="center" style="width:110px">상태</th>
          <th style="width:130px"></th>
          <!-- 부수(발급 매수) 컬럼 없음 — M-031·P-003과 동일, 발급 매수 기능 제외 -->
        </tr>
      </thead>
      <tbody>
        <!-- 대기 → 신청 취소 버튼 -->
        <tr>
          ...
          <td class="center"><span class="badge w">대기</span></td>
          <td><a href="#none" class="btn btn-sm" onclick="showToastV7('신청이 취소되었습니다.')">신청 취소</a></td>
        </tr>
        <!-- 발급완료 → 다운로드 버튼 -->
        <tr>
          ...
          <td class="center"><span class="badge s">발급완료</span></td>
          <td><a href="#none" class="btn btn-sm btn-primary" onclick="showToastV7('📄 재직증명서를 다운로드합니다.')">다운로드</a></td>
        </tr>
        <!-- 반려 → 사유보기 버튼 -->
        <tr>
          ...
          <td class="center"><span class="badge d">반려</span></td>
          <td><a href="#none" class="btn btn-sm" onclick="openCertReasonModal(...)">사유보기</a></td>
        </tr>
      </tbody>
    </table>
  </div>

</div>
```

#### 상태별 액션 버튼 규칙 (직원)

| 상태 | 뱃지 | 버튼 |
|------|------|------|
| 대기 | `.badge.w` | `.btn.btn-sm` "신청 취소" |
| 발급완료 | `.badge.s` | `.btn.btn-sm.btn-primary` "다운로드" |
| 반려 | `.badge.d` | `.btn.btn-sm` "사유보기" |

#### 증명서 신청 — 2단계 모달 흐름

탭 구조가 아닌 **2단계 모달 전환** 방식. 이력 조회는 페이지 테이블, 신청은 모달로 분리.

```
openEmpCertModal()
  └→ Step 1 모달 (#empCertStep1Bg) — 종류 선택
       └→ 버튼 클릭 → openEmpCertStep2(type)
            └→ Step 2 모달 (#empCertStep2Bg) — 정보 입력
                 └→ submitEmpCert() → 모달 닫기 + toast
```

**Step 1 — 종류 선택** `id="empCertStep1Bg"` `.modal--sm`

```html
<div class="modal-backdrop" id="empCertStep1Bg">
  <div class="modal modal--sm">
    <div class="modal-head">
      <h3>증명서 신청 — 종류 선택</h3>
      <div class="close" onclick="closeEmpCertModal()">...</div>
    </div>
    <div class="modal-body" style="padding:20px;display:flex;flex-direction:column;gap:10px">
      <!-- 각 항목: height:56px .btn, 아이콘박스(36px) + 설명 + chevron -->
      <a href="#none" class="btn" style="justify-content:flex-start;gap:14px;height:56px;padding:0 16px"
              onclick="openEmpCertStep2('재직증명서')">
        <div style="width:36px;height:36px;border-radius:10px;background:#dbeafe;display:grid;place-items:center;flex-shrink:0">
          <svg .../><!-- 파일 아이콘, stroke:#1e40af -->
        </div>
        <div style="text-align:left">
          <div style="font-size:14px;font-weight:700">재직증명서</div>
          <div style="font-size:11px;color:var(--text-tertiary)">현재 재직 사실을 증명하는 문서</div>
        </div>
        <svg .../><!-- chevron-right, margin-left:auto -->
      </a>
      <!-- 경력증명서 (background:#fce7f3) / 기타증명서 (background:#d1fae5) 동일 구조 -->
    </div>
  </div>
</div>
```

| 종류 | 아이콘 배경 | 설명 |
|------|------------|------|
| 재직증명서 | `#dbeafe` (파랑) | 현재 재직 사실을 증명하는 문서 |
| 경력증명서 | `#fce7f3` (핑크) | 입사일·직위·담당업무 등 경력 증명 |
| 기타증명서 | `#d1fae5` (초록) | 근속확인서, 급여확인서 등 기타 문서 |

**Step 2 — 신청 정보 입력** `id="empCertStep2Bg"` `.modal--sm`

```html
<div class="modal-backdrop" id="empCertStep2Bg">
  <div class="modal modal--sm">
    <div class="modal-head">
      <div>
        <h3 id="empCertStep2Title">재직증명서 신청</h3>  <!-- JS로 종류명 갱신 -->
        <div style="font-size:12px;color:var(--text-secondary);margin-top:2px">신청 정보를 입력해주세요</div>
      </div>
      <div class="close" onclick="closeEmpCertModal()">...</div>
    </div>
    <div class="modal-body">
      <!-- 명칭: 기타증명서 선택 시에만 노출(JS로 display 토글), 필수, maxlength=50 -->
      <div class="field" id="empCertNameField" style="margin-bottom:12px;display:none">
        <label>명칭 <span style="color:var(--danger)">*</span></label>
        <input type="text" id="empCertName" maxlength="50" placeholder="예) 근속확인서, 급여확인서 등" />
      </div>
      <div class="field" style="margin-bottom:12px">
        <label>제출처 / 용도 <span style="color:var(--danger)">*</span></label>
        <input type="text" id="empCertPurpose" maxlength="100" placeholder="예) 은행 대출 신청용, 비자 신청용 등" />
      </div>
      <div class="field">
        <label>요청 메모 <span style="font-weight:400;font-size:10px;color:var(--text-tertiary)">(선택)</span></label>
        <textarea id="empCertMemo" rows="4" placeholder="특별 요청사항을 입력해주세요&#10;예) 담당자 서명 추가 요청 등"></textarea>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="closeEmpCertModal()">취소</a>
      <a href="#none" class="btn btn-primary" onclick="submitEmpCert()">
        <svg .../>  신청하기
      </a>
    </div>
  </div>
</div>
```

**JS 요약**

```js
openEmpCertModal()       // empCertStep1Bg.show
openEmpCertStep2(type)   // empCertStep1Bg 닫기 → empCertStep2Title = type+'신청' → 명칭 필드(기타증명서)만 표시 → empCertStep2Bg.show
closeEmpCertModal()      // 두 모달 모두 닫기
submitEmpCert()          // 필수값 검증(기타 명칭·제출처/용도) → closeEmpCertModal() + showToastV7('✅ {종류} 신청이 완료되었습니다...')
```

---

### P-09. 직원 프로필 관리 (`data-page="emp-profile"`)

#### 레이아웃 구성

```
[ 상단 액션 버튼: 수정 / (취소 + 저장) ]
─────────────────────────────────────────
[ 좌측 260px           ] [ 우측 flex:1        ]
  ├ 프로필 사진 패널      ├ 개인 정보 패널
  └ 인사 기본 정보 패널   └ 내 인사 정보 패널
                              (자격증/학력/어학)
```

```html
<div class="page" data-page="emp-profile">

  <!-- 상단 액션 버튼 -->
  <div style="display:flex;justify-content:flex-end;margin-bottom:16px;gap:8px" id="profileActions">
    <a href="#none" id="profileEditBtn"   class="btn"           onclick="setProfileMode('edit')">수정</a>
    <a href="#none" id="profileCancelBtn" class="btn"    style="display:none" onclick="setProfileMode('view')">취소</a>
    <a href="#none" id="profileSaveBtn"   class="btn btn-primary" style="display:none" onclick="saveProfile()">저장</a>
  </div>

  <div style="display:grid;grid-template-columns:260px 1fr;gap:16px">

    <!-- 좌: 프로필 사진 + 인사 기본 정보 -->
    <div style="display:flex;flex-direction:column;gap:16px">

      <!-- 프로필 사진 패널 -->
      <div class="panel">
        <div class="panel-head"><h3>프로필 사진</h3></div>
        <div class="panel-body" style="text-align:center;padding:24px 20px">
          <div style="position:relative;width:88px;margin:0 auto 12px;display:inline-block">
            <!-- 아바타: 이니셜 텍스트, 88×88px, border-radius:22px, accent 그라디언트 -->
            <div style="width:88px;height:88px;border-radius:22px;background:linear-gradient(135deg,var(--accent),var(--accent-mid));color:#fff;display:grid;place-items:center;font-size:34px;font-weight:800">홍</div>
            <!-- 수정 모드에서만 표시되는 카메라 버튼 -->
            <a href="#none" id="profilePhotoEditBtn" style="display:none;position:absolute;top:-6px;right:-6px;..." onclick="document.getElementById('profilePhotoInput').click()">
              <svg .../>  <!-- 카메라 아이콘 -->
            </a>
            <input type="file" id="profilePhotoInput" accept="image/*" style="display:none" ... />
          </div>
          <div style="font-size:15px;font-weight:800;margin-bottom:3px">홍길동</div>
          <div style="font-size:12px;color:var(--text-secondary)">마케팅팀 · 대리</div>
        </div>
      </div>

      <!-- 인사 기본 정보 패널 (읽기 전용) -->
      <div class="panel">
        <div class="panel-head"><h3>인사 기본 정보</h3></div>
        <div class="panel-body" style="padding:0">
          <!-- 항목 행: padding:10px 16px, border-bottom, font-size:12px -->
          <div style="padding:10px 16px;display:flex;justify-content:space-between;border-bottom:1px solid var(--border);font-size:12px">
            <span style="color:var(--text-secondary)">사번</span>
            <strong>EMP-2103</strong>
          </div>
          <!-- 입사일, 부서, 직급, 고용형태 동일 구조 -->
        </div>
      </div>

    </div>

    <!-- 우: 개인 정보 + 내 인사 정보 -->
    <div style="display:flex;flex-direction:column;gap:16px">

      <!-- 개인 정보 패널 -->
      <div class="panel">
        <div class="panel-head"><h3>개인 정보</h3></div>
        <div class="panel-body">
          <!-- 조회 모드 (기본 표시) -->
          <div id="profileViewMode">
            <div style="display:grid;grid-template-columns:1fr 1fr;gap:0;border:1px solid var(--border);border-radius:var(--radius-md);overflow:hidden">
              <div style="padding:12px 16px;border-bottom:1px solid var(--border);border-right:1px solid var(--border)">
                <div style="font-size:11px;color:var(--text-tertiary);font-weight:600;margin-bottom:4px">이름</div>
                <div style="font-size:13px;font-weight:600">홍길동</div>
              </div>
              <!-- 영문 이름 / 이메일 / 연락처 동일 구조 -->
            </div>
          </div>
          <!-- 수정 모드 (display:none → 수정 클릭 시 표시) -->
          <div id="profileEditMode" style="display:none">
            <div class="form-row">
              <div class="field"><label>이름 <span style="color:var(--danger)">*</span></label><input type="text" value="홍길동" /></div>
              <div class="field"><label>영문 이름</label><input type="text" value="Hong Gil Dong" /></div>
            </div>
            <div class="form-row">
              <div class="field"><label>이메일 <span style="color:var(--danger)">*</span></label><input type="email" value="hong@company.com" /></div>
              <div class="field"><label>연락처 <span style="color:var(--danger)">*</span></label><input type="tel" value="010-1234-5678" /></div>
            </div>
          </div>
        </div>
      </div>

      <!-- 내 인사 정보 패널 (자격증/학력/어학) -->
      <div class="panel">
        <div class="panel-head">
          <h3>내 인사 정보</h3>
          <!-- 수정 모드에서만 표시 -->
          <a href="#none" id="hrInfoAddBtn" class="btn btn-sm" style="display:none" onclick="openHrInfoModal()">
            <svg .../>  항목 추가
          </a>
        </div>
        <div class="panel-body" style="padding:0">

          <!-- 섹션: 자격증 / 학력 / 어학 — 동일 구조 반복 -->
          <div style="padding:12px 18px;border-bottom:1px solid var(--border)">
            <div style="font-size:11px;font-weight:700;color:var(--text-tertiary);text-transform:uppercase;letter-spacing:0.06em;margin-bottom:10px">자격증</div>
            <!-- 항목 카드 -->
            <div style="display:flex;align-items:center;justify-content:space-between;padding:9px 12px;background:var(--bg);border-radius:var(--radius-md);border:1px solid var(--border)">
              <div style="display:flex;align-items:center;gap:10px">
                <!-- 아이콘 박스: 28×28px, border-radius:8px, 배경색은 카테고리별 -->
                <div style="width:28px;height:28px;border-radius:8px;background:#dbeafe;display:grid;place-items:center;flex-shrink:0">
                  <svg .../>
                </div>
                <div>
                  <div style="font-size:13px;font-weight:600">정보처리기사</div>
                  <div style="font-size:11px;color:var(--text-tertiary)">한국산업인력공단 · 2020.06.05</div>
                </div>
              </div>
              <!-- 수정 모드에서만 표시 -->
              <a href="#none" class="icon-btn hr-edit-btn" style="display:none" onclick="openHrEditModal(...)"><svg .../></a>
            </div>
          </div>

        </div>
      </div>

    </div>
  </div>
</div>
```

#### 프로필 조회/수정 모드 전환 JS 패턴

```js
function setProfileMode(mode) {
  const isEdit = mode === 'edit';
  // 버튼 토글
  document.getElementById('profileEditBtn').style.display   = isEdit ? 'none' : '';
  document.getElementById('profileCancelBtn').style.display = isEdit ? ''     : 'none';
  document.getElementById('profileSaveBtn').style.display   = isEdit ? ''     : 'none';
  // 조회/수정 영역 토글
  document.getElementById('profileViewMode').style.display  = isEdit ? 'none' : '';
  document.getElementById('profileEditMode').style.display  = isEdit ? ''     : 'none';
  // 사진 수정 버튼
  document.getElementById('profilePhotoEditBtn').style.display = isEdit ? 'grid' : 'none';
  // 인사 정보 수정 버튼들
  document.querySelectorAll('.hr-edit-btn').forEach(b => b.style.display = isEdit ? '' : 'none');
  document.getElementById('hrInfoAddBtn').style.display = isEdit ? '' : 'none';
}
```

#### 내 인사 정보 아이콘 박스 색상 (섹션별)

| 섹션 | 배경 | 아이콘 색 |
|------|------|---------|
| 자격증 | `#dbeafe` | `#1e40af` |
| 학력 | `#f3e8ff` | `#7c3aed` |
| 어학 | `#fef9c3` | `#92400e` |

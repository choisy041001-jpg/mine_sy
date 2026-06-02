---
name: rules_myinsa_pc_components_page
description: MY인사 PC 특정 화면에서만 쓰는 컴포넌트 — KPI카드·연차사이드패널·연차툴팁·대시보드·직원연차·QR카드·프로필·FAQ카드·파일첨부·인라인편집
metadata: 
  node_type: memory
  type: project
  originSessionId: c4cb22ee-5bf5-4a66-91cc-1113065fd4e6
---

**Why:** 특정 화면에서만 쓰는 컴포넌트. 간단한 화면은 [[rules_myinsa_pc_components_core]]만으로 충분.
**How to apply:**
- 대시보드 화면 → core + 이 파일 §1(KPI), §3(대시보드)
- 연차 관리 → core + 이 파일 §2(사이드패널), §4(연차툴팁), §10(인라인편집)
- 직원 대시보드 → core + 이 파일 §5(직원연차), §6(QR카드)
- 프로필 관리 → core + 이 파일 §7(프로필)
- FAQ / intro → core + 이 파일 §8(FAQ카드)
- 업무신청(증명서) → core + 이 파일 §9(파일첨부)

---

## 1. KPI 카드 (대시보드 전용)

```html
<div class="kpi-grid">
  <div class="kpi kpi-leave" onclick="goPageByName('leave')" title="연차 관리로 이동">
    <div class="label">대기중 연차 결재</div>
    <div class="value">4</div>
  </div>
  <div class="kpi kpi-faq" onclick="goPageByName('faq')">
    <div class="label">미답변 FAQ</div>
    <div class="value">3</div>
  </div>
  <div class="kpi kpi-cert" onclick="goPageByName('cert')">
    <div class="label">미처리 증명서</div>
    <div class="value">3</div>
  </div>
  <div class="kpi kpi-total">
    <div class="label">총 직원수</div>
    <div class="value">9</div>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.kpi-grid` | 4열 그리드 (grid, repeat(4,1fr), gap:--sp-5) |
| `.kpi` | 카드 공통 (--radius-xl, --sp-8, --shadow-card, hover:translateY(-2px)) |
| `.kpi-leave` | 연차 (파랑 계열) |
| `.kpi-faq` | FAQ (보라 계열) |
| `.kpi-cert` | 증명서 (초록 계열) |
| `.kpi-total` | 전체 (주황 계열) |

**상단 3px 색상 선** — CSS `::before`로 자동 적용. `.label` / `.value` 내부 클래스 사용.
클릭 가능한 KPI는 `onclick="goPageByName('...')"` 추가.

---

## 2. 연차 사이드 패널 (연차 관리 전용)

```html
<!-- 딤 배경 -->
<div class="leave-side-overlay" id="lspOverlay" onclick="closeLeavePanel()"></div>

<!-- 패널 본체 -->
<div class="leave-side-panel" id="lspPanel">
  <div class="lsp-head">
    <h3 id="lspTitle">연차 결재 현황</h3>
    <div class="lsp-close" onclick="closeLeavePanel()">
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
      </svg>
    </div>
  </div>
  <div class="lsp-body">
    <!-- 프로필 -->
    <div class="lsp-profile">
      <div class="emp-name" id="lspName">홍길동</div>
      <div class="emp-meta" id="lspMeta">마케팅팀</div>
    </div>
    <!-- 통계 3분할 -->
    <div class="lsp-stats">
      <div class="lsp-stat"><div class="sv" id="lspTotal">15</div><div class="sl">총 연차</div></div>
      <div class="lsp-stat"><div class="sv" id="lspUsed">3</div><div class="sl">사용</div></div>
      <div class="lsp-stat accent"><div class="sv" id="lspRemain">12</div><div class="sl">잔여</div></div>
    </div>
    <!-- 결재 현황 -->
    <div class="lsp-section">
      <div class="lsp-section-title">결재 현황</div>
      <div id="lspApprList"><!-- JS로 채움 --></div>
    </div>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.leave-side-overlay` | 딤 배경 (fixed, rgba(0,0,0,.25)). `.show` → display:block |
| `.leave-side-panel` | 우측 슬라이드 (width:400px, fixed, right:0, translateX(100%) → `.show` translateX(0)) |
| `.lsp-head` | 패널 헤더 (flex, space-between, border-bottom) |
| `.lsp-close` | 닫기 버튼 (30×30, --radius-sm) |
| `.lsp-body` | 본문 (flex:1, overflow-y:auto) |
| `.lsp-profile` | 상단 프로필 (accent 그라디언트 bg) |
| `.lsp-stats` | 통계 3열 그리드 |
| `.lsp-stat` | 통계 항목. `.accent` → 잔여 강조 |
| `.lsp-section` | 패널 내 섹션 (border-bottom) |
| `.lsp-section-title` | 섹션 제목 (--fs-xs, uppercase, letter-spacing:0.07em, --text-tertiary, mb:--sp-5) |
| `.lsp-appr-item` | 결재 항목 행 (padding:12px 0, flex, align-items:center, gap:--sp-4, border-bottom) |
| `.ai-info` | 결재 항목 정보 묶음 (flex:1, min-width:0) |
| `.ai-title` | 결재 항목 제목 (--fs-body, --fw-semibold, mb:2px) |
| `.ai-meta` | 결재 항목 메타 (--fs-xs, --text-tertiary) |
| `.sv` | 통계 수치 (font-size:22px, --fw-extrabold, --text). `.accent` → --accent |
| `.sl` | 통계 레이블 (--fs-xs, --text-tertiary, uppercase, letter-spacing:0.04em) |

**JS:** `openLeavePanel(name, dept, total, used, remain, status)` / `closeLeavePanel()`

---

## 3. 대시보드 레이아웃 (관리자 대시보드 전용)

```html
<!-- 2열 그리드 -->
<div class="dash-grid">
  <!-- 미처리 업무 패널 -->
  <div class="panel">
    <div class="panel-head"><h3>미처리 업무 (10)</h3></div>
    <div class="p-0">
      <div class="alert-row" onclick="[모달/페이지 이동]">
        <div class="alert-icon faq"><!-- SVG --></div>
        <div class="alert-body">
          <div class="alert-title">질문 내용</div>
          <div class="alert-meta"><strong>홍길동</strong> · 마케팅팀 · 2시간 전</div>
        </div>
        <div class="alert-actions" onclick="event.stopPropagation()">
          <a href="#none" class="btn btn-sm btn-primary">답변</a>
        </div>
      </div>
    </div>
  </div>

  <!-- 직원 현황 패널 -->
  <div class="panel panel--start">
    <div class="panel-head"><h3>직원 현황</h3></div>
    <div class="emp-tab-bar">
      <a href="#none" class="emp-tab-btn active" onclick="filterEmpStatus(this,'all')" data-status="all">
        전체 <span class="emp-tab-count">9</span>
      </a>
      <a href="#none" class="emp-tab-btn" onclick="filterEmpStatus(this,'재직')" data-status="재직">
        재직 <span class="emp-tab-count">8</span>
      </a>
      <!-- 휴직 / 퇴직 동일 패턴 -->
    </div>
    <div class="emp-tbl-wrap">
      <table class="tbl emp-tbl" id="empStatusTable">
        <thead>
          <tr>
            <th style="width:100px">이름</th>
            <th style="width:110px">부서</th>
            <!-- 직급/입사일/연락처/상태 -->
          </tr>
        </thead>
        <tbody>
          <tr data-emp-status="재직">
            <td><strong>홍길동</strong></td>
            <td>마케팅팀</td>
            <!-- ... -->
            <td class="center"><span class="badge s">재직</span></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.dash-grid` | 대시보드 2열 그리드 |
| `.alert-row` | 알림 목록 행 (flex, padding, hover:--surface-hover) |
| `.alert-icon` | 알림 아이콘 박스. `.faq` / `.cert` / `.leave` 변형 |
| `.alert-body` | 알림 텍스트 영역 (flex:1) |
| `.alert-title` | 알림 제목 (--fw-semibold) |
| `.alert-meta` | 알림 메타 (--fs-sm, --text-secondary) |
| `.alert-actions` | 알림 액션 버튼 |
| `.emp-tab-bar` | 직원 현황 탭 바 |
| `.emp-tab-btn` | 탭 버튼. `.active` → accent |
| `.emp-tab-count` | 탭 수량 (--fs-xs, 뱃지형) |
| `.emp-tbl-wrap` | 테이블 래퍼 (max-height, overflow-y:auto) |

**주의:** `.alert-actions`에 `onclick="event.stopPropagation()"` 필수 (row 클릭 버블 방지)

---

## 4. 연차 툴팁 (연차 관리 전용)

```html
<!-- HTML에 한 번만 선언 (body 하단) -->
<div class="leave-tooltip" id="leaveTooltip"></div>
```

| 클래스 | 설명 |
|---|---|
| `.leave-tooltip` | 연차 월별 상세 툴팁 (fixed, --radius-md, pre-wrap, z-index) |
| `.month-h` | 월 헤더 열 스타일 |
| `.month-used-nonzero` | JS가 0이 아닌 월별 셀에 추가 (파란 색) |
| `.tt-active` | 현재 클릭된 셀 (배경 강조) |

**JS:** `attachLeaveTooltips()` — DOMContentLoaded에서 실행. 클릭으로 툴팁 고정, 다시 클릭 or 외부 클릭으로 닫기.

---

## 5. 직원 연차 현황 (직원 대시보드 전용)

```html
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
            <div class="leave-stat-chip">
              <span class="leave-stat-num">15</span>총
            </div>
            <div class="leave-stat-chip">
              <span class="leave-stat-num">4</span>사용
            </div>
          </div>
        </div>
        <div class="leave-progress">
          <div class="leave-progress-fill" style="width:26.7%"></div>  <!-- 인라인 % -->
        </div>
        <div class="leave-progress-meta">
          <span>사용 4일</span>
          <span>잔여 11일 / 총 15일</span>
        </div>
      </div>
      <!-- 우: QR (§6 참조) -->
    </div>
  </div>
</div>

<!-- 2열 그리드 -->
<div class="emp-dash-grid">
  <!-- 연차 사용 이력 + 증명서 관리 패널 -->
</div>
```

| 클래스 | 설명 |
|---|---|
| `.leave-remain-grid` | 연차 현황 2열 그리드 (좌: 수치, 우: QR) |
| `.leave-card-hd` | 헤더 (flex, space-between) |
| `.leave-card-label` | "잔여 연차" 레이블 |
| `.leave-num-row` | 숫자 행 |
| `.leave-num-big` | 큰 숫자 (--fs-xl 이상) |
| `.leave-num-unit` | "일" 단위 (--fs-sm) |
| `.leave-stat-chips` | 총/사용 칩 묶음 |
| `.leave-stat-chip` | 통계 칩 하나 |
| `.leave-stat-num` | 칩 내 숫자 (굵음) |
| `.leave-progress` | 프로그레스 바 트랙 |
| `.leave-progress-fill` | 채워진 부분 (width:XX% 인라인) |
| `.leave-progress-meta` | 바 아래 텍스트 (flex, space-between) |
| `.emp-dash-grid` | 직원 대시보드 2열 그리드 |
| `.cert-row` | 증명서 목록 행 (flex, space-between, border-bottom) |

---

## 6. QR 카드

```html
<!-- 단독 카드형 (직원 대시보드) -->
<div class="qr-card">
  <div class="qr-box">
    <svg viewBox="0 0 41 41"><!-- QR SVG --></svg>
    <div class="qr-badge">MY</div>
  </div>
  <div>
    <div class="qr-title">모바일 앱 설치</div>
    <div class="qr-desc">QR 스캔으로 언제 어디서나<br>연차·증명서 신청</div>
    <div class="qr-link">my-insa.app/install</div>
  </div>
</div>

<!-- 대형 박스형 (온보딩 페이지) -->
<div class="qr-box" id="qrBox">
  <svg viewBox="0 0 41 41"><!-- QR SVG --></svg>
  <div class="logo-overlay">
    <div class="onb-logo">MY</div>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.qr-card` | QR 카드 래퍼 (flex, gap, --surface, --radius-xl) |
| `.qr-box` | QR 이미지 박스 (relative, --radius-lg) |
| `.qr-badge` | QR 중앙 로고 뱃지 (absolute, center) |
| `.logo-overlay` | QR 중앙 오버레이 (온보딩용) |
| `.qr-title` | QR 카드 제목 |
| `.qr-desc` | QR 카드 설명 (--fs-sm) |
| `.qr-link` | 앱 링크 텍스트 (--accent) |

---

## 7. 프로필 관리 (직원 프로필 전용)

```html
<!-- 조회/수정 모드 전환 버튼 (툴바 대신) -->
<div class="profile-actions" id="profileActions">
  <a href="#none" id="profileEditBtn" class="btn" onclick="setProfileMode('edit')">
    <!-- 편집 SVG --> 수정
  </a>
  <a href="#none" id="profileCancelBtn" class="btn" style="display:none" onclick="setProfileMode('view')">취소</a>
  <a href="#none" id="profileSaveBtn" class="btn btn-primary" style="display:none" onclick="saveProfile()">저장</a>
</div>

<!-- 2열 레이아웃 -->
<div class="profile-layout">
  <!-- 좌: 사진 패널 -->
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

  <!-- 인사 기본 정보 (읽기 전용) -->
  <div class="hr-info-row">
    <span class="text-secondary">사번</span>
    <strong>EMP-2103</strong>
  </div>

  <!-- 개인 정보 — 조회 모드 -->
  <div class="profile-view-grid">
    <div class="profile-view-cell">
      <div class="profile-view-label">이름</div>
      <div class="item-title">홍길동</div>
    </div>
  </div>

  <!-- 인사 정보 섹션 -->
  <div class="hr-section">
    <div class="section-label">자격증</div>
    <div class="hr-item-list">
      <div class="hr-item">
        <div class="hr-item-inner">
          <div class="hr-item-icon hr-item-icon--cert"><!-- 파랑 SVG --></div>
          <div>
            <div class="item-title">정보처리기사</div>
            <div class="item-meta">한국산업인력공단 · 2020.06.05</div>
          </div>
        </div>
        <!-- 수정 모드에서만 표시 -->
        <a href="#none" class="icon-btn hr-edit-btn" style="display:none"
          onclick="openHrEditModal('cert', ...)"><!-- 편집 SVG --></a>
      </div>
    </div>
  </div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.profile-actions` | 수정/저장/취소 버튼 영역 (toolbar 대체) |
| `.profile-layout` | 2열 레이아웃 |
| `.profile-avatar-wrap` | 아바타+카메라 버튼 래퍼 (relative) |
| `.profile-avatar` | 글자 아바타 (대형, 원형) |
| `.profile-photo-btn` | 카메라 편집 버튼 (absolute, 우하단) |
| `.profile-name` | 이름 텍스트 |
| `.profile-dept` | 부서·직급 텍스트 |
| `.profile-view-grid` | 조회 모드 정보 그리드 (2열) |
| `.profile-view-cell` | 조회 정보 셀 |
| `.profile-view-label` | 레이블 (--fs-xs, uppercase) |
| `.hr-info-row` | 인사기본정보 행 (flex, space-between, border-bottom) |
| `.hr-section` | 인사정보 섹션 (border-bottom) |
| `.hr-item-list` | hr-item 묶음 |
| `.hr-item` | 인사정보 항목 행 (flex, space-between) |
| `.hr-item-inner` | 아이콘+텍스트 묶음 (flex, gap) |
| `.hr-item-icon` | 아이콘 박스 (32×32, --radius-md) |
| `.hr-item-icon--cert` | 자격증 (파랑 bg: `var(--hr-cert-bg)` = #dbeafe) |
| `.hr-item-icon--edu` | 학력 (보라 bg: `var(--hr-edu-bg)` = #f3e8ff) |
| `.hr-item-icon--lang` | 어학 (노랑 bg: `var(--hr-lang-bg)` = #fef9c3) |
| `.hr-edit-btn` | **JS 선택자 전용 — CSS 정의 없음.** 반드시 `.icon-btn`과 함께 사용: `class="icon-btn hr-edit-btn"`. 시각 스타일은 `.icon-btn`이 담당하고, `.hr-edit-btn`은 `querySelectorAll('.hr-edit-btn')`로 일괄 제어하는 JS 훅 용도. `setProfileMode('edit')` 호출 시 `style.display=''`로 표시, `setProfileMode('view')` 시 `style.display='none'`으로 숨김. **`.icon-btn` 없이 단독 사용 금지.** |

**JS:** `setProfileMode('edit'|'view')` — 버튼/영역 표시 토글

---

## 8. FAQ 카드 / 카테고리 탭 (FAQ·intro 전용)

```html
<!-- FAQ 카테고리 탭 (toolbar 내 filters 대신 사용) -->
<a href="#none" class="faq-cat-btn active" onclick="filterFaqCommonTable(this,'all')">전체</a>
<a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'연차')">연차</a>
<a href="#none" class="faq-cat-btn" onclick="filterFaqCommonTable(this,'증명서')">증명서</a>

<!-- FAQ 카드 (intro 페이지 폰 목업 내) -->
<div class="faq-card">
  <div class="faq-card-hd">
    <span class="faq-card-tag">연차</span>
    <span class="faq-card-status done">답변 완료</span>
  </div>
  <div class="faq-card-title">연차 신청 후 결재 전에 취소할 수 있나요?</div>
  <div class="faq-card-date">4월 25일 · 답변 4월 26일</div>
</div>
```

| 클래스 | 설명 |
|---|---|
| `.faq-cat-btn` | 카테고리 탭 버튼. `.active` → 강조 |
| `.faq-card` | FAQ 카드 (--radius-lg, --surface, border) |
| `.faq-card-hd` | 카드 상단 (flex, space-between) |
| `.faq-card-tag` | 카테고리 태그 칩 (--radius-md, --accent-light) |
| `.faq-card-status` | 답변 상태. `.done` → 완료, `.wait` → 대기 |
| `.faq-card-title` | 질문 제목 |
| `.faq-card-date` | 날짜 메타 (--fs-xs, --text-tertiary) |
| `.price-unit` | 가격 단위 텍스트 (font-size:18px, --fw-semibold, ml:--sp-1) — lux-price-value 내 "원" 단위 표시용 |

---

## 9. 파일 첨부 / 프리뷰 (업무신청·FAQ답변 전용)

```html
<!-- 클릭 업로드 존 (증명서 발급처리 모달 전용) -->
<div class="file-drop-zone" onclick="document.getElementById('certIssueFileInput').click()">
  <svg class="drop-icon">...</svg>
  <div class="drop-title">PDF 파일을 클릭하여 업로드</div>
  <div class="item-meta">PDF · 최대 10MB</div>
  <input type="file" id="certIssueFileInput" accept=".pdf" style="display:none"
    onchange="handleModalIssueFile(this)" />  <!-- 핸들러: handleModalIssueFile(input) -->
</div>
<div id="certIssueFileList" class="mt-3"></div>

<!-- 업로드된 파일 아이템 (JS로 생성) -->
<div class="file-item">
  <div class="file-type-badge"><span class="file-type-label">PDF</span></div>
  <div class="flex-1-min">
    <div class="item-title truncate">파일명.pdf</div>
    <div class="item-meta-t">142 KB</div>
  </div>
  <div class="attach-badge"><!-- 체크 SVG --> 첨부됨</div>
</div>

<!-- 발급 이력 파일 아이템 -->
<div class="cert-file-item" onclick="downloadCertFile('파일명')">
  <div class="cert-file-icon">
    <span class="file-ext-label">PDF</span>
  </div>
  <div class="flex-1-min">
    <div class="item-title truncate">재직증명서_홍길동.pdf</div>
    <div class="item-meta-t">142 KB</div>
  </div>
  <div class="download-link"><!-- 다운로드 SVG --> 다운로드</div>
</div>

<!-- 버튼형 파일 첨부 (FAQ 답변 모달 전용) -->
<div class="file-attach-row">
  <label class="file-attach-label">
    <svg><!-- 클립 SVG --></svg>
    파일 첨부
    <input type="file" multiple accept=".pdf,.jpg,.jpeg,.png,.docx,.xlsx"
      style="display:none" onchange="handleFaqAnswerFile(this,'faqDetailFileList')" />
    <!-- 핸들러: handleFaqAnswerFile(input, listId) -->
  </label>
  <span class="item-meta">PDF · 이미지 · 문서 · 최대 10MB</span>
</div>
<div id="faqDetailFileList" class="file-list"></div>
```

| 클래스 | 설명 |
|---|---|
| `.file-drop-zone` | 드롭/클릭 업로드 영역 (border-dashed, --radius-lg, text-center) |
| `.drop-icon` | 업로드 아이콘 SVG |
| `.drop-title` | 영역 안내 텍스트 |
| `.file-item` | 업로드된 파일 행 (flex, --radius-md, border) |
| `.file-type-badge` | 파일 타입 박스 (32×32, --radius-sm) |
| `.file-type-label` | "PDF" 텍스트 (--fs-2xs, --fw-bold) |
| `.attach-badge` | "첨부됨" 뱃지 (--success-bg, --success) |
| `.cert-file-item` | 이력 파일 행 (flex, --radius-md, border, cursor:pointer) |
| `.cert-file-icon` | 이력 파일 아이콘 박스 (--radius-md) |
| `.file-ext-label` | 확장자 레이블 (--fs-2xs) |
| `.download-link` | 다운로드 링크 (--accent, flex) |
| `.file-attach-row` | 버튼형 첨부 행 (flex, gap) |
| `.file-attach-label` | 첨부 레이블 버튼 (cursor:pointer) |
| `.file-list` | 첨부 파일 목록 래퍼 |
| `.file-item-shadow` | 파일 아이템 (shadow 변형, --shadow-card) |
| `.file-name` | 파일명 텍스트 (--fs-body, --fw-semibold) |
| `.file-remove-btn` | 파일 삭제 버튼 (24×24px, --radius-sm, --text-tertiary) |
| `.file-tag` | 파일 태그 칩 (inline-flex, border-radius:20px, --fs-xs, --fw-semibold) |
| `.file-tag-name` | 태그 파일명 (max-width:120px) |
| `.file-tag-remove` | 태그 삭제 버튼 (14×14px, --text-tertiary) |
| `.preview-img` | 이미지 프리뷰 (max-width:100%, max-height:480px, --radius-md) |
| `.preview-body` | 프리뷰 본문 래퍼 (text-center, --text-secondary) |
| `.preview-icon-lg` | 대형 아이콘 (64×64px, border-radius:16px, mx:auto, mb:14px) |
| `.preview-pdf-icon` | PDF 아이콘 박스 (60×60px, --radius-xl, --danger-bg, mx:auto, mb:12px) |
| `.preview-pdf-label` | PDF 레이블 텍스트 (font-size:18px, fw:900, --danger) |
| `.preview-filename` | 파일명 (--fs-md, --fw-semibold, mb:6px) |
| `.preview-meta` | 프리뷰 메타 (--fs-sm, --text-tertiary) |
| `.preview-attach` | 첨부 완료 표시 (inline-flex, --success-bg, border-radius:20px, --success) |
| `.preview-hint` | 업로드 힌트 텍스트 (--fs-body, --fw-medium) |
| `.preview-hint-sub` | 힌트 서브 텍스트 (--fs-sm, mt:--sp-2) |

---

## 10. 인라인 셀 편집 (연차 관리 전용)

더블클릭(`ondblclick="editTotal(this)"`)으로 활성화. `editTotal(td)` 함수가 아래 클래스를 제어.

| 클래스 | 설명 |
|---|---|
| `.editing` | 편집 중인 셀 상태 (JS로 추가/제거) |
| `.edit-input` | 인라인 편집 input (border:none, bg:transparent, width:100%) |
| `.leave-total-cell` | 총 연차 편집 가능 셀 (editable과 조합) |

### `editTotal(td)` 함수 동작 순서

```
1. 이미 편집 중(.editing)이면 무시(중복 실행 방지)
2. 기존 텍스트 값 보존 → td에 .editing 추가
3. td 내용을 <input class="edit-input"> 으로 교체 후 포커스·전체 선택
4. 입력 제한: 숫자(0-9)만 허용, 최대 2자리
5. 저장 조건: blur 또는 Enter 키 → 빈 값이면 원본값 복원, 값 있으면 저장
6. 취소 조건: ESC 키 → 원본값으로 복원, 저장하지 않음
7. 저장 후: 해당 행 전체 합계 재계산 (recalcRow(tr) 호출)
```

**HTML 마크업 패턴:**
```html
<!-- .editable: 편집 가능 셀 공통 스타일(cursor:text)
     .leave-total-cell: editTotal() JS 훅 전용 선택자
     data-emp: 사원명 (JS 데이터 참조용) -->
<td class="num editable leave-total-cell"
    ondblclick="editTotal(this)"
    data-emp="홍길동">15</td>
```

> `.leave-total-cell`은 `editTotal()`이 대상 셀을 일괄 제어하기 위한 JS 선택자이므로, **연차 총일수 셀에만 사용**. 다른 셀에 `ondblclick="editTotal(this)"` 추가 시 반드시 이 클래스도 함께 추가.

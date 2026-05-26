# MY인사 PC — 컴포넌트 규칙

> 기준 파일: `MY인사_PC_v31.html`  
> CSS 변수 토큰은 `rules_myinsa_pc_layout.md` 참고.

---

## 1. 버튼

### 기본 버튼 (.btn)

```html
<!-- 기본 -->
<a href="#none" class="btn">취소</a>

<!-- 프라이머리 -->
<a href="#none" class="btn btn-primary">저장</a>

<!-- 스몰 -->
<a href="#none" class="btn btn-sm">수정</a>

<!-- 프라이머리 스몰 -->
<a href="#none" class="btn btn-sm btn-primary">발급처리</a>
```

| 클래스 | height | padding | font-size |
|--------|--------|---------|-----------|
| `.btn` | 34px | `0 14px` | 13px |
| `.btn.btn-sm` | 28px | `0 10px` | 12px |

```css
.btn {
  height:34px; padding:0 14px; font-size:13px; font-weight:600;
  border-radius:var(--radius-md); cursor:pointer;
  display:inline-flex; align-items:center; gap:6px;
  border:1.5px solid var(--border);
  background:var(--surface); color:var(--text);
  transition:.15s; font-family:inherit; white-space:nowrap;
}
.btn:hover { background:var(--surface-hover) }

.btn-primary {
  background:linear-gradient(135deg,var(--accent),var(--accent-mid));
  color:#fff; border:none;
  box-shadow:0 3px 10px rgba(35,99,168,.3);
}
.btn-primary:hover {
  background:linear-gradient(135deg,#1b4d82,var(--accent));
  box-shadow:0 4px 14px rgba(35,99,168,.4);
}

/* 위험 액션 버튼 — 삭제·철회 등 */
.btn--danger { background:var(--danger); color:#fff; border-color:var(--danger) }
.btn--danger:hover { background:#b91c1c; border-color:#b91c1c }
```

> **명칭 주의**: `.btn--danger` 는 BEM 수정자 패턴(하이픈 2개)을 사용. `.btn-primary`, `.btn-sm` 과 달리 이중 하이픈이므로 오타 주의.

```html
<a href="#none" class="btn btn--danger">삭제</a>
```

### 아이콘 버튼 (.icon-btn)

테이블 행 내 편집/삭제 등 단독 아이콘 버튼.

```html
<div class="row-actions">
  <a href="#none" class="icon-btn" title="수정">
    <svg .../>
  </a>
</div>
```

```css
.icon-btn {
  width:26px; height:26px;
  display:grid; place-items:center;
  background:transparent; border:none;
  border-radius:4px; cursor:pointer;
  color:var(--text-secondary);
}
.icon-btn:hover { background:var(--accent-light); color:var(--accent) }
```

### 카카오 버튼 (.kakao-btn)

```html
<a href="#none" class="kakao-btn">
  <svg .../>  카카오톡 발송
</a>
```

```css
.kakao-btn {
  display:inline-flex; align-items:center; gap:6px;
  padding:6px 12px; font-size:12px; font-weight:700;
  border-radius:6px; border:none; cursor:pointer;
  background:#FEE500; color:#3C1E1E; transition:.15s;
}
.kakao-btn:hover { background:#f5db00 }
```

> **실제 사용 패턴 주의**  
> `MY인사_PC_v31.html`의 모달 내 카카오 발송 버튼은 `.kakao-btn` 독립 클래스 대신  
> `.btn`에 인라인 스타일로 색상을 덮어쓰는 방식을 혼용함:  
> ```html
> <a href="#none" class="btn" style="background:#FEE500;color:#3C1E1E;border-color:#FEE500;font-weight:700">
>   <svg .../>  카카오톡 발송
> </a>
> ```  
> AI가 신규 화면을 만들 때 `.kakao-btn` 독립 클래스와 `.btn` + 인라인 오버라이드 두 방식 모두 유효.  
> **권장**: `.kakao-btn` 클래스를 정의해두고 사용하는 것이 일관성에 유리.

---

## 2. 뱃지 (.badge)

상태 표시용. 텍스트 앞에 점(dot) SVG를 넣기도 함.

```html
<span class="badge s">재직</span>       <!-- 초록: 성공/승인/노출 -->
<span class="badge w">대기</span>       <!-- 주황: 경고/대기 -->
<span class="badge d">반려</span>       <!-- 빨강: 위험/반려/퇴사 -->
<span class="badge i">연차</span>       <!-- 파랑: 정보 -->
<span class="badge muted">비노출</span> <!-- 회색: 비활성 -->
```

```css
.badge {
  display:inline-flex; align-items:center; gap:4px;
  padding:3px 9px; border-radius:20px;
  font-size:11px; font-weight:700; border:none;
  background:var(--bg); white-space:nowrap; color:var(--text-secondary);
}
.badge.s     { color:var(--success); background:var(--success-bg) }
.badge.w     { color:var(--warning); background:var(--warning-bg) }
.badge.d     { color:var(--danger);  background:var(--danger-bg) }
.badge.i     { color:var(--info);    background:var(--info-bg) }
.badge.muted { color:var(--text-tertiary); background:var(--bg) }
```

### 증명서 종류 뱃지

```html
<span class="cert-badge cert-badge-emp">재직증명서</span>
<span class="cert-badge cert-badge-exp">경력증명서</span>
<span class="cert-badge cert-badge-other">기타증명서</span>
```

```css
/* 공통 구조 */
.cert-badge { display:inline-flex; align-items:center; gap:4px; padding:3px 9px; border-radius:20px; font-size:11px; font-weight:700; white-space:nowrap }

/* 색상 테마 */
.cert-badge-emp { background:#dbeafe; color:#1e40af }
.cert-badge-exp { background:#fce7f3; color:#9d174d }
.cert-badge-other { background:#d1fae5; color:#065f46 }
```

> 반드시 `.cert-badge` + `.cert-badge-종류` 두 클래스를 함께 사용한다.

---

## 3. 툴바 + 필터 인풋

테이블 상단 필터/액션 영역.

```html
<div class="toolbar">
  <div class="filters">
    <select class="filter-input"><option>전체 부서</option>...</select>
    <select class="filter-input"><option>전체 상태</option>...</select>
    <input  class="filter-input" type="date" value="2026-04-01" />
    <span style="color:var(--text-tertiary);font-size:12px">~</span>
    <input  class="filter-input" type="date" value="2026-04-30" />
    <input  class="filter-input" placeholder="검색어" />
  </div>
  <div style="display:flex;gap:8px">
    <a href="#none" class="btn">엑셀 다운로드</a>
    <a href="#none" class="btn btn-primary">+ 등록</a>
  </div>
</div>
```

```css
.toolbar {
  display:flex; justify-content:space-between;
  align-items:center; margin-bottom:14px; gap:12px;
}
.toolbar .filters { display:flex; gap:8px; align-items:center }

.filter-input {
  height:34px; padding:0 12px;
  border:1.5px solid var(--border); border-radius:var(--radius-md);
  font-size:13px; font-family:inherit;
  outline:none; background:var(--surface); transition:.15s;
}
.filter-input:focus {
  border-color:var(--accent);
  box-shadow:0 0 0 3px var(--accent-light);
}
```

---

## 4. 테이블

### 기본 구조

```html
<div class="table-wrap">
  <div class="scroll-body">             <!-- 스크롤 필요 시 -->
    <table class="tbl" style="table-layout:fixed">
      <thead>
        <tr>
          <th style="width:140px">신청일</th>
          <th style="width:120px">신청자</th>
          <th>제출처 / 용도</th>
          <th class="center" style="width:100px">상태</th>
          <th style="width:160px"></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>2026.04.29 09:14</td>
          <td><strong>홍길동</strong></td>
          <td>은행 대출 신청용</td>
          <td class="center"><span class="badge w">대기</span></td>
          <td>
            <div class="row-actions">
              <a href="#none" class="btn btn-sm btn-primary">발급처리</a>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```

```css
.table-wrap {
  background:var(--surface); border:none;
  border-radius:var(--radius-xl); overflow:clip;
  box-shadow:var(--shadow-card);
}
table.tbl {
  width:100%; border-collapse:collapse;
  font-size:13px; table-layout:fixed;
}
table.tbl thead th {
  text-align:left; padding:12px 20px;
  font-size:11px; font-weight:700;
  color:var(--text-tertiary); background:var(--bg);
  border-bottom:1px solid var(--border);
  position:sticky; top:0; z-index:2;
  letter-spacing:0.03em; text-transform:uppercase;
  overflow:hidden; text-overflow:ellipsis; white-space:nowrap;
  user-select:none;
}
table.tbl tbody td {
  padding:13px 20px; border-bottom:1px solid var(--border);
  vertical-align:middle;
  overflow:hidden; text-overflow:ellipsis; white-space:nowrap;
}
table.tbl tbody td.wrap { white-space:normal }       /* 줄바꿈 허용 */
table.tbl tbody tr:last-child td { border-bottom:none }
table.tbl tbody tr:hover { background:var(--accent-light) }
table.tbl .num    { font-variant-numeric:tabular-nums; text-align:right }
table.tbl .center { text-align:center }
```

### 스크롤 바디 (.scroll-body)

테이블이 길어질 때 헤더를 고정하고 tbody만 스크롤.

```html
<div class="table-wrap">
  <div class="scroll-body" id="certScroll">   <!-- max-height:calc(100vh - 280px) -->
    <table class="tbl">...</table>
  </div>
</div>
```

```css
.scroll-body {
  max-height: calc(100vh - 280px);
  overflow-y: auto;
}
/* table.tbl thead th 는 기본적으로 position:sticky 적용 — 별도 override 불필요 */
```

### 페이지네이션 (.pagi)

```html
<div class="pagi">
  <span>총 8건</span>
  <div class="controls">
    <a href="#none">‹</a>
    <a href="#none" class="active">1</a>
    <a href="#none">2</a>
    <a href="#none">›</a>
  </div>
</div>
```

```css
.pagi {
  display:flex; justify-content:space-between; align-items:center;
  padding:10px 14px; border-top:1px solid var(--border);
  font-size:12px; color:var(--text-secondary); background:var(--surface);
}
.pagi .controls { display:flex; gap:4px; align-items:center }
.pagi .controls a {
  display:grid; place-items:center;
  width:28px; height:28px;
  border:1.5px solid var(--border); border-radius:var(--radius-sm);
  background:var(--surface); font-size:12px;
  color:var(--text-secondary); transition:.15s;
}
.pagi .controls a:hover { border-color:var(--accent); color:var(--accent) }
.pagi .controls a.active {
  background:linear-gradient(135deg,var(--accent),var(--accent-mid));
  color:#fff; border:none;
  box-shadow:0 2px 6px rgba(35,99,168,.3);
}
```

### 빈 상태 (.empty)

테이블·패널에 데이터가 없을 때 사용. `<tbody>` 안 또는 `.panel-body` 안에 단독 행으로 삽입한다.

```html
<!-- 테이블 내부 -->
<tbody>
  <tr>
    <td colspan="전체컬럼수" class="empty">조회된 데이터가 없습니다.</td>
  </tr>
</tbody>

<!-- 패널 내부 -->
<div class="panel-body">
  <div class="empty">등록된 항목이 없습니다.</div>
</div>
```

```css
.empty { text-align:center; padding:60px 20px; color:var(--text-tertiary); font-size:13px }
```

> - JS로 데이터 로드 후 결과가 0건이면 `<tbody>` 내용을 `.empty` 행으로 교체한다.
> - 인라인 스타일로 빈 상태를 처리하지 않는다 — 반드시 `.empty` 클래스를 사용한다.

### sticky 고정 컬럼 (.col-sticky-l / .col-sticky-r)

테이블에서 좌우 컬럼을 고정할 때 사용. `th` 와 `td` 모두 같은 클래스를 붙인다.

```html
<!-- 헤더 -->
<th class="col-sticky-l" style="width:120px">직원</th>
<th class="num col-sticky-r" style="width:60px">잔여</th>

<!-- 바디 -->
<td class="col-sticky-l">홍길동</td>
<td class="num col-sticky-r">13</td>
```

```css
.col-sticky-l { position:sticky; left:0 }
table.tbl thead .col-sticky-l { z-index:3; background:var(--bg) }
table.tbl tbody .col-sticky-l { z-index:1; background:var(--surface) }

.col-sticky-r { position:sticky; right:0; box-shadow:-2px 0 4px rgba(0,0,0,.06) }
table.tbl thead .col-sticky-r { z-index:3; background:var(--bg) }
table.tbl tbody .col-sticky-r { background:var(--surface); font-weight:600 }

/* 행 hover 시 sticky 셀도 동일 배경 */
table.tbl tbody tr:hover .col-sticky-l,
table.tbl tbody tr:hover .col-sticky-r { background:var(--accent-light) }
```

### 인라인 편집 셀 (.editable)

더블클릭으로 셀 값을 직접 수정하는 패턴. 연차 관리 총연차 셀에서 사용.

```html
<td class="num editable leave-total-cell"
    ondblclick="editTotal(this)" data-emp="홍길동">15</td>
```

```css
.editable { cursor:pointer; position:relative }
.editable:hover { background:var(--accent-light); outline:1px dashed var(--accent); outline-offset:-2px }
.editable.editing { padding:0; background:transparent; outline:none }
.editable input.edit-input {
  width:100%; height:100%; border:2px solid var(--accent);
  background:#fff; text-align:right; padding:10px 14px;
  font-size:13px; font-family:inherit; outline:none;
  font-variant-numeric:tabular-nums;
}
/* number input 스핀 버튼 제거 */
.editable input.edit-input::-webkit-outer-spin-button,
.editable input.edit-input::-webkit-inner-spin-button { -webkit-appearance:none; margin:0 }
```

### 파일 첨부 (.file-attach-label / .file-drop-zone)

```html
<!-- 인라인 첨부 버튼 -->
<label class="file-attach-label">
  <svg .../> 파일 첨부
  <input type="file" style="display:none">
</label>

<!-- 드래그앤드롭 영역 -->
<div class="file-drop-zone">파일을 여기에 드래그하거나 클릭하여 선택</div>
```

```css
.file-attach-label {
  display:inline-flex; align-items:center; gap:6px;
  height:30px; padding:0 12px; font-size:12px; font-weight:600;
  border:1.5px dashed var(--border); border-radius:var(--radius-md);
  cursor:pointer; color:var(--text-secondary); background:var(--bg); transition:.15s;
}
.file-attach-label:hover { border-color:var(--accent); color:var(--accent) }

.file-drop-zone {
  border:2px dashed var(--border); border-radius:var(--radius-lg);
  padding:24px 20px; text-align:center; cursor:pointer; background:var(--bg); transition:.2s;
}
.file-drop-zone:hover { border-color:var(--accent); background:var(--accent-light) }
```

### split 레이아웃 (.split / .split-list-row)

좌측 목록 + 우측 상세 2단 구조. FAQ 답변 관리 등에서 사용.

```html
<div class="split">
  <div class="panel">
    <div class="panel-body">   <!-- padding:0 으로 오버라이드 -->
      <div class="split-list-row active">
        <span class="name">질문 제목</span>
        <span class="cnt">2건</span>
      </div>
      ...
    </div>
  </div>
  <div class="panel">...</div>
</div>
```

```css
.split { display:grid; grid-template-columns:300px 1fr; gap:16px }
.split .panel { display:flex; flex-direction:column; min-height:480px }
.split .panel-body { flex:1; overflow-y:auto; padding:0 }  /* panel-body padding 오버라이드 */

.split-list-row { padding:12px 18px; border-bottom:1px solid var(--border); cursor:pointer; display:flex; align-items:center; gap:8px }
.split-list-row:hover { background:var(--accent-light) }
.split-list-row.active { background:var(--accent-light); border-left:3px solid var(--accent) }
.split-list-row .name { flex:1; font-size:13px; font-weight:500 }
.split-list-row .cnt  { font-size:11px; color:var(--text-tertiary) }
```

---

## 5. 패널 카드 (.panel)

대시보드, 직원 현황 등 구획된 콘텐츠 카드.

```html
<div class="panel">
  <div class="panel-head">
    <h3>직원 현황</h3>
    <span class="more">더보기</span>   <!-- 선택적 -->
  </div>
  <div class="panel-body">
    ...
  </div>
</div>
```

```css
.panel {
  background:var(--surface); border:none;
  border-radius:var(--radius-xl); overflow:hidden;
  box-shadow:var(--shadow-card);
}
.panel-head {
  padding:14px 18px; border-bottom:1px solid var(--border);
  display:flex; justify-content:space-between; align-items:center;
}
.panel-head h3  { font-size:14px; font-weight:700; letter-spacing:-0.01em }
.panel-head .more { font-size:12px; color:var(--accent); cursor:pointer; font-weight:600 }
.panel-body { padding:18px }
```

---

## 6. KPI 카드

대시보드 상단 4개 카드. 상단에 3px 컬러 바, 클릭 시 해당 페이지로 이동.

```html
<div class="kpi-grid">   <!-- grid-template-columns:repeat(4,1fr); gap:12px -->

  <div class="kpi kpi-leave" onclick="goPageByName('leave')">
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

```css
.kpi {
  background:var(--surface); border:none;
  border-radius:var(--radius-xl); padding:18px;
  box-shadow:var(--shadow-card);
  position:relative; overflow:hidden;
  cursor:pointer; transition:.15s;
}
.kpi::before {               /* 상단 3px 컬러 바 */
  content:''; position:absolute;
  top:0; left:0; right:0; height:3px;
  border-radius:var(--radius-xl) var(--radius-xl) 0 0;
}
.kpi:hover { transform:translateY(-2px) }

.kpi .label {
  font-size:11px; font-weight:700; color:var(--text-tertiary);
  margin-bottom:10px; text-transform:uppercase; letter-spacing:0.06em;
}
.kpi .value {
  font-size:28px; font-weight:800;
  letter-spacing:-0.03em; font-variant-numeric:tabular-nums;
}
```

| 테마 클래스 | 컬러 바 | label·value 색상 | hover 그림자 |
|-------------|---------|-----------------|-------------|
| `.kpi-leave` | `#1a54a8 → #4d8fd4` | `#1a54a8` | `rgba(26,84,168,.18)` |
| `.kpi-faq` | `#6b35c9 → #9b6de0` | `#6b35c9` | `rgba(107,53,201,.18)` |
| `.kpi-cert` | `#0d8f6e → #2bbf9a` | `#0d8f6e` | `rgba(13,143,110,.18)` |
| `.kpi-total` | `#c95f0a → #f08030` | `#c95f0a` | `rgba(201,95,10,.18)` |

```css
/* ── KPI 테마 CSS (각 클래스별 전체 코드) ── */

.kpi-leave::before { background:linear-gradient(90deg,#1a54a8,#4d8fd4) }
.kpi-leave .label  { color:#1a54a8 }
.kpi-leave .value  { color:#1a54a8 }
.kpi-leave:hover   { box-shadow:0 8px 24px rgba(26,84,168,.18) }

.kpi-faq::before   { background:linear-gradient(90deg,#6b35c9,#9b6de0) }
.kpi-faq .label    { color:#6b35c9 }
.kpi-faq .value    { color:#6b35c9 }
.kpi-faq:hover     { box-shadow:0 8px 24px rgba(107,53,201,.18) }

.kpi-cert::before  { background:linear-gradient(90deg,#0d8f6e,#2bbf9a) }
.kpi-cert .label   { color:#0d8f6e }
.kpi-cert .value   { color:#0d8f6e }
.kpi-cert:hover    { box-shadow:0 8px 24px rgba(13,143,110,.18) }

.kpi-total::before { background:linear-gradient(90deg,#c95f0a,#f08030) }
.kpi-total .label  { color:#c95f0a }
.kpi-total .value  { color:#c95f0a }
.kpi-total:hover   { box-shadow:0 8px 24px rgba(201,95,10,.18) }
```

---

## 7. 모달

> ⚠️ **배치 규칙**: 모달(`.modal-backdrop`)과 사이드 패널(`.leave-side-overlay` 등)은 **반드시 `</body>` 직전**에 선언한다.  
> `.page` 내부나 `.content` 안에 넣으면 `z-index` 가 `.content` 의 stacking context에 갇혀 전체 화면을 덮지 못한다.

### 기본 구조

```html
<!-- 백드롭 — body 끝에 선언 -->
<div class="modal-backdrop" id="myModal">
  <div class="modal">                          <!-- width:560px (기본) -->
    <div class="modal-head">
      <h3>타이틀</h3>
      <div class="close" onclick="document.getElementById('myModal').classList.remove('show')">
        <svg width="18" height="18" ...>X</svg>
      </div>
    </div>
    <div class="modal-body">
      ...
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn">취소</a>
      <a href="#none" class="btn btn-primary">저장</a>
    </div>
  </div>
</div>

<!-- 열기: classList.add('show') -->
<!-- 닫기: classList.remove('show') -->
```

```css
.modal-backdrop {
  position:fixed; inset:0;
  background:rgba(0,0,0,.4); z-index:1000;
  display:none; align-items:center; justify-content:center; padding:20px;
}
.modal-backdrop.show { display:flex }

.modal {
  background:var(--surface); border-radius:var(--radius-xl);
  width:560px; max-width:100%; max-height:90vh;
  display:flex; flex-direction:column;
  box-shadow:0 24px 60px -10px rgba(0,0,0,.25), 0 0 0 1px rgba(0,0,0,.06);
}
.modal-head {
  padding:16px 20px; border-bottom:1px solid var(--border);
  display:flex; justify-content:space-between; align-items:center;
  flex-shrink:0;
}
.modal-head h3  { font-size:15px; font-weight:800; letter-spacing:-0.01em }
.modal-head .close {
  width:30px; height:30px; display:grid; place-items:center;
  cursor:pointer; border-radius:var(--radius-sm);
  color:var(--text-secondary); background:var(--bg); border:1px solid var(--border);
}
.modal-head .close:hover { background:var(--surface-hover) }
.modal-body { padding:20px; overflow-y:auto; flex:1 }
.modal-foot {
  padding:14px 20px; border-top:1px solid var(--border);
  display:flex; justify-content:flex-end; gap:8px; flex-shrink:0;
}
```

### 모달 너비 변형

너비는 **인라인 스타일 금지 — CSS 클래스** 로 지정한다.

```html
<div class="modal modal--xs">...</div>   <!-- 400px — 확인 다이얼로그 -->
<div class="modal modal--sm">...</div>   <!-- 480px -->
<div class="modal modal--md">...</div>   <!-- 520px — FAQ 수정, 직원 불러오기 -->
<div class="modal modal--lg">...</div>   <!-- 680px — FAQ 답변 -->
<div class="modal modal--xl">...</div>   <!-- 700px — 업무신청관리 액션 -->
```

```css
.modal--xs { width:400px }
.modal--sm { width:480px }
.modal--md { width:520px }
.modal--lg { width:680px }
.modal--xl { width:700px }
```

---

## 8. 연차 결재현황 사이드 패널

우측에서 슬라이드인하는 패널. `.leave-side-overlay`(반투명 배경)와 세트.

```html
<!-- body 끝에 선언 -->
<div class="leave-side-overlay" id="lspOverlay" onclick="closeLeavePanel()"></div>
<div class="leave-side-panel"   id="lspPanel">

  <div class="lsp-head">
    <h3 id="lspTitle">연차 결재 현황</h3>
    <div class="lsp-close" onclick="closeLeavePanel()">✕</div>
  </div>

  <div class="lsp-body">

    <!-- 프로필 배너 (accent 그라디언트) -->
    <div class="lsp-profile">
      <div class="emp-name" id="lspName">홍길동</div>
      <div class="emp-meta" id="lspMeta">마케팅팀</div>
    </div>

    <!-- 연차 통계 3분할 -->
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

```css
/* 열기: overlay.classList.add('show'), panel.classList.add('show') */
/* 닫기: 반대로 remove */

.leave-side-overlay {
  position:fixed; inset:0;
  background:rgba(0,0,0,.25); z-index:500; display:none;
}
.leave-side-overlay.show { display:block }

.leave-side-panel {
  position:fixed; top:0; right:0;
  width:400px; height:100vh;
  background:var(--surface);
  box-shadow:-4px 0 24px rgba(0,0,0,.12);
  z-index:501; display:flex; flex-direction:column;
  transform:translateX(100%);
  transition:transform .25s cubic-bezier(.4,0,.2,1);
}
.leave-side-panel.show { transform:translateX(0) }

.lsp-profile {
  padding:20px;
  background:linear-gradient(135deg,var(--accent),var(--accent-mid));
  color:#fff;
}
.lsp-stats {
  display:grid; grid-template-columns:repeat(3,1fr);
  gap:1px; background:var(--border); border-bottom:1px solid var(--border);
}
.lsp-stat { padding:14px 0; text-align:center; background:var(--surface) }
.lsp-stat .sv { font-size:22px; font-weight:800; letter-spacing:-0.03em; color:var(--text) }
.lsp-stat .sl { font-size:11px; color:var(--text-tertiary); margin-top:3px; font-weight:600; text-transform:uppercase; letter-spacing:0.04em }
.lsp-stat.accent .sv { color:var(--accent) }
```

---

## 9. 폼 필드

모달 내부 폼 레이아웃.

```html
<!-- 2열 나란히 -->
<div class="form-row">
  <div class="field">
    <label>카테고리</label>
    <select>...</select>
  </div>
  <div class="field">
    <label>노출 상태</label>
    <select>...</select>
  </div>
</div>

<!-- 1열 전체 -->
<div class="field" style="margin-bottom:12px">
  <label>질문 <span style="color:var(--danger)">*</span></label>
  <input type="text" placeholder="질문을 입력하세요" />
</div>

<div class="field">
  <label>답변 <span style="color:var(--danger)">*</span></label>
  <textarea rows="6"></textarea>
</div>
```

```css
.form-row { display:flex; gap:12px; margin-bottom:12px }
.form-row > .field { flex:1 }

.field label {
  display:block; font-size:11px; font-weight:700;
  color:var(--text-tertiary);
  text-transform:uppercase; letter-spacing:0.06em; margin-bottom:6px;
}
.field input,
.field select,
.field textarea {
  width:100%; padding:9px 12px;
  border:1.5px solid var(--border); border-radius:var(--radius-md);
  font-size:13px; font-family:inherit;
  background:var(--surface); outline:none; transition:.15s;
}
.field input:focus,
.field select:focus,
.field textarea:focus {
  border-color:var(--accent);
  box-shadow:0 0 0 3px var(--accent-light);
}
```

> **textarea 높이 규칙**: CSS `height` 미지정 — `rows` 속성으로 제어한다.  
> 모달 내 답변 입력: `rows="6"` / 간단한 메모 입력: `rows="4"`  
> `resize:vertical` 은 기본값(브라우저 허용) — 별도 제한 없음.

---

## 10. 토스트 (.toast-v7)

하단 중앙 슬라이드업 알림. body 끝에 선언.

```html
<div class="toast-v7" id="toastV7"></div>

<!-- 서브 텍스트 포함 시 -->
<div class="toast-v7" id="toastV7">
  메시지 텍스트
  <div class="sub">부연 설명</div>
</div>
```

```js
// 표시
function showToastV7(msg) {
  const t = document.getElementById('toastV7');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2800);
}
```

> `msg` 단일 인자. 이모지 포함 가능 (`'✅ 발급이 완료되었습니다.'`).  
> 2800ms 후 자동 소멸.

```css
.toast-v7 {
  position:fixed; bottom:32px; left:50%;
  transform:translateX(-50%) translateY(100px);
  background:#18181b; color:white;
  padding:14px 24px; border-radius:10px;
  font-size:13px; font-weight:500;
  box-shadow:0 10px 30px rgba(0,0,0,.2);
  z-index:9999; transition:transform 0.3s;
  pointer-events:none; max-width:90%; text-align:center;
}
.toast-v7.show { transform:translateX(-50%) translateY(0) }
.toast-v7 .sub { font-size:11px; color:rgba(255,255,255,.65); margin-top:3px }
```

---

## 11. 미처리 업무 알림 행 (.alert-row)

대시보드 좌측 패널의 각 알림 항목.

```html
<div class="alert-row" onclick="..." style="cursor:pointer">
  <div class="alert-icon cert">   <!-- cert / faq / leave -->
    <svg .../>
  </div>
  <div class="alert-body">
    <div class="alert-title">재직증명서 발급 요청</div>
    <div class="alert-meta"><strong>홍길동</strong> · 마케팅팀 · 2시간 전</div>
  </div>
  <div class="alert-actions" onclick="event.stopPropagation()">
    <a href="#none" class="btn btn-sm btn-primary">발급</a>
  </div>
</div>
```

```css
.alert-row {
  display:flex; align-items:center; gap:12px;
  padding:12px 18px; border-bottom:1px solid var(--border);
  transition:background .15s;
}
.alert-row:hover { background:var(--surface-hover) }
.alert-row:last-child { border-bottom:none }

.alert-icon {
  width:34px; height:34px; border-radius:10px;
  display:grid; place-items:center; flex-shrink:0;
}
.alert-icon.cert  { background:var(--info-bg);    color:var(--info) }
.alert-icon.faq   { background:var(--warning-bg); color:var(--warning) }
.alert-icon.leave { background:var(--success-bg); color:var(--success) }

.alert-title {
  font-size:13px; font-weight:600; color:var(--text);
  margin-bottom:2px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;
}
.alert-meta { font-size:11px; color:var(--text-tertiary) }
```

---

## 12. 연차 월별 툴팁 (.leave-tooltip / .month-used-nonzero)

연차 관리 테이블의 핵심 인터랙션. 월별 사용 일수가 0이 아닌 셀을 클릭하면 상세 내역을 툴팁으로 표시.

#### HTML

```html
<!-- body 최하단에 1개만 배치 (position:fixed 이므로 DOM 위치 무관) -->
<div class="leave-tooltip" id="leaveTooltip"></div>
```

#### CSS

```css
/* 툴팁 컨테이너 */
.leave-tooltip {
  position: fixed;
  background: #ffffff; color: #111118;
  padding: 10px 14px; border-radius: 10px;
  font-size: 12px; font-weight: 500;
  pointer-events: none;
  z-index: 9999; display: none;
  box-shadow: 0 6px 24px rgba(0,0,0,.15);
  white-space: pre; line-height: 1.8;
  border: 1px solid #e8e8ef;
}
/* 위쪽 삼각형 화살표 */
.leave-tooltip::before {
  content: ''; position: absolute;
  top: -6px; left: 50%; transform: translateX(-50%);
  width: 10px; height: 10px; background: #ffffff;
  clip-path: polygon(50% 0, 100% 100%, 0 100%);
  border-top: 1px solid #e8e8ef;
}

/* 연차 테이블 — 0이 아닌 월별 셀 */
.month-used-nonzero {
  color: #0f3870 !important;
  font-weight: 600 !important;
  cursor: pointer;
  position: relative;
}
.month-used-nonzero:hover {
  text-decoration: underline;
  text-underline-offset: 3px;
  text-decoration-color: #0f3870;
}
/* 클릭(pin)된 셀 */
.month-used-nonzero.tt-active {
  text-decoration: underline;
  text-underline-offset: 3px;
  text-decoration-color: #0f3870;
  background: var(--accent-light);
}
```

#### 동작 방식

| 이벤트 | 동작 |
|--------|------|
| 0이 아닌 셀 렌더 시 | `.month-used-nonzero` 클래스 부여 (파랑 폰트) |
| hover | underline 표시 |
| 클릭 | 툴팁 고정(pin) + `.tt-active` 배경색, 셀 기준 아래 중앙에 위치 |
| 같은 셀 재클릭 | 툴팁 닫기 |
| 다른 곳 클릭 / 스크롤 | 툴팁 닫기 |

#### 툴팁 내용 형식

```
홍길동 · 4월 사용내역
──────────────────
04월 15일 오후반차(0.5일)
```

`white-space:pre`로 개행 그대로 출력. `leaveTooltipEl.textContent`에 `\n` 구분 문자열 직접 할당.

#### JS 구조 (요약)

```js
// 데이터
const LEAVE_MONTH_DETAILS = {
  '홍길동': { 1: ['01월 08일 연차(1일)'], 4: ['04월 15일 오후반차(0.5일)'] },
  // 직원명: { 월번호(1~12): ['날짜 종류(일수)', ...] }
};

// 핵심 함수
attachLeaveTooltips()   // DOMContentLoaded 시 호출 — 0이 아닌 셀에 이벤트 부착
_posTooltipAboveCell(cell)  // 셀 아래 중앙 위치 계산 + 화면 밖 보정
_closeLeaveTooltip()    // 툴팁 숨김 + tt-active 제거
```

> **월 인덱스 매핑**: 연차 테이블 `td` 기준 index 4 = 1월, index 15 = 12월 (`month = index - 3`)

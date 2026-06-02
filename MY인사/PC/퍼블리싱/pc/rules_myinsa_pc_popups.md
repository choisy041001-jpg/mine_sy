---
name: rules_myinsa_pc_popups
description: "MY인사 PC v31 모달·팝업·사이드패널 전체 목록, ID·사이즈·열기함수·내부구조 — 팝업 수정·추가 시 참조"
metadata: 
  node_type: memory
  type: project
  originSessionId: c4cb22ee-5bf5-4a66-91cc-1113065fd4e6
---

**Why:** MY인사_PC_v31.html에 팝업 14종이 있음. 팝업 수정 시 HTML 파일 전체 스캔 없이 바로 찾기 위해.
**How to apply:** 기본 모달 구조는 [[rules_myinsa_pc_components_core]] §6 참조. 이 파일은 각 팝업의 ID·사이즈·개봉함수·상세 구조만 기록.

---

## 0. 공통 패턴

```html
<!-- 기본 모달 구조 -->
<div class="modal-backdrop" id="[모달ID]">
  <div class="modal modal--[xs|sm|md|lg]">
    <div class="modal-head">
      <h3>타이틀</h3>
      <div class="close" onclick="document.getElementById('[모달ID]').classList.remove('show')">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
        </svg>
      </div>
    </div>
    <div class="modal-body">...</div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
      <a href="#none" class="btn btn-primary">확인</a>
    </div>
  </div>
</div>
```

**열기:** `document.getElementById('모달ID').classList.add('show')`
**닫기:** `document.getElementById('모달ID').classList.remove('show')`

### 신규 팝업 JS 함수 추가 위치

`MY인사_PC_v31.html` 내 `<script>` 블록은 아래 순서로 구성된다.  
신규 `openXxxModal()` 함수는 반드시 **블록4** 에 추가한다.

```
[블록1] 전역 데이터
  const PAGE_META = { ... };
  const SIDEBAR_PROFILES = { ... };

[블록2] 페이지 전환 함수
  function goPage(el) { ... }
  function goPageByName(name) { ... }
  function switchRole(role) { ... }

[블록3] 공통 유틸
  function showToastV7(msg) { ... }
  function attachLeaveTooltips() { ... }

[블록4] ← 신규 팝업 함수 여기에 추가
  function openLeavePanel(...) { ... }
  function openCertIssueModal(...) { ... }
  function openFaqDetailModal(...) { ... }
  ...

[블록5] 페이지별 핸들러
  // [leave 페이지]
  function editTotal(td) { ... }
  // [cert 페이지] ...
  // [faq 페이지] ...
```

> **규칙:** 같은 계열 팝업 함수(예: cert 계열) 바로 다음에 배치한다. 함수명 중복 금지 — 추가 전 `Ctrl+F`로 검색 확인.

**⚠️ 닫기 버튼 태그 구분 규칙**

| 버튼 위치 | 태그 | 이유 |
|---|---|---|
| 모달 우상단 × (닫기) | `<div class="close">` | CSS `.close`가 div 전용으로 정의됨. `<a>` 사용 시 스타일 깨짐 |
| 하단 "취소" 버튼 | `<a href="#none" class="btn">` | 일반 버튼 — button 태그 금지 규칙 적용 |
| 하단 "확인/저장" 버튼 | `<a href="#none" class="btn btn-primary">` | 동일 |

```html
<!-- ✅ 올바른 패턴 -->
<div class="modal-head">
  <h3>타이틀</h3>
  <div class="close" onclick="document.getElementById('xxxModalBg').classList.remove('show')">
    <!-- X 아이콘 SVG -->
  </div>
</div>
<div class="modal-foot">
  <a href="#none" class="btn" onclick="document.getElementById('xxxModalBg').classList.remove('show')">취소</a>
  <a href="#none" class="btn btn-primary">확인</a>
</div>
```

**사이즈 토큰**

| 클래스 | 너비 | 용도 |
|---|---|---|
| `.modal--xs` | 400px | 확인 다이얼로그 |
| `.modal--sm` | 480px | 단순 폼 |
| `.modal--md` | 520px | 중간 폼 |
| (기본) | 560px | 기본 |
| `.modal--lg` | 680px | 대형 콘텐츠 |
| `.modal--xl` | 700px | 초대형 |

---

## 1. 전체 팝업 목록

### `</body>` 직전 모달 배치 순서

신규 모달 추가 시 아래 계열 순서를 따른다. **계열이 다른 모달 사이에 삽입 금지.**

```
1. 공통 계열    — kakaoModalBg
2. FAQ 계열     — faqDetailModalBg, editFaqModal, deleteFaqModal, addFaqModal
3. 연차 계열    — lspPanel, lspOverlay (사이드패널)
4. 직원 불러오기 — importEmpModal
5. 증명서 계열  — certIssueModalBg, certHistoryModalBg, certRejectModalBg, certReasonModalBg
6. 직원 신청 계열 — empCertStep1Bg, empCertStep2Bg, hrInfoModalBg, hrEditModalBg
7. 토스트        — #toastV7
```

> 신규 모달은 가장 유사한 계열의 **마지막 모달 다음**에 배치한다.

> **행 번호는 참고용** — 파일 편집에 따라 달라질 수 있음. 실제 위치는 ID로 검색할 것.

| ID | 이름 | 사이즈 | 오픈 방법 | HTML 위치(행) |
|---|---|---|---|---|
| kakaoModalBg | 카카오톡 발송 | 기본 | `openKakaoSingle(id)` (개별) / `openKakaoBulk()` (일괄) | 1378 |
| faqDetailModalBg | FAQ 답변 작성/보기 | modal--lg | `openFaqDetailModal(name, mode)` | 1527 |
| lspPanel + lspOverlay | 연차 결재현황 사이드패널 | — | `openLeavePanel(...)` | 1582 |
| importEmpModal | 경리나라 직원 불러오기 | modal--md | 연차 관리 툴바 `+ 일반근로자 불러오기` 버튼 → `classList.add('show')` | 1621 |
| editFaqModal | FAQ 수정 | modal--md | `openFaqEdit(cat,q,a,btn)` | 1695 |
| deleteFaqModal | FAQ 삭제 확인 | modal--xs | `confirmFaqDelete()` | 1737 |
| addFaqModal | FAQ 등록 | modal--md | `classList.add('show')` | 1768 |
| certIssueModalBg | 증명서 발급처리 | modal--md | `openCertIssueModal(name,kind,purpose,date)` | 1804 |
| certHistoryModalBg | 발급 이력보기 | modal--sm | `openCertHistoryModal(name,kind,date)` | 1852 |
| certRejectModalBg | 증명서 반려 | modal--sm | `openCertRejectModal(name,kind)` | 1886 |
| certReasonModalBg | 반려 사유 보기 | modal--sm | `openCertReasonModal(name,reason)` | 1913 |
| empCertStep1Bg | 직원 증명서 신청 step1 | modal--sm | `openEmpCertModal()` | 3019 |
| empCertStep2Bg | 직원 증명서 신청 step2 | modal--sm | `openEmpCertStep2(type)` | 3063 |
| hrInfoModalBg | 직원 인사정보 추가 | modal--sm | `openHrInfoModal()` | 3095 |
| hrEditModalBg | 직원 인사정보 수정 | modal--sm | `openHrEditModal(type,v1,v2,v3,v4)` | 3239 |

---

## 2. 사이드 패널 (모달과 다른 구조)

```html
<div class="leave-side-overlay" id="lspOverlay" onclick="closeLeavePanel()"></div>
<div class="leave-side-panel" id="lspPanel">
  <div class="lsp-head">
    <h3 id="lspTitle">연차 결재 현황</h3>
    <div class="lsp-close" onclick="closeLeavePanel()"><!-- X SVG --></div>
  </div>
  <div class="lsp-body">
    <div class="lsp-profile">
      <div class="emp-name" id="lspName">홍길동</div>
      <div class="emp-meta" id="lspMeta">마케팅팀</div>
    </div>
    <div class="lsp-stats">
      <div class="lsp-stat"><div class="sv" id="lspTotal">15</div><div class="sl">총 연차</div></div>
      <div class="lsp-stat"><div class="sv" id="lspUsed">3</div><div class="sl">사용</div></div>
      <div class="lsp-stat accent"><div class="sv" id="lspRemain">12</div><div class="sl">잔여</div></div>
    </div>
    <div class="lsp-section">
      <div class="lsp-section-title">결재 현황</div>
      <div id="lspApprList"><!-- JS로 채움 --></div>
    </div>
  </div>
</div>
```

열기: `.show` 클래스 추가가 아닌 별도 CSS transform 처리.
`openLeavePanel(name, dept, total, used, remain, status)` 함수 사용.

---

## 3. 팝업별 상세 구조

### 3-1. kakaoModalBg — 카카오톡 발송

**주의:** 이 팝업만 `style="display:none"` 초기값 사용 (다른 팝업과 다름)

```html
<div class="modal-backdrop" id="kakaoModalBg" style="display:none">
  <div class="modal">
    <div class="modal-head">
      <h3 id="kakaoModalTitle">💬 카카오톡 발송</h3>
      <div class="close" onclick="closeKakaoModal()">...</div>
    </div>
    <div class="modal-body">
      <div class="field mb-6">
        <label>📞 받는 사람</label>
        <div class="info-box" id="kakaoTargetInfo">-</div>
      </div>
      <div class="field mb-6">
        <label>📝 발송 메시지</label>
        <textarea id="kakaoMsgInput" rows="10" class="textarea"
          oninput="updateKakaoPreview()"></textarea>
        <div class="field-hint">
          💡 <span class="code-chip">{이름}</span> 자리에 직원 이름 자동 입력
        </div>
      </div>
      <div class="kakao-preview">
        <div class="kakao-bubble" id="kakaoPreviewBubble"></div>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="closeKakaoModal()">취소</a>
      <a href="#none" class="btn btn-kakao" onclick="sendKakao()">💬 카카오톡 발송</a>
    </div>
  </div>
</div>
```

### 3-2. faqDetailModalBg — FAQ 답변 작성/보기

2-mode: `'write'`(답변 대기) / `'view'`(답변 완료)

```html
<div class="modal-backdrop" id="faqDetailModalBg">
  <div class="modal modal--lg">
    <div class="modal-head">
      <div>
        <h3 id="faqDetailModalTitle">답변 작성</h3>
        <div class="item-sub" id="faqDetailModalMeta"></div>
      </div>
      <div class="flex-center">
        <span id="faqDetailBadge" class="badge w">답변 대기</span>
        <div class="close" onclick="closeFaqDetailModal()">...</div>
      </div>
    </div>
    <div class="modal-body modal-body--flush">
      <!-- 질문 영역 -->
      <div class="modal-section">
        <div class="faq-detail-q" id="faqDetailQuestion"></div>
        <div class="faq-detail-body" id="faqDetailBody"></div>
        <div class="faq-detail-meta" id="faqDetailMeta2"></div>
      </div>
      <!-- 답변 완료 시 -->
      <div id="faqDetailAnswerView" class="modal-section--sep" style="display:none">
        <div class="section-label">등록된 답변</div>
        <div class="faq-answer-box" id="faqDetailAnswerText"></div>
        <div class="faq-answer-meta" id="faqDetailAnswerMeta"></div>
      </div>
      <!-- 답변 대기 시 -->
      <div id="faqDetailWriteArea" class="modal-section--plain">
        <div class="section-label">답변 작성 <span class="req">*</span></div>
        <textarea id="faqDetailTextarea" rows="8" class="textarea"></textarea>
        <div class="file-attach-row">
          <label class="file-attach-label">
            <!-- 클립 SVG --> 파일 첨부
            <input type="file" multiple style="display:none"
              onchange="handleFaqAnswerFile(this,'faqDetailFileList')" />
          </label>
          <span class="item-meta">PDF · 이미지 · 문서 · 최대 10MB</span>
        </div>
        <div id="faqDetailFileList" class="file-list"></div>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="closeFaqDetailModal()">닫기</a>
      <a href="#none" class="btn btn-primary" id="faqDetailSubmitBtn" onclick="submitFaqAnswer()">
        <!-- 전송 SVG --> 답변 등록
      </a>
    </div>
  </div>
</div>
```

`openFaqDetailModal(name, 'write'|'view')` — 모드에 따라 write/view 영역 토글

### 3-3. importEmpModal — 경리나라 직원 불러오기

```html
<div class="modal-backdrop" id="importEmpModal">
  <div class="modal modal--md">
    <div class="modal-head">
      <div>
        <h3>경리나라 직원 불러오기</h3>
        <div class="item-sub">연차 관리에 등록할 직원을 확인하세요</div>
      </div>
      <div class="close" onclick="...classList.remove('show')">...</div>
    </div>
    <div class="modal-body p-0">
      <!-- 상단 배너 -->
      <div class="modal-banner">
        <div>경리나라에서 <strong class="text-accent">미등록 직원 5명</strong>이 확인됩니다</div>
        <span class="badge i">재직 중</span>
      </div>
      <!-- 스크롤 목록 -->
      <div class="modal-scroll-list">
        <div class="list-row" id="impRow1">
          <div class="icon-avatar icon-avatar--accent">김</div>
          <div class="flex-1">
            <div class="item-bold">김지원</div>
            <div class="item-meta-t">개발팀 · 2024.01.02 입사</div>
          </div>
          <a href="#none" class="btn btn-sm btn-primary" onclick="importSingle(this,'impRow1')">등록</a>
        </div>
        <!-- 추가 행 동일 패턴 -->
      </div>
    </div>
    <div class="modal-foot modal-foot--between">
      <a href="#none" class="btn" onclick="...classList.remove('show')">닫기</a>
      <a href="#none" class="btn btn-primary">
        <!-- 체크 SVG --> 전체 일괄 등록
      </a>
    </div>
  </div>
</div>
```

### 3-4. editFaqModal — FAQ 수정

```html
<div class="modal-backdrop" id="editFaqModal">
  <div class="modal modal--md">
    <div class="modal-head"><h3>FAQ 수정</h3>...</div>
    <div class="modal-body">
      <div class="form-row">
        <div class="field"><label>카테고리</label>
          <select id="editFaqCat"><option>연차</option><option>증명서</option><option>급여</option><option>기타</option></select>
        </div>
        <div class="field"><label>노출 상태</label>
          <select id="editFaqVisible"><option>노출 중</option><option>비노출</option></select>
        </div>
      </div>
      <div class="field mb-5"><label>질문 <span class="req">*</span></label>
        <input type="text" id="editFaqQ" /></div>
      <div class="field"><label>답변 <span class="req">*</span></label>
        <textarea rows="6" id="editFaqA"></textarea></div>
    </div>
    <div class="modal-foot modal-foot--between">
      <a href="#none" class="btn btn--danger" onclick="confirmFaqDelete()">
        <!-- 삭제 SVG --> 삭제
      </a>
      <div class="btn-group">
        <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
        <a href="#none" class="btn btn-primary" onclick="saveFaqEdit()">저장</a>
      </div>
    </div>
  </div>
</div>
```

### 3-5. deleteFaqModal — FAQ 삭제 확인

취소 시 `editFaqModal`을 다시 열어야 함 (흐름 유의)

```html
<div class="modal-backdrop" id="deleteFaqModal">
  <div class="modal modal--xs">
    <div class="modal-head"><h3>FAQ 삭제</h3>...</div>
    <div class="modal-body">
      <div class="confirm-row">
        <div class="confirm-icon"><!-- danger 삼각형 SVG --></div>
        <div>
          <div class="confirm-title">이 FAQ를 삭제하시겠습니까?</div>
          <div class="confirm-body" id="deleteFaqPreview"></div>
          <div class="confirm-warn">삭제 후 복구할 수 없습니다.</div>
        </div>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn"
        onclick="...deleteFaqModal classList.remove('show'); ...editFaqModal classList.add('show')">취소</a>
      <a href="#none" class="btn btn--danger" onclick="executeFaqDelete()">
        <!-- 삭제 SVG --> 삭제 확정
      </a>
    </div>
  </div>
</div>
```

### 3-6. addFaqModal — FAQ 등록

editFaqModal과 동일 구조. `modal-foot--between` 없음, footer에 취소+등록만.

### 3-7. certIssueModalBg — 증명서 발급처리

발급 모달에서 반려 버튼 클릭 시 `certRejectModalBg`로 연결됨.

```html
<div class="modal-backdrop" id="certIssueModalBg">
  <div class="modal modal--md">
    <div class="modal-head">
      <div>
        <h3 id="certIssueTitle">증명서 발급 처리</h3>
        <div class="item-sub" id="certIssueMeta"></div>
      </div>
      <div class="close" onclick="...classList.remove('show')">...</div>
    </div>
    <div class="modal-body">
      <div class="flex-col">
        <div class="modal-notice">
          <!-- info SVG -->
          <span>직접 작성한 PDF 파일을 첨부하여 발급합니다...</span>
        </div>
        <div>
          <label class="label-sm">증명서 파일 첨부 <span class="req">*</span></label>
          <div class="file-drop-zone" onclick="document.getElementById('certIssueFileInput').click()">
            <!-- 업로드 SVG -->
            <div class="drop-title">PDF 파일을 클릭하여 업로드</div>
            <div class="item-meta">PDF · 최대 10MB</div>
            <input type="file" id="certIssueFileInput" accept=".pdf" style="display:none"
              onchange="handleModalIssueFile(this)" />
          </div>
          <div id="certIssueFileList" class="mt-3"></div>
        </div>
        <div>
          <label class="label-sm">인사담당자 메모 <span class="text-opt">(선택, 직원에게 전달)</span></label>
          <textarea rows="3" class="textarea"></textarea>
        </div>
      </div>
    </div>
    <div class="modal-foot modal-foot--between">
      <!-- 왼쪽: 반려 (certRejectModalBg로 전환) -->
      <a href="#none" class="btn btn--link-danger"
        onclick="...certIssueModalBg.classList.remove('show'); ...certRejectModalBg.classList.add('show')">반려</a>
      <div class="btn-group">
        <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
        <a href="#none" class="btn btn-primary" style="min-width:120px" onclick="[파일검증+발급완료]">
          <!-- 체크 SVG --> 발급 완료
        </a>
      </div>
    </div>
  </div>
</div>
```

### 3-8. certHistoryModalBg — 발급 이력보기

footer 없음 (조회 전용)

```html
<div class="modal-backdrop" id="certHistoryModalBg">
  <div class="modal modal--sm">
    <div class="modal-head">
      <div>
        <h3 id="certHistoryTitle">발급 이력</h3>
        <div class="item-sub" id="certHistoryMeta"></div>
      </div>
      <div class="close" onclick="...classList.remove('show')">...</div>
    </div>
    <div class="modal-body">
      <div class="section-card">
        <div class="panel-section-hd">발급 상세</div>
        <div class="info-row"><span class="text-secondary">증명서 종류</span><strong id="certHistoryKind">재직증명서</strong></div>
        <div class="info-row"><span class="text-secondary">신청자</span><strong id="certHistoryName2">-</strong></div>
        <div class="info-row"><span class="text-secondary">발급일시</span><span id="certHistoryDate">-</span></div>
        <div class="info-row"><span class="text-secondary">발급 담당자</span><span>한관리자</span></div>
        <div class="info-row"><span class="text-secondary">수령 방법</span><span>앱 내 다운로드</span></div>
      </div>
      <div class="section-card" id="certHistoryFileSection">
        <div class="panel-section-hd">첨부파일</div>
        <div id="certHistoryFileList" class="section-body">
          <!-- JS로 .cert-file-item 생성 -->
        </div>
      </div>
      <div class="section-card">
        <div class="panel-section-hd">인사담당자 메모</div>
        <div id="certHistoryMemo" class="section-content"></div>
      </div>
    </div>
    <!-- footer 없음 -->
  </div>
</div>
```

### 3-9. certRejectModalBg — 증명서 반려

```html
<div class="modal-backdrop" id="certRejectModalBg">
  <div class="modal modal--sm">
    <div class="modal-head"><h3>신청 반려</h3>...</div>
    <div class="modal-body">
      <div class="modal-summary">
        <div class="row-between mb-2"><span class="text-secondary">신청자</span><strong id="certRejectApplicant">-</strong></div>
        <div class="row-between"><span class="text-secondary">증명서 종류</span><span id="certRejectKind">-</span></div>
      </div>
      <div class="field">
        <label>반려 사유 <span class="req">*</span></label>
        <textarea rows="5"></textarea>
      </div>
      <div class="note">* 작성한 사유는 신청자에게 전달됩니다.</div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
      <a href="#none" class="btn btn--danger" onclick="[반려확정]">반려 확정</a>
    </div>
  </div>
</div>
```

### 3-10. certReasonModalBg — 반려 사유 보기

```html
<div class="modal-backdrop" id="certReasonModalBg">
  <div class="modal modal--sm">
    <div class="modal-head"><h3>반려 사유</h3>...</div>
    <div class="modal-body">
      <div id="certReasonText" class="modal-reason-box"></div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn btn-primary" onclick="...classList.remove('show')">확인</a>
    </div>
  </div>
</div>
```

### 3-11. empCertStep1Bg + empCertStep2Bg — 직원 증명서 신청 (2단계)

**Step 1:** 종류 선택
```html
<div class="modal-backdrop" id="empCertStep1Bg">
  <div class="modal modal--sm">
    <div class="modal-head"><h3>증명서 신청 — 종류 선택</h3>
      <div class="close" onclick="closeEmpCertModal()">...</div>
    </div>
    <div class="modal-body modal-body--list">
      <a href="#none" class="btn cert-type-btn" onclick="openEmpCertStep2('재직증명서')">
        <div class="icon-avatar icon-avatar--cert"><!-- SVG --></div>
        <div class="text-left">
          <div class="item-md">재직증명서</div>
          <div class="item-meta">현재 재직 사실을 증명하는 문서 · 국문/영문</div>
        </div>
        <!-- 화살표 SVG -->
      </a>
      <!-- 경력증명서(icon-avatar--career), 기타증명서(icon-avatar--other) 동일 패턴 -->
    </div>
  </div>
</div>
```

**Step 2:** 용도 입력
```html
<div class="modal-backdrop" id="empCertStep2Bg">
  <div class="modal modal--sm">
    <div class="modal-head">
      <div>
        <h3 id="empCertStep2Title">재직증명서 신청</h3>
        <div class="item-sub">신청 정보를 입력해주세요</div>
      </div>
      <div class="close" onclick="closeEmpCertModal()">...</div>
    </div>
    <div class="modal-body">
      <div class="field mb-5">
        <label>제출처 / 용도 <span class="req">*</span></label>
        <input type="text" id="empCertPurpose" />
      </div>
      <div class="field">
        <label>요청 메모 <span class="label-opt">(선택)</span></label>
        <textarea id="empCertMemo" rows="4"></textarea>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="closeEmpCertModal()">취소</a>
      <a href="#none" class="btn btn-primary" onclick="submitEmpCert()">
        <!-- 전송 SVG --> 신청하기
      </a>
    </div>
  </div>
</div>
```

**JS 흐름:**
- `openEmpCertModal()` → Step1 열기
- `openEmpCertStep2(type)` → Step1 닫고 Step2 열기
- `backToStep1()` → Step2 닫고 Step1 열기
- `closeEmpCertModal()` → 모두 닫기

### 3-12. hrInfoModalBg — 직원 인사정보 추가

유형 선택 후 동적으로 폼 필드 변경

```html
<div class="modal-backdrop" id="hrInfoModalBg">
  <div class="modal modal--sm">
    <div class="modal-head"><h3>인사 정보 추가</h3>...</div>
    <div class="modal-body">
      <div class="field mb-5">
        <label>항목 유형 <span class="req">*</span></label>
        <select id="hrInfoType" onchange="updateHrInfoFields(this.value)">
          <option value="">선택해주세요</option>
          <option value="cert">자격증</option>
          <option value="edu">학력</option>
          <option value="lang">어학</option>
          <option value="award">수상 / 표창</option>
          <option value="etc">기타</option>
        </select>
      </div>
      <!-- 유형 선택 후 표시 (JS updateHrInfoFields 처리) -->
      <div id="hrInfoFields" style="display:none">
        <div class="form-row" id="hrInfoRow1">
          <div class="field"><label id="hrInfoLabel1">명칭 <span class="req">*</span></label>
            <input type="text" id="hrInfoVal1" /></div>
          <div class="field"><label id="hrInfoLabel2">발급 기관</label>
            <input type="text" id="hrInfoVal2" /></div>
        </div>
        <div class="form-row">
          <div class="field"><label>취득일 / 기간</label><input type="text" id="hrInfoDate" /></div>
          <div class="field"><label>점수 / 등급 (선택)</label><input type="text" id="hrInfoScore" /></div>
        </div>
        <div class="field"><label>메모 (선택)</label>
          <textarea id="hrInfoMemo" rows="3"></textarea></div>
      </div>
    </div>
    <div class="modal-foot">
      <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
      <a href="#none" class="btn btn-primary" onclick="submitHrInfo()">저장</a>
    </div>
  </div>
</div>
```

`updateHrInfoFields(type)` — 유형에 따라 레이블·placeholder 변경

### 3-13. hrEditModalBg — 직원 인사정보 수정

hrInfoModalBg와 유사. select 없이 바로 입력 필드. 삭제 버튼 포함.

```html
<div class="modal-backdrop" id="hrEditModalBg">
  <div class="modal modal--sm">
    <div class="modal-head"><h3 id="hrEditModalTitle">항목 수정</h3>...</div>
    <div class="modal-body">
      <div class="form-row" id="hrEditRow1">
        <div class="field"><label id="hrEditLabel1">명칭 <span class="req">*</span></label>
          <input type="text" id="hrEditVal1" /></div>
        <div class="field"><label id="hrEditLabel2">기관</label>
          <input type="text" id="hrEditVal2" /></div>
      </div>
      <div class="form-row">
        <div class="field"><label id="hrEditLabel3">취득일 / 기간</label>
          <input type="text" id="hrEditVal3" /></div>
        <div class="field"><label id="hrEditLabel4">점수 / 등급 (선택)</label>
          <input type="text" id="hrEditVal4" /></div>
      </div>
    </div>
    <div class="modal-foot modal-foot--between">
      <a href="#none" class="btn btn--link-danger"
        onclick="...classList.remove('show'); showToastV7('항목이 삭제되었습니다.')">
        <!-- 삭제 SVG --> 삭제
      </a>
      <div class="btn-group">
        <a href="#none" class="btn" onclick="...classList.remove('show')">취소</a>
        <a href="#none" class="btn btn-primary" onclick="saveHrEdit()">저장</a>
      </div>
    </div>
  </div>
</div>
```

`openHrEditModal(type, v1, v2, v3, v4)` — `HR_TYPE_LABELS` 객체로 타입별 레이블 자동 세팅

---

## 4. 팝업 전용 클래스

### 4-1. 카카오 발송 팝업 (kakaoModalBg)

| 클래스 | 설명 |
|---|---|
| `.kakao-preview` | 카카오 미리보기 박스 (#ffeb3b bg, border-radius:16px, max-width:320px, mx:auto). `::before` → "💬 카카오톡 미리보기" 레이블 |
| `.kakao-bubble` | 말풍선 (white bg, border-radius:12px, padding:12px 14px, pre-wrap) |
| `.kakao-btn` | **테이블 행 독립 버튼** (#FEE500 bg, #3C1E1E, --radius-sm). `.btn` 불필요. JS가 테이블 행에 렌더링. **`.btn-kakao`와 다름 — 혼용 금지.** |
| `.btn.btn-kakao` | **모달 내 버튼** (`.btn` 위에 수식어). `<a class="btn btn-kakao">`. 카카오 팝업 foot 버튼에 사용. **`.kakao-btn`과 다름 — 혼용 금지.** |

### 4-2. 삭제 확인 팝업 (deleteFaqModal)

| 클래스 | 설명 |
|---|---|
| `.confirm-row` | 확인 다이얼로그 행 (flex, gap:--sp-6, align-items:flex-start) |
| `.confirm-icon` | 아이콘 박스 (40×40px, --radius-lg, --danger-bg, grid place-items:center) |
| `.confirm-title` | 확인 제목 (--fs-md, --fw-bold, mb:6px) |
| `.confirm-body` | 확인 본문 (--fs-body, --text-secondary, line-height:1.6) |
| `.confirm-warn` | 경고 텍스트 (--fs-sm, --danger, mt:--sp-4, --fw-medium) |

### 4-3. FAQ 답변 팝업 (faqDetailModalBg)

| 클래스 | 설명 |
|---|---|
| `.faq-detail-q` | 질문 텍스트 (--fs-lg, --fw-bold, line-height:1.5, mb:10px) |
| `.faq-detail-body` | 질문 본문 (--fs-body, --text-secondary, line-height:1.7, --surface bg, border, --radius-md) |
| `.faq-detail-meta` | 질문 메타 (flex, gap:14px, --fs-xs, --text-tertiary, mt:10px) |
| `.faq-answer-box` | 등록된 답변 박스 (--fs-body, line-height:1.7, --accent-light bg, border:#bfd9ef, --radius-md) |
| `.faq-answer-meta` | 답변 메타 (--fs-xs, --text-tertiary, mt:--sp-3) |

---

## 5. 신규 팝업 추가 절차

### ID 명명 규칙

| 패턴 | 언제 사용 | 예시 |
|---|---|---|
| `xxxModalBg` | backdrop(`<div class="modal-backdrop">`) id | `certIssueModalBg`, `kakaoModalBg` |
| `xxxModal` | backdrop 없이 단독 div id (소수) | `importEmpModal`, `editFaqModal` |

→ **신규는 `xxxModalBg` 패턴 권장** — `modal-backdrop` 클래스에 id를 붙이는 방식으로 통일.

### 사이즈 결정 기준

| 사이즈 | 너비 | 사용 기준 |
|---|---|---|
| `.modal--xs` | 400px | 확인/삭제 다이얼로그 (2줄 이내 메시지) |
| `.modal--sm` | 480px | 단순 폼 (1~2개 필드) |
| `.modal--md` | 520px | 중간 폼 (3~5개 필드) |
| (기본) | 560px | 표준 폼 |
| `.modal--lg` | 680px | 대형 콘텐츠 (미리보기, 긴 목록) |
| `.modal--xl` | 700px | 초대형 (단계별 UI) |

### 신규 팝업 추가 체크리스트

1. **HTML** — `</body>` 직전에 `modal-backdrop` 블록 추가
   ```html
   <!-- ★ 모달·사이드패널은 반드시 </body> 직전에 선언 -->
   <div class="modal-backdrop" id="xxxModalBg">
     <div class="modal modal--sm">
       <div class="modal-head">
         <h3>타이틀</h3>
         <div class="close" onclick="document.getElementById('xxxModalBg').classList.remove('show')">
           <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
             <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
           </svg>
         </div>
       </div>
       <div class="modal-body">...</div>
       <div class="modal-foot">
         <a href="#none" class="btn" onclick="document.getElementById('xxxModalBg').classList.remove('show')">취소</a>
         <a href="#none" class="btn btn-primary">확인</a>
       </div>
     </div>
   </div>
   ```

2. **JS 열기 함수** — 단순 열기 or 데이터 주입 패턴 중 택일
   ```js
   // 단순 열기 (데이터 불필요)
   function openXxxModal() {
     document.getElementById('xxxModalBg').classList.add('show');
   }

   // 데이터 주입 패턴 (파라미터 받아서 폼 채움)
   function openXxxModal(param1, param2) {
     document.getElementById('xxx-field').textContent = param1;
     document.getElementById('xxxModalBg').classList.add('show');
   }
   ```

3. **팝업 목록 등록** — 이 파일 `## 1. 전체 팝업 목록` 표에 행 추가
   ```
   | xxxModalBg | 팝업 설명 | modal--sm | openXxxModal(param) | 행번호 |
   ```

4. **CSS (필요 시)** — `my_cont.css` 파일 맨 끝 `@keyframes` 블록 앞에 새 섹션 추가
   ```css
   /* ── xxx 팝업 ── */
   .xxx-class { ... }
   ```

> **절대 금지:** `<button>` 태그 사용. 닫기·확인 버튼 모두 `<a href="#none" class="btn ...">` 사용.

---

## 6. 신규 사이드패널 추가 절차

모달과 다른 구조. **오버레이 div + 패널 div 쌍**으로 구성한다.  
기존 참조: `lspPanel` + `lspOverlay` (연차 결재현황)

### HTML (모달과 동일하게 `</body>` 직전)

```html
<!-- 오버레이: 클릭 시 패널 닫기 -->
<div class="xxx-side-overlay" id="xxxOverlay" onclick="closeXxxPanel()"></div>

<!-- 패널 본체 -->
<div class="xxx-side-panel" id="xxxPanel">
  <div class="xsp-head">
    <h3 id="xspTitle">패널 제목</h3>
    <div class="xsp-close" onclick="closeXxxPanel()">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/>
      </svg>
    </div>
  </div>
  <div class="xsp-body">
    <!-- 패널 콘텐츠 -->
  </div>
</div>
```

### CSS (`my_cont.css` 해당 페이지 섹션에 추가)

```css
/* 오버레이 */
.xxx-side-overlay {
  position: fixed; inset: 0;
  background: var(--overlay-bg, rgba(0,0,0,0.35));
  z-index: var(--z-overlay);   /* 500 */
  display: none;
}
.xxx-side-overlay.show { display: block; }

/* 패널 */
.xxx-side-panel {
  position: fixed; top: 0; right: 0;
  width: 400px; height: 100vh;
  background: var(--surface);
  z-index: var(--z-panel);    /* 501 */
  transform: translateX(100%);
  transition: transform var(--transition-panel);
  overflow-y: auto; display: flex; flex-direction: column;
}
.xxx-side-panel.show { transform: translateX(0); }
```

### JS 함수 (블록4에 추가)

```js
function openXxxPanel(/* 필요한 파라미터 */) {
  // 데이터 주입
  document.getElementById('xspTitle').textContent = '...';
  // 표시
  document.getElementById('xxxOverlay').classList.add('show');
  document.getElementById('xxxPanel').classList.add('show');
}

function closeXxxPanel() {
  document.getElementById('xxxOverlay').classList.remove('show');
  document.getElementById('xxxPanel').classList.remove('show');
}
```

### 팝업 목록 등록 (§1 표에 추가)

```
| xxxPanel + xxxOverlay | 패널 설명 | — | openXxxPanel(...) | 행번호 |
```

> **모달과 차이:** 모달은 `.show`만 제어, 사이드패널은 오버레이+패널 **두 개** 모두 제어. z-index는 `--z-overlay`(500) / `--z-panel`(501) 사용.

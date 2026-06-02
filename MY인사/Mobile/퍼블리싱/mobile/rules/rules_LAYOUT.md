# MY인사 모바일 — 레이아웃 구조

앱 전체 레이아웃 구조, 화면 전환, 오버레이 시스템, 하단 여백 규칙을 기술한다.

---

## 앱 루트 구조

`html`, `body`, `.phone` 모두 `height:100%`로 전체화면을 채운다.

```
html (height:100%)
└── body (height:100%)
    └── .phone (height:100%)          ← 앱 루트 컨테이너
        └── .app                      ← flex col, position:relative, height:100%
            ├── .screen[data-screen]  ← 기본 display:none
            ├── .screen.active        ← display:block, overflow-y:auto
            ├── .tabbar               ← position:absolute; bottom:0
            ├── .sheet-backdrop       ← position:absolute; inset:0 (바텀시트)
            └── [오버레이 ID들]       ← position:absolute; inset:0
```

```html
<div class="phone">
  <div class="app">

    <div class="screen active" data-screen="home">
      <!-- 콘텐츠 + .pb-tabbar 로 탭바 높이 확보 -->
    </div>

    <div class="screen" data-screen="leave">…</div>

    <!-- 탭바: position:absolute;bottom:0 -->
    <div class="tabbar">…</div>

    <!-- 바텀시트: position:absolute;inset:0 -->
    <div class="sheet-backdrop" id="mySheet">
      <div class="sheet">…</div>
    </div>

    <!-- 오버레이: position:absolute;inset:0, style="display:none" -->
    <div id="postActionSheet" style="display:none">…</div>

  </div>
</div>
```

| 클래스 | CSS 속성 | 설명 |
|--------|----------|------|
| `.phone` | height:100% | 앱 루트. 전체화면 |
| `.app` | flex col; position:relative; height:100% | `.tabbar`, 오버레이의 absolute 기준점 |
| `.screen` | display:none (기본) | JS로 전환. 동시에 하나만 active |
| `.screen.active` | display:block; overflow-y:auto; padding-bottom:90px | 현재 화면 |

---

## 화면 전환

`showScreen(id)` 함수로 `.screen.active`를 토글한다.

```js
// 내부 동작
document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
document.querySelector(`[data-screen="${id}"]`).classList.add('active');
```

화면 ID는 `data-screen` 속성으로 지정한다.

```html
<div class="screen" data-screen="leave-apply">…</div>
```

---

## 탭바

`.app`(position:relative) 안에서 `position:absolute;bottom:0`으로 고정된다.  
콘텐츠가 탭바 아래 가려지지 않도록 콘텐츠 영역에 반드시 하단 여백을 추가해야 한다.

```
탭바 높이: 64px  →  콘텐츠 padding-bottom 최소 80px 필요
```

---

## 하단 여백 유틸리티

`.screen.active`에는 기본 `padding-bottom:90px`이 내장되어 있다.  
상황에 따라 아래 유틸 클래스를 사용해 재정의한다.

| 클래스 | 값 | 사용 상황 |
|--------|----|-----------|
| `.pb-tabbar` | padding-bottom:80px | 탭바 있는 화면 기본 여백 (필수) |
| `.mb-tabbar` | margin-bottom:80px | 마지막 섹션이 탭바에 걸릴 때 |
| `.pb90` | padding-bottom:90px | 하단 고정 버튼(.bottom-action) 있는 화면 |
| `.pb73` | padding-bottom:73px | 탭바 없는 서브 화면 |
| `.pb0` | padding-bottom:0 | 전체 화면 오버레이, 탭바 없는 화면 |
| `.mb0` | margin-bottom:0 | 폼 섹션 마지막 항목 하단 마진 제거 |

---

## 바텀시트

`.sheet-backdrop`에 `.show` 클래스를 추가/제거해 열고 닫는다.  
`display:none`이 인라인에 없으므로 JS는 classList만 조작한다.

```html
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
```

```js
// 열기
document.getElementById('mySheet').classList.add('show');
// 닫기
document.getElementById('mySheet').classList.remove('show');
```

---

## 오버레이 시스템

바텀시트형(`modal-backdrop-sheet`)과 중앙 팝업형(`modal-backdrop-center`) 두 가지 베이스 클래스를 사용한다.  
`display:none`은 반드시 인라인에 유지 — JS가 `element.style.display`를 직접 조작하므로 CSS 클래스로 대체하면 동작하지 않는다.

```html
<!-- 바텀시트형 (하단에서 올라옴) -->
<div class="modal-backdrop-sheet" id="boardFilterSheet" style="display:none">
  <div class="sheet">…</div>
</div>

<!-- 중앙 팝업형 -->
<div class="modal-backdrop-center" id="certModal" style="display:none">
  <div class="modal">…</div>
</div>
```

```css
/* 공통 베이스 — display:flex는 JS가 style.display로 제어 */
.modal-backdrop-sheet { position:absolute; inset:0; align-items:flex-end; justify-content:center; }
.modal-backdrop-center { position:absolute; inset:0; align-items:center; justify-content:center; }
```

```js
// 열기
document.getElementById('boardFilterSheet').style.display = 'flex';
// 닫기
document.getElementById('boardFilterSheet').style.display = 'none';
```

### z-index 계층

오버레이마다 z-index와 배경 투명도가 달라 CSS에서 ID 셀렉터로 개별 지정한다.

| ID | background | z-index |
|----|-----------|---------|
| `#boardFilterSheet` | rgba(0,0,0,.45) | 300 |
| `#certModal` | rgba(0,0,0,.45) | 300 |
| `#emailSheet` | rgba(0,0,0,.45) | 350 |
| `#postActionSheet` | rgba(0,0,0,.45) | 350 |
| `#postDeleteConfirmOverlay` | rgba(0,0,0,.5) | 380 |
| `#deleteConfirmOverlay` | rgba(0,0,0,.5) | 380 |
| `#pfResultOverlay` | rgba(0,0,0,.5) | 400 |
| `#approvalLinePopup` | rgba(0,0,0,.5) | 400 |
| `#datetimePickerOverlay` | rgba(0,0,0,.5) | 400 |
| `#workplacePopup`, `#switchWorkplacePopup` | rgba(0,0,0,.55) | 400 |
| `#imgViewOverlay` | rgba(0,0,0,.85) | 350 |

---

## 태블릿 분할 레이아웃 (≥900px)

`@media (min-width:900px)` 안에 정의된 규칙으로, JS가 `.app`에 상태 클래스를 추가하면 CSS가 자동으로 분할 레이아웃을 적용한다.  
너비 조건은 CSS 한 곳에서만 관리 — JS에서 별도로 체크하지 않는다.

### 분할 패턴 3가지

| 상태 클래스 | 좌측 (50%) | 우측 (50%) | 진입 경로 |
|------------|-----------|-----------|---------|
| `.has-detail` | `board` | `post-detail` | 게시판에서 게시글 클릭 |
| `.has-faq` | `more` | `post-list` | 더보기 → 인사 FAQ |
| `.has-faq-detail` | `more` | `post-detail` | `.has-faq` 상태에서 게시글 클릭 |

### JS 클래스 관리

```js
// 분할 활성화 (각 진입 함수 내부)
document.querySelector('.app').classList.add('has-detail');   // 게시글 클릭 시
document.querySelector('.app').classList.add('has-faq');      // 인사 FAQ 클릭 시
document.querySelector('.app').classList.add('has-faq-detail'); // FAQ 목록에서 게시글 클릭 시

// 분할 해제 — showScreen() 또는 goTab() 호출 시 자동 처리
document.querySelector('.app').classList.remove('has-detail','has-faq','has-faq-detail');
```

### 리사이즈 반응형 전환

모바일 상태에서 창을 넓혀 900px를 넘으면 현재 화면에 맞는 분할 레이아웃으로 자동 전환된다.  
반대로 태블릿 분할 중 900px 미만으로 좁히면 우측 패널 화면을 풀스크린으로 유지한다.

| 리사이즈 방향 | 조건 | 결과 |
|-------------|------|------|
| 모바일 → 태블릿 | `post-detail` 활성 + history에 `board` | `has-detail` (board 좌 · post-detail 우) |
| 모바일 → 태블릿 | `post-list` 활성 + history에 `more` | `has-faq` (more 좌 · post-list 우) |
| 모바일 → 태블릿 | `post-detail` 활성 + history에 `post-list` | `has-faq-detail` (more 좌 · post-detail 우) |
| 태블릿 → 모바일 | `has-detail` 활성 | `post-detail` 풀스크린, history에 `board` 복원 |
| 태블릿 → 모바일 | `has-faq` 활성 | `post-list` 풀스크린, history에 `more` 복원 |
| 태블릿 → 모바일 | `has-faq-detail` 활성 | `post-detail` 풀스크린, history에 `more`·`post-list` 복원 |

구현 위치: `index_m.html` — `window.addEventListener('resize', ...)`(`_prevWidth`로 경계 통과 시점만 감지)

### has-faq-detail 뒤로가기

`has-faq-detail` 상태에서 뒤로가기를 누르면 일반 `showScreen()` 흐름이 아닌 `has-faq` 복원으로 처리한다.

```js
function goBack(){
  const app = document.querySelector('.app');
  if(app.classList.contains('has-faq-detail')){
    app.classList.remove('has-faq-detail');
    app.classList.add('has-faq');
    return;
  }
  // ... 기존 히스토리 스택 처리
}
```

### 탭바 · FAB · comment-input

| 요소 | 모바일 | 태블릿 분할 |
|------|--------|------------|
| `.tabbar` | `width:100%` | `width:50%` (좌측 패널에만) |
| `.fab-bottom-right` | `right:18px; bottom:76px` | `right:calc(50% + 18px)` (좌측 패널 우측 끝) |
| `.comment-input` | `left:0; right:0; bottom:64px` | `left:50%; bottom:0; padding:13px 12px 14px` (우측 패널에만) |

### 탑바 높이 클래스

태블릿에서 좌우 패널 탑바 높이를 맞추기 위해 각 화면 탑바에 클래스를 부여한다.

| 클래스 | 적용 화면 | `min-height` | 추가 속성 |
|--------|---------|-------------|---------|
| `.topbar-board` | `board` | 69px | — |
| `.topbar-more` | `more` | 68px | `padding-top:21px` |
| `.topbar-leave` | `leave-approval` | 70px | `border-bottom:2px solid var(--border)` |

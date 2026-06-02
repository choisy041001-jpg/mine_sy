# MY인사 모바일

모바일 인사 관리 앱 UI 프로토타입. 빌드 도구 없이 브라우저에서 바로 실행되는 단일 HTML 파일 구조.

---

## 파일 구조

```
MY인사/
├── mobile/
│   ├── index_m.html                       # 앱 본체
│   ├── mobile-style-guide.html            # 모바일 컴포넌트 스타일 가이드
│   ├── css/
│   │   └── myinsa-mobile.css              # 앱 전용 스타일시트
│   ├── rules_README.md                    # 프로젝트 개요 (이 파일)
│   ├── rules_LAYOUT.md                    # 레이아웃 구조 레퍼런스
│   ├── rules_COMPONENTS.md                # 컴포넌트 레퍼런스
│   ├── rules_DECISIONS.md                 # 결정 맥락
│   └── rules_FLOWMAP.md                   # 화면 전환 플로우맵
└── _common/
    └── fonts_Pretendard/                  # Pretendard 폰트
```

---

## 실행 방법

별도 서버 불필요. HTML 파일을 브라우저에서 직접 열면 된다.

```
index_m.html                       → 앱 프로토타입 확인
mobile-style-guide.html            → 컴포넌트 레퍼런스 확인
```

---

## CSS 아키텍처

### 파일

| 파일 | 역할 |
|------|------|
| `../_common/my_tokens.css` | 공통 디자인 토큰 + Pretendard @font-face. `myinsa-mobile.css` 보다 먼저 로드 |
| `css/myinsa-mobile.css` | 앱 전용 스타일. 토큰 재정의 → 리셋 → 컴포넌트 → 화면별 → 태블릿 순서로 구성 |

`@media (min-width:900px)` 블록으로 태블릿 분할 레이아웃 대응. 자세한 내용은 `rules_LAYOUT.md` — 태블릿 분할 레이아웃 참고.

#### 토큰 사용 규칙
- `myinsa-mobile.css`에서는 **세트 A 토큰만** 사용한다 (`--bg`, `--accent`, `--text` 계열)
- `--primary-normal`, `--label-*`, `--bg-normal` 등 세트 B 토큰은 `index.html` 전용이며 모바일 앱에서 사용 금지
- 세트 A에 없는 값이 필요할 경우: 임의 값 사용 금지. 세트 A에 토큰을 추가한 뒤 사용할 것
- `my_tokens.css`는 직접 수정하지 않는다. 모바일 전용 재정의는 `myinsa-mobile.css` 상단 DESIGN TOKENS 블록에서 처리

### CSS 섹션 순서

```
DESIGN TOKENS        ← 모바일 전용 재정의 및 추가 토큰 (my_tokens.css 공통 토큰 상속)
ANIMATIONS           ← @keyframes (pulse, fadeIn, slideUp, fadeScaleIn, fabRise)
PRESENTATION LAYOUT  ← 프리젠테이션 래퍼 (프로토타입 전용)
USER FLOW PANEL      ← 흐름 다이어그램 패널
INFO PANEL           ← 정보 패널 (.info-panel .panel-header 포함)
PHONE FRAME          ← .phone 앱 루트 컨테이너 (height:100%)
APP CONTAINER        ← .app, .screen
TOPBAR               ← .topbar, .topbar.with-back
TABBAR               ← .tabbar, .tab
BOTTOM SHEET         ← .sheet-backdrop, .sheet
OVERLAYS             ← ID 셀렉터 (#boardFilterSheet, #postActionSheet 등)
UTILITIES            ← 레이아웃·타이포·색상·패딩·여백 유틸 클래스
HOME                 ← 홈 화면 컴포넌트
BOARD                ← 게시판
FAQ                  ← FAQ
MORE                 ← 더보기
알림 화면            ← 알림·알림설정
LOGIN                ← 로그인
COMMON               ← 공용 컴포넌트 (폼, 토글, 칩, 캘린더 등)
EXPENSE              ← 비용처리
BBS FLAT ROW         ← 게시판 플랫 행
QUICK ACTION GRID    ← 홈 퀵 액션 카드
게시글 액션시트      ← .action-sheet-*
결재선 변경 팝업     ← .alp-*
사용일시 선택 팝업   ← .dt-picker-*, .dt-cal-*, .dt-time-*
TABLET               ← @media (min-width:900px) 태블릿 분할 레이아웃
```

### 디자인 토큰 주요 변수

| 토큰 | 값 | 용도 |
|------|----|------|
| `--accent` | `#2363a8` | 주 브랜드 컬러 |
| `--bg` | `#f7f7fa` | 앱 배경 |
| `--surface` | `#ffffff` | 카드·시트 배경 |
| `--text` | `#111118` | 기본 텍스트 |
| `--text-secondary` | `#4a4a5a` | 보조 텍스트 |
| `--text-tertiary` | `#9898a8` | 힌트·메타 텍스트 |
| `--border` | `#e8e8ef` | 구분선 |
| `--radius-sm/md/lg/xl` | 8/10/14/18px | 모서리 반경 |

---

## 레퍼런스 문서

| 파일 | 내용 |
|------|------|
| `mobile-style-guide.html` | 모든 컴포넌트 실제 렌더링 + HTML 스니펫 |
| `rules_LAYOUT.md` | 앱 루트 구조, 화면 전환, 오버레이, 하단 여백 |
| `rules_COMPONENTS.md` | 컴포넌트별 클래스·HTML 레퍼런스 |
| `rules_DECISIONS.md` | 설계 결정 배경과 이유 |
| `rules_FLOWMAP.md` | 화면 전환 플로우, 진입/이탈 경로, Mermaid 다이어그램 |

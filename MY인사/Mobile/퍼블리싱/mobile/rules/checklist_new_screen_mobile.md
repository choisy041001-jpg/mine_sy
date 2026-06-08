# 신규 화면 추가 체크리스트 (MY인사 모바일)

> 새 `data-screen`을 추가할 때 아래 7단계를 순서대로 처리한다.
> 목적: 새 화면이 (a) 어디에도 안 걸려 "고립"되거나, (b) 인라인·하드코딩으로 규칙을 깨거나,
> (c) 기존 화면을 건드리는 일이 없도록 한다.
> 배치 권장: `rules_LAYOUT.md` 하단 섹션으로 편입 (PC의 layout.md 11번 항목과 동일 위치).

---

## 1단계 — `.screen` 블록 생성 (스캐폴드)

`.app` 안에 추가한다.

```html
<!-- ==== SCREEN:new-screen START ==== -->
<div class="screen" data-screen="new-screen">
  …
</div>
<!-- ==== SCREEN:new-screen END ==== -->
```

- `.active`는 **붙이지 않는다** — `showScreen()`이 관리. HTML에서 active는 첫 진입 화면만.
- 콘텐츠에 하단 여백 유틸 추가(탭바 가림 방지): 탭 화면 `.pb-tabbar`, 하단 고정 버튼 화면 `.pb90`, 탭 없는 서브 화면 `.pb73`, 전체 오버레이 `.pb0`.
- 앵커 주석(START/END)으로 감싸 작업 경계를 만든다.

## 2단계 — 진입·이탈 경로 연결 ★ 고립 방지 (가장 중요)

새 화면으로 **들어오는 호출**과 **나가는 경로**를 반드시 만든다.

- 진입: 어딘가에서 `showScreen('new-screen')` / `goTab(...)` / `goPostList(...)` / `openX()` 호출 추가.
- 이탈/뒤로가기: `goBack()` 히스토리로 복귀되는지 확인.
- 탭 루트 화면이면: `TAB_SCREEN` 매핑 추가 + `.tabbar`에 탭 추가.

> ⚠ 진입 경로 없이 두면 FLOWMAP에서 "⚠ 고립"으로 표기되고, 이후 "정리 대상"으로 오인·삭제될 수 있다.
> (지난 산출물에서 보호 화면 5개가 이 경로로 삭제됨.)

## 3단계 — `rules_FLOWMAP.md` 등록 ★ 필수

화면 행을 추가한다: `data-screen` / 진입 / 이탈 / 오버레이.

- 의도적으로 진입이 없는 보류성 화면이라면, **같은 줄에 "보류 — 삭제 금지" 딱지를 명시**한다.
- 보류 여부의 최종 판단은 `rules_DECISIONS.md` 고립 화면 표가 권위를 가진다. FLOWMAP은 그걸 가리킨다.

> 이렇게 해야 "고립=정리" 신호와 "보류=유지" 규칙이 충돌하지 않는다.

## 4단계 — 스타일·토큰

- 기존 클래스 우선 사용 (`rules_COMPONENTS.md` 카탈로그에서 먼저 탐색).
- 신규 스타일은 **인라인 금지**, 클래스로 분리 CSS(또는 `myinsa-mobile.css` 해당 섹션)에 작성.
- **세트 A 토큰만** `var(--accent)` 식으로 참조. 하드코딩 색·여백 금지.
- 없는 토큰은 `myinsa-mobile.css` 상단 DESIGN TOKENS 블록에 추가 (`my_tokens.css` 불가침, 세트 B 사용 금지).
- 인라인 허용은 이 3종뿐: `display:none`(오버레이) · JS가 쓰는 동적 값 · 테이블/컬럼 가변 너비.

## 5단계 — 오버레이가 있으면

- 인라인엔 `display:none`만. `position`/`background`/`z-index`는 CSS **ID 셀렉터**(`#newOverlay{}`)로.
- `rules_LAYOUT.md` z-index 계층 표에 ID·z-index·배경 투명도를 등록.
- 열기/닫기 함수 추가 후 `rules_COMPONENTS.md` JS 함수 표에 기재.

## 6단계 — 접근성·프로토타입 범위

- 클릭 요소는 `<button>`, 라벨은 `label for`, 이미지 `alt`, 오버레이 `aria-*`.
- 입력값 상태 저장·서버 연동은 **명시 지시가 없으면 구현하지 않는다** (DECISIONS 프로토타입 범위).

## 7단계 — 문서 동기화 + 게이트

- 새 컴포넌트면 `rules_COMPONENTS.md`와 `mobile-style-guide.html`을 **동시** 갱신.
- `BASELINE_index_m.md`의 화면 수·manifest 갱신 (예: 31 → 32).
- `mockup_analyzer` 통과 확인: 인라인 OTHER · 하드코딩 색 · 정의 안 된 토큰 = **0**.
- 기존 화면 31종이 모두 그대로 존재하는지 BASELINE과 대조.

---

## 빠른 검증 (작업 직후)

- [ ] 새 화면에 진입 호출이 1개 이상 있다 (고립 아님)
- [ ] FLOWMAP에 진입·이탈이 등록됐다 (보류면 "삭제 금지" 표기)
- [ ] 신규 스타일이 인라인이 아니라 클래스로 들어갔다
- [ ] 세트 A 토큰만 썼고 하드코딩 색이 없다
- [ ] 기존 화면·`<script>`·`<head>`·CSS 링크를 건드리지 않았다
- [ ] 화면 수가 +1만 됐고 줄바꿈(CRLF)이 유지됐다

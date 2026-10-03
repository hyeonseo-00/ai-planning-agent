# Design System Agent

> 디자인 규칙이 정의된 Figma Section을 분석해 **`rules/design-system.md`(원시 토큰)와 `rules/design-application.md`(적용 규칙)**를 생성·갱신한다.
> Figma에 명시되지 않은 값은 임의 확정하지 않는다.

---

## 역할

- **입력:** 태스크 `입력물`에 명시된 디자인 규칙 Figma 소스. **여러 node일 수 있다.** 예:
  - `node-id=XX-XXXXX` — Variables (색상·폰트·간격·radius 토큰)
  - `node-id=XX-XXXX` — 디자인 적용 규칙 캔버스 (컴포넌트별 스펙·상태·사이즈)
- **출력:**
  - `rules/design-system.md` — Palette / Semantic Color / Typography / Spacing / Border·Radius / Shadow / Layout·Grid
  - `rules/design-application.md` — 레이아웃 구조 / 반응형 / 타이포 클래스 / 컴포넌트별 스펙(사이즈·상태·정책) / 아이콘·이미지 규칙 / 알려진 이슈
- **담당:** 파이프라인 밖 (사람이 `/dsys {번호}` 직접 실행). 모든 화면의 공통 기준.

---

## 사고 순서

### STEP 0. 대상 파악 (다중 소스 순서)

- 태스크 `입력물`에서 **소스 node 목록**을 뽑는다. 여러 개면 아래 순서로 처리한다:
  1. **토큰/Variables 소스** (색·폰트·간격·radius) → `design-system.md`
  2. **적용 규칙/컴포넌트 소스** (레이아웃·타이포 클래스·컴포넌트 스펙) → `design-application.md`
  - **토큰 먼저, 컴포넌트 나중.** `design-application.md`가 `design-system.md`의 토큰명(`brand/default`, `text-title-sb-16`)을 참조하므로.
- 각 node를 `get_metadata`로 조회. Variables는 `get_variable_defs`도. 테이블이 이미지로만 렌더돼 있으면 `get_metadata`의 텍스트 레이어에서 파싱.
- 입력물에 "산출: design-system.md 만" 처럼 **한 파일만** 지정돼 있으면 그것만 만든다.
- **이미 `rules/design-system.md`·`design-application.md`가 있고** 입력물에 "재추출"/"처음부터" 지시가 없으면 → **대조 후 누락분만 보강**. "재추출"/"처음부터"면 → 전체 재작성 (기존 파일은 `/dsys` 실행 전 사람이 `.bak` 백업).

### STEP 1. design-system.md (원시 토큰)

수집 항목 (Figma에 있는 것만):
- **Palette:** 색 그룹별 단계 (Brand/900~20, neutral/900~0, status/*)
- **Semantic Color:** fg/* surface/* stroke/* brand/* — 참조 관계까지 (`brand/default = Brand/500 (#2563EB)` 형태로 풀어서)
- **Typography:** font family, size 스케일, weight, line-height, letter-spacing
- **Spacing:** space/* 스케일
- **Border/Radius:** radius/* , border-width/*
- **Shadow:** shadow/*
- **Layout/Grid:** container max, columns, gutter, column width

각 토큰은 표로. Figma 원본의 오타(midium/nomal 등)는 **추적성 위해 원본대로 유지**하고 "알려진 이슈"에 기록.

### STEP 2. design-application.md (적용 규칙)

- **레이아웃 구조:** MainLayout(사이드바 폭 펼침/접힘 등)
- **반응형:** 브레이크포인트별 동작
- **타이포 클래스:** `text-{역할}-{weight}-{size}` 네이밍 규칙 + 클래스 표
- **컴포넌트별 스펙:** Button/Input/Card/Dropdown/Chips/Tooltip/Toast/ToggleSwitch/StatusTag/MessageBubble 등 — variant / 상태명 / 사이즈 / 정책. Figma에 있는 것만.
- **아이콘·이미지 규칙:** 아이콘 세트, 사이즈(24/32)
- **알려진 이슈:** 죽은 클래스, 미정의 토큰, 오타 — Screen/Implementation Agent가 "정상 동작"으로 쓰지 않도록 명시

### STEP 3. [확인 필요] 처리

Figma에서 값이 잘리거나 안 보이는 것은 `[확인 필요]`로 표기. 창작 금지.

### STEP 4. 저장 + 필드 갱신

- `rules/design-system.md`, `rules/design-application.md` 저장(또는 보강).
- 태스크 `산출물`에 두 경로 기록, `현재 단계`=완료, `➡️ 다음 실행`=`완료`.
- `메모`에 "커버한 node / 보강 내역 / [확인 필요] 개수" 기록.

---

## 출력

- 갱신한 파일 · 새로 추가된 토큰/컴포넌트 · `[확인 필요]` 목록
- (이 태스크는 대개 여기서 완료)

## 금지 패턴

- Figma에 없는 토큰 값을 채워 넣는 것
- 기존 `design-system.md` 토큰명을 임의로 바꾸는 것 (다른 산출물이 참조 중)
- 원본 오타를 "고쳐서" 기록하는 것 (추적성 파괴 — 원본 유지 + 이슈 명시)
- 컴포넌트 상태를 Figma 변형 없이 창작하는 것

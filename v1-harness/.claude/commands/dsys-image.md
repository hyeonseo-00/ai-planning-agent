---
description: Design System (이미지) — 디자인 스크린샷 이미지를 토큰·적용규칙 문서로 뽑는다 (파이프라인 밖, 노션 불필요, 사람이 직접 실행)
argument-hint: "{.claude/design-inputs/ 아래 폴더명 또는 절대경로}"
---

`.claude/rules/design-system.md` / `design-application.md`와 같은 형식으로 만들되, 소스가 **Figma가 아니라 이미지**라는 점만 다르다. `.claude/agents/design-system-agent.md`의 판단 원칙(창작 금지, 원본 오타 유지, [확인 필요] 표기)을 그대로 따른다. 노션 태스크 조회는 하지 않는다.

## STEP 0. 대상 파악

- `$ARGUMENTS`가 `.claude/design-inputs/` 아래 폴더명이면 `.claude/design-inputs/{$ARGUMENTS}/`를, 절대/상대 경로면 그 경로를 이미지 소스 폴더로 삼는다.
- 폴더 안의 이미지 파일(png/jpg/jpeg 등)을 전부 나열한다. 파일이 없으면 즉시 중단하고 사람에게 알린다 — 창작하지 않는다.
- 각 이미지를 읽어(Read 도구로 시각 분석) 무엇을 담고 있는지 먼저 분류한다:
  - **토큰형** — 색상 팔레트, 폰트 스케일, spacing/radius 표, Figma Variables 패널 캡처 등 → `design-system.md` 대상
  - **적용규칙형** — 실제 화면/컴포넌트 스크린샷, 버튼·인풋·뱃지 등 UI 조합 → `design-application.md` 대상
  - 분류 불명확한 이미지는 두 문서 후보에 모두 걸어두고 실제 내용에 따라 STEP 1/2에서 배치한다.

## STEP 1. design-system.md (원시 토큰) — 이미지에서 읽을 수 있는 것만

- **Palette:** 이미지에 보이는 색상 스와치와 그 옆 라벨/hex값. hex가 안 보이면 스포이드 추정하지 말고 `[확인 필요: hex 값 안 보임]`.
- **Semantic:** 라벨이 `fg/*`, `surface/*` 처럼 의미 토큰명으로 붙어 있는 경우만. 참조 관계(`brand/default = Brand/500`)가 이미지에 명시된 경우만 옮긴다.
- **Typography:** family/size/weight/line-height/letter-spacing 중 이미지에 실제로 표기된 값만.
- **Spacing / Radius / Shadow / Layout:** 이미지에 수치가 적혀 있는 것만. 비율을 눈대중으로 재서 숫자를 만들어내지 않는다.
- 표 형식은 기존 [design-system.md](.claude/rules/design-system.md)와 동일하게 맞춘다.

## STEP 2. design-application.md (적용 규칙) — 이미지에서 읽을 수 있는 것만

- 레이아웃 구조, 반응형 동작, 타이포 클래스명, 컴포넌트별 variant/상태/사이즈 — 이미지 안에 라벨이나 주석으로 명시된 것만.
- 스크린샷만 있고 라벨이 없는 컴포넌트는 형태를 설명하되 클래스명·토큰명은 `[확인 필요: 라벨 없음]`으로 남긴다. 클래스명을 추측해서 짓지 않는다.
- 기존 [design-application.md](.claude/rules/design-application.md)와 같은 절 구성(레이아웃/반응형/타이포/컴포넌트/아이콘/알려진 이슈)을 따른다.

## STEP 3. 저장 위치 — 기존 파일과 분리

기존 `rules/design-system.md`·`design-application.md`는 Figma 기반 `/dsys` 전용 SSoT이므로 **덮어쓰지 않는다**. 대신:

- `.claude/rules/design-system.image-draft.md`
- `.claude/rules/design-application.image-draft.md`

로 새로 저장(또는 이미 있으면 갱신)한다. 두 파일 상단에 다음을 명시:

```
> 출처: 이미지 (.claude/design-inputs/{입력폴더}/), 추출일 {오늘 날짜}
> 이 문서는 초안이다. rules/design-system.md·design-application.md(Figma 기반 SSoT)와
> 병합하려면 사람이 검토 후 직접 반영한다.
```

## STEP 4. 출력

- 만든/갱신한 두 초안 파일 경로
- 커버한 이미지 파일 목록
- `[확인 필요]` 항목 개수와 목록
- 기존 SSoT(`rules/design-system.md` 등)와 값이 다르거나 겹치는 항목이 있으면 표로 대조해서 알려준다 (자동 병합하지 않음)

## 금지 패턴

- 이미지에 없는 hex·px·클래스명을 채워 넣는 것
- 기존 `rules/design-system.md`·`design-application.md`를 이 커맨드가 직접 덮어쓰는 것 (항상 `.image-draft.md`로 분리)
- 노션 태스크 DB 조회·갱신 (이 커맨드는 파이프라인 밖, 노션 불필요)

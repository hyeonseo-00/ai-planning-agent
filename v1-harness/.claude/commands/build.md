---
description: Implementation Agent — Frontend Spec + 확정 Figma를 React 코드로 매핑
argument-hint: "{번호}"
---

`.claude/CLAUDE.md` → `.claude/agents/implementation-agent.md`를 읽고 그 사고 순서를 따른다.
**설계가 아니라 매핑이다** — Frontend Spec에 있는 Component Tree/State/Event/Validation을 코드로 1:1 옮긴다.

## 1. 선행 조건 (필수 3종)
DB `collection://<YOUR_NOTION_DB_ID>`에서 `$ARGUMENTS`(번호) 조회.
- `outputs/{번호}/3-1_common-screen-spec.md`(Layer 1) + `3-2b_frontend-spec.md`(Frontend Spec) + `Figma URL`(상위 또는 `{번호}-D`) 셋 다.
- `{번호}-D`의 `Figma URL` 비어 있으면 → **[사람 차례]** 안내, 중단.
- Layer 파일 없으면 → "`/go {번호}`로 `/screen`부터" 안내, 중단.

## 2. 절차 (agents/implementation-agent.md STEP 1~9)
1. 입력 검증 (Layer 1 / Frontend Spec / Figma 존재)
2. **Frontend Spec 분석** → 구현 범위 목록화 (Component Tree / State / Event / Validation / Data / API / 제약)
3. **Figma 분석** → 레이아웃·컴포넌트·타이포·색·간격·반응형 읽기, 토큰명 매핑
4. **★ Frontend Spec ↔ Figma 정합성 검증** — 표로 대조 (영역 수 / 반복 요소 개수 / 모달 유무 / variant 개수 / 인터랙션 / 예외 UI)
   - 🔴 BLOCKER 불일치 → **구현 중단.** 어느 쪽이 맞는지 보고 (Frontend Spec 수정=`/screen` 재실행 / Figma 기준=진행 지시). 자동이면 Orchestrator 멈춤 지점.
   - 🟡 TOLERABLE → 보고만, Figma 기준으로 구현
5. 프로젝트 구조·package.json·기존 공통 컴포넌트 확인 (없으면 스캐폴딩, 임의 라이브러리 금지)
6. **Frontend Spec → Code 매핑표** (Component Tree / State / Event / Validation / API) — **상태 컬럼 4단계**(✅ 구현 가능 / 🟡 mock 대체 / 🔴 구현 보류 / ⛔ 구현 불가). Screen Agent가 표기한 상태를 따르고 재판단 금지, Figma·프로젝트 확인으로 조정만. 자동이면 승인 없이 진행, 보고서에 기록
6-1. **⛔ 차단 항목이 하나라도 있으면 → 구현 중단.** 차단 항목·근거·결정 주체 보고. 자동이면 Orchestrator 멈춤 지점
7. 구현 — 매핑표대로. ✅ 그대로 / 🟡 mock 함수(주석 명시) / 🔴 placeholder·비활성. 토큰·클래스만. Layer 1 예외 6종·비활성 조건 전부. 죽은 클래스 회피
7-1. **정책 캡처 테스트 생성** — Layer 1 정책·예외 6종을 `{화면}.policy.test.ts`로. 정책 키워드 걸린 로직은 반드시. 재수정 후에도 통과해야 함
8. `outputs/{번호}/4_build-report.md` — 매핑 계획(상태 요약) / 정합성 결과(BLOCKER·TOLERABLE) / 수정·생성 파일 / 재사용 컴포넌트 / 토큰 / 미구현(🟡mock·🔴보류) / 확인 필요 / **정책 캡처 테스트 결과** / 영향 범위 / lint·build
9. 태스크 `산출물` 갱신, `➡️ 다음 실행` = `/qa {번호}`

## 출력
선행 조건 결과 · Frontend Spec/Figma 분석 요약 · **정합성 검증 표** · 매핑표(상태 4단계) · (⛔ 있으면 중단 사유) · 구현 결과 · "`/qa {번호}`" 안내

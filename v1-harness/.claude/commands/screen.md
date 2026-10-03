---
description: Screen Agent — 2계층 화면설계서 + 누락 리포트 + 디자인 태스크 생성
argument-hint: "{번호}"
---

`.claude/CLAUDE.md` → `.claude/agents/screen-agent.md`를 읽고 그 사고 순서(READ→REACT→ANALYZE→RESTRUCTURE→STRUCTURE→REFLECT)를 따른다.

1. DB `collection://<YOUR_NOTION_DB_ID>`에서 `$ARGUMENTS`(번호) 조회. `outputs/{번호}/1_functional-spec.md`를 입력으로. 없으면 "`/spec {번호}` 먼저" 안내하고 중단.
2. **READ/REACT** — 기능명세서 6섹션 정독. 컴포넌트가 design-application.md에 있나 확인. **정책 모순이면 정지.** **정책 키워드 스캔**(권한/금액/날짜/보관/삭제/제한/disabled 등) → 걸린 기능의 정책 항목이 비면 `[⚠️ 정책 필수]` 승격.
3. **ANALYZE** — Layer 1 항목별로 근거 유무 판정. 근거 없는 항목을 **4단계 분류**: ⛔ 차단 / ⚠️ 정책 필수 / 🔴 BLOCKER / 🟡 QUALITY.
4. **RESTRUCTURE/STRUCTURE** — 4개 파일 산출:
   - `outputs/{번호}/3-1_common-screen-spec.md` (Layer 1: 공통 화면 명세)
   - `outputs/{번호}/3-2a_design-spec.md` (Layer 2-A: Design Spec — UX 의도를 측정 가능하게)
   - `outputs/{번호}/3-2b_frontend-spec.md` (Layer 2-B: Frontend Spec — Component Tree/State/Event/Validation/Data/제약/**API 연결 정보 + 상태 4단계 미리 판정**)
   - `outputs/{번호}/3_missing-info.md` (누락 리포트 — ⛔차단 / 🔴BLOCKER / ⚠️정책 필수 / 🟡QUALITY 4섹션, "예상 답변 형태" 칸 필수)
   - 각 Layer 파일에 인라인 `[❓ 필요: {담당}]` / `[⚠️ 정책 필수]` 태그.
   - 색·간격·타이포는 design-system.md·design-application.md 토큰·클래스명. 하드코딩 금지.
5. **REFLECT** — 디자인 하위 태스크 `{번호}-D` 생성 (타입 디자인(사람), Figma URL 비움, 입력물=Design Spec + 누락 리포트). 상위에 Figma URL 이미 있으면 복사 + 완료 처리.
6. 상위 태스크 갱신: `산출물`에 4개 경로, `메모`에 "누락 총 N건 (⛔{c} 🔴{b} ⚠️정책{p} 🟡{q}), 상세 경로", `➡️ 다음 실행`:
   - ⛔ 차단 있으면 → `[사람] {번호} ⛔ 차단 {c}건 결정 → /screen {번호} 재실행`
   - 없으면 → `[사람] {번호}-D에 Figma 링크 기입 → /go {번호}` (Figma 있으면 `/go {번호}`)

## 출력
- 4개 산출물 경로 · 화면 수
- **⚠️ 기능명세서에 없어서 못 채운 정보 총 N건** — ⛔ 차단 항목명(있으면 /build 못 감) / 🔴 BLOCKER / ⚠️ 정책 필수 / 🟡 QUALITY 건수 + "상세는 3_missing-info.md"
- 생성한 디자인 태스크 번호 · 다음 액션(⛔ 있으면 "차단 결정" / 없으면 "사람 차례")

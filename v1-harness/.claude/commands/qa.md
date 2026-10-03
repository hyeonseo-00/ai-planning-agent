---
description: QA Agent — 5단계 품질 게이트로 검증하고 리포트를 만든다
argument-hint: "{번호}"
---

`.claude/CLAUDE.md` → `.claude/agents/qa-agent.md`를 읽고 그 사고 순서를 따른다.

1. DB `collection://<YOUR_NOTION_DB_ID>`에서 `$ARGUMENTS`(번호) 조회. 대상: `1_functional-spec.md`, `3-1_common-screen-spec.md`(Layer 1), `3-2b_frontend-spec.md`, `4_build-report.md`, React 코드, 정책 캡처 테스트.

## 품질 게이트 (순서 고정, FAIL 지점에서 멈춤)

- **G1 — 3단 매핑 + 역방향 대조**: 기능ID → Layer 1 → Frontend Spec → 실제 구현 파일. Build Report 매핑표를 근거로 삼되 **코드를 직접 열어 확인**. Frontend Spec의 State/Event/Validation 항목이 코드에 실재하는지 역으로도 훑음. X면 FAIL.
- **G2 — 예외 케이스**: 화면별 Layer 1 예외 6종(빈 입력값/중복 제출/권한 없음/데이터 없음/네트워크 오류/시스템 오류). 근거 없이 O 금지 (N/A는 사유 필수).
- **G3 — 디자인 토큰**: 하드코딩 hex·px grep (0건), 죽은 클래스 의존 (0건).
- **G4 — 구현 범위**: Layer 1/Frontend Spec 범위 이탈 / API 정의 여부 / 신규 라이브러리 / 공통 컴포넌트 중복 / ⛔ 차단 임의처리 여부. T1 위반은 무조건 FAIL. Build Report BLOCKER 해소 근거 / TOLERABLE 처리 확인.
- **G5 — lint·build·정책 테스트**: lint 통과 / build 통과 / **정책 캡처 테스트 통과** (재수정 후에도). 정책 키워드 걸린 로직에 테스트 없으면 FAIL.

G1~G5 통과 = PASS. 남는 리스크(🟡 mock, 🔴 placeholder, 잠정 QUALITY)는 리포트 "재검증 대상"에 명시하고 PASS 가능.

2. `outputs/{번호}/5_qa-report.md` — 게이트 통과 현황 표 + 각 게이트 상세 + 재검증 대상 + FAIL 항목(게이트별) + REFLECT.
3. 필드 갱신:
   - PASS → `현재 단계`=완료, `➡️ 다음 실행`=`완료`
   - FAIL → `➡️ 다음 실행`=`/build {번호} ({게이트} FAIL 항목 수정)`, `메모`에 FAIL 항목

## 출력
종합 결과 · 게이트 통과 현황(G1~G5) · 멈춘 게이트 · 재검증 대상 · (FAIL 시) 항목과 다음 액션

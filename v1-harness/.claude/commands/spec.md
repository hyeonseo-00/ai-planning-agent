---
description: Spec Agent — 소스를 6섹션 개발 기준 기능명세서로 만든다
argument-hint: "{번호}"
---

`.claude/CLAUDE.md` → `.claude/agents/spec-agent.md`를 읽고 그 사고 순서를 따른다.

1. DB `collection://<YOUR_NOTION_DB_ID>`에서 `$ARGUMENTS`(번호)를 조회. `입력물`에서 원본·대상 범위를 파싱.
2. STEP 0: 소스 모드 판정 (A 엑셀 / F Figma / I 아이디어). 구조 자동 감지. 근거 없으면 중단.
3. 기능ID 확정 (기존 시트 이어받기 or 신규 접두어).
4. 기능ID마다 **6섹션**(정상 흐름 / 예외 6항목 / 정책 / 데이터 요건 / 권한 / UI 요소) 채우기. 빈 항목은 `[확인 필요: ...]`.
5. 누락 N건 카운트. **N ≥ 6이면 사용자에게 질문**해 답을 받아 채운다 (모드 F의 처리로직·API 계열은 완화).
6. `outputs/{번호}/1_functional-spec.md` 저장. 태스크 `산출물`·`현재 단계`(진행 중)·`➡️ 다음 실행`(`/screen {번호}`)·`메모`(누락 N건) 갱신.

## 출력
소스 판정 요약 · 기능ID 개수 · 누락 N건 · (질문했으면) 반영 결과 · "`/go {번호}`" 안내

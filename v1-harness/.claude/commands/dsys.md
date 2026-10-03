---
description: Design System Agent — 디자인 규칙 Figma를 토큰·적용규칙 문서로 만든다 (파이프라인 밖, 사람이 직접 실행)
argument-hint: "{번호}"
---

`.claude/CLAUDE.md` → `.claude/agents/design-system-agent.md`를 읽고 그 사고 순서를 따른다.

1. DB `collection://<YOUR_NOTION_DB_ID>`에서 `$ARGUMENTS`(번호) 조회. `입력물`에서 **소스 node 목록**을 뽑는다 (여러 개 가능).
2. **처리 순서: 토큰 먼저 → 컴포넌트 나중.**
   - 토큰/Variables node (예: `21-10808`) → `get_metadata` + `get_variable_defs` → `rules/design-system.md`
   - 적용 규칙/컴포넌트 node (예: `48-5413`) → `get_metadata` (텍스트 레이어 파싱) → `rules/design-application.md`
3. 입력물에 "재추출"/"처음부터"가 있으면 전체 재작성 (기존 파일은 실행 전 사람이 `.bak` 백업). 없으면 대조 후 누락분만 보강.
4. design-system.md: Palette / Semantic(참조 관계까지) / Typography / Spacing / Border·Radius / Shadow / Layout. Figma에 있는 것만. 원본 오타 유지 + "알려진 이슈"에 기록.
5. design-application.md: 레이아웃(MainLayout) / 반응형 / 타이포 클래스(`text-*`) / 컴포넌트별 스펙(variant·상태·사이즈·정책) / 아이콘 / 알려진 이슈(죽은 클래스·미정의 토큰).
6. `[확인 필요]`는 표기만 (창작 금지).
7. 태스크 `산출물`(두 경로)·`현재 단계`(완료)·`➡️ 다음 실행`(`완료`)·`메모`(커버 node / 재작성·보강 / [확인 필요] 개수) 갱신.

## 출력
갱신 파일 · 커버한 node · 새/변경 토큰·컴포넌트 · `[확인 필요]` 목록

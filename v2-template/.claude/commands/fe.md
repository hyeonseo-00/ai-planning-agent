---
description: 명세 없이 하는 작은 프론트 작업 ("기본값" 템플릿)
argument-hint: {요청사항}
---

`.claude/rules/frontend.md`를 따른다.

1. `$ARGUMENTS`로 `templates/request.md`(목표 · 현재 요구사항/구현 내용)를 채워 보여 준다. 빈칸은 질문으로 모은다.
2. 요청과 관련된 파일과 공통 컴포넌트만 읽는다.
3. 구현 계획을 설명하고 확인받은 뒤 진행한다.
4. `templates/build-report.md` 형식으로 수정 파일 · 변경 내용 · 영향 범위를 보고한다.

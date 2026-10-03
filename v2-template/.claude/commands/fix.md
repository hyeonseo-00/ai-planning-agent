---
description: 이미 구현된 화면의 이슈를 고친다 ("이슈 해결" 템플릿)
argument-hint: {현재 문제 설명}
---

`.claude/rules/frontend.md`를 따른다.

1. `$ARGUMENTS`로 `templates/issue.md`(현재 문제 · 수정 목표 · 요구사항)를 채워 보여 준다. 빈칸은 질문으로 모은다.
2. 문제가 난 파일과 그 파일이 쓰는 공통 컴포넌트만 읽는다.
3. 원인과 수정 계획을 설명하고 확인받은 뒤 고친다.
4. `templates/build-report.md` 형식으로 수정 파일 · 변경 내용 · 영향 범위를 보고한다.

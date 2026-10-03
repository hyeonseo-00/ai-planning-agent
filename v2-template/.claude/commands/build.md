---
description: 화면설계서와 확정 Figma로 프론트 코드를 만든다 ("확실한 명령" 템플릿)
argument-hint: {화면ID} {Figma URL}
---

`.claude/rules/frontend.md`를 따른다. 진행 방식·구현 범위·디자인 기준은 화면설계서가 정한다.

1. 읽기: `outputs/{화면ID}/screen.md`, `.claude/rules/tokens.md`, Figma node의 `get_screenshot`·`get_metadata`.
2. Figma와 개발 명세의 컴포넌트 개수·흐름이 다르면 계획 설명 때 어느 쪽을 따를지 함께 묻는다.
3. 개발 명세를 코드로 1:1 옮긴다. API는 표의 상태를 따른다 (🟡 mock, 🔴 비활성, ⛔면 구현하지 않고 멈춘다).
4. `templates/build-report.md`를 `outputs/{화면ID}/build-report.md`에 채운다. 자체 점검에 ❌가 있으면 한 번 고치고, 그래도 ❌면 멈추고 보고한다.

---
description: 기능명세서로 화면설계서(공통 명세 + 개발 명세)를 만든다
argument-hint: {화면ID}
---

1. 읽기: `outputs/{화면ID}/spec.md`, `outputs/{화면ID}/missing.md`만.
2. `.claude/templates/screen.md`를 `outputs/{화면ID}/screen.md`에 채운다.
   - 정책·예외는 **공통 명세에만** 원본으로 적고, 개발 명세는 거기서 파생한다.
   - API 상태는 기능명세서에 근거가 있을 때만 ✅. 엔드포인트만 미정이면 🟡, 없어도 화면이 나오면 🔴, 없으면 화면이 성립하지 않으면 ⛔.
3. 새로 생긴 누락을 `missing.md`에 덧붙인다 (기존 행은 수정하지 않는다).
4. `⛔`가 있으면 그 항목만 질문하고 멈춘다. 없으면 "다음: Figma 확정 후 `/build {화면ID} {Figma URL}`"이라고만 출력한다.

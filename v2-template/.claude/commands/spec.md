---
description: 원본(엑셀·Figma·메모)으로 기능명세서를 만든다
argument-hint: {화면ID} {원본 경로 또는 메모}
---

1. 읽기: `$ARGUMENTS`의 원본만. Figma면 해당 node의 `get_metadata`만 조회한다.
2. `.claude/templates/spec.md`를 복사해 `outputs/{화면ID}/spec.md`에 채운다.
3. 비어 있는 칸은 `[확인 필요: 누가]`로 남기고, `.claude/templates/missing.md` 형식으로 `outputs/{화면ID}/missing.md`를 만든다.
4. `⛔ 지금 결정 필요`가 있으면 그 항목만 질문하고 멈춘다. 없으면 "다음: `/screen {화면ID}`"라고만 출력한다.

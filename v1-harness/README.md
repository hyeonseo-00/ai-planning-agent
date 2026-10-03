# v1 — 에이전트 7개 하네스 구조 (이전 버전, 비교용)

처음 설계한 버전입니다. `/task` 한 번과 Figma 링크 한 번이면 오케스트레이터가 기능명세서 → 화면설계서 → 코드 → QA를 자동으로 이어서 실행합니다.

- 에이전트 7개 (`.claude/agents/`), 커맨드 8개 (`.claude/commands/`), 공통 규칙 (`.claude/CLAUDE.md`)
- 멈춤 조건 8가지, 누락 4단계 등급, 규칙 위반 Tier, 정책 회귀 테스트, QA 실패 시 자동 재시도
- 흐름 예시: [`examples/walkthrough.md`](examples/walkthrough.md)

이 폴더를 프로젝트 루트로 열면 Claude Code가 `.claude/`를 인식합니다.

단계마다 에이전트가 같은 규칙을 반복해서 읽어 토큰 비용이 컸고, 혼자 쓰기에는 구조가 무거웠습니다. 그래서 [`v2-template/`](../v2-template/)(템플릿 구조)로 다시 설계했습니다. 비교는 [상위 README](../README.md)에 있습니다.

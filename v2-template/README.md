# v2 — 템플릿 구조 (현재 버전)

기능명세서 → 화면설계서 → 프론트 코드를 **템플릿 빈칸 채우기**로 만듭니다. 사람이 단계마다 직접 실행합니다.

프론트 작업 커맨드(`/build` · `/fix` · `/fe`)는 회사에서 쓰던 [상황별 요청서 프롬프트 3종](../origin/README.md)을 옮긴 것이고, 세 프롬프트에 반복되던 "주의" 항목은 공통 규칙 [`rules/frontend.md`](.claude/rules/frontend.md) 한 곳에 모았습니다.

이 폴더를 프로젝트 루트로 열면 Claude Code가 `.claude/`를 인식합니다.

## 사용법

```
/spec   {화면ID} {원본}        → outputs/{화면ID}/spec.md + missing.md
/screen {화면ID}               → outputs/{화면ID}/screen.md
  (사람이 Figma 확정)
/build  {화면ID} {Figma URL}   → 코드 + outputs/{화면ID}/build-report.md   ("확실한 명령" 템플릿)

/fix    {현재 문제}            → 이슈 수정 + 작업 보고                    ("이슈 해결" 템플릿)
/fe     {요청사항}             → 작은 작업 + 작업 보고                    ("요구사항 기본값" 템플릿)
```

프론트 작업은 모두 **구조·기존 컴포넌트 확인 → 계획 설명 후 확인받기 → 구현 → 수정 파일·변경 내용·영향 범위 보고** 순서로 진행합니다.

`⛔ 지금 결정 필요`가 하나라도 있으면 다음 단계로 가지 않고 그 항목만 묻습니다. 그 외 누락은 `🔴 나중에 결정`으로 적고 placeholder로 진행합니다.

👉 **[흐름 예시 보기](examples/walkthrough.md)** — 가상의 "클래스 예약 취소 화면". 메모에 "취소하면 환불"만 있을 때 AI가 환불 금액을 지어내지 않고 멈춰서 묻는 장면이 나옵니다.

## 구조

```
.claude/
├── CLAUDE.md            # 공통 규칙 1쪽 (원칙 5줄 + 멈춤 기준)
├── commands/            # /spec · /screen · /build · /fix · /fe — 읽을 파일과 채울 템플릿만 지정
├── templates/           # spec · screen · missing · build-report · issue · request — 빈칸 표
└── rules/
    ├── frontend.md      # 프론트 작업 공통 규칙 (작업 전 · 주의 · 작업 완료 후)
    └── tokens.md        # 디자인 토큰 이름표 (형식 예시)
examples/walkthrough.md  # 흐름 예시
```

## 토큰을 줄인 방법

| 방법 | 내용 |
|---|---|
| 항상 읽는 규칙 최소화 | `CLAUDE.md`를 원칙 5줄과 멈춤 기준만 남긴 1쪽으로 |
| 형식 설명 대신 빈칸 채우기 | 형식을 글로 설명하지 않고, 표 골격만 있는 템플릿을 채우게 함 |
| 지정된 입력만 읽기 | 커맨드마다 읽을 파일을 명시 (예: `/build`는 화면설계서와 토큰 이름표만) |
| 디자인 규칙 압축 | 긴 설명 문서 대신 쓸 수 있는 토큰 **이름 목록**만 (`rules/tokens.md`) |
| 산출물 통합 | 화면설계서 3개 파일 → 1개 파일 |
| 반복 지시문 통합 | 요청서 3종에 반복되던 "주의" 6줄을 `rules/frontend.md` 한 곳으로 |

v1과의 비교는 [상위 README](../README.md)에 있습니다.

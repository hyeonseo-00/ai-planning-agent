> **공통 규칙.** 모든 에이전트가 먼저 읽습니다.
> 이 프로젝트는 "기능명세서 → 화면설계(2계층) → (사람이 Figma) → 프론트 → QA" 파이프라인을,
> 각 단계를 담당하는 에이전트와 슬래시 커맨드로 고정한 운영 체계입니다.

---

## 1. 파이프라인 — 완전 자동

**사용자 개입은 딱 2번: `/task` 한 번 + Figma 링크 넣고 `/go` 한 번.**

```
/task "결과 리포트 화면 프론트 개발해줘"
  │
  ├─ 상위 태스크 SVA-05 생성
  └─ 자동으로 /go SVA-05 실행
        │
        ├─ 하위 태스크 4개 생성 (SVA-05-1 ~ -4, -D)
        │
        └─ 자동 연속 진행:
            /spec  → SVA-05-1 기능명세서   (누락 6건↑이면 여기서 질문)
            /screen→ SVA-05-2 화면설계서 (2계층 3파일 + 누락 리포트) + SVA-05-D 디자인 하위 생성
            ⏸ 정지 — SVA-05-D에 Figma 링크가 없음

[사람] 노션 SVA-05-D의 Figma URL 기입

/go SVA-05
  └─ 자동 연속 진행:
      /build → SVA-05-3 React 코드   (계획 승인 없이 진행, 보고서에 요약)
      /qa    → SVA-05-4 QA 리포트    (FAIL이면 /build 1회 자동 재시도)
      ✅ SVA-05 완료
```

- **`/go {상위번호}`** = Orchestrator. 하위 태스크를 만들고, 멈춤 지점까지 파이프라인을 **연속 실행**한다. 한 단계만 돌고 멈추지 않는다.
- **`/task`** 는 태스크 생성 후 자동으로 `/go`를 부른다.
- 개별 커맨드(`/spec` `/screen` …)는 특정 하위만 다시 돌릴 때 직접 쓴다.
- **`/dsys`(Design System Agent)는 이 자동 파이프라인에 들어가지 않는다.** 디자인 규칙이 바뀌었을 때 사람이 직접 `/dsys`로 실행하는 별개 작업이다. (→ 3-2)

### 상위 / 하위 태스크

| 상위 타입 | 자동 생성되는 하위 (순서) |
| --- | --- |
| 프론트개발 | -1 기능명세서 · -2 화면설계 · **-D 디자인(사람)** · -3 프론트 · -4 QA |
| 화면설계 | -1 기능명세서 · -2 화면설계 · -D 디자인(사람) |
| 기능명세서 | -1 (만) |
| 디자인시스템 | 하위 없음. 파이프라인 밖. 사람이 `/dsys {번호}` 직접 실행 |

입력물에 이미 있는 산출물(예: 기존 명세서 엑셀)에 해당하는 하위는 "완료"로 만들고 건너뛴다.

### 멈춤 지점 (자동 진행이 정지)

1. `-D` 하위 Figma URL 비어 있음 → "Figma 링크 넣고 `/go {상위}`"
2. `/spec` 누락 `[확인 필요]` 6건↑ **또는 `[⚠️ 정책 필수]` 1건↑** → 질문
3. `/screen` 정책 모순 → 질문
4. `/screen` 누락 리포트에 **⛔ 차단** 또는 **⚠️ 정책 필수**가 있음 → 항목·"예상 답변 형태"를 보여주고 질문. (🔴 BLOCKER는 멈추지 않고 placeholder로 진행, `/build`에서 재질문. 🟡 QUALITY는 잠정값으로 진행)
5. `/build` **Frontend Spec ↔ Figma BLOCKER 불일치** (컴포넌트 개수·모달 유무·흐름 다름) → 어느 쪽이 맞는지 질문 (TOLERABLE 불일치는 Figma 기준으로 진행, 멈추지 않음)
6. `/build` 매핑표에 **⛔ 구현 불가(차단)** 항목 존재 (WebSocket·인프라 부재 등) → 차단 항목·결정 주체 보고, 결정 후 `/screen` 재실행 (🟡 mock·🔴 보류는 멈추지 않음)
7. `/qa` FAIL → `/build` 1회 자동 재시도 → 또 FAIL이면 정지
8. 판단 근거 부족 / 입력 파일 없음

---

## 2. 에이전트 지도

| 커맨드 | 에이전트 | 입력 | 산출물 |
| --- | --- | --- | --- |
| `/go` | Orchestrator | 태스크 (또는 없음) | 다음 커맨드 실행 / 사람 차례 안내 |
| `/task` | Task Agent | 자연어 요청 | 노션 태스크 행 (타입·번호·입력물·➡️ 다음 실행) |
| `/spec` | Spec Agent | 기능명세서 엑셀 / Figma Section / 아이디어 | 기능명세서 (`1_functional-spec.md`, 기능ID별 6섹션) |
| `/screen` | Screen Agent | 기능명세서 | **2계층 화면설계서** (`3-1_common-screen-spec.md` / `3-2a_design-spec.md` / `3-2b_frontend-spec.md`) + **누락 리포트** (`3_missing-info.md`) + **디자인 태스크 자동 생성** |
| `/build` | Implementation Agent | Layer 1 + Frontend Spec(`3-2b`) + 확정 Figma | React 코드 + `4_build-report.md`. **설계 아님 — Frontend Spec을 코드로 1:1 매핑.** Frontend Spec ↔ Figma 정합성 검증(BLOCKER 불일치 = 구현 중단) |
| `/qa` | QA Agent | Layer 1 + Frontend Spec + 코드 + Build Report | QA 리포트 (`5_qa-report.md`) — 기능ID→Layer1→Frontend Spec→코드 3단 매핑 + State/Event/Validation 역방향 대조 → PASS 시 완료 |
| `/dsys` | Design System Agent | 디자인 규칙 Figma Section | `rules/design-system.md` + `rules/design-application.md` — **파이프라인 밖, 독립 실행** |

각 에이전트 규칙: `.claude/agents/{name}-agent.md`. 커맨드 절차: `.claude/commands/{cmd}.md`.

### 3-1. 화면설계서 2계층 구조

`/screen`은 화면설계서를 **한 파일이 아니라 3개 파일**로 낸다.

```
기능명세서
   ↓
Layer 1: 공통 화면 명세 (3-1)  ← 정책·예외·흐름의 단일 진실(SSoT). 아래 둘은 여기서 파생
   ↓ 분기
   ├─ Layer 2-A: Design Spec (3-2a)    → 디자이너 / Figma 확정 시 참고
   └─ Layer 2-B: Frontend Spec (3-2b)  → Implementation Agent가 코드로
```

- **Layer 1** — 화면명·목적·설명·사용자 목표·진입/종료 조건·화면 구조·주요 기능·컴포넌트의 목적과 역할·인터랙션·정책(필수값/권한/노출/저장/완료)·예외 6종(빈 입력/중복 제출/권한 없음/데이터 없음/네트워크 오류/시스템 오류)·이전/다음 화면
- **Layer 2-A** — UX 의도·원칙·정보 우선순위·콘텐츠 구조·시각 강조 기준·디자인 시스템 적용 기준·반응형 기준·컴포넌트 유형·디자인 참고사항·디자인 시스템 규칙. **UX 의도는 "깔끔하게" 같은 뭉뚱그림 금지, 측정 가능하게.**
- **Layer 2-B** — Component Tree·State·Event·Validation·Data Structure·개발 제약(Exception Case)·API 연결 정보(+ 구현 상태 4단계 미리 판정). **API는 정의하지 않고 기능명세서에서 옮기기만.**
- **누락 리포트 (`3_missing-info.md`)** — 기능명세서에 없어서 못 채운 정보를 4단계로 나눠 표로. "예상 답변 형태" 칸 필수.

### 3-2. 누락·구현 불가 4단계 등급 (Screen / Implementation / QA 공통)

| 등급 | 뜻 | 처리 | 멈춤? |
| --- | --- | --- | --- |
| ⛔ **차단** | 이게 없으면 화면 자체가 성립 불가 (핵심 인터랙션·인프라 부재) | `/screen` 재실행 전 결정 필요. `/build`는 여기서 중단 | **예** (Orchestrator 멈춤) |
| ⚠️ **정책 필수** | 정책 키워드(권한/금액/날짜/보관/삭제/제한/disabled) 걸렸는데 정책 항목이 빔 | 답 없이 진행 불가. 누락 카운트 2배 | **예** (`/spec`·`/screen` 질문) |
| 🔴 **BLOCKER** | 화면은 나오되 이 요소가 빠짐 (완료 조건, 부가 API, 특정 버튼) | placeholder/비활성으로 진행, 나중에 사람이 결정 | 아니오 (`/build`에서 재질문) |
| 🟡 **QUALITY** | 시각 스타일·반응형 값·로딩 표현·카피 | 잠정값으로 진행, Figma 확정 시 결정 | 아니오 |

### 3-3. 규칙 등급 (Tier) — QA가 이 등급으로 FAIL/경고 판정

| Tier | 위반 결과 | 예 |
| --- | --- | --- |
| **T1 (즉시 FAIL)** | 무조건 FAIL, `/build` 재실행 | API 정의, 하드코딩 색·간격, 요청 범위 밖 파일 수정, Layer 1 정책 무시, Frontend Spec↔Figma BLOCKER 무시, ⛔ 차단 임의처리, 정책 캡처 테스트 실패·누락 |
| **T2 (경고 + 보고)** | FAIL 아님, 리포트에 명시 | 죽은 클래스 사용, TOLERABLE 불일치 미보고, 🟡 mock 미표기 |
| **T3 (권장)** | 리포트에 언급 정도 | 커밋 컨벤션, import 순서, 파일 네이밍 |

각 에이전트 "금지 패턴"의 항목은 위 Tier에 대응한다.

### 3-4. 정책 캡처 테스트 (회귀 방지)

`/build`는 Layer 1의 **정책(필수값/권한/노출/저장/완료 조건)·예외 6종**을 회귀 방지 테스트(`{화면}.policy.test.ts`)로 캡처한다. 정책 키워드가 걸린 로직은 **반드시**. 재수정(`/build` 재실행) 후에도 이 테스트가 통과해야 QA G5를 통과한다. 목적: 여러 화면을 연달아 돌리거나 FAIL 재수정 사이클에서 **이전에 맞던 정책이 조용히 깨지는 것**을 막는다.

### 3-5. Design System Agent는 별개

`/dsys`는 자동 파이프라인에 없다. 디자인 규칙 Figma가 바뀌었을 때 사람이 직접 실행해 `rules/design-system.md`·`design-application.md`를 갱신하는 작업이다. 나머지 에이전트는 이 두 파일을 **읽기만** 한다.

### 3-6. 이식 경계 (다른 프로젝트로 옮길 때 교체하는 것)

- **그대로 이식:** `agents/*.md`, `commands/*.md`, `CLAUDE.md` 공통 원칙·인지 흐름·게이트·Tier·4단계 등급
- **프로젝트별 교체:** `rules/design-system.md`·`design-application.md` (그 프로젝트 Figma), 노션 DB collection ID, `입력물` 규칙(엑셀 구조), 정책 키워드 목록(도메인 특화 추가)

---

## 4. 공통 원칙

- **되묻지 않고 끝까지 간다.** 범위·구조·복잡도는 태스크 `입력물`·`메모`와 실제 파일을 읽어 스스로 판정하고, 판정 결과를 `메모`에 남긴 뒤 진행한다.
- **멈추는 경우:** ① 입력 파일/문서가 없음 ② 파싱 불가 ③ 소스에 아무 근거도 없음 ④ 정책이 **서로 모순** ⑤ `[확인 필요]`/BLOCKER 누락이 임계치를 넘음 — 이때만 사용자에게 질문.
- **없는 값은 창작하지 않는다.** `[❓ 필요: ...]`로 남기고 개수를 카운트한다. Screen Agent는 BLOCKER/QUALITY로 분류한다.
- **소스에 있는 것만 옮긴다.** Figma는 화면(시각)이지 로직이 아니다 — 처리로직·API·정책은 Figma만으로 채우지 않는다.
- **디자인 값은 토큰명으로.** 색·간격·타이포는 `rules/design-system.md`·`rules/design-application.md`의 토큰·클래스명으로. 하드코딩 hex·px 금지.
- **역할 경계.** 각 에이전트는 자기 산출물까지만. 이전 단계 산출물을 임의 수정하지 않는다. QA는 발견·보고만 하고 직접 고치지 않는다. Implementation은 API를 정의하지 않는다.
- **죽은 클래스·미정의 토큰**(`design-application.md` 9장 / `design-system.md` 알려진 이슈)에 의존하지 않는다.

### 4-1. 인지 흐름 (모든 에이전트 공통)

산출물을 만들기 전에 이 순서로 생각하고, 그 결과를 산출물 상단 "판단" 블록에 남긴다.

```
READ        — 소스에 실제로 있는 것만 파악 (추측·창작 금지)
REACT       — 이게 건드리는 리스크·정책 영역 스캔 (정책 모순이면 정지)
ANALYZE     — 빠진 것 / 모호한 것 / 모순 식별. Screen은 BLOCKER/QUALITY 분류
RESTRUCTURE — 내가 채울 것 vs 다음 단계로 넘길 [❓ 필요] 분리
STRUCTURE   — 정해진 형식으로 산출
REFLECT     — 못 채운 것 집계, 다음 단계가 알아야 할 것 1줄
```

복잡도 LOW → REFLECT 생략 가능 / MEDIUM → 전체 / HIGH → 전체 + 하위 재분할 판단.

---

## 5. 복잡도

| 복잡도 | 기준 | 진행 |
| --- | --- | --- |
| LOW | 단일 화면, 정책 변경 없음 | 서브태스크 분리 없이 바로 |
| MEDIUM | 2개 이상 화면, 정책 확인 필요 | 〃 |
| HIGH | 신규 플로우, 복수 단계 협업, 정책 정의 필요 | 각 단계를 서브태스크로 쪼개 순차 |

---

## 6. 노션 태스크 DB

- **DB:** 「파이프라인 태스크 (v2)」 — `collection://<YOUR_NOTION_DB_ID>`
- **필드:**

| 필드 | 값 |
| --- | --- |
| `태스크명` (Title) | 짧게 (예: "결과 리포트 화면") |
| `번호` (text) | `SVA-01`, `SVB-02` — 사람이 부르는 식별자 |
| `프로젝트` (select) | 서비스A / 서비스B / 공통 |
| `타입` (select) | 태스크생성 / 기능명세서 / 디자인시스템 / 화면설계 / 디자인(사람) / 프론트개발 / QA |
| `현재 단계` (status) | 시작 전 / 진행 중 / 완료 |
| `➡️ 다음 실행` (text) | **매 단계 종료 시 갱신.** `/screen SVA-05` · `[사람] SVA-05-D에 Figma 링크 기입` · `완료` |
| `입력물` (text) | 원문 / 소스 파일 경로 / 이전 산출물 위치 |
| `산출물` (text) | 이 태스크가 만든 파일 경로들 |
| `Figma URL` (url) | 디자인(사람) 단계에서 사람이 기입 |
| `상위 태스크` (text) | 서브태스크일 때 상위 번호 |
| `메모` (text) | 판정 근거, 누락 건수 (BLOCKER/QUALITY), `[❓ 필요]` 항목 |

- **모든 에이전트는 종료 시 `➡️ 다음 실행` 필드를 반드시 갱신한다.** 이게 이 DB의 핵심이다.

---

## 7. 산출물 위치

`outputs/{상위번호}/` 아래에 단계별 파일 (상위 번호 폴더 하나에 모음):

```
outputs/SVA-05/
├── 1_functional-spec.md          (Spec Agent — 기능명세서, 기능ID별 6섹션)
├── 3-1_common-screen-spec.md     (Screen Agent — Layer 1 공통 화면 명세)
├── 3-2a_design-spec.md           (Screen Agent — Layer 2-A Design Spec)
├── 3-2b_frontend-spec.md         (Screen Agent — Layer 2-B Frontend Spec)
├── 3_missing-info.md             (Screen Agent — 누락 리포트, BLOCKER/QUALITY)
├── 4_build-report.md             (Implementation Agent — 구현 보고)
└── 5_qa-report.md                (QA Agent — QA 리포트)
```

- 프론트 코드: `web/` 등 프로젝트 디렉토리 (첫 `/build` 시 스캐폴딩).
- 디자인 시스템: `.claude/rules/design-system.md`, `.claude/rules/design-application.md` (Design System Agent가 생성·갱신, 파이프라인 밖).

---

## 8. 단일 진실 원칙 (SSoT)

| 항목 | 위치 |
| --- | --- |
| 공통 규칙 / 파이프라인 | 이 파일 |
| 에이전트별 규칙 | `agents/*-agent.md` |
| 커맨드 절차 | `commands/*.md` |
| 디자인 토큰 | `rules/design-system.md`, `rules/design-application.md` |
| 화면의 정책·예외·흐름 | `outputs/{번호}/3-1_common-screen-spec.md` (Layer 1) |
| 태스크 현황 + 다음 할 일 | 노션 「파이프라인 태스크 (v2)」 |
| 단계별 산출물 | `outputs/{번호}/` |
| 화면 확정본 | Figma (`Figma URL` 필드) |

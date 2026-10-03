# 흐름 예시 — 가상의 "클래스 예약 취소 화면"

> **형식 예시입니다.** 실제 산출물은 기업 대외비라서, 가상의 클래스 예약 서비스를 두고 템플릿(`.claude/templates/`)에 맞춰 **직접 작성한 축약본**입니다. 실제 실행 결과가 아닙니다.

---

## 1. `/spec RSV-01 "수업 시작 24시간 전까지 취소 가능, 취소하면 환불"`

`templates/spec.md`의 빈칸을 채워 `outputs/RSV-01/spec.md`를 만듭니다.

| 항목 | 내용 |
| --- | --- |
| 정상 흐름 | 1. 예약 상세에서 [예약 취소] → 2. 확인 모달 → 3. [취소하기] → 4. 상태 "취소됨" |
| 예외 - 유효성 오류 | 수업 시작 24시간 이내면 취소 불가 안내 |
| 예외 - 네트워크·서버 오류 | [확인 필요: PM] |
| 예외 - 중복 제출 | [확인 필요: PM] |
| 정책 | 24시간 전까지 취소 가능. 환불 **[확인 필요: PM — 금액·수단·기간]** |
| 권한 | 예약한 본인 |

`outputs/RSV-01/missing.md`

| 구분 | 항목 | 이유 | 누가 |
| --- | --- | --- | --- |
| ⛔ 지금 결정 필요 | 환불 금액·수단·기간 | 정책 키워드 "금액"인데 비어 있음 | PM |
| 🔴 나중에 결정 | 네트워크 오류 처리, 중복 클릭 처리 | 화면은 나오지만 해당 동작 미정 | PM |

> **⛔가 있어서 멈춥니다.** AI가 "전액 환불"처럼 그럴듯한 값을 지어내지 않고 묻습니다.
> 사람: "24시간 전 취소는 전액 환불, 원 결제수단으로 3영업일 내" → 반영 후 다음 단계로.

## 2. `/screen RSV-01`

`spec.md`와 `missing.md`**만** 읽고 `templates/screen.md`를 채웁니다. 공통 명세와 개발 명세가 한 파일입니다.

```
### 1. 공통 명세
목적: 취소 결과(환불 금액·시점)를 알고 나서 확정하게 한다
정책: 24시간 전까지 / 전액 환불, 원 결제수단, 3영업일 내
완료 조건: 상태 "취소됨" 반영 + 완료 토스트
UX 의도: 환불 금액과 입금 예정일이 스크롤 없이 보인다

### 2. 개발 명세
CancelConfirmModal > RefundSummary, ButtonGroup(Close, ConfirmCancel)
상태: idle → submitting → success | error
이벤트: ConfirmCancel 클릭 → submitting 동안 버튼 disabled
API: 예약 취소 — 🟡 mock (엔드포인트 미정, 백엔드)
```

> `⛔` 없음 → "다음: Figma 확정 후 `/build RSV-01 {Figma URL}`"

## 3. `/build RSV-01 {Figma URL}`

`rules/frontend.md`(작업 규칙)를 따라 `screen.md`, `rules/tokens.md`, Figma 화면만 읽습니다.

**먼저 계획을 보여 주고 확인받습니다.**
```
변경할 파일: src/pages/reservation/CancelConfirmModal.tsx (신규)
재사용할 컴포넌트: Modal, Button(Danger·Secondary), Toast — 기존 공통 컴포넌트
질문: 없음 (Figma와 개발 명세의 컴포넌트 구성 일치)
```

확인 후 구현하고 `templates/build-report.md`를 채웁니다.

| 항목 | 내용 |
| --- | --- |
| 수정 파일 | `CancelConfirmModal.tsx` (신규), `ReservationDetail.tsx` (취소 버튼 연결) |
| 영향 범위 | 직접: 예약 상세 / 간접: 없음 (공통 컴포넌트 수정 없음) / API·서버: 변경 없음 / 라이브러리 추가: 없음 |
| 자체 점검 | 범위 밖 수정 없음 ✅ · 공통 컴포넌트 재사용 ✅ · 토큰 이름만 사용 ✅ · 정책·예외·완료 조건 반영 ✅ · API는 mock ✅ |
| 남은 일 | 🟡 예약 취소 API 엔드포인트 확정 시 연결 |

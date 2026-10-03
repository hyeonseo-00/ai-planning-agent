# 디자인 토큰 이름표

`/build`가 쓸 수 있는 토큰·클래스 **이름만** 둔다. 설명 문서가 아니라 이름 목록이라 짧게 유지한다.
실제 프로젝트의 디자인 시스템은 기업 대외비라 공개하지 않았다. 아래는 형식 예시다.

| 분류 | 쓸 수 있는 이름 |
| --- | --- |
| 색 | `brand/default` `brand/hover` `fg/primary` `fg/secondary` `surface/base` `stroke/default` `status/error` |
| 간격 | `space/4` `space/8` `space/12` `space/16` `space/24` |
| 타이포 | `text-title-sb-16` `text-body-r-14` `text-caption-r-12` |
| 컴포넌트 | `Button(Primary·Secondary·Danger / sm·md)` `Modal` `Toast` `Chips` `StatusTag` |
| 쓰면 안 되는 것 | {죽은 클래스·미정의 토큰} |

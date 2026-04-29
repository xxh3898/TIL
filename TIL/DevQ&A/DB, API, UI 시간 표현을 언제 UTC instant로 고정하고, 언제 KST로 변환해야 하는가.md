---
note_type: dev_qa
status: seed
created: 2026-04-28
updated: 2026-04-28
question: "DB, API, UI 시간 표현을 언제 UTC instant로 고정하고, 언제 KST로 변환해야 하는가"
domain: backend
related_topics:
  - utc
  - timezone
  - serialization
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-27 - TIL.md"
  - "AI/Inbox/cubing-hub/20260427/02-time-contract/review-time-contract.md"
tags:
  - backend
  - time
  - timezone
  - devqa
area: "[[Areas/Career]]"
project:
---

# DB, API, UI 시간 표현을 언제 UTC instant로 고정하고, 언제 KST로 변환해야 하는가

## Why

- 시간 문제는 포맷보다 의미 계약이 먼저다.
- 저장과 응답, 화면 표시를 한 번에 섞으면 서버 timezone, 브라우저 timezone, legacy 데이터 보정 범위가 같이 흔들린다.
- 면접에서도 "KST로 보여 줬다"보다 "어디까지를 UTC 의미로 고정했는가"를 설명할 수 있어야 실무 감각이 드러난다.

## Core Concept

- 저장과 API 응답은 가능한 한 UTC `Instant`로 고정하는 편이 안전하다. 그래야 서버가 어느 timezone에서 돌아가든 같은 시점을 같은 값으로 다룰 수 있다.
- 현재 시각 생성과 DB 직렬화도 같은 기준을 써야 한다. 예를 들어 `Clock` 주입과 `hibernate.jdbc.time_zone=UTC`를 함께 맞춰야 생성 시점과 저장 시점이 어긋나지 않는다.
- 사람에게 보이는 화면은 마지막 단계에서만 `Asia/Seoul` 같은 서비스 기준 timezone으로 변환하는 편이 좋다. 표시 형식과 저장 의미를 분리해야 디버깅 범위가 커지지 않는다.
- 기존 `LocalDateTime` 성격의 데이터 보정은 별도 migration 문제다. 새 코드 계약을 맞췄다고 해서 과거 데이터를 곧바로 `-9시간` 보정하면 정상 행까지 망가질 수 있다.

## Interview Answer Version

- 30~40초 답변: 저는 저장과 API 응답은 UTC `Instant`로 고정하고, 화면에서만 KST로 변환하는 편이 가장 안전하다고 봅니다. 이렇게 해야 서버 timezone이나 브라우저 환경에 흔들리지 않고 같은 시점을 일관되게 다룰 수 있습니다. 대신 현재 시각 생성과 DB 직렬화도 같은 기준이어야 해서 `Clock` 주입이나 JDBC timezone 설정까지 같이 맞춥니다. 과거 `LocalDateTime` 데이터 보정은 별도 migration으로 떼어, 기존 값의 실제 의미를 확인한 뒤 좁은 범위로 처리해야 합니다.

## Practical Tip

- 실제 적용 사례: 백엔드 감사 필드와 도메인 시간 필드를 `Instant`로 바꾸고, API는 `Z`가 붙은 UTC 문자열을 내려주고, 프런트는 공통 `dateTime` 유틸에서만 KST와 한국어 표현으로 변환한다.
- 잘못 쓰면 생기는 문제: 서버는 UTC로 시간을 만들고 프런트는 timezone 없는 문자열을 그대로 읽으면, 사용자가 실제로 `15:38`에 작업했어도 화면에는 `06:38`처럼 어긋난 시간이 보일 수 있다.
- 프로젝트 연결: 시간 계약을 정리할 때는 코드 변경, 화면 포맷, legacy backfill을 한 작업으로 뭉개지 말고 `의미 계약 -> 표시 계층 -> 과거 데이터 판단` 순서로 분리하는 것이 안전하다.

## Related

- [2026-04-27 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-27%20-%20TIL.md)
- [2026-04-27 - Daily Log](../Retrospectives/2026/04%EC%9B%94/2026-04-27%20-%20Daily%20Log.md)
- [테스트 전략과 테스트 코드 작성 기준](./%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%A0%84%EB%9E%B5%EA%B3%BC%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1%20%EA%B8%B0%EC%A4%80.md)

## 예상 꼬리 질문

- 왜 API도 KST 문자열로 바로 내려주지 않나요?
  - 답변: 화면 표기 기준과 저장 의미를 섞으면 다른 timezone 소비자나 테스트 환경에서 다시 흔들릴 수 있습니다. API는 시점 자체를 표현하고, 표시는 소비 계층에서 처리하는 편이 더 단단합니다.
- `LocalDateTime`을 계속 써도 안 되나요?
  - 답변: 단일 서버, 단일 timezone에서는 버틸 수 있지만, 운영 환경과 직렬화 경계가 늘어나면 같은 값이 다른 의미로 읽힐 위험이 커집니다. 그래서 절대 시점은 `Instant`가 더 안전합니다.
- 과거 데이터는 언제 보정해야 하나요?
  - 답변: 새 계약 반영과 분리해서, 샘플 행과 배포 시각으로 기존 값의 실제 의미를 확인한 뒤 좁은 범위로 해야 합니다. 코드를 바꿨다고 일괄 보정부터 하면 위험합니다.

## Internal Links

- [[Areas/Career]]
- [[Archive/Projects/Cubing Hub/Docs/API Specification]]

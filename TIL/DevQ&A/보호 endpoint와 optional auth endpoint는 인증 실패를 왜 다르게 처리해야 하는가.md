---
note_type: dev_qa
status: seed
created: 2026-04-21
updated: 2026-04-21
question: "보호 endpoint와 optional auth endpoint는 인증 실패를 왜 다르게 처리해야 하는가"
domain: spring
related_topics:
  - auth
  - authorization
  - error-handling
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-17 - TIL.md"
tags:
  - auth
  - api
  - devqa
area: "[[Areas/Career]]"
project:
---

# 보호 endpoint와 optional auth endpoint는 인증 실패를 왜 다르게 처리해야 하는가

## Why

- 같은 인증 실패라도 endpoint 계약이 다르면 응답 전략도 달라져야 한다.
- 이 기준이 없으면 보호 API는 너무 느슨해지고, guest 허용 API는 불필요하게 깨진다.
- 면접에서는 인증 기술 자체보다 "어떤 계약에서 어떻게 fail-closed 또는 fallback 할 것인가"를 설명할 수 있는지가 더 실무적이다.

## Core Concept

- 먼저 봐야 할 것은 예외 종류보다 endpoint 계약이다. 보호 endpoint는 인증된 사용자 컨텍스트가 전제이므로 사용자 조회 실패나 인증 복원 실패를 `401 Unauthorized`로 끊는 편이 맞다.
- 반대로 optional auth endpoint는 guest도 정상 사용자다. 이 경우 인증 정보가 있으면 추가 정보를 붙이고, 복원에 실패하면 제한적으로 guest 응답으로 내려갈 수 있다.
- 중요한 점은 optional auth가 "모든 예외를 삼킨다"는 뜻이 아니라는 것이다. 인증 컨텍스트 복원 단계의 실패만 fallback 대상으로 보고, 실제 도메인 오류나 시스템 오류는 그대로 드러내야 한다.
- 프런트도 같은 계약을 따라야 한다. 보호 화면은 `401`을 세션 정리나 재로그인 흐름으로 연결하고, optional auth 화면은 guest 렌더링을 우선하되 실제 오류만 에러 UI로 보여 주는 편이 일관된다.

## Interview Answer Version

- 30~40초 답변: 저는 인증 실패를 예외 원인보다 endpoint 계약 기준으로 나눠서 처리하는 편입니다. 보호 endpoint는 인증된 사용자 컨텍스트가 전제이기 때문에 인증 복원에 실패하면 `401`로 fail-closed 해야 합니다. 반면 optional auth endpoint는 guest도 정상 흐름이므로, 인증 정보가 있으면 확장 응답을 주고 복원에 실패하면 제한적으로 guest fallback을 허용할 수 있습니다. 다만 이 fallback은 인증 컨텍스트 복원 단계에만 적용하고, 도메인 오류나 시스템 오류까지 삼키면 안 된다고 생각합니다.

## Practical Tip

- 실제 적용 사례: 커뮤니티 작성, 댓글, 마이페이지 같은 보호 API는 사용자 조회 실패를 `401`로 통일하고, 홈 화면처럼 비로그인 접근이 가능한 API만 guest fallback을 둔다.
- 잘못 쓰면 생기는 문제: 모든 인증 실패를 같은 응답으로 묶으면 보호 API가 너무 느슨해지거나, 반대로 홈 같은 공개 API가 불필요하게 깨진다.
- 프로젝트 연결: `Spring Security`와 전역 예외 처리 구조를 쓰더라도, 최종 응답 전략은 endpoint 계약을 먼저 닫아 두어야 프런트 상태 처리와 테스트 분기도 안정된다.

## Related

- [2026-04-17 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-17%20-%20TIL.md)
- [Spring Security 인증 인가 동작 구조](./Spring%20Security%20인증%20인가%20동작%20구조.md)
- [예외 처리와 API 에러 응답 설계](./%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20API%20%EC%97%90%EB%9F%AC%20%EC%9D%91%EB%8B%B5%20%EC%84%A4%EA%B3%84.md)

## 예상 꼬리 질문

- optional auth endpoint에서는 어떤 예외까지만 fallback 해야 하나요?
  - 답변: 인증 컨텍스트 복원 단계의 실패까지만 제한적으로 흡수하는 편이 맞습니다. 비즈니스 규칙 위반이나 시스템 오류까지 fallback으로 숨기면 실제 장애가 가려집니다.
- 보호 endpoint에서도 refresh 재시도 같은 복구 흐름을 둘 수 있나요?
  - 답변: 둘 수는 있지만 최종 계약은 여전히 fail-closed여야 합니다. 즉 재시도 후에도 인증이 복구되지 않으면 `401`로 끝내야 합니다.
- guest fallback이 과도하면 어떤 문제가 생기나요?
  - 답변: 인증이 필요한 정보가 실수로 guest 응답으로 내려가거나, 실제 인증 장애가 UI에서 정상처럼 보일 수 있습니다. 그래서 fallback 범위를 endpoint별로 명시해야 합니다.

## Internal Links

- [[Areas/Career]]


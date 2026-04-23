---
note_type: dev_qa
status: seed
created: 2026-04-23
updated: 2026-04-23
question: "malformed refresh_token가 login 자체를 막을 때 복구 endpoint는 왜 cookie path 밖에 있어야 하는가"
domain: spring
related_topics:
  - auth
  - cookie
  - recovery
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-22 - TIL.md"
tags:
  - auth
  - cookie
  - devqa
area: "[[Areas/Career]]"
project:
---

# malformed refresh_token가 login 자체를 막을 때 복구 endpoint는 왜 cookie path 밖에 있어야 하는가

## Why

- refresh token 장애는 인증 실패 일반론보다 더 구체적인 브라우저 계약 문제다.
- bad cookie를 지우는 API의 위치를 잘못 잡으면 복구 요청 자체가 같은 cookie에 다시 막힐 수 있다.
- 면접에서도 "쿠키를 쓴다"보다 "오염된 쿠키를 어떻게 안전하게 복구할 것인가"를 설명할 수 있으면 실무 감각이 드러난다.

## Core Concept

- 브라우저는 cookie의 `Path` 규칙에 맞는 요청마다 해당 cookie를 계속 붙인다. 그래서 malformed `refresh_token`가 `/api/auth` 범위에 살아 있으면, 같은 경로 아래의 login·refresh·logout·clear 요청에도 반복적으로 실릴 수 있다.
- 이때 복구 endpoint가 같은 `cookie path` 아래 있으면 "문제 cookie를 지우기 위한 요청"도 같은 bad cookie에 다시 오염된다. 결과적으로 복구 API가 존재해도 실제 복구는 실패할 수 있다.
- 그래서 cookie clear endpoint는 원인 path 밖의 public 경로에 두는 편이 안전하다. 그래야 브라우저가 문제 cookie를 보내지 않는 경로로 진입해 최소한의 복구 요청을 통과시킬 수 있다.
- client recovery도 제한적으로 설계해야 한다. guest 첫 방문처럼 정상적인 `missing cookie`까지 매번 cleanup 대상으로 넣으면 노이즈가 커지므로, malformed·`401`·network-error처럼 실제 cookie 오염 가능성이 큰 경우만 best-effort cleanup을 시도하는 편이 맞다.

## Interview Answer Version

- 30~40초 답변: malformed `refresh_token`가 login 자체를 막는 상황에서는 clear endpoint를 같은 `cookie path` 아래 두면 복구 요청도 다시 같은 bad cookie에 오염될 수 있습니다. 그래서 저는 clear endpoint를 원인 path 밖의 public 경로로 분리하는 편입니다. 이렇게 해야 브라우저가 문제 cookie를 붙이지 않는 요청으로 복구를 먼저 통과시킬 수 있습니다. 또 client에서는 모든 실패를 복구하지 않고 malformed, `401`, network-error처럼 실제 cookie 오염 가능성이 큰 경우만 best-effort cleanup을 하도록 좁히는 것이 안전하다고 봅니다.

## Practical Tip

- 실제 적용 사례: `POST /api/session/clear-refresh-cookie`처럼 `/api/auth` 밖의 public endpoint를 두고, bootstrap/login 실패 중 malformed·`401`·network-error 계열일 때만 clear를 시도한다.
- 잘못 쓰면 생기는 문제: clear endpoint를 `/api/auth` 안에 두면 같은 bad cookie가 다시 실려 복구 요청이 막히고, 사용자는 login 화면에서도 계속 `400`이나 CORS 없는 실패만 보게 된다.
- 프로젝트 연결: refresh token cookie를 쓰는 SPA에서는 인증 성공 흐름보다 실패 복구 경로를 먼저 닫아야 실제 사용자 장애를 줄일 수 있다.

## Related

- [2026-04-22 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-22%20-%20TIL.md)
- [session로그인방식과 token로그인 방식에 대해](./session로그인방식과%20token로그인%20방식에%20대해.md)
- [예외 처리와 API 에러 응답 설계](./%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20API%20%EC%97%90%EB%9F%AC%20%EC%9D%91%EB%8B%B5%20%EC%84%A4%EA%B3%84.md)

## 예상 꼬리 질문

- 복구 endpoint를 public으로 열면 보안상 문제는 없나요?
  - 답변: cookie 제거 자체는 세션을 얻는 동작이 아니라 오염된 상태를 정리하는 동작이기 때문에, 서버가 민감 정보를 반환하지 않고 대상 cookie만 만료시키면 공개 경로로 두는 편이 더 안전할 수 있습니다.
- 왜 모든 login 실패에서 무조건 clear를 시도하지 않나요?
  - 답변: 정상적인 `missing cookie`나 단순 validation 실패까지 전부 clear 대상으로 넣으면 불필요한 호출이 늘고 원인 분리가 흐려집니다. 실제 cookie 오염 가능성이 큰 경우만 좁혀야 합니다.
- 근본 해결은 endpoint 분리만으로 충분한가요?
  - 답변: 아닙니다. 장기적으로는 `refresh_token`의 `Path`, `Domain`, 재발급 정책까지 같이 봐야 합니다. 다만 즉시 장애를 끊는 최소 변경으로는 clear endpoint를 path 밖으로 분리하는 것이 효과적입니다.

## Internal Links

- [[Areas/Career]]

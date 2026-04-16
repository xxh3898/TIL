---
note_type: dev_qa
status: seed
created: 2026-01-13
updated: 2026-01-13
question: "Spring MVC 요청 처리 흐름"
domain: spring
related_topics: []
interview_priority: high
source_ref:
tags: []
area: "[[Areas/Career]]"
project:
---
## Topic
Spring MVC 요청 처리 흐름

### 1. Why
- **실무**: 요청이 들어왔을 때 `DispatcherServlet`부터 `Controller`, `Service`, `Repository`, DTO 변환, 응답 직렬화까지 어떤 흐름으로 처리되는지 알아야 디버깅과 계층 책임 분리가 쉬워진다.
- **구조적 의미**: Spring MVC는 Front Controller 패턴과 MVC 패턴을 결합해 웹 요청 처리 책임을 표준화한다. 그래서 요청 흐름을 이해하는 것은 프레임워크 사용법이 아니라 구조 이해에 가깝다.
- **면접 의도**: 면접에서는 "요청이 들어오면 내부에서 정확히 무엇이 실행되는가", "각 계층이 무엇을 해야 하는가", "왜 MVC 패턴을 쓰는가"를 설명할 수 있는지 본다.

### 2. Core Concept
- **개념 정의**: `DispatcherServlet`은 Spring MVC의 Front Controller로 모든 웹 요청의 진입점이다. `Controller`는 요청과 응답 경계를 다루고, `Service`는 비즈니스 로직을 수행하고, `Repository`는 DB 접근을 담당한다. DTO는 계층 간 데이터 전달과 API 계약 표현에 사용된다.
- **동작 방식**: 클라이언트 요청이 오면 `DispatcherServlet`이 이를 받고 `HandlerMapping`으로 대상 `Controller`를 찾는다. 이후 `HandlerAdapter`가 `Controller` 메서드를 실행하고, `Controller`는 `Request DTO`를 받아 `Service`로 넘긴다. `Service`는 필요한 도메인 로직을 처리하며 `Repository`를 호출하고, 최종 결과를 `Response DTO` 또는 모델로 반환한다. `@ResponseBody`가 있으면 `HttpMessageConverter`가 JSON 등으로 직렬화해 응답한다.
- **장점 / 단점**: MVC 패턴은 요청 처리, 비즈니스 로직, 데이터 접근을 분리해 테스트와 유지보수를 쉽게 만든다. 반면 계층이 많아 보여 초기에는 번거롭게 느껴질 수 있고, 책임을 잘못 나누면 형식적인 계층만 남을 수 있다.
- **비교 포인트**: `Controller`는 HTTP 세부사항을 다루고, `Service`는 유스케이스와 트랜잭션을 다루며, `Repository`는 영속성 처리에 집중한다. DTO는 API 계약과 내부 모델을 분리하는 도구이고, Entity는 영속성 모델이라는 점에서 역할이 다르다.
- **사용 조건 / 주의점**: `Controller`에 비즈니스 로직을 넣지 않고, `Service`가 HTTP 객체에 의존하지 않도록 분리해야 한다. DTO 검증은 요청 경계에서 하고, 응답도 필요한 필드만 담아 전달하는 것이 안전하다.

### 3. Interview Answer Version
- 30~40초 답변: Spring MVC에서 요청이 들어오면 먼저 `DispatcherServlet`이 받습니다. 그 다음 `HandlerMapping`과 `HandlerAdapter`를 통해 실행할 `Controller`를 찾고 메서드를 호출합니다. `Controller`는 요청 DTO를 받아 검증하고 `Service`에 유스케이스를 위임하고, `Service`는 비즈니스 로직을 수행하면서 필요하면 `Repository`로 DB를 조회하거나 저장합니다. 마지막으로 결과를 응답 DTO로 만들어 반환하면 `HttpMessageConverter`가 JSON으로 직렬화합니다. 이렇게 MVC 패턴을 쓰는 이유는 웹 처리, 비즈니스 로직, 데이터 접근 책임을 분리해 유지보수성과 테스트성을 높이기 위해서입니다.

### 4. Practical Tip
- 실제 적용 사례: `POST /records` 요청이 들어오면 `RecordController`가 `CreateRecordRequest`를 받고 검증한 뒤 `RecordService`에 넘기고, `RecordService`가 `RecordRepository`로 저장한 후 `CreateRecordResponse`를 반환하는 흐름으로 설계하면 책임이 명확하다.
- 잘못 쓰면 생기는 문제: `Controller`에서 바로 `Repository`를 호출하거나, `Service`에서 `HttpServletRequest`를 직접 다루기 시작하면 계층 분리가 무너지고 테스트가 어려워진다.
- 프로젝트 연결: Spring Boot API 프로젝트에서는 `DispatcherServlet`이 기본 진입점을 담당하고, `@RestController` + DTO + Service 계층 분리를 유지하는 것이 가장 흔하고 안정적인 구조다.

### 5. 예상 꼬리 질문
1. `DispatcherServlet`과 `Filter`는 요청 흐름에서 어떤 차이가 있나요?
- 답변: `Filter`는 Servlet 스펙 레벨에서 `DispatcherServlet`보다 앞단에서 동작하며, Spring MVC와 무관하게 모든 요청을 가로챌 수 있습니다. 반면 `DispatcherServlet`은 Spring MVC 내부 진입점으로, 컨트롤러 매핑과 실행을 담당하는 Front Controller입니다.
2. `@ResponseBody`와 `HttpMessageConverter`는 어떤 관계인가요?
- 답변: `@ResponseBody`는 반환값을 뷰가 아니라 HTTP 본문으로 쓰겠다는 선언입니다. 실제 직렬화는 `HttpMessageConverter`가 담당해서 객체를 JSON이나 문자열로 변환해 응답으로 내려줍니다.
3. `Controller`에서 validation을 하고 `Service`에서도 검증을 하는 이유는 무엇인가요?
- 답변: `Controller` 검증은 형식과 필수값 같은 입력 경계 검증이고, `Service` 검증은 비즈니스 규칙 검증입니다. 둘은 역할이 달라서 하나만으로는 충분하지 않은 경우가 많습니다.

## Related

- [Filter, Interceptor, AOP를 언제 어떻게 구분해서 쓰는가](./Filter,%20Interceptor,%20AOP를%20언제%20어떻게%20구분해서%20쓰는가.md)
- [예외 처리와 API 에러 응답 설계](./예외%20처리와%20API%20에러%20응답%20설계.md)

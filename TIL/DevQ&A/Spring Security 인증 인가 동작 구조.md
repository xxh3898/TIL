---
note_type: dev_qa
status: seed
created: 2026-04-08
updated: 2026-04-08
question: "Spring Security 인증 인가 동작 구조"
domain: spring
related_topics: []
interview_priority: high
source_ref:
tags: []
area: "[[Areas/Career]]"
project:
---
## Topic
Spring Security 인증 인가 동작 구조

### 1. Why
- **실무**: 로그인, 권한 체크, 비밀번호 암호화, JWT 검증은 보안의 핵심이고 실수 비용이 크다. Spring Security의 기본 흐름을 알아야 설정을 안전하게 바꾸고 장애를 추적할 수 있다.
- **구조적 의미**: Spring Security는 인증과 인가를 애플리케이션 전역 규칙으로 관리하기 위해 `Security Filter Chain` 기반 구조를 제공한다. 즉 보안 로직을 컨트롤러마다 흩어 놓지 않고 중앙에서 통제하는 구조다.
- **면접 의도**: 면접에서는 인증과 인가를 구분하는지, `UserDetailsService`, `PasswordEncoder`, 필터 체인이 어떤 역할을 하는지, JWT를 어디에 연결하는지 설명할 수 있는지를 본다.

### 2. Core Concept
- **개념 정의**: 인증은 사용자가 누구인지 확인하는 과정이고, 인가는 인증된 사용자가 어떤 자원에 접근할 수 있는지 판단하는 과정이다. Spring Security는 이를 `Security Filter Chain`으로 처리한다.
- **동작 방식**: 요청이 들어오면 먼저 `Security Filter Chain`이 실행된다. 로그인 요청이라면 `AuthenticationManager`가 인증을 시도하고, 이 과정에서 `UserDetailsService`가 사용자 정보를 조회하고 `PasswordEncoder`가 저장된 암호화 비밀번호와 입력값을 비교한다. 인증에 성공하면 `Authentication` 객체가 `SecurityContext`에 저장되고, 이후 요청에서는 권한 처리 흐름에 따라 URL 권한 규칙이나 메서드 보안 규칙이 적용된다.
- **장점 / 단점**: 장점은 인증과 인가를 일관되게 중앙 관리할 수 있고, 필터 기반이라 웹 요청 앞단에서 제어가 가능하다는 점이다. 단점은 초기 설정이 복잡하고 필터 순서, 인증 객체 구조, 예외 흐름을 이해하지 못하면 디버깅 난이도가 높다는 점이다.
- **비교 포인트**: `UserDetailsService`는 사용자 조회 책임, `PasswordEncoder`는 비밀번호 암호화/매칭 책임을 가진다. 세션 기반 인증은 서버가 인증 상태를 저장하고, JWT 기반 인증은 토큰을 기준으로 stateless하게 처리한다는 차이가 있다. 하지만 두 방식 모두 권한 처리 자체는 인증 이후 `Authentication` 정보를 기준으로 동작한다.
- **사용 조건 / 주의점**: 비밀번호는 절대 평문 저장하면 안 되고 `PasswordEncoder`로 해시해야 한다. JWT를 쓴다면 보통 커스텀 필터에서 토큰을 파싱하고 검증한 뒤 `SecurityContext`에 인증 정보를 넣는다. 단, JWT는 stateless라 편하지만 강제 로그아웃, 토큰 폐기, 재발급 전략을 별도로 설계해야 한다.

### 3. Interview Answer Version
- 30~40초 답변: Spring Security에서 인증은 사용자를 확인하는 과정이고, 인가는 인증된 사용자가 어떤 자원에 접근 가능한지 판단하는 과정입니다. 이 흐름은 보통 `Security Filter Chain`에서 시작되고, 로그인 시에는 `UserDetailsService`가 사용자 정보를 조회하고 `PasswordEncoder`가 비밀번호를 검증합니다. 인증이 성공하면 `Authentication`이 `SecurityContext`에 저장되고, 이후 URL 권한 규칙이나 메서드 보안으로 인가가 처리됩니다. JWT를 사용할 때도 결국 필터에서 토큰을 검증하고 인증 객체를 만들어 `SecurityContext`에 넣는 방식으로 연결됩니다.

### 4. Practical Tip
- 실제 적용 사례: 로그인 API에서는 아이디와 비밀번호를 검증해 JWT를 발급하고, 이후 요청에서는 커스텀 JWT 필터가 토큰을 검사해 사용자 인증 정보를 `SecurityContext`에 넣는 구조가 가장 흔하다.
- 잘못 쓰면 생기는 문제: `PasswordEncoder` 없이 비밀번호를 직접 비교하거나, JWT 검증을 컨트롤러마다 수동으로 처리하면 보안 취약점과 중복 코드가 동시에 생긴다.
- 프로젝트 연결: Spring Boot + JWT 프로젝트에서는 `SecurityFilterChain` 설정, 사용자 조회 서비스, 비밀번호 해시 정책, 인증 실패/인가 실패 응답 전략을 함께 맞춰야 전체 보안 흐름이 안정적으로 동작한다.

### 5. 예상 꼬리 질문
1. `UserDetailsService`와 `UserDetails`는 각각 어떤 역할을 하나요?
- 답변: `UserDetailsService`는 사용자 정보를 어디서 어떻게 조회할지 담당하는 서비스이고, `UserDetails`는 조회된 사용자 인증 정보를 Spring Security가 다룰 수 있는 형식으로 담은 객체입니다. 즉 하나는 조회 책임, 다른 하나는 인증 정보 표현 책임입니다.
2. JWT 기반 인증에서 로그아웃이나 토큰 폐기는 어떻게 처리하나요?
- 답변: JWT는 기본적으로 stateless라 발급된 액세스 토큰을 즉시 무효화하기가 어렵습니다. 그래서 보통 짧은 만료 시간, refresh token 관리, 블랙리스트 저장, 토큰 버전 관리 같은 전략을 함께 씁니다.
3. URL 권한 제어와 `@PreAuthorize` 같은 메서드 보안은 언제 구분해서 쓰나요?
- 답변: URL 권한 제어는 `/admin/**`처럼 큰 범위의 접근 정책을 빠르게 통제할 때 유용합니다. 반면 메서드 보안은 같은 URL이라도 소유자 여부나 파라미터 기반 권한처럼 더 세밀한 비즈니스 조건이 필요할 때 적합합니다.

## Related

- [session로그인방식과 token로그인 방식에 대해](./session로그인방식과%20token로그인%20방식에%20대해.md)
- [쿠키(Cookie) vs 세션(Session): 서버가 나를 기억하는 법](./쿠키와%20세션에%20대해.md)

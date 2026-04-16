---
note_type: dev_qa
status: seed
created: 2026-01-17
updated: 2026-01-17
question: "Bean 생명주기와 Spring 컨테이너 관리 방식"
domain: spring
related_topics: []
interview_priority: high
source_ref:
tags: []
area: "[[Areas/Career]]"
project:
---
## Topic
Bean 생명주기와 Spring 컨테이너 관리 방식

### 1. Why
- **실무**: Spring을 쓴다는 것은 객체를 직접 `new`로 관리하는 대신 컨테이너에 맡긴다는 뜻이다. Bean 생명주기와 scope를 이해해야 메모리, 상태 공유, 초기화 코드, 의존성 주입 문제를 제대로 다룰 수 있다.
- **구조적 의미**: Spring 컨테이너는 객체 생성, 초기화, 의존성 연결, 소멸 시점을 중앙에서 관리해 애플리케이션 구조를 일관되게 유지한다.
- **면접 의도**: 면접에서는 Bean이 단순히 "등록된 객체" 수준이 아니라, 생성부터 소멸까지 어떤 과정을 거치고 `singleton` 기본 전략과 `scope`, `DI`가 어떻게 연결되는지 이해하는지 본다.

### 2. Core Concept
- **개념 정의**: Bean은 Spring 컨테이너가 생성하고 관리하는 객체다. 생명주기는 보통 생성, 의존성 주입, 초기화, 사용, 소멸 단계로 나뉜다.
- **동작 방식**: 컨테이너가 Bean을 생성한 뒤 의존성을 주입하고, `@PostConstruct` 같은 초기화 콜백을 실행한 후 런타임 동안 재사용한다. 종료 시점에는 `@PreDestroy` 또는 destroy 메서드가 호출된다. 이 과정에서 `DI`는 Bean 간 연결을 컨테이너가 대신 해 주는 메커니즘이다.
- **장점 / 단점**: 기본 `singleton` 전략은 객체를 하나만 만들어 재사용하므로 메모리 효율과 관리 일관성이 좋다. 반면 상태를 잘못 두면 공유 객체가 되어 동시성 문제가 생길 수 있다. 다양한 `scope`는 유연성을 주지만 생명주기 관리가 더 복잡해진다.
- **비교 포인트**: `singleton`은 컨테이너당 하나, `prototype`은 요청할 때마다 새 객체, `request`와 `session`은 웹 요청/세션 범위에서 유지된다. 직접 객체를 만드는 방식은 단순하지만 테스트, 교체, 초기화 관리 측면에서 컨테이너 관리보다 불리하다.
- **사용 조건 / 주의점**: 기본 Bean은 `singleton`으로 동작하므로 내부에 mutable 상태를 두지 않는 것이 안전하다. `prototype` Bean은 생성 이후 소멸까지 컨테이너가 완전히 관리하지 않으므로 사용 목적이 분명해야 한다. 웹 전용 `scope`는 웹 환경에서만 의미가 있다.

### 3. Interview Answer Version
- 30~40초 답변: Spring Bean은 컨테이너가 생성하고 관리하는 객체입니다. 컨테이너는 Bean을 생성한 뒤 의존성을 주입하고, 초기화 콜백을 실행한 후 런타임 동안 재사용하다가 종료 시 소멸 콜백까지 관리합니다. 기본 scope는 `singleton`이라 컨테이너당 하나의 인스턴스를 재사용하고, 필요하면 `prototype`, `request`, `session` 같은 scope를 선택할 수 있습니다. DI는 이 Bean들 사이의 의존성을 컨테이너가 연결해 주는 방식이라, 객체 생성과 조립 책임을 애플리케이션 코드에서 분리하는 핵심 개념입니다.

### 4. Practical Tip
- 실제 적용 사례: DB 연결 설정, 서비스 객체, 리포지토리 객체처럼 상태를 가지지 않는 컴포넌트는 기본 `singleton` Bean으로 두는 것이 가장 일반적이다.
- 잘못 쓰면 생기는 문제: `singleton` Bean에 사용자별 상태나 요청별 값을 필드로 저장하면 동시성 버그가 생기고, `prototype`을 남용하면 객체 생성 비용과 관리 복잡도가 늘어난다.
- 프로젝트 연결: Spring Boot에서는 `@Component`, `@Service`, `@Repository`, `@Configuration`으로 등록된 객체들이 Bean으로 관리되며, 생성자 주입을 통해 DI를 명확하게 표현하는 것이 유지보수에 가장 유리하다.

### 5. 예상 꼬리 질문
1. 생성자 주입을 필드 주입보다 더 권장하는 이유는 무엇인가요?
- 답변: 생성자 주입은 필요한 의존성이 무엇인지 객체 생성 시점에 명확히 드러나고, `final`로 불변성을 유지하기 좋습니다. 또 테스트에서 직접 객체를 생성하기 쉬워지고 순환 참조도 더 빨리 드러납니다.
2. `prototype` Bean을 `singleton` Bean이 주입받으면 어떤 문제가 생기나요?
- 답변: 기본 주입 방식으로는 `singleton` 생성 시점에 `prototype`도 한 번만 만들어져 사실상 같은 객체를 계속 쓰게 됩니다. 요청할 때마다 새 인스턴스가 필요하면 `ObjectProvider` 같은 지연 조회 방식이 필요합니다.
3. `@PostConstruct`와 생성자의 역할 차이는 무엇인가요?
- 답변: 생성자는 객체를 만들고 필수 의존성을 받는 단계이고, `@PostConstruct`는 의존성 주입이 끝난 뒤 초기화 로직을 수행하는 단계입니다. 따라서 다른 Bean이 모두 연결된 상태가 필요하면 `@PostConstruct`가 더 적합합니다.

## Related

- [Spring Framework 핵심 개념 완벽 정리 (IoC, DI, AOP, PSA)](./Spring%20프레임워크에%20대해.md)
- [스프링 부트란 무엇이며, 왜 사용하는가? (Spring vs Spring Boot)](./스프링%20부트란%20무엇이며,%20왜%20사용하는가.md)
- [ApplicationContext: 스프링 컨테이너의 완성체](./ApplicationContext란.md)

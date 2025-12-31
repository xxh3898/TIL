# Spring Framework 핵심 개념 완벽 정리 (IoC, DI, AOP, PSA)

자바 개발자라면 피할 수 없는, 아니 반드시 정복해야 하는 산이 바로 **Spring Framework**입니다. 단순히 "많이 쓰니까" 사용하는 것을 넘어, 스프링이 왜 탄생했고 어떤 철학을 가지고 있는지 이해하는 것은 코드의 퀄리티를 높이는 첫걸음입니다.

오늘은 스프링 프레임워크를 관통하는 핵심 개념 3가지(IoC/DI, AOP, PSA)와 POJO에 대해 정리해보겠습니다.

---

## 1. Spring Framework란?

스프링은 **"자바 엔터프라이즈 개발을 편하게 해주는 오픈소스 경량급 애플리케이션 프레임워크"** 입니다.
과거 EJB(Enterprise Java Beans) 시절의 복잡하고 무거운 개발 방식에서 벗어나, **POJO(Plain Old Java Object)** 기반의 가볍고 유연한 개발을 지향합니다.

### 스프링의 핵심 철학: POJO (Plain Old Java Object)
스프링은 특정 기술이나 라이브러리에 종속되지 않는 순수한 자바 객체, 즉 **POJO**를 지향합니다.
우리가 작성하는 비즈니스 로직이 특정 프레임워크(심지어 스프링 자체)의 클래스를 상속받거나 인터페이스를 구현해야 한다면, 그 코드는 테스트하기 어렵고 객체지향적이지 않게 됩니다. 스프링은 이러한 침투적인 방식을 최소화합니다.

---

## 2. 스프링의 3대 핵심 요소 (The Spring Triangle)

스프링은 POJO 프로그래밍을 가능하게 하기 위해 다음 3가지 핵심 기술을 제공합니다.
![삼각형](https://blog.kakaocdn.net/dna/bVbak1/btrSaIz9bP9/AAAAAAAAAAAAAAAAAAAAAJgw8WhW7awiAm7kBe8FrB2wtx2CYBFOryiA27eFkq5-/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1767193199&allow_ip=&allow_referer=&signature=dCdgJIsW5aNkmuat3Cq%2B8IkcgBE%3D)
### ① IoC (Inversion of Control) / DI (Dependency Injection)

**"제어의 역전"** 과 **"의존성 주입"** 입니다. 가장 중요한 개념입니다.

* **기존 방식 (개발자가 제어):**
    개발자가 필요한 객체를 직접 `new` 연산자로 생성하고 의존성을 맺어줍니다.
    ```java
    public class MemberService {
        // 개발자가 직접 Repository를 생성함 (강한 결합)
        private MemberRepository repository = new JdbcMemberRepository();
    }
    ```

* **스프링 방식 (컨테이너가 제어):**
    객체의 생성과 생명주기 관리를 개발자가 아닌 **스프링 컨테이너(IoC Container)** 가 대신합니다. 컨테이너는 필요한 의존성을 런타임에 주입(**DI**)해줍니다.
    ```java
    @Service
    public class MemberService {
        private final MemberRepository repository;

        // 생성자를 통해 외부(컨테이너)에서 주입받음 (느슨한 결합)
        @Autowired
        public MemberService(MemberRepository repository) {
            this.repository = repository;
        }
    }
    ```

**장점:**
* 객체 간의 결합도(Coupling)가 낮아집니다.
* 유연한 코드 변경이 가능합니다. (구현체만 갈아 끼우면 됨)
* 테스트가 용이해집니다. (Mock 객체 주입 가능)

### ② AOP (Aspect Oriented Programming)

**"관점 지향 프로그래밍"**입니다. 핵심 비즈니스 로직과 부가 기능(공통 관심사)을 분리하는 것입니다.

애플리케이션 전반에 걸쳐 공통적으로 사용되는 기능들(로깅, 보안, 트랜잭션 등)을 **횡단 관심사(Cross-cutting Concerns)** 라고 합니다. 이를 비즈니스 로직마다 일일이 코딩하면 중복이 발생하고 유지보수가 힘들어집니다.

* **핵심 관심사:** 비즈니스 로직 (예: 회원 가입, 주문 생성)
* **횡단 관심사:** 로깅, 보안, 트랜잭션 등

스프링 AOP는 이 횡단 관심사를 따로 떼어내어 모듈화하고, 실행 시점에 비즈니스 로직 앞/뒤에 자동으로 끼워 넣습니다.

**대표적인 예시: `@Transactional`**
우리는 DB 트랜잭션을 위해 `connection.setAutoCommit(false)` 같은 코드를 직접 쓰지 않습니다. 그저 `@Transactional` 어노테이션만 붙이면 스프링이 알아서 트랜잭션 시작과 커밋/롤백을 처리합니다. 이것이 AOP의 위력입니다.

### ③ PSA (Portable Service Abstraction)

**"일관된 서비스 추상화"** 입니다.
환경이나 구현 기술이 변경되더라도, 일관된 방식으로 기술을 사용할 수 있게 해주는 인터페이스를 말합니다.

**예시:**
* **트랜잭션 추상화:** JDBC를 쓰든, JPA를 쓰든, MyBatis를 쓰든 `PlatformTransactionManager`라는 동일한 인터페이스로 트랜잭션을 제어합니다.
* **캐시 추상화:** EhCache, Redis 등 어떤 구현체를 쓰더라도 `@Cacheable`이라는 동일한 어노테이션을 사용합니다.
* **웹 MVC:** 서블릿 기반의 웹 기술이 로우 레벨에서 어떻게 동작하는지 몰라도 `@Controller`, `@GetMapping` 등을 통해 편리하게 웹 개발을 할 수 있습니다.

**장점:**
* 특정 기술에 종속되지 않습니다.
* 코드를 거의 수정하지 않고 기술 스택을 교체할 수 있습니다.

---

## 3. 요약 (Summary)

| 개념 | 설명 | 핵심 키워드 |
| :--- | :--- | :--- |
| **POJO** | 특정 기술에 종속되지 않는 순수한 자바 객체 | 순수성, 객체지향 |
| **IoC/DI** | 객체의 생성과 의존성 관리를 컨테이너에게 위임 | 제어의 역전, 느슨한 결합 |
| **AOP** | 핵심 로직과 공통 기능을 분리하여 관리 | 횡단 관심사 분리, 중복 제거 |
| **PSA** | 기술이 바뀌어도 일관된 사용 방식 제공 | 추상화, 기술 독립성 |

---

## 마치며

스프링 프레임워크는 단순히 기능을 구현하는 도구가 아니라, **"좋은 객체지향 설계(SOLID)"** 를 할 수 있도록 도와주는 프레임워크입니다. 스프링을 공부할 때는 단순히 사용법을 익히는 것을 넘어, 스프링이 왜 이런 구조를 가지게 되었는지 그 '의도'를 파악하는 것이 중요합니다.
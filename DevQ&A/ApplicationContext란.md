# ApplicationContext: 스프링 컨테이너의 완성체

## 1. 개요
### 1.1 정의
**ApplicationContext**는 스프링 프레임워크에서 **빈(Bean)의 생성, 관계 설정, 관리, 제거** 등 생명주기 전반을 관리하는 **IoC 컨테이너(Inversion of Control Container)**입니다.

### 1.2 역할
단순히 객체를 생성해 주는 것을 넘어, 애플리케이션 개발에 필요한 **다양한 부가 기능(국제화, 이벤트 발행, 환경 변수 관리 등)**을 제공하는 일종의 **"종합 관리 시스템"**입니다.

---

## 2. 계층 구조와 비교
`ApplicationContext`는 최상위 컨테이너인 `BeanFactory`를 상속받아 확장한 인터페이스입니다.

![ApplicationContext 계층 구조](https://i.ibb.co/KxTkZpqZ/Application-Context.png)

### BeanFactory vs ApplicationContext

| 구분 | BeanFactory | ApplicationContext |
| :--- | :--- | :--- |
| **성격** | 빈을 관리하는 **최소한의 기능**만 제공 | BeanFactory + **엔터프라이즈 부가 기능** |
| **로딩 시점** | **Lazy Loading** (지연 로딩) | **Eager Loading** (즉시 로딩) |
| **동작** | `getBean()` 호출 시점에 객체를 생성함 | 서버 시작 시점에 모든 싱글톤 빈을 미리 생성함 |
| **사용처** | 메모리가 극도로 부족한 모바일/임베디드 (거의 안 씀) | **대부분의 스프링 웹 애플리케이션** |

> **핵심:** 실무에서는 99.9% `ApplicationContext`를 사용합니다. 서버가 뜰 때 미리 빈을 다 만들어 놔야, 사용자가 요청했을 때 기다리지 않고 바로 응답할 수 있기 때문입니다.

---

## 3. 주요 기능
`BeanFactory`에는 없는 `ApplicationContext`만의 강력한 기능들입니다.

1.  **국제화 (MessageSource):** 한국어, 영어 등 언어에 따라 다른 메시지를 출력할 수 있게 해줍니다.
2.  **환경 변수 관리 (EnvironmentCapable):** 로컬(Local), 개발(Dev), 운영(Prod) 등 배포 환경을 구분하고 설정을 다르게 가져갈 수 있습니다 (`@Profile`).
3.  **이벤트 발행 (ApplicationEventPublisher):** 이벤트를 던지고(`publish`), 받는(`listen`) 구조를 만들어 컴포넌트 간의 결합도를 낮춥니다.
4.  **리소스 로딩 (ResourceLoader):** 파일, 클래스패스, URL 등 다양한 리소스를 편하게 읽어올 수 있습니다.

---

## 4. 구현체 종류
시대의 흐름에 따라 사용하는 구현체가 달라졌습니다.

1.  **ClassPathXmlApplicationContext:** (과거) `xml` 파일을 읽어서 설정. 레거시 시스템에서 볼 수 있음.
2.  **AnnotationConfigApplicationContext:** (현재 표준) 자바 클래스(`@Configuration`)와 어노테이션(`@Bean`)을 읽어서 설정. **Spring Boot**가 기본으로 사용하는 방식입니다.
3.  **WebApplicationContext:** 웹 환경에 특화된 컨테이너입니다.

---

## 5. 예시 코드
요즘 가장 많이 쓰는 **Java Config** 방식의 예제입니다.

### 5.1 설정 클래스 (AppConfig.java)
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration // "이것은 설정 파일입니다"라고 알려줌
public class AppConfig {

    @Bean // "이 메서드가 반환하는 객체를 빈으로 관리해줘"
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```

### 5.2 실행 코드 (Main.java)
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class CoreApplication {
    public static void main(String[] args) {
        // 1. 스프링 컨테이너 생성 (AppConfig 정보를 보고 빈을 다 생성함)
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

        // 2. 빈 조회 (DL - Dependency Lookup)
        // "컨테이너야, memberService라는 이름의 빈 좀 줘"
        MemberService memberService = ac.getBean("memberService", MemberService.class);

        // 3. 사용
        memberService.join(new Member(1L, "memberA", Grade.VIP));
    }
}
```

---

## 6. 결론
**"스프링 컨테이너 = ApplicationContext"** 라고 생각하셔도 무방합니다.

개발자가 `new` 키워드로 객체를 직접 생성하고 연결하던 귀찮은 작업을, `ApplicationContext`가 대신해줌으로써(제어의 역전), 우리는 **비즈니스 로직에만 집중**할 수 있게 됩니다.

또한, **싱글톤(Singleton)** 패턴을 몰라도 모든 객체를 알아서 싱글톤으로 관리해 주는 고마운 존재입니다.
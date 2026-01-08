# 빌더 패턴(Builder Pattern): 객체 생성의 혁명

## 1. 개요 (Overview)
### 1.1 정의
**빌더 패턴(Builder Pattern)**은 복잡한 객체의 생성 과정과 표현 방법을 분리하여, 다양한 구성의 인스턴스를 동일한 생성 절차로 만들 수 있게 해주는 **생성(Creational) 패턴**입니다.

### 1.2 핵심 아이디어
> **"생성자 파라미터가 너무 많아서 헷갈린다면, 하나씩 지정해서 조립하게 하자."**

마치 **서브웨이 샌드위치**를 주문하는 것과 같습니다.
1. 빵 선택 -> 2. 치즈 추가 -> 3. 야채 선택 -> 4. 소스 선택 -> **완성(`build()`)**

---

## 2. 등장 배경: 생성자의 한계 (Why?)

빌더 패턴이 없는 세상에서, 필드가 많은 객체를 생성하려면 다음과 같은 문제에 직면합니다.

### 2.1 점층적 생성자 패턴의 문제 (Telescoping Constructor Problem)
필드가 늘어날 때마다 생성자를 계속 만들어야 합니다.
```java
public class Member {
    // 생성자가 너무 많아짐...
    public Member(String name) { ... }
    public Member(String name, int age) { ... }
    public Member(String name, int age, String email) { ... }
    public Member(String name, int age, String email, String address) { ... }
}
```

### 2.2 가독성과 실수 가능성
```java
// 무엇이 이름이고, 무엇이 이메일이고, 무엇이 주소인지 한눈에 알 수 없음
new Member("홍길동", 20, "Seoul", "010-1234-5678", "user@test.com");
```
만약 인자의 순서가 바뀌면(`address`와 `email`이 둘 다 String이라 바뀐 줄 모름), 런타임 버그가 발생합니다.

### 2.3 자바 빈즈(Setter) 패턴의 문제
기본 생성자로 객체를 만들고 `set` 메서드로 값을 채우면 가독성은 좋아지지만, **객체의 불변성(Immutability)**이 깨집니다. (누군가 중간에 값을 바꿔버릴 수 있음)

---

## 3. 빌더 패턴 구조 및 구현 (Implementation)

### 3.1 구조 다이어그램
빌더는 내부에 `static` 클래스를 두어, 원본 객체의 생성을 대행합니다.



### 3.2 Pure Java 구현 (정석)
Lombok 없이 직접 구현하면 이런 형태입니다.

```java
public class Member {
    private final String name;
    private final int age;
    private final String email;

    // 1. private 생성자 (외부에서 직접 생성 금지)
    private Member(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
        this.email = builder.email;
    }

    // 2. static 내부 클래스 (Builder)
    public static class Builder {
        private final String name; // 필수 인자
        private int age;           // 선택 인자
        private String email;      // 선택 인자

        // 필수 인자는 빌더 생성자로 받음
        public Builder(String name) {
            this.name = name;
        }

        public Builder age(int age) {
            this.age = age;
            return this; // 메서드 체이닝을 위해 자기 자신 반환
        }

        public Builder email(String email) {
            this.email = email;
            return this;
        }

        // 3. 최종 객체 생성 메서드
        public Member build() {
            return new Member(this);
        }
    }
}
```

### 3.3 사용 예시 (Client Code)
```java
Member member = new Member.Builder("홍길동") // 필수값
    .age(30)          // 선택값 (메서드 체이닝)
    .email("test@gmail.com")
    .build();         // 최종 생성
```
**장점:** 어떤 필드에 어떤 값을 넣는지 명확하며(`age(30)`), 순서가 중요하지 않습니다.

---

## 4. 실무 활용: Lombok @Builder (Best Practice)
실무에서는 위처럼 긴 코드를 직접 짜지 않습니다. **Lombok** 라이브러리가 어노테이션 하나로 해결해 줍니다.

```java
import lombok.Builder;
import lombok.Getter;

@Getter
@Builder // 이거 하나면 끝!
public class Member {
    private String name;
    private int age;
    private String email;
}
```

```java
// 사용법 (자동 생성된 builder() 메서드 사용)
Member member = Member.builder()
    .name("김철수")
    .age(25)
    .email("kim@test.com")
    .build();
```

---

## 5. 장점과 단점 (Pros & Cons)

### 장점 (Pros)
1.  **가독성:** 코드를 읽기 쉽고, 인자의 의미가 명확합니다.
2.  **유연성:** 필요한 데이터만 설정하여 객체를 만들 수 있습니다. (생성자 오버로딩 불필요)
3.  **불변성(Immutability):** `setter` 없이 객체를 생성하므로, 한 번 만들어진 객체의 상태가 변하지 않음을 보장할 수 있습니다.

### 단점 (Cons)
1.  **코드량 증가:** Lombok 없이는 코드가 매우 길어집니다.
2.  **객체 생성 비용:** 객체를 만들기 위해 `Builder` 객체를 먼저 만들어야 하므로 성능에 아주 미세한 영향이 있을 수 있습니다. (현대 하드웨어에서는 무시할 수준)

---

## 6. 결론 (Conclusion)
빌더 패턴은 **"필드가 많은 객체"**나 **"변경 불가능한(Immutable) 객체"**를 만들 때 필수적입니다.

특히 개발 중인 프로젝트의 DTO(Data Transfer Object)나 Entity 클래스에서 생성자 파라미터가 4~5개를 넘어간다면, 주저 없이 **`@Builder`**를 적용하세요. 코드의 퀄리티가 달라집니다.
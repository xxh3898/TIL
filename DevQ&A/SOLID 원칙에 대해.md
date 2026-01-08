# SOLID 원칙: 객체지향 설계의 5가지 절대 법칙

## 1. 개요 (Overview)
### 1.1 정의
**SOLID**는 로버트 마틴(Robert C. Martin)이 정립한 **좋은 객체지향 설계를 위한 5가지 원칙**의 앞 글자를 딴 용어입니다. 시간이 지나도 **유지보수하기 쉽고, 유연하며, 확장하기 쉬운 소프트웨어**를 만드는 것이 목표입니다.

![SOLID 원칙 5가지](https://i.ibb.co/yFFq0L9r/SOLID-5.png)

---

## 2. 상세 내용 (The 5 Principles)

### 2.1 S: 단일 책임 원칙 (SRP - Single Responsibility Principle)
> **"한 클래스는 하나의 책임만 가져야 한다."**

- **의미:** 클래스를 변경해야 하는 이유는 단 하나뿐이어야 합니다.
- **Bad Case:** `User` 클래스가 '로그인', '이메일 전송', 'DB 저장'을 모두 함.
- **Good Case:** `User` (데이터), `AuthManager` (로그인), `EmailSender` (전송), `UserRepository` (DB)로 분리.
- **이점:** 기능 변경 시 파급 효과가 적어짐.

### 2.2 O: 개방-폐쇄 원칙 (OCP - Open/Closed Principle)
> **"확장에는 열려 있고, 변경에는 닫혀 있어야 한다."**

- **의미:** 기존 코드를 수정하지 않고도 기능을 추가할 수 있어야 합니다. (주로 **인터페이스** 사용)
- **예시:** 결제 시스템을 만들 때 `if (payType == NAVER)` 처럼 분기처리를 하지 않고, `Payment` 인터페이스를 상속받은 `NaverPay`, `KakaoPay` 클래스를 추가함.
- **이점:** 기존 코드를 건드리지 않으니 버그 발생 확률이 낮아짐.

### 2.3 L: 리스코프 치환 원칙 (LSP - Liskov Substitution Principle)
> **"서브 타입은 언제나 자신의 기반(부모) 타입으로 교체할 수 있어야 한다."**

- **의미:** 자식 클래스는 부모 클래스의 기능을 수행할 수 있어야 하며, 부모의 규약을 깨면 안 됩니다.
- **대표적 위반 사례:** '직사각형'을 상속받은 '정사각형'. (너비와 높이가 다를 수 있는 직사각형의 성질을 정사각형이 깸)
- **핵심:** "자식이 부모의 행동을 거부하거나 퇴색시키면 안 된다."

### 2.4 I: 인터페이스 분리 원칙 (ISP - Interface Segregation Principle)
> **"하나의 범용 인터페이스보다 여러 개의 구체적인 인터페이스가 낫다."**

- **의미:** 클라이언트는 자신이 사용하지 않는 메서드에 의존하면 안 됩니다.
- **예시:** `SmartPhone` 인터페이스에 `call()`, `sms()`, `wirelessCharge()`가 다 있다면? 무선 충전 기능이 없는 구형 폰도 `wirelessCharge()`를 구현해야 하는 문제 발생. -> `Phone`, `Chargable` 인터페이스로 분리해야 함.

### 2.5 D: 의존관계 역전 원칙 (DIP - Dependency Inversion Principle)
> **"구체적인 것이 추상적인 것에 의존해야 한다."**

- **의미:** 구현 클래스(구체)에 의존하지 말고, 인터페이스(추상)에 의존하라는 뜻입니다.
- **현실:** 스프링의 **의존성 주입(DI)**이 바로 이 원칙을 따르는 것입니다.
- **Bad:** `MemberService`가 `MemoryRepository`를 직접 `new` 해서 사용.
- **Good:** `MemberService`는 `Repository` 인터페이스만 바라보고, 실제 구현체는 외부에서 주입해줌.

---

## 3. 코드 예시 (Example: OCP & DIP)
SOLID 중 가장 중요한 OCP와 DIP가 적용된 결제 시스템 예시입니다.

```java
// 1. 추상화 (Payment 인터페이스) - DIP 적용 (구체적인 카카오페이에 의존 X)
interface Payment {
    void pay(int amount);
}

// 2. 확장 (구현 클래스들)
class SamsungPay implements Payment {
    public void pay(int amount) { System.out.println("삼성페이 결제: " + amount); }
}

class KakaoPay implements Payment {
    public void pay(int amount) { System.out.println("카카오페이 결제: " + amount); }
}

// 3. 사용 (클라이언트) - OCP 적용
class PaymentService {
    private Payment payment; // 인터페이스에 의존 (DIP)

    // 외부에서 어떤 결제수단인지 주입받음 (생성자 주입)
    public PaymentService(Payment payment) {
        this.payment = payment;
    }

    public void process(int amount) {
        // 기존 코드를 수정하지 않고도, SamsungPay가 들어오든 KakaoPay가 들어오든 동작함 (OCP)
        payment.pay(amount);
    }
}
```

---

## 4. 왜 사용하는가? (Importance)
이 원칙들을 지키지 않으면 소위 **"스파게티 코드"**가 됩니다.

1.  **유지보수성 (Maintainability):** 어디를 고쳐야 할지 명확해집니다.
2.  **확장성 (Scalability):** 새로운 기능을 추가할 때 기존 시스템에 영향을 주지 않습니다.
3.  **테스트 용이성 (Testability):** 의존성이 분리되어 있어 단위 테스트(Mocking)가 쉬워집니다.

---

## 5. 사용하지 않을 땐? (Anti-Pattern)
SOLID를 무시하면 **'기술 부채(Technical Debt)'**가 쌓입니다.
- **Rigidity (경직성):** 하나를 바꿨는데 관련된 모든 코드를 다 뜯어고쳐야 함.
- **Fragility (취약성):** 수정한 부분과 전혀 관계없는 곳에서 에러가 터짐.
- **Immobility (부동성):** 코드가 너무 얽혀있어서 재사용이 불가능함.

---

## 6. 결론 (Conclusion)
SOLID는 객체지향 프로그래밍을 지탱하는 기둥입니다.

처음부터 100% 지키기는 어렵지만, **"코드를 짤 때 의존성은 줄이고, 응집도는 높인다(Low Coupling, High Cohesion)"**는 마음가짐을 갖는 것이 중요합니다. 특히 **스프링 프레임워크**는 이 SOLID 원칙을 극한으로 활용하여 만들어진 프레임워크이므로, 스프링을 잘 쓰기 위해서라도 반드시 이해해야 합니다.
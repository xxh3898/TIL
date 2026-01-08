# 접근 제어자(Access Modifier): 정보 은닉과 캡슐화의 핵심

## 1. 개요 (Overview)
### 1.1 정의
**접근 제어자(Access Modifier)**란 클래스, 메서드, 변수 등에 붙여서 **"해당 멤버에 접근할 수 있는 범위"**를 제어하는 키워드입니다.

### 1.2 목적
객체지향 프로그래밍의 핵심인 **캡슐화(Encapsulation)**와 **정보 은닉(Information Hiding)**을 구현하기 위해 사용합니다. 외부에서 건드리면 안 되는 중요한 데이터를 보호하고, 불필요한 부분은 감추어 복잡성을 줄입니다.

---

## 2. 접근 제어자의 종류와 범위 (The 4 Levels)
자바에는 4가지 수준의 접근 제어자가 있으며, **좁은 범위에서 넓은 범위 순서**로 이해하는 것이 좋습니다.

| 제어자           | 키워드         | 접근 가능 범위             | 설명                                   |
| :------------ | :---------- | :------------------- | :----------------------------------- |
| **private**   | `private`   | **클래스 내부**           | 가장 엄격함. 내 클래스 안에서만 보임.               |
| **default**   | (없음)        | **같은 패키지**           | 키워드를 안 쓰면 적용됨. 같은 폴더(패키지) 친구끼리만 공유.  |
| **protected** | `protected` | **같은 패키지 + 상속받은 자식** | default보다 조금 더 넓음. 다른 패키지라도 자식이면 허용. |
| **public**    | `public`    | **전체 (All)**         | 제한 없음. 프로젝트 어디서든 접근 가능.              |

![자바 접근 제어자 범위](https://hongong.hanbit.co.kr/wp-content/uploads/2021/09/01-%EC%9E%90%EB%B0%94%EC%9D%98-%EC%A0%91%EA%B7%BC-%EC%A0%9C%ED%95%9C%EC%9E%90_public_private.png)

---

## 3. 왜 중요한가? (Why & Importance)
### 3.1 "최소 권한의 원칙" (Principle of Least Privilege)
필요한 만큼만 열어두어야 합니다. 모든 것을 `public`으로 열어두면 다음과 같은 문제가 발생합니다.
1.  **데이터 무결성 훼손:** 외부에서 마음대로 값을 바꿔버릴 수 있습니다. (예: 계좌 잔액을 -로 설정)
2.  **결합도 증가:** 외부 코드가 내 클래스의 내부 변수에 직접 의존하게 되어, 나중에 변수명을 바꾸면 외부 코드도 다 에러가 납니다.

---

## 4. 예시 코드 (Example Code)

### 4.1 접근 범위 테스트
패키지가 서로 다른 상황(`pkg1`, `pkg2`)을 가정해야 `protected`와 `default`의 차이가 명확해집니다.

```java
package pkg1;

public class Parent {
    private String secret = "비밀";       // 나만 볼 거야
    String neighborhood = "동네 친구";    // (default) 같은 패키지
    protected String heritage = "유산";   // 같은 패키지 + 자식
    public String everyone = "공공재";    // 누구나
    
    public void printSecret() {
        System.out.println(secret); // private은 클래스 내부에서는 접근 가능
    }
}
```

```java
package pkg2; // 다른 패키지
import pkg1.Parent;

public class Child extends Parent { // Parent를 상속받음
    public void checkAccess() {
        // System.out.println(secret);       // 에러! (private)
        // System.out.println(neighborhood); // 에러! (default는 다른 패키지 불가)
        
        System.out.println(heritage); // 성공! (protected는 다른 패키지여도 '상속'받으면 가능)
        System.out.println(everyone); // 성공! (public)
    }
}
```

```java
package pkg2; // 다른 패키지, 상속 관계 아님

public class Stranger {
    public void checkAccess() {
        Parent p = new Parent();
        // System.out.println(p.heritage); // 에러! (상속받지 않은 남남은 protected 접근 불가)
        System.out.println(p.everyone);    // 성공!
    }
}
```

---

## 5. Getter와 Setter (캡슐화의 실천)
변수는 `private`으로 감추고, 메서드(`public`)를 통해 간접적으로 접근하게 하는 것이 표준 패턴입니다.

```java
public class Account {
    // 1. 변수는 private으로 숨김 (외부에서 직접 수정 금지)
    private int balance;

    // 2. 메서드는 public으로 틔움 (제어 가능)
    public void deposit(int amount) {
        if (amount < 0) {
            System.out.println("마이너스 금액은 입금할 수 없습니다.");
            return; // 잘못된 데이터 입력을 막는 로직(유효성 검사) 추가 가능
        }
        this.balance += amount;
    }

    public int getBalance() {
        return this.balance; // 읽기 전용 접근 허용
    }
}
```

---

## 6. 사용 시 주의할 점 & Best Practice

### 6.1 기본은 'private' 부터 시작하라
코드를 짤 때 접근 제어자를 고민한다면, 일단 `private`으로 만드세요. 개발하다가 외부에서 접근이 꼭 필요해질 때 하나씩 넓혀가는 것이 안전합니다.
> **순서:** `private` -> (필요시) `protected` -> (정말 필요시) `public`

### 6.2 클래스 변수(필드)는 절대 public으로 두지 마라
`public int age;` 처럼 필드를 바로 노출하는 것은 객체지향에서 **금기** 사항입니다. 반드시 `private`으로 막고 Getter/Setter를 사용하세요. (단, `public static final` 상수(Constant)는 예외)

### 6.3 protected의 오해
`protected`는 "상속받은 자식만" 접근 가능한 것이 아니라, **"같은 패키지 + 자식"**입니다. 즉, 같은 패키지에 있는 다른 클래스들도 `protected` 멤버에 접근할 수 있다는 점을 주의해야 합니다.

---

## 7. 결론 (Conclusion)
접근 제어자는 자바 프로그래밍의 **보안 담당자**입니다.

- **Private:** "내 방" (아무도 들어오지 마)
- **Default:** "우리 집" (가족끼린 공유)
- **Protected:** "우리 집 + 출가한 자식"
- **Public:** "공원" (누구나 환영)

이 4가지의 차이를 명확히 이해하고 적절히 사용하는 것이 **견고하고 안전한 애플리케이션**을 만드는 첫걸음입니다.
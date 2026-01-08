# 상속(Inheritance) vs 컴포지션(Composition): 무엇을 선택해야 할까?

## 1. 개요 (Overview)
### 1.1 정의
- **상속 (Inheritance):** 부모 클래스의 기능을 자식 클래스가 물려받아 사용하는 것. **IS-A 관계** ("Laptop is a Computer").
- **컴포지션 (Composition):** 다른 객체의 인스턴스를 자신의 필드(부품)로 포함해서 기능을 사용하는 것. **HAS-A 관계** ("Car has an Engine").

### 1.2 핵심 차이
상속은 코드를 재사용하는 강력한 방법이지만, **부모와 자식 간의 결합도(Coupling)**가 매우 높습니다. 반면 컴포지션은 객체를 조립해서 사용하므로 결합도가 낮고 유연합니다.

---

## 2. 왜 중요한가? (Why & Background)
### 2.1 "상속을 남용하지 마라"
객체지향 입문 단계에서는 코드 중복을 줄이기 위해 상속을 자주 사용합니다. 하지만 실무에서는 **"상속은 캡슐화를 깨뜨린다"**라는 치명적인 단점 때문에, 단순히 코드 재사용을 목적으로 하는 상속은 지양하고 **컴포지션**을 권장합니다.
(이펙티브 자바 Item 18: 상속보다는 컴포지션을 사용하라)

---

## 3. 동작 원리 및 비교 (Mechanism)

![상속과 컴포지션 구조 비교](https://i.ibb.co/cKNTsc3W/image.png)

| 구분 | 상속 (Inheritance) | 컴포지션 (Composition) |
| :--- | :--- | :--- |
| **관계** | **IS-A** (은 ~이다) | **HAS-A** (은 ~을 가지고 있다) |
| **방식** | `extends` 키워드 사용 | 클래스 필드에 객체 선언 (`private final Class ...`) |
| **결합도** | **높음** (부모 변경 시 자식 영향 큼) | **낮음** (인터페이스 통신) |
| **재사용** | 화이트박스 재사용 (부모 내부가 보임) | 블랙박스 재사용 (인터페이스만 보임) |
| **유연성** | 컴파일 타임에 결정 (정적) | 런타임에 부품 교체 가능 (동적) |

---

## 4. 예시 코드: 상속의 문제점 vs 컴포지션 해결책

상속을 잘못 사용했을 때 발생하는 **'취약한 기반 클래스 문제(Fragile Base Class Problem)'**를 코드로 봅시다.

### 4.1 상속 실패 사례 (HashSet 확장)
우리가 만든 Set에 요소가 몇 번 추가되었는지 세는 `InstrumentedHashSet`을 만든다고 가정합시다.

```java
// 상속 사용 (Inheritance)
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0; // 추가된 횟수

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
}

// 문제 발생
InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
s.addAll(List.of("A", "B", "C")); 
// 예상값: 3, 실제값: 6 (Why?)
// HashSet의 addAll()은 내부적으로 add()를 호출함.
// addAll에서 +3, 내부 add에서 +3 되어 중복 카운팅 발생!
```
부모 클래스(`HashSet`)의 내부 구현 방식(addAll이 add를 부른다)을 모르면 버그가 발생합니다. 즉, **캡슐화가 깨졌습니다.**

### 4.2 컴포지션 해결책 (Forwarding/Delegation)
상속 대신 `HashSet`을 필드로 가지고, 기능을 위임(Delegate)합니다.

```java
// 컴포지션 사용 (Composition)
public class InstrumentedSet<E> {
    private final Set<E> set; // 부품으로 가짐 (HAS-A)
    private int addCount = 0;

    public InstrumentedSet(Set<E> set) {
        this.set = set;
    }

    public boolean add(E e) {
        addCount++;
        return set.add(e); // 기능 위임 (Delegation)
    }

    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c); // 기능 위임
    }
    
    public int getAddCount() { return addCount; }
}
```
이제 `HashSet`이 내부적으로 `addAll`에서 `add`를 호출하든 말든 상관없습니다. 우리는 우리가 가진 `set` 객체에 일을 시키기만 하면 됩니다.

---

## 5. 장점과 단점 (Pros & Cons)

### 컴포지션의 장점
1.  **결합도 감소:** 부모 클래스의 내부 구현이 바뀌어도 영향을 덜 받습니다.
2.  **유연성:** 런타임에 다른 객체로 교체할 수 있습니다. (예: 생성자 주입으로 `HashSet` 대신 `TreeSet`을 넣을 수 있음)
3.  **캡슐화 유지:** 필요한 메서드만 외부에 노출할 수 있습니다.

### 컴포지션의 단점
1.  **Boilerplate Code:** 상속은 그냥 쓰면 되지만, 컴포지션은 위임 메서드(Forwarding Method)를 일일이 작성해야 해서 코드가 길어질 수 있습니다.

---

## 6. 언제 컴포지션을 사용해야 할까? (Usage)
이 기준을 면접에서 말하면 아주 좋습니다.

1.  **IS-A 관계가 명확하지 않을 때:**
    - 단순히 "코드 재사용"만을 위해 상속하려 한다면 -> **컴포지션**을 쓰세요.
2.  **API 오염을 막고 싶을 때:**
    - 상속을 받으면 부모의 불필요한 메서드까지 모두 자식에게 노출됩니다. (예: `Stack`이 `Vector`를 상속받아 불필요한 기능이 노출된 자바의 실수)
3.  **부모 클래스가 자주 변경될 때:**
    - 부모가 변경될 때마다 자식 코드를 고치고 싶지 않다면 컴포지션이 답입니다.

---

## 7. 사용 시 주의할 점 (Precautions)
### 7.1 진짜 상속(Inheritance)은 언제 쓰는가?
상속이 나쁜 것은 아닙니다. 다음 두 조건을 만족할 때만 상속을 사용하세요.
1.  확실한 **IS-A 관계**일 때. (사람 is a 포유류)
2.  상위 클래스가 **확장을 목적으로 설계**되었고, 문서화가 잘 되어 있을 때.

---

## 8. 결론 (Conclusion)
**"상속은 부모 클래스의 구현까지 물려받는 것이고, 컴포지션은 부모 클래스의 인터페이스(기능)만 빌려 쓰는 것이다."**

유연하고 유지보수하기 쉬운 시스템을 원한다면, 일단 **컴포지션**을 먼저 고려하세요. 상속은 정말 확실한 경우에만 사용하는 것이 모던 자바 개발의 핵심 원칙입니다.
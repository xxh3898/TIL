# Garbage Collection(GC): 자바 메모리 관리의 자동화

## 1. 개요 (Overview)
### 1.1 정의
**Garbage Collection(GC)**은 자바의 메모리 관리 기법 중 하나로, 애플리케이션이 동적으로 할당했던 메모리 영역(Heap) 중에서 **더 이상 사용되지 않는 객체(Garbage)를 찾아내어 자동으로 제거**하는 프로세스를 말합니다.

### 1.2 핵심 개념: Reachability
GC는 객체가 **유효한 참조(Reachability)**를 가지고 있는지를 기준으로 삭제 대상을 판단합니다.
- **Reachable:** 스택 영역의 변수 등에서 참조하고 있는 객체 (삭제 안 함)
- **Unreachable:** 어떤 변수에서도 참조하지 않는 객체 (삭제 대상)

---

## 2. 등장 배경 (Background)
### 2.1 "프로그래머를 믿지 마라" (C/C++의 문제점)
과거 C나 C++ 언어에서는 개발자가 메모리를 할당(`malloc`)하고, 사용이 끝나면 반드시 직접 해제(`free`)해야 했습니다.
- **Memory Leak:** 해제를 깜빡하면 메모리가 계속 쌓여 시스템이 멈춤.
- **Dangling Pointer:** 이미 해제된 메모리를 다시 참조하여 프로그램이 비정상 종료됨.

### 2.2 Java의 해결책
자바는 **"개발자는 비즈니스 로직에만 집중하라, 메모리는 내가 청소한다"**는 철학으로 GC를 도입했습니다. 이를 통해 개발 생산성과 안정성을 획기적으로 높였습니다.

---

## 3. 동작 원리 (Mechanism)

GC의 동작 과정을 이해하려면 **Heap 메모리 구조**와 **Mark and Sweep** 알고리즘을 알아야 합니다.

### 3.1 Heap 메모리의 구조 (Visual)
자바의 Heap 영역은 객체의 생존 기간에 따라 크게 두 영역으로 나뉩니다.

![Heap 메모리 구조](https://i.ibb.co/d4mRcgGb/Heap.png)

1.  **Young Generation (젊은 영역):**
    - 새롭게 생성된 객체가 할당되는 곳(`Eden`).
    - 대부분의 객체는 금방 사용되지 않게 되므로(Unreachable), 여기서 많은 객체가 사라집니다. (**Minor GC**)
2.  **Old Generation (늙은 영역):**
    - Young 영역에서 오랫동안 살아남은 객체들이 이동(Promotion)되는 곳.
    - 영역이 크고 가득 차면 큰 비용의 청소 작업이 발생합니다. (**Major GC / Full GC**)

### 3.2 Mark and Sweep 알고리즘
GC가 실제로 청소하는 기본적인 단계입니다.
1.  **Mark:** Root Space(스택, static 변수 등)에서 그래프 순회를 통해 연결된(Reachable) 객체를 찾아 마킹합니다.
2.  **Sweep:** 마킹되지 않은(Unreachable) 객체들을 Heap에서 제거합니다.
3.  *(Compact):* (일부 알고리즘) 메모리 파편화를 방지하기 위해 남은 객체들을 한곳으로 모읍니다.

---

## 4. 예시 코드 (Example Code)
언제 객체가 GC의 대상이 되는지 코드로 확인해 봅시다.

```java
public class GCTest {
    public static void main(String[] args) {
        // 1. 객체 생성 (Heap의 Eden 영역에 할당)
        Person person = new Person("Developer");

        // person 변수는 힙 영역의 Person 객체를 참조 중 (Reachable)

        // 2. 참조 끊기
        person = null; 
        
        // 이제 처음에 생성된 Person 객체를 가리키는 변수가 없음.
        // 해당 객체는 Unreachable 상태가 되어, 다음 GC가 돌 때 수거됨.
        
        // 3. 강제 GC 요청 (주의: 실제로는 잘 쓰지 않음, 요청만 할 뿐 즉시 실행 보장 안 됨)
        System.gc(); 
    }
}
```

---

## 5. 장점과 단점 (Pros & Cons)

### 장점 (Pros)
- **생산성 향상:** 메모리 할당/해제 로직을 짤 필요가 없어 개발 속도가 빠릅니다.
- **안정성:** 유효하지 않은 포인터 접근(Segfault)이나 메모리 누수를 원천적으로 줄여줍니다.

### 단점 (Cons)
- **성능 오버헤드:** GC가 실행되는 동안 CPU 리소스를 사용합니다.
- **Stop-The-World (STW):** 이게 가장 큰 단점입니다.
    > **Stop-The-World란?**
    > GC를 실행하기 위해 **JVM이 애플리케이션 실행을 멈추는 현상**입니다. 이 시간 동안에는 아무런 작업도 할 수 없으므로, STW 시간을 줄이는 것이 GC 튜닝의 핵심입니다.

---

## 6. 종류 및 진화 (Types of GC)

1.  **Serial GC:** 스레드 1개로 동작. 가장 단순하지만 멈춤 시간(STW)이 깁니다. (실무 사용 X)
2.  **Parallel GC:** Young 영역 청소를 멀티 스레드로 수행. (Java 8 기본)
3.  **G1 GC (Garbage First):** Heap을 바둑판 모양의 Region으로 나누어 관리. 전체를 뒤지지 않고 꽉 찬 영역만 청소하여 STW를 획기적으로 줄임. **(Java 9+ 기본)**
4.  **ZGC:** 대용량 메모리 처리를 위해 설계됨. STW 시간을 몇 ms 단위로 줄이는 초저지연 GC.

---

## 7. 사용 시 주의할 점 (Precautions & Best Practice)
### 7.1 System.gc() 절대 사용 금지
코드에서 `System.gc()`를 직접 호출하면 시스템 전체가 멈추는(Full GC) 성능 재앙이 발생할 수 있습니다. 운영 서버 코드에는 절대 포함하면 안 됩니다.

### 7.2 메모리 누수 패턴 주의
GC가 있어도 메모리 누수는 발생합니다.
- **Static Collection:** `HashMap` 등에 객체를 넣고 제거하지 않으면 프로그램 종료 시까지 메모리를 잡고 있습니다.
- **Inner Class:** 익명 클래스 등이 외부 클래스의 인스턴스를 암묵적으로 참조하는 경우.

---

## 8. 사용하지 않을 땐? (Alternatives)
"GC의 멈춤 현상조차 허용할 수 없는 초고성능 시스템이라면?"

1.  **객체 풀링 (Object Pooling):**
    - 객체를 생성/삭제하는 비용과 GC 부하를 줄이기 위해, 미리 객체를 만들어두고 재사용하는 방식입니다. (예: DB Connection Pool)
2.  **Off-Heap Memory:**
    - Heap이 아닌 OS 메모리(Direct Buffer)를 직접 사용하여 GC 대상에서 제외합니다. (Netty 같은 고성능 네트워크 프레임워크에서 사용)
3.  **No-GC 언어 사용:**
    - C++, Rust 처럼 수동 관리 혹은 소유권 모델을 사용하는 언어로 핵심 모듈을 개발합니다.

---

## 9. 결론 (Conclusion)
자바 개발자에게 GC는 **"고마운 청소부이자, 때로는 관리해야 할 대상"**입니다.
GC의 동작 원리(Mark & Sweep)와 힙 구조를 이해하는 것은 단순한 이론 공부를 넘어, **대규모 트래픽을 감당하는 안정적인 서비스**를 운영하기 위한 필수 조건입니다.
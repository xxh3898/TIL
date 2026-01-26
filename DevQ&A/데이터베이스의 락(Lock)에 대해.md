# 락(Lock): 데이터의 무결성을 지키는 자물쇠


**락(Lock)**은 데이터베이스에서 여러 트랜잭션이 **동시에 같은 데이터에 접근할 때**, 데이터의 일관성을 해치지 않도록 접근 순서를 제어하는 기능입니다.

### 비유하자면 "화장실 열쇠"
화장실(데이터)은 한 번에 한 명만 쓸 수 있습니다.
* **Lock 획득:** 화장실에 들어가면서 문을 잠그는 행위.
* **Lock 반납(해제):** 일을 다 보고 나오면서 문을 여는 행위.
* **대기:** 안에 사람이 있으면, 나올 때까지 줄 서서 기다려야 함.

---

## 왜 필요한가?
락이 없다면 **동시성 문제(Concurrency Problem)**가 발생합니다.

* **갱신 손실 (Lost Update):** A와 B가 동시에 잔액 1000원을 조회해서 500원을 뺐는데, 결과가 500원이 아니라 1000원 -> 500원으로 덮어씌워지는 현상.
* **오손 읽기 (Dirty Read):** 아직 확정(Commit)되지 않은 데이터를 다른 사람이 읽어버리는 현상.



---

## 락의 종류

### 1. DB 레벨에서의 분류 (기본)
데이터베이스가 내부적으로 사용하는 기본적인 락입니다.

| 종류 | 이름 | 설명 | 특징 |
| :--- | :--- | :--- | :--- |
| **공유 락** | **Shared Lock (S-Lock)** | 데이터를 **읽을 때(Read)** 사용 | "나 읽고 있어. 너도 읽는 건 되는데, 쓰지는 마." (읽기끼리 공유 가능) |
| **베타 락** | **Exclusive Lock (X-Lock)** | 데이터를 **변경할 때(Write)** 사용 | "나 수정 중이야. 아무도 읽지도 말고 쓰지도 마." (독점) |

### 2. JPA/애플리케이션 레벨의 전략 
실무에서는 상황에 따라 아래 두 가지 전략 중 하나를 선택합니다.

#### (1) 비관적 락 (Pessimistic Lock)
> **"충돌이 무조건 일어날 거야. 그러니까 처음부터 잠그자."**
* **방식:** 데이터를 읽을 때부터 아예 락(X-Lock)을 걸어버려 다른 사람이 접근 못 하게 합니다. (`SELECT ... FOR UPDATE`)
* **장점:** 데이터 정합성이 완벽하게 보장됩니다.
* **단점:** 서로 자원을 기다리다 멈추는 **데드락(Deadlock)** 가능성이 있고, 성능이 저하됩니다.
* **사용처:** 돈(계좌 이체), 재고 차감 등 민감한 데이터.

#### (2) 낙관적 락 (Optimistic Lock)
> **"충돌이 설마 일어나겠어? 일단 하고 나중에 확인하자."**
* **방식:** 실제로 DB 락을 걸지 않습니다. 대신 **버전(Version)** 컬럼을 이용해 충돌을 감지합니다.
    1.  데이터 읽을 때 버전 확인 (Version: 1)
    2.  수정 요청 시 버전을 1 증가시킴 (Version: 2)
    3.  저장할 때, "지금 DB 버전이 아까 읽은 1 맞아?" 확인. 맞으면 저장, 아니면 롤백.
* **장점:** 락을 안 거니 성능이 좋습니다.
* **단점:** 충돌이 발생하면 개발자가 직접 재시도 로직(Retry)을 짜야 합니다.

---

## 교착 상태 (Deadlock)
락을 쓰다 보면 필연적으로 마주치는 문제입니다.
* **상황:**
    * 트랜잭션 A: 1번 자원 잡고, 2번 자원 기다림.
    * 트랜잭션 B: 2번 자원 잡고, 1번 자원 기다림.
    * **결과:** 둘 다 서로 놓아주길 기다리며 무한 대기.
* **해결:** 트랜잭션을 최대한 짧게 유지하거나, 접근 순서를 일치시켜야 합니다.

---

## 코드 예시 (JPA/Spring Boot)

### 1. 비관적 락 (Pessimistic)
```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // SELECT ... FOR UPDATE 쿼리가 나감
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("select p from Product p where p.id = :id")
    Optional<Product> findByIdWithPessimisticLock(Long id);
}
```

### 2. 낙관적 락 (Optimistic)
**Entity 설정 (@Version 추가)**
```java
@Entity
public class Product {
    @Id @GeneratedValue
    private Long id;

    private int quantity;

    @Version // 낙관적 락을 위한 버전 관리 필드
    private Long version;
}
```

**예외 처리 (충돌 시)**
```java
try {
    productService.decrease(id, quantity);
} catch (ObjectOptimisticLockingFailureException e) {
    // 버전이 안 맞으면 이 예외가 발생함
    // 여기서 재시도 로직을 작성
    System.out.println("누군가 먼저 수정했습니다. 다시 시도하세요.");
}
```

---

## 결론
* **동시 접속자가 적고 충돌 확률이 낮다?** -> **낙관적 락 (@Version)** (성능 우선)
* **돈이나 재고처럼 데이터가 절대 틀리면 안 된다?** -> **비관적 락 (For Update)** (정합성 우선)

특히 선착순 이벤트나 수강 신청 같은 **고트래픽 환경**에서는 DB 락만으로는 버티기 힘들어, **Redis** 같은 분산 락(Distributed Lock)을 추가로 사용하기도 합니다.
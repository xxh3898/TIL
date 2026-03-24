# QueryDSL

## 1. 개념 (Concept)
QueryDSL은 정적 타입을 이용하여 SQL과 같은 쿼리를 자바 코드로 생성할 수 있도록 해주는 오픈소스 프레임워크다.
JPA와 연동할 경우(QueryDSL JPA), 엔티티(Entity) 클래스를 기반으로 자동 생성된 Q-Class를 활용하여 컴파일 타임에 문법 오류를 검증할 수 있는 객체 지향적 쿼리 작성을 지원한다.

## 2. 도입 목적 및 시장 현실 (Why Use It?)
실무 애플리케이션에서는 단순 CRUD보다 다중 검색 조건(필터링), 페이징, 복잡한 테이블 조인이 결합된 '동적 쿼리(Dynamic Query)'가 시스템의 핵심이다.
* **경쟁 기술의 한계:** * Spring Data JPA의 기본 쿼리 메서드(`findBy...`)는 조건이 추가될수록 메서드 이름이 기형적으로 길어져 가독성이 붕괴된다.
  * `@Query`(JPQL)는 순수 문자열이므로 오타 발생 시 런타임 에러(서비스 작동 중 장애)로 이어진다.
  * JPA Criteria는 자바 표준이지만 코드가 지나치게 복잡하여 실무에서 유지보수가 불가능에 가깝다.
* **문제 해결:** QueryDSL은 자바 코드로 쿼리를 작성하므로 컴파일 시점에 문법 오류를 100% 차단하며, IDE의 자동 완성 기능을 완벽하게 지원하여 개발 생산성을 극대화한다.

## 3. 장단점 및 리스크 분석 (Pros & Cons)

### 강점 (Pros)
1. **타입 안정성 (Type Safety):** 쿼리를 문자열이 아닌 코드로 작성하므로, 필드명 변경이나 오타로 인한 치명적인 런타임 에러를 컴파일 단계에서 방어한다.
2. **가독성 및 유지보수성:** 자바의 메서드 체이닝(Method Chaining) 방식을 사용하므로 SQL과 구조가 유사하면서도 직관적이다. 코드를 읽고 분석하는 시간이 대폭 단축된다.
3. **동적 쿼리의 모듈화:** 다중 조건 `where` 절 처리를 압도적으로 깔끔하게 작성할 수 있으며, 검색 조건 자체를 자바 메서드로 분리하여 재사용할 수 있다.

### 단점 및 리스크 (Cons)
1. **초기 설정의 복잡성:** 빌드 도구(Gradle 등)에 Q-Class 생성 스크립트를 구성해야 하며, 스프링 부트 버전에 따라 의존성 설정 및 충돌 문제가 빈번하게 발생한다.
2. **JPA 의존성 한계:** QueryDSL은 결국 JPQL을 빌드해주는 도구일 뿐이다. JPA 영속성 컨텍스트, 1차 캐시, 플러시(Flush) 메커니즘을 모르면 N+1 문제나 성능 지연을 본질적으로 해결할 수 없다.
3. **벌크 연산 리스크:** QueryDSL로 대량의 데이터를 한 번에 수정/삭제(Update/Delete)하는 벌크 연산을 수행할 경우, 영속성 컨텍스트를 무시하고 DB에 직접 쿼리를 실행한다. 이로 인해 DB와 메모리(영속성 컨텍스트) 간의 데이터 불일치가 발생하므로, 연산 후 반드시 영속성 컨텍스트를 수동으로 초기화(`em.clear()`)해야 하는 치명적인 리스크가 있다.

## 4. 실무 활용 및 최적 전략 (How to Use)
QueryDSL을 단순한 쿼리 작성 도구가 아닌, 아키텍처의 유지보수성을 높이는 전략적 도구로 활용해야 한다.

1. **동적 쿼리 최적화:** `BooleanBuilder`를 사용하는 대신, **`BooleanExpression`을 반환하는 메서드를 조합하는 방식**이 최적의 전략이다. `isAdult()`, `isActive()`와 같이 조건을 모듈화하여 가독성을 높이고 다른 쿼리에서 재사용하라.
2. **페이징 및 성능 최적화:** * 연관된 엔티티를 한 번에 가져와야 할 때(N+1 문제 방어), 단순 `join()`이 아닌 반드시 `fetchJoin()`을 사용하여 쿼리 발생 횟수를 1회로 통제한다.
   * 대용량 데이터 페이징 시 `offset`, `limit` 방식은 데이터가 많아질수록 치명적인 성능 저하를 유발한다. **No-Offset 방식(마지막으로 조회된 ID를 인덱스로 삼아 다음 페이지 조회)**을 QueryDSL로 구현하여 성능을 최적화하라.

## 5. 면접 핵심 방어 논리 및 자가 합리화 지적
* **자가 합리화 경계:** "진행한 프로젝트 규모가 작아서 Spring Data JPA의 기본 기능과 `@Query` 문자열만으로 충분했다"는 변명은 확장에 대한 고려가 없는 비효율적 마인드다. 비즈니스 로직은 반드시 변하고 확장된다는 시장의 현실을 외면하는 것이다.
* **최적의 실행 전략 제시:** 면접관에게 아키텍처 분리 전략을 명확히 제시하라. "단순한 생성, 수정, 단건 조회(Command)는 Spring Data JPA로 빠르게 처리하고, 복잡한 검색 조건과 다중 조인이 필요한 목록 조회(Query) 로직은 사용자 정의 리포지토리(Custom Repository)를 만들어 QueryDSL로 철저히 분리하는 **CQRS(명령과 조회의 책임 분리) 유사 패턴**을 적용해 시스템의 복잡도를 통제했다"고 논리적으로 주장하라.

## 예시 코드
``` java
// 유지보수성과 타입 안정성을 확보하고, Fetch Join을 통해 DB I/O를 최적화한다.

package com.example.order.repository.custom;

import com.example.order.domain.Order;
import com.querydsl.core.types.dsl.BooleanExpression;
import com.querydsl.jpa.impl.JPAQueryFactory;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import org.springframework.util.StringUtils;

import java.util.List;

// Q-Class 정적 임포트 (컴파일 타임에 타입과 필드명 오류를 원천 차단)
import static com.example.order.domain.QOrder.order;
import static com.example.member.domain.QMember.member;

@Repository
@RequiredArgsConstructor
public class OrderRepositoryCustomImpl implements OrderRepositoryCustom {

    private final JPAQueryFactory queryFactory;

    /**
     * 주문 다중 조건 검색 및 연관된 회원 정보 즉시 로딩 (N+1 리스크 방어)
     * * @param memberName 검색할 회원 이름 (가변적)
     * @param orderStatus 검색할 주문 상태 (가변적)
     * @return 필터링된 주문 목록
     */
    @Override
    public List<Order> findOrdersDynamicQuery(String memberName, String orderStatus) {
        return queryFactory
                .selectFrom(order)
                // 핵심 리스크 방어: 일반 join()을 쓰면 Order 조회 후 Member 조회를 위해 N번의 추가 쿼리가 발생한다.
                // fetchJoin()을 강제하여 하나의 쿼리로 연관 데이터를 모두 가져와 I/O 병목을 해결한다.
                .leftJoin(order.member, member).fetchJoin()
                .where(
                        // 다중 조건(동적 쿼리)을 콤마(,)로 연결하여 AND 조건으로 처리한다.
                        // null이 반환되면 QueryDSL이 안전하게 해당 조건을 무시하므로 분기문(if) 없이 깔끔하게 처리된다.
                        memberNameEq(memberName),
                        orderStatusEq(orderStatus)
                )
                .limit(1000) // 대량 데이터 조회 시 안전장치(Limit) 부여
                .fetch();
    }

    /**
     * 조건 모듈화 1: 회원 이름 동적 검증
     * 자바 메서드로 분리하여 가독성을 높이고 다른 쿼리에서도 재사용성을 확보한다.
     */
    private BooleanExpression memberNameEq(String memberName) {
        // 입력값이 비어있으면 null을 반환하여 where 절에서 해당 조건을 무력화시킨다.
        if (!StringUtils.hasText(memberName)) {
            return null; 
        }
        return member.name.eq(memberName);
    }

    /**
     * 조건 모듈화 2: 주문 상태 동적 검증
     */
    private BooleanExpression orderStatusEq(String orderStatus) {
        if (!StringUtils.hasText(orderStatus)) {
            return null;
        }
        return order.status.stringValue().eq(orderStatus);
    }
}
```
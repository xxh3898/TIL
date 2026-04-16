---
note_type: dev_qa
status: seed
created: 2026-03-24
updated: 2026-03-24
question: "Redis (Remote Dictionary Server)"
domain: database
related_topics: []
interview_priority: high
source_ref:
tags: []
area: "[[Areas/Career]]"
project:
---
# Redis (Remote Dictionary Server)
## 1. 개념 (Concept)
Redis는 **메모리 기반(In-Memory)의 Key-Value 구조를 가진 비관계형 데이터베이스(NoSQL)**다.
데이터를 물리적 디스크(Disk)가 아닌 주 메모리(RAM)에 저장하여, 디스크 I/O 병목을 원천적으로 제거하고 극단적으로 빠른 읽기 및 쓰기 속도를 보장한다.

## 2. 도입 목적 및 시장 현실 (Why Use It?)
RDBMS(MySQL, Oracle 등)는 데이터의 영속성과 정합성을 보장하지만, 구조적으로 디스크에 접근해야 하므로 대규모 트래픽(I/O) 발생 시 시스템 전체의 지연(Latency)을 초래한다.
* **병목 해결:** 애플리케이션과 DB 사이의 캐시(Cache) 계층으로 작동하여 DB의 부하를 획기적으로 낮춘다.
* **비용 효율성:** DB 서버의 하드웨어를 증설(Scale-up)하는 막대한 비용보다, 앞단에 Redis를 배치하여 자주 조회되는 데이터를 캐싱하는 것이 인프라 비용 대비 성능(ROI) 측면에서 월등히 효율적이다.

## 3. 장단점 및 리스크 분석 (Pros & Cons)

### 강점 (Pros)
1. **압도적인 처리 속도:** 메모리 접근 방식을 통해 초당 수십만 회 이상의 연산 처리가 가능하다.
2. **다양한 자료구조(Data Structures) 지원:** 단순 String 뿐만 아니라 List, Set, Sorted Set, Hash 등을 지원한다. 이는 애플리케이션 단계에서 구현해야 할 로직 복잡도를 대폭 낮춘다. (예: 랭킹 시스템 구현 시 RDBMS의 복잡한 쿼리보다 Redis의 Sorted Set을 사용하는 것이 시간 복잡도와 리소스 측면에서 최적의 선택이다.)
3. **싱글 스레드 (Single Thread):** 데이터베이스 엔진 코어가 단일 스레드로 동작하여 한 번에 하나의 명령만 처리하므로, 복잡한 락(Lock) 없이도 원자성(Atomicity)을 보장하고 Race Condition을 방지한다.

### 단점 및 리스크 (Cons)
1. **비용 및 리소스 한계:** RAM은 디스크에 비해 단위 용량당 가격이 매우 높다. 무분별하게 데이터를 적재하면 메모리 파편화(Fragmentation)가 발생하거나 OOM(Out of Memory)으로 인해 시스템이 다운될 수 있다.
2. **휘발성 (Data Loss Risk):** 주 메모리를 사용하므로 전원이 차단되면 데이터가 소실된다. 이를 방지하기 위해 RDB(Snapshot)나 AOF(Append Only File) 방식으로 디스크에 백업하는 메커니즘이 존재하지만, 이 설정 과정에서 성능 저하가 발생할 수 있다.
3. **싱글 스레드의 치명적 맹점:** 하나의 명령어 처리 시간이 길어지면 전체 시스템이 대기(Blocking) 상태에 빠진다. 실무에서 `KEYS *`나 `FLUSHALL`과 같은 시간 복잡도 $O(N)$의 명령어를 무심코 사용할 경우 대규모 장애로 직결된다.

## 4. 실무 활용 및 최적 전략 (How to Use)
Redis는 단순한 저장소가 아니라 시스템의 한계를 돌파하기 위한 '전략적 도구'로 설계되어야 한다.

1. **캐싱 전략 (Caching):**
   * **Look-Aside (Lazy Loading):** 데이터 조회 시 Redis를 먼저 확인하고, Cache Miss 발생 시 DB에서 조회 후 Redis에 저장한다. (가장 검증된 표준 전략)
   * **Write-Around / Write-Through:** 쓰기 작업 빈도와 데이터 동기화 요구사항에 따라 DB와 캐시 간의 갱신 전략을 선택한다.
2. **분산 환경의 세션 저장소 (Session Store):** 서버가 여러 대인 Scale-out 환경에서 사용자 세션의 정합성을 유지하기 위해 중앙 집중식 세션 스토어로 활용한다.
3. **실시간 랭킹 산출 (Leaderboard):** `Sorted Set`을 활용해 DB에 부하를 주지 않고 실시간 순위 데이터를 제공한다.
4. **유량 제어 (Rate Limiter):** 특정 시간 내 API 호출 횟수를 제한하여 서버 과부하 및 악의적인 트래픽(Abusing)을 차단한다.

## 5. 면접 핵심 방어 논리 및 자가 합리화 지적
* **자가 합리화 경계:** "스프링이 제공하는 로컬 캐시(Caffeine 등)나 추상화(Spring Cache) 기능만 쓰면 충분하다"는 생각은 비효율적이고 좁은 시야다. 다중 서버 환경(MSA 등)에서 로컬 캐시가 야기하는 데이터 불일치(Inconsistency) 문제를 인지하고, 왜 '분산 캐시'인 Redis가 필수적인지 논리적으로 설명해야 한다.
* **최적의 실행 전략 제시:** Spring Data Redis (Lettuce 클라이언트)를 활용해 시스템을 설계하되, 면접관에게 반드시 **데이터 정합성 문제(Cache Stampede 현상 등)**에 대한 현실적인 방어책을 먼저 제시하라. (예: 각 키마다 적절하고 분산된 TTL(Time To Live)을 설정하고, 대규모 트래픽 갱신 시 분산 락(Redisson)을 활용해 DB 부하를 차단한다는 논리 전개)

## 예시 코드
``` java
// RDBMS의 부하를 최소화하고 응답 속도를 극대화하는 실무 표준 패턴이다.

package com.example.product.service;

import com.example.product.domain.Product;
import com.example.product.repository.ProductRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.time.Duration;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class ProductRedisService {

    private final ProductRepository productRepository;
    private final RedisTemplate<String, Object> redisTemplate;
    
    // 캐시 키의 접두사. 식별을 명확히 하여 Key 충돌 리스크를 방지한다.
    private static final String CACHE_KEY_PREFIX = "product:info:";
    // TTL(Time To Live) 설정. 영구 저장은 메모리 고갈(OOM) 리스크를 유발하므로 필수적으로 설정해야 한다.
    private static final Duration CACHE_TTL = Duration.ofMinutes(30);

    /**
     * 상품 정보 조회 (Look-Aside 패턴)
     * 1. Redis 캐시 먼저 확인
     * 2. 데이터가 없으면 DB(RDBMS)에서 조회
     * 3. DB에서 조회한 데이터를 Redis에 적재 후 반환
     */
    public Product getProductInfo(Long productId) {
        String cacheKey = CACHE_KEY_PREFIX + productId;

        // 1. Redis에서 데이터 조회 시도 (I/O 병목이 없는 메모리 접근)
        Product cachedProduct = (Product) redisTemplate.opsForValue().get(cacheKey);
        
        // 캐시 히트(Cache Hit): DB 접근 없이 즉시 반환하여 성능 최적화
        if (cachedProduct != null) {
            return cachedProduct;
        }

        // 2. 캐시 미스(Cache Miss): Redis에 데이터가 없으므로 RDBMS에서 실제 데이터 조회
        Product dbProduct = productRepository.findById(productId)
                .orElseThrow(() -> new IllegalArgumentException("존재하지 않는 상품입니다. 확인 불가한 ID: " + productId));

        // 3. DB에서 조회한 데이터를 Redis에 적재 (이후 동일 요청 시 캐시 히트 발생)
        // 주의: 반드시 TTL을 설정하여 메모리 파편화 및 OOM 리스크를 제어해야 한다.
        redisTemplate.opsForValue().set(cacheKey, dbProduct, CACHE_TTL);

        return dbProduct;
    }
}
```

## Related

- [캐시 설계와 캐시 무효화 전략](./캐시%20설계와%20캐시%20무효화%20전략.md)
- [데이터 정합성과 중복 요청 방지 전략](./데이터%20정합성과%20중복%20요청%20방지%20전략.md)

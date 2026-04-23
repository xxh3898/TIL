---
note_type: dev_qa
status: seed
created: 2026-04-23
updated: 2026-04-23
question: "MySQL source of truth를 유지한 채 Redis read model을 어떻게 안전하게 붙여야 하는가"
domain: database
related_topics:
  - redis
  - read-model
  - fallback
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-21 - TIL.md"
tags:
  - redis
  - architecture
  - devqa
area: "[[Areas/Career]]"
project:
---

# MySQL source of truth를 유지한 채 Redis read model을 어떻게 안전하게 붙여야 하는가

## Why

- Redis를 붙인다고 해서 영속 기준까지 같이 옮기면 정합성과 롤백 경로가 급격히 복잡해진다.
- read model 전환의 핵심은 저장 위치 변경이 아니라 조회 책임을 분리하는 것이다.
- 면접에서는 "Redis를 썼다"보다 "source of truth, fallback, rebuild를 어떻게 설계했는가"를 설명할 수 있어야 설계 판단이 드러난다.

## Core Concept

- 먼저 source of truth를 고정해야 한다. 영속성과 최종 정합성 판단은 계속 MySQL 같은 주 저장소가 맡고, Redis는 조회 hot path를 빠르게 읽기 위한 파생 저장소로 두는 편이 안전하다.
- rollout 범위도 좁게 잡아야 한다. 기본 조회만 Redis로 옮기고 검색이나 ready marker 부재 같은 조건에서는 MySQL fallback을 유지하면, 계약 변경 없이 성능 개선과 롤백 경로를 동시에 확보할 수 있다.
- read model은 steady-state latency만 보고 끝내면 안 된다. PB 변경 시 증분 동기화, 전체 backfill, startup 재구축 시간, ready marker 같은 운영 메커니즘까지 닫혀 있어야 실제 서비스에서 쓸 수 있다.
- 결국 중요한 것은 "Redis가 더 빠르다"가 아니라 "언제 Redis를 신뢰하고, 언제 MySQL로 돌아가며, 재구축 실패를 어떻게 감지할 것인가"를 명시하는 것이다.

## Interview Answer Version

- 30~40초 답변: 저는 Redis read model을 붙일 때 먼저 MySQL을 source of truth로 고정합니다. 영속성과 정합성 판단은 계속 MySQL이 맡고, Redis는 조회 hot path를 위한 파생 저장소로만 두는 방식입니다. 또 처음부터 전체 조회를 다 옮기지 않고 기본 조회만 Redis로 전환하고, 검색이나 ready marker 부재 같은 조건에서는 MySQL fallback을 유지합니다. 여기에 증분 동기화, 전체 backfill, startup 재구축 시간까지 같이 설계해야 read model이 단순 캐시가 아니라 운영 가능한 구조가 된다고 봅니다.

## Practical Tip

- 실제 적용 사례: 랭킹 기본 조회는 Redis ZSET으로 옮기고, `nickname` 검색이나 비정상 상태는 MySQL query 경로를 유지해 hybrid rollout을 택한다.
- 잘못 쓰면 생기는 문제: Redis를 곧바로 주 저장소처럼 다루면 동기화 실패, backfill 누락, 재구축 지연이 생겼을 때 어떤 데이터가 진짜 기준인지 설명하기 어려워진다.
- 프로젝트 연결: 대규모 조회 병목에서는 `same API contract + narrow rollout + explicit fallback` 조합이 가장 현실적인 첫 단계다.

## Related

- [2026-04-21 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-21%20-%20TIL.md)
- [Redis란](./Redis란.md)
- [캐시 설계와 캐시 무효화 전략](./%EC%BA%90%EC%8B%9C%20%EC%84%A4%EA%B3%84%EC%99%80%20%EC%BA%90%EC%8B%9C%20%EB%AC%B4%ED%9A%A8%ED%99%94%20%EC%A0%84%EB%9E%B5.md)

## 예상 꼬리 질문

- read model과 일반 캐시는 어떻게 다른가요?
  - 답변: 일반 캐시는 주 저장소 앞단에 임시로 두는 경우가 많지만, read model은 특정 조회 계약을 위해 별도 자료구조와 재구축 흐름을 갖는 파생 저장소에 더 가깝습니다. 그래서 backfill과 운영 훅 설계가 더 중요합니다.
- fallback은 언제까지 유지해야 하나요?
  - 답변: 장애 감지와 재구축 경로가 충분히 안정화되기 전까지는 유지하는 편이 안전합니다. 특히 검색, rare query, warm-up 이전 상태처럼 불확실성이 큰 경계는 늦게 옮기는 것이 낫습니다.
- startup 재구축 시간이 길면 어떻게 하나요?
  - 답변: 부팅 시 전체 재구축을 항상 켜기보다 별도 운영 트리거, 배치 backfill, ready marker 확인 같은 방식으로 분리하는 편이 현실적입니다. 핵심은 서비스 시작 경로와 재구축 경로를 분리하는 것입니다.

## Internal Links

- [[Areas/Career]]

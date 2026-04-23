---
note_type: dev_qa
status: seed
created: 2026-04-21
updated: 2026-04-21
question: "성능 리팩터링 전후 비교에서 benchmark baseline 계약을 왜 먼저 고정해야 하는가"
domain: performance
related_topics:
  - benchmarking
  - performance-testing
  - architecture
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-20 - TIL.md"
tags:
  - performance
  - benchmark
  - devqa
area: "[[Areas/Career]]"
project:
---

# 성능 리팩터링 전후 비교에서 benchmark baseline 계약을 왜 먼저 고정해야 하는가

## Why

- 성능 개선은 숫자를 만드는 일보다 "같은 조건에서 비교 가능한 실험"을 만드는 일이 먼저다.
- baseline 계약이 없으면 Day 1 숫자와 Day 2 숫자가 같은 API를 측정한 것인지조차 설명하기 어려워진다.
- 면접이나 회고에서도 단순히 "몇 ms 빨라졌다"보다 실험 조건을 어떻게 고정했는지를 말해야 결과 신뢰도가 생긴다.

## Core Concept

- benchmark baseline 계약은 무엇을 같은 조건으로 비교할지 미리 고정하는 약속이다. 최소한 요청 시나리오, query parameter, page/size, 실패 허용 기준은 먼저 닫혀 있어야 한다.
- 데이터셋도 중요하다. 같은 seed를 재적재할 수 없으면 후보 수, 정렬 비용, 캐시 상태가 달라져 refactor 전후 숫자를 같은 실험으로 보기 어렵다.
- 결과 형식도 baseline의 일부다. `summary.json`, 비교용 Markdown/HTML, 같은 대시보드 레이아웃처럼 어떤 artifact를 남길지 먼저 정해야 다음 실험과 나란히 비교할 수 있다.
- 그래서 benchmark는 일반 기능 CI와 분리하는 편이 낫다. 기능 CI는 빠른 회귀 검출이 목적이지만, benchmark는 동일 조건 재현과 artifact 보관이 더 중요하기 때문이다.

## Interview Answer Version

- 30~40초 답변: 성능 리팩터링 전후 비교에서는 refactor보다 먼저 benchmark baseline 계약을 고정해야 한다고 생각합니다. 어떤 API를 어떤 파라미터로 측정할지, 같은 seed 데이터를 어떻게 재현할지, 결과를 어떤 artifact 형식으로 남길지를 먼저 닫아야 전후 숫자가 같은 실험이라고 설명할 수 있습니다. 그래서 저는 benchmark를 일반 기능 CI에 섞기보다 별도 workflow로 두고, same scenario, same seed, same artifact 원칙으로 비교하는 편입니다.

## Practical Tip

- 실제 적용 사례: `GET /api/rankings?eventType=WCA_333&page=1&size=25`처럼 시나리오를 고정하고, 같은 seed SQL과 같은 `k6` 스크립트, 같은 결과 리포트 형식을 유지한 뒤 전후 수치를 비교한다.
- 잘못 쓰면 생기는 문제: refactor 전후에 파라미터나 데이터셋이 바뀌면 더 빠른 숫자가 나와도 구조 개선 효과인지 실험 조건 차이인지 설명할 수 없다.
- 프로젝트 연결: JPA/QueryDSL/Redis 리팩터링을 비교할 때도 결국 핵심은 same scenario, same seed, same artifact다. 그래야 실행 계획 변화와 응답 시간 변화를 함께 읽을 수 있다.

## Related

- [2026-04-20 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-20%20-%20TIL.md)
- [SQL 성능과 조회 설계 기초](./SQL%20%EC%84%B1%EB%8A%A5%EA%B3%BC%20%EC%A1%B0%ED%9A%8C%20%EC%84%A4%EA%B3%84%20%EA%B8%B0%EC%B4%88.md)
- [데이터베이스 인덱스와 조회 성능 최적화](./%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%9D%B8%EB%8D%B1%EC%8A%A4%EC%99%80%20%EC%A1%B0%ED%9A%8C%20%EC%84%B1%EB%8A%A5%20%EC%B5%9C%EC%A0%81%ED%99%94.md)

## 예상 꼬리 질문

- baseline 계약에는 최소 무엇이 들어가야 하나요?
  - 답변: 측정 대상 API, 파라미터, 동시성/부하 시나리오, seed 데이터, 실패 허용 기준, 결과 artifact 형식은 최소한 고정해야 합니다.
- benchmark를 기능 CI와 분리하는 이유는 무엇인가요?
  - 답변: 기능 CI는 빠른 회귀 검출이 우선이지만, benchmark는 같은 조건 재현과 결과 보관이 더 중요합니다. 목적이 달라서 workflow를 분리하는 편이 해석이 쉽습니다.
- 캐시나 warm-up 상태는 어떻게 다뤄야 하나요?
  - 답변: 완전히 배제할 수 없다면 baseline 계약에 포함해 같은 방식으로 초기화하거나 warm-up 절차를 고정해야 합니다. 숨기지 않고 조건으로 명시하는 것이 더 중요합니다.

## Internal Links

- [[Areas/Career]]


---
note_type: weekly_review
status: active
created: 2026-04-19
updated: 2026-04-19
week: 2026-W16
month: 2026-04
tags: []
area:
project:
---

# Weekly Review

주차: 2026-W16

---

## 이번 주 성과

- 핵심 주제는 `Cubing Hub` 실연동/안정화, PKM 구조와 하네스 재정렬, AI 산출물 source of truth 정리였다.
- `Cubing Hub`에서는 인증 실연동, Day 16 기능 연동, Day 17/18 auth·validation·CSS 안정화까지 이어지며 기능 구현과 검증, 문서 동기화를 함께 닫았다.
- PKM에서는 `Dashboard`, `Weekly Dashboard`, `Templates/**`, `PKM Guide.md`, 루트 `AGENTS.md`를 현재 운영 흐름에 맞게 정렬해 노트 생성과 회고, 면접, 공개 링크 규칙의 기준선을 세웠다.
- `/Users/chiho/AI/**` 원본, `PKM/AI/Inbox/**` 수집함, `PKM/AI/<project>/**` 최종 보관소를 분리하는 기준을 고정해 자동화와 수동 검토의 역할을 더 명확히 했다.
- `CalmDesk`, `MediFlow`, `Feature Backlog`, `면접 대시보드` 같은 장기 참조 문서를 보강해 과거 프로젝트와 면접 자산을 다시 꺼내 쓰기 쉬운 형태로 승격했다.

---

## 공부 시간

월: 시간 미기록 - 인증 실연동, 테스트 구조 리뷰, 중간 점검
화: 시간 미기록 - docs facts-first sync, JaCoCo/품질 규칙 정리
수: 시간 미기록 - PKM 대량 이관, AI/자동화/하네스 정렬
목: 시간 미기록 - optional auth/보호 endpoint 기준 정리, CSS 구조 정리
금: 시간 미기록 - backlog/interview/archive 문서 승격 기준 정리
토: 시간 미기록 - Apple Journal 수집 기준 조사, canonical note 보강
일: 별도 작업 기록 없음

합계: 별도 시간 집계는 없었지만, 구현 안정화와 PKM 운영 기준 정리에 집중한 주였다.

---

## 운동

운동 횟수: 기록 없음

---

## 독서

읽은 책: 기록 없음

---

## 잘한 점

- 구현, 테스트, 문서, 하네스를 따로 밀어두지 않고 같은 흐름에서 함께 닫으려는 습관이 유지됐다.
- 큰 이관 작업에서도 바로 삭제나 병합으로 가지 않고, `canonical note`, `internal/public`, `inbox/final` 같은 역할 경계를 먼저 세운 점이 좋았다.
- 기능 목록보다 리스크와 source of truth를 먼저 보는 기준이 일관돼서 우선순위가 흔들리지 않았다.

---

## 문제점

- `Cubing Hub`의 랭킹/PB 정합성, 모바일 UX, 실사용 피드백 전달처럼 사용자 신뢰도에 직접 연결되는 후속 과제가 아직 남아 있다.
- 자동화와 `AI/Inbox` 규칙은 많이 정리했지만, 실제 예약 실행에서 중복 세션과 후보 노이즈가 충분히 줄었는지는 아직 관찰이 더 필요하다.

---

## 다음 주 전략

- `Cubing Hub`는 랭킹 V2, PB 정합성, 배포 전 스모크 체크처럼 제품 신뢰도를 좌우하는 항목을 먼저 완료한다.
- PKM은 구조를 또 넓히기보다 `Dashboard`, `Weekly Dashboard`, `QuickAdd`, 상대 링크, 자동화 결과를 실제 사용 기준으로 검증하고 필요한 최소 수정만 한다.

---

## 미완료 항목

- `Cubing Hub` 랭킹/PB 정합성 정리와 랭킹 V2 착수
- `AI/Inbox` 자동화와 단일 `cwd` 설정의 실제 중복 감소 여부 관찰
- DevQ&A 중복본 장기 정리 정책 확정

---

## 다시 봐야 할 노트

- [2026-04-16 - Daily Log](./2026-04-16%20-%20Daily%20Log.md)
- [2026-04-17 - TIL](./2026-04-17%20-%20TIL.md)
- [Feature Backlog](../../../../Archive/Projects/Cubing%20Hub/Docs/Feature%20Backlog.md)
- [면접 대시보드](../../../../Areas/Career/Interview%20Prep/%EB%A9%B4%EC%A0%91%20%EB%8C%80%EC%8B%9C%EB%B3%B4%EB%93%9C.md)
- [GitHub 링크가 NFD 파일명 때문에 깨지는 문제](../../../Troubleshooting/GitHub%20링크가%20NFD%20파일명%20때문에%20깨지는%20문제.md)

---
note_type: project_doc
status: active
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - backlog
area:
project:
---
# Feature Backlog

## 목적

- 개발 중 떠오른 후속 기능 후보를 한곳에 모아 관리한다.
- 아직 일정에 올리지 않은 제품/기능 아이디어만 기록한다.

## 운영 원칙

- 이 문서에는 제품/기능 후보를 우선 기록한다.
- 실제 일정에 올린 항목은 `Project Schedule.md`나 개발 로그로 승격한다.
- MVP 범위와 제외 범위의 기준선은 `Project Overview.md`에서 관리한다.
- 상태와 우선순위가 불명확하면 추측해서 채우지 말고 `candidate`, `미정`처럼 보수적으로 적는다.

## Backlog Summary

| Category | Count | Note |
| --- | --- | --- |
| Core Flow | 0 | 핵심 사용자 흐름 확장 |
| Data & Insights | 0 | 통계, 내보내기, 분석 |
| UX & Platform | 0 | 반응형, 접근성, 입력 방식 |
| Reference Notes | 0 | 비교 서비스, 벤치마크, 조사 메모 |

## [Category Name]

| Feature | Status | Priority | User Value | Related Docs | Notes |
| --- | --- | --- | --- | --- | --- |
|  | candidate | 미정 |  |  |  |

## [Another Category Name]

| Feature | Status | Priority | User Value | Related Docs | Notes |
| --- | --- | --- | --- | --- | --- |
|  | candidate | 미정 |  |  |  |

## Reference Notes

| Reference | Status | Why Review | Next Action |
| --- | --- | --- | --- |
|  | note |  |  |

## 승격 규칙

- backlog 항목이 실제 구현 범위로 확정되면 상태를 갱신하거나 일정 문서로 옮긴다.
- 구현 완료 이후에는 backlog에서 삭제하기보다, 완료 여부를 남길지 개발 로그에 위임할지 프로젝트 운영 방식에 맞춰 정한다.
- 상위 수준 범위 설명만 필요한 내용은 `Project Overview.md`에 남기고, 상세 후보는 이 문서에서 계속 관리한다.

## Internal Links

- [[Projects/Project Name/Project Name]]
- [[Projects/Project Name/Docs/Project Overview]]
- [[Projects/Project Name/Docs/Project Schedule]]

---
note_type: project_index
status: active
created: 2026-04-16
updated: 2026-04-17
tags:
  - project-index
area:
project: "Cubing Hub"
---

# Cubing Hub

## Summary

- 활성 개발 중인 `Cubing Hub` 프로젝트 문서 허브
- 설계 문서, 개발 로그, 프로젝트 트러블슈팅, 내부 문서를 여기서 함께 관리

## Quick Links

- [Project README](./README.md)
- [Dev Log Index](./Docs/dev-log.md)
- [Project Overview](./Docs/Project%20Overview.md)
- [System Architecture](./Docs/System%20Architecture.md)
- [API Specification](./Docs/API%20Specification.md)
- [Internal Schedule](./Docs/Internal%20Schedule.internal.md)

## Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Projects/Cubing Hub/Docs"
WHERE !contains(file.path, "/Docs/ai/") AND !contains(file.path, ".internal.md")
SORT updated DESC
```

## Development Log

```dataview
TABLE WITHOUT ID file.link AS "Log", updated AS "Updated"
FROM "Projects/Cubing Hub/Log/Development Log"
SORT updated DESC
```

## Troubleshooting

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Projects/Cubing Hub/Troubleshooting"
SORT updated DESC
```

## Internal Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Projects/Cubing Hub/Docs"
WHERE contains(file.path, ".internal.md")
SORT updated DESC
LIMIT 20
```

## Internal Links

- [Dashboard](../../Dashboard.md)
- [[CodingTests Dashboard]]

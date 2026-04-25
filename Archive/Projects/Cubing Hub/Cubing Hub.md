---
note_type: project_index
status: archived
created: 2026-04-16
updated: 2026-04-24
tags:
  - project-index
area:
project: "Cubing Hub"
---

# Cubing Hub

## Summary

- 종료된 `Cubing Hub` 프로젝트의 보관 문서 허브
- 최신 큐빙허브 repo 동기화 문서, 개발 로그, 성능 문서, 내부 문서를 여기서 함께 보관

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
FROM "Archive/Projects/Cubing Hub/Docs"
WHERE !contains(file.path, "/Docs/Development Log/")
  AND !contains(file.path, "/Docs/Trouble Shooting/")
  AND !contains(file.path, "/Docs/performance/")
  AND !contains(file.path, "/Docs/ai/")
  AND !contains(file.path, ".internal.md")
SORT updated DESC
```

## Development Log

```dataview
TABLE WITHOUT ID file.link AS "Log", updated AS "Updated"
FROM "Archive/Projects/Cubing Hub/Docs/Development Log"
SORT updated DESC
```

## Troubleshooting

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/Cubing Hub/Docs/Trouble Shooting"
SORT updated DESC
```

## Performance Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/Cubing Hub/Docs/performance"
SORT updated DESC
```

## Internal Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/Cubing Hub/Docs"
WHERE contains(file.path, ".internal.md")
SORT updated DESC
LIMIT 20
```

## Internal Links

- [Dashboard](../../../Dashboard.md)
- [[CodingTests Dashboard]]

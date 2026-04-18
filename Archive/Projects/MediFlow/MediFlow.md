---
note_type: project_index
status: archived
created: 2026-04-16
updated: 2026-04-18
tags:
  - project-index
area:
project: "MediFlow"
---

# MediFlow

## Summary

- KDT 세미 프로젝트 `MediFlow`의 보관 문서 허브
- README와 회고 문서에 흩어진 내용을 프로젝트 개요/아키텍처/포트폴리오 설명 문서로 다시 정리

## Quick Links

- [Project Overview](./Docs/Project%20Overview.md)
- [System Architecture](./Docs/System%20Architecture.md)
- [Portfolio Notes](./Docs/portfolio.internal.md)
- [Project Feedback](./Log/세미%20프로젝트%20피드백.md)

## Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/MediFlow/Docs"
WHERE !contains(file.path, ".internal.md")
SORT updated DESC
```

## Internal Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/MediFlow/Docs"
WHERE contains(file.path, ".internal.md")
SORT updated DESC
```

## Logs

```dataview
TABLE WITHOUT ID file.link AS "Log", default(note_type, "-") AS "Type", updated AS "Updated"
FROM "Archive/Projects/MediFlow/Log"
SORT updated DESC
```

## Internal Links

- [[Areas/Career]]
- [[Dashboard]]

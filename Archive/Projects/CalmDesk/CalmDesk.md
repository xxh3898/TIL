---
note_type: project_index
status: archived
created: 2026-04-16
updated: 2026-04-18
tags:
  - project-index
area:
project: "CalmDesk"
---

# CalmDesk

## Summary

- KDT 파이널 프로젝트 `CalmDesk`의 보관 문서 허브
- 기존 회의록과 이슈 로그에 더해, 프로젝트 개요/아키텍처/포트폴리오 설명용 문서를 함께 관리

## Quick Links

- [Project README](./Docs/README.md)
- [Project Overview](./Docs/Project%20Overview.md)
- [System Architecture](./Docs/System%20Architecture.md)
- [API Specification](./Docs/API%20명세서.md)
- [MyPage Guide](./Docs/MYPAGE-GUIDE.md)
- [Portfolio Notes](./Docs/portfolio.internal.md)
- [Project Plan](./Log/파이널%20프로젝트%20수행계획서.md)
- [Planning Q&A](./Log/기획%20발표%20질문.md)

## Key Issues

- [API 403 Forbidden 오류 해결](./Log/이슈/API%20403%20Forbidden%20오류%20해결.md)
- [백엔드 CD 배포 및 대시보드 성능 최적화](./Log/이슈/백엔드%20CD%20배포%20및%20대시보드%20성능%20최적화.md)
- [프론트 딜레이 해결](./Log/이슈/프론트%20딜레이%20해결.md)

## Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Docs"
WHERE !contains(file.path, ".internal.md")
SORT updated DESC
```

## Internal Docs

```dataview
TABLE WITHOUT ID file.link AS "Doc", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Docs"
WHERE contains(file.path, ".internal.md")
SORT updated DESC
```

## Issues

```dataview
TABLE WITHOUT ID file.link AS "Issue", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Log/이슈"
SORT updated DESC
```

## Weekly Meetings

```dataview
TABLE WITHOUT ID file.link AS "Meeting", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Log/주간 회의록"
SORT updated DESC
```

## Daily Meetings

```dataview
TABLE WITHOUT ID file.link AS "Meeting", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Log/데일리 회의록"
SORT updated DESC
LIMIT 10
```

## Daily Scrum

```dataview
TABLE WITHOUT ID file.link AS "Scrum", updated AS "Updated"
FROM "Archive/Projects/CalmDesk/Log/데일리 스크럼"
SORT updated DESC
LIMIT 10
```

## Internal Links

- [[Areas/Career]]
- [Dashboard](../../../Dashboard.md)

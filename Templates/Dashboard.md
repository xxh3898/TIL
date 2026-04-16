---
note_type: dashboard
status: active
dashboard_type: home
scope: pkm
created: <% tp.date.now("YYYY-MM-DD") %>
updated: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - dashboard
area: "[[Areas/PKM]]"
project:
---

# Dashboard

## 오늘 Daily Log

```dataview
TABLE WITHOUT ID file.link AS "Daily Log"
FROM "TIL/Retrospectives"
WHERE note_type = "daily_log" AND date = date(today)
SORT updated DESC
LIMIT 1
```

## 최근 TIL

```dataview
TABLE WITHOUT ID file.link AS "TIL", date AS "Date", updated AS "Updated"
FROM "TIL/Retrospectives"
WHERE note_type = "til"
SORT updated DESC
LIMIT 7
```

## 최근 DevQ&A

```dataview
TABLE WITHOUT ID file.link AS "Note", default(question, file.name) AS "Question", default(domain, "-") AS "Domain", default(interview_priority, "-") AS "Priority", updated AS "Updated"
FROM "TIL/DevQ&A"
SORT updated DESC
LIMIT 10
```

## 최근 Troubleshooting

```dataview
TABLE WITHOUT ID file.link AS "Issue", default(system, "-") AS "System", created AS "Created"
FROM "TIL/Troubleshooting"
SORT created DESC
LIMIT 10
```

## 최근 Retrospectives

```dataview
TABLE WITHOUT ID file.link AS "Retrospective", updated AS "Updated"
FROM "TIL/Retrospectives"
WHERE note_type = "retrospective_legacy"
SORT updated DESC
LIMIT 10
```

## 최근 수정된 핵심 노트

```dataview
TABLE WITHOUT ID file.link AS "Note", default(note_type, "legacy") AS "Type", updated AS "Updated"
FROM "TIL"
WHERE !contains(file.path, "TIL/Retrospectives/")
SORT updated DESC
LIMIT 10
```

## Project Note

```dataview
TABLE WITHOUT ID
  file.link AS "Project",
  choice(contains(file.path, "Archive/Projects/"), "archive", "active") AS "Type",
  choice(contains(file.path, "Archive/Projects/"), "-", default(project_stage, "-")) AS "Stage",
  updated AS "Updated"
FROM ""
WHERE
  (contains(file.path, "Projects/") AND note_type = "project_index" AND status = "active")
  OR
  (contains(file.path, "Archive/Projects/") AND note_type = "project_index")
SORT contains(file.path, "Archive/Projects/") ASC, updated DESC
LIMIT 20
```

## Today Tasks

```tasks
not done
path does not include Templates
due on or before today
sort by priority
sort by due
limit 10
```

## Internal Links

- [[CodingTests Dashboard]]
- [[Weekly Dashboard]]
- [[Areas/PKM]]
- [[PKM Guide]]

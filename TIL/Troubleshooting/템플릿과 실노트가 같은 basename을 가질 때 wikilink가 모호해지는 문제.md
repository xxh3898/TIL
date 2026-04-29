---
note_type: troubleshooting
status: active
created: 2026-04-22
updated: 2026-04-22
issue: ambiguous-wikilink-from-shared-basename
system: obsidian + markdown + static-link-audit
symptom: short-wikilink-resolves-ambiguously
root_cause: template-and-live-note-share-basename
tags: []
area: "[[Areas/PKM]]"
project:
source_ref: pkm-link-audit, pkm-link-fixes
---

# 템플릿과 실노트가 같은 basename을 가질 때 wikilink가 모호해지는 문제

## Summary

- Obsidian에서는 대충 열리지만, 정적 감사나 다른 렌더러 기준으로 짧은 wikilink가 ambiguous 판정을 받을 수 있다.
- 이번 사례의 직접 원인은 `Dashboard.md`와 `Templates/Dashboard.md`가 같은 basename을 공유한 점이었다.
- 해결은 의도 대상이 고정되도록 explicit relative Markdown link를 쓰거나, 애초에 basename 충돌을 피하는 것이다.

## Environment

- Obsidian vault
- Markdown relative links
- Wikilink static audit

## Symptom

- [Dashboard](../../Dashboard.md)처럼 짧은 wikilink가 정적 링크 감사에서 ambiguous로 잡힌다.
- Obsidian 안에서는 열려도, 다른 도구나 나중의 자동 검증에서는 의도 대상을 확정하지 못한다.
- 템플릿과 실노트가 같은 이름을 공유할수록 이런 모호성이 반복된다.

## Reproduction

1. 실사용 노트와 템플릿 노트가 같은 basename을 갖게 둔다.
2. 다른 문서에서 짧은 wikilink로 basename만 적는다.
3. 정적 도구나 비-Obsidian 해석기에서는 target이 하나로 고정되지 않아 ambiguous 판정이 난다.

## Expected

- 같은 링크가 Obsidian 안팎에서 같은 파일 하나로 안정적으로 해석된다.

## Actual

- 짧은 wikilink는 사람 눈에는 자연스럽지만, basename 충돌이 있으면 도구마다 다르게 해석되거나 모호성 경고가 발생한다.

## Root Cause

- Obsidian은 짧은 wikilink를 유연하게 처리하지만, basename이 겹치면 정적 해석 기준은 흔들린다.
- 이번 PKM 볼트에서는 `Dashboard.md`와 `Templates/Dashboard.md`가 같은 basename을 가져 [Dashboard](../../Dashboard.md) 링크 17건이 모호해졌다.
- 즉 문제의 핵심은 링크 문법 자체보다 "동일 basename을 가진 두 노드가 공존하는 구조"였다.

## Fix

- 문제 링크를 루트 `Dashboard.md`를 가리키는 explicit relative Markdown link로 치환했다.
- 템플릿과 실노트가 같은 이름을 가져야 한다면, 공개 문서에서는 짧은 wikilink 대신 상대 Markdown link를 우선한다.
- 가능하면 템플릿 파일명 단계에서 basename 충돌을 피한다.

## Verification

- [Dashboard](../../Dashboard.md) occurrence 17건을 모두 치환했다.
- 변경 후 새 `[Dashboard](...)` 링크 17건이 모두 루트 `Dashboard.md`로 정상 해석되는 것을 확인했다.
- `rg -n '\\[\\[Dashboard\\]\\]' --glob '*.md'` 결과가 비어 모호한 짧은 링크가 남지 않음을 확인했다.

## Prevention

- 템플릿과 실노트가 같은 basename을 공유하면 짧은 wikilink 사용을 기본값으로 두지 않는다.
- 링크 감사에서 ambiguous가 나오면 링크 문자열만 볼 게 아니라 basename 충돌 구조를 먼저 확인한다.
- 공개 범위 문서에서는 도구 간 해석이 흔들리지 않도록 explicit relative Markdown link를 우선 검토한다.

## Related

- [PKM Guide](../../PKM%20Guide.md)
- [2026-04-19 - Daily Log](../Retrospectives/2026/04%EC%9B%94/2026-04-19%20-%20Daily%20Log.md)

## Internal Links

- [[Areas/PKM]]
- [[AI/pkm/20260419/04-pkm-link-audit/review-pkm-link-audit]]
- [[AI/pkm/20260419/05-pkm-link-fixes/review-pkm-link-fixes]]

---
note_type: dev_qa
status: seed
created: 2026-04-28
updated: 2026-04-28
question: "모바일 반응형 회귀를 디버깅할 때 왜 source CSS보다 빌드 산출물의 media query 문법을 먼저 확인해야 하는가"
domain: frontend
related_topics:
  - css
  - vite
  - browser-compatibility
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-25 - TIL.md"
  - "AI/Inbox/cubing-hub/20260425/04-mobile-safari-media-query/review-mobile-safari-media-query.md"
tags:
  - frontend
  - css
  - vite
  - devqa
area: "[[Areas/Career]]"
project:
---

# 모바일 반응형 회귀를 디버깅할 때 왜 source CSS보다 빌드 산출물의 media query 문법을 먼저 확인해야 하는가

## Why

- 프런트 배포본은 source CSS를 그대로 내보내지 않는다.
- 번들러와 CSS optimizer가 target에 따라 문법을 재작성할 수 있어서, 소스가 맞아도 실제 배포본은 다른 규칙으로 동작할 수 있다.
- 면접에서도 "CSS를 고쳤다"보다 "빌드 출력 계약이 문제였는지 먼저 확인했다"를 설명할 수 있어야 디버깅 순서가 설득력 있다.

## Core Concept

- 반응형 회귀는 컴포넌트 CSS 자체보다 빌드 산출물의 문법 변환에서 시작될 수 있다. 예를 들어 source에는 `@media (max-width: 720px)`가 있어도 배포본은 `@media (width<=720px)`처럼 더 최신 문법으로 바뀔 수 있다.
- 문제는 최신 문법이 항상 안전한 것은 아니라는 점이다. 구형 iOS Safari나 오래된 Android 브라우저는 Media Queries Level 4 range syntax를 제대로 처리하지 못해 media query 전체를 무시할 수 있다.
- 그래서 모바일 회귀를 볼 때는 JSX나 레이아웃을 바로 다시 쓰기보다, 실제 배포 CSS에서 어떤 문법이 나갔는지 먼저 확인하는 편이 더 빠르고 정확하다.
- 원인이 빌드 출력이라면 수정도 소스 재작성보다 `build.cssTarget` 같은 target 조정이 더 작고 안전할 수 있다.

## Interview Answer Version

- 30~40초 답변: 모바일 반응형 회귀를 볼 때는 source CSS보다 먼저 실제 빌드된 CSS를 확인하는 편이 좋습니다. Vite나 Lightning CSS 같은 도구가 target에 따라 media query 문법을 더 최신 형태로 바꿀 수 있어서, 소스에 `max-width`가 있어도 배포본에서는 `width<=...` 같은 문법으로 나갈 수 있기 때문입니다. 구형 Safari나 Android는 이 문법을 무시할 수 있으니, 저는 레이아웃 코드를 다시 쓰기 전에 배포 CSS와 `cssTarget`부터 확인합니다. 원인이 빌드 출력이면 target만 조정하는 것이 가장 작은 수정입니다.

## Practical Tip

- 실제 적용 사례: `@media (max-width: 720px)`가 source에는 정상인데 배포 CSS가 `@media (width<=720px)`로 축약된 것을 확인하고, `build.cssTarget`을 보수화해 다시 `max-width` 문법으로 출력되게 맞춘다.
- 잘못 쓰면 생기는 문제: source CSS만 보고 "규칙이 있으니 브라우저 버그겠지"라고 판단하면, 실제로는 빌드 출력 문법이 구형 브라우저에서 통째로 무시되는 문제를 놓치게 된다.
- 프로젝트 연결: 커뮤니티 목록, 카드형 테이블, 모바일 내비게이션처럼 media query 의존도가 높은 화면은 `source 확인 -> built CSS 확인 -> target 조정` 순서 체크리스트를 재사용하는 편이 좋다.

## Related

- [2026-04-25 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-25%20-%20TIL.md)
- [2026-04-25 - Daily Log](../Retrospectives/2026/04%EC%9B%94/2026-04-25%20-%20Daily%20Log.md)
- [2026-W17 - Weekly Review](../Retrospectives/2026/04%EC%9B%94/2026-W17%20-%20Weekly%20Review.md)

## 예상 꼬리 질문

- source CSS가 맞는데도 배포본이 달라질 수 있나요?
  - 답변: 가능합니다. 번들러, minifier, CSS optimizer가 target에 맞춰 문법을 재작성하거나 축약할 수 있기 때문입니다.
- `cssTarget`을 낮추면 무조건 좋은가요?
  - 답변: 아닙니다. 출력이 더 보수적으로 바뀌고 크기가 조금 늘 수 있습니다. 다만 실제 지원해야 할 브라우저 범위가 분명하다면 그 범위에 맞춰 조정하는 편이 낫습니다.
- 왜 JSX나 CSS 구조를 먼저 안 바꾸나요?
  - 답변: 원인이 소스 구조가 아니라 배포 문법이면 구조를 다시 써도 같은 문제가 반복됩니다. 먼저 산출물을 확인해야 수정 범위를 최소화할 수 있습니다.

## Internal Links

- [[Areas/Career]]
- [[Archive/Projects/Cubing Hub/Cubing Hub]]

---
note_type: troubleshooting
status: active
created: 2026-04-16
updated: 2026-04-16
issue: github-markdown-link-breakage
system: github + obsidian + macos
symptom: markdown-relative-links-break-on-github
root_cause: unicode-normalization-nfd-vs-nfc
tags: []
area: "[[Areas/PKM]]"
project:
source_ref: public-unicode-normalization, comma-link-compatibility
---

# GitHub 링크가 NFD 파일명 때문에 깨지는 문제

## Summary

- Obsidian에서는 열리는데 GitHub 웹에서는 일부 한글 파일 링크가 깨졌다.
- 초기 원인은 공개 범위 파일/폴더명이 `NFD`와 `NFC`로 섞여 있던 점이었다.
- 추가로 일부 링크에서 쉼표를 `%2C`로 인코딩하면 GitHub는 열리지만 Obsidian에서는 로컬 파일 경로를 제대로 해석하지 못했다.
- 최종 해결은 공개 범위 파일/폴더명을 `NFC`로 통일하고, 상대 Markdown 링크에서 공백만 `%20`으로 인코딩하고 쉼표는 raw 문자 `,`로 유지하는 방식으로 정리했다.

## Environment

- macOS
- Obsidian vault
- GitHub repository rendering
- 공개 범위: `README.md`, `TIL/**`, `Templates/**`

## Symptom

- 같은 파일처럼 보이는데 GitHub에서 링크를 눌렀을 때 404처럼 동작했다.
- 링크 문자열 안의 한글이 자모 단위로 분해된 `NFD` 경로와 일반 `NFC` 경로가 섞여 있었다.
- 일부 링크는 쉼표를 `%2C`로 인코딩해 GitHub에서는 열리지만 Obsidian에서는 실패했다.
- 즉 같은 링크라도 GitHub와 Obsidian이 다르게 성공/실패하는 사례가 생겼다.

## Reproduction

1. 공개 범위 노트 안에 상대 Markdown 링크를 넣는다.
2. 링크 대상 파일명이 한글이고, 실제 디스크 경로가 `NFD` 상태로 저장돼 있다.
3. 같은 링크를 GitHub 웹에서 누르면 깨지는 경우가 발생한다.

## Expected

- Obsidian과 GitHub에서 같은 상대경로 Markdown 링크가 동일 파일을 정상적으로 연다.

## Actual

- Obsidian에서는 열리지만 GitHub 웹에서는 같은 경로를 다른 파일처럼 취급해 링크가 깨졌다.

## Root Cause

- 공개 범위 파일/폴더 112개 중 100개가 `NFD` 경로였다.
- 링크는 상대경로 + percent-encoding으로 써도, 실제 대상 파일명이 `NFD`면 GitHub 웹의 경로 해석과 어긋날 수 있다.
- 여기에 쉼표를 `%2C`로 인코딩한 링크는 GitHub에서는 열리지만 Obsidian에서는 로컬 파일명을 그대로 찾지 못했다.
- 즉 이 이슈는 파일/폴더명 Unicode 정규화 혼재와 쉼표 인코딩 규칙이 함께 겹친 복합 문제였다.

## Fix

- 공개 범위 파일/폴더 104개를 `NFC` 기준으로 rename했다.
- 공개 Markdown 링크를 실제 경로 기준으로 다시 계산했다.
- 링크 규칙은 `상대경로 유지 + 파일명 NFC + 공백은 %20 + 쉼표는 raw 문자 ,`로 조정했다.
- 괄호가 있는 파일명과 확장자 누락 링크는 수동으로 추가 보정했다.
- `%2C`가 들어 있던 공개 링크 11개는 raw comma로 다시 정리했다.

## Verification

- 공개 범위 전체를 다시 스캔했을 때 `NFD` 경로는 0개였다.
- `Templater` placeholder를 제외한 공개 범위 로컬 Markdown 링크 97개를 검사했고, 깨진 링크는 0개였다.
- `%2C`가 남아 있는 공개 링크를 다시 검색했을 때 결과가 0개였다.
- 실제 문제 링크는 raw comma로 바꾼 뒤 Obsidian에서 정상 동작했다.

## Prevention

- 공개 범위(`README.md`, `TIL/**`, `Templates/**`)의 파일/폴더명은 항상 `NFC`를 유지한다.
- 공개 범위를 수정한 뒤에는 `NFD` 경로 잔존 여부와 상대 Markdown 링크 해석을 같이 확인한다.
- 공개 범위 상대 링크는 공백만 `%20`으로 인코딩하고, 쉼표는 `%2C` 대신 raw 문자 `,`로 유지한다.
- 새 문제를 기록할 때는 `Troubleshooting` 템플릿을 사용해 증상, 원인, 해결, 검증을 같이 남긴다.

## Related

- [README](../../README.md)

## Internal Links

- [[Areas/PKM]]
- [[AI/20260416/05-public-unicode-normalization/review-public-unicode-normalization]]
- [[AI/20260416/07-comma-link-compatibility/review-comma-link-compatibility]]

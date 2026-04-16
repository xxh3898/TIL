# PKM

개인 PKM(Personal Knowledge Management) 저장소다.  
백엔드 개발 학습, 프로젝트 문서, 회고, 템플릿을 한 곳에서 관리하고, GitHub에는 공개 가능한 범위만 선별해서 추적한다.

## 이 저장소에서 다루는 것

- Java / Spring Boot 중심 학습 기록
- DevQ&A, Troubleshooting, Study Notes 같은 재사용 가능한 노트
- 프로젝트 문서와 개발 로그
- 회고와 템플릿

## 공개 추적 범위

이 저장소는 로컬 PKM 전체를 그대로 올리지 않고, 아래 범위만 공개 추적한다.

- `TIL/**`
- `Templates/**`
- `Projects/**`
- `Archive/Projects/**`
- 루트 `README.md`

반대로 아래는 로컬 전용 또는 비공개 범위라 기본적으로 추적하지 않는다.

- `AI/**`
- `Areas/**`
- `Resources/**`
- `Archive/**` 중 `Archive/Projects/**`를 제외한 나머지
- `Assets/**`
- `Inbox/**`
- `.obsidian/**`
- `CodingTests/**`
- `Projects/Portfolio/**`
- `*.internal.md`

## 현재 로컬 볼트 구조

```text
PKM/
├── AI/           # 작업 산출물
├── Archive/      # 종료된 프로젝트와 보관 문서
├── Areas/        # 지속 관리 영역
├── Assets/       # 이미지, PDF, 첨부 파일
├── CodingTests/  # 별도 nested repo 성격의 코딩테스트 저장소
├── Inbox/        # 미분류 메모
├── Projects/     # 진행 중인 프로젝트 문서
├── Resources/    # 책, 레퍼런스 자료
├── TIL/          # 학습, 회고, 트러블슈팅
└── Templates/    # 재사용 템플릿
```

로컬 사용 기준의 자세한 분류 규칙은 로컬 전용 내부 가이드 `PKM Guide.md`에서 관리한다.

## 공개 문서에서 주로 보게 되는 영역

### `TIL`

- `DevQ&A`
  - 면접/실무 대비 질문과 답변 정리
- `Troubleshooting`
  - 문제 원인과 해결 과정을 남긴 트러블슈팅 노트
- `Study Notes`
  - 개념을 재사용 가능한 형태로 정리한 학습 노트
- `Retrospectives`
  - 일간/주간 회고와 레거시 학습 기록

### `Projects`

- 현재는 `Cubing Hub` 프로젝트 문서를 공개 추적한다.
- 설계 문서, 개발 로그, 트러블슈팅, 프로젝트 인덱스를 포함한다.

주요 진입 문서:

- [Cubing Hub Index](./Projects/Cubing%20Hub/Cubing%20Hub.md)
- [Cubing Hub README](./Projects/Cubing%20Hub/README.md)

### `Archive/Projects`

- 완료된 프로젝트 문서와 회고성 기록을 공개 추적한다.
- 현재는 `CalmDesk`, `MediFlow`, `React 개인 프로젝트` 아카이브가 포함된다.

주요 진입 문서:

- [CalmDesk Archive](./Archive/Projects/CalmDesk/CalmDesk.md)
- [MediFlow Archive](./Archive/Projects/MediFlow/MediFlow.md)

### `Templates`

- `Daily Log`, `TIL`, `DevQ&A`, `Troubleshooting`, `Weekly Review`, `Coding Test Note`
- 프로젝트 문서용 `Project Docs/**`

대표 템플릿:

- [DevQ&A Template](./Templates/DevQ&A.md)
- [Troubleshooting Template](./Templates/Troubleshooting.md)
- [Weekly Review Template](./Templates/Weekly%20Review.md)
- [Project Overview Template](./Templates/Project%20Docs/Project%20Overview.md)

## 로컬 운영 흐름

로컬 Obsidian 볼트에서는 아래 흐름으로 사용한다.

- 메인 진입점: `Dashboard.md`
- 보조 진입점: `Weekly Dashboard.md`, `CodingTests Dashboard.md`
- 생성 흐름: `QuickAdd` + `Templates/**`
- 구조 가이드: 로컬 전용 내부 문서 `PKM Guide.md`

이 대시보드, `PKM Guide.md`, 플러그인 설정은 로컬 사용 기준이며, GitHub에는 기본적으로 추적하지 않는다.

## 운영 원칙

- 공개 문서는 상대 Markdown 링크를 기본으로 사용한다.
- 공개 범위 파일명은 Unicode `NFC`를 유지한다.
- 내부 메모는 `.internal.md`로 구분하고 공개 추적에서 제외한다.
- 자동 생성 산출물과 사람이 직접 정리한 노트를 구분한다.
- 허브 문서와 핵심 문서만 자연스럽게 연결하고, 사실 기록성 로그에는 억지 링크를 늘리지 않는다.

## 참고

- 코딩테스트 학습은 로컬 볼트 안 `CodingTests/` nested repo에서 관리한다.
- 포트폴리오 정적 사이트 원본은 로컬 `Projects/Portfolio/`에 보존하지만, PKM 메인 공개 추적에서는 제외한다.

# PKM

개인 개발 PKM(Personal Knowledge Management) 저장소입니다.  
백엔드 학습 기록, 프로젝트 문서, 회고, 템플릿을 한곳에서 관리하며, 그중 공개 가능한 문서만 GitHub에서 추적합니다.

## 이 저장소에서 볼 수 있는 것

- Java / Spring Boot 중심의 학습 기록을 담고 있습니다.
- DevQ&A, Troubleshooting, Study Notes 등 필요할 때 다시 꺼내 볼 지식 노트를 정리합니다.
- 진행 중인 프로젝트의 주요 설계 문서와 개발 로그를 공개합니다.
- 완료된 프로젝트 관련 문서는 아카이브 형태로 보관합니다.
- 일상적인 기록 작성을 돕는 기반 템플릿을 관리합니다.

## 먼저 읽어볼 문서

- [TIL](./TIL/)
- [Cubing Hub Index](./Projects/Cubing%20Hub/Cubing%20Hub.md)
- [Cubing Hub README](./Projects/Cubing%20Hub/README.md)
- [CalmDesk Archive](./Archive/Projects/CalmDesk/CalmDesk.md)
- [MediFlow Archive](./Archive/Projects/MediFlow/MediFlow.md)
- [DevQ&A Template](./Templates/DevQ&A.md)
- [Troubleshooting Template](./Templates/Troubleshooting.md)

## 공개 문서 구성

### `TIL`

- `DevQ&A`: 면접 대비 및 실무 맥락의 주요 질문 정리
- `Troubleshooting`: 실제 부딪힌 문제의 원인과 해결 과정 기록
- `Study Notes`: 여러 상황에서 재사용 가능한 핵심 개념 보관
- `Retrospectives`: 일간/주간 회고 및 레거시 학습 기록 보관

### `Projects`

- 현재는 `Cubing Hub` 프로젝트 문서를 중심으로 공개 추적
- 프로젝트 인덱스, 주요 설계 문서, 개발 로그, 트러블슈팅 등 포함

### `Archive/Projects`

- 완료된 프로젝트 리소스 및 회고성 기록 보관소
- 현재 `CalmDesk`, `MediFlow`, `React 개인 프로젝트` 포함

### `Templates`

- **개인 기록 베이스**: `Daily Log`, `TIL`, `Weekly Review`
- **학습/문제 해결**: `DevQ&A`, `Troubleshooting`, `Coding Test Note`
- **프로젝트/이슈**: `Project Docs/**`, `Git/이슈 해결 리포트 템플릿`

## 공개 범위

이 저장소는 로컬 PKM 전체를 그대로 공개하지 않고, 외부에 공유해도 되는 문서만 선별해 추적합니다.
내부 운영 문서, 개인 영역, Obsidian 설정, 비공개 자료는 제외합니다.

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

분류 규칙과 로컬 운영 기준은 로컬 전용 내부 가이드 `PKM Guide.md`에서 관리합니다.

## 로컬 볼트(Vault) 운영 방식

- 메인 진입점은 `Dashboard.md`
- 보조 진입점은 `Weekly Dashboard.md`, `CodingTests Dashboard.md`
- 문서 생성 흐름은 `QuickAdd` + `Templates/**`
- 대시보드, 내부 가이드, Obsidian 설정은 로컬 사용 기준이며 GitHub에는 기본적으로 추적하지 않습니다.

## 공개 저장소 관리 원칙

- 공개 문서는 상대 Markdown 링크를 기본으로 사용합니다.
- 공개 범위 파일명은 Unicode `NFC`를 유지합니다.
- 내부 메모는 `.internal.md`로 구분하고 공개 추적에서 제외합니다.
- 자동 생성 산출물과 사람이 직접 정리한 노트를 구분합니다.
- 허브 문서와 핵심 문서 중심으로 연결하고, 사실 기록성 로그에는 과한 링크를 늘리지 않습니다.

## 참고

- 코딩테스트 학습은 로컬 볼트 안 `CodingTests/` nested repo에서 관리합니다.
- 포트폴리오 정적 사이트 원본은 로컬 `Projects/Portfolio/`에 보존하지만, PKM 메인 공개 추적에서는 제외합니다.

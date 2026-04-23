---
note_type: dev_qa
status: seed
created: 2026-04-23
updated: 2026-04-23
question: "Apple Silicon 로컬 빌드 이미지를 amd64 EC2에 배포할 때 release 계약으로 무엇을 먼저 보장해야 하는가"
domain: infra
related_topics:
  - docker
  - aws
  - release
interview_priority: high
source_ref:
  - "TIL/Retrospectives/2026/04월/2026-04-21 - TIL.md"
tags:
  - docker
  - aws
  - devqa
area: "[[Areas/Career]]"
project:
---

# Apple Silicon 로컬 빌드 이미지를 amd64 EC2에 배포할 때 release 계약으로 무엇을 먼저 보장해야 하는가

## Why

- 로컬 `docker build` 성공과 운영 서버 `docker pull` 성공은 같은 검증이 아니다.
- Apple Silicon에서는 기본적으로 `arm64` 이미지를 만들 수 있기 때문에, 배포 대상 서버 아키텍처와 manifest가 맞지 않으면 compose와 env가 모두 맞아도 실제 배포는 실패한다.
- 면접에서도 Docker 일반론보다 "왜 로컬에서 되는데 EC2에서는 pull이 안 되는가"를 설명할 수 있어야 운영 감각이 드러난다.

## Core Concept

- release 계약에서 먼저 보장해야 하는 것은 이미지가 대상 서버 아키텍처를 실제로 포함하고 있다는 점이다. `amd64` EC2에 배포한다면 registry manifest에 `linux/amd64` 또는 multi-arch 지원이 있어야 한다.
- Apple Silicon 맥에서 기본 `docker build`만 하면 `arm64` 단일 이미지가 올라갈 수 있다. 이 상태에서는 서버의 compose 파일, 인증서, DB 연결 정보가 모두 맞아도 `pull` 단계에서 막힌다.
- 그래서 배포 체크리스트는 "이미지를 만들었다"가 아니라 "대상 아키텍처 manifest를 포함한 release를 만들었다"여야 한다. 필요하면 `buildx`로 multi-arch 이미지를 만들고, 배포 전 manifest 확인을 별도 단계로 둔다.
- 핵심은 Docker 배포를 설정 파일 문제로만 보지 않는 것이다. 이미지 아키텍처는 실행 환경과 직접 맞물리는 release 계약이다.

## Interview Answer Version

- 30~40초 답변: Apple Silicon에서 만든 이미지를 `amd64` EC2에 배포할 때는 compose나 env보다 먼저 이미지 manifest를 봐야 합니다. 맥에서 기본 빌드를 하면 `arm64` 이미지가 올라갈 수 있어서, 서버 아키텍처인 `linux/amd64`가 registry manifest에 없으면 `docker pull` 단계에서 바로 막힙니다. 그래서 저는 release 계약에 대상 서버 아키텍처 포함 여부를 먼저 넣고, 필요하면 `buildx` 기반 multi-arch 이미지를 만들도록 합니다. 즉 로컬 빌드 성공보다 대상 환경에서 실행 가능한 artifact인지가 먼저라고 봅니다.

## Practical Tip

- 실제 적용 사례: 배포 전 `docker manifest inspect`나 registry UI로 `linux/amd64` 포함 여부를 확인하고, Apple Silicon 개발 환경에서는 `buildx` 기반 multi-arch release를 기본값으로 둔다.
- 잘못 쓰면 생기는 문제: compose 파일, Nginx 설정, RDS 연결까지 다 맞췄다고 생각해도 실제 서버에서는 `no matching manifest for linux/amd64`로 배포가 멈춘다.
- 프로젝트 연결: 개인 프로젝트라도 로컬 개발 머신과 운영 서버 아키텍처가 다르면 release artifact 검증을 빌드 성공과 분리해서 봐야 한다.

## Related

- [2026-04-21 - TIL](../Retrospectives/2026/04%EC%9B%94/2026-04-21%20-%20TIL.md)
- [Docker 기반 배포 구조와 운영 흐름](./Docker%20%EA%B8%B0%EB%B0%98%20%EB%B0%B0%ED%8F%AC%20%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9A%B4%EC%98%81%20%ED%9D%90%EB%A6%84.md)
- [AWS 백엔드 배포 구조 이해](./AWS%20%EB%B0%B1%EC%97%94%EB%93%9C%20%EB%B0%B0%ED%8F%AC%20%EA%B5%AC%EC%A1%B0%20%EC%9D%B4%ED%95%B4.md)

## 예상 꼬리 질문

- multi-arch 이미지를 항상 만드는 것이 정답인가요?
  - 답변: 팀의 개발 환경과 운영 환경이 자주 다르면 기본값으로 두는 편이 좋습니다. 다만 단일 아키텍처만 운영한다면 필요한 아키텍처를 명시적으로 빌드해도 됩니다. 핵심은 대상 환경과 manifest를 맞추는 것입니다.
- 이미지 아키텍처 문제와 runtime 설정 문제는 어떻게 구분하나요?
  - 답변: `pull` 단계에서 manifest 에러가 나면 release artifact 문제에 가깝고, 이미지를 가져온 뒤 컨테이너가 죽으면 env나 runtime 설정 문제일 가능성이 큽니다. 그래서 체크 순서를 분리해야 합니다.
- 왜 compose 검증보다 manifest 확인을 먼저 두나요?
  - 답변: compose는 실행 계획이 맞는지 보는 단계이고, manifest는 그 계획에 쓸 이미지가 실제로 존재하는지 보는 단계입니다. artifact가 없으면 뒤 단계 검증은 통과해도 배포가 성립하지 않습니다.

## Internal Links

- [[Areas/Career]]

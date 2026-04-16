---
note_type: project_log
status: archived
created: 2025-12-11
updated: 2025-12-18
tags: []
area:
project: "React 개인 프로젝트"
---

# 📝 Cubing Hub 개발 일지 - Day 10
2024-12-11 | 🏷️ Tags: #Project #Fullstack #SpringBoot #React #Refactoring

## 1. ✅ 오늘 한 일 (Done)
*기존 React 단일 프로젝트를 Spring Boot와 통합된 풀스택 모노레포 구조로 전환함.*

- **프로젝트 구조 리팩토링 (Monorepo)**
  - 기존 React 프로젝트를 `frontend` 디렉토리로 이동
  - `backend` 디렉토리에 Spring Boot 프로젝트 초기화 및 배치
  - 최상위 `.gitignore` 통합 설정 (React + Spring Boot)
- **문서화 (Documentation)**
  - 프로젝트 루트 `README.md` 작성: 전체 아키텍처, 기술 스택, API 명세, 실행 가이드 정리
  - 프론트엔드/백엔드 별 `README.md` 분리 및 고도화

## 2. 🧠 오늘 배운 점 (Today I Learned)
*RESTful API 설계 원칙과 JPA 연관관계 매핑에 대해 학습함.*

- **RESTful API 설계**: 리소스는 명사(URI), 행위는 메서드(HTTP Method)로 표현하며, 적절한 상태 코드(201, 400 등)와 DTO 사용이 필수적임을 이해함.
- **Monorepo 관리**: 프론트엔드와 백엔드를 하나의 저장소에서 관리할 때의 폴더 구조와 Git 관리 전략.

## 3. 🚨 트러블 슈팅 (Troubleshooting)

### 🚧 문제 상황 (Issue)
프로젝트 구조 변경 후 확인해 보니, `frontend/react-cube-project/package.json` 처럼 폴더가 불필요하게 한 단계 더 중첩(Nested)되어 있는 현상 발생.

### 🔍 원인 분석 (Cause)
기존 프로젝트 폴더를 통째로 `frontend` 폴더 안으로 드래그해서 옮기는 과정에서, 알맹이(src 등)만 옮겨지지 않고 상위 폴더까지 포함되어 이동됨. 이로 인해 터미널 경로 진입이 불편해지고 배포 시 경로 설정이 복잡해짐.

### 🛠️ 해결 방법 (Solution)
1. 중첩된 내부 폴더(`react-cube-project`, `cube-server`) 안의 모든 파일(숨김 파일 포함)을 상위 폴더(`frontend`, `backend`)로 **잘라내기 & 붙여넣기** 하여 이동.
2. 비어있는 껍데기 폴더 삭제.
3. 터미널에서 `ls` 명령어로 `frontend` 직하위에 `package.json`이 위치함을 확인하여 구조 평탄화 완료.

### 📚 참고 자료 (References)
- [Spring Data JPA Reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [REST API Design Guide](https://restfulapi.net/)

## 4. 📅 내일 할 일 (To-Do)
- [ ] **Repository Layer 구현**: JpaRepository 인터페이스 생성
- [ ] **DTO (Data Transfer Object) 설계**: 엔티티와 분리된 요청/응답 객체 생성 (`MemberDto`, `PostDto` 등)
- [ ] **Service Layer 구현**: 비즈니스 로직 작성 (회원가입, 게시글 작성 등)
- [ ] **Controller Layer 구현**: REST API 엔드포인트 생성 및 테스트
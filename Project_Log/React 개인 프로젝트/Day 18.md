# 📝 큐브 허브(Cubing Hub) 개발 일지 - Day 18
2025-12-19 | 🏷️ Tags: #Springboot #JPA #Refactoring #BugFix

## 1. ✅ 오늘 한 일 (Done)
- **백엔드 아키텍처 리팩토링 (JPA Best Practice 적용)**
  - **Entity 개선**: `Member`, `Post` 등 주요 엔티티에서 무분별한 `@Setter` 제거 및 `@Builder` 패턴 적용으로 객체 불변성 확보
  - **DTO 구조 분리**: 하나의 DTO 클래스 내부에 `Request`(생성/수정)와 `Response`(응답) Inner Class를 정의하여 역할 명확화
  - **Logic 개선**: `Controller`와 `Service` 계층의 로직을 RESTful 설계 원칙에 맞춰 개선 (적절한 `ResponseEntity` 반환 등)
- **풀스택 버그 수정 (Hotfix)**
  - **게시판 오류 수정**: 상세 조회 시 작성자 권한(`authorId`) 미인식 문제 해결 및 날짜 필드명 불일치(`date` → `createTime`) 수정
  - **알고리즘 페이지 수정**: 훅 반환 변수명 대소문자 불일치 버그 수정 (`ALGO_DATA` → `algoData`)
  - **코드 정리**: 사용하지 않는 레거시 스토어 파일(`useBoardStore.js`) 삭제
- **문서화**: 백엔드 API 명세 및 프로젝트 구조를 담은 `README.md` 업데이트

## 2. 🧠 오늘 배운 점 (Today I Learned)
- **Entity의 무결성:** `@Setter`를 열어두면 어디서든 값이 변할 수 있어 유지보수가 힘들지만, `@Builder`를 쓰면 생성 시점에만 값을 주입하므로 데이터 안전성이 높아짐을 배움.
- **DTO 분리의 중요성:** 요청(Request) 받을 때 필요한 데이터와 응답(Response) 줄 때 필요한 데이터가 다르므로, 이를 확실히 분리해야 API 스펙이 깔끔해짐.
- **변수명 일치:** 프론트엔드와 백엔드, 혹은 훅과 컴포넌트 간의 변수명이 다르면 데이터가 있어도 렌더링되지 않는 사소하지만 치명적인 실수를 경험함.

## 3. 🚨 트러블 슈팅 (Troubleshooting)

### 🚧 문제 상황 (Issue)
1. 게시글 상세 페이지에서 작성자임에도 '수정/삭제' 버튼이 보이지 않고, 작성일이 비어있음.
2. 알고리즘 페이지 진입 시 데이터가 로드되지 않고 렌더링 에러 발생.

### 🔍 원인 분석 (Cause)
1. 백엔드 `PostDto` 응답에 `authorId`가 포함되지 않아 프론트에서 권한 확인 불가. 또한 API는 `createTime`을 주는데 프론트는 `date`를 찾고 있었음.
2. `useAlgorithms` 훅은 `ALGO_DATA`를 반환하는데, 컴포넌트에서는 `algoData`로 구조 분해 할당을 시도하여 `undefined`가 됨.

### 🛠️ 해결 방법 (Solution)
1. `PostDto.Response`에 `authorId` 필드를 추가하고, 프론트엔드 `Detail.jsx`의 날짜 필드명을 `createTime`으로 수정함.
2. 훅의 반환 구문을 수정하여 컴포넌트가 기대하는 변수명(`algoData`)으로 데이터를 넘겨주도록 통일함.

## 4. 📅To-Do
- Spring Security 도입 및 비밀번호 암호화(BCrypt) 적용
- AWS 등 클라우드 배포 환경 구성
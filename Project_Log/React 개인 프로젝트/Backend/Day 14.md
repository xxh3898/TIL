# 📝 CUBE Project 개발 일지 - Day 14
2025-12-15 | 🏷️ Tags: #Project #DevLog #SpringBoot #React #FullStack

## 1. ✅ 오늘 한 일 (Done)
*프론트엔드와 백엔드를 연동하여 실제 데이터를 주고받는 풀스택 구조 완성*

- **프론트엔드 API 전면 교체 (Dummy Data 제거)**
    - `useBoardStore` 등 로컬 스토리지 기반 코드를 `axios` 기반 API 호출로 변경
    - 홈 화면(`Home.jsx`), 게시판 상세(`Detail.jsx`), 글쓰기(`Write.jsx`), 수정(`Edit.jsx`) 기능 연동
    - 마이페이지(`Mypage.jsx`) 내 기록 조회 기능 연동
- **백엔드 기능 확장 및 버그 수정**
    - **게시판 기능 완성**: 상세 조회(`findById`), 수정(`update`), 삭제(`delete`) API 및 서비스 로직 구현
    - **CORS 설정 변경**: 프론트엔드 포트 변경(5174 등)에 유연하게 대응하기 위해 `@CrossOrigin(origins = "*")` 적용
    - **DTO 수정**: 프론트엔드와의 데이터 키 값 통일 (`memberId` vs `authorId`)
- **스타일 및 UI 수정**
    - `BoardStyled.js`, `MemberStyled.js`에 누락된 스타일 컴포넌트(`ProfileSection`, `ButtonArea` 등) 추가 및 에러 해결

## 2. 🧠 오늘 배운 점 (Today I Learned)
*풀스택 연동 과정에서 겪은 경험*

- **CORS (Cross-Origin Resource Sharing)**: 브라우저는 보안상의 이유로 다른 출처(Port가 다르면 다른 출처)간의 리소스 공유를 막는다. 이를 해결하기 위해 백엔드에서 허용 정책을 설정해줘야 함을 배웠다.
- **API와 데이터 일관성**: 백엔드가 보내주는 필드명(`authorId`)과 프론트엔드가 기대하는 필드명(`memberId`)이 다르면, 데이터가 있어도 화면에 제대로 표시되지 않거나 권한 체크 로직이 깨질 수 있음을 확인했다.
- **RESTful API 설계의 빈틈**: 목록 조회만 만들고 상세 조회를 안 만들면 `404 Not Found`가 발생한다. 프론트엔드에서 필요한 엔드포인트가 무엇인지 미리 명세하고 구현하는 것이 중요하다.

## 3. 🚨 트러블 슈팅 (Troubleshooting)
*오늘 발생했던 주요 에러와 해결 과정*

### 🚧 문제 상황 1: CORS 정책에 의한 차단 (Access Blocked)
- **증상**: 프론트엔드 실행 포트가 `5174`로 바뀌자 로그인 및 회원가입 요청이 실패함.
- **원인**: 백엔드 컨트롤러에 `@CrossOrigin(origins = "http://localhost:5173")`으로 특정 포트만 허용되어 있었음.
- **해결**: 모든 출처를 허용하도록 `@CrossOrigin(origins = "*")`으로 변경하여 해결.

### 🚧 문제 상황 2: 게시글 상세 페이지 404 에러
- **증상**: 게시글 목록에서 글을 클릭하면 `404 Not Found` 에러 발생.
- **원인**: 백엔드 `PostController`에 전체 목록 조회(`findAll`)만 있고, 단건 상세 조회(`findById`) 메서드가 구현되어 있지 않았음.
- **해결**: `PostService`와 `Controller`에 상세 조회, 수정, 삭제 로직을 추가 구현하여 해결.

### 🚧 문제 상황 3: 수정/삭제 버튼 미노출 (권한 체크 오류)
- **증상**: 내가 쓴 글임에도 불구하고 상세 페이지에서 '수정', '삭제' 버튼이 보이지 않음.
- **원인**: 백엔드 DTO는 작성자 ID를 `authorId`로 보내는데, 프론트엔드(`Detail.jsx`)는 `post.memberId`와 비교하고 있었음. (`undefined`와 비교되어 항상 false)
- **해결**: 프론트엔드 코드를 `post.authorId`로 수정하여 백엔드 데이터와 일치시킴.

### 🚧 문제 상황 4: 스타일 컴포넌트 누락 (Runtime Error)
- **증상**: 글쓰기 및 마이페이지 진입 시 화면이 하얗게 변하며 렌더링 에러 발생.
- **원인**: `Write.jsx`에서 사용하는 `ButtonArea`, `Mypage.jsx`에서 쓰는 `ProfileSection` 등이 스타일 파일(`Styled.js`)에 정의되지 않음.
- **해결**: 누락된 스타일 컴포넌트 정의를 추가하여 정상 렌더링 복구.

## 4. 📅 내일 할 일 (To-Do)
- [ ] **DB 영속화 설정**: H2 DB 설정을 메모리(`mem`)에서 파일 저장(`file`) 모드로 변경하여 서버 재시작 후에도 데이터 유지하기
- [ ] **마이페이지 고도화**: 누락된 기능 구현 및 UI 개선
- [ ] **문서화(Documentation) 정리**: 프로젝트 전체 `README.md` 작성 및 프론트/백엔드 `README` 내용 최신화 및 통일
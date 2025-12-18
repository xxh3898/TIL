# 📝 큐브 허브(Cubing Hub) 개발 일지 - Day 17
2025-12-18 | 🏷️ Tags: #React #Refactoring #CustomHooks #ResponsiveDesign

## 1. ✅ 오늘 한 일 (Done)
- **프론트엔드 아키텍처 리팩토링 (View & Logic 분리)**
  - 컴포넌트 내 비즈니스 로직을 **Custom Hooks**로 전면 분리 (`useCubeTimer`, `useLogin`, `useSignup`, `useMypage`, `useBoard` 등)
  - UI 컴포넌트는 오직 렌더링에만 집중하도록 구조 개선 (Container-Presenter 패턴 적용 효과)
- **코드 최적화 및 정리**
  - 방대한 알고리즘 데이터를 `src/constants/cubeData.js`로 분리하여 가독성 향상
  - API 연동으로 불필요해진 `useBoardStore`(로컬 스토리지 기반) 및 관련 초기화 코드 삭제
- **전체 페이지 반응형 디자인(Responsive UI) 적용**
  - 모바일/태블릿 환경 대응을 위한 미디어 쿼리(`@media`) 적용
  - 로그인/회원가입, 마이페이지, 홈, 타이머, 게시판 등 전 페이지 레이아웃 유동적 처리
  - 모바일에서 테이블 깨짐 방지를 위한 가로 스크롤(`TableWrapper`) 구현

## 2. 🧠 오늘 배운 점 (Today I Learned)
- **Custom Hooks 패턴의 장점:** 로직을 별도 파일로 분리하니 컴포넌트 코드가 간결해지고, 로직의 재사용성과 테스트 용이성이 높아짐을 체감함.
- **반응형 웹 디자인 전략:**
  - 고정 단위(`px`)보다는 유동 단위(`%`, `max-width`)를 사용하는 것이 모바일 확장에 유리함.
  - `flex-wrap: wrap`을 활용하면 화면이 줄어들 때 자연스러운 줄바꿈 처리가 가능함.
- **Co-location 원칙:** 스타일이나 로직 파일을 무조건 분리하는 것보다, 관련된 컴포넌트 근처에 두는 것이 유지보수에 더 효율적일 수 있음을 배움.

## 3. 🚨 트러블 슈팅 (Troubleshooting)

### 🚧 문제 상황 (Issue)
1. 리팩토링 후 로그인 페이지 진입 시 `Cannot read properties of undefined (reading 'id')` 에러 발생하며 흰 화면 뜸.
2. 모바일 화면에서 게시판 테이블이 찌그러지거나 화면 밖으로 튀어 나가는 현상 발생.

### 🔍 원인 분석 (Cause)
1. `useLogin.js` 훅에서 값을 반환할 때 객체 리터럴 `{}` 대신 괄호 `()`를 사용하여, 마지막 함수 하나만 반환됨. (JS의 쉼표 연산자 동작)
2. `table` 태그는 기본적으로 내용물에 맞춰 너비가 결정되므로, 작은 화면에서는 강제로 줄어들거나 부모 영역을 침범함.

### 🛠️ 해결 방법 (Solution)
1. `useLogin.js`의 리턴문을 `return { input, handleChange, handleLogin };` (중괄호)로 수정하여 정상적으로 객체를 반환하도록 함.
2. `TableWrapper`라는 `div` 컨테이너를 만들고 `overflow-x: auto` 속성을 부여하여, 테이블이 화면보다 클 경우 자동으로 가로 스크롤이 생기도록 처리함.

## 4. 📅 내일 할 일 (To-Do)
- **보안 강화:** Spring Security 도입 및 비밀번호 암호화(BCrypt) 적용
- **배포(Deployment):** AWS 또는 클라우드 플랫폼을 통한 실제 서비스 배포 준비
- 알고리즘 페이지: `Cannot read properties of undefined`오류 수정
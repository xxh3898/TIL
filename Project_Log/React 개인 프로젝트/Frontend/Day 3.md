# react-cube-project 개발 일지 - Day 3
2025-12-04 | 🏷️ Tags: #Project #DevLog #React #Zustand #VisualCubeAPI

## 1. ✅ 오늘 한 일 (Done)
*구체적인 작업 내용과 관련 커밋 링크가 있다면 함께 적어주세요.*
- **게시판(Board) 기능 구현 (CRUD)**
    - `useBoardStore`와 `localStorage`를 연동하여 게시글 작성, 조회, 수정, 삭제 기능 구현
    - **작성자 권한 체크 로직**: 로그인한 유저와 게시글 작성자가 일치할 때만 수정/삭제 버튼이 노출되도록 구현 (`user.name === post.author`)
    - 게시글 목록, 상세 페이지, 글쓰기/수정 폼 UI 스타일링 (`BoardStyled.js` 통합)

- **마이페이지(Mypage) 대시보드 구현**
    - 유저 프로필 및 큐브 기록 통계(총 솔빙 수, 최고 기록, 평균 기록) 자동 계산 로직 추가
    - 타이머 기록 목록 조회 및 개별 삭제 기능 구현 (`removeRecord` 액션 추가)
    - 데이터가 없을 때의 `EmptyMsg` UI 처리

- **알고리즘(Algorithms) 페이지 및 3D 시각화**
    - **VisualCube API 연동**: 텍스트 공식(`formula`)을 쿼리 파라미터로 변환하여 3D 큐브 이미지를 동적으로 생성
    - **탭(Tab) 기능**: F2L, 2-Look OLL, Full PLL 카테고리별 데이터 구조화 및 탭 네비게이션 구현
    - **시각화 최적화**: `stage` 파라미터를 활용해 OLL/PLL 단계별로 불필요한 부분은 마스킹 처리하여 가독성 높임

- **코드 리팩토링 및 최적화**
    - 스타일 파일 통합 (`TimerStyled.js`, `BoardStyled.js`, `MemberStyled.js`) 및 폴더 구조 정리
    - `key` props 경고 수정 및 불필요한 리렌더링 방지

## 2. 🧠 오늘 배운 점 (Today I Learned)
*새롭게 알게 된 기술적 사실, 개념, 꿀팁을 기록합니다.*
- **외부 이미지 API 활용법 (VisualCube)**: 별도의 3D 라이브러리 없이 URL 파라미터(`case`, `view`, `sch`)만으로 원하는 상태의 큐브 이미지를 생성하는 방법을 익힘.
- **상태 관리 패턴**: `Zustand`의 `persist` 미들웨어 없이 `localStorage`를 직접 연동하여 `initialize` 하는 패턴을 통해 데이터 영속성을 구현함.
- **이미지 URL 인코딩**: 공식을 URL 파라미터로 넘길 때 특수문자(`'`, 공백) 처리를 위해 `encodeURIComponent`가 필수적임을 확인함.

## 3. 🚨 트러블 슈팅 (Troubleshooting)
*가장 중요한 섹션입니다. 에러 로그와 해결 과정을 상세히 적습니다.*

### 🚧 문제 상황 (Issue 1)
- **상황**: VisualCube API로 이미지를 생성할 때, `x`나 `y` 등 큐브 전체 회전이 포함된 공식(예: Aa Perm, E Perm)의 경우 윗면이 노란색이 아닌 다른 색으로 출력됨.
- **결과**: 사용자가 공식을 볼 때 기준 면(Top: Yellow, Front: Green 등)이 달라져 혼란을 줄 수 있음.

### 🔍 원인 분석 (Cause)
- API의 `case` 파라미터는 입력된 공식을 역재생하여 큐브 상태를 보여주는데, 회전 기호가 포함되면 큐브의 시점 자체가 돌아가버린 상태로 렌더링되기 때문.

### 🛠️ 해결 방법 (Solution)
- **해결**: 데이터 객체에 실제 공식(`formula`)과 별개로 **이미지 생성용 공식(`display`)** 속성을 추가함.
    - 예: `x ...` 로 시작하는 공식 대신, 회전 없이 동일한 패턴을 만드는 공식을 `display`에 할당하여, 텍스트는 원본을 유지하되 이미지는 정면(윗면 노랑)을 바라보도록 보정함.
    - `sch=yrbwog` 파라미터를 추가하여 색상 스키마를 고정함.

---

### 🚧 문제 상황 (Issue 2)
- **상황**: 기존에 사용하던 `http://cube.rider.biz` API가 브라우저의 Mixed Content 정책(HTTPS 페이지에서 HTTP 리소스 로드 차단)으로 인해 이미지가 엑스박스(깨짐)로 나옴.

### 🛠️ 해결 방법 (Solution)
- **해결**: HTTPS를 지원하는 최신 엔드포인트 `https://visualcube.api.cubing.net/visualcube.php`로 마이그레이션하여 해결.

### 📚 참고 자료 (References)
- [VisualCube API Documentation](https://visualcube.api.cubing.net/)
- [Zustand Guide](https://docs.pmnd.rs/zustand/getting-started/introduction)

## 4. 📅 내일 할 일 (To-Do)
- 전체 코드 리뷰 피드백 반영 (변수명 정리, 불필요한 주석 제거)
- (선택) 댓글 기능 또는 대댓글 구조 설계 고민
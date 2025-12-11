# React 개인 프로젝트 유틸리티 웹 'SpeedCubing.io' 

---

## Ⅰ. 서론

### 1. 주제 선정 배경: 왜 큐브인가?
평소 취미가 큐브인데 지금은 타이머 앱 따로, 커뮤니티 따로, 공식 사이트 따로, 모든 게 파편화되어 있어 아쉬웠습니다. 그래서 큐브 기록 측정부터 공식 학습, 그리고 커뮤니티까지 한곳에서 해결할 수 있는 올인원 유틸리티 사이트를 직접 만들어보기로 했습니다.

기존의 큐브 타이머 앱들은 기능이 하나에만 집중되어 있거나 디자인이 투박한 경우가 많았습니다. 그래서 **React**의 상태 관리와 컴포넌트 구조를 학습함과 동시에, 실제 큐버(Cuber)들에게 필요한 기능을 깔끔한 UI로 제공하는 **'React Cube Project'** 를 기획하게 되었습니다.

### 2. 프로젝트 미리보기
이 프로젝트는 크게 네 가지 핵심 기능을 제공합니다.
* **큐브 타이머**: 스택매트(Stackmat) 방식의 타이머 구현 및 스크램블 자동 생성
* **기록 관리**: 로컬 스토리지를 활용한 개인별 기록 저장 및 통계(PB, Average) 제공
* **공식 시각화**: VisualCube API를 연동한 3D 큐브 회전 공식 시각화
* **커뮤니티**: 사용자 간 소통을 위한 게시판

### 3. 기술 스택 (Tech Stack)
* **Frontend**: React (Vite), JavaScript (ES6+)
* **State Management**: Zustand
* **Styling**: Styled-components
* **Routing**: React Router DOM v6
* **Persistence**: LocalStorage

### 4. 프로젝트 구조
기능별로 폴더를 명확히 분리하여 유지보수성을 높였습니다.

```bash
src/
├── components/      # Header, Layout 등 공통 UI 컴포넌트
├── pages/           # 라우팅되는 페이지 단위
│   ├── board/       # 게시판 (목록, 상세, 글쓰기, 수정)
│   ├── cube/        # 큐브 기능 (타이머, 알고리즘)
│   ├── member/      # 회원 기능 (로그인, 회원가입, 마이페이지)
│   └── Home.jsx     # 대시보드 메인 페이지
├── stores/          # Zustand 전역 상태 관리 (Member, Board, Timer)
└── utils/           # 스크램블 생성기 등 유틸리티 함수
```

---

## Ⅱ. 주요 기능 구현 및 로직 분석

### 1. 큐브 타이머: 스택매트 UX 구현
![Main Banner](https://raw.githubusercontent.com/xxh3898/react-cube-project/main/react-cube-project/src/assets/Timer.gif)

큐브 타이머의 핵심은 실제 대회용 타이머(스택매트)처럼 **'스페이스바를 꾹 누르고 있을 때 준비(Holding 상태), 손을 떼면 시작(Running 상태)'** 하는 UX를 구현하는 것입니다.

이를 위해 `onKeyDown`과 `onKeyUp` 이벤트를 활용했으며, 특히 스페이스바를 계속 누르고 있을 때 이벤트가 반복 발생하여 타이머가 오작동하는 것을 방지하기 위해 `e.repeat` 속성을 체크했습니다.

**구현 코드 (`src/pages/cube/Timer.jsx`)**
```jsx
// 상태 관리를 위한 useRef와 useState 활용
const handleKeyDown = (e) => {
  if (e.code !== 'Space') return;
  e.preventDefault();
  if (e.repeat) return; // 키 반복 입력 방지

  if (statusRef.current === 'running') {
    // 타이머 정지 로직
    clearInterval(timerIntervalRef.current);
    // ... 기록 저장 로직 ...
    statusRef.current = 'idle';
    setStatus('idle');
  } else if (statusRef.current === 'idle') {
    // 준비 상태 진입
    setTime(0);
    statusRef.current = 'holding';
    setStatus('holding');
  }
};

const handleKeyUp = (e) => {
  if (e.code !== 'Space') return;

  if (statusRef.current === 'holding') {
    // 손을 떼면 타이머 시작
    startTimeRef.current = Date.now();
    statusRef.current = 'running';
    setStatus('running');

    timerIntervalRef.current = setInterval(() => {
      setTime(Date.now() - startTimeRef.current);
    }, 10);
  }
};
```

### 2. 서버리스 데이터 영속성: Zustand + LocalStorage
![mypage](https://raw.githubusercontent.com/xxh3898/react-cube-project/main/react-cube-project/src/assets/mypage.png)

별도의 백엔드 서버 없이도 사용자가 새로고침 후에도 로그인 상태와 기록을 유지할 수 있도록 **Zustand**와 **LocalStorage**를 연동했습니다. `useMemberStore` 훅을 통해 회원 정보와 기록 데이터를 전역으로 관리하며, 상태가 변경될 때마다 로컬 스토리지에 저장됩니다.

**구현 코드 (`src/stores/useMemberStore.js`)**
```javascript
import { create } from 'zustand';

const STORAGE_KEY = "cube_app_data";
const USER_KEY = "cube_user_session";

const useMemberStore = create((set, get) => ({
    members: [],
    user: null,

    // 초기화: 앱 실행 시 로컬 스토리지에서 데이터 로드
    initialize: () => {
        const savedData = localStorage.getItem(STORAGE_KEY);
        if (savedData) {
            set({ members: JSON.parse(savedData).members || [] });
        }
        const savedUser = localStorage.getItem(USER_KEY);
        if (savedUser) {
            set({ user: JSON.parse(savedUser) });
        }
    },

    // 기록 추가 시 상태 업데이트 및 로컬 스토리지 저장
    addRecord: (newRecord) => {
        const { user, members } = get();
        if (!user) return;

        const updatedUser = {
            ...user,
            records: [...user.records, newRecord]
        };
        set({ user: updatedUser });
        localStorage.setItem(USER_KEY, JSON.stringify(updatedUser)); // 세션 업데이트

        // 전체 멤버 목록에서도 해당 유저 정보 업데이트
        const updatedMembers = members.map(m =>
            m.id === user.id ? updatedUser : m
        );
        set({ members: updatedMembers });
        localStorage.setItem(STORAGE_KEY, JSON.stringify(updatedMembers)); // 로컬 스토리지 업데이트
    },
    // ...
}));
```

### 3. 알고리즘 시각화: 외부 API 활용
![Algorithms](https://raw.githubusercontent.com/xxh3898/react-cube-project/main/react-cube-project/src/assets/algorithms.png)

사용자가 텍스트로 된 공식을 더 직관적으로 이해할 수 있도록 **VisualCube API**를 활용했습니다. 사용자가 탭(F2L, OLL, PLL)을 변경하면, 해당 탭에 맞는 3D 큐브 이미지를 동적으로 생성하여 보여줍니다.

**구현 코드 (`src/pages/cube/Algorithms.jsx`)**
```jsx
const getImageUrl = (algo, tab) => {
  const baseUrl = "https://visualcube.api.cubing.net/visualcube.php";

  // 화면에 보여줄 공식 또는 내부 처리용 공식 사용
  const formulaToUse = algo.display || algo.formula;
  const encodedAlgo = encodeURIComponent(formulaToUse);

  // API 파라미터 설정 (포맷, 크기, 단계 등)
  let params = `fmt=svg&size=150&pzl=3&case=${encodedAlgo}&sch=yrbwog`;

  if (tab === 'OLL') params += '&stage=oll';
  if (tab === 'PLL') params += '&stage=pll';

  return `${baseUrl}?${params}`;
};
```

### 4. 커뮤니티: 권한 관리 (Permission)
![board](https://raw.githubusercontent.com/xxh3898/react-cube-project/main/react-cube-project/src/assets/board.png)

게시판 기능에서는 **본인이 작성한 글만 수정/삭제할 수 있게** 권한 로직을 구현했습니다. 로그인한 유저(`user.id`)와 게시글 작성자(`post.authorId`)를 비교하여 버튼의 렌더링 여부를 결정했습니다.

**구현 코드 (`src/pages/board/Detail.jsx`)**
```jsx
const Detail = () => {
  const { id } = useParams();
  const { posts, deletePost } = useBoardStore();
  const { user } = useMemberStore();

  const post = posts.find((p) => p.id === Number(id));
  
  // 작성자 권한 체크
  const isAuthor = user && user.id === post.authorId;

  const handleDelete = () => {
    if (!isAuthor) {
      alert("권한이 없습니다.");
      return;
    }
    if (window.confirm("정말로 삭제하시겠습니까?")) {
      deletePost(id);
      navigate('/board');
    }
  };

  return (
    <BoardContainer>
      {/* ... 내용 생략 ... */}
      
      <ButtonGroup>
        <SecondaryButton onClick={() => navigate('/board')}>목록으로</SecondaryButton>
        
        {/* 작성자 본인에게만 수정/삭제 버튼 노출 */}
        {isAuthor && (
          <>
            <Button onClick={() => navigate(`/board/edit/${id}`)}>수정</Button>
            <SecondaryButton onClick={handleDelete}>삭제</SecondaryButton>
          </>
        )}
      </ButtonGroup>
    </BoardContainer>
  );
};
```

---

## Ⅲ. 회고

### 1. 이번 프로젝트에서 배운 점
* **Zustand의 간결함**: `create` 함수 하나로 전역 상태와 액션을 직관적으로 관리할 수 있어 생산성이 크게 향상되었습니다.
* **CSS-in-JS (Styled-components)**: 컴포넌트 파일 내에서 스타일을 함께 관리하니 유지보수가 편했고, `props`를 통해 동적으로 스타일을 변경(예: 타이머 상태에 따른 색상 변경)하는 로직을 쉽게 구현할 수 있었습니다.
* **useState와 useRef의 차이**: `useState`는 상태 변경 시 리렌더링을 유발하지만, `useRef`는 리렌더링 없이 값을 유지한다는 차이를 배웠습니다.
  
  0.01초 단위로 갱신되는 타이머에서 모든 변수를 `useState`로 관리할 경우, 불필요한 렌더링으로 인해 **화면 버벅임(Stuttering)** 과 **시간 정밀도 저하**가 발생함을 경험했습니다.
  
  자주 변하는 `status` 등은 `useRef`로 관리하여 **불필요한 렌더링을 방지**하고, `useEffect` 의존성 배열 문제를 해결해 **이벤트 리스너가 불필요하게 재생성되는 성능 문제**를 최적화했습니다.
### 2. 트러블 슈팅 (Trouble Shooting)
- **문제**: 타이머 구현 시 키보드를 누르고 있으면 `keydown` 이벤트가 연속으로 발생하여 타이머 상태가 꼬이는 현상이 발생했습니다.
- **해결**: `KeyboardEvent` 객체의 `repeat` 속성을 발견하여, `if (e.repeat) return;` 코드를 추가함으로써 키를 꾹 누르고 있어도 이벤트가 한 번만 처리되도록 수정했습니다.

- **문제**: 타이머 정지 시 `setInterval`의 지연과 리액트 렌더링 속도 차이로 인해, 화면에 표시된 시간과 실제 저장되는 기록 간에 미세한 오차(약 0.002초~0.01초)가 발생했습니다.
- **해결**: `setInterval`은 화면 갱신용으로만 사용하고, 실제 기록은 `Date.now()`를 활용해 **시작 시간과 종료 시간의 차이(Delta Time)** 를 계산하여 저장하는 방식으로 정확도를 높였습니다.

- **문제**: 새로고침 시 게시판 데이터나 로그인 정보가 휘발되는 문제.
- **해결**: `App.jsx`가 마운트되는 시점(`useEffect`)에 `localStorage`에서 데이터를 불러와 Zustand Store에 다시 넣어주는 초기화 로직(`initialize`)을 추가하여 해결했습니다.

### 3. 향후 보완 계획
* **페이징 처리**: 현재는 모든 게시글과 기록을 한 번에 보여주고 있는데, 데이터가 쌓일 것을 대비해 페이지네이션(Pagination) 기능을 추가할 예정입니다.
* **통계 고도화**: 단순 평균 외에 큐브 종목에서 많이 쓰이는 Ao5(최근 5회 평균), Ao12 등을 자동으로 계산해 주는 기능을 추가하여 전문성을 높이고 싶습니다.
* **랭킹 시스템**: 게시판 아이디 옆에 해당 유저의 최고 기록(PB)을 뱃지 형태로 보여주어 경쟁 요소를 도입해 볼 생각입니다.
* **댓글 기능**: 게시판 글에 댓글 기능을 도입하여 커뮤니티 기능을 더욱 활성화할 예정입니다.
* **모바일 반응형**: 모바일 사용자를 위한 반응형 디자인

---

> **Github Repository**: [https://github.com/xxh3898/react-cube-project](https://github.com/xxh3898/react-cube-project)
>
> **Demo**: [https://react-cube-project.vercel.app/](https://react-cube-project.vercel.app/)
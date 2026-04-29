---
note_type: project_issue
status: archived
created: 2026-02-15
updated: 2026-02-15
tags: []
area:
project: "CalmDesk"
---
# 이슈 해결 리포트

| 작성일       | 2026.01.23                                                                       | 작성자     | 조치호         |
| :-------- | :------------------------------------------------------------------------------- | :------ | :---------- |
| **이슈 유형** | - [X] 🐛 버그/에러 <br>- [ ] 🗣️ 커뮤니케이션/일정 <br>- [ ] ⚡ 기획/스펙 변경 <br>- [ ] 🏗️ 인프라/환경 | **중요도** | ⭐⭐⭐ (상/중/하) |
| **관련 태그** | **#403Error #JWT #Axios #SpringSecurity #Recharts**<br>                          |         |             |

---

## 1. 문제 상황

* **상황:** 백엔드 API 연동 과정에서 `부서 조회`, `근태 내역`, `상담 신청` 등 주요 페이지 진입 시 **403 Forbidden** 에러가 지속적으로 발생하여 데이터를 불러오지 못함.
* **상황:** 대시보드의 주간 스트레스 차트 영역에서 `The width(-1) and height(-1) of chart should be greater than 0`라는 콘솔 경고가 뜨며 차트가 비정상적으로 렌더링되거나 깨지는 현상 발생.
* **영향:** 사용자(직원)가 자신의 근태 정보를 확인하거나 상담을 신청하는 핵심 기능을 전혀 사용할 수 없어 개발 진행이 차단됨.

## 2. 원인 분석

* **원인 1 (Frontend Auth):** JWT 토큰 저장 키 불일치. 로그인 시에는 `authToken`이라는 키로 로컬 스토리지에 저장했으나, 정작 API 요청을 보내는 `Frontend/src/api/axios.js` 인터셉터에서는 `token`이라는 키로 조회를 시도하여 헤더에 토큰이 실리지 않음.
* **원인 2 (Frontend Code):** `Frontend/src/pages/employee/Department/Department.jsx`, `Frontend/src/pages/employee/Attendance/Attendance.jsx` 등 개별 페이지에서 중앙 설정된 `apiClient`를 사용하지 않고, 기본 `axios` 객체를 직접 import하여 사용함. 이로 인해 인터셉터(Header 주입 로직)가 작동하지 않은 채 쌩 요청(Raw Request)이 전송됨.
* **원인 3 (Backend Security):** `Backend/src/main/java/com/code808/calmdesk/global/config/SecurityConfig.java` 설정 미흡.
    * URL 오타: `/api/employee/...`를 `/api/emplpoyee/...`로 잘못 기재.
    * 허용 누락: `/api/departments/`**, `/api/consultations/`** 등 일부 엔드포인트에 대한 `authenticated()` 설정이 빠져 있어 접근이 거부됨.
* **원인 4 (Library):** Recharts 라이브러리는 부모 컨테이너의 크기를 감지해 반응형으로 동작하는데, Flexbox 레이아웃 내에서 `min-width` 설정이 없으면 초기 렌더링 시 너비를 제대로 계산하지 못하는 CSS 이슈가 있었음.

## 3. 해결 과정 및 의사결정

1.  **1차 시도 (Axios 설정 수정):** `Frontend/src/api/axios.js`의 토큰 키를 `token`에서 `authToken`으로 수정함. → 대시보드 일부 기능은 복구되었으나 여전히 다른 페이지에서는 403 에러 발생.
2.  **2차 시도 (백엔드 설정 수정):** `Backend/src/main/java/com/code808/calmdesk/global/config/SecurityConfig.java`의 오타를 수정하고 누락된 엔드포인트 설정을 추가함. → 여전히 해결되지 않음.
3.  **심층 분석 (코드 리뷰):** 403이 발생하는 페이지들의 코드를 `view_file`로 확인한 결과, `import axios from 'axios'` 구문을 발견. 설정된 `apiClient`를 쓰지 않고 있음을 파악함.
4.  **최종 조치:** 모든 API 호출부의 `axios`를 삭제하고 `apiClient`로 교체 및 상대 경로(`.get('/departments/1')`) 사용으로 통일함.

## 4. 최종 해결책

* **Frontend (`Frontend/src/api/axios.js`):** `localStorage.getItem('token')` ➡️ `localStorage.getItem('authToken')`으로 키 값 통일.
* **Frontend (Pages):** 각 페이지 컴포넌트(Department, Attendance, Consultation) 리팩토링.

    ```javascript
    // 변경 전
    import axios from 'axios';
    axios.get('http://localhost:8080/api/...');

    // 변경 후
    import apiClient from '../../../api/axios';
    apiClient.get('/...'); // 토큰 자동 주입 확인
    ```

* **Backend (`Backend/src/main/java/com/code808/calmdesk/global/config/SecurityConfig.java`):** 오타 수정 및 `requestMatchers`에 누락된 엔드포인트 전수 등록.
* **Frontend (Style):** 차트 컨테이너 스타일 수정.

    ```css
    width: 100%;
    min-width: 0; /* Flexbox 내 width 계산버그 방지 */
    overflow: hidden;
    ```


## 5. 배운 점 및 인사이트

* **Keep:** API 통신 모듈(`apiClient`)을 중앙화해 둔 설계 자체는 좋았음. 이를 일관성 있게 사용하는 것이 중요함.
* **Try:** 새로운 페이지나 컴포넌트를 만들 때 무의식적으로 `axios`를 import 하지 않도록, `apiClient` 사용을 팀 룰로 명확히 정해야겠음.
* **Note:** 403 에러가 발생하면 무조건 백엔드 권한 문제라고 생각하기 쉬운데, 프론트엔드에서 토큰을 제대로 보내고 있는지(헤더 검사)부터 먼저 크로스 체크해야 함을 재확인함.

* * *

### 📎 참고 자료 (Reference)

* 수정된 파일: `Frontend/src/api/axios.js`, `Backend/src/main/java/com/code808/calmdesk/global/config/SecurityConfig.java`, `Frontend/src/pages/employee/Department/Department.jsx` 등
* Recharts Issue Reference: [Flexbox container sizing issue](https://github.com/recharts/recharts/issues/...)

## Internal Links

- [[Archive/Projects/CalmDesk/CalmDesk]]
- [[Archive/Projects/CalmDesk/Docs/README]]
- [[Archive/Projects/CalmDesk/Docs/API 명세서]]

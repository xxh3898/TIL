---
note_type: project_doc
status: archived
created: 2026-02-02
updated: 2026-02-02
tags: []
area:
project: "CalmDesk"
---
# 마이페이지 가이드 (관리자 · 직원)

마이페이지 관련 기능을 **관리자**와 **직원** 역할별로 정리한 가이드입니다.  

---

## 1. 직원(Employee) 마이페이지

### 1-1. 화면 진입

| 구분 | 내용 |
|------|------|
| **URL** | `/app/mypage` (및 하위 경로) |
| **대상** | 로그인한 **직원(EMPLOYEE)** 본인 |
| **라우팅** | `App.jsx` → `path="mypage/*"` → `MyPage.jsx` (직원용) |

**하위 경로**

| 경로 | 화면 | 설명 |
|------|------|------|
| `/app/mypage` | MyPageMain | 마이페이지 메인 (프로필·기프티콘 미리보기) |
| `/app/mypage/profile` | ProfileEditView | 프로필 수정 (이메일·연락처·비밀번호) |
| `/app/mypage/points` | PointHistoryView | 포인트 내역 |
| `/app/mypage/coupons` | CouponWalletView | 기프티콘 보관함 |

---

### 1-2. 직원용 API (본인 데이터)

**Base URL:** `GET/PUT` → `/api/mypage/...`  
**공통:** 쿼리 파라미터 `memberId` = 로그인한 본인 회원 ID (예: `user.id`)

| 기능 | 메서드 | 엔드포인트 | Body(해당 시) | 설명 |
|------|--------|------------|---------------|------|
| 프로필 조회 | GET | `/api/mypage/profile?memberId={id}` | - | 본인 프로필(이메일, 연락처, 회사, 부서, 직급, 포인트 등) |
| 프로필 수정 | PUT | `/api/mypage/profile?memberId={id}` | `{ email, phone }` | 이메일·연락처 수정 (중복 검사 있음) |
| 비밀번호 변경 | PUT | `/api/mypage/password?memberId={id}` | `{ currentPassword, newPassword }` | 비밀번호 변경 |
| 포인트 내역 | GET | `/api/mypage/points?memberId={id}` | - | 포인트 적립/사용 내역 목록 |
| 기프티콘 목록 | GET | `/api/mypage/coupons?memberId={id}` | - | 보유 기프티콘 목록 |
| 스트레스 요약 | GET | `/api/mypage/stress?memberId={id}` | - | 스트레스 요약 (있을 경우) |

직원은 **본인 `memberId`만** 사용합니다. (store의 `user.id` 등)

---

### 1-3. 직원 사용 시 참고

- **프로필 수정**: 이메일·연락처 변경 시 다른 회원과 중복 불가. 실패 시 `"이미 사용 중인 이메일입니다."` 등 메시지 확인.
- **비밀번호 변경**: 현재 비밀번호가 일치해야 하며, 백엔드에서 BCrypt로 검증·저장.
- **포인트/기프티콘**: 계좌(ACCOUNT)·포인트 내역(Point_History)·주문(Order) 기준으로 조회.

---

## 2. 관리자(Admin) 마이페이지

### 2-1. 화면 진입

| 구분 | 내용 |
|------|------|
| **URL** | `/app/mypage` (관리자 로그인 시) |
| **대상** | 로그인한 **관리자(ADMIN)** |
| **라우팅** | `App.jsx` → `path="mypage/*"` (관리자 분기) → `AdminMyPage` |

관리자는 **특정 직원의 마이페이지 데이터**를 열람·수정할 수 있습니다.  
(어떤 직원을 볼지 `memberId`로 지정)

---

### 2-2. 관리자용 API (특정 회원 데이터)

**Base URL:** `/api/admin/mypage/{memberId}/...`  
**경로에 `memberId`** = 조회/수정 대상 회원 ID (직원 한 명)

| 기능 | 메서드 | 엔드포인트 | Body(해당 시) | 설명 |
|------|--------|------------|---------------|------|
| 프로필 조회 | GET | `/api/admin/mypage/{memberId}/profile` | - | 해당 직원 프로필 조회 |
| 프로필 수정 | PUT | `/api/admin/mypage/{memberId}/profile` | `{ email, phone }` | 해당 직원 이메일·연락처 수정 |
| 비밀번호 변경 | PUT | `/api/admin/mypage/{memberId}/password` | `{ currentPassword, newPassword }` | 해당 직원 비밀번호 변경 |
| 포인트 내역 | GET | `/api/admin/mypage/{memberId}/points` | - | 해당 직원 포인트 내역 |
| 기프티콘 목록 | GET | `/api/admin/mypage/{memberId}/coupons` | - | 해당 직원 기프티콘 목록 |

- 직원 API와 **비즈니스 로직(서비스)은 동일**하고, **URL·파라미터 방식만 다름**  
  - 직원: `/api/mypage/...?memberId=`  
  - 관리자: `/api/admin/mypage/{memberId}/...`

---

### 2-3. 관리자 사용 시 참고

- **memberId**: 조회/수정할 **직원**의 회원 ID. 관리자 본인 ID가 아님.
- **권한**: 실제 권한 체크(관리자만 호출 가능 등)는 SecurityConfig·JWT 역할(ADMIN)로 제어.
- **비밀번호 변경**: 관리자가 대신 변경할 때는 프론트에서 “현재 비밀번호”를 어떻게 넣을지(직원에게 받거나, 관리자 전용 플로우 등) 정책 확인 필요.

---

## 3. 직원 vs 관리자 요약

| 구분 | 직원(Employee) | 관리자(Admin) |
|------|----------------|----------------|
| **화면** | `/app/mypage` (MyPage) | `/app/mypage` (AdminMyPage) |
| **API prefix** | `/api/mypage/...` | `/api/admin/mypage/...` |
| **대상** | 본인 (`memberId` = 본인 ID) | 특정 직원 (`memberId` = 그 직원 ID) |
| **memberId 위치** | 쿼리: `?memberId=` | 경로: `/{memberId}/...` |
| **Controller** | `MyPageController` (employee) | `AdminMyPageController` |
| **Service** | 동일 `MyPageService` / `MyPageServiceImpl` | 동일 |

---

## Internal Links

- [[Archive/Projects/CalmDesk/CalmDesk]]
- [[Archive/Projects/CalmDesk/Docs/README]]
- [[Archive/Projects/CalmDesk/Docs/API 명세서]]

---
note_type: project_doc
status: archived
created: 2026-02-25
updated: 2026-02-25
tags: []
area:
project: "CalmDesk"
---
# CalmDesk API 명세서 (API Specification)

본 문서는 **Code808 - CalmDesk** 프로젝트의 모든 API 명세 정보를 담고 있습니다. 
본 명세서는 Swagger(OpenAPI) 기반으로 작성되었으며, 실제 백엔드 컨트롤러 구조를 반영합니다.

## 📌 기본 정보
- **Base URL**: `http://localhost:8080`
- **Content-Type**: `application/json`
- **인증 방식**: JWT (Header: Authorization: Bearer {Access_Token}, Cookie: refresh_token)
- **Response Format**: 하단의 `공통 응답 규격` 섹션을 따릅니다.

---

## 🔐 1. 인증 API (Auth)
사용자 인증 및 권한 관리를 담당합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/auth/login` | **로그인**: 이메일/비밀번호로 인증 후 토큰 발급 |
| `POST` | `/api/auth/signup` | **회원가입**: 새로운 사용자 계정 생성 |
| `POST` | `/api/auth/logout` | **로그아웃**: 현재 세션 및 토큰 무효화 |
| `POST` | `/api/auth/refresh` | **토큰 갱신**: Refresh Token으로 새로운 Access Token 발급 |

---

## 2. 사용자 마이페이지 API (My Page)
직원 본인의 프로필 및 자산을 관리합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/mypage/profile` | **프로필 조회**: 본인의 기본 정보 조회 |
| `PUT` | `/api/mypage/profile` | **프로필 수정**: 이름, 전화번호, 프로필 이미지 등 수정 |
| `PUT` | `/api/mypage/password` | **비밀번호 변경**: 현재 비밀번호 확인 후 새 비밀번호 설정 |
| `GET` | `/api/mypage/stress` | **스트레스 요약 조회**: 최근 AI 분석 결과 요약 |
| `GET` | `/api/mypage/points` | **포인트 내역 조회**: 획득/사용 히스토리 (페이징) |
| `GET` | `/api/mypage/coupons` | **기프티콘 목록 조회**: 내가 보유한 쿠폰함 조회 |

---

## 👑 3. 관리자용 사용자 관리 API (MyPage Admin)
관리자가 사내 특정 멤버의 정보를 조회하거나 수정합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/mypage/{memberId}/profile` | 특정 멤버의 프로필 정보 조회 |
| `PUT` | `/api/admin/mypage/{memberId}/profile` | 특정 멤버의 프로필 정보 수정 |
| `PUT` | `/api/admin/mypage/{memberId}/password` | 특정 멤버의 비밀번호 강제 변경 |
| `GET` | `/api/admin/mypage/{memberId}/points` | 특정 멤버의 포인트 내역 조회 |
| `GET` | `/api/admin/mypage/{memberId}/coupons` | 특정 멤버의 보유 기프티콘 조회 |

---

## 🖥️ 4. 직원용 대시보드 API (Dashboard Employee)
오늘의 출결 상태 및 근무 상태를 관리합니다.

| 메서드    | 엔드포인트                                      | 설명                                   |
| :----- | :----------------------------------------- | :----------------------------------- |
| `GET`  | `/api/employee/dashboard`                  | **대시보드 데이터 조회**: 오늘의 출결, 미션 상태 등     |
| `POST` | `/api/employee/dashboard/status/clock-in`  | **출근 처리**: 출근 체크 및 현재 기분(Emotion) 기록 |
| `POST` | `/api/employee/dashboard/status/clock-out` | **퇴근 처리**: 퇴근 체크 및 현재 기분(Emotion) 기록 |
| `POST` | `/api/employee/dashboard/status`           | **근무 상태 변경**: 업무중, 회의중, 외출중 등으로 변경   |

---

## 📊 5. 관리자용 대시보드 통계 API (Dashboard Admin)
회사 전체의 실시간 지표를 확인합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/dashboard/stats` | **대시보드 통계 조회**: 실시간 출근자, 위험군 현황 등 |
| `GET` | `/api/dashboard/stats/yesterday` | **어제자 통계 조회**: 전날의 주요 지점 데이터 비교용 |
| `GET` | `/api/dashboard/subscribe` | **SSE 구독**: 대시보드 데이터 실시간 갱신을 위한 스트림 구독 |

---

## 6. 직원 근태 관리 API (Attendance)
개인의 근무 및 휴가 이력을 확인합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/employee/attendance/summary` | **근태 요약 조회**: 월별 총 근무 시간, 지각 횟수 등 |
| `GET` | `/api/employee/attendance/history` | **근태 기록 히스토리 조회**: 전체 출퇴근 로그 내역 |
| `GET` | `/api/employee/attendance/leaves` | **휴가 신청 현황 조회**: 본인이 신청한 휴가의 승인/반려 상태 |

---

## 🏖️ 7. 직원용 휴가 관리 API (Vacation Employee)
휴가 신청 및 취소 기능을 담당합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/employee/vacation` | **휴가 신청**: 연차, 반차 등 휴가 신청 생성 |
| `DELETE` | `/api/employee/vacation/{vacationId}` | **휴가 취소**: '대기' 상태인 신청 건 취소 |

---

## ⚖️ 8. 관리자용 휴가 관리 API (Vacation Admin)
사내 휴가 신청을 승인하거나 반려합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/vacation` | **휴가 신청 목록 조회**: 회사 전체의 미처리 휴가 신청 목록 |
| `PUT` | `/api/admin/vacation/{vacationId}/approve` | **휴가 승인**: 특정 신청 건 승인 처리 |
| `PUT` | `/api/admin/vacation/{vacationId}/reject` | **휴가 반려**: 특정 신청 건 사유와 함께 반려 |

---

## 9. 실시간 채팅 API (Chatting)
회원 간 1:1 및 그룹 채팅 기능을 제공합니다.

### 📡 REST API
| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/chat/rooms` | **내 채팅방 목록 조회**: 참여 중인 방 목록 조회 |
| `POST` | `/api/chat/room` | **채팅방 생성/조회**: 상대방과 1:1 대화방 시작 (상대방 ID 기반) |
| `POST` | `/api/chat/create` | **채팅방 생성**: 다자간 대화 등 신규 방 생성 |
| `GET` | `/api/chat/history/{roomId}` | **채팅 기록 조회**: 특정 방의 이전 메시지 내역 |
| `POST` | `/api/chat/room/{roomId}/read` | **메시지 읽음 처리**: 특정 방의 모든 메시지 읽음 갱신 |
| `PATCH` | `/api/chat/message/{messageId}` | **메시지 수정**: 보낸 메시지 내용 수정 |
| `DELETE` | `/api/chat/message/{messageId}` | **메시지 삭제**: 보낸 메시지 삭제 |
| `GET` | `/api/chat/members` | **회사 내 사용자 조회**: 채팅방 초대 및 시작을 위한 멤버 검색 |

### 🔌 WebSocket (STOMP)
- **Endpoint**: `/ws-stomp`
- **보내기 (Pub)**: `/pub/chat/message`
  - Payload: `{ "roomId": Long, "senderId": Long, "content": String, "type": "TALK" }`
- **받기 (Sub)**: `/sub/chat/room/{roomId}`

---

## 🤖 10. AI 챗봇 API (AI Chat)
도움이 필요한 경우 AI와 대화합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/chat` | **AI 메시지 전송**: 상담 혹은 질문 메시지 전송 및 답변 수신 |

---

## 🔔 11. 알림 API (Notification)
시스템 알림 및 실시간 알림을 관리합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/subscribe/{memberId}` | **알림 구독 (SSE)**: 실시간 알림 수신을 위한 SSE 구독 |
| `GET` | `/api/notifications/{memberId}` | **알림 내역 조회**: 해당 사용자의 최근 알림 목록 조회 |
| `PATCH` | `/api/notifications/{id}/read` | **알림 읽음 처리**: 특정 알림을 확인 상태로 변경 |
| `PATCH` | `/api/notifications/read-all/{memberId}` | **모든 알림 읽음 처리**: 사용자의 모든 미확인 알림 확인 처리 |

---

## 🛍️ 12. 직원용 포인트 몰 API (Gifticon Employee)
획득한 포인트로 기프티콘을 구매하고 미션을 수행합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/employee/shop/{userId}` | **포인트 몰 메인 데이터 조회**: 포인트, 미션, 상점 통합 데이터 |
| `POST` | `/api/employee/shop/purchase` | **기프티콘 구매**: 포인트를 소모하여 상품 교환 |
| `POST` | `/api/employee/shop/mission/complete` | **미션 완료 및 보상 지급**: 미션 수행 확인 후 포인트 지급 |
| `POST` | `/api/employee/shop/attendance/check-in` | **출근 체크 미션 업데이트**: 출근 시 미션 진행 데이터 전송 |

---

## 13. 관리자용 상점 관리 API (Gifticon Admin)
판매 상품의 재고와 판매 상태를 관리합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/shop/items` | **아이템 목록 조회**: 상점의 모든 상품 현황 조회 |
| `PUT` | `/api/admin/shop/items/{id}/{quantity}` | **아이템 수량 업데이트**: 재고 변경 |
| `PATCH` | `/api/admin/shop/items/{id}/toggle` | **아이템 활성 상태 토글**: 판매 시작/중지 |
| `POST` | `/api/admin/shop/items/activate-all` | **아이템 전체 활성화** |
| `POST` | `/api/admin/shop/items/deactivate-all` | **아이템 전체 비활성화** |
| `GET` | `/api/admin/shop/history/all` | **전체 구매 내역 조회**: 전 직원의 구매 이력 통계 |

---

## 🩺 14. 상담 신청 및 관리 API (Consultation & Admin)
심리 상담 혹은 업무 상담을 신청하고 관리합니다.

### 👤 직원용
| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/consultations` | **상담 신청 생성**: 고민 및 희망 시간 등 작성 |
| `GET` | `/api/consultations/me` | **본인 상담 신청 목록 조회**: 내 신청 건의 현재 상태 여부 확인 |
| `GET` | `/api/consultations/count` | **대기 중인 상담 수 조회**: 회사 내 전체 상담 대기 밀도 확인 |

### 👑 관리자용
| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/consultations` | **상담 신청 목록 조회**: 회사 내 상담 요청 리스트 |
| `PUT` | `/api/admin/consultations/{consultationId}/complete` | **상담 완료 처리**: 상담 진행 후 완료 상태 변경 |
| `PUT` | `/api/admin/consultations/{consultationId}/cancel` | **상담 취소 처리**: 부적절한 요청 혹은 일정 문제로 취소 |

---

## 🏢 15. 회사 및 조직 관리 API (Company & Department)
회사 정보와 조직 구성을 관리합니다.

### 📄 회사 관련
| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/companies/genderate-code` | **회사 코드 생성**: 회사 등록을 위한 유효 코드 발급 |
| `POST` | `/api/companies/register` | **회사 등록**: 신규 회사를 시스템에 등록 (관리자 권한) |
| `GET` | `/api/companies/by-code/{companyCode}` | **코드 기반 회사 조회**: 코드로 회사 확인 및 정보(부서/직급) 수신 |
| `POST` | `/api/companies/join` | **회사 참여 신청**: 해당 회사로 입사 신청 |

### 📂 부서 관련
| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/departments/{departmentId}` | **부서 상세 정보 조회**: 부서명 및 기본 지표 |
| `GET` | `/api/departments/{departmentId}/members` | **부서원 목록 조회 (페이징)**: 특정 부서 소속 멤버 리스트 |

---

## ✅ 16. 관리자용 입사 신청 관리 API (Company Join)
신규 입사자의 승인 과정을 관리합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/joins` | **입사 신청 목록 조회**: 대기 중인 가입 희망자 목록 |
| `PUT` | `/api/admin/joins/{memberId}/approve` | **입사 신청 승인**: 해당 멤버를 정식 소속으로 승인 |
| `PUT` | `/api/admin/joins/{memberId}/reject` | **입사 신청 반려**: 승인 거절 |
| `GET` | `/api/admin/joins/ranks` | **직급 목록 조회**: 가입 승인 시 부여할 직급 리스트 조회 |

---

## 🪪 17. 명함 관리 API (Business Card)
명함 정보 추출 및 등록을 지원합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/business-card/extract` | **명함 정보 추출**: 이미지(OCR/AI)로부터 이름, 연락처 등 추출 |
| `POST` | `/api/business-card/register` | **명함 등록**: 추출된 정보를 바탕으로 최종 저장 |
| `GET` | `/api/business-card/contacts` | **명함 목록 조회**: 사내 및 공유된 명함 연락처 목록 |

---

## 📞 18. 통화 기록 API (Call Record)
통화 내역 업로드 및 STT(Speech-to-Text) 분석 결과를 제공합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `POST` | `/api/call-records` | **통화 녹음 파일 업로드**: 파일을 서버로 전송 및 STT 시작 |
| `GET` | `/api/call-records` | **통화 기록 목록 조회**: 본인 혹은 회사의 통화 이력 리스트 |
| `GET` | `/api/call-records/search` | **전화번호 검색**: 특정 번호 혹은 내용으로 기록 검색 |
| `POST` | `/api/call-records/{recordId}/reprocess` | **STT 재처리**: 에러가 발생한 건에 대해 인식 재시도 |

---

## 19. 관리자용 모니터링 API (Monitoring Admin)
실시간 지표 모니터링 및 리포트를 출력합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/monitoring` | **모니터링 데이터 조회**: 전체 근무 현황 및 요약 데이터 |
| `GET` | `/api/admin/monitoring/excel` | **엑셀 다운로드**: 현재 필터링된 데이터를 엑셀 파일로 생성 |

---

## 📋 20. 관리자용 팀 관리 API (Team Admin)
회사 내 조직 및 멤버를 통합 관리합니다.

| 메서드 | 엔드포인트 | 설명 |
| :--- | :--- | :--- |
| `GET` | `/api/admin/team/members` | **전체 멤버 목록 조회 (페이징)**: 회사 모든 직원 목록 관리 |
| `GET` | `/api/admin/team/members/{memberId}/attendance` | **특정 멤버 근태 조회**: 개별 직원의 상세 근태 캘린더 데이터 |
| `GET` | `/api/admin/team/stats` | **팀 통계 조회**: 부서별 인원 및 성과 지표 요약 |
| `GET` | `/api/admin/team/departments` | **부서 목록 조회 (이름만)**: 부서 이동 및 필터용 목록 |
| `GET` | `/api/admin/team/departments-list` | **부서 목록 상세 조회**: 관리용 부서 상세 리스트 |
| `POST` | `/api/admin/team/departments` | **부서 생성**: 새로운 조직(부서) 추가 |

---

## 📝 공통 응답 규격 (Common Response)
모든 API는 아래와 같은 표준 응답 구조를 가집니다.

### 성공 (Success)
```json
{
  "status": "success",
  "message": "요청이 성공적으로 처리되었습니다.",
  "data": { ... }
}
```

### 실패 (Error)
```json
{
  "status": "error",
  "message": "에러 발생 시 출력되는 메시지입니다.",
  "data": null
}
```

## Internal Links

- [[Archive/Projects/CalmDesk/CalmDesk]]
- [[Archive/Projects/CalmDesk/Docs/README]]
- [[Archive/Projects/CalmDesk/Docs/MYPAGE-GUIDE]]

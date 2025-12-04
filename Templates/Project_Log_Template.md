# 📝 프로젝트명 개발 일지 - Day 15
<% tp.date.now("YYYY-MM-DD") %> | 🏷️ Tags: #Project #DevLog

## 1. ✅ 오늘 한 일 (Done)
*구체적인 작업 내용과 관련 커밋 링크가 있다면 함께 적어주세요.*
- [FE] 로그인 페이지 UI 퍼블리싱 (CSS Grid 적용)
- [BE] JWT 토큰 발급 API 연동 테스트 완료

## 2. 🧠 오늘 배운 점 (Today I Learned)
*새롭게 알게 된 기술적 사실, 개념, 꿀팁을 기록합니다.*
- **JWT 구조**: Header, Payload, Signature로 구성된다는 점을 재확인함.
- **CSS**: `gap` 속성이 Flexbox에서도 지원된다는 것을 알게 됨.

## 3. 🚨 트러블 슈팅 (Troubleshooting)
*가장 중요한 섹션입니다. 에러 로그와 해결 과정을 상세히 적습니다.*

### 🚧 문제 상황 (Issue)
- 프론트(3000번 포트)에서 백엔드(8080번 포트) 호출 시 CORS 에러 발생.

### 🔍 원인 분석 (Cause)
- 브라우저의 SOP(Same-Origin Policy) 정책으로 인해 서로 다른 출처(Origin) 간의 리소스 공유가 제한됨.

### 🛠️ 해결 방법 (Solution)
- **임시 해결**: 서버 설정(`WebMvcConfig`)에서 `allowedOrigins("*")`로 모든 도메인 허용.
- **개선 예정**: 내일 Spring Security 설정에서 특정 도메인(`http://localhost:3000`)만 허용하도록 `CorsConfigurationSource` 수정 필요.

### 📚 참고 자료 (References)
- [블로그] Spring Boot CORS 설정 완벽 가이드 (링크)
- [공식문서] MDN Web Docs - CORS (링크)

## 4. 📅 내일 할 일 (To-Do)
- [Security] CORS 설정 구체화 (보안 강화)
- [User] 회원가입 유효성 검사 로직 (Regex 적용)
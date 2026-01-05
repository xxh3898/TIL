# 📝 프로젝트명 개발 일지 - Day 35
2026-01-05 | 🏷️ Tags: #Project #DevLog #SpringSecurity #JWT

## 1. ✅ 오늘 한 일 (Done)
- **Spring Security + JWT 기반 인증 시스템 구축**
	- SecurityConfig, JwtTokenProvider, JwtAuthenticationFilter 구현
	 - 비밀번호 암호화(`BCryptPasswordEncoder`) 적용
	- PR #40: feat: Spring Security + JWT 기반 인증/인가 시스템 구현
- **로그인 로직 리팩토링 및 분리**
	- MemberService에서 인증 로직을 제거하고 AuthService로 분리
	- `/api/auth/login` 엔드포인트 생성
- **Frontend 연동 작업**
	- Axios Interceptor를 통한 JWT 토큰 자동 전송 구현
	- 로그인 API 요청 경로 및 파라미터 수정 (id/password -> `user_id`/`user_pwd`)
- **GitHub MCP 활용**
	- 로컬 변경 사항 커밋 및 푸시, PR 자동 생성 테스트

## 2. 🧠 오늘 배운 점 (Today I Learned)
- **JWT 보안 전략**: Access Token(단기)과 Refresh Token(장기)을 분리하여 보안성과 사용자 편의성을 동시에 확보하는 방법.
- **Spring Security와 H2 Console**: Spring Security는 기본적으로 `X-Frame-Options`를 통해 iframe을 차단하므로, H2 Console을 사용하려면 `http.headers().frameOptions().disable()` 설정이 필수임.
- **GitHub MCP 권한**: MCP 도구가 PR 생성 등 쓰기 작업을 수행하려면 Personal Access Token에 `repo` 또는 `public_repo` 스코프가 반드시 포함되어야 함.

## 3. 🚨 트러블 슈팅 (Troubleshooting)

### 🚧 문제 상황 (Issue)
1. **서버 빌드 실패**: MemberService 수정 후 "cannot find symbol" 에러 다수 발생.
2. **H2 Console 접속 불가**: 로그인 페이지가 뜨지 않고 연결 거부됨.
3. **Frontend 로그인 실패**: 회원가입 후 로그인 시도 시 계속 실패.
4. **MCP PR 생성 실패**: GitHub MCP 도구로 PR 생성 시 `403 Forbidden` 에러 발생.

### 🔍 원인 분석 (Cause)
1. MemberService.java 코드 교체 시 패키지 선언과 import 구문이 실수로 누락되었고, build.gradle에 `validation` 의존성이 없었음.
2. Spring Security가 `/h2-console` 경로를 인증 없이 접근하지 못하게 막았고, iframe 사용도 차단함.
3. Frontend는 구버전 API(`/api/members/login`, id/password)를 호출하고 있었으나 Backend는 신버전 API(`/api/auth/login`, `user_id`/`user_pwd`)로 변경됨.
4. 로컬에 저장된 GitHub Auth Token에 PR 생성을 위한 쓰기 권한(Scope)이 부족했음.

### 🛠️ 해결 방법 (Solution)
1. MemberService.java에 누락된 코드를 복구하고 build.gradle에 `spring-boot-starter-validation` 추가.
2. SecurityConfig에 `.requestMatchers("/h2-console/**").permitAll()` 및 frame options disable 설정 추가.
3. Frontend의 requests.js와 useLogin.js를 Backend 최신 API 명세에 맞게 수정.
4. 권한 문제는 즉시 해결이 어려워 수동 링크를 통해 웹에서 PR을 생성함. (향후 토큰 권한 수정 필요)

## 4. 📅 내일 할 일 (To-Do)
- Refresh Token 저장소(Redis/DB) 구현 및 토큰 재발급(Reissue) API 개발
- 게시판(Post) 및 기록(Record) API에 JWT 인증 적용 및 테스트
- Access Token 만료 시 Frontend에서 자동으로 갱신하는 로직 구현
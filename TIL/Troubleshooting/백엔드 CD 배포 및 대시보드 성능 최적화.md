---
note_type: project_issue
status: archived
created: 2026-03-09
updated: 2026-03-09
tags: []
area:
project: "CalmDesk"
---
# 이슈 해결 리포트

| 작성일 | 2026.01.09 | 작성자 | 조치호 |
|--------|------------|--------|--------|
| **이슈 유형** | [x] 🐛 버그/에러 <br>[ ] 🗣️ 커뮤니케이션/일정 <br>[ ] ⚡ 기획/스펙 변경 <br>[x] 🏗️ 인프라/환경 | **중요도** | ⭐⭐⭐ |
| **관련 태그** | #GitHubActions #SpringBoot #JPA #NPlusOne #AWS #EC2 #Scheduler |

---

## 1. 문제 상황

### 상황 1 — GitHub Actions CD 배포 실패
- GitHub Actions를 통해 EC2 서버로 배포 후 서버가 정상적으로 실행되지 않고 중단됨.
- 로그에 다음 오류 발생
	- `Could not resolve placeholder 'jwt.secret'`
	- `Failed to determine suitable jdbc url`

### 상황 2 — JPQL 엔티티 인식 오류
- 서버 구동 중 다음 예외 발생
  `UnknownEntityException: Could not resolve root entity 'CoolDown'`

### 상황 3 — 대시보드 API 응답 지연
- 프론트엔드에서 대시보드 조회 시 **응답까지 약 25초 소요**
- EC2 메모리 Swap 사용량이 **440MB 이상** 증가하며 서버가 버벅거림

### 상황 4 — EC2 CPU 사용률 급증
- N+1 문제 해결 이후에도 API 응답이 **8~28초** 발생
- `top` 명령어 확인 결과 Java 프로세스 CPU 사용률 **180% 이상**
- Idle 시간이 **0%**로 서버 과부하 상태

---

## 2. 원인 분석

### 원인 1 — 설정 파일 누락
- `application-prod.yaml` 파일이 `.gitignore`에 등록되어 있어 **JAR 파일에 포함되지 않음**
- 실행 시 DB 접속 정보 및 JWT 설정을 찾지 못해 애플리케이션 시작 실패
- 실행 명령어에서 `-DJWT_SECRET`을 사용했지만 스프링은 `jwt.secret`을 찾고 있어 매핑 실패

### 원인 2 — JPQL 엔티티 이름 불일치
- `CoolDown` 엔티티에 다음과 같이 설정

```java
@Entity(name = "COOLDOWN")
```

- 하지만 JPQL에서는 다음처럼 사용

```java
SELECT c FROM CoolDown c
```

- 엔티티 이름이 **COOLDOWN vs CoolDown**으로 달라 JPQL이 엔티티를 인식하지 못함

### 원인 3 — JPA N+1 문제
- 대시보드에서 직원 50명 데이터를 조회할 때

```text
직원 1명당
Vacation 조회
Stress 조회
CoolDown 조회
```

- 총 **150개 이상의 쿼리 발생**

또한 설정

```yaml
default_batch_fetch_size: 1000
```

이 적용되지 않은 이유

- 연관관계 조회가 아닌
- `for`문 내부에서 **직접 Repository 호출**

### 원인 4 — EC2 CPU 크레딧 고갈
- AWS EC2 프리티어 인스턴스 (t2.micro / t3.micro)는 **CPU Credit 기반 성능 구조**
- 기존 N+1 쿼리 문제와 함께

```java
cron = "0 * * * * *"
```

- 스케줄러가 **1분마다 실행되며 DB 조회 반복**
- CPU Credit을 모두 소모하여 **AWS가 CPU 성능을 10~20%로 제한(Throttling)**

---

## 3. 해결 과정 및 의사결정

### 1차 시도
- 프론트엔드 프록시 설정 및 환경변수 전달 방식 수정 시도
- 하지만 서버 시작 오류 해결 실패

### 2차 시도
- GitHub Actions 로그 분석
- `application-prod.yaml` 누락 확인

### 3차 시도
- JPQL 에러 분석 후 엔티티 네이밍 문제 확인

### 4차 시도
- 대시보드 API 쿼리 분석
- 반복 Repository 호출 구조 확인

### 5차 시도
- EC2 서버 상태 점검 (`top`)
- CPU 사용률 180% 이상 확인
- AWS CPU Credit 구조 확인

### 최종 결정
- 외부 설정 파일 방식 적용
- JPQL 엔티티 네이밍 수정
- N+1 문제 제거
- 스케줄러 주기 변경

---

## 4. 최종 해결책

### 1️⃣ 외부 설정 파일 적용

EC2 서버에 설정 파일 직접 생성

```
/home/ubuntu/application-prod.yaml
```

배포 스크립트 수정

```bash
nohup java -jar \
--spring.config.additional-location=file:/home/ubuntu/application-prod.yaml
```

---

### 2️⃣ 엔티티 매핑 수정

기존 코드

```java
@Entity(name = "COOLDOWN")
```

수정 코드

```java
@Entity
@Table(name = "cooldown")
```

---

### 3️⃣ N+1 문제 해결 및 인프라 병목 최적화

기존 구조

```text
for (member) {
 vacationRepository.find...
 stressRepository.find...
 coolDownRepository.find...
}
```

개선 구조

```text
1. JPA @Query JOIN FETCH 및 WHERE IN 절 적용
2. 연관 엔티티를 DB 계층에서 단일 벌크 쿼리(IN + JOIN FETCH) 1회로 페치
3. WAS 메모리 조립(Map) 로직을 폐기하고, DB에서 최적화된 형태로 데이터 병합
```

결과 및 검증

```text
- 150 + 단건 쿼리 → 3개의 최적화된 벌크 쿼리로 병합
- 단일 응답 속도 25초 → 0.9초 (96% 단축)
- EC2 CPU 크레딧 고갈(Throttling) 마비 현상 완전 해소
- K6 부하 테스트(50 VUs) 기준 응답 지연 377.19ms → 101.01ms (73% 단축) 및 RPS 14% 향상
```

---

### 4️⃣ 스케줄러 최적화

기존

```java
cron = "0 * * * * *"
```

수정

```java
cron = "0 0 0 * * *"
```

또한 서버 부하 제거를 위해

```bash
sudo pkill -9 java
```

실행 후 CPU 크레딧 회복 대기

결과

```text
API 응답 속도
0.5초 수준으로 정상화
```

---

## 5. 배운 점 및 인사이트

### Keep
- GitHub Actions 로그 분석을 통해 문제 원인을 단계적으로 추적한 점
- 서버 상태(`top`)와 클라우드 인프라 구조를 함께 분석한 점

### Try
- 새로운 기술 도입 시 **PoC(기술 검증)** 먼저 수행
- JPA 사용 시 반복 조회 구조 사전 점검
- 스케줄러 주기 설계 시 서버 부하 고려

### Note
- Spring Boot 배포 시 **외부 설정 파일 관리 전략이 중요**
- JPA에서 `for`문 기반 조회는 **N+1 문제의 대표적인 원인**
- AWS EC2 프리티어는 **CPU Credit 기반 성능 제한 구조**가 존재
- 스케줄러 주기 또한 인프라 성능에 직접적인 영향을 준다

---

### 📎 참고 자료 (Reference)

- GitHub Actions 배포 로그
- EC2 서버 상태 (`top` 명령어)
- AWS EC2 CPU Credit 정책 문서

## Internal Links

- [[Archive/Projects/CalmDesk/CalmDesk]]
- [[Archive/Projects/CalmDesk/Docs/README]]

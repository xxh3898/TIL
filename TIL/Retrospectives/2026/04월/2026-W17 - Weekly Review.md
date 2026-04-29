---
note_type: weekly_review
status: active
created: 2026-04-26
updated: 2026-04-26
week: 2026-W17
month: 2026-04
tags: []
area:
project:
---

# Weekly Review

주차: 2026-W17

---

## 이번 주 성과

- 핵심 주제는 `Cubing Hub`의 성능 기준선 고정, Redis read model 전환, production 경계 안정화, PKM AI 산출물 흐름 실반영이었다.
- 랭킹 조회는 같은 seed와 같은 `k6` 시나리오 기준으로 baseline을 고정한 뒤 Redis ZSET 기반 hybrid read model로 전환했고, `avg 7245.23 ms -> 21.10 ms`, `p95 12429.58 ms -> 36.94 ms`까지 줄였다.
- 배포 쪽에서는 EC2 첫 기동 전제조건, Apple Silicon `arm64` 이미지와 `amd64` 서버 mismatch, CORS/apex origin, malformed `refresh_token` 복구, feedback Discord 알림 분리 같은 운영 차단 이슈를 실제 수정 단위와 DevQ&A/Troubleshooting 자산으로 묶었다.
- 마이페이지 summary aggregate query, 이메일 인증 회원가입, 비밀번호 재설정, 게시글 이미지 업로드와 조회수 격리, 모바일 상호작용 정리까지 이어지면서 기능 추가와 운영 안정화가 같은 흐름에서 닫혔다.
- PKM에서는 `AI/Inbox` copy-only 수집과 최종 보관소 승격을 실제로 반영해 원본 `/Users/chiho/AI/**`, 수집함, 장기 보관소의 역할을 더 명확히 했다.

---

## 공부 시간

월: 시간 미기록 - benchmark baseline 고정, 일정 재베이스라인, Redis 랭킹 V2 준비
화: 시간 미기록 - Redis read model 전환, EC2 배포 artifact 정리, 아키텍처 mismatch 확인
수: 시간 미기록 - auth recovery, CORS 수정, MyPage benchmark와 Grafana 비교
목: 시간 미기록 - 이메일 인증, 업로드/조회수 경계 안정화, PKM AI 수집 흐름 반영
금: 시간 미기록 - `Cubing Hub` 문서 closeout과 포트폴리오/면접 자산 보강 흔적 확인
토: 별도 작업 시간 기록 없음
일: 주간 리뷰 정리

합계: 시간 총량 기록은 없었지만, 이번 주는 benchmark 계약 고정과 production 경계 안정화에 가장 많은 에너지가 들어갔다.

---

## 운동

운동 횟수: 기록 없음

---

## 독서

읽은 책: 기록 없음

---

## 잘한 점

- 성능 개선을 감으로 적지 않고 `same scenario + same seed + same artifact` 기준으로 고정해 재사용 가능한 설명 자산까지 남겼다.
- 운영 문제를 한꺼번에 뭉개지 않고 `cookie path`, `CORS origin`, 이미지 manifest, `multipart` 헤더, body size처럼 경계 계약 단위로 잘게 분해해 해결했다.
- PKM 쪽도 규칙만 적는 데서 멈추지 않고 `AI/Inbox` 수집과 승격을 실제 노트 흐름으로 반영했다.

---

## 문제점

- 성능 수치와 기능 추가는 크게 전진했지만, 실운영 기준 수동 smoke와 closeout이 뒤에 몰리면서 배포 계약 검증이 반복적으로 병목이 됐다.

---

## 다음 주 전략

- `Cubing Hub`는 남은 운영 검증을 기능별로 나누어 닫는다. 이메일 인증/비밀번호 재설정, bad cookie recovery, 게시글 이미지 업로드, feedback/Discord, 배포 smoke를 각각 실제 브라우저와 서버 기준으로 확인한다.
- 배포는 SSH 유지 vs SSM 전환, multi-arch release 계약, 서버 디스크 압박 완화 같은 운영 정책을 더 미루지 말고 하나의 체크리스트로 굳힌다.

---

## 미완료 항목

- SMTP 실자격 증명 연결 후 회원가입 이메일 인증 / 비밀번호 재설정 end-to-end 확인
- bad `refresh_token`, apex/www 도메인, 배포 후 로그인 흐름의 운영 브라우저 smoke
- 게시글 이미지 업로드, 공개 Q&A, 관리자 피드백/메모 화면의 production 유사 조건 점검
- `Cubing Hub` 문서 closeout과 backend CD 정책 최종 확정

---

## 다시 봐야 할 노트

- [2026-04-21 - TIL](./2026-04-21%20-%20TIL.md)
- [2026-04-22 - TIL](./2026-04-22%20-%20TIL.md)
- [2026-04-23 - TIL](./2026-04-23%20-%20TIL.md)
- [Apple Silicon에서 만든 Docker 이미지가 amd64 서버에서 pull되지 않는 문제](../../../Troubleshooting/Apple%20Silicon%EC%97%90%EC%84%9C%20%EB%A7%8C%EB%93%A0%20Docker%20%EC%9D%B4%EB%AF%B8%EC%A7%80%EA%B0%80%20amd64%20%EC%84%9C%EB%B2%84%EC%97%90%EC%84%9C%20pull%EB%90%98%EC%A7%80%20%EC%95%8A%EB%8A%94%20%EB%AC%B8%EC%A0%9C.md)
- [malformed refresh_token가 login 자체를 막을 때 복구 endpoint는 왜 cookie path 밖에 있어야 하는가](../../../DevQ&A/malformed%20refresh_token%EA%B0%80%20login%20%EC%9E%90%EC%B2%B4%EB%A5%BC%20%EB%A7%89%EC%9D%84%20%EB%95%8C%20%EB%B3%B5%EA%B5%AC%20endpoint%EB%8A%94%20%EC%99%9C%20cookie%20path%20%EB%B0%96%EC%97%90%20%EC%9E%88%EC%96%B4%EC%95%BC%20%ED%95%98%EB%8A%94%EA%B0%80.md)

# 💥 Trouble Shooting: [에러 이름 또는 현상 요약]

**📅 Date:** <% tp.date.now("YYYY-MM-DD") %>
**🏷️ Tags:** #Error #Java #Spring #<% tp.date.now("MM월") %>

---

## 1. 🚨 문제 상황 (The Issue)
> 어떤 기능을 구현하다가 문제가 발생했나요? 구체적인 상황을 적어주세요.

- **상황:** (예: 회원가입 버튼을 눌렀는데 서버에서 500 에러가 발생함)
- **기대 결과:** (예: 회원 정보가 DB에 저장되고 로그인 페이지로 이동해야 함)
- **실제 결과:** (예: 아무 반응 없이 콘솔에 에러 메시지만 뜸)

### 🐾 에러 로그 / 스크린샷
```bash
# 에러 메시지나 로그를 복사해서 붙여넣으세요
java.lang.NullPointerException: Cannot invoke "String.equals(Object)" because "param" is null
    at com.example.controller.MemberController.join(MemberController.java:25)
```

---

## 2. 🔍 원인 분석 (Root Cause)
> 단순히 "오타였다"로 끝내지 말고, 왜 그 오타가 문제였는지 **논리적으로 추론한 과정**을 적으세요.

- **가설 1:** (예: 프론트엔드에서 값을 제대로 안 보낸 게 아닐까?)
    - *검증:* (예: 개발자 도구 확인 결과 데이터는 정상 전송됨. ❌ 기각)
- **가설 2:** (예: 백엔드 컨트롤러 파라미터 매핑 문제?)
    - *검증:* (예: 디버깅 결과 null 확인. ⭕ 확인)
- **최종 원인:**
    - (예: @RequestBody 누락으로 인한 매핑 실패)

---

## 3. 🛠️ 해결 방법 (Solution)
> 어떻게 코드를 수정했는지 **Before & After**로 비교해서 보여주세요.

### ❌ 변경 전 (Before)
```java
// 문제의 코드
@PostMapping("/join")
public String join(@RequestParam String id) { ... }
```

### ⭕ 변경 후 (After)
```java
// 수정된 코드
@PostMapping("/join")
public String join(@RequestBody MemberDTO memberDto) { ... }
```

---

## 4. 💡 알게 된 점 (Insight)
> 이번 에러를 통해 새롭게 배운 개념이나, 앞으로 주의해야 할 점을 기록하세요.

- 
- 

---

## 5. 📚 참고 자료 (References)
> 도움받은 블로그나 공식 문서 링크를 남겨두세요.

- [제목](링크)
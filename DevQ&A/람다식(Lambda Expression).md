자바 8(Java 8)은 자바 역사상 가장 큰 변화가 있었던 버전입니다. 그 변화의 중심에는 **람다식(Lambda Expression)** 이 있습니다.

처음 람다식을 보면 `->` 같은 낯선 화살표 때문에 "이게 무슨 외계어인가" 싶지만, 익숙해지면 "이거 없이 예전에 어떻게 코딩했지?"라는 생각이 들 정도로 강력하고 편리한 기능입니다.

오늘은 **람다식이 왜 탄생했는지**, 그리고 **어떻게 코드를 획기적으로 줄여주는지** 아주 쉽게 정리해 보겠습니다.

---

## 자바 8, 혁명의 시작 (The Why)

### 동작(코드 덩어리)을 전달하고 싶었던 자바
자바는 강력한 객체지향 언어입니다. 이 특성 때문에 어떤 '기능(동작)' 하나를 다른 메서드에 매개변수로 전달하려면, 반드시 그 기능을 가진 **객체(클래스 또는 인터페이스의 구현체)** 형태로 만들어서 전달해야 했습니다.

아주 간단한 동작 하나를 구현하려 해도 배보다 배꼽이 더 큰 상황이 발생하곤 했죠.

### 익명 내부 클래스 (Anonymous Inner Class)
스레드 하나를 실행하기 위해 `Runnable` 인터페이스를 구현하는 과거의 코드를 봅시다.

**[Before Java 8]**
```java
// 실제 필요한 건 출력문 한 줄인데, 껍데기 코드가 너무 많다.
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("스레드 실행!");
    }
}).start();
```
우리가 진짜 원하는 건 `System.out.println("스레드 실행!");` 이 한 줄의 동작인데, 이를 위해 `new Runnable() { ... @Override ... }` 라는 장황한 코드를 작성해야 했습니다.

### 람다식의 등장
자바 8은 이 문제를 해결하기 위해 함수형 프로그래밍의 개념을 도입했습니다.
**람다식**은 쉽게 말해 **"메서드를 하나의 간결한 식(Expression)으로 표현한 것"** 입니다. 거추장스러운 클래스 선언이나 메서드 이름을 없애고, **핵심 로직만 남기는 것**이 목표입니다.

![비교표](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2Fbn4WXc%2FdJMcagKRHVs%2FAAAAAAAAAAAAAAAAAAAAAKd4-u2SNNXCP7i23nNVlA8R5YWQ1AcQ-zlcDL5py133%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1767193199%26allow_ip%3D%26allow_referer%3D%26signature%3DQRCzrhaL5%252FHNpVJ7VomAXo3a0ks%253D)

---

## 람다식, 어떻게 생겼나? (Syntax)

### 기본 문법 구조
람다식은 화살표(`->`)를 기준으로 왼쪽은 입력(매개변수), 오른쪽은 동작(실행 코드)으로 나뉩니다

> **(매개변수 목록) -> { 실행할 코드 바디 }**

아까의 스레드 예제는 람다식을 만나면 이렇게 바뀝니다.

**[After Java 8]**
```java
// 훨씬 깔끔해졌다!
new Thread(() -> {
    System.out.println("스레드 실행!");
}).start();
```

### 람다식의 생략 규칙
람다식의 진가는 '생략'에 있습니다. 컴파일러가 문맥을 보고 추론할 수 있는 정보는 과감하게 생략합니다.

```java
// 예제용 인터페이스
interface Calculator { int operation(int a, int b); }

// 1. 기본 형태 (생략 없음)
(int a, int b) -> { return a + b; }

// 2. 매개변수 타입 생략 (컴파일러가 추론 가능)
(a, b) -> { return a + b; }

// 3. 실행 문장이 하나일 때, 중괄호 {}와 return, 세미콜론 생략 (가장 많이 씀!)
(a, b) -> a + b
```

---

## 람다식이 되기 위한 조건 (Functional Interface)

### 아무데나 쓸 수 있는 건 아니다
람다식은 마법이 아닙니다. 사실 컴파일러가 람다식을 보면 뒤에서 몰래 **익명 클래스의 객체를 생성**해주는 것입니다. 그렇다면 어떤 인터페이스를 람다식으로 바꿀 수 있을까요?

> **조건: 구현해야 할 추상 메서드가 오직 '하나'만 있는 인터페이스**

이를 **함수형 인터페이스(Functional Interface)** 라고 합니다. 추상 메서드가 2개 이상이면 어떤 메서드를 구현하는 것인지 알 수 없기 때문에 람다식을 쓸 수 없습니다.

* **`@FunctionalInterface` 어노테이션:** 인터페이스 위에 이 어노테이션을 붙이면, 컴파일러가 추상 메서드가 하나인지 검사해 줍니다. (만약 2개 이상이면 빨간 줄로 컴파일 에러를 띄워 실수를 방지합니다.)

### 자바가 미리 만들어둔 표준 함수형 인터페이스
개발자들이 매번 인터페이스를 정의하는 수고를 덜어주기 위해, 자바는 `java.util.function` 패키지에 자주 쓰는 형태를 미리 만들어 두었습니다.

| 인터페이스명             | 추상 메서드        | 매개변수  |     반환값     | 설명                     |
| :----------------- | :------------ | :---: | :---------: | :--------------------- |
| `Runnable`         | `run()`       |   X   |      X      | 매개변수도 없고, 반환 값도 없다.    |
| `Supplier<T>`      | `get()`       |   X   |    O (T)    | 아무것도 안 받고 값만 제공함 (공급자) |
| `Consumer<T>`      | `accept(T t)` | O (T) |      X      | 값을 받아서 사용만 함 (소비자)     |
| `Function<T, R>`   | `apply(T t)`  | O (T) |    O (R)    | 값을 받아서 결과로 매핑함 (함수)    |
| **`Predicate<T>`** | `test(T t)`   | O (T) | **boolean** | 값을 받아서 조건을 검사함 (판별자)   |

**[예제: Predicate 활용]**
조건 검사 로직을 람다식으로 간단히 구현하는 예제입니다.

```java
import java.util.function.Predicate;

public class LambdaExample {
    public static void main(String[] args) {
        // 문자열 길이가 5를 넘는지 검사하는 조건(동작)을 정의
        Predicate<String> isLongString = (str) -> str.length() > 5;

        System.out.println(isLongString.test("Hello")); // false
        System.out.println(isLongString.test("Lambda!!")); // true
    }
}
```

---

## 실전 활용 예제

### 컬렉션 정렬 (Comparator)
리스트를 정렬할 때 사용하는 `Comparator`도 추상 메서드가 하나인 함수형 인터페이스입니다.

```java
List<String> list = Arrays.asList("Orange", "Apple", "Banana");

// [Before] 익명 클래스 사용
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2); // 오름차순 정렬 로직
    }
});

// [After] 람다식 사용 (코드가 확 줄어듦)
Collections.sort(list, (s1, s2) -> s1.compareTo(s2));
```

### 스트림 API (Stream API)와의 환상적인 궁합
람다식의 진가는 자바의 강력한 데이터 처리 도구인 **Stream API**를 만났을 때 폭발합니다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 짝수만 골라서 출력하는 코드
numbers.stream()
       .filter(n -> n % 2 == 0)  // Predicate 람다식: 짝수 조건 걸기
       .forEach(n -> System.out.println(n)); // Consumer 람다식: 출력하기
```
이처럼 데이터를 마치 SQL처럼 선언적으로 처리할 때 람다식이 핵심적인 역할을 합니다.

---
## 람다식, 무조건 좋을까? (단점과 주의사항)

람다식은 강력하지만, 만능열쇠는 아닙니다. 무분별하게 사용할 경우 오히려 독이 될 수 있습니다.

### 1. 가독성 저하 (Lambda Hell)
람다식은 '짧고 간결한' 로직에 최적화되어 있습니다. 만약 로직이 3~4줄을 넘어가거나 복잡하다면, 람다식 안에 억지로 구겨 넣는 순간 코드를 이해하기 매우 어려워집니다.
-> **해결책:** 복잡한 로직은 별도의 메서드로 빼내고, **메서드 참조(`Class::method`)**를 사용하는 것이 좋습니다.

### 2. 디버깅의 어려움
람다식은 '익명 함수'입니다. 에러가 발생했을 때 스택 트레이스(Stack Trace)에 메서드 이름 대신 `lambda$main$0`과 같은 알 수 없는 이름이 찍힙니다. 이로 인해 에러가 발생한 정확한 위치나 원인을 추적하기가 일반 메서드보다 까다로울 수 있습니다.

### 3. 재귀 호출 부적합
람다식 내부에서는 자기 자신을 가리키는 이름이 없기 때문에, 재귀 함수(Recursion)를 구현하기에 적합하지 않습니다. 억지로 구현할 수는 있지만 코드가 매우 지저분해집니다.

---

## 요약 및 마무리

### 익명 클래스 vs 람다식 한눈에 보기

| 구분 | 익명 내부 클래스 (기존) | 람다식 (모던) |
| :--- | :--- | :--- |
| **사용 조건** | 모든 인터페이스/추상클래스 | **함수형 인터페이스만 가능** (추상 메서드 1개) |
| **가독성** | 코드가 길고 장황함 (Boilerplate code 많음) | 간결하고 핵심 로직에 집중 가능 |
| **목적** | 객체 생성을 명시적으로 보여줌 | 동작(함수) 자체를 정의하고 전달함 |

### 마무리
람다식 도입으로 자바는 더 간결하고 유연한 언어로 재탄생했습니다. 코드량이 줄어들어 가독성이 높아지고, 함수형 프로그래밍 스타일을 통해 병렬 처리(Stream)도 쉬워졌습니다.
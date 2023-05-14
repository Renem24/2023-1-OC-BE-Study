# 6주차

---

## 1. 추상 클래스와 인터페이스

### 추상 클래스

- 추상 메소드를 하나라도 갖고 있는 클래스는 반드시 추상 클래스로 선언해야함
- 추상 클래스는 인스턴스, 즉 객체를 만들 수 없음
(new 사용 불가)
- 추상 메소드는 하위 클래스에게 메소드의 구현을 강제함 → **오버라이딩** 강제
- 추상 메소드를 포함하는 클래스는 반드시 추상 클래스여야 함

### 인터페이스

- 클래스에서 구현할 수 있는 메서드 집합을 정의하는 참조 타입
- public 추상 메소드와 public 정적 상수만 가질 수 있음

## 2. static와 final, 불변 객체

### static

**static 블록**

- static 멤버만 사용 가능

다음의 경우에만 한 번 호출 됨

- 클래스의 정적 속성을 사용할 때
- 클래스의 정적 메소드를 사용할 때
- 클래스의 인스턴스를 최초로 만들 때

### final

- 최종이라는 의미 → **오버라이딩** 금지
(constant의 의미)

### 불변 객체

- 생성된 후에는 상태가 변하지 않는 객체
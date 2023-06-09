# 5주차

---

## 1. 객체지향 패러다임과 객체란?

### 객체 지향 패러다임

- 기계에 맞춰 사고하는 방식X
- 사람이 **현실세계를 인지하는 방식**으로 프로그램을 만들자는 아이디어 → 직관성 ↑

### 객체

- 현실세계의 개체를 프로그램에서 모델링한 것
- 속성과 행위(메소드)를 하나로 묶음
- 각 객체들의 공통적 속성, 행위로 분류 →  **클래스**
    
    ex) **직립보행을 하며 말을 하는 존재**를 **사람**이라고 분류한다.
    

## 2. 객체 지향의 4대 특성

**캡 상 추 다**

- **캡슐화 (Encapsulation)**
    - 정보 은닉 (information hiding)
    - 외부의 접근을 제어
    - 접근 제어자
        - private
            - -
            - 본인만 접근 가능
        - default
            - ~
            - 같은 패키지 내의 클래스만 접근 가능
        - protected
            - #
            - 상속 / 같은 패키지 내의 클래스에서 접근 가능
        - public
            - +
            - 모두 접근 가능
- **상속 ~~(inheritance)~~ → 재사용+확장(extend)**
    - 재사용
- **추상화 (Abstraction)**
    - 모델링
    - 구체적인 것을 분해해서 관심 영역에 대한 특성만을 가지고 재조합하는 것
- **다형성 (Polymorphism)**
    - 사용 편의
    - 하나의 인터페이스나 메소드 이름으로 다양한 형태의 구현 가능
    - 오버라이딩 & 오버로딩

## 3. 오버라이딩 vs 오버로딩

- 오버라이딩 (Overriding)
    - 같은 메소드 이름, 같은 인자 목록으로 상위(부모) 클래스의 메소드를 재정의
    - 덮어 쓰기
- 오버로딩 (Overloading)
    - 같은 메소드 이름, 다른 인자 목록으로 다수의 메소드를 중복 정의
    - 같은 메소드 이름에 다른 인자 목록을 사용하면 다른 메소드가 호출됨

## 4. call by value? call by reference?

### Call By Value

값에 의한 호출

- 그 값이 복사되며 두 개의 변수는 서로에게 영향 주지X
- 저장하고 있는 값을 그 값 자체로 해석

### Call By Reference

참조에 의한 호출

- 같은 객체를 참조하여 서로에게 영향을 줌
- 저장하고 있는 값을 그 주소로 해석
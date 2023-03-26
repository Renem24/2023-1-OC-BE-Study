# 1주차

---

### 프로그램 환경설정

## 프로젝트 환경설정

### 프로젝트 생성

- Java 11 (openjdk 11)
- IDE : IntelliJ 사용

### 스프링 프로젝트 생성

- 스프링 부트 스타터 사이트
    - [https://start.spring.io/](https://start.spring.io/)

- Project
    - 필요한 라이브러리를 땡겨오고 빌드하는 라이프 사이클까지 관리해주는 툴
    - Maven Project : 과거에 자주 사용
    - Gradle Project : 최근에 자주 사용

- Language - Java

- Spring Boot
    - SNAPSHOT : 만들고 있는 버전
    - M1 : 정식 릴리즈 되지 않은 버전
    - 2.7.10 사용 중

- Project Metadata
    - Group : 기업 도메인 명
    - Artifact : 빌드되어 나온 결과물
    - Name
    - Discription

- **Dependencies**
    - Spring Boot 기반으로 프로젝트 시작할 때
    - 어떤 라이브러리를 쓸 지
    
    - Spring Web
    - Thymeleaf : template engines의 한 종류(회사마다 다르)

GENERATE 버튼 눌러서 zip파일 다운

원하는 파일에 압축 해제

IntelliJ > Open or Import > 압축 해제한 파일 > build.gradle > Open > Open as Project

처음 연다면 외부에서 라이브러리 자동 다운로드됨(인터넷 필요) - 로딩 소요

### 프로젝트 설정

![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled.png)

- .idea
    - IntelliJ가 사용하는 설정 파일들
- gradle
    - gradle에 관련된, gradle을 사용하는 폴더
- src
    - main
        - java
            - 실제 패키지 & 소스파일들
        - resources
            - 실제 자바 코드 파일을 제외한, xml이나 properties 같은 설정파일 등
    - test
        - test코드와 관련된 소스들
    - 요즘에는 main과 test 분리

- build.gradle
    
    ![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled%201.png)
    
    - 버전 설정하고, 라이브러리 땡겨옴
    - repositories
        - mavenCentral 사이트에서 라이브러리 다운
    - dependencies
        - 처음에 추가한
        - thymeleaf
        - web
        - junit5 라이브러리(기본으로 추가됨)
        
- .gitignore
    - git 소스코드 관리
    - git에는 소스코드만 올라가고 나머지 빌드된 결과물 등은 올라가면 안됨

- gradlew
- gradlew.bat
- settings.gradle

- src > main > java > hello > hellospring > HelloSpringApplication
    - 실행
    - Tomcat started on port(s) : 8080 → localhost:8080

main 메소드 실행 → SpringApplication.run(HelloSpringApplication.class, args);

→ SpringBootApplication 실행 → 내장된 tomcat (웹서버)를 자체적으로 띄움, 스프링 부트가 같이 올라옴

- 번외
    - IntelliJ 사용 시 : Java를 직접 실행하는 것이 아니라 gradle을 통해서 실행될 때가 있음
    - Preferences > Gradle 검색 > Build and run using & Run tests using > IntelliJ IDEA로 변경
    - gradle을 통해서 실행하면 속도가 늦어짐
    

## 라이브러리 살펴보기

- 처음에 선택한 라이브러리
    - thymeleaf - template engine(html 생성)
    - web
    - test(자동)

- External Libraries
    - 땡겨온 라이브러리 - 매우 매우 많다. (서로가 서로를 땡겨기 때문)
    - Maven Project나 Gradle Project 같은 툴들은 의존 관계를 관리해줌
    
    ex) spring-boot-starter-web → tomcat
    

- 왼쪽 밑에 아이콘을 눌러 gradle을 누르면 Dependencies(의존관계)를 볼 수 있음

- 실제 현업에서는 sout으로 출력X
- log로 출력

### 스프링 부트 라이브러리

- spring-boot-starter-web
    - spring-boot-starter-web-tomcat : 톰캣 (웹 서버)
- spring-boot-starter-thymeleaf : 타임리즈 template engine(view)
- spring-boot-starter(공통) : 스프링 부트 + 스프링 코어 + 로깅
    - spring-boot
        - spring-core
    - spring-boot-starter-logging
        - logback, slf4j

### 테스트 라이브러리

- spring-boot-starter-test
    - junit : 테스트 프레임워크
    - mockito : 목 라이브러리
    - assertj : 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
    - spring-test : 스프링 통합 테스트 지원

## View 환경설정

### Welcome Page 만들기

- src > main > resources > static > index.html 생성
    - 정적 페이지
- 스프링 부트가 제공하는 Welcome Page 기능
- Spring Boot는 static에 있는 index.html를 찾고, 없으면 index 템플릿을 찾음

- 템플릿 엔진(thymeleaf)을 사용하면 Welcome Page를 바꿀 수 있음

### 동적 페이지 만들기

- 컨트롤러 Controller
    - src > main > java > hello.hellospring > controller 생성 > HelloController 생성
    
    ```java
    @Controller
    public class HelloController {
    
        @GetMapping("hello")
        public String hello(Model model){
            model.addAttribute("data", "hello!!");
            return "hello";
        }
    }
    ```
    
    - `@Controller (annotation)`
    - `@GetMapping(”hello”)`
    - 웹 어플리케이션에서 /hello라고 들어오면 hello 메소드 실행
        - `@GetMapping("hello")
            public String hello(Model model){
                model.addAttribute("data", "hello!!");
                return "hello";
            }`
    - `model.addAttribute(”data”,”hello!!”);`
    - key : data
    - value : hello!!

- src > main > resources > templates > hello.html 생성
    - `<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>`
    - th : thymeleaf
    - data : 위의 value(hello!!)가 key(data)로 치환됨

![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled%202.png)

- 웹 브라이저에서 localhost:8080/hello를 던지면
- 스프링 부트가 내장한 톰캣이라는 웹서버에서
- 스프링에게 물어봄
- helloController에 있는 hello 메소드가 실행됨 (hello url에 매칭됨)
    - `@GetMapping(”hello”)`(Get방식)
    - model(data : hello!!)
        - 스프링이 model을 만들어서 넣어줌
        - model에 data가 hello!!라고 넣음
    - return hello
        - resources > templates 아래의 hello.html을 찾아서 렌더링
            - thymeleaf 템플릿 엔진 처리
        - 스프링 부트 기본세팅 - resources > templates폴더 아래에서 search 해서 
        thymeleaf 템플릿 엔진 처리
- 컨트롤러에서 return값으로 문자를 반환하면 viewResolver가 화면을 찾아서 처리함
    - 스프링 부트 템플릿 엔진 기본 viewName 매핑
        
        resources:templates/ + viewName + .html
        
- `<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>`
    - data : model의 key 값 (key의 위치에 value가 들어감)
    

## 빌드 & 실행하기

- cmd 실행
- cd hello-spring
- ./gradlew
- gradlew
- gradlew build
- cd build
- cd libs
- java -jar hello-spring-0.0.1-SNAPSHOT.jar

---

### 스프링 웹 개발 기초

## 정적 컨텐츠

- 서버에서 하는거 없이 파일을 웹브라우저로 그대로 내려주는 방식

- src > resources > static > hello-static.html 생성
- url : localhost:8080/hello-static 으로 접속

### 정적 컨텐츠 정리

![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled%203.png)

- 웹브라우저에서 localhost:8080/hello-static
- 내장 톰캣 서버에서 요청 받음 → 스프링에 넘김
- 컨트롤러 쪽에서 hello-static을 찾아봄(컨트롤러가 우선순위를 가짐) → 매핑되는 컨트롤러 없음
- 내부의 resources: static/hello-static.html을 찾음 → 찾으면 웹브라우저에 반환

## MVC와 템플릿 엔진

- 가장 많이 하는 방식
- jsp, tsp : 템플릿 엔진
- 서버에서 웹브라우저로로 html을 그냥 주지 않고, 서버에서 프로그래밍해서(동적으로 바꿔서) 내려주는 방식

- MVC : Model, View, Controller
- View - 화면을 그리는데 모든 역량을 집중해야함
- Model, Controller - 비즈니스 로직과 관련있거나 내부적인 걸 처리하는데 집중해야함
- model에 담으면 view에서 렌더링할 때 사용

```java
		@GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
		}
```

- `public String helloMvc(@RequestParam("name") String name, Model model)`
    - 외부에서 url parameter 입력받기
    - `model.addAttribute("name", name);`
    - “name” : key
    - name :   name

- `<p th:text="'hello ' + ${name}">hello! empty</p>`
    - hello! empty는 경로로 접근할 때 보여주는 값 (템플릿 엔진으로 동작 안할 때)
    - 서버를 타고 돌면(템플릿 엔진 동작)
        - `'hello ' + ${name}` 로 내용물이 바뀜

- ctrl + p 로 옵션 확인
    - required가 기본이 true로 설정됨 → url로 값을 넘겨야함
    - localhost:8080/hello-mvc?name=spring!!!!
    - name = spring!!!!이 model에 전달됨

### MVC, 템플릿 엔진 정리

![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled%204.png)

- 웹브라우저에서 localhost:8080/hello-mvc를 넘기면
- 스프링 부트에 내장된 톰캣 서버가 스프링에게 이를 던짐
- 스프링은 helloController의 hello-mvc메소드에 매핑되어 있는 것을 찾고 메소드를 호출
    - return hello-template
    - model(name : spring)
    - 두 정보를 스프링에 넘겨줌
- viewResolver(view를 찾아주고, 템플릿 엔진 연결하는 역할)가
- templates/hello-template.html을 찾아서 thymeleaf 템플릿 엔진에게 처리하라고 넘김
- thymeleaf 템플릿 엔진이 렌더링을 해서
- 변환을 한 html을 반환 (정적에서는 변환 X)

## API

- 안드로이드, ios 또는 서버끼리 통신할 때
- JSON 포맷으로 전달

```java
		@GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name; // "hello spring"
    }
```

- @ResponseBody
    - html의 body 말고, http의 헤더 + 바디 부분에서
    - return뒤의 값(”hello ” + name)을 직접 넣어주겠다는 의미
    - view 없이 이 문자(”hello ” + name)가 그대로 넘어감 (html 태그 X)

```java
		@GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello(); // Hello클래스의 객체hello 생성
        hello.setName(name); // 객체가 오면 JSON 방식 사용이 디폴트
        return hello;
    }

    static class Hello { // static를 붙여 주면 class내에서 또 class 정의 가능
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
```

- JSON 방식으로 전달됨
    - JSON 방식 : *`{"key" : "value"}`  방식*
        - 심플함
        - 최근에 자주 사용됨
        - 기본 디폴트로 설정되어있음
    - XML 방식 : <html> </html>
        - 무겁고
        - 열고 닫고 다 써야함

### API 정리 (@ResponseBody 사용 원리)

![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20f1b8d3087aff43749c55561272f0cb0c/Untitled%205.png)

- 웹브라우저에서 localhost:8080/hello-api를 치면(넘기면)
- 스프링 부트에 내장된 톰캣 서버가 스프링에 이를 던지면
- hello-api를 찾고 @ResponseBody가 붙어있는 걸 봄
    - return hello(name : spring) ⇒ 객체 를 넘김
- HttpMessageConverter가 동작
    - 기존에는 viewResolver가 동작했었음
    - 문자인 경우 - StringConverter가 동작
        - StringHttpMessageConverter
    - 객체인 경우 - JsonConverter가 동작 → JSON 형태로 바꿈
        - MappingJackson2HttpMessageConverter
- 웹브라우저에 JSON 형태로 반환
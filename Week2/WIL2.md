# 2주차

---

### 회원 관리 예제 - 백엔드 개발

- ctrl + shift + T : 테스트 케이스 작성

## 비즈니스 요구사항

- 데이터
    - 회원 ID
    - 회원 이름
- 기능
    - 회원 등록
    - 회원 조회
- 아직 데이터 저장소(DB)가 선정되지 않음 (가상의 시나리오)

### 일반적인 웹 애플리케이션 계층 구조

![Untitled](2%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20e18a1d34d87c42d8bdb799306b24a8c0/Untitled.png)

- **컨트롤러** : 웹 MVC의 컨트롤러 역할
- **서비스** : 핵심 비즈니스 로직 구현
    - ex) 회원 중복 가입 X 등
    - 비즈니스 도메인 객체를 가지고 핵심 비즈니스 로직을 구현한 객체
- **리포지토리** : 데이터베이스에 접근, 도메인 객체를 DB 에 저장하고 관리
- **도메인** : 비즈니스 도메인 객체
    - 회원, 주문, 쿠폰 등 주로 데이터 베이스에 저장하고 관리되는 것들

### 클래스 의존관계

![Untitled](2%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20e18a1d34d87c42d8bdb799306b24a8c0/Untitled%201.png)

- 아직 데이터 저장소가 선전되지 않음 → 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
- 데이터 저장소는 RDB, NoSQL 등 다양한 저장소를 고민중인 상황으로 가정
- 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용
- Interface - MemberRepository
    - 회원 저장
    

## 회원 도메인 & 리포지토리

### 회원 도메인

- src > main > java > hello.hellospring > domain 생성 > Member(클래스) 생성

```java
public class Member {

    private Long id; // 회원 ID
    private String name; // 회원 이름

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

### 리포지토리

### MemberRepository

- src > main > java > hello.hellospring > repository 생성 > MemberRepository(interface) 생성

```java
public interface MemberRepository {
    Member save(Member member); // 회원이 저장소에 저장됨
    Optional<Member> findById(Long id); // 찾아오기
    Optional<Member> findByName(String name); // 찾아오기
    List<Member> findAll(); // 모든 회원 리스트 반환
}
```

- `Member save(Member member);`
    - 회원을 저장소에 저장
- `Optional<Member> findById(Long id);`
    - findById로 저장소에서 찾아올 수 있음
- `Optional<Member> findByName(String name);`
    - findByName으로 저장소에서 찾아올 수 있음
- `List<Member> findAll();`
    - 저장된 모든 회원 리스트를 반환해줌

- Optional : findById와 findByName으로 가져왔는데 null(없음)인 경우를 처리하기 위함

### MemoryMemberRepository

- src > main > java > hello.hellospring > repository > MemoryMemberRepository(클래스) 생성

```java
public class MemoryMemberRepository implements MemberRepository{

    private static Map<Long, Member> store = new HashMap<>();
    // ctrl + spacebar
    // alt + enter
    private static long sequence = 0L; // 0, 1, 2, ... 키값을 생성

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(),member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values()); // store.values() : member들
    }

    public void clearStore() {
        store.clear();
    }
}
```

- `public class MemoryMemberRepository implements MemberRepository{`
    - 위의 MemberRepository를 implements하고
    - alt + enter : implement method

- `private static Map<Long, Member> store = new HashMap<>();`
    - save할 때, 저장할 위치
    - 실무에서는 동시성 문제 고려해야함
- `private static long sequence = 0L;`
    - 키 값을 생성
    - 0, 1, 2, …

```java
		@Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(),member);
        return member;
    }
```

- `member.setId(++sequence);`
    - store에 넣기 전, 미리 id 설정해줌
        
        (name은 사용자가 입력하는 값, id는 시스템이 부여)
        
- `store.put(member.getId(),member);`
    - store에 넣어줌
        
        (Map에 저장)
        
- `return member;`
    - 스펙에 따라 저장된 결과 반환

```java
		@Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }
```

- `return Optional.ofNullable(store.get(id));`
    - `return store.get(id);` 로 충분하지만, null 반환 가능성 고려해야함
        
        → `Optional.ofNullable` 로 감싸서 반환
        

```java
    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }
```

- `.filter(member -> member.getName().equals(name))`
    - getName()이 parameter로 넘어온 name과 같은지 확인
        
        → 같은 경우에만 필터링 & 찾으면 반환
        
- `.findAny();`
    - 하나라도 찾기
- 결과가 Optional로 반환됨 (찾지 못한 경우에는 null이 반환됨)

```java
    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values()); // store.values() : member들
    }
```

- `return new ArrayList<>(store.values());`
    - store.values() : member들 (Map에 저장되어있는)

## 회원 리포지토리 테스트

- 개발한 기능을 실행해서 테스트 할 때,  자바의  main메소드를 실행하거나, 웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행
- 단점
    - 준비하고 실행하는데 오래걸림
    - 반복 실행이 어려움
    - 여러 테스트를 한 번에 실행하기 어려움

⇒ 자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제 해결

- 회원 리포지토리라는 클래스가 내가 원하는 대로 동작하는지 검증하는 과정 → 테스트 케이스 작성
- 코드를 코드로 검증

### 테스트 케이스

- src > test > java > hello.hellospring > repository 생성 > MemoryMemberRepositoryTest(클래스) 생성

```java
public class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository = new MemoryMemberRepository();

    @AfterEach
    public void afterEach() {
        repository.clearStore();
    }

    @Test
    public void save() {
        Member member = new Member();
        member.setName("spring"); // ctrl + shift + enter

        repository.save(member);
        Member result = repository.findById(member.getId()).get();
        // System.out.println("result = " + (result == member));
        // Assertions.assertEquals(member, result); // 기대하는 값 : member
        assertThat(member).isEqualTo(result);
    }

    @Test
    public void findByName() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member(); // rename : Shift + F6
        member2.setName("spring2");
        repository.save(member2);

        Member result = repository.findByName("spring1").get();

        assertThat(result).isEqualTo(member1);
    }

    @Test
    public void findAll() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);
    }

}
```

```java
    @Test
    public void save() {
        Member member = new Member();
        member.setName("spring"); // ctrl + shift + enter

        repository.save(member);
        Member result = repository.findById(member.getId()).get();
        // System.out.println("result = " + (result == member));
        // Assertions.assertEquals(member, result); // 기대하는 값 : member
        assertThat(member).isEqualTo(result);
    }
```

- main 메소드 처럼 작성
- `Member member = new Member();`
    
    `member.setName("spring");`
    
    - 저장이 잘되는지 test
- `repository.save(member);`
    - repository에 member를 save
- `Member result = repository.findById(member.getId()).get();`
    - `repository.findById(member.getId())`
        - findById로 내가 넣은 멤버가 제대로 들어갔는지 꺼내보기
    - get() 을 사용해 꺼내기
- `// System.out.println("result = " + (result == member));
 // Assertions.assertEquals(member, result); // 기대하는 값 : member
 assertThat(member).isEqualTo(result);`
    - 내가 넣은 member와 result로 가져오는 값이  같은지 검증

```java
    @Test
    public void findByName() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member(); // rename : Shift + F6
        member2.setName("spring2");
        repository.save(member2);

        Member result = repository.findByName("spring1").get();

        assertThat(result).isEqualTo(member1);
    }
```

```java
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member(); // rename : Shift + F6
        member2.setName("spring2");`
        repository.save(member2);
```

- member1 - ”spring1” 회원이 새로 가입
- member2 - ”spring2” 회원이 새로 가입

- `Member result = repository.findByName("spring1").get();`
    - get을 사용해 꺼내기
- `assertThat(result).isEqualTo(member1);`

```java
    @Test
    public void findAll() {
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);
    }
```

- member1 - ”spring1” 회원이 새로 가입
- member2 - ”spring2” 회원이 새로 가입

- `List<Member> result = repository.findAll();`
    - result에 전체를 반환
- `assertThat(result.size()).isEqualTo(2);`

### 테스트 케이스의 특징

- 각 테스트는 따로 돌려볼 수 있음
- 모든 테스트는 순서에 상관없이 따로 동작하도록 만들어야함
    
    (순서에 의존적으로 설계 X)
    
    → Test가 하나 끝날 때마다 리포지토리를 clear해주어야함
    

```java
		@AfterEach
    public void afterEach() {
        repository.clearStore();
    }
```

- 각 메소드가 실행이 끝날때마다 실행됨

- MemoryMemberRepository에서 clearStore() 메소드 생성

```java
		public void clearStore() {
        store.clear();
    }
```

### TDD 테스트 주도 개발

- 테스트 케이스를 먼저 작성 → 구현 클래스 작성

## 회원 서비스

- 회원 서비스 : 회원 리포지토리와 도메인을 활용 → 비즈니스 로직 작성
- 서비스 클래스는 비즈니스에 가까운 용어를 사용해야함
    
    (서비스 클래스는 비즈니스에 의존적으로 설계)
    
- 리포지토리는 서비스 보단 단순히 기계적으로 설계

- src > main > java > hello.hellospring > service 생성 > MemberService(클래스) 생성

```java
public class MemberService {

    private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    /*
        회원가입
    */
    public Long join(Member member) {
        // 같은 이름이 있는 중복 회원 X
        // ctrl + alt + V
        validateDuplicateMember(member); // 중복 회원 검증
        // ctrl + alt + shift + T
        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }

    /*
        전체 회원 조회
    */
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

- `private final MemberRepository memberRepository;`
    - 회원 서비스를 만들려면 회원 리포지토리가 필요함
    
- 회원가입

```java
    public Long join(Member member) {
        // 같은 이름이 있는 중복 회원 X
        // ctrl + alt + V
        validateDuplicateMember(member); // 중복 회원 검증
        // ctrl + alt + shift + T
        memberRepository.save(member);
        return member.getId();
    }
```

- `memberRepository.save(member);`
    - save를 호출
- `return member.getId();`
    - 임의로 id를 반환하기로 함

- `validateDuplicateMember(member);`
    - 같은 이름이 있는 중복 회원 배제
    - 따로 함수로 구현

```java
    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }
```

- `memberRepository.findByName(member.getName())`
    - memberRepository에서 findByName으로 먼저 찾아보기
- `.ifPresent(m -> {`
    - ifPresent : 값이 있으면 (null이 아니면) 동작
        
        → member에 값이 있으면
        
- `throw new IllegalStateException("이미 존재하는 회원입니다.");`

- `Optional<Member> result = memberRepository.findByName(member.getName());
result.ifPresent(m -> {`
    
    ⇒
    
    `memberRepository.findByName(member.getName())
            .ifPresent(m -> {`
    
    - findByname을 한 결과가 Optional임
    - Optional member.ifPresent() 실행

- 전체 회원 조회

```java
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }
```

- 단일 회원 조회 (id를 통해)

```java
    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
```

## 회원 서비스 테스트

- 테스트는 한글로 이름을 적어도 상관 X
- ctrl + shift + T : 테스트 케이스 작성

- src > test > java > hello.hellospring > service 생성> MemberServiceTest(클래스) 생성

```java
class MemberServiceTest {

    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }

    @AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }

    @Test
    void 회원가입() {
        // given 뭔가 주어졌을 때
        Member member = new Member();
        member.setName("hello");

        // when 이런 상황이 나왔는데
        Long saveId = memberService.join(member);

        // then 결과가 이게 나와야함
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    public void 중복_회원_예외() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");
        // when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join((member2)));

        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");

//        try {
//            memberService.join(member2);
//            fail();
//        } catch (IllegalStateException e) {
//            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
//        }

        // then
    }

    @Test
    void findMembers() {
    }

    @Test
    void findOne() {
    }
}
```

- 회원가입

```java
    @Test
    void 회원가입() {
        // given 뭔가 주어졌을 때
        Member member = new Member();
        member.setName("hello");

        // when 이걸 실행했을 때
        Long saveId = memberService.join(member);

        // then 결과가 이게 나와야함
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }
```

- given, when, then 문법 → 테스트에서 사용하는 문법
    - given : 뭔가가 주어졌는데, 어떤 데이터 기반인지
    - when : 이걸 실행했을 때, 어떤 걸 검증해야 하는지
    - then : 결과가 이게 나와야함, 검증하는 부분

given

- `Member member = new Member();
 member.setName("hello");`

when

- `Long saveId = memberService.join(member);`
    - return은 저장한 id가 나오기로 했음

then

- `Member findMember = memberService.findOne(saveId).get();
 assertThat(member.getName()).isEqualTo(findMember.getName());`
    - 우리가 저장한 게 리포지토리에 있는게 맞는지 검증
    - name을 검증

- 테스트는 정상 flow보다 예외 flow가 훨씬 중요

- 회원가입 (중복) - 예외

```java
    @Test
    public void 중복_회원_예외() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");

        // when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> 
						memberService.join(member2));

//        try {
//            memberService.join(member2);
//            fail();
//        } catch (IllegalStateException e) {
//            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
//        }

				// then
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
    }
```

- `Member member1 = new Member();
member1.setName("spring");`

`Member member2 = new Member();
member2.setName("spring");`
    - 같은 Name → 중복

- try-catch

```java
				memberService.join(member1);
				try {
            memberService.join(member2);
            fail();
        } catch (IllegalStateException e) {
            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
        }
```

- `memberService.join(member2);`
    - 중복 발생
- MemberService클래스의 메소드에서 정의한
    - `throw new IllegalStateException("이미 존재하는 회원입니다.");`
    - `IllegalStateException` 이 터져야함
- 예외 발생에도 catch로 넘어가지 않으면 `fail();`
    
    (”예외가 발생해야 합니다.”)
    
- `assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");`
    - 예외 메세지가 맞는지 검증

- assertThrow

```java
        // when
        memberService.join(member1);
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> 
						memberService.join(member2));				

				// then
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
```

- `IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));`
    - 이 로직 (memberService.join(member2))을 태울 때,
    - 이 예외 (IllegalStateException.class)가 터져야 함

- `assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");`
    - 예외 메세지가 맞는지 검증

- 테스트

```java
    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }

    @AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }
```

- afterEach()를 써야하는데 MemberSevice 밖에 없으므로
- `MemoryMemberRepository memberRepository;` 를 생성해줘야함

- `MemberService memberService = new MemberService();`
    
    `MemoryMemberRepository memberRepository = new MemoryMemberRepository();`
    
    - MemberSevice 클래스에서 사용하는 MemoryMemberRepository과
    - 테스트에서 사용하는 MemoryMemberRepository가 다른 리포지토리(인스턴스)임
    - 같은 리포지토리로 테스트해야함 → 같은 것을 사용하도록 바꿔야함

- MemberService 클래스

```java
		private final MemberRepository memberRepository;

    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
```

- `private final MemberRepository memberRepository = new MemberReporsitory();`
    - 직접 new해서 생성하는 것이 아니라
- `public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }`
    - 외부에서 넣어주도록 바꿈

- 테스트

```java
    MemberService memberService;
    MemoryMemberRepository memberRepository;

    @BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }
```

- 각 테스트를 실행하기 전에(BeforeEach) MemoryMemberRepository를 만들고
    
    `MemoryMemberRepository memberRepository;` 에 넣어놓고
    
- memberRepository를 MemberService에 넣어줌
- MemberService 입장에서 보면 직접 new를 하지 않고 memberRepository를 외부에서 넣어줌
    
    ⇒ Dependent Injection (DI)
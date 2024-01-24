# 📍main topic : 스프링 빈 주입 방법에 대해 설명하시오


## 1. 필드 주입(@Autowired)
```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private MemberService memberService;

}
```

필드에 `@Autowired`어노테이션을 사용하여 의존성을 주입하는 방법.

### pros and cons
pros) 
+ 어노테이션 하나만 추가해서 의존성을 주입 할 수 있으므로 사용이 간단함

cons)
+ 외부에서 변경이 불가능하기때문에 DI 컨테이너없이는 테스트 할 수없음!
+ 객체의 생성이 끝난후 의존성 주입이 일어남.

> 대신 실제코드와 상관없는 테스트 코드에 사용하기에 적합하고 스프링 빈 등록에 목적이 있는
> Configuration에서도 사용 적합

## 2. setter 주입
```java
@Component
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
    
    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

만약 스프링 컨테이너를 실행한다면, 의존성 주입의 순서는 다음과 같다.
```
1. MemberRepository, DiscountPolicy 빈 등록
2. OrderServiceImpl 객체 생성
3. setter 메서드 호출 
4. OrderServiceImpl 빈 등록
```

### pros and cons
pros) 
+ 의존관계를 런타임 중에 동적으로 변경가능

cons)
+ 의존관계를 런타임 중에 동적으로 변경할 일이 거의 없음. 오히려 변하지 않아야함.
+ setter메서드를 `public`으로 열기 때문에 캡슐화 불가

## 3. 생성자 주입
```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired //생성자가 1개만 있다면 생략해도 됨
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy
            discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```
### 특징
+ 생성자 호출 시점에 딱 1번 의존성 주입되는 것이 보장된다. 
+ 불변, 필수 의존관계에 주로 사용한다.
+ 객체의 생성과 의존성 주입의 시점이 같다.

### 🔥 생성자 주입을 사용해야하는 이유
+ 객체의 불변성 확보
+ 테스트 코드의 작성
  + 순수 자바코드로 단위 테스트 작성하는것이 가능해진다.
+ final 키워드 작성 및 Lombok과의 결합
  + 컴파일 시점 누락 확인
  + Lomok 의 `RequiredArgsConstructor`와 결합하여 간결하게 작성 가능
+ 순환 참조 에러 방지
  + 스프링 부트 2.6 버전부터 필드 주입을 하더라도 에러 발생.


[인프런 김영한님 강의 - 스프링_고급](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)



# 11 스프링 AOP - 포인트컷

포인트컷 표현식이란 AspectJ pointcut Expression 을 말한다.

이장에서는 포인트컷 표현식을 자세히 배운다.



## 11-1 포인트컷 지시자

포인크서 지시자(PointCut Designator) 줄여서 PCD 라고도 하는데 포인트컷이 적용되는 대상(조인포인트)을 지정한다.



#### 포인트컷 지시자의 종류

+ `execution` 메소드 실행 조인포인트를 매칭한다. 가장 많이 사용하고, 복잡하다.

+ `whithin` 특정 타입 내의 조인포인트를 매칭한다.

+ `args` 인자가 주어진 타입의 인스턴스를 조인포인트로 매칭한다.

+ `this` 스프링 빈 객체(프록시) 를 대상으로 한다.

+ `target` Target 객체(프록시가 가르키는 실체 대상) 를 대상으로 하는 조인포인트

+ `@target` 실행 객체의 클래스에 주어진 타입의 애노테이션이 있는 조인포인트

+ `@within` 주어진 애노테이션이 있는 타입내의 조인포인트

+ `annotation`메서드가 주어진 애너테이션이 있는 조인포인트

+ `@args` 인수 가 애너테이션일 갖는 조인포인트

+ `bean` 스프링 전용 포인트컷 지시자 빈의 이름으로 지정



사실 `execution` 이외 에는 잘 사용하지 않는다. 따라서 `execution` 위주로 살펴볼것이다.

<br>

## 11-2 execution

#### execution 의 형태

`execution(modifiers-pattern? ret-type-pattern declaring-type-patternnamepattern(param-pattern)
 throws-pattern?)` 

-> execution(접근제어자? 반환타입 선언타입?메서드이름(파라미터) 예외?)

? 는 생략 가능하다.



#### 예제 - 가장 정확한 포인트컷

```java
@Test
@DisplayName("가장 정확한 포인트컷")
void exactMatch() {
    //public java.lang.String hello.aop.memeber.MemberServiceImpl.hello(java.lang.String)
    pointcut.setExpression("execution(public String hello.aop.memeber.MemberServiceImpl.hello(String))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}
```

포인트컷 표현식은 생각보다 많이 직관적이다. 메서드 정의할 때를 생각하면 되니깐.

이렇게 정확하게 정의하면 딱 하나의 메서드에만 적용된다는것을 유추할 수 있다.



#### 예제 - 가장 생략한 포인트컷

```java
@Test
@DisplayName("가장 생략한 포인트컷")
void allMatch() {
    pointcut.setExpression("execution(* *(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}
```

접근제어자, 패키지+타입,예외을 생략했다. 

+ 반환타입 : `*`

+ 메서드명 : `*`

+ 파라미터 : `(..)`

`*` 은 아무 이름이나 들어와도 된다는 뜻이고, `(..)` 는 파라미터의 타입과 수과 상관 없다는 뜻이다.

즉, 이때는 모든 빈객체가 프록시로 등록 될 것임을 유추할 수 있다.



#### 예제 - 메서드 이름 매칭 포인트컷

```java
@Test
void nameMatch() {
    pointcut.setExpression("execution(* hello(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

@Test
void nameMatchStar1() {
    pointcut.setExpression("execution(* hel*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

@Test
void nameMatchStar2() {
    pointcut.setExpression("execution(* *el*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}
```

메서드명 앞뒤로 `*` 가 들어올 수 있다.



#### 예제 - 패키지 매칭 관련 포인트컷

```java
@Test
void packageExactMatch() {
    pointcut.setExpression("execution(* hello.aop.memeber.MemberServiceImpl.hello(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

@Test
void packageExactMatch2() {
    pointcut.setExpression("execution(* hello.aop.memeber.*.*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

@Test
void packageExactFalse() {
    pointcut.setExpression("execution(* hello.aop.*.*(..))"); //.. 을 쓰지않으면 정확히 일치해야함
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isFalse();
}

@Test
void packageMatchSubPackage() {
    pointcut.setExpression("execution(* hello.aop.memeber..*.*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}
```

여기서 조심해야할 것은 `.` 과 `..` 의 차이이다.

`.` 을 쓰면 정확하게 해당 위치의 패키지를 의미한다. 하위패키지는 포함하지 않는다.

`..` 을 쓰면 해당위치의 패키지와 하위패키지를 모두 포함한다.

<br>

## 11-3 execution2

#### 예제 - 타입 매칭 -부모타입 허용

```java
@Test
void typeExactMatch() {
    pointcut.setExpression("execution(* hello.aop.memeber.MemberServiceImpl.*(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

@Test
void typeExactSuperType() {
    pointcut.setExpression("execution(* hello.aop.memeber.MemberSerivce.*(..))"); //부모 타입도 일치함.
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

```

-포인트컷 표현식에 타입으로 부모 타입을 명시해도 자식타입은 당연히 적용된다. 



#### 예제 타입매칭 - 부모 타입에 있는 메서드만 허용

```java
@Test
void typeMatchNoSuperTypeMethodFalse() throws NoSuchMethodException {
    pointcut.setExpression("execution(*
    hello.aop.member.MemberService.*(..))");
    Method internalMethod = MemberServiceImpl.class.getMethod("internal",String.class);
    assertThat(pointcut.matches(internalMethod,MemberServiceImpl.class)).isFalse();
}
```

딱히 다른 것없고, 표현식에 부모 타입을 선언한 경우 자식 클래스에 부모타입에 없는 메서드는 해당이 안된다.



#### 예제 파라미터 매칭

```java
//String 타입의 파라미터 허용
@Test
void argsMatch() {
    pointcut.setExpression("execution(* *(String))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();

}

@Test
void argsMatchNoArgs() {
    pointcut.setExpression("execution(* *())");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isFalse();
}

//정확히 하나의 파라미터허용 대신, 모든 타입 허용
@Test
void argsMatchStar() {
    pointcut.setExpression("execution(* *(*))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}


//숫자와 무관하게 모든 파라미터, 모든 타입 허용
//(), (Xxx) ,(Xxx,Xxx)
@Test
void argsMatchAll() {
    pointcut.setExpression("execution(* *(..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

//String 타입으로 시작, 숫자와 무관하게 모든 파라미터 ,모든 타입허용
//(String), (String,Xxx) , (String Xxx,Xxx)
@Test
void argsMatchComplex() {
    pointcut.setExpression("execution(* *(String, ..))");
    assertThat(pointcut.matches(helloMethod,MemberServiceImpl.class)).isTrue();
}

```

+ `(String)`  정확히 String 타입 파라미터, 단하나

+ `()`  파라미터가 없어야함

+ `(*)` 하나의 파라미터, but 모든 타입허용

+ `(* , * )` 정확히 두개의 파라미터,but 모드타입허용

+ `(..)`  파라미터가 없거나, 있거나 , 개수 타입 상관 없이 모두 허용

+ `(String, ..)` String 타입으로 시작해야하고 그뒤에는 타입 , 갯수 상관 없음





## 11-4 매개변수 전달

```java
    @Around("allMember() && args(arg,..)")
    public Object logArgs1(ProceedingJoinPoint joinPoint, Object arg) throws Throwable {
        log.info("[logArgs1]{}, arg={}", joinPoint.getSignature(), arg);
        return joinPoint.proceed();
    }

    @Before("allMember() && args(arg,..)")
    public void logArgs3(String arg) {
        log.info("[logArgs3] arg ={}",arg);
    }

    @Before("allMember() && this(obj)")
    public void thisArgs(JoinPoint joinPoint , MemberSerivce obj) {
        log.info("[this]{}, obj = {}",joinPoint.getSignature(), obj.getClass() );
    }

    @Before("allMember() && target(obj)")
    public void targetArgs(JoinPoint joinPoint , MemberSerivce obj) {
        log.info("[target]{}, obj = {}",joinPoint.getSignature(), obj.getClass() );
    }

    @Before("allMember() && @target(annotation)")
    public void atTarget(JoinPoint joinPoint , ClassAop annotation) {
        log.info("[@target]{}, obj = {}",joinPoint.getSignature(), annotation );
    }

    @Before("allMember() && @within(annotation)")
    public void atWithin(JoinPoint joinPoint , ClassAop annotation) {
        log.info("[@whithIn]{}, obj = {}",joinPoint.getSignature(), annotation );
    }

    @Before("allMember() && @annotation(annotation)")
    public void atWithin(JoinPoint joinPoint , MethodAop annotation) {
        log.info("[@annotation]{}, annotationValue = {}",joinPoint.getSignature(), annotation.value() );
    }
```

포인트컷 과 매개변수의 이름을 맞추어야함

메서드에 파라미터 타입을 지정하면 그 지정한 타입으로 제한됨. 별건아니고 

`args(arg)`  이고 메서드에 파라미터가 `String arg` 으로 되어있다면 사실상 이것이다.

`args(String)` 표현식이 아닌 메서드 파라미터에 제한되는 타입을 지정한다는 말.

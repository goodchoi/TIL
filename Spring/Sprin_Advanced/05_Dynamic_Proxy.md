[인프런 김영한님 강의 - 스프링_고급](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard)

# 05 동적 프록시 기술

#### 들어가기앞서

저번장까지해서 기존 코드 변경 없이 추가 동작 및 제어가 가능하게하는 프록시 기술에 대해 알아보았다.

하지만 여전히 남은 문제점은 프록시 클래스를 반복해서 만들어야하는가에 대한 의문이였다. 이번장에서는 그 의문을 해결하기위해 동적 프록시 기술을 배울 것이다.

기본적으로 동적 프록시 기술에는 크게 두가지가 있다.

+ 자바 기본 JDK 동적 프록시 기술

+ CGLIB 프록시 생성 오픈소스 기술

이 두 기술을 이해 하려면 먼저 리플렉션 기술을 이해해야한다.

## 05-1 리플렉션 기술

#### 예제

```java
@Test
void reflection1() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, IllegalAccessException {
    //클래스정보
    Class classHello = Class.forName("hello.proxy.jdkdynmaic.ReflectionTest$Hello");

    Hello target = new Hello();

    //callA 메서드 정보
    Method methodCallaA = classHello.getMethod("callA");
    Object result1 = methodCallaA.invoke(target); //target의 인스턴스의 메서드를 호출함.

    log.info("result1={}",result1);

    //callB 메서드 정보
    Method methodCallaB = classHello.getMethod("callB");
    Object result2 = methodCallaB.invoke(target); //target의 인스턴스의 메서드를 호출함.

    log.info("result1={}",result2);
}
```

`Class.forName()`  : 클래스의 메타 정보를 획득하는 메서드. 

`classHello.getMethod("callA")`  : "callA" 라는 이름의 메서드 메타 정보 획득

`methodCallaA.invoke(target);`  target의 "callA" 메서드 호출.

별것 아닌 것 같지만, 이것들로 인해 동적 기술이 가능해진다.

```java
@Test
void reflection2() throws NoSuchMethodException, ClassNotFoundException, InvocationTargetException, IllegalAccessException {
    //클래스정보
    Class classHello = Class.forName("hello.proxy.jdkdynmaic.ReflectionTest$Hello");

    Hello target = new Hello();

    //callA 메서드 정보
    Method methodCallaA = classHello.getMethod("callA"); //메서드 추상화
    dynamicCall(methodCallaA,target);

    //callB 메서드 정보
    Method methodCallaB = classHello.getMethod("callB");
    dynamicCall(methodCallaB,target);
}

private void dynamicCall(Method method, Object target) throws InvocationTargetException, IllegalAccessException {
    log.info("start");
    Object result = method.invoke(target);
    log.info("result={}",result);
}
```

위의 개념을 활용한다. 원하는 메서드를 동적으로 파라미터로 넘겨받아서, 공통로직과 함께 기존 로직을 호출한다. 

참고로) 리플렉션 기술은 일반적으로 사용하지 않는다.

<br>

## 05-2 JDK 동적 프록시 기술

앞에서 프록시 기술을 배울때, 인터페이스를 활용한 프록시와 상속을 활용한 프록시를 배웠다. 

JDK 동적 프록시 기술은 전자의 경우를 표준화했다.

어떻게 JDK 동적프록시 기술을 써야하는가? 에대한 시작은 인터페이스 `InvocationHandler` 구현 부터 시작한다.

> 참고로 지금 배우는 JDK 동적프록시 기술, 뒤에 배우는 CGLIB 기술은 스프링 ProxyFactory에 통합되므로 개별적으로 사용할 일은 없을 것같다.

```java
@Slf4j
public class TimeinvocationHandler implements InvocationHandler {

    private final Object target;

    public TimeinvocationHandler(Object target) {
        this.target = target;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log.info("TimeProxy 실행");
        long startTime = System.currentTimeMillis();

        Object result = method.invoke(target, args);
        long endTime = System.currentTimeMillis();

        long resultTime = endTime - startTime;
        log.info("TimeProxy 종료 resultTime", resultTime);

        return result;
    }
}
```

+ 파라미터 설명
  + `proxy` : 프록시 자신
  + `method` : 호출한 메서드
  + `args` : 메서드를 호출하때 전달한 인수

이부분에 대해 설명이 부족했던것은 동적으로 생성되는 부분들이 너무 내부적 요소이고, 그렇게 까지 딥하게 알게 없다. 그냥<u> 파라미터로 넘겨받는 것들이 동적으로 생성되는 것이라 생각하면된다.!</u> 즉, 내가 직접 넘기는게 아니다. 예시를 보면 더 쉽게 와닿는다.

```java
@Test
void dynamciA() {
    Ainterface target = new AImpl();

    TimeinvocationHandler handler = new TimeinvocationHandler(target);

    Ainterface proxy = (Ainterface) Proxy.newProxyInstance(Ainterface.class.getClassLoader(), new Class[]{Ainterface.class}, handler);

    proxy.call();
    log.info("targetClass={}",target.getClass()); //targetClass=class hello.proxy.jdkdynamic.code.AImpl
    log.info("proxyCalss={}",proxy.getClass()); //proxyClass=class com.sun.proxy.$Proxy1
}
```

이제 동적으로 생성이된다는게 살짝 체감이 된다. 앞장에서 배웠던 실제 객체 즉, target을 넘겨 줌으로써 handler는 끝이다.

최종적으로 우리가 필요한것은 `Proxy` 객체이다. 이 객체를 통해 클라이언트가 요청을하게 되기 때문이다.

`Proxy.newProxyInstance(AInterface.class.getClassLoader(), new Class[]{AInterface.class}, handler)` 을 호출하면서 동적 프록시 객체를 생성한다. 이때 다운캐스팅을 통해 원하는 필요한 타입을 얻는다.

실행동작을 그림으로 살펴보면 더 쉽다.

<img title="" src="IMG/jdk_proxy_ex.png" alt="" data-align="center" width="574">

간단 하지 않은가? 물론 내부적으로 어떻게 동적으로 프록시 객체를 생성하는가에 대한 내용은 모르지만 집착하지 않기로 하자.

## 05-3 JDK 동적 프록시 기술 적용 (version1)

JDK 동적 프록시 기술은 인터페이스가 있는 경우에만 적용할 수 있다고 했으므로 기존에 만들어놨던 계층예제중 인터페이스가 전부 존재하는 `v1` 에 적용한다. 우선 우리가 해야할 것은 `InvoacationHandler` 를 구현 하는 것이다.

```java
public class LogTraceBasicHandler implements InvocationHandler {

    private final Object target;
    private final LogTrace logTrace;

    public LogTraceBasicHandler(Object target, LogTrace logTrace) {
        this.target = target;
        this.logTrace = logTrace;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        TraceStatus status = null;
        try {
            String message = method.getDeclaringClass().getSimpleName() + "." + method.getName() + "()";
            status = logTrace.begin(message);

            //target 호출
            Object result = method.invoke(target, args);
            logTrace.end(status);

            return result;
        } catch (Exception e) {
            logTrace.exception(status,e);
            throw e;
        }
    }
}
```

끝이다. 동적프록시를 사용할 것이기 때문에 기존 코드들은 손대지 않는다.(컨트롤러, 서비스 등) 

마지막으로 해줄것은 `Config` 에서 빈만 잘 등록하면 된다. -> 이게 은근 생각 할 것이 많다.

```java
@Configuration
public class InterfaceProxyConfig {

    @Bean
    public OrderControllerV1 orderController(LogTrace logTrace) {
        OrderControllerV1Impl controllerImpl = new OrderControllerV1Impl(orderService(logTrace));
        return new OrderControllerInterfaceProxy(controllerImpl,logTrace);
    }

    @Bean
    public OrderServiceV1 orderService(LogTrace logTrace) {
        OrderSerivceV1Impl orderSerivceImpl = new OrderSerivceV1Impl(orderRepository(logTrace));
        return new OrderSerivceInterfaceProxy(orderSerivceImpl,logTrace);
    }

    @Bean
    public OrderRepositoryV2 orderRepository(LogTrace logTrace) {

        OrderRepositoryV2Impl orderRepositoryImpl = new OrderRepositoryV2Impl();
        return new OrderRepositoryInterfaceProxy(orderRepositoryImpl,logTrace);
    }
}
```

흐름만 잘 생각하면된다. 빈에는 프록시가 들어가야한다는것, 프록시는 실제 객체를 알고 있어야한다는것. 이것만 주의하면된다. 

근데 지금 상황에서는 문제점이 하나 존재한다. 바로 모든 메서드에 프록시가 적용된다는 것. 분명히 예제를 만들때 로그를 찍지 않는 메서드가 존재했다. 이경우는 어떻게 해결해야할까?

#### InvocationHandler 수정 - 메서드 이름 필터 추가

```java
public class LogTracceFilterHandler implements InvocationHandler {

    private final Object target;
    private final LogTrace logTrace;
    private final String[] patterns;

    public LogTracceFilterHandler(Object target, LogTrace logTrace, String[] patterns) {
        this.target = target;
        this.logTrace = logTrace;
        this.patterns = patterns;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        //메서드 이름 필터
        String methodName = method.getName();
        //save, request, reque* , *est
        if (!PatternMatchUtils.simpleMatch(patterns, methodName)) {
            return method.invoke(target,args);
            //특정 메서드 패턴일치해야만 추가 로직수행
        }

        TraceStatus status = null;
        try {
          //생
        } catch (Exception e) {
           //생
        }
    }
}
```

`PatternMatchUtils.simpleMatch(..)` 스프링이 제공하는 기능을 사용하면 매칭 로직을 쉽게 적용한다.

+ `xxx` : 정확히 일치하면 참

+ `xxx*` : xxx 로 시작하면 참

+ `*xxx` : xxx로 끝나면 참

+ `*xxx*` : xxx가 존재하면 참

이제 컨피그만 다시 수정하면 된다.

```java
@Configuration
public class DynamicProxyfilterConfig {

    private static final String[] PATTERNS = {"request*", "order*" , "save*"};

    @Bean
    public OrderControllerV1 orderControllerV1(LogTrace logTrace) {
        OrderControllerV1 orderControllerV1= new OrderControllerV1Impl(orderServiceV1(logTrace));

        OrderControllerV1 proxy = (OrderControllerV1) Proxy.newProxyInstance(OrderControllerV1.class.getClassLoader()
                , new Class[]{OrderControllerV1.class}, new LogTracceFilterHandler(orderControllerV1, logTrace,PATTERNS));

        return proxy;
    }
```

이제 더이상 `noLog` 메서드에는 핸들러가 적용되지 않는다.

#### 한계

JDK 동적프록시는 인터페이스가 필수이다. 앞에서 프록시를 적용하는 두가지 패턴이있었다. 첫번째는 인터페이스를 이용하는것, 두번째는 클래스 상속을 이용하는 것. JDK 동적프록시는 전자이다.

후자는 CGLIB 기술을 이용하면된다.

<br>

## 05-4 CGLIB

#### CGLIB란?

Code Generator Library

+ 이름에서 알 수 있듯이 바이트 코드를 조작해서 동적으로 클래스를 생성해버리는 기술이다.

+ 구체클래스만 가지고 동적으로 프록시를 생성할 수 있다.

+ 영한좌가 말씀하셨든 백문이 불여일타 코드로 이해해보자

#### 예제

```java
@Slf4j
public class TimeMethodInterceptor implements MethodInterceptor {

    private final Object target;

    public TimeMethodInterceptor(Object target) {
        this.target = target;
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        log.info("TimeProxy 실행");
        long startTime = System.currentTimeMillis();

        Object result = methodProxy.invoke(target, args);

        long endTime = System.currentTimeMillis();

        long resultTime = endTime - startTime;
        log.info("TimeProxy 종료 resultTime = {}ms", resultTime);

        return result;
    }
}
```

+ JDK 동적 프록시 기술에 `InvocationHandler` 가 제공 되었듯이 , CGLIB 는 `MethodInterCeptor` 를 제공하고 이것을 상속받아 구현 하면된다. 

+ 파라미터로 넘어오는 것들이 바로 프록시 기술들이 제공하는 것이다. 이것을 구분하자. 어디까지가 기술들이 제공하는 것이고 어디부터가 내가 구현해야하는 지를 정확하게 아는 것이 기술을 사용하고 이해하는데 중요하다. 

+ 물론,,,, 직접 사용할 일없다..

```java
@Slf4j
public class CglibTest {

    @Test
    void cglib() {
        ConcreteService target = new ConcreteService();

        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(ConcreteService.class);
        enhancer.setCallback(new TimeMethodInterceptor(target));
        ConcreteService proxy = (ConcreteService) enhancer.create();
        log.info("targetClass={}",target.getClass());
        log.info("proxyClass={}",proxy.getClass());

        proxy.call();

    }
}
```

+ `Enhancer` 가 프록시를 생성한다.

+ `setSuperclass` 로 어떤 구체클래스를 상속 받을 지 지정한다.



#### CGLIB 제약

+ CGLIB는 상속을 사용하기 때문에 몇가지 제약이 있다.
  
  + 생성자 체크가필요하다. 자바 기본 문법 때문에 상속시 부모 클래스의 기본 생성자가 필요하다.
  
  + `final` 클래스는 상속이 불가능하다. -> 프록시를 만들 수없다.
  
  + `final` 메서드는 오버라이딩 불가능하다. -> 프록시 로직이 동작하지 않는다.

> V2 에 CGLIB 를 적용하면 되는 차례인데 , 지금 당장은 적용할 수 없다. target 들에 기본생성자가 적용되어있지 않기 때문이다.





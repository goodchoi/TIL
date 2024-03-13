# 📍main topic : 스프링의 IoC(Inversion of Control)과 DI(Dependency Injection)에 대해 설명하시오

## IoC(Inversion of Control)란?
> 제어의 역전

굉장히 생소한 단어처럼 들리지만, 풀어서 설명하면 프로그램 제어의 흐름을 프레임워크에게 맡기는 것을 말한다.
프레임워크를 적용하지 않은 프로그램을 생각하면 객체의 사용의 제어(생성, 초기화, 소멸 등)을 직접 관리 해서 
작성했을 것이다. 하지만 스프링 같은 프레임워크에서는 이러한 제어를 스프링 컨테이너가 담당한다.

이것은 프레임워크와 라이브러리를 구분하는 척도가 되기도 한다.
+ 프레임워크는 내가 작성한 코드를 제어하고, 대신 실행한다.
+ 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다

### 장점
+ 프로그램의 진행흐름과 구체적인 구현을 분리할 수 있다.
+ 개발자는 비지니스 로직에 집중할 수 있다.
+ 객체간 의존성이 낮아진다.

### IOC 컨테이너
스프링에서 이러한 `IoC`를 담당하는 컨테이너를 IOC컨테이너 혹은 DI 컨테이너, `Application Context`라고 부른다.

![application_context.png](..%2Fimg%2Fapplication_context.png)

실제 스프링에서 클래스 계층을 보면 
가장 상위에 스프링 빈을 관리하고 조회하는 역할을 담당하는 `BeanFactory`인터페이스가 있고

하위 인터페이스로 추가 기능을 가진 `ApplicationContext`가 있다.
대표적인 `ApplicationContext`로는 어노테이션을 활용한 `AnnotaionConfigApplicationContext`가 있다.

> 스프링 빈이란?
> 
> 스프링 컨테이너에 의해 생성 및 관리되는 인스턴스화 된 객체를 의미한다.
> 일반적으로 빈의 생성과 소멸시점과 관련된 스코프의 기본설정은 싱글톤으로 되어있어 
> 하나의 컨텍스트당 하나의 인스턴스로 관리된다.

## DI(Dependency Injection)란?

의존성이란 객체 지향프로그래밍에서 객체사이에 협력이 일어날 때, 다양한 형태로 참조하는 것을 의미한다.
(파라미터, 리턴값, 지역변수 등) 이때, 실제 구현 객체를 외부에서 생성하고 주입하는 것을
**DI (의존성주입)** 이라고 말한다.

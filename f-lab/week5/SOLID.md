# 📍main topic : 객체 지향 설계 5원칙 - SOLID (vs 다른 개발 원칙)

## SOLID 원칙

### SRP(Single Responsibility Principle)  단일 책임 원칙
> 하나의 클래스는 하나의 책임만 가진다.

하나의 책임이라는 말의 모호함에 얽매이지 말아야 한다. 대신 `모듈이 변경 되는 이유가 
한가지여야 한다` 로 해석하는 것이 바람직하다.
만약 한 클래스 내에서 여러

장점 
+ 코드의 가독성 향상
+ 유지보수 용이 (변경 시 수정할 대상이 명확해짐)
+ 나머지 SOLID 원칙의 대전제가 됨.

### OCP(Open-Closed Principle)  개방 폐쇄 원칙
> 확장에 열려있고 수정에 닫혀 있어야 한다.

1. 모듈의 확장성을 보장하여 새로운 변경사항이 발생했을때, 유연하게 코드를 추가 또는 수정할 수 있게해야한다.
2. 객체를 직접 수정하지않고도 변경사항을 적용할 수 있도록 설계해야한다.

OCP에서 가장 중요한 메커니즘은 `추상화`와 `다형성`이다
추상화를 통해 변하는 것들을 숨기고 변하지않는 것들에 의존하게 하면 확장의 용이성을 높일수 있다.

> 주의
> 
> 개방 폐쇠 원칙은 `공짜`가 아니다. 추후 확장 될 가능성이 거의 없는 것들까지 미리 준비하는것은
> 과도한 설계를 유발한다.

### LSP(Liskov Substitution Principle) 리스코프 치환 원칙
> 서브 타입은 언제나 기반타입과 호환될 수 있어야한다.

즉, 서브 타입은 기반타입이 약속한 인터페이스를 지켜야한다.
`LSP`는 특히 다형성에서 지켜야 할 원칙을 말하는 것으로 보인다.

+ 상속을 통한 재사용은 기반 클래스와 서브 클래스 사이에 `Is-A` 관계가 있을 경우로만 제한해야한다.
+ 그렇지 않다면 합성을 이용한다.
+ 서브 클래스는 기반 클래스의 가정과 인터페이스를 항상 준수해야한다.

### ISP(Interface Segregation Principle) 인터페이스 분리 원칙
> 한 클래스는 자신이 사용하지 않는 인터페이스는 구현하지 말아야한다.

클라이언트의 목적과 용도에 적합한 <u>인터페이스만을</u> 제공해야한다.
`SRP`가 클래스의 단일 책임을 강조한다면 `IRP`는 인터페이스의 단일 책임을 강조한다.

### DIP(Dependency Inversion Principle) 의존 역전 원칙
> 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안되며, 저수준 모듈이 고수준 모듈애 의존해야한다.

하위 모듈의 변경이 상위 레벨 모듈의 변경을 요구하는 위계관계를 끊는 의미에서의 역전을 말한다.
결국 `DIP` 또한 `OCP`와 거의 흡사한 형태의 구조의 코드를 만들 수 밖에 없다.
구체클래스에 의존하지 않고 추상클래스에 의존하면서 `비즈니스 관련 부분이 세부사항에 의존하지 않는 설계원칙`을 
지켜야하기 때문이다.

## 소프트웨어 개발 3대원칙
### KISS (Keep It Simple, Stupid)
> 심플하고 멍청하게 작성하라

### DRY (Don't Repeat Yourself)
> 반복하지마라

### YAGNI (You Ain't Gonna Need It)
> 필요한 작업만 하라.
---
### 참고
+ [Nextree - 객체지향 개발 5대원리 : SOLID](https://www.nextree.co.kr/p6960/)
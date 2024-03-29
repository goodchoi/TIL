# 📍main topic: 객체지향 프로그래밍(OOP) 이란?
>  ✅ 주요 키워드 : 다른 패러다임과 비교, 장단점, 사용이유

## 절차 지향 패러다임과의 비교
### 절차지향 
- 절차지향 프로그래밍은 프로시저(함수) 중심으로 프로그램을 설계하는 방식이다.
데이터와 프로시저(기능)을 분리하여 생각한다.
- 절차지향 프로그래밍은 의미 그대로 해석하면 안된다. 즉, 절차적으로 실행된다는 의미로 해석해선안되며,
원문의 `Procedural`은 프로시저의 의미를 가진다.
- 하나의 큰 기능을 작은 기능들로 나누어 처리하는 `Top-down` 접근 방식으로 설계된다.

> 프로시저(절차)란 ?
> 특정 행동 혹은 목표를 수행하기 위한 일련의 작업이나 순서이다.
> 기능 단위의 프로시저(함수)를 생성하여 순차적으로 일련의 작업을 수행한다.

- 객체의 생성에 따른 오버헤드가 없으므로 메모리 효율성에서 이점이 있음.
- 유연한 추상화를 요구하는 대규모의 프로젝트에서 유지보수의 어려움이 있음.

### 객체지향
- 소프트웨어 개발의 복잡성과 유지보수의 어려움에 대한 대응으로 고안된 개념.
- 객체지향 프로그래밍은 프로그래밍을 객체들의 모임으로 보며, 객체간의 상호 작용으로 프로그램을 구성한다. 객체는 
데이터와 기능을 하나의 단위로 묶어서 생성된다. 
- 작은 문제들을 해결 할 수 있는 객체들을 만든 뒤, 이 객체들을 조합해서 큰 문제를 해결하는 `Bottom-up` 접근 방식을 취한다.

> 객체란? 
> - 현실 세계의 개념 혹은 사물을 모델링 한 것이라 볼 수 있다.
> - 객체는 상태(속성)를 데이터로 표현 할 수 있다.
> - 객체는 행위를 가질 수 있다. 이 행위는 자신의 데이터를 조작하여 상태를 변경하거나 
다른 객체에게 메세지를 전달하는 행위들을 의미한다.

## 객체 지향 프로그래밍의 장단점
### 장점
- 데이터와 행위를 가지는 객체로 모듈화하여 코드의 이해와 유지보수에서 이점이 있다. 코드 변경으로 인한 파급효과가 줄어든다.
- 다형성으로 인해 코드의 유연함이 증가한다.
- 캡슐화로 인해 안정성과 보안성이 증대된다.

### 단점
- 설계 과정에서 어려움이 있을 수 있다.
- 객체의 사용에따른 메모리 사용량이 증가한다.

## 객체지향을 사용하는 이유
가장 큰 목표는 유지보수성, 확장성을 향상 시키는 것. 시중에 나오는 서비스들은 규모가 굉장히 크고 기능의 추가,변경,삭제는 불가피하다.
객체지향적으로 잘 작성된 프로그램은 기능의 변경, 추가의 관점에서 큰 의미가 있다.

















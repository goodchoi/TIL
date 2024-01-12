# 📍main topic: 객체지향 프로그래밍의 특징
> ✅ 주요 키워드 : 추상화/상속/다형성/캡슐화


## 추상화(Abstraction)
    객체의 공통적인 속성과 기능을 추출하여 정의하는 것.
자바에서는 추상화의 방법으로 인터페이스와 추상클래스를 지원한다.
추상화의 정도는 인터페이스 > 추상클래스 이며, 둘은 사용 목적이 다르다.

+ 추상클래스
  + 추상 클래스는 하나 이상의 추상 메서드를 포함한다.
  + 인스턴스를 생성할 수 없다.
  + 클래스와 같이 멤버 변수와 일반 메서드를 포함할 수 있다.
  + 당연히 하나의 상속밖에 허용하지 않는다.(자바는 다중 상속을 허용하고 있지 않기때문에)
  + 상속을 통한 확장에 목적을 둔다.
  + `Is-A` "~이다"

+ 인터페이스
  + 멤버 변수를 포함하지 않는다.(자바 8에서 상수 필드 추가)
  + 모든 메서드는 `abstract`이다. (자바 8에서 `default` 메서드 추가)
  + `Has-A` 
  + 다중 상속(구현)이 가능하다.
  + 특정 행위가 가능함을 의미한다.

## 상속(inheritance)
기존의 클래스를 재활용하여 새로운 클래스를 작성하는 자바의 문법요소. 상속을 받는 클래스는 자식클래스이며
상속의 대상은 부모 클래스이다.

+ 상속의 장점
  + 반복 코드의 최소화
  + 공유하는 속성과 기능에 간편하게 접근
+ 상속의 단점
  + 자식 클래스와 부모 클래스 간의 결합도가 증가한다.
  + 캡슐화를 깨뜨린다.
+ 상속의 문제점 예시
  + 자바 컬렉션 프레임워크 `Stack`
    + `Stack`은 기본적으로 `LIFO` 형태를 취해야하는데 `Vector`를 상속 받고 있으므로
    `Vector`의 모든 메서드를 사용할 수 있음. 즉, 중간지점에서의 조회 및 추가,삭제가 가능해짐
    + 뿐만 아니라, `Vector`가 가진 성능 저하(동기화로 인한)를 같이 가짐.

- 상속보다는 합성을 사용하는게 권장 된다.

> 합성(Composition)이란?
>  + 객체가 다른 객체의 참조자를 얻는 방식으로 런타임시에 동적으로 이뤄진다. 이는 보통 has-a 관계라고 일컫는다.
>  + 다른 객체의 참조자를 얻은 후 그 참조자를 이용해서 객체의 기능을 이용하기 때문에 해당 객체의 인터페이스만을 바라보게 됨으로써 캡슐화가 잘 이뤄질 수 있다.



## 다형성(Polymorphism)
    같은 모양의 코드가(메서드명) 다른 행위(구현방법)를 하는 것.

비유 : 키보드 `esc` , `enter` 등의 키는 누른다는 행위를 수반하다. 이때 키보드마다 각각 누르는 과정을 구현하는 방법은 제각각 일 수 있다.
하지만 누른다는 행위를 요청하는 입장에서는 행위의 결과만 관심있을 뿐이지, 과정은 관심이 없다.


자바에서 다형성 구현 방법
+ 오버라이딩
+ 오버로딩
+ 함수형 인터페이스

다형성은 변화에 유연하게 대처해야하는 객체지향 프로그래밍에서 핵심적인 중추를 담당한다.



## 캡슐화(Encapsulation)
    객체의 속성과 행위를 하나로 묶고, 외부로 부터 감추어 은닉한다.
외부에서 객체의 데이터에 직접접근 할 수 없으므로 
객체의 행위를 요청하는 형태로 강제한다(메시지를 보내야만 한다.). 만약 캡슐화를 하지 않고 외부에서 데이터에 직접 접근하여 
행위를 구현한다면 코드의 반복이 일어나며, 데이터의 접근이 자유로워져 보안성이 떨어지게된다.








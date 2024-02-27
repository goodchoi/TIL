# 📍main topic : Spring Entity vs DTO vs DAO vs VO 개념과 차이점 및 관련 어노테이션에 대해 설명하시오
> ✅ 주요 키워드 :

### Entity
    데이터베이스의 테이블과 `1:1` 맵핑 되는 클래스
상속을 받거나 구현체 인터페이스 구현체가 아닌 단일 클래스여야한다.
내부에 비즈니스 로직을 가지고 있으며, 뷰나 컨트롤러 계층에 의존하거나
의존적인 코드를 가져서는 안된다.

### DTO(Data transfer Object)
계층간 데이터 교환을 위한 자바 클래스를 의미한다.
DTO는 로직을 가지고 있지 않아야하며 오직 `getter`와 `setter`만
가져야한다.


### VO(Value Object)
값 그 자체를 표현하는 객체.

DTO와 다르게 로직을 포함할 수 있으며, 객체의 불변성을 보장한다.
또한, VO는 `equals`와 `hashcode`를 오버라이딩하여 동등성을 보장할 수 있다.

> 개발자 사이에서 VO는 모호하게 사용되는 경우가 더러있다.
> 예를들어, VO와 DTO를 같은 선상에 놓고 혼용을 하는 경우 그리고
> JPA에서 사용하는 값 타입을 의미하는 경우가 있다.
> 문맥에 맞는 이해가 필요하다.

### DAO(Data Access Object)
DAO는 MVC 패턴구조에서는 Pesistence계층에 속하는 Repository를 말한다.

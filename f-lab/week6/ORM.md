# 📍main topic : ORM (Object Relational Mapping)에 대해 설명하시오
> ✅ 주요 키워드 :

### ORM이란?
ORM(Object Relational Mapping) 이란 프로그램의 객체(Object) DB에 저장되는 데이터를
`Mapping`해주는 것을 말한다.
객체모델과 관계형 모델 사이에는 구조적/문법적인 간극이 존재한다.
`ORM`은 객체를 데이터베이스의 데이터와 자동으로 맵핑해주기 때문에
객체를 통해 데이터베이스 다루는 것을 가능하게 한다.

대표적인 ORM 기술에는 자바 표준 `JPA`가 있으며 구현체로 `Hibernate`가 있다.

### MyBatis와 비교
JPA는 객체를 데이터베이스와 자동으로 맵핑한다. 반면 Mybatis는
자동으로 맵핑하는 방식이 아니 직접 작성한 SQL을 Java코드와 직접 맵핑 해줘야했다.

### 장점
- 객체지향적 코드로 더 직관적이고, 비즈니스 로직 집중할 수 있게해준다.
- DBMS에 대한 종속성이 줄어듭니다.
  - RDBMS 마다 세부적인 `SQL`문법이 다르므로 종속성이 생길 수 밖에 없는데
  ORM은 자동으로 `SQL`문을 생성하므로 이런 종속성에서 벗어날 수 있다.

### 단점
- 복잡한 SQL 생성의 어려움
  - join하는 테이블의 개수가 많은 경우 같이 복잡한 sql을 작성해야하는 경우
  JPA로 원하는 쿼리문을 도출하는 것이 용이하지 않음.
- 성능에 대한 고려 필요
  -  JPA에 의해 자동으로 SQL이 만들어지다 보니, DB의 특성(index, join 등)을 이해하고 DB에 맞는 SQL을 직접 튜닝해서 만들면 성능이 훨씬 뛰어날 수 있으나
  , 자동화된 SQL 문으로 인해서 데이터 조회 성능이 떨어질 가능성이 있다.

위와 같은 단점의 영향으로 `SI`나 은행 업계같은 경우 아직 `Mybatis`를 사용하는 경우가
대부분이다.

따라서, `JPA`를 사용하는 것은 정답이 아니며, 상황에 맞게 기술을 선택하는 것이 중요하다.

### 객체 지향 모델과 관계형 모델의 불일치
> The Object-Relational Impedance Mismatch
#### 1. Granularity (세분성)
때로는 데이터베이스의 해당 테이블 수보다 더 많은 클래스가 있는 개체 모델이 있을 수 있다. (개체 모델이 관계형 모델보다 더 세분화되어 있다고 말합니다.)

```
create table USER (
    id varchar(255) not null,
    name varchar(255),
    city varchar(255),
    zipcode integer
    street varchar(255),
)

public class Address {
    private String city;
    private String streeet;
    private String zipcode;
}

public class User {
    private String id;
    private String name;
    private Address address; 
}
```

위의 예시와 같이 객체모델의 클래스 숫자와 데이터베이스의 테이블이 1:1 맵핑이 되지않음을 말한다.

#### 2. Subtypes (inheritance) - 하위타입(상속)
RDBMS에는 상속이란 개념이 없다. 하위 타입을 지원하긴 하지만 표준화되어 있지 않다.


#### 3. Identity(동일성)
RDBMS는 정확히 하나의 동일성을 보장하는 개념인 기본 키(Primary Key,PK)를 제공한다. 
지만 (Java)객체는 동일성(==) 뿐만 아니라 동등성(equals())을 모두 정의한다.


#### 4. Associations (연관)
객체지향 언어에서 연관관계는 단방향 참조로만 이루어진다.
RDBMS는 외래 키(Foreign key, FK) 하나를 사용해서 양방향 참조를 가진다.
하지만 객체 모델은 단방향 참조가 기본이고 양방향을 참조하려면 양쪽에 연관을 두 번 정의해야 한다.

#### 5. Data navigation (데이터 탐색)
Java에서 데이터에 접근할 때 하나의 객체에서 출발해 다른 연결로 이어지는 객체 그래프 탐색 방식을 사용한다.
하지만 이 방식은 관계형 데이터베이스에서 이 방식은 비효율적이다.
RDBMS는 SQL쿼리를 최소화하기 위해 JOIN을 통해 여러 엔티티를 불러와서 데이터를 탐색한다.

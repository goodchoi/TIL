# 📍main topic : ORM/JPA의 n+1문제와 해결방법에 대해 설명하시오

## N+1 문제란?
연관 관계에서 발생하는 이슈로 연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼 연관관계의 조회 쿼리가 추가로 발생하여 데이터를 읽어오게 된다. 이를 N+1 문제라고 한다. 

> Fetch Type - Eager, Lazy
> 
> 연관관계로 설정된 엔티티의 조회 시점을 언제로 잡냐마냐를 선택하는 옵션으로 
> N+1 문제의 해결방안은 아니다. Eager는 엔티티 조회시 연관관계의 엔티티를 즉시
> 추가 쿼리문으로 영속성 컨텍스트에 올리고, Lazy는 프록시로 연관관계 엔티티를 주입한 후,
> 연관관계의 엔티티 호출시 실제 쿼리문이 나가게 된다.

## N+1 해결방안
### 1. Fetch join
```java
@Query("select o from Owner o join fetch o.cats")
List<Owner> findAllJoinFetch();
```

연관관계 있는 엔티티를 `fetch join`하여 영속화 시키는 방법.
실제 SQL은 `InnerJoin`을 사용하여 결과를 조회한다.

주의할 것은
카테시안 곱(Cartesian Product)이 발생하여 중복이 발생한다는 점이므로,
distinct를 사용하거나, 자료형을 `Set`으로 관리해야한다.

> 가장 근본적인 해결방법이지만, 그렇다고 해서 무분별한 사용은 자제해야한다.
> 연관관계 엔티티를 실제로 다루는 경우만 사용해야 영속성 컨텍스트에 무의미한 엔티티들이
> 존재하게 되는 것을 방지할 수 있다.

### 2. Entity Graph
```java
@EntityGraph(attributePaths = "cats")
@Query("select o from Owner o")
List<Owner> findAllEntityGraph();
```
`join fetch`가 inner join을 사용한다면, `Entity Graph`는 outer join을 사용한다.


### 3. Batch Size   
연관된 엔티티를 조회할 때 지정된 size 만큼 SQL의 IN절을 사용해서 조회한다.

Fetch join이 한번의 조회 쿼리문으로 연관관계에 있는 엔티티까지 조회 했다면
한번의 조회 쿼리문을 더 필요를 한다. 이때, 기존의 조회 하고자 하는
엔티티를 in절에 넣는데, size를 적절하게 선택하여 지정해야한다.

하지만, 최적의 size를 찾는것이 어려운일이므로 과도한 사용을 지양해야한다.

> 컬렉션 연관관계 필드에 fetch join은 한번 밖에 사용할 수없다.
> 이때 batch size가 유용하게 사용될 수있다.





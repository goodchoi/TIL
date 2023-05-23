[인프런 김영한님 스프링 DB part2](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)

# 02 데이터 접근기술 -테스트

> 이번장에서는 DB 접근기술을 테스트 하기위해 테스트 설정 및 코드작성을 다룬다.

## 02-1 데이터베이스 분리

#### 테스트에서 매우 중요한 원칙

+ 테스트는 다른 테스트와 격리되어야한다.

+ 테스트는 반복해서 실행할 수 있어야한다.
  
  + 반복해서 실행되어도 똑같은 결과가 유지 되어야한다.

## 02 -2 원칙을 지키는 법 1 : 데이터 롤백

```java
    @Autowired
    PlatformTransactionManager transactionManager;

    TransactionStatus status;

    @BeforeEach
    void beforeEach() {
        //트랜잭션시작
        status = transactionManager.getTransaction(new DefaultTransactionDefinition());
    }

    @AfterEach
    void afterEach() {
        //MemoryItemRepository 의 경우 제한적으로 사용
        if (itemRepository instanceof MemoryItemRepository) {
            ((MemoryItemRepository) itemRepository).clearStore();
        }

        //트랜잭션 롤백
        transactionManager.rollback(status);
    }
```

+ 앞에서 배운 트랜잭션을 수동으로 하는법과 `@BeforeEach` , `@AfterEach` 를 이용하여 트랜잭션을 테스트 케이스마다 부여한다.

+ 스프링부트는 `TransactionManager`를 자동으로 빈으로 등록한다. 

<br>

## 02-3 원칙을 지키는 법 2 : @Transactional

스프링에서는 결국 `@Transactional`로 귀결된다. 사실, 트랜잭션 매니저 ,동기화매니저 이런거 하나 몰라도 스프링이 이미 추상화를 다 해놓았기 때문에 신경을 쓸 필요가없다. 하지만 내부동작을 이해하고 있는 것은 큰 도움이 될것이다.

```java
@Transactional
@SpringBootTest
class ItemRepositoryTest {

    @Autowired
    ItemRepository itemRepository;

   /* @Autowired
    PlatformTransactionManager transactionManager;

    TransactionStatus status;

    @BeforeEach
    void beforeEach() {
        //트랜잭션시작
        status = transactionManager.getTransaction(new DefaultTransactionDefinition());
    }

    @AfterEach
    void afterEach() {
        //MemoryItemRepository 의 경우 제한적으로 사용
        if (itemRepository instanceof MemoryItemRepository) {
            ((MemoryItemRepository) itemRepository).clearStore();
        }

        //트랜잭션 롤백
        transactionManager.rollback(status);
    }*/
```

+ 이렇게 쓸데 없는 코드를 다 지울 수 있다.

#### 테스트 코드에서 @Transactional 동작 방식

1. 트랜잭션을 시작한다.

2. 테스트 로직을 실행한다. 모든 로직은 트랜잭션에서 실행된다.

3. 테스트 검증이 끝나면 강제로 롤백한다.

결과적으로 마치 아무일이 없었던것처럼 된다.

만약 , 결과를 DB에서 확인하고 싶으면 `@Commit`을 붙이면된다.

<br>

## 02-4 임베디드 모드 DB

어플리케이션 서버와 테스트 서버는 분리되어야한다. 그럼 테스트 서버를 위한 데이터베이스를 새로 설치하고 운영해야하는가? 이건 너무나 복잡한 일이다.

#### 임베디드 모드

H2 데이터베이스는 자바로 개발되있고, JVM안에서 메모리모드로 동작하는 기능을 가진다. 그래서 메모리에서 DB를 사용할 수 있다! 이런 편의성 때문에 테스트 서버로 많이 사용한다.

#### 임베디드 모드 직접사용하기

```java
    @Bean
    @Profile("test")
    public DataSource dataSource() {
        log.info("메모리 데이터베이스 초기화");
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("org.h2.Driver");
        dataSource.setUrl("jdbc:h2:mem:db;DB_CLOSE_DELAY=-1");
        dataSource.setUsername("sa");
        dataSource.setPassword("");
        return dataSource;
    }
```

+ `DataSource` 를 직접 등록한다. (`application.properties` 로 자동 등록되는 것보다 우선순위 높음)

+ `jdbc:h2:mem:db` : 이부분이 중요, 임베디드 모드로 h2 데이터베이스로 사용하겠다는 뜻이다.
  
  + 당연히 H2 라이브러리 필요



#### DB 초기화

+ 테스트를 하려면 테이블이 필요하다. DDL을 직접 작성해서`@Beforeeach` 로 쓸수 있지만 너무 복잡하다.

```java
drop table if exists item CASCADE;
create table item
(
    id        bigint generated by default as identity,
    item_name varchar(10),
    price     integer,
    quantity  integer,
    primary key (id)
);
//scheama.sql
```

+ `src/test/resources/schema.sql` 이경로로 sql 스크립트를 사용하여 초기화를 간단히 할 수있다. 더 자세한것은 매뉴얼을 찾아보자.



#### 스프링 부트가 제공하는 임베디드 모드

+ `그냥 아무것도 안하면 스프링부트가 알아서 임베디드 모드를 실행한다.......`

+ 정확히는 테스트 `application.properties` 에 datasource설정을 아무것도 하지않으면 된다.
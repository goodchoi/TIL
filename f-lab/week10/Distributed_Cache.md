# 📍main topic : 분산 캐시(distributed cache) eg. Redis

## Cache란?
> 데이터의 원본(DB 데이터) 보다 더 빠르고 효율적으로 액세스 할 수 있는 
> 임시 데이터 저장소

### 💡Cache를 도입하는 진입점
+ 원본 데이터 저장소에서 원하는 데이터를 찾기위해 검색하는 시간이 오래걸리거나, 매번 계산을 통해 데이터를 가져와야하는경우
  + 계속 같은 계산을 할 필요가 없다.
+ 캐시에서 데이터를 가져오는것이 원본 저장소에서 요청하는것보다 빨라야함
+ 캐시에 저장 되는 데이터는 잘 변하지 않는 데이터일것.
+ 캐시에 저장되는 데이터는 **자주** 검색 되는 데이터 일것.


## Cache의 유형
캐싱은 크게 로컬 캐싱과 분산 캐싱의 두가지 유형으로 분류할 수 있다.

### 로컬 캐싱
단일 시스템 혹은 응용프로그램 내에 데이터를 저장하는 것을 말한다. ex) 브라우저 캐시 또는 응용프로그램 수준의 캐시

### 분산 캐싱
여러 시스템 혹은 노드(네트워크)에 데이터를 저장하는 작업이 포함된다. 이러한 유형의 캐싱은 여러 서버에 걸쳐 확장해야하거나
분산되어있는 응용프로그램에 필수적이다 

+ 분산캐싱의 예
  + Redis
  + Memcached
  + Hazelcast 


## 캐싱의 여러 전략

## 1. 읽기 전략
#### 📍Look aside 읽기 전략
동작과정
+ Cache에 검색하는 데이터가 있는 지확인
+ 없을경우 DB에서 조회
+ 조회한 데이터를 Cache에 업데이트

특징
+ Redis 서버에 문제가 생기더라도 장애가 발생하지 않고, DB에서 데이터를 확보 할 수 있다.
+ 초기 호출시 무조건 DB에 접근해야하므로 커넥션 부하가 생길 수 있다.
+ 초기 캐시 미스를 줄이기위해 `Cache Warming`을 고려 할 수 있다.

> Cache warming 
> 
> 미리 데이터베이스에서 캐시로 데이터를 미러 넣어 주는 작업.


### 📍Read Through 읽기 전략
기본적으로 `Look aside` 방식과 비슷하나 , `Cache`에 저장하는 주체가
`Server`이냐, 혹은 캐시(`redis`) 이냐에서 차이점이 있다.

즉, `Read Through` 방식은 데이터베이스에 접근하는 과정을 `Cahce Store`에게 모두 위임한채
데이터의 획득에만 집중한다. 

하지만, 캐시에 문제가 발생할 경우 서비스 장애의 위험이 있기때문에 Cluster를 사용하여
가용성을 높여야한다.


## 2. 쓰기 전략
### 📍Write Through 쓰기 전략
> 데이터 베이스에 업데이트 할 때마다 매번 캐시에도 데이터를 함께 업데이트 하는 방식

특징
+ DB와 캐시가 항상 동기화가 되어있어서 캐시의 데이터를 항상 최신 상태로 유지한다.
+ 자주 사용되지 않는 불필요한 리소스를 저장하는 현상 발생
+ 매번 2개의 저장소에 저장돼야하기 때문에 시간이 소요.

### 📍Cache invalidation 쓰기 전략
> 데이터 베이스에 값을 업데이트 할때마다 캐시에서 데이터를 삭제하는 전략

특징
+ 데이터를 삭제하는 것이 새로운 데이터를 저장하는 것보다 훨씬 리소스를 적게 사용한다는 점을 활용
+ write through의 단점을 보


### 📍write behind(write back) 쓰기 전략
> 데이터 저장작업을 DB에 바로 하는것이 아니라 캐시에 저장하여 모아두었다가
> 특정시점에 DB로 쓰는 방식.

특징
+ 캐시 장애시 데이터 유실 발생
+ 하지만 오히려, DB 장애 발생시 지속적 서비스 제공 가능(단, 한계는 존재 할 듯)


## 캐시의 만료시간
> TTL (Time to Live)
> 
> 데이터가 얼마나 오래 저장 될 것인지른 나타 내는 시간 설정

만료 캐시 삭제 방식

+ passive
  + 클라이언트가 키에 접근하고자 할 때 만료 됐으면 삭제 되는방식
+ active
  + TTL을 가진 키중 20개를 랜덤하게 뽑하낸뒤 만료된 키를 모두 메모리에서 삭제

## 메모리 관리

+ Noeviction
  + 데이터가 가득차더라도 임의로 데이터를 삭제 하지않고 에러를 반환

+ LRU eviction
  + 가장 최근에 사용되지 않은 데이터를 ㅏㄱ제하는 정책
+ LFU eviction
  + 가장 자주 사용 되지 않은 데이터부터 삭제하는 정책

## 캐시 스탬피드 현상
> 특정 키가 만료되시점에서 여러개의 어플리케이션에서 해당키에 접근하고자 할때, 데이터베이스에 접근하여 `중복 읽기`가 발생하고,
> 레디스에 쓸 때, `중복 쓰기`가 발생 이것을 캐시 스탬피드라고 한다.

만료시간을 너무 짧게 설정하지 않음으로서 캐시스탬퍼드 현상을 줄일 수 있다. 







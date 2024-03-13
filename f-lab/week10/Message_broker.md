# 📍main topic : 메시지 브로커 

## AMQP 와 MOM
> MOM(Message-Oriented Middleware)
> 
> 여기서 미들웨어는 서로 다른 플랫폼간 중간 계층으로 통신을 담당한다.
> MOM은 결국 상호작용을 `메시지`를 이용하여 이루어진다.


> AMQP(Advanced Message Queuing Protocol)
> 
> 메시지 큐 시스템에서 메시지를 교환하고 전달하기 위한 업게 표준 프로토콜
> AMQP는 MOM을 구현하기위한 인터페이스이다.

메시지 브로커의 대표적인 예로 `Rabbit MQ`가 있으며,
`Rabbit MQ`는 `AMQP`를 구현한 `MOM`의 대표적인 한 종류이다.


## 💬 대체 왜 쓰는 건가?
`AMQP`는 TCP/IP 프로토콜 위 에서 동작하는 <u>어플리케이션 레이어 프로토콜</u>이다.
우리가 잘 아는 다른 어플리케이션 프로토콜로는 `HTTP`가 있다.

그렇다면 `MOM`을 구현하기 위해 `HTTP` 통신을 하는 임의의 미들웨어를 충분히 만들수도 있다.
`HTTP`는 우리가 아는대로, 
    
    HTTP는 클라이언트와 서버 사이에 이루어지는 요청/응답(request/response) 프로토콜이다. -wiki

하지만 msa 같은 분산환경을 떠올려보자. 여러 서비스간에 `요청/응답`이 아닌,
단순히 메시지의 `전송/수신` 하는 환경이 필요하다면? 
이와 같은 이유로 `AMQP`가 등장했을 것이고, rabbit mq 같은 메시지 큐가 그 역할을 하고 있는 것이다.
또한 메시지 큐만의 특화된 기능은 두말 할 것도 없다.

### 메시지 큐의 이점
1. 비동기(Asynchronous)

메시지 큐의 경우 생산된 메시지의 저장, 전송에 대해 동기화 처리를 하지 않고 
Queue에 넣어두기 때문에 나중에 처리할 수 있으며, 
이러한 방식으로 인해 기존의 동기화 방식에서 발생할 수 있는 병목 현상을 방지할 수 있다.

2. 낮은 결합도(Decoupling)

메시지를 생산하여 발송하는 서비스(Producer)와 
메시지를 받아서 처리하는 서비스(Consumer)가 독립적으로 행동하면서 
서비스 간의 결합도가 낮아지는 장점이 있다.

3. 확장성(Scalable)

다수의 프로세스들이 메시지 큐를 통해 메시지를 
보낼 수 있기 때문에 확장성 및 분산 처리에 대한 장점이 있다.

4. 탄력성(Resilience)

메시지를 받아서 처리하는 서비스가 다운(중지) 되더라도 
메시지 큐가 중단되는 것은 아니기 때문에 메시지는 Queue에 남아있게 되며, 
중지된 서비스가 재시작되면 Queue에 있는 
메시지를 다시 처리할 수 있습니다.

5. 보장성(Guarantees)

메시지 큐는 보관되는 모든 메시지가 결국 Consumer 서비스에게 전달된다는 
일반적인 보장을 제공합니다.

## Rabbit MQ의 구조
![rabbit_mq_structure.png](..%2Fimg%2Frabbit_mq_structure.png)

- Producer
  - 메시지를 생성하고 발송하는 주체
  - 생성한 메시지는 `Exchange`에 publish한다.
- Consumer
  - 메시지를 받아서 처리하는 주체
- Exchange
  - `Producer`로부터 전달받은 메시지를 어떤 `Queue` 발송할지 결정하는 객체
  -  4가지 타입(Direct, Topic, Headers, Fanout)이 있다.
- Binding
  - Exchange와 Queue를 연결하는 관계
  - Exchange Type과 Binding 규칙에 따라 적절한 Queue로 메시지가 전달된다.

## Exchange Types
+ Direct Exchange
  + 메시지에 포함된 routing key를 기반으 Queue에 메시지를 전달
  + 하나의 Queue에 여러 routing key를 지정할 수 있고, 여러 Queue에 같은 routing key를 지정할 수 있다.
+ Topic Exchange
  + 메시지 포함된 routing key의 패턴을 이용하여 일치하는 패턴의 queue로 메시지를 전달한다.
+ Header Exchange
  + 메시지 라우팅을 위해 헤더를 사용하는 방식
  + binding key만을 사용하는 것보다 더 다양한 속성을 사용할 수 있다는 특징이 있다.
+ Fan out Exchange
  + exchange에 등록된 모든 queue에 동일한 메시지를 전달한다.

## 동시성문제 해결 방식 -  Round-Robin Dispatch, Fair Dispatch
하나의 queue에 여러 consumer가 존재한다면 메시지를 어떻게 분배할까?

기본적인 동작방식은 `Round-Robin` 방식이다.
![round-robbin-rabbitmq.png](..%2Fimg%2Fround-robbin-rabbitmq.png)
즉 메시지큐에 들어있는 메시지를 연결된 consumer에 순차적으로 돌아가면서 분배를 해서 병렬적으로 처리한다.
이때 생길 수 있는 문제점은 뭐가 있을까?

*특정 cousumer에 처리시간이 긴 메시지가 축적이 되는 문제가 발생한다.*

이러한 문제를 예방하기위 `Prefetch`를 설정할 수 있다.
`Prefetch`는 `Consumer`의 메모리에 쌓아 둘 수있는 최대 메시지의 양이다. 
이것을 1로 설정하면 하나의 메시지를 처리하기 전까지 다른 메시지를 추가로 받지 않기 때문에 메시지의 비정상적인
축적이 일어나는 현상을 막을 수 있다.

이것을 `fair dispatch`라 한다.








# 📍main topic : http methods(POST, GET, PUT, PATCH, DELETE)와 http status code(1xx, 2xx, 3xx, 4xx)대해 설명하시오

## http method란?
http 통신을 통해 원하는 요청을 하기 위해서는 `대상`이 지정되어야하며, 그 `대상`이 수행하길 원하는
`행위`가 필수적으로 지정되어야한다. 여기서 `행위`가 `http methods` 라고 할 수 있다. (대상은 `resource`)

## http method 종류
### POST
요청 데이터 처리를 목적으로, 주로 등록에 사용된다.
보통 리소스 생성이 수반된다.

ex)
```
POST /members HTTP/1.1
Content-Type: application/json
{
"username": "hello",
"age": 20
}
```
전송할 데이터를 위와 같이 메세지 바디에 보낸다. 바디의 타입은 `Content-Type`에 의해 결정된다.

주 사용처는 꽤 넓은편인데, 다음과 같다.
1. 새 리소스 생성(등록)
2. 요청 데이터처리(프로세스 처리)
3. 다른 메서드로 처리하기 힘든 경우(`GET`).

### GET
리소스 조회의 목적이 있으며, 서버에 전달하고 싶은 데이터를 `query` 스트링 형태로 전달한다.

ex)
```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

### PUT
리소스 대체의 목적을 가진다.
1) 리소스가 없을시 생성
2) 리소스가 있을시 대체(완전히 대체 - 덮어쓰기)

**클라이언트는 리소를 식별할 수 있어야한다.**
리소스 대체의 목적을 갖기 때문에 대상이 되는 리소스를 식별자를 uri에 포함한다.
---> `post`와 차이점.

ex)
```
PUT /members/100 HTTP/1.1
Content-Type: application/json
{
"username": "old",
"age": 50
}
```

### PATCH
리소스 부분 변경의 목적을 가진다.
```
PATCH /members/100 HTTP/1.1
Content-Type: application/json
{
"age": 50
}
```

### DELETE
리소스 제거의 목적을 가진다.
```
DELETE /members/100 HTTP/1.1
Host: localhost:8080
```
### HTTP 메서드의 속성
![method_property.png](..%2Fimg%2Fmethod_property.png)
#### 안전(Safe methods)
호출해도 리소스를 변경하지 않음을 의미
ex) `GET`,`HEAD` , `OPTIONS`, `TRACE`

#### 멱등(Idempotent Methods)
동일한 요청을 계속해서 요청시 항상 똑같은 결과를 내는 것.
ex) `GET`, `PUT`, `DELETE`

> `PATCH`는 왜 멱등이 아닐까?
> 
> `PATCH`는 전체에 대한 변경이 아니기 때문에 현재 변경하고자 하는 부분 외의
> 다른 `PATCH`요청이 들어왔을때, 기존 `PATCH`가 기대했던 결과와 다른 결과를 도출함

#### 캐시가능(Cacheable)
동일한 요청에대한 응답결과를 재사용 가능한지의 여부
ex) 'GET', 'HEAD' , 'POST','PATCH'


## Http 상태코드(Status Code)
상태의 결과를 메세지 바디와 함께 보내는 것만으로는 부족하기에 요청의 처리상태를 
코드로서 함께 나타나는 기능.

### 상태코드의 종류
+ 1xx (Informational): 요청이 수신되어 처리중 
+ 2xx (Successful): 요청 정상 처리 
+ 3xx (Redirection): 요청을 완료하려면 추가 행동이 필요 
+ 4xx (Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음 
+ 5xx (Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함


### 2xx (Successful)
#### 200 OK
요청의 성공을 표시

[request] 
```
GET /members/100 HTTP/1.1
Host: localhost:8080
```

[response]
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 34
{
"username": "young",
"age": 20
}
```

#### 201 Created
요청 성공해서 새로운 리소스 생성됨
생성된 리소스는 응답의 Location 헤더 필드로 식별할 수 있다.
[request]
```
POST /members HTTP/1.1
Content-Type: application/json
{
"username": "young",
"age": 20
}
```

[response]
```
HTTP/1.1 201 Created
Content-Type: application/json
Content-Length: 34
Location: /members/100    ---------------!
{
"username": "young",
"age": 20
}
```

#### 202 Accepted
요청이 접수되었으나 처리가 완료되지 않음
-> 배치 처리같은 경우 사용

#### 204 No Content
서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음
ex) save 버튼.


### 3xx(Redirection)

#### 리다이렉션의 종류
+ 영구 리다이렉션 - 특정 리소스의 URI가 영구적으로 이동
+ 일시 리다이렉션 - 일시적인 변경 ex) PRG (POST->REDIRECT->GET)
+ 특수 리다이렉션 - 캐시를 사용

#### 301, 308 영구 리다이렉션 
+ 301 Moved Permanently
  + 리다이렉트시 요청 메서드가 `GET`으로 변하고, 본문이 제거될 수 있음
+ 308 Permanent Redirect
  + 리다이렉트시 요청 메서드와 본문 유지

#### 302, 307, 303 일시 리다이렉션
+ 302 Found 
  + 리다이렉트시 요청 메서드가 GET으로 변하고(정해지지 않음), 본문이 제거될 수 있음(MAY)
+ 307 Temporary Redirect 
  + 302와 기능은 같음 
  + 리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경하면 안된다. MUST NOT) 
+ 303 See Other 
  + 302와 기능은 같음 
  + 리다이렉트시 요청 메서드가 GET으로 변경

> 302 스펙 첫 의도는 기존 요청 메서드를 유지하는 것이였으나 브라우저들이 임의로 `GET`으로 바꿔어버리는
> 동작을 하고 있다. 따라서 302 보다 명확한 307 혹은 303을 사용하는 것이 바람직하나
> 302를 사용해 큰 문제가 없고, 302를 그냥 기본값으로 쓰고 있다.

#### 304 Not Modified
캐시를 목적으로 사용한다. 리소스가 수정되지 않았음을 의미한다.


### 4xx Client Error
오류의 원인이 클라이언트에게 있음을 나타낸다.

#### 400 Bad Request
클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음
+ 요청 파라미터, API 스펙에 맞지 않을시.

#### 401 Unauthorized
클라이언트가 해당 리소스에 대한 인증이 필요함
+ 401 오류 발생시 응답에 WWW-Authenticate 헤더와 함께 인증 방법을 설명

#### 403 Forbidden
서버가 요청을 이해했으나 거부함.
+ 주로 인증은 거쳤으나, 권한이 불충분한 경우를 나타냄.

#### 404 Not Found
요청 리소스를 찾을 수 없음.

### 5xx (Server Error)
서버에 문제가 있음을 나타냄. 재시도시 성공할 가능성이 있다. (4xx의 경우 재시도가 실패)

#### 500 Internal Server Error
서버 문제로 인한 모든 오류에 대한 상태코드
애매하면 500 에러 내는게 맞다.
 
#### 503 Service Unavailable
서비스 이용불가.
+ 서버가 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음

# 📍main topic : OpenAPI spec과 사용 이유
> ✅ 주요 키워드 :


## OpenAPI
![OpenAPI.png](..%2Fimg%2FOpenAPI.png)

서비스의 `Restful API`를 정의된 규칙에 맞게 API Spec을
json이나 yaml로 표현하는 방식


### Swagger?
OpenAPI는 한때 Swagger로 불렸던 때가 있었으나 2015년 Linux foundation의
`Open Initiative`에 `Swagger`를 기부하면서 현재는 `OpenAPI spec`으로 
이름이 변경 되었다.

### 현재 사용되는 용어
+ OpenAPI : 과거 `Swagger Specifiaction`의 spec 그 자체
+ Swagger : OpenAPI를 구현하기 위한 `툴`


따라서 `Swagger`를 사용하여 내가 구현한 Restful API를 
`OpenAPI spec`으로 문서화한다.


### 왜 사용해야하는가?
Restful API로 서비스를 구현하는 것은 서버사이드 렌더링을 활용하여
실제 화면을 구성하고 하이퍼링크로 url을 엮는 것이 아니기때문에
문서화는 필수이다. 이때, OpenAPI spec처럼 하나로 통일된 규격으로
문서화한다면 문서를 읽는 독자가 더욱 빨리, 정확하게 이해 할 수 있다.

즉, 협업의 효율을 높이는 역할을 한다.

뿐만아니라, Swagger를 활용하여 문서 자동화를 사용할 수 있고 자동으로 
Restful API를 html로 확인 할 수 있다.


> Swagger와 동일 선상에서 많이 사용되는 툴은 Spring Rest Docs가 있다.
> 다만, Spring Rest Docs는 OpenAPI Spec을 따르지않고 
> 독자적인 방법으로 restful api를 문서 자동화한다.
> 둘의 장단점 비교하는 글 작성 필요.

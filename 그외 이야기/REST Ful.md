## Rest Ful API
---
### Rest Ful Api 란
#### 월드 와이드 웹(World Wide Web W3)과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식으로
#### 자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반에 대한 패턴이다.

### REST 란
#### REpresentational State Transfer 의 약자이다.
#### Representation 이란 단어자체의 뜻은 '대표''표현', 이고 REST 에서 해석을 해보면 어떤 리소스의 정보이다.
#### State 는 여기서 상태를 나태내고, Transfer 는 전송을 나타낸다.
#### 즉, 리소스의 전송 상태 정보 로 생각되어지낟.
#### 여기에 ~ful 이라는 형용사형 어미를 붙여 ~한 API 라는 표현으로 사용된다.
#### 죽, REST 의 기본 원칙을 성실히 지킨 서비스 디자인은 'REST Ful' 하다라고 표현할 수 있다.
---
#### REST 를 좀더 정확히 표현하면 Resource Oriented Architecture 라고 할 수 있다.
#### API 설계의 중심에 자원(Resource)이 있고, Http Method 를 통해 자원을 처리하도록 설계하는 것이다.
---
### REST API 의 구성
#### 자원(Resource) - URI(Uniform Resource Identifier)
- 모든 자원에는 고유한 ID가 존재하고 이 자원은 Server 에 존재한다.
- Client 는 URI 를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server 에 요청을 한다.
#### 행위(Verb) - HTTP Method(HyperText Transfer Protocol)
- HTTP 프로토콜의 Method 를 사용한다.
- GET , POST , PUT , DELETE
#### 표현(Representation)
- Client 가 자원의 상태에 대한 조작을 Server 에 요청하면 적절한 응답( Representation ) 을 보낸다.
- REST 에서 하나의 자원은 JSON , XML , TEXT , CSV 등 여러 형태의 응답 ( Representation ) 으로 나타내어 진다.

### 참고 블로그 ;
[[Network] RESTful API란?](https://velog.io/@yes3427/network-about-RESTfulAPI)
---

##### 추가적으로 URI 는 URL(Uniform Resource Locator) 과 URN(Uniform Resource Name) 을 포괄한다.
##### 재밌는 예시로 http://endic.naver.com/endic.nhn?docid=1232950 와 http://endic.naver.com/endic.nhn?docid=1232690 는 
##### 같은 URL이고 다른 URI라고 할 수 있다.

### 참고 블로그 :
[URI & URL](https://velog.io/@jch9537/URI-URL)
---

### REST 6가지 원칙
1. Uniform Interface (일관된 인터페이스)
- URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일이다.
- REST 는 4가지 인터페이스 제약 조건으로 정의된다 - 자원식별 , 표현을 통한 자원 조작 , 자기 설명 메시지 , 응용 프로그램 상태의 엔진으로서의 하이퍼미디어

2. Stateless (무상태)
- 자겁을 위한 상태정보를 따로 저장하고 관리하지 않는다. 세션 정보나 쿠키 정보를 별도로 저장하고 관리하지 않기 때문에 API 서버는 들어오는 요청만 단순 처리하면 된다.
- 때문에 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해진다.
- 클라이언트에서 서버로 오는 각각의 요청은 이해할 수 있는 모든 정보를 포함해야 하며, 
- 서버쪽에서는 어떤 것도 얻을 수 없어야한다. 즉 세션 상태는 전적으로 클라이언트에서 유지된다.

3. Cacheable (캐시 처리 가능)
- REST 는 HTTP 라는 기존 웹 표준을 그대로 사용하기 때문에, HTTP 가 가진 캐싱 기능의 적용이 가능하다.
- 캐시 제약 조건에 따라 암묵적이거나 명시적으로 응답 내 데이터는 캐시 가능 혹은 불가능으로 레이블이 지정되어야 한다.
- HTTP 프로토콜 표준에서 사용하는 Last-Modified 태그나 E-Tag 를 이용하면 캐싱 구현이 가능하다.

4. Layered System (레이어화된 시스템)
- 레이어화된 시스템 스타일을 사용하면 아키텍처를 계층적으로 구성 할 수 있다.
- 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고, Proxy, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

5. Client - Server (클라이언트 - 서버)
- Client 는 사용자 인증이나 컨텍스트(세션, 로그인 정보) 등을 직접 관리하는 구조로 Server 와 Client 간의 역할이 확실히 구분된다.
- 즉 Client 의 관심사를 데이터 저장소의 관심사로부터 분리하여 여러 플랫폼에서 유저 인터페이스의 이식성을 개선하고 서버 컴포넌트를 단순하게 하여 유연하고 확장성을 높일 수 있다.

6. Code On Demand (주문형 코드 , 선택사항 - 권장x)
- 클라이언트는 리소스에 대한 표현을 응답으로 받고 처리하는데, 어떻게 처리하는지에 대한 code 를 서버가 제공하는 것을 의미한다.
- code 를 서버가 제공함으로써 클라이언트는 이를 실행하여 기능을 확장 할 수 있게 해준다.
- 그러나 서버에서 제공되는 코드를 실행해야 하기 때문에 보안 문제를 야기할 수 있다.

### 참고 블로그 : 

[RESTful 에 대한 이해 - 1](http://amazingguni.github.io/blog/2016/03/REST%EC%97%90-%EB%8C%80%ED%95%9C-%EC%9D%B4%ED%95%B4-1)

[Spring MVC Last-Modified, If-Modified-Since 캐시 설정](https://itstory.tk/entry/Spring-MVC-LastModified-IfModifiedSince-%EC%BA%90%EC%8B%9C-%EC%84%A4%EC%A0%95)

---

### REST Ful 하게 API 를 디자인 한다는 것은?
1. 리소스와 행위를 명시적이고 직관적으로 분리한다.
- 리소스는 URI 로 표현되는데 리소스가 가리키는 것은 '명사' 로 표현되어야 한다.
- 행위는 HTTP Method 로 표현하고 분명한 목적으로 사용한다. {  GET(조회), POST(생성), PUT(기존 entity 전체 수정), PATCH(기존 entity 일부 수정), DELETE(삭제) }

2. Message 는 Header 와 Body 를 명확하게 분리해서 사용한다.
- Entity 에 대한 내용은 body 에 담는다.
- 애플리케이션 서버가 행동할 판단의 근거가 되는 컨트롤 정보인 API 버전 정보, 응답받고자 하는 MIME 타입 등은 header 에 담는다.

3. API 버전을 관리한다.
- 환경을 항상 변하기 때문에 API의 signature 가 변경될 수도 있음에 유의해야 한다.
- 특정 API 를 변경할 때는 반드시 하위호환성을 보장해야 한다.

4. 서버와 클라이언틀가 같은 방식을 사용해서 요청하도록 한다.
- 서버와 클라이언트는 json 으로 보내든, 둘 다 form-data 형식으로 보내든 하나로 통일한다.
- 다른 말로 표현하자면 URI가 플랫폼 중립적이어야 한다.

---

### 장단점
1. 장점
- Open API 를 제공하기 쉽다.
- 멀티플랫폼 지원 및 연동이 용이하다.
- 원하는 타입으로 데이터를 주고 받을 수 있다.
- 기존 웹 인프라(HTTP) 를 그대로 사용할 수 있다.
2. 단점
- 사용할 수 있는 메소드가 4 가지 밖에 없다.
- 분산환경에는 부적합하다.
- HTTP 통신 모델에 대해서만 지원한다.

### 참고 블로그 : 
[[개발 상식] RESTful](https://woovictory.github.io/2018/04/18/devknowledge-RESTful/)

---
### 추가적인 TIP
1. 슬래시(/) 는 계층관계를 나타내는데 사용하라
2. URI 가독성을 위해 긴 경로는 하이픈(-) 을 사용해라
3. 언더바(_)는 사용하지 않는다.
4. 소문자로 작성해라
5. 파일 확장자는 URI 에 포함시키지 않는다.

### 참고 블로그 : 
[RESTful 하게 설계하기](https://alwaysone.tistory.com/entry/RESTful-%ED%95%98%EA%B2%8C-%EC%84%A4%EA%B3%84%ED%95%98%EA%B8%B0)
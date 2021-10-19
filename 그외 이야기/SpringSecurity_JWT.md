## SpringSecurity
<img src="SpringSecurity.png" width="450px">

### Spring Security 는 Http Request 요청을 받으면 Authentication Filter 가 작동한다.
### 이때 미검증 Authentication 인 UsernamePasswordAuthenticationToken(username(O), password(O), authorization(X)) 생성.
### AuthenticationManager 에게 전달되고, AuthenticationManager 구현체인 ProviderManager 에게 전달한다.
### ProviderManager 는 인증을 AuthenticationProvider 에게 인증을 위임,
### UserDetailsService 에 Token 을 넘겨 DB 에 들어있는 사용자 정보와의 조회를 시작한다.
### 조회가 통과되어 권한이 있는 사용자는 UserDetails 에 구현체인 User 객체에 정보를 담고 UsernamePasswordAuthenticationToken 에 인증을 담아
### SecurityContext 에 Authentication 객체로 저장된다.

##### 추가적으로 REST Ful 방식으로 구현시 SpringSecurity 는 교차출처리소스공유(Cross-Origin Resource Sharing)설정을 해주어야 (CorsConfigurationSource)
##### SpringSecurity 방식이 아니면 WebMvcConfigurer 상속받아 addCorsMappings 를 오버라이딩해서 설정해줘야한다.
### 참고 블로그 :

[[스프링] Spring Security 인증은 어떻게 이루어질까?](https://cjw-awdsd.tistory.com/45)

[[SpringBoot] Spring Security 처리 과정 및 구현 예제](https://mangkyu.tistory.com/77)

[[Spring Boot] CORS 설정하기](https://dev-pengun.tistory.com/entry/Spring-Boot-CORS-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)

---

## JWT (JSON Web Token)
### JSON 포맷을 이용하여 사용자에 대한 속성을 저장하는 Claim 기반의 Web Token 이다.
### JWT 는 토큰 자체를 정보로 사용하는 Self-Contained 방식으로 전달한다.
### 구성은 Header - Payload - Signature 로 나뉘어지며
### Header 는 토큰의 타입과 해시 암호화 알고리즘으로 구성되어 있다.
### 첫째는 토큰의 유형을 나타내고, 둘째는 해시 알고리즘을 나타낸다.
### Payload 는 토큰에 담을 Claim 정보를 포함하고 있다. 이때 정보의 한 조각을 Claim 이라 부르며, name/value 한 쌍으로 이루어져있다.
### Claim 의 정보로는 등록된 Claim(registered), 공개(public) Claim, 비공개(private) Claim 세 종류가 있다.
### Signature 는 Secret Key 를 포함하여 암호화되어 있다.
---
## JWT 의 장점 :
### 무상태성(Stateless) & 확장성(Scalability) - 서버와의 연결고리가 없다.
### 여러 플랫폼 및 도메인 - 서버 기반 인증 시스템의 문제점 중 하나인 CORS 를 해결할 수 있어, 어떤 도메인에서도 토큰의 검사후 요청을 처리할 수 있다.
### 보안성 - 기존에 쿠키 사용에 의한 취약점이 사라진다. 하지만 토큰사용으로 인한 보안 취약점 또한 명백히 존재하기에 조심해야야한다.

## JWT 의 단점 : 
### 데이터 트래픽의 증가 - 토큰에 들어가는 정보가 많아질 수록 토큰 길이가 증가해, 네트워크 부하를 줄 수 있다.
### Stateless - JWT 는 상태를 저장하지 않기 때문에 한번 토큰이 만들어지면 제어가 불가능하다. 때문에 반드시 만료시간을 넣어줘야 한다.

### 참고 블로그 :

[[Server] JWT(Json Web Token)란?](https://mangkyu.tistory.com/56)

[JWT ( JSON WEB TOKEN ) 이란?](http://www.opennaru.com/opennaru-blog/jwt-json-web-token/)

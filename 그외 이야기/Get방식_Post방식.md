## Get 방식
### Post 방식보다 상대적으로 속도가 빠르다.
### URL 에 쿼리스트링 형대로 보여지기때문에 보안성이 떨어진다.
### 길이 제한이 있다.
### 캐싱이 가능하다.
### Get 요청은 브라우저 히스토리에 남는다.
### Get 요청은 북마크 될 수 있다.
### 멱등성이다. (Idempotent)
### 요청에 Body 가 존재 하지 않는다.

---
## Post 방식
### 일정 크기 이상의 데이터를 보낼때 사용한다.
### URL 에 데이터를 노출하지 않고 Body 에 데이터를 담아 요청한다.
### 길이에 제한이 없다.
### Post 요청은 캐싱되지 않는다.
### Post 요청은 브라우저 히스토리에 남지 않는다.
### Post 요청은 북마크되지 않는다.
### 멱등성이 아니다. (Non-Idempotent)

#### 멱등이란? 연산을 여러번 적용하더라도 결과가 달라지지 않는 성질을 의미한다.
#### 캐시란? 캐시란 한번 접근 후, 또 요청할시 빠르게 접근하기 위해 메모리영역에 데이터를 저장시켜 놓는 것입니다.


### 참고 블록그 : 
[[네트워크] get 과 post 의 차이](https://noahlogs.tistory.com/35)

[[Web] GET과 POST의 비교 및 차이](https://mangkyu.tistory.com/17)

[GET과 POST의 차이](https://hongsii.github.io/2017/08/02/what-is-the-difference-get-and-post/)

[캐싱 개요](https://aws.amazon.com/ko/caching/)

[캐싱이란](https://velog.io/@inyong/%EC%BA%90%EC%8B%B1%EC%9D%B4%EB%9E%80)

[캐싱(caching)이란](https://net-gate.tistory.com/11)
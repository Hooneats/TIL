## AJAX (Asynchronous Javascript And XML)
### 자바스크립트를 사용한 비동기 통신 , Http 통신으로 request 와 response 를 비동기로 다룸.
### 웹페이지의 reLoad 없이 데이터를 요청할 수 있음
### AJAX 는 jQuery 를 써서 편리하게 좀 더 사용가능하다

### AXIOS (Promise based HTTP client for the browser and node.js)
#### XHR(XML Http Request) 는 잘 디자인 된 API 가 아니다.
#### 때문에 좀 더 Http 에 최적화 되어있고 코드도 간결한 AXIOS 와 FETCH 가 나오게 되었다.
#### Axios 는 node.js 와 브라우저를 위한 HTTP 통신 라이브러리이다.
#### 비동기로 HTTP 통신을 가능하게 해주며 return 을 promise 객체로 해주기 때문에 response 데이터를 다루기도 쉽다.

### Fetch
#### Axios 와 마찬가지로  비동기 통신을 위해 사용하며, promise 객체를 return 해준다.
#### Fetch 는 ES6(표준스펙 ECNAScript6) 부터 JavaScript 의 내장 라이브러리로 들어왔다.

### Axios 와 Fetch 차이점
|Axios       | Fetch        |
|:----------:|:------------:|
|설치가 쉽다   |설치가 필요없다  |
|XSRF 보안기능제공|없다.        |
|자동 JSON 데이터변환| .json() 메서드 필요|
|Request 취소와 Request Timeout 설정가능 | 없음|

#### Axios 는 설치를 해야하지만 설치자체가 간단하고 좀더 강력한 모습이다.
#### 또한 Axios 는 TypeScript 를 사용할 수 있으며, View.js 에서는 비동기 통신으로 Axios 를 권장하고 있다.
###### TypeScript 는 정적타입언어로 동적타입언어인 Javascript 데이터 안정성을 보장합니다. 또한 인터페이스, 상속 등을 지원하여 객체지향적으로 데이터를 다룰 수 있습니다.
###### promise 객체는 .then() 으로 실행을 보장받음으로써(마치 콜백함수 같이) 실행순서를 보장받는 로직 처리가 가능합니다.(.then() 의 사용으로 콜백지옥에서 벗어날 수 있다.)

---

#### 참고로 Ajax, Axios, Fetch 등이 나오기 이전에 비동기 통신은 XMLHttpRequest 객체를 직접 생성해 이루어 졌다.
#### 사용법은 요즘에는 잘 쓰지 않으니 블로그를 참고하자

### 참고 블로그 : 
[[개발상식] Ajax와 Axios 그리고 fetch](https://velog.io/@kysung95/%EA%B0%9C%EB%B0%9C%EC%83%81%EC%8B%9D-Ajax%EC%99%80-Axios-%EA%B7%B8%EB%A6%AC%EA%B3%A0-fetch)

[자바스크립트 Promise 쉽게 이해하기](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)

[Javascript | Fetch vs Axios 차이점 비교](https://yeonfamily.tistory.com/10)

[XMLHttpRequest 객체](http://www.ktword.co.kr/test/view/view.php?m_temp1=5939&id=1382)

[TCPSchool - XMLHttpRequest](http://tcpschool.com/xml/xml_dom_xmlHttpRequest)
---




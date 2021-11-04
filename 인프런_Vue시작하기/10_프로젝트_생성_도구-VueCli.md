## 프로젝트 생성 도구 - Vue CLI

---
### 최신 Vue CLI 소개
#### [Vue CLI 설치](https://cli.vuejs.org/guide/installation.html)

### Vue Cli 도구 설치 문제점
#### 설치시 권한이 없다고 하면 sudo 를 맨앞에 붙여 다시 설치해보기
#### 설치 위치는 윈도우 기준 %USERPROFILE%\AppData\Roaming\npm\node_modules 로 잡히게 된다.

### CLI 2.x 와 3.x 의 차이점 / 프로젝트 생성 및 서버 실행
### [Vue CLI 2.x]
#### vue init '프로젝트 템플릿 유형' '프로젝트 폴더 위치'
#### vue init webpack-simple '프로젝트 폴더 위치'
---
### [Vue DLI 3.x]
#### vue create '프로젝트 폴더 위치'
#### ex) vue create vue-cli

---

### CLI 로 생성한 프로젝트 폴더 구조 확인 및 main.js 파일 설명
#### npm 은 노드 팩키지 이고 이 노드 팩기지는 package.json 의 라이브러리들 에서
#### "scripts" 밑의 "serve" 라는 부분들이 실행되는 것이다.
#### 즉 npm run serve = vue-cli-service serve 와도 같다.

#### npm run serve 를 통해 실행되는 파일은 public 의 index.html 이며
#### div#app 밑에 빌트 파일들(src 밑에 정의되어있는 파일들)이 주입될 것이다.

#### main.js 에 new Vue({}).$mount('#app') 은 new Vue({ el: '#app' }) 과 같다.
#### render 는 기본적으로 templates 를 정의했을때 Vue 내부적으로 render 라는 함수가 실행된다.
```vue
var App = {
template: '<div>app</div>'
}
new Vue({
  el: '#app',
  components: {
    'app' : App
  }
})
```
### 이것과
```vue
new Vue({
  render: h => h(App),
}).$mount('#app')
```
### 은 같다(동일)

---

### 싱글 파일 컴포넌트 소개 및 여태까지 배운 내용 적용하는 방법
#### vud-cli 폴더 a.vue

---
### App.vue 와 HelloWorld.vue 설명


# Vuejs 개발환경 세팅
- [reference](https://velog.io/@ljinsk3/ESLint-Prettier-%EC%84%A4%EC%A0%95%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)
- 웹팩 v5 버전에서 발생하는 오류 > 웹팩 버전을 v4로 downgrade
    ```
    rule can only have one resource source (provided resource and test + include + exclude) in
    ```
  <br>
- eslint
    - vue-cli 3버전 이상에서는 package.json에 자동으로 eslint를 추가해준다
        - 이전 버전에서는 자동으로 eslint옵션을 추가하지않아서 package.json과 같은 위치에 `.eslintrs.js`를 추가해야했다
    - rules에 원하는 규칙을 추가한다
<br><br>
- router
    - [중첩 라우트](https://router.vuejs.org/kr/guide/essentials/nested-routes.html)
        
    ```javascript
    const routes = [
      {
          path:'/aaa',component: A,
          children: [
            {
                path:'bbb',component: B
            }
          ]  
      } 
    ] 
    ```
      
    - localhost:8080/aaa -> A 컴포넌트 렌더링
    - localhost:8080/aaa/bbb -> B 컴포넌트 렌더링
    - 이게 가능하려면 A 컴포넌트안에 `<router-view />`가 있어야한다. 부모 컴포넌트에 `<router-view />`가 없으면 routes에서 설정하더라도 localhost:8080/aaa/bbb -> A 컴포넌트로 이동한다
  
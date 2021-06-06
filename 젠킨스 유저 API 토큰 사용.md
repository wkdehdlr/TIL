# 젠킨스 유저 API 토큰 사용
> curl로 젠킨스 빌드를 유발한다거나 젠킨스 REST API를 호출할 때, 즉 Jenkins API통신에는 `API Token`이 필요하다.<br>
> 토큰이 없으면 `권한에러` 발생!
- 젠킨스

    ![1](./img/jenkins%20user%20api%20token/1.png)
<br><br>
- 젠킨스 REST API 권한에러

    ![2](./img/jenkins%20user%20api%20token/2.png)
<br><br><br>
## API 토큰 생성
- 메뉴 > 사람 > 토큰을 발행할 유저선택 > 설정 > API Token

    ![3](./img/jenkins%20user%20api%20token/3.png)
    ![4](./img/jenkins%20user%20api%20token/4.png)
    ![5](./img/jenkins%20user%20api%20token/5.png)
- 토큰을 생성하는 부분이 젠킨스 버전에 따라 살짝 차이가 있을수도 있음(`Show API Token`, `Add new Token` 등등)

    ![6](./img/jenkins%20user%20api%20token/6.png)
    ![7](./img/jenkins%20user%20api%20token/7.png)
- `정상호출`
    ```shell script
    curl 주소 --user userid:토큰
    curl userid:토큰@주소
    ```
    ![8](./img/jenkins%20user%20api%20token/8.png)
<br><br><br>
## RestTemplate 적용
- [소스](https://github.com/wkdehdlr/jenkins-api-token)
- RestTemplate에 `BasicAuth` 적용

    ![9](./img/jenkins%20user%20api%20token/9.png)
    ![10](./img/jenkins%20user%20api%20token/10.png)
## 레퍼런스
- [reference](https://oingdaddy.tistory.com/237)
- [reference](https://velog.io/@owljoa/190731-%EC%9E%84%EC%8B%9C)
- [reference](http://confluence.augkorea.org/pages/viewpage.action?pageId=19629608)
- [reference](https://fntg.tistory.com/204)

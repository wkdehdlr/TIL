# @WebMvcTest & @SpringBootTest
> [Actuator](https://github.com/wkdehdlr/actuator) 테스트 짜다가 알아본 내용
- Boot는 `8080`으로 띄우고 Actuator는 `8081`로 띄움
- ControllerTest
    ```java
    @WebMvcTest
    class MonitoringTest {
    
        @Autowired
        private MockMvc mockMvc;
        
        ...생략
    ```
  - 슬라이싱 테스트를 위해 `@WebMvcTest`를 사용하면 실제 서블릿 컨테이너가 구동되는건 아니기 때문에 `MockMvc`를 사용
<br><br>
- ActuatorTest
    ```java
    @SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)
    public class ActuatorTest {
    
        @Autowired
        private TestRestTemplate testRestTemplate;
        
        ...생략
    ```
    ```yaml
    management:
      server:
        port: 8081
  
    ...생략
    ```
  - Actuator같은 경우 지정한 포트로 실제 스프링이 구동된 환경에서 테스트를 진행해야 하기 때문에<br>
  @WebMvcTest가 아닌 `@SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)`를 사용
  - 또한 컨테이너가 실제로 구동되었기 때문에 MockMvc가 아닌 `TestRestTemplate`을 사용
<br><br>
- 실제 서블릿 컨테이너가 구동된 환경에서는 TestRestTemplate를 써야하고 Mock환경에서는 MockMvc를 써야함
<br><br>
## Web Environment
> @SpringBootTest의 webEnvironment 파라미터를 이용해서 테스트 환경을 선택
   
- MOCK(default)
    - WebApplicationContext를 로드
    - 실제 서블릿 컨테이너가 아닌 Mock 서블릿을 제공
    - MockMvc로 테스트하기 위해 @AutoConfigureMockMvc 어노테이션을 함께 사용
      ```java
      @SpringBootTest(webEnvironment = WebEnvironment.MOCK)
      @AutoConfigureMockMvc
      class blahblahTest{
      
          @Autowired
          private MockMvc mockMvc;
      
      ...생략
      ```
    - MockMvc를 사용하기 위해서는 @AutoConfigureMockMvc가 필요한데<br>
    @WebMvcTest에서는 바로 가능했던게 @WebMvcTest 애노테이션이 @AutoConfigureMockMvc를 가지고 있기 때문 
- RANDOM_PORT
    - WebApplicationContext를 로드
    - 실제 서블릿 환경을 구성
    - 임의의 포트로 서블릿 컨테이너 생성
- DEFINED_PORT
    - WebApplicationContext를 로드
    - 실제 서블릿 환경을 구성
    - application.properties 또는 application.yml에서 지정한 포트로 서블릿 컨테이너 생성
- NONE
    - 일반적인 ApplicationContext를 로드하며 아무런 서블릿 환경을 구성하지 않음
<br><br>
## 레퍼런스
- [reference](https://atoz-develop.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EB%82%B4%EC%9E%A5-%EC%84%9C%EB%B2%84-%EB%9E%9C%EB%8D%A4-%ED%8F%AC%ED%8A%B8%EB%A1%9C-%EB%9D%84%EC%9A%B0%EA%B8%B0)
- [reference](https://ict-nroo.tistory.com/96)
- [reference](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-testing-spring-boot-applications-testing-autoconfigured-mvc-tests)
- [reference](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-testing-spring-boot-applications)
- [reference](https://meetup.toast.com/posts/124)

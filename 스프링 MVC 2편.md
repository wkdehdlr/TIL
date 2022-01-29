# 스프링 MVC 2편
## 필터 & 인터셉터
> AOP로도 해결할 수 있지만<br>
> 웹과 관련된 공통 관심사를 처리할 때는 HTTP의 헤더나 URL의 정보들이 필요한데<br>
> 서블릿 필터나 스프링 인터셉터는 `HttpServletRequest`를 제공한다
### 필터
> `서블릿`이 제공하는 기능
- 특정 URL 패턴에 적용할 수 있다.(`/*`이라고 하면 모든 요청에 필터가 적용된다)<br><br>
- 필터흐름
```
HTTP 요청 > WAS > 필터 > 서블릿(디스패처 서블릿) > 컨트롤러
```
- 필터제한
```
로그인 : HTTP 요청 > WAS > 필터 > 서블릿 > 컨트롤러
비로그인 : HTTP 요청 > WAS > 필터
```
- 필터체인
```
HTTP 요청 > WAS > 필터1 > 필터2 > 필터3 > 서블릿 > 컨트롤러
```
- 필터 인터페이스
```java
public interface Filter {
    
    default void init(FilterConfig filterConfig) throws ServletException {}
    
    void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException ;
    
    default void destroy() {}
}
```
- `init`
    - 필터 초기화 메서드
    - 서블릿 컨테이너가 생성될 때 호출
- `doFilter`
    - 고객의 요청이 올 때 마다 호출
    - 필터 로직
    - `ServletRequest`는 기능이 별로 없어서 `HttpServletRequest`로 다운캐스팅 후 사용
- `destroy`
    - 필터 종료 메서드
    - 서블릿 컨테이너가 종료될 때 호출
<br><br>
- 필터 등록
```java
@Configuration
public class WebConfig {

    @Bean
    public FilterRegistrationBean<Filter> logFilter() {
        FilterRegistrationBean<Filter> filterFilterRegistrationBean = new FilterRegistrationBean<>();
        filterFilterRegistrationBean.setFilter(new LogFilter());
        filterFilterRegistrationBean.setOrder(1);
        filterFilterRegistrationBean.addUrlPatterns("/*");
        return filterFilterRegistrationBean;
    }
}

```
- 필터를 등록하는 방법은 여러가지가 있지만, 스프링 부트를 사용한다면 `FilterRegistrationBean`을 사용해서 등록한다
- commit
  - https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/b73a5bced9a86552e4d38c9ee42eef25da50831a
  - https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/1d841fbc5b2db7efab044ae5d31a924cae15aea9
### 인터셉터
> `스프링`이 제공하는 기능<br>
> 서블릿 필터보다 편리하고 더 정교하고 다양한 기능을 제공
- 필터 URL 패턴과는 다르고, 매우 정밀하게 설정할 수 있다(`addPathPatterns`, `excludePathPatterns`)
- 인터셉터 흐름
```
HTTP 요청 > WAS > 필터 > 서블릿 > 스프링 인터셉터 > 컨트롤러
```
- 인터셉터 제한
```
로그인 : HTTP 요청 > WAS > 필터 > 서블릿 > 인터셉터 > 컨트롤러
비로그인 : HTTP 요청 > WAS > 필터 > 서블릿 > 인터셉터
```
- 인터셉터 체인
```
HTTP 요청 > WAS > 필터 > 서블릿 > 인터셉터1 > 인터셉터2 > 인터셉터3 > 컨트롤러
```
- 인터셉터 인터페이스
```java
public interface HandlerInterceptor {

  default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
          throws Exception { return true; }

  default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
          @Nullable ModelAndView modelAndView) throws Exception {}

  default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
          @Nullable Exception ex) throws Exception {}
}
```
- 어떤 컨트롤러(`handler`)가 호출되는지 호출정보도 받을 수 있다
- 어떤 `modelAndView`가 반환되는지도 알 수 있다
- `preHandle` : 컨트롤러 호출 전(더 정확히는 핸들러 어댑터 호출 전)
- `postHandle` : 컨트롤러 호출 후(더 정확히는 핸들러 어댑터 호출 후) // **컨트롤러에서 예외가 터지면 호출되지 않는다**
- `afterCompletion` : 요청 완료 이후(뷰가 렌더링 된 이후에 호출) // **예외가 터져도 호출된다**
- `request.setAttribute(LOG_ID, uuid)`
  - 서블릿 필터의 경우 지역변수로 가능하지만, 인터셉터는 호출 시점이 완전히 분리되어 있다.
  싱글톤이기 때문에 멤버변수는 위험하다. 따라서 `request`에 담았다가 찾아서 사용한다
- commit
  - https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/a3f3e81b5bafdbe9e8296e3479b6215cf55f7355
  - https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/696bad51ec265ade85e00c3e63bb5adc0e618286
## 예외 처리와 오류 페이지
### 서블릿 예외 처리 방식 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/3ba831ab3814d45b7ade933d41496e5ecfc7d46b)
### `Exception`
  ```java
WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
### `response.sendError(HTTP 상태 코드, 오류 메시지)`
```java
WAS(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```
- 호출한다고 당장 예외가 발생하는게 아니라, 서블릿 컨테이너에게 오류가 발생했음을 전달
- 서블릿 컨테이너는 응답 전에 response에 sendError를 확인하고, 존재하면 설정에 따라 오류 페이지를 보여준다
### 기본 오류 페이지
> 스프링 기본 예외페이지를 끄고 실행하면 WAS(여기선 부트니까 톰캣) 에러페이지로 응답이 온다
```properties
server.error.whitelabel.enabled=false
```
### 오류 페이지 추가 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/c177f3700233ac5dc11fd18503d88db8c0809def)
- 과거에는 `web.xml`에 등록해서 사용
```xml
<web-app>
  <error-page>
    <error-code>400</error-code>
    <location>/error-page/400.html</location>
  </error-page>
  <error-page>
    <error-code>500</error-code>
    <location>/error-page/500.html</location>
  </error-page>
  <error-page>
    <exception-type>java.lang.RuntimeException</exception-type>
    <location>/error-page/500.html</location>
  </error-page>
</web-app>
```
- 커스터마이징하게 오류페이지 등록이 가능
```java
@Component
public class WebServerCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {

    @Override
    public void customize(ConfigurableWebServerFactory factory) {

        ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/error-page/400");
        ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error-page/500");
        ErrorPage errorPageEx = new ErrorPage(RuntimeException.class, "/error-page/500");

        factory.addErrorPages(errorPage404, errorPage500, errorPageEx);
    }
}
```
### 오류 페이지 작동 원리
> WAS는 오류 페이지를 출력하기 위해 설정한 정보로 다시 요청한다 -> 이때 필터, 인터셉터도 모두 다시 통과한다(설정필요)<br>
> **서버 내부에서 요청이 다시 시작된다(HTTP 요청이 새로 들어온건 아니다)**
- 에러 페이지 요청 흐름
  - 1번 요청은 `dispatchType=REQUEST`
  - 2번 요청은 `dispatchType=ERROR`
```
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러
2. WAS(/error-page/500) 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View
```
- WAS가 에러 페이지 재요청을 할 때는 오류 정보를 `request`에 담아서(`attribute`) 넘겨준다 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/058c52a8ef18f1e5d5b897489a44d3007c7ba319)
  - `request.getAttribute(RequestDispatcher.~~~)`
  - `RequestDispatcher`에 상수로 정의되어 있음
### 예외 처리 - 필터 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/d664a158967de794eeadf5b0c5a4470997d8e704)
- 기본값은 `DispatcherType.REQUEST`
- 설정한 `DispatcherType`에 대해 필터를 통과한다
  - 아래 예시는 `DispatcherType.REQUEST`, `DispatcherType.ERROR`에 대해 필터 적용
```java
filterFilterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);
```
### 예외 처리 - 인터셉터 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/832668fe34bc4459f2644365e9175c4ae5901a6d)
- 경로를 설정
```java
.addPathPatterns("/**")
.excludePathPatterns("/css/**", "/*.ico", "/error");
```
### 스프링 부트
> `BasicErrorController`를 제공<br>
> 오류 페이지 화면만 `BasicErrorController`에 따라 등록하면 된다<br>
> 커스터마이징 설정이 기본으로 들어가있음
- `BasicErrorController`의 처리 순서
  1. 뷰 템플릿
     - resources/templates/error/500.html
     - resources/templates/error/5xx.html
  2. 정적 리소스(static, public)
      - resources/static/error/400.html   
      - resources/static/error/404.html   
      - resources/static/error/4xx.html   
  3. 적용 대상이 없을 때 뷰 이름(error)
      - resources/templates/error.html
## API 예외 처리
> HTTP Header `Accept`가 `application/json`이 아니면 기존 HTML 응답이 내려간다 []()
### 스프링 부트 기본 오류 처리
> `BasicErrorController`
- 클라이언트 요청의 Accept 헤더값이 `text/html`이면 `errorHtml()`, 그 외에는 `error()` 호출 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/77e7b18033428d55e1159cf6a5a1463602c6e9b4)
```java
@RequestMapping(produces = MediaType.TEXT_HTML_VALUE)
public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
    HttpStatus status = getStatus(request);
    Map<String, Object> model = Collections
            .unmodifiableMap(getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.TEXT_HTML)));
    response.setStatus(status.value());
    ModelAndView modelAndView = resolveErrorView(request, response, status, model);
    return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
}

@RequestMapping
public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
    HttpStatus status = getStatus(request);
    if (status == HttpStatus.NO_CONTENT) {
        return new ResponseEntity<>(status);
    }
    Map<String, Object> body = getErrorAttributes(request, getErrorAttributeOptions(request, MediaType.ALL));
    return new ResponseEntity<>(body, status);
}
```
- `BasicErrorController`는 HTML 화면을 처리할 때 사용하고, API는 `@ExceptionHandler`를 사용하는것을 추천!!
### HandlerExceptionResolver [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/d5e03cf7a756286439ab81b374937f2d5e58017b)
- ExceptionResolver로 예외를 해결해도 `postHandle()` 호출은 없다
```java
public interface HandlerExceptionResolver {
	@Nullable
	ModelAndView resolveException(
			HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex);

}
```
- `ModelAndView`를 반환하는 이유는 Exception을 처리해서 정상흐름처럼 변경하는 것이 목적이기 때문
### 반환 값에 따른 동작 방식
- `HandlerExceptionResolver`의 반환 값에 따라 `DispatcherServlet` 동작 방식이 다르다

|반환|동작|
|---|---|
|빈 ModelAndView|`new ModelAndView()`처럼 빈 ModelAndView를 반환하면 뷰를 렌더링 하지 않고, 정상 흐름으로 서블릿이 리턴|
|ModelAndView 저장|저장한 정보(View, Model 등)를 바탕으로 뷰를 렌더링|
|null|다음 `ExceptionHandlerResolver`를 찾아서 실행한다. 처리할 수 있는 ExceptionHandlerResolver가 없으면 기존에 발생한 예외를 서블릿 밖으로 던진다|
### ExceptionResolver 활용 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/bde9e779194c66aed8028807309d0f0303789bb6)
- 예외 상태 코드 변환
    - `response.sendError(xxx)` 호출
- 뷰 템플릿 처리
    - `ModelAndView`에 값을 채워서 뷰를 렌더링
- API 응답 처리
    - `response.getWriter().println("xxx")` 처럼 HTTP 응답 바디에 직접 데이터를 넣어주는 것도 가능하다
### 스프링이 제공하는 ExceptionResolver
- 스프링 부트가 기본으로 제공하는 ExceptionHandler
    - ExceptionHandlerExceptionResolver
    - ResponseStatusExceptionResolver
      - HTTP 상태 코드를 지정
      - `@ResponseStatus(value = HttpStatus.NOT_FOUND)`
    - DefaultHandlerExceptionResolver
- `ResponseStatusExceptionResolver` [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/101c0e97d6138958706bf545760780ece6b09b32)
    - 결국 코드 안에서 `response.sendError()`를 호출한다 > **WAS에서 다시 오류페이지를 내부 요청한다**
    - `ResponseStatusException`
        - `@ResponseStatus`는 개발자가 직접 변경할 수 없는 예외에는 적용할 수 없기 때문에 이때는 `ResponseStatusException`을 사용
- `DefaultHandlerExceptionResolver` [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/5e8a624290413ca7d00a05696a51517e610351ae)
    - 스프링 내부에서 발생하는 스프링 예외를 처리
    - 대표적으로는 `TypeMissmatchException`
- `ExceptionHandlerExceptionResolver`
### @ExceptionHandler [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/316e8a6dd888d3ac827108516ddf9660c9d4224a)
    - `ExceptionHandlerExceptionResolver`가 `@ExceptionHandler`를 처리한다
    - 기본으로 제공되는 ExceptionResolver 중에 우선순위가 가장 높다
### @ControllerAdvice [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/831a730003295b436ee748c52c8677c7da896429)
## 동적 프록시
### JDK 동적 프록시 [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/f0d996ad84f3da5be7e50250a5a6f2b4227c8672)
> 개발자가 직접 프록시 클래스를 만들지 않아도 된다<br>
> 이름 그대로 객체를 동적으로 런타임에 개발자 대신 만들어준다
- InvocationHandler
```java
public interface InvocationHandler {
    Object invoke(Object proxy, Method method, Object[] args) throws Throwable;
}
```
- `주의`
    - JDK 동적 프록시는 `인터페이스`를 기반으로 프록시를 만들기 때문에 `인터페이스가 필수`다.

![스크린샷 2022-01-29 오후 10 44 53](https://user-images.githubusercontent.com/26949623/151663381-78b89ef7-6a8f-44ea-8906-d8caa8bb91f2.png)
![스크린샷 2022-01-29 오후 10 06 26](https://user-images.githubusercontent.com/26949623/151662174-ad16f981-6fc9-4b5b-b9f8-82a312b0c19b.png)
### CGLIB [commit](https://github.com/wkdehdlr/wkdehdlr-spring-mvc-2/commit/17fd6be0b5f71c97815aef1893aa16f1bdf10dbc)
> 바이트 코드를 조작해서 동적으로 클래스를 생성<br>
> `인터페이스가 없어도` 구체 클래스만 가지고 동적 프록시를 만들 수 있다.<br>
> 스프링 `ProxyFactory`가 이 기술을 편리하게 사용할 수 있도록 도와준다.
- MethodInterceptor
```java
public interface MethodInterceptor extends Callback {
    Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
}
```
![스크린샷 2022-01-29 오후 10 43 16](https://user-images.githubusercontent.com/26949623/151663350-736199ec-8522-4fee-b470-0e882db2e441.png)
![스크린샷 2022-01-29 오후 10 46 04](https://user-images.githubusercontent.com/26949623/151663416-bca739b8-73ef-4caa-a55b-829bf3dc33b2.png)
- 상속을 사용하기 때문에 제약이 있다.
    - 부모 클래스의 생성자를 체크해야 한다
    - 클래스에 `final`이 붙으면 상속이 불가 -> CGLIB에서 예외발생
    - 메소드에 `final`이 붙으면 해당 메소드 오버라이딩 불가 -> CGLIB 프록시 로직 동작 안함


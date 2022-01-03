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
  
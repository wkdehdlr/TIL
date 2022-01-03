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

### 인터셉터
> `스프링`이 제공하는 기능
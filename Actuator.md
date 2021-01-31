# Actuator
> 스프링부트 애플리케이션에서 제공하는 여러가지 정보를 모니터링 하기 쉽게 해주는 기능<br>
> `HTTP endpoints` 또는 `JMX`를 사용할 수 있다.

## Endpoints
- 각 endpoint는 `enabled or disabled`, `exposed 여부`(HTTP, JMX에서 접근가능하게)를 설정할 수 있다.
- HTTP로 접근할 때 각 endpoint는`기본 prefix`로 `/actuator`를 사용하며, `/actuator/endpoint` 형태로 접근한다. ex> `health` -> `/actuator/health`
- 기본 prefix, endpoint 경로는 모두 커스터마이징 가능하다.
- [endpoint 목록](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
<br><br>
## Endpoint 활성화
- 기본값으로 `shutdown`을 제외한 모든 endpoint는 `enabled`
    ```yaml
    management:
      endpoint:
        shutdown:
          enabled: true
    ```
  <br>
- 반대로 `모두 disabled`하고 `특정 endpoint enabled`
    ```yaml
    management:
      endpoints:
        enabled-by-default: false
      endpoint:
        info:
          enabled: true
    ```
<br><br>
## Endpoint 노출
- endpoint를 `disabled`하면 application context 전체에서 삭제된다. 따라서 `노출여부`만 설정하고 싶다면 `include, exclude` 속성을 사용
- 활성화(`enable`)와 노출(`exposure`)은 별개로 간다!
- [default endpoint exposure](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints-exposing-endpoints)
- 모든 endpoint 노출
    ```yaml
    management:
      endpoints:
        web:
          exposure:
            include: "*"
    ```
<br><br>
## Endpoint Path 재설정
- base-path (ex> `/actuator` -> `/manage`)
    ```yaml
    management:
      endpoints:
        web:
          base-path: "/manage"
    ```
  <br>
- endpoint (ex> `/actuator/health` -> `/healthcheck` )
    ```yaml
    management:
      endpoints:
        web:
          base-path: "/"
          path-mapping:
            health: "healthcheck"
    ```
<br><br>
## 서버포트 재설정
- management 서버포트
- address를 "127.0.0.1"로 지정하면 8081 actuator endpoint에 localhost로 접근할때만 가능
    ```yaml
    management:
      server:
        port: 8081
        address: "127.0.0.1"
    ```
<br><br>
## 사용자정의 HealthIndicator
- `HealthIndicator` 인터페이스
    ```java
    package org.springframework.boot.actuate.health;
    
    @FunctionalInterface
    public interface HealthIndicator extends HealthContributor {
    
        default Health getHealth(boolean includeDetails) {
            Health health = health();
            return includeDetails ? health : health.withoutDetails();
        }
    
        Health health();
    
    }
    ```
  <br>
- `HealthIndicator` 인터페이스 구현
    ```java
    @Component
    public class MyHealthIndicator implements HealthIndicator {
    
        private final AtomicReference<Health> healthRef = new AtomicReference<>(Health.up().build());
    
        @Override
        public Health health() {
            return healthRef.get();
        }
    
        public Health up() {
            Health up = Health.up().build();
            healthRef.set(up);
            return up;
        }
    
        public Health down() {
            Health down = Health.down().build();
            healthRef.set(down);
            return down;
        }
    }
    ```
  <br>
  
    ```java
    @RestController
    @RequiredArgsConstructor
    @RequestMapping("/monitor/l7check")
    public class Monitoring {
        private final MyHealthIndicator myHealthIndicator;
    
        @GetMapping
        public ResponseEntity<Health> health() {
            Health health = myHealthIndicator.health();
            boolean isUp = health.getStatus().equals(Status.UP);
            return ResponseEntity.status(isUp ? HttpStatus.OK : HttpStatus.SERVICE_UNAVAILABLE).body(health);
        }
    
        @PostMapping("/start")
        public ResponseEntity<Health> up() {
            return ResponseEntity.ok(myHealthIndicator.up());
        }
    
        @DeleteMapping("/stop")
        public ResponseEntity<Health> down() {
            return ResponseEntity.ok(myHealthIndicator.down());
        }
    }
    ```
  - `Controller`를 통한 healthcheck: localhost:8080/monitor/l7check
  - `actuator`를 통한 healthcheck: localhost:8081/healthcheck
<br><br>
## 보안
> endpoints에는 민감한 정보가 있기에 보호되어야한다.
```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http.requestMatcher(EndpointRequest.toAnyEndpoint()).authorizeRequests((requests) ->
        requests.anyRequest().hasRole("ENDPOINT_ADMIN"));
    http.httpBasic();
    return http.build();
}
```
<br><br>
## 레퍼런스
- [reference](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready)
- [reference](https://forward.nhn.com/2020/seoul/hands-on-labs/java.spring-boot-actuator/01-ready.html)
- [reference](https://velog.io/@neptunes032/Spring-Boot-Actuator-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%84%9C-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EB%AA%A8%EB%8B%88%ED%84%B0%EB%A7%81%ED%95%98%EA%B8%B0)
- [reference](https://twofootdog.tistory.com/22)
- [reference](https://silvernine.me/wp/?p=1072)
- [reference](https://devday.tistory.com/m/3576?category=609812)

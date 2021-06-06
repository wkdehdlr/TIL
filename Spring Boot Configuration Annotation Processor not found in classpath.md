# Spring Boot Configuration Annotation Processor not found in classpath
- 스프링부트에서 @ConfigurationProperties을 사용할 때 `Spring Boot Configuration Annotation Processor not found in classpath`가 발생하면 의존성을 추가하자
- Gradle 4.6 이후면 아래와 같은 의존성을 사용하면 된다.
    ```yaml
    dependencies {
        annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'
    }
    ```
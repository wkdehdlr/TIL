# Spring RabbitMQ
> RabbitMQ를 도커로 띄우고 스프링과 연동해보기

<br><br>
## RabbitMQ 설치
- `management plugin`이 적용된 rabbitmq image를 pull
    - management plugin이 적용된 rabbitmq로 띄워야 어드민페이지 접속이 가능하다
    ```shell script
    docker pull rabbitmq:3-management
    ``` 
<br><br>
## RabbitMQ 실행
- RabbitMQ(5672), 어드민(15672)
    ```shell script
    docker run -d --hostname my-rabbit --name some-rabbit -p 5672:5672 -p 15672:15672 rabbitmq:3-management
    ```
<br><br>
## 어드민 접속
- localhost:15672
- Username : guest
- Password : guest
![1](./img/Spring%20RabbitMQ/screenshot202102141850.png)
<br><br>
## 큐 생성
![2](./img/Spring%20RabbitMQ/screenshot202102141856.png)
<br><br>
## 스프링설정
```yaml
spring:
  rabbitmq:
    host: localhost
    username: guest
    password: guest
    port: 5672
```
<br><br>
## [소스](https://github.com/wkdehdlr/rabbitmq)
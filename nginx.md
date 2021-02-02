# nginx

## SSL
```shell script
server {
        listen 443;
        server_name myssl.com
      
        ssl           on;
        
}
```
<br><br>
## 로드밸런싱
<br><br>
## NGINX 에러처리
##### 웹서비스
```shell script
server {
        location / {
            
        }   
        error_page 400 401 403 404 /error/aaabbbccc
        error_page 500 503 505 505 /error/xxxyyyzzz
}
```
- Spring boot + Vuejs로 구성했는데 NGINX에러가 발생하면 `/error/블라블라블라`로 이동하는데 바로 위 `location /` 에 걸려서 웹서비스 애플리케이션으로 이동한다.
- Vuejs Router 설정에서 /error/aaabbbccc, /error/xxxyyyzzz를 받아 처리한다.
```javascript
// router
```
<br><br>
##### API SERVER
```shell script
server {
        location / {
            
        }   
        error_page 400 401 403 404 /4xx.html
        error_page 500 503 505 505 /5xx.html
        
        location = /4xx.html {
            root /usr/
        }

        location = /5xx.html {
            root /usr
        } 
}
```
- API 서버는 json으로 응답이 나가니까 에러에 대한 html을 지정하고<br>html에 리턴할 json을 정의
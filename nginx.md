# nginx

## Reverse Proxy
```shell script
server {
        listen 80;
        server_name myproxy.com
        
        location / {
              # 프록시서버에 request header를 다시 재정의해서 전달할 때 사용
              proxy_set_header    Host                $host;

              # X-Forwarded-For는 프록시환경에서 client IP를 식별하기 위한 헤더로 X-Real-IP를 사용하기도 함
              proxy_set_header    X-Real-IP           $remote_addr;   
              proxy_set_header    X-Forwarede-For     $proxy_add_x_forwarded_for;

              proxy_pass http://127.0.0.1:8080;  
        } 
}
```
<br><br>
## SSL
```shell script
server {
        # NGINX 1.15 이전 버전에서는 listen의 ssl 인자를 사용하지 않고 ssl on; 을 지정해야함
        listen 443 ssl;
        server_name myssl.com;
      
        ssl_certificate           경로/myssl_cert.pem;
        ssl_certificate_key       경로/myssl.key;
        ssl_password_file         경로/password;

        # worker process가 공통으로 세션 캐시를 사용할 수 있도록 shared memory에 저장하도록 설정하고
        # 세션캐시 메모리 공간을 설정한다.(예제 20MB)
        # Nginx에 문서에 따르면 1MB 저장소는 4000 세션,
        # 우리는 20MB 저장소의 80000 세션을 설정
        ssl_session_cache    shared:SSL:20m;

        # session id가 유효한 시간 정의(예제 180분)
        ssl_session_timeout  180m;

        # 허용가능한 cipher list
        ssl_ciphers HIGH:!aNULL:!MD5;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        # 클라이언트에서 보내온 cipher list 중, 서버가 선호하는 cipher list를 우선시하여 사용
        ssl_prefer_server_ciphers  on;
}

# http로 접속하면 https로 리다이렉트
server {
        listen 80;
        server_name myssl.com;
      
        location / {
            return 301 https://$server_name$request_uri;
        } 
}
```
<br><br>
## 로드밸런싱
```shell script
server {
  location / {
    proxy_pass http://loadbalancing;
  }
}

upstream loadbalancing {
  # default round_robin
  # least_conn, ip_hash;
  ip_hash;
  
  # weight=n: 업스트림 서버의 비중. 이 값을 2로 설정하면 그렇지 않은 서버에 비해 두배 더 자주 선택
  # max_fails=n: n으로 지정한 횟수만큼 실패가 일어나면 서버가 죽은 것으로 간주
  # fail_timeout=n: max_fails가 지정된 상태에서 이 설정만큼 서버가 응답하지 않으면 죽은 것으로 간주
  # down: 해당 서버를 사용하지 않게 지정. ip_hash가 설정된 상태에서만 유효
  # backup: 모든 서버가 동작하지 않을 때 backup으로 표시된 서버가 사용되고 그 전까지는 사용되지 않는다.
  server 127.0.0.1:7000 weight=2 max_fails=3 fail_timeout=30s;
  server 127.0.0.1:8000 max_fails=3 fail_timeout=30;
  server 127.0.0.1:9000 backup;
}
```
<br><br>
## NGINX 에러처리
##### 웹서비스
```shell script
server {
        location / {
            proxy_pass http://127.0.0.1:8080;
        }   
        error_page 400 401 403 404 /error/aaabbbccc
        error_page 500 /error/xxxyyyzzz
}
```
```javascript
// vue router
const routes = [
    {
        ...생략
        children: [
            {
                { path: 'error/:errorCode', name: 'Error', component: Error},
                { path: '/error/aaabbbccc', name: 'Error4xx', redirect: { name: 'Error', params: { errorCode: 'aaabbbccc'} }},
                { path: '/error/xxxyyyzzz', name: 'Error5xx', redirect: { name: 'Error', params: { errorCode: 'xxxyyyzzz'} }}
            }   
        ]   
    }
]
```
- Spring boot + Vuejs로 구성했는데 NGINX에러가 발생하면 `/error/블라블라블라`로 이동하는데 바로 위 `location /` 에 걸려서 웹서비스 애플리케이션으로 이동한다.
- Vuejs Router 설정에서 /error/aaabbbccc, /error/xxxyyyzzz를 받아 처리한다.

<br><br>
##### API SERVER
```shell script
server {
        location / {
            proxy_pass http://127.0.0.1:8080;
        }   

        error_page 400 401 403 404 /4xx.html
        error_page 500 /5xx.html
        
        location = /4xx.html {
            root 경로/4xx.html
        }

        location = /5xx.html {
            root 경로/5xx.html
        } 
}
```
- API 서버는 json으로 응답이 나가니까 에러에 대한 html을 지정하고<br>html에 리턴할 json을 정의
<br><br>
## 레퍼런스
- [reference](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect)
- [reference](https://ohgyun.com/603)
- [reference](https://www.mynotes.kr/optimizing-https-on-nginx/)
- [reference](https://server-talk.tistory.com/315)
- [reference](https://m.blog.naver.com/PostView.nhn?blogId=seri0528&logNo=20187762421&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [reference](https://techlog.io/Server/HTTPS/nginx%EC%97%90-https-ssl-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0/)
- [reference](https://developer88.tistory.com/299)
- [reference](https://kscory.com/dev/nginx/loadbalancer)
# logrotate
> 로그파일을 관리

<br><br>
## logrotate 관련파일
-  파일

    |파일|설명|
    |---|---|
    |/usr/sbin/logrotate|데몬의 위치 및 데몬프로그램|
    |/etc/logrotate.conf|설정 파일|
    |/etc/logrotate.d|logrotate를 적용할 로그 파일 보관 디렉토리|
    |/var/lib/logrotate.status|logrotate가 작업 내역 보관 파일|
    |/etc/cron.daily/logrotate|cron에 의해 `일` 단위로 실행|
<br><br>
## 동작순서
1. cron.daily에서 /usr/sbin/logrotate 호출
2. /usr/sbin/logrotate 에서 /etc/logrotate.conf 설정파일 참조
3. /etc/logrotate.conf 설정 파일에서 /etc/logrotate.d 참조(logrotate.conf 파일안에 `include /etc/logrotate.d`)
<br><br>
## logrotate 옵션
- 옵션

    |옵션|설명|
    |---|---|
    |monthly, weekly, daily|월, 주 일 단위로 로테이트 진행|
    |rotate [숫자]|log파일이 `숫자`개 이상 되면 삭제|
    |maxage [숫자]|log파일이 `숫자`일 이상 되면 삭제|
    |size [용량]|지정한 용량이 되면 로테이트 실행 ex> size 100k|
    |create [권한][유저][그룹]|로테이트되는 로그파일 권한 지정|
    |notifempty|로그 내용이 없으면 로테이트 하지 않음|
    |ifempty|로그 내용이 없어도 로테이트 진행(`default`)|
    |compress|로테이트 되는 로그파일 gzip 압축|
    |nocompress|로테이트 되는 로그파일 압축 X(`default`)|
    |missingok|로그파일이 없어도 에러로 처리하지 않음|
    |dateext|백업 파일 이름에 날짜가 들어가도록 함|
    |copytrucate|이 옵션이 없으면 현재 사용중인 로그를 다른이름으로 move하고 새로운 파일을 생성|
    |prerotate-endscript|사이 명령어를 로그파일 처리 전 실행|
    |postrotate-endscript|사이 명렁어를 로그파일 처리 후 실행|
<br><br>
## logrotate 등록
```shell script
sudo vi /etc/logrotate.d/myrotate

myrotate경로/*log {
  daily
  maxage 90
  copytruncate
  compress
  ifempty
  missingok
  dateext
}
```
```shell script
# /etc/logrotate.d/ 위치가 아닌 다른 경로(/opt/myrotate)에 있으면 crontab에 직접 등록해서 실행
sudo vi /etc/crontab

0 * * * * root /usr/sbin/logrotate /opt/myrotate
```
<br><br>
## 실행
```shell script
# -f 와 -d 옵션은 함께 사용하지 못함
# -f 강제실행옵션
/usr/sbin/logrotate -f /etc/logrotate.d/myrotate

# -d 디버그 모드 
/usr/sbin/logrotate -d /etc/logrotate.d/myrotate
```
<br><br>
## 레퍼런스
- [reference](https://byd0105.tistory.com/29)
- [reference](https://m.blog.naver.com/PostView.nhn?blogId=zinyboy&logNo=220651013609&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [reference](https://kykkyn2.tistory.com/75)
- [reference](https://blueskai.tistory.com/101)
- [reference](https://server-talk.tistory.com/271)
- [reference](https://m.blog.naver.com/PostView.nhn?blogId=didim365_&logNo=220405747299&proxyReferer=https:%2F%2Fwww.google.com%2F)
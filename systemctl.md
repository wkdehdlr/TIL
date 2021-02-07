# systemctl
> CentOS 7부터 서비스 관리 스크립트들은 몇몇 서비스를 제외하고 각 서비스 유닛(Unit)으로 변경되고 `systemctl`로 제어
## 부팅 시간 표시
```shell script
systemd-analyze
```
<br><br>
### 서비스별 초기화 시간
```shell script
systemd-analyze blame
```
<br><br>
### 서비스 목록 확인
```shell script
systemctl
```
<br><br>
### 전체 서비스
```shell script
systemctl list-unit-files
```
<br><br>
### 서비스 정보보기
```shell script
systemctl show sshd
```
<br><br>
### 부팅시 활성화 여부
```shell script
systemctl disable 서비스명
systemctl enable 서비스명
```
<br><br>
### 시작 중지
```shell script
systemctl start 서비스명
systemctl stop 서비스명
systemctl restart 서비스명
systemctl reload 서비스명
systemctl kill 서비스명
```
<br><br>
### 서비스 설정반영
```shell script
systemctl daemon-reload
```
<br><br>
### 의존성 파악
```shell script
systemctl list-dependencides 서비스명
```
<br><br>
## 현재 시스템이 사용 중인 기본 target 확인
```shell script
systemctl get-default
```
<br><br>
## 활성화된 모든 target 출력
```shell script
systemctl list-units --type target
```
<br><br>
## 활성화 및 비활성화된 모든 target 출력
```shell script
systemctl list-units --type target all
```
<br><br>
## 레퍼런스
- [reference](https://jm4488.tistory.com/31)
- [reference](http://oniondev.egloos.com/9972854)
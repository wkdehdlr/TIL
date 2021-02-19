# EC2 Permission Denied
> 인스턴스 유형과 username이 맞지 않아서 발생

- pem 파일 권한 수정
```shell script
chmod 400 [key path]
```
<br><br>
- Amazon Linux AMI의 사용자 이름 : `ec2-user`
- Ubuntu AMI의 사용자 이름 : `ubuntu`
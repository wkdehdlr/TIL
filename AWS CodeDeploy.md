# AWS CodeDeploy
CodeDeploy는 Amazon EC2 인스턴스, 온프레미스 인스턴스, 서버리스 Lamda 함수 또는 Amazon ECS 서비스로 애플리케이션 배포를 자동화하는 배포서비스다.
CodeDeploy를 활용하면 Amazon S3 버킷, GitHub 레파지토리 또는 Bitbucket 레파지토리에 저장된 code, 
서버리스 AWS Lamda 함수, 웹 및 구성파일, 실행파일, packages, 스크립트, 멀티미디어 파일 등을 거의 무제한으로 배포가 가능
![25](./img/aws%20codedeploy/25.png)
> `CodeDeploy`가 `S3`에서 아티팩트(zip)를 가져와서 `EC2 서버`에 배포
# 세팅
- AWS CodeDeploy를 만들기 위해서는 총 2개의 IAM 역할이 필요
    - AWS CodeDeploy를 생성하기 위한 역할
    - EC2 인스턴스에서 CodeDeploy Agent를 활용하여 S3에 있는 아티팩트(배포파일)를 가져오기 위한 S3 접근 가능 역할
<br><br><br>
## AWS CodeDeploy용 IAM 역할 생성
- 역할만들기<br>
![1](./img/aws%20codedeploy/1.png)
<br><br><br>
- CodeDeploy를 선택하고 사용 사례에서 CodeDeploy를 선택한다(CodeDeploy - ECS 아님)
![2](./img/aws%20codedeploy/2.png)
<br><br><br>
- 해당 정책이 어떤 권한을 갖는지 알 수 있다.<br>
![3](./img/aws%20codedeploy/3.png)
<br><br><br>
- 역할 이름을 설정<br>
![4](./img/aws%20codedeploy/4.png)
<br><br><br>
## AWS EC2 인스턴스에서 S3 접근을 위한 IAM 역할 생성
- AWS 서비스에서 EC2 사용 사례를 선택<br>
![5](./img/aws%20codedeploy/5.png)
<br><br><br>
- `AmazonEC2RoleforAWSCodeDeploy` 선택<br>
![6](./img/aws%20codedeploy/6.png)
<br><br><br>
- 정책을 확인하면 S3 접근을 허용하는걸 알 수 있다<br>
![7](./img/aws%20codedeploy/7.png)
<br><br><br>
- 역할 이름을 설정<br>
![8](./img/aws%20codedeploy/8.png)
<br><br><br>
## EC2 인스턴스 역할부여
- EC2 인스턴스를 새로 만들면서 진행한다.
- 추후 yum 으로 agent 파일을 설치해야하는데 ubuntu로 깔면 초기에 yum이 없다. 따라서 Amazon Linux로 진행<br>
![9](./img/aws%20codedeploy/9.png)
<br><br><br>
- EC2 인스턴스에 위에서 만든 `S3 접근 가능 IAM 역할`을 부여<br>
![10](./img/aws%20codedeploy/10.png)
<br><br><br>
- CodeDeploy에서 배포그룹으로 이 인스턴스를 배포 대상으로 선택할 때 태그로 인스턴스를 구분하기 위해 태그추가<br>
![11](./img/aws%20codedeploy/11.png)
<br><br><br>
## CodeDeploy Agent 설치
- EC2 인스턴스에 접속 후 설치
- agent 설치를 위해 ruby도 필요하기 때문에 ruby도 설치
    ```shell script
    sudo yum install -y ruby
    wget https://aws-codedeploy-ap-northeast-2.s3.amazonaws.com/latest/install
    chmod +x ./install
    sudo ./install auto
    rm -rf install
    ```
<br><br>
- 제대로 설치 & 서비스가 작동하고 있는지 확인
    ```shell script
    sudo service codedeploy-agent status
    ```
    ![12](./img/aws%20codedeploy/12.png)
<br><br><br>
## CodeDeploy 만들기
- 애플리케이션 생성
- 플랫폼은 `EC2/온프레미스` 선택<br>
![13](./img/aws%20codedeploy/13.png)
<br><br><br>
- 배포그룹생성<br>
![14](./img/aws%20codedeploy/14.png)
<br><br><br>
- 서비스 역할
- 위에서 만든 CodeDeploy용 IAM역할 선택<br>  
![15](./img/aws%20codedeploy/15.png)
<br><br><br>
- 배포 유형
- 현재위치 선택<br>
![16](./img/aws%20codedeploy/16.png)
<br><br><br>
- 환경구성
- Amazon EC2 인스턴스 선택
- EC2 생성 시 설정한 태그로 배포할 인스턴스를 선택<br>
![17](./img/aws%20codedeploy/17.png)
<br><br><br>
- 나머지 설정
- Agent는 여기서는 사용안함으로 선택
- 배포설정은 `AllAtOnce`로 선택
    - CodeDeployDefault.AllAtOnce: 한 번에 가급적 많은 수의 인스턴스 어플리케이션 개정을 배포시도한다. 하나 이상의 인스턴스에 애플리케이션 개정이 배포되면 전체 배포 상태가 성공으로 표시
    - CodeDeployDefault.HalfAtATime: 최대 절반의 인스턴스에 한번에 배포한다. 절반이상이 배포성공하면 전체 배포를 성공으로 표시
    - CodeDeployDefault.OneAtATime: 한 번에 한 인스턴스에만 배포한다. 하나라도 틀리면 배포실패
- 로드밸런서 비활성화로 선택<br>
![18](./img/aws%20codedeploy/18.png)
<br><br><br>
## appspec.yml & 스크립트 추가
- AWS CodeDeploy는 appspec.yml을 통해서 어떤 파일을, 어느 위치에 배포하고, 이후 어떤 스크립트를 실행할지 관리
1. 스프링부트 소스코드에 appspec.yml 및 스크립트 추가
2. AWS CodeBuild에서 사용한 buildspec.yml 수정
    - artifacts.files 부분에 appspec.yml 과 start.sh를 추가(CodeBuild가 패키징을 진행할때 이 리소스도 포함시킴)
3. CodeDeploy가 S3에서 내려받은 zip파일을 unzip한 후 appspec.yml을 참고해 배포진행<br>
![19](./img/aws%20codedeploy/19.png)<br>
<br><br>
- appspec.yml 예시<br>
    ```shell script
    version: 0.0
    os: linux
    # destination에 아티팩트가 unzip된 결과가 생성될 디렉토리명을 넣어준다.
    files:
      - source:  /
        destination: /home/ec2-user/build/
    permissions:
      - object: /
        pattern: "**"
        owner: ec2-user
        group: ec2-user
    
    hooks:
      # 주의 할 점은 빈칸 yml파일 특성상 빈칸 개수를 중시해야하고 Tab을 쓰면 안된다는점
      # AfterInstall은 배포를 완료한 후 실행되는 스크립트를 명시하며
      # ApplicationStart나 ValidateService 등을 대신 쓸 수 있다.
      AfterInstall:
        - location: start.sh
          timeout: 60
          runas: ec2-user
    ```
<br><br>
- start.sh 예시
    ```shell script
    #!/bin/bash
    BUILD_JAR=$(ls /home/ec2-user/build/*.jar)
    JAR_NAME=$(basename $BUILD_JAR)
    PROFILE=$(</home/ec2-user/profile.txt) > 스프링부트에서 profile 옵션 주기위함
    chmod +x /home/ec2-user/build/start.sh > ec2 systemd 설정할때 이 스크립트를 실행하는데 실행권한이 있어야하기 때문 
    chmod +x /home/ec2-user/build/stop.sh > ec2 systemd 설정할때 이 스크립트를 실행하는데 실행권한이 있어야하기 때문
    echo "> build 파일명: $JAR_NAME" >> /home/ec2-user/deploy/deploy.log
    
    echo "> build 파일 복사" >> /home/ec2-user/deploy/deploy.log
    DEPLOY_PATH=/home/ec2-user/deploy/
    cp $BUILD_JAR $DEPLOY_PATH
    
    echo "> 현재 실행중인 애플리케이션 pid 확인" >> /home/ec2-user/deploy/deploy.log
    CURRENT_PID=$(pgrep -f $JAR_NAME)
    
    if [ -z $CURRENT_PID ]
    then
      echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다." >> /home/ec2-user/deploy/deploy.log
    else
      echo "> kill -15 $CURRENT_PID"
      kill -15 $CURRENT_PID
      sleep 5
    fi
    
    DEPLOY_JAR=$DEPLOY_PATH$JAR_NAME
    echo "> DEPLOY_JAR 배포"    >> /home/ec2-user/deploy/deploy.log
    echo "> 현재 active profile $PROFILE " >> /home/ec2-user/deploy/deploy.log
    nohup java -jar -Dspring.profiles.active=$PROFILE $DEPLOY_JAR >> /home/ec2-user/deploy/deploy.log 2>/home/ec2-user/deploy/deploy_err.log &
    ```
<br><br>
- buildspec.yml<br>
![20](./img/aws%20codedeploy/20.png)
<br><br><br>
## CodeDeploy 진행
- CodeDeploy 애플리케이션을 생성하며 설정한 배포그룹을 선택
- 개정유형은 S3선택
- 배포할 zip파일 S3 위치를 입력
- 개정파일형식 `.zip` 선택<br>
![21](./img/aws%20codedeploy/21.png)
<br><br><br>
- 배포성공<br>
![22](./img/aws%20codedeploy/22.png)
<br><br><br>
- 배포확인
- `/home/ec2-user/deploy/deploy.log` 스프링부트 실행로그 확인<br>
![23](./img/aws%20codedeploy/23.png)
- 애플리케이션 요청테스트 확인<br>
![24](./img/aws%20codedeploy/24.png)
<br><br><br>
- [reference](https://lemontia.tistory.com/945)
- [reference](https://twofootdog.tistory.com/38?category=868323) 






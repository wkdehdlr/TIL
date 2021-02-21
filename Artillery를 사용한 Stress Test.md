# Artillery를 사용한 Stress Test
## CPU를 극단적으로 사용하는 애플리케이션 만들기
- [소스](https://github.com/wkdehdlr/artillery)
- port를 `80`으로 변경
<br><br>
## AWS EC2에서 실행하기
- Amazon Linux로 만들면 wget은 이미 설치되어있음
- 간단한 배포이기 때문에 `wget`을 이용해서 미리 올려둔 jar파일을 받아서 직접 실행
    ```shell script
    wget https://github.com/wkdehdlr/artillery/raw/master/jar/demo-0.0.1-SNAPSHOT.jar
    
    sudo java -jar demo-0.0.1-SNAPSHOT.jar
    ```
- 요청확인

    ![1](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211525.png)
<br><br>
## [artillery 설치](https://artillery.io/docs/guides/getting-started/installing-artillery.html#System-requirements)
- nodejs 설치가 선행되어야함
    ```shell script
    sudo npm install -g artillery
    ```
    ![2](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211526.png)
<br><br>
## [artillery 세팅](https://artillery.io/docs/guides/getting-started/core-concepts.html#High-level-view)
- yaml 파일 생성 후 스크립트 복사
![3](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211527.png)
- 스크립트 수정
![4](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211528.png)
## artillery 실행
```shell script
artillery run --output report.json cpu-test.yaml
```
![5](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211529.png)
<br><br>
## 결과파일 html로 변환
- 테스트 결과인 `report.json`파일을 html로 변환
    ```shell script
    artillery report report.json
    ```
    ![6](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211530.png)
<br><br>
## 수치를 변경하면서 테스트 진행
- arrivalRate를 10으로 올림

    ![7](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211549.png)
- 결과

    ![8](./img/Artillery를%20사용한%20Stress%20Test/screenshot202102211550.png)
<br><br>
## 성능고려 포인트
- 예상 TPS보다 여유롭게
- 기대 Latency를 만족할 때까지
- Scale-out을 해도 성능이 늘지 않으면 병목을 의심
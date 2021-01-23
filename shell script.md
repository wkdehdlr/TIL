# shell script

## 변수
- 정의 : 변수명=값
- 주의 : '=' 사이 좌우 공백이 없어야 함
- 대소문자 구별
- 숫자형 변수와 문자형 변수 구분이 없음
- 변수를 사용할 때는 변수명 앞에 '$'를 붙임
<br>
## 함수
- function은 생략가능
- 함수 정의부가 호출부보다 먼저 있어야함
    ```shell script
    apple() {
      echo "apple"
    }
    
    function banana() {
      echo "banana"  
    }
    
    apple
    banana
    ```
  <br>
## 위치 매개 변수
- position parameter

    |문자|설명|
    |---|---|
    |$0|실행된 스크립트 이름|
    |$1|$1, $2, $3...${10}인자 순서대로 번호가 부여된다. 10번째부터는 "{}"감싸야함|
    |$*|전체 인자 값 - 모든 parameter를 하나의 단어로 취급|
    |$@|전체 인자 값 - 모든 parameter를 별도의 문자로 취급(쌍따옴표로 묶으면 한 문자로 인식)|
    |$#|매개 변수의 총 개수|
    <br>

- $* 과 $@ 차이( ex > position.sh )
    ```shell script
    for param in "$*"
    do
            echo $param
    done
    
    echo "==========="
    for param in "$@"
    do
            echo $param
    done
    ```
  <br>
- 결과
    ```shell script
    ./position.sh aa bb cc
    aa bb cc
    ===========
    aa
    bb
    cc
  
    ./position.sh "aa bb" cc
    aa bb cc
    ===========
    aa bb
    cc    
    ```
  <br>
## 특수 매개 변수
- special parameter

    |문자|설명|
    |---|---|
    |$$|현재 스크립트 PID|
    |$?|최근에 실행된 명령어, 함수, 스크립트 자식의 종료 상태|
    |$!|최근에 실행한 백드라운드(비동기)명령의 PID|
    |$-|현재 옵션 플래그|
    |$_|지난 명령의 마지막 인자로 설정된 특수 변수|
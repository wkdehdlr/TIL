# shell script

## 변수
- 정의 : 변수명=값
- 주의 : '=' 사이 좌우 공백이 없어야 함
- 대소문자 구별
<br><br>
## 명령치환
- 명령 결과를 변수에 저장 할 수 있다
- $()를 사용하여 명령의 결과값을 저장
- `소괄호 안에는 반드시 명령어가 들어가야함`
    ```shell script
    #!/bin/bash
    
    VAR=$(ls)
    
    for file in $VAR
    do
            echo $file
    done
    ```
<br><br>
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
<br><br>
## 논리연산자
- 종류

    |문자|설명|
    |---|---|
    |&&, -a|AND|
    |-o|OR|
<br><br>
## 산술연산자
- 종류

    |문자|설명|
    |---|---|
    |+|더하기|
    |-|빼기|
    |*|곱하기|
    |/|나누기|
    |**|거듭제곱|
    |%|modulo|
    |+=||
    |-=||
    |*=||
    |/=||
    |%=||
    <br>
- 산술연산을 `(( )) 이중괄호`로 처리
    ```shell script
    #!/bin/bash
    
    A=2
    B=4
    C=$(( A + B ))
    
    echo $C
    echo $(( A * B ))
    ```
    <br>
## 비트연산자
- 종류

    |문자|설명|
    |---|---|
    |<<||
    |<<=||
    |>>||
    |>>=||
    |&||
    |&=||
    |||
    |||
    |~||
    |!||
    |^||
    |^=||
    <br>
## 정수비교
- 종류

    |문자|설명|
    |---|---|
    |-eq|같음|
    |-ne|같지 않음|
    |-gt, >|크다|
    |-ge, >=|크거나 같음(`>=` 이중소괄호에서 사용가능)|
    |-lt, <|작다|
    |-le, <=|작거나 같음(`<=` 이중소괄호에서 사용가능)|
    <br>
    
    ```shell script
    num1=10
    num2=20
    
    if [[ $num1 -gt $num2 ]]
    # if [[ $num1 > $num2 ]]
    # if (( num1 > num2 ))
    then
            echo $num1
    else
            echo $num2
    fi
    ```
    <br>
## 문자열비교
- 종류

    |문자|설명|
    |---|---|
    |=, ==|같음|
    |!=|같지 않음|
    |<|ASCII 알파벳 순서로 더 작음|
    |\>|ASCII 알파벳 순서로 더 큼|
    |-z|문자열이 NULL, 길이가 0|
    |-n|문자열이 NULL이 아님|
    ```shell script
    s1="A"
    s2="B"
    
    if [[ $s1 > $s2 ]]
    then
            echo $s2
    else
            echo $s1
    fi
  
    if [[ -n $s1 ]]
    then
            echo "not null"
    else
            echo "null"
    fi
  
    if [[ -z $s1 ]]
    then
            echo "null"
    else
            echo "not null"
    fi

    ```
<br><br>
## 와일드카드
- 종류

    |문자|설명|
    |---|---|
    |*|0개 이상의 문자로 대체|
    |?|1개의 문자로 대체|
    |[]|문자 범위 지정하는 특수 문자로 [] 괄호안에 포함된 문자 중 하나를 나타냄|
<br><br>
## 파일비교
- 종류

    |문자|설명|
    |---|---|
    |-d|파일이 존재하며, 파일의 종류가 디렉토리인 경우|
    |-e|파일이 존재(파일 종류 상관 X)|
    |-f|파일이 존재하고, 정규 파일인 경우(디렉토리 혹은 장치파일이 아닌 경우)|
    |-r|파일이 존재하고, 읽을 수 있는 경우|
    |-w|파일이 존재하고, 쓸 수 있는 경우|
    |-x|파일이 존재하고, 실행할 수 있는 경우|
    |-s|파일이 존재하고, 비어 있지 않은 경우|
    |-O|파일이 존재하고, 현재 사용자가 소유한 경우|
    |-G|파일이 존재하고, 기본 그룹이 현재 사용자와 같은 경우|
    |file1 -nt file2|file1이 file2보다 최신인 경우|
    |file1 -ot file2|file1이 file2보다 예전인 경우|
    <br>
- 정규파일이란?
    - a file that is not a directory, symlink, socket, device, etc
<br><br>
## 조건문
- `if`로 시작해서 `fi`로 끝남
- 대괄호 앞 뒤에는 한칸 띄어야함
- 조건문에서 []하나만 쓸 수도 있고, [[]]두개를 쓸 수도 있다. 실행방식이 다르다<br>
`[]`는 별도의 프로세스를 실행. `/usr/bin[` 이 파일을 실행하여 조건식의 결과를 얻음.<br>
`[[]]`는 bash 자체적으로 내장된 기능을 사용하고 별도의 프로세스를 실행하지 않는다. 그리고 2개로 할때는 이스케이프 처리 안해도 되는듯?
    ```shell script
    if [[ condition ]]
    then
        command
    fi
    ```
- 실행문장이 없으면 오류발생
    ```shell script
    s1="apple"
    s2="banana"
    num1=10
    num2=20
  
    if [[ $num1 -gt $num2 ]]
    # if [[ $num1 > $num2 ]]
    # if (( num1 > num2 ))
    then
            echo $num1
    else
            echo $num2
    fi
    
    if [[ $s1 == $s2 ]]
    then
            # 실행문장이 없으면 오류
            echo "s1 = s2"
    else
            echo "s1 != s2"
    fi
    
    # AND
    if [[ $s1 == $s2 ]] && [[ $s3 == $s4 ]]
    ..생략
    
    # OR
    if [[ $s1 == $s2 ]] || [[ $s3 == $s4 ]]
    ..생략
    
    # 다중 조건
    if [[ $s1 == $s2 || $s3 == $s4 ]] && [[ $s5 == $s6 ]]
    ..생략
    ```
<br><br>
## 이중괄호
- `(( expression ))`
- expression에는 `수식`이나 `비교 표현식`이 들어갈 수 있다
- 스크립트 내에서 `산술연산처리`를 위해 사용
<br><br>
## 반복문
- break
- continue
    ```shell script
    for string in "hello" "world" "..."
    do
        echo $string
    done
    
    # 수행 조건이 true 일때 실행됨
    count=0
    while [[ $count -le 5 ]]
    # while (( count <= 5 ))
    do
        echo $count
        count=$(( count + 1 ))
    done
    
    # 수행 조건이 false 일때 실행됨
    count=10
    until [[ $count -le 5 ]]
    # until (( count <= 5 ))
    do
        echo $count
        count=$(( count - 1 ))
    done
    ```
<br><br>
## 선택문
- 정규식을 지원
- `|`기호로 다중 값 조건가능
    ```shell script
        string="hello"
        
        # case문 시작
        case $string in
            hello|HELLO)
                echo "hello 일 때"
                ;;
            wo*)
                echo "wo로 시작하는 단어 일 때"
                ;;
            s|start)
                echo "s 혹은 start 일 때"
                ;;
            e|end)
                echo "e 혹은 end 일 때"
                ;;
            *)
                echo "기타"
                ;;
        esac
        # //case문 끝
    ```
<br><br>
## 위치 매개 변수
- 종류

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
<br><br>
## 특수 매개 변수
- 종류

    |문자|설명|
    |---|---|
    |$$|현재 스크립트 PID|
    |$?|최근에 실행된 명령어, 함수, 스크립트 자식의 종료 상태|
    |$!|최근에 실행한 백드라운드(비동기)명령의 PID|
    |$-|현재 옵션 플래그|
    |$_|지난 명령의 마지막 인자로 설정된 특수 변수|
<br><br>
## 매개 변수 확장
- `$변수`와 동일하지만 `{}`를 사용해야만 동작

    |문자|설명|
    |---|---|
    |${변수:위치}|위치 다음부터 문자열 추출 (`echo ${string:4}`)|
    |${변수:위치:길이}|위치 다음부터 지정한 길이 만큼의 문자열 추출(`echo ${string:4:3}`)|
    |${변수:-단어}|변수 미선언시만 기본값 지정, 위치 매개 변수는 사용 불가(`echo ${string-HELLO}`)|
    |${변수:=단어}|변수 미선언 혹은 NULL일 때 기본값 지정, 위치 매개 변수 사용 가능(`echo ${string:=HELLO}`)|
    |${변수=단어}|변수 미선언시만 기본값 지정, 위치 매개 변수 사용 가능(`echo ${string=HELLO}`)|
    |${변수:?단어}|변수 미선언 혹은 NULL일 때 단어 출력 후 스크립트 종료(`echo ${string:?HELLO}`)|
    |${변수?단어}|변수 미선언시만 단어 출력 후 스크립트 종료(`echo ${string?HELLO}`)|
    |${변수:+단어}|변수 선언시만 단어 사용(`echo ${string:+HELLO}`)|
    |${변수+단어}|변수 선언 혹은 NULL일 때 단어 사용(`echo ${string+HELLO`)|
    |${#변수}|문자열 길이(`echo ${#string}`)|
<br><br>
## 레퍼런스
- [reference](https://blog.gaerae.com/2015/01/bash-hello-world.html?m=1)
- [reference](https://jake-factory.tistory.com/35?category=849783)
- [reference](https://jake-factory.tistory.com/35?category=849783)
- [reference](https://linuxism.ustd.ip.or.kr/57)
- [reference](https://m.blog.naver.com/PostView.nhn?blogId=adh3305&logNo=221090115150&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [reference](http://bahndal.egloos.com/531206)
- [reference](http://blog.daum.net/servant2342/8382771)
- [reference](https://coding-chobo.tistory.com/54)
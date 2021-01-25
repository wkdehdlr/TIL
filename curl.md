# curl
- 주요 옵션

    |short|long|설명|비고|
    |---|---|---|----|
    |-X|--request|HTTP 메서드를 설정|GET, POST, PUT, DELETE|
    |-H|--header|헤더설정, 여러개 설정가능|-H "Content-Type:application/json"<br><br>-H "Content-Type:application/json" -H "Accept:text/plain"|
    |-d|--data|데이터 전달|[GET] -d "key1=value1&key2=value2" -H "Content-Type:application/x-www-form-urlencoded"<br><br>[POST] -d "key1=value1&key2=value2" -H "Content-Type:application/x-www-form-urlencoded"<br><br>[POST] -d '{"key1":"value1","key2":"value2"}' -H "Content-Type: application/json" |
    |-o|--output|기본적으로 응답을 콘솔에 출력하지만<br> `-o <file>` 지정하면 해당파일에 저장|[응답버리기] -o /dev/null|
    |-w|--write-format|`-w %{format}` 응답에서 [포맷](https://linux.die.net/man/1/curl)에 맞는 데이터를 출력|[응답코드 가져오기] -w %{http_code}|
    |-s|--silent|정숙모드, 진행내역이나 에러메세지 노출X|curl ww.google.co<br>curl -s ww.google.co|

    - 응답코드만 가져오기
        ```shell script
        curl -o /dev/null -s -w %{http_code} www.google.com
        ```

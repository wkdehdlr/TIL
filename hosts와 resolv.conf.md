# /etc/hosts와 /etc/resolv.conf
- [reference](https://itsyouuu.tistory.com/19)
- [reference](https://blueyikim.tistory.com/2010)
- [reference](http://blog.naver.com/PostView.nhn?blogId=minki0127&logNo=220820508210&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)
- DNS보다가 위 내용이 언급되어서 정리
- /etc/hosts
    - IP와 hostname을 매핑하는 역할
- /etc/host.conf
    - 도메인 요청시 검색순서를 정함
    - `맥에서는 다른 파일인듯함...위 파일이 없음`
    - hosts : /etc/hosts에서 검색
    - bind : DNS서버에서 검색
    - ex> order hosts, bind
        - IP Request > /etc/hosts > /etc/resolv.conf(DNS)
        - 로컬시스템에서 도메인 검색 질의를 할 경우 /etc/hosts에 매핑정보 있는지 확인하고 없으면 /etc/resolv.conf에 등록된 네임서버에 도메인검색을 질의해서 IP주소로 변환됨
- /etc/resolv.conf
    - 네임서버를 지정할 수 있음


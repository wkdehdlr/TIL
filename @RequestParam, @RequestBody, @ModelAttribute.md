# @RequestParam, @RequestBody, @ModelAttribute
- [reference](https://mangkyu.tistory.com/72)
- [reference](http://wonwoo.ml/index.php/post/1834)
- @RequestBody
    - Http 요청의 Body내용을 Java Object로 변환
    - Body가 존재하지 않는 GetMapping에 @RequestBody는 에러발생
    - Json이나 XML을 Jackson과 같은 `MessageConverter(ObjectMapper)`를 사용하여 변환
    - setter가 없어도 값 매핑
    - 생략하면 RequestBody를 모델로 간주해 ModelAttribute로 변환하려해서 빈 모델이 담겨온다 `반드시 작성!!` 
<br><br>
- @ModelAttribute
    - 클라이언트가 전송하는 여러 파라미터들을 1대1로 객체에 바인딩
    - 여러 개의 파라미터를 바로 자바빈 객체로 매핑
    - setter가 없으면 값 매핑이 안됨
    - 생략가능(하지만 작성을 권장)
    ```java
    @GetMapping("/members")
    public MemberDTO getMember(String id, MemberRequest request) {
        //String id, MemberRequest request 둘 다 어노테이션을 생략하는데
        //스프링은 각각 RequestParam, ModelAttritube를 어떻게 판단할까?
    }
    ```
    - RequestParam, ModelAttribute 둘 다 생략하면<br>
      primitive type은 RequestParam으로 인식하고<br>
      reference type은 ModelAttribute로 인식
<br><br>
- @RequestBody & @ModelAttribute 비교
    - @RequestBody는 데이터 변환이고 @ModelAttribute는 값 매핑
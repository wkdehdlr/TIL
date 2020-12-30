# Mock
- [reference](https://cobbybb.tistory.com/16)
- @Mock, @MockBean 어노테이션을 사용해서 주입받은객체에 대한 행위를 전제하고 진행한다.(행위를 강제시킨다)
- Mock
    - 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 행동을 관리하는 객체
    - DB, 외부 API를 호출할 때 해당 기능을 항상 테스트에 포함할 수 없기 때문에 미리 행동을 예측
- Mockito
    - Mock 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법을 제공
<br><br>

# Mock 객체 Stubbing
- 모든 Mock 객체의 행동
    - return이 있는 메소드는 null을 리턴(Optional 타입은 Optional.empty() 리턴)
    - primitive 타입은 기본 primitive 값
    - 콜렉션은 비어있는 콜렉션
<br><br>

# Mockito BDD 스타일 API
- when -> given
- verify -> then
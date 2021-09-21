# Mockito 기초 사용법
> [테스트 주도 개발 시작하기](http://www.yes24.com/Product/Goods/89145195) 부록 정리
## 모의 객체 생성
```java
public interface GameNumGen {
    String generate(GameLevel level);
}
```
```java
import static org.mockito.Mockito.mock;

public class GameGenTest {
    @Test
    void mockTest() {
        GameNumGen genMock = mock(GameNumGen.class);
    }
}
```
## 스텁 설정
> BDDMockito 클래스를 이용해서 모의 객체에 스텁을 구성할 수 있다
```java
public class GameGenMockTest {
    @Test
    void mockTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        BDDMockito.given(genMock.generate(GameLevel.EASY)).willReturn("123");
        
        String num = genMock.generate(GameLevel.EASY);
        assertEquals("123", num);
    }
    
    @Test
    void mockThrowTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        BDDMockito.given(genMock.generate(null)).willThrow(IllegalArgumentException.class);
        
        assertThrows(
            IllegalArgumentException.class,
            () -> genMock.generate(null)
        );
    }
    
    // 리턴 타입이 void인 메소드에 대해 익셉션 발생테스트
    @Test
    void voidMethodWillThrowTest() {
        List<String> mockList = mock(List.class);
        willThrow(UnsupportedOperationException.class)
            .given(mockList)
            .clear();
        
        assertThrows(
            UnsupportedOperationException.class,
            () -> mockList.clear()
        );
    }
}
```
## 인자 매칭 처리
> 정확하게 일치하는 값 대신 임의의 값에 일치하도록 설정
```java
public class AnyMatcherTest {
    @Test
    void anyMatchTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        BDDMockito.given(genMock.generate(any())).willReturn("123");

        String num1 = genMock.generate(GameLevel.EASY);
        assertEquals("123", num1);
        
        String num2 = genMock.generate(GameLevel.NORMAL);
        assertEquals("123", num2);
    }
}
```
|메소드|설명|
|---|---|
|anyInt(), anyShort(), anyLong(), anyByte(), anyChar()<br> anyDouble(), anyFloat(), anyBoolean()|기본 데이터 타입에 대한 임의 값 일치|
|anyString()|문자열에 대한 임의 값 일치|
|any()|임의 타입에 대한 일치|
|anyList(), anySet(), anyMap(), anyCollection()|임의의 콜렉션에 대한 일치|
|matches(String), matches(Pattern)|정규표현식을 이용한 String 값 일치 여부|
|eq(값)|특정 값과 일치 여부|
### 주의
> 인자가 두 개 이상인 경우
```java
// 기존
List<String> mockLIst = mock(List.class);
given(mockList.set(anyInt(), "123")).willReturn("456");
String old = mockList.set(5, "123");
```
- 위 코드는 익셉션 발생
- Mockito는 한 인자라도 ArgumentMatcher를 사용해서 설정한 경우 모든 인자를 ArgumentMatcher를 이용해서 설정
```java
// 수정
List<String> mockLIst = mock(List.class);
given(mockList.set(anyInt(), eq("123"))).willReturn("456");
String old = mockList.set(5, "123");
```
- ArgumentMatchers.eq() 사용
## 행위 검증
> 모의 객체 역할 중 하나는 실제로 모의 객체가 불렸는지 검증하는 것
```java
public class GameTest {
    
    @Test
    void init() {
        GameNumGen genMock = mock(GameNumGen.class);
        Game game = new Game(genMock);
        game.init(GameLevel.EASY);
        
        BDDMockito.then(genMock).should().generate(GameLevel.EASY);
        BDDMockito.then(genMock).should().generate(any());
        BDDMockito.then(genMock).should(only()).generate(any());
    }
}
```
|메소드|설명|
|---|---|
|only()|한 번만 호출|
|times(int)|지정한 횟수만큼 호출|
|never()|호출하지 않음|
|atLeast(int)|적어도 지정한 횟수만큼 호출|
|atLeastOnce()|atLeast(1)과 동일|
|atMost(int)|최대 지정한 횟수만큼 호출|
## 인자 캡처
> 모의 객체를 호출할 때 사용한 인자를 검증해야 할 때
```java
public class UserRegisterMockTest {
    private UserRegister userRegister;
    private EmailNotifier mockEmailNotifier = mock(EmailNotifier.class);
    
    @Test
    void whenRegisterThenSendMail() {
        userRegister.register("id", "pw", "email@email.com");
        
        ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
        BDDMockito.then(mockEmailNotifier)
                    .should().sendRegisterEmail(captor.capture());
        
        String realEmail = captor.getValue();
        assertEquals("email@email.com", realEmail);
    }
}
```
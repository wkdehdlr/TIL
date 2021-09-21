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
|타입|설명|
|---|---|
|anyInt(), anyShort(), anyLong(), anyByte(), anyChar()<br> anyDouble(), anyFloat(), anyBoolean()|기본 데이터 타입에 대한 임의 값 일치|
|anyString()|문자열에 대한 임의 값 일치|
|any()|임의 타입에 대한 일치|
|anyList(), anySet(), anyMap(), anyCollection()|임의의 콜렉션에 대한 일치|
|matches(String), matches(Pattern)|정규표현식을 이용한 String 값 일치 여부|
|eq(값)|특정 값과 일치 여부|

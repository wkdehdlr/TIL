# Scanner Mocking 하기
> NEXTSTEP 과제 중 Scanner를 Mocking했다<br>
> 실무에서 Scanner를 쓸일이 없어서 거의 사용할 일은 없을 듯 하다.

## dependency
- testCompile('org.mockito:mockito-inline:2.13.0')
    - [mockito-inline](https://github.com/wkdehdlr/TIL/blob/main/Mockito%20cannot%20mock%2Cspy%20final%20class%20%EC%97%90%EB%9F%AC%20%ED%95%B4%EA%B2%B0%20%EB%B0%A9%EB%B2%95.md)
<br><br>
## 코드
- InputClass
    ```java
    public class Input {
        
        private final Scanner scanner;
    
        public Input() {
            this(new Scanner(System.in));
        }
        
        public Input(Scanner scanner) {
            this.scanner = scanner;
        }
        
        public String getInput() {
            return scanner.nextLine();
        }
    }
    ```
- Test
    ```java
    public class Test {
    
        private final Scanner mockScanner = mock(Scanner.class);
        private final Input input = new Input(mockScanner);
        
        @Test
        void input() {
            when(mockScanner.nextLine()).thenReturn("hello java");
            
            assertEquals("hello java", input.getInput());
            verify(mockScanner).nextLine();
        }
    }
    ```
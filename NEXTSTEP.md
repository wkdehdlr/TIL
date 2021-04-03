# NEXTSTEP
> TDD, Clean Code with Java를 수강하며 정리한 글

## 엘레강트 오브젝트 원칙 적용하기
- 인자의 값으로 null을 절대 허용하지 마라
    ```java
    public String find(String name) {
        if(name == null){
            //do something        
        } else{
            //do something
        }
    }
    ```
    - 조건의 존재 여부를 객체 스스로 결정하게 했을 것이다.
    - 객체지향 생활 체조 원칙의 `원시 값과 문자열을 포장한다`. 원칙을 적용하는 것이 null을 허용하지 않는 방법 중의 하나가 될 수 있다.
- er로 끝나는 이름을 사용하지 마라
- 메소드이름은 빌더와 조정자로 분리
    - 빌더는 명사, 반환타입이 있음(명사를 꾸미는 형용사까지 허용)
    - 조정자는 동사, 반환타입이 없음(동사를 꾸미는 부사까지 허용)
    - boolean값은 예외적으로 빌더이지만 형용사로 짓는걸 지향
- 생성자 하나를 주 생성자로 만들어라
    ```java
    public Car(String name, int position) {
        this(new Name(name), new Position(positon));
    }
    
    public Car(Name name, Position position){ <- 주 생성자
        this.name = name;
        this.position = position;
    }
    ```    
- SRP & 캡슐화
    - 객체를 존중하라
        - 객체에 위임해라
    - 메소드가 모든 필드에 접근할수록 좋은 메소드
<br><br>
## 자동차 경주
- 테스트 데이터는 경계값을 사용해라
- 오버엔지니어링을 유의해라
    - 첫 설계부터 과한 설계를 지향하기 보단 유지보수 시 반복되는 작업을 인터페이스로 추출하는게 좋다
- DTO가 아닌 도메인객체는 가급적 getter를 쓰지않게 연습해라(setter는 당연히 쓰지말고) 
- primitive값 자체를 비교하기 보다 클래스로 감싸서 equals를 통한 비교로 수정해라
    ```java
    public class Position {
        private int position;
        .
        .
        .
        public int getPosition() {
            return this.position;
        }
          
        @Override
        public boolean equals(Object o) {
              .
              .
              .
        }   
    }
    
    public class PositionTest {
    
        @Test
        void create() {
            Position position = new Position(5);
            //assertEqauls(5, position.getPosition());
            assertEquals(new Position(5), position);
        }
    }
    ```
- 모든 원시값과 문자열을 포장
    - SRP에 따라 책임을 적절한 객체에 위임할 수 있음
- 일급컬렉션을 사용하라
- 테스트를 위한 메소드추가는 `지양`해라
<br><br>
## 로또
- 생성자에 코드를 넣지 마라
- 불변객체를 만들어라
    - 클래스를 상태 변경이 불가능한 불변 클래스(immutable class)로 구현하면 유지보수성을 향상시킬 수 있다.
    - 불변 객체를 기반으로 사고하면 더 깔끔하고, 더 작고, 더 쉽게 이해할 수 있는 코드를 구현할 수 있다.
# NEXTSTEP
> TDD, Clean Code with Java를 수강하며 정리한 글

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
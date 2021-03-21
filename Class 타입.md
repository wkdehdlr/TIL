# Class 타입
> 자바 리플렉션을 보면서 알아본 Class 타입에 대해 정리

### 리플렉션이 어떻게 가능할까?
```java
class ContainerTest {
    MemberRepository memberRepository = Container.getBean(MemberRepository.class);
}

class Container {
    public static <T> T getBean(Class<T> classType) {
        //do someThing
    }
}
```
- [IoC 만들어보기 코드](https://github.com/wkdehdlr/My-Spring-Container) 중 일부이다.
    - getBean 메소드 파라미터를 보면 Class<T> 타입 인스턴스를 받고 있는데<br>
    실제로 호출하는 부분에서는 MemberRepository.class를 넘긴다.<br>
    즉, MemberRespository.class가 곧 Class 타입 인스턴스라는 것인데<br>
    코드 어디에도 MemberRepository.class 인스턴스를 생성하는 부분이 없다.<br>
    (MemberRepository memberRepository = new MemberRepository(); 이건 MemberRepository 인스턴스를 생성하는것이지 MemberRepository.class 인스턴스를 생성하는게 아니다.)
<br><br>
```java
Class.java 일부

 * <p> {@code Class} has no public constructor. Instead a {@code Class}
 * object is constructed automatically by the Java Virtual Machine
 * when a class loader invokes one of the
 * {@link ClassLoader#defineClass(String,byte[], int,int) defineClass} methods
 * and passes the bytes of a {@code class} file.
```
- 우선 JVM 메모리구조를 이해할 필요가 있다.
    - .java파일 -> .class파일 -> 클래스로더가 JVM에 로딩
    - .class 파일은 클래스로더에 의해 JVM 클래스영역(메소드영역)에 올라가는데 이게 클래스로딩이다.
    - 이 과정에서 JVM에 클래스로딩이 끝나면 자동으로 Class 타입 인스턴스를 생성한다.<br>
    이 Class 타입 인스턴스도 말그대로 인스턴스(객체)이기 떄문에 heap에 저장된다.<br>
    다만, heap은 GC의 대상인데 GC에 의해 수거되는것은 아닐까?<br>
    이 Class 인스턴스는 heap중에서도 metaspace라는 곳에 저장되며 특별히 관리된다.
    - 따라서 클래스정보를 담고있는 Class 인스턴스가 만들어지고 heap에 저장되어 있기 때문에 heap에서 꺼내와서 일련의 처리를 할 수 있는것이다. 즉, 리플렉션이 가능한것!!
<br><br>
```java
public class Hello {  
	public static void main(String[] args) {  
		Class c1 = Class.forName(args[0]); 
	}  
} 
```
- 이와 관련해서 자바 런타임로드에 대해서 좀 헷갈렸던 부분이 있었다.
- 실행시 인자로 받은 클래스를 런타임로드하는 코드인데<br>
해당 클래스가 아직 로딩된적이 없기 때문에 heap에 Class 인스턴스가 없는데 어떻게 인자로받은 클래스에 해당하는 Class 인스턴스를 받아올 수 있을까?라고 생각했다.
- 사실 위 내용에 대해선 틀린내용은 없다.<br>
다만, 로딩이 안되어있기 때문에 인자로 받은 클래스를 클래스로더가 클래스패스에서 찾아서 로딩한다.<br>
클래스 로딩이 발생하고 Class 인스턴스가 만들어지는 과정이 미리 발생하는것이다.<br>
이 후 생성된 Class 인스턴스를 가져오기 때문에 런타임로드가 가능한것.

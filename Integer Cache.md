# Integer Cache
```java
Integer a1 = 3;
Integer a2 = 3;

System.out.println(a1 == a2);
>> true

Integer a3 = 300;
Integer a4 = 300;

System.out.println(a3 == a4);
>> false
```

```java
Integer a1 = 3;
```
- 위 코드를 실행하면 내부적으로 `Integer.valueOf()`를 실행한다
```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
- Integer는 -127 ~ 128 범위일 때 캐시를 저장한다.<br>
따라서, a1과 a2는 범위안에 들어오기 때문에 캐시를 사용하여 == 결과 true<br>
a3과 a4는 범위밖이기 때문에 새로 인스턴스를 생성해서 == 결과 false
```java
-XX:AutoBoxCacheMax=size
```
- 128 이상의 값을 저장하기 위해서는 `VM option`을 사용
- [reference](https://programming.guide/java/boxed-values-equality.html)
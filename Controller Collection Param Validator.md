# 컨트롤러에서 Collection 파라미터 검증
- 컨트롤러에서 @Valid 어노테이션으로 파라미터에 대한 검증을 진행하는데 파라미터가 Collection일 때 원하는대로 Valid가 동작하지 않았다.
```java
@PostMapping
public CommonResponse addList(@RequestParam @Valid AddListRequest request) {
    .
    .
    .
}

@Data
@NoArgsConstructor
@AllArgsConstructor
public AddListRequest {
    @NotNull
    private List<Item> itemList;
}

@Data
@NoArgsConstructor
@AllArgsConstructor
public Item {
    @NotNull
    private String name;
    private String description;
    private Long price;
}
```
- addList메소드의 AddListRequest파라미터에 붙어있는 @Valid는 AddListRequest파라미터 자체에 대한 검증을 진행한다.
- itemList 원소하나하나에 대한 검증이 아니라 itemList자체에 대한 @NotNull 검증만 진행한다.
```json
{
  "itemList": [
      {
        "description": "설명",
        "price": 12000
      } 
  ] 
}
```
- 클라이언트가 위 json으로 요청을 보내면(name이 없이 보내도) Item name필드가 @NotNull임에도 성공한다.
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public AddListRequest {
    @Valid
    @NotNull
    @Size(min = 1)
    private List<Item> itemList;
}
```
- AddListRequest에서 itemList에도 @Valid를 달아주면 원하는대로 itemList 각 원소들에 대해서도 검증을 진행한다.
- 빈 리스트는 @Size로 검증할 수 있다.
```java
@PostMapping
public CommonResponse addList(@RequestParam @Valid List<Item> itemList) {
    .
    .
    .
}
``` 
- 만약 컨트롤러 메소드에서 Collection을 바로 받는경우에는 [다른 방법](https://gompangs.tistory.com/entry/Spring-Valid-Collection-Validation-%EA%B4%80%EB%A0%A8)으로 처리할 수 있다.
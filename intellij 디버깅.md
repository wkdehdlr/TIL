# intellij 디버깅
## 이전단계로 돌아가기
> Drop Frame으로 콜스택 지우기
- 디버깅을 하다보면 이전단계로 돌아가고 싶을때가 있다. 재실행없이 이전 콜스택으로 돌아가는 방법이 있다.
- 디버깅중에 `Drop Frame`을 사용한다.

![2](./img/intellij%20디버깅/2.png)
![3](./img/intellij%20디버깅/3.png)
- MyService 14라인에서 breakPoint가 걸리고 현재 콜스택확인<br>
   - MyService getApi()
   - MyController getApi()
![4](./img/intellij%20디버깅/4.png)
- `Drop Frame`으로 돌아가기<br>
![5](./img/intellij%20디버깅/5.png)
- MyController로 돌아옴
![6](./img/intellij%20디버깅/6.png)
- MyService getApi 콜스택이 지워진것을 확인
![7](./img/intellij%20디버깅/7.png)
- 메소드 호출간 돌아갈 수 있다. 한 메소드 안에서는 이동할 수 없다.(같은 콜스택은 불가)
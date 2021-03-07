# Mockito cannot mock/spy final class 에러 해결 방법
- 해결
- `core`대신 `inlne`사용
```yaml
    testImplementation 'org.mockito:mockito-inline:2.13.0'
    //    testImplementation 'org.mockito:mockito-core:2.28.2'
```
- [reference](https://codechacha.com/ko/android-mockito-cannot-mock-final-error/
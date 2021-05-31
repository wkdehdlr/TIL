# mac alias
- .bash_profile 편집
    ```shell script
    vi ~/.bash_profile
    ```
- alias 설정
    ```shell script
    alias ll='ls -l'
    ```
- 적용
    ```shell script
    source ~/.bash_profile
    ```
- 추가
    - 위 변경 사항은 쉘이 재부팅 될 때마다 초기화 되기 때문에 source 명령을 매번 사용해야한다.
    - MacOS에서 터미널이 실행될 때마다 `.zshrc`파일도 함께 실행되기 때문에<br>
    `~/.zshrc`파일에 `source ~/.bash_profile` 을 추가
- [참고](https://yong-park.tistory.com/3)
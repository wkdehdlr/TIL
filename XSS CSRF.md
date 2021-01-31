# XSS CSRF
- 서버 access token, refresh token 발급
- access token은 json으로 넘김, refresh token은 httponly cookie
- 프론트에서 access token을 받아서 vuex에 저장
- 매 api 호출마다 header에 vuex에 저장된 access token 실어서 보냄
- TODO

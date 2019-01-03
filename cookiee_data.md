# 쿠키 데이터 저장/불러오기
구현에 앞서 로그인이 필요함으로 [쿠키 로그인 API](https://github.com/Waterticket/cookiee_API/blob/master/cookiee_login.md) 를 보고오길 바란다

## 데이터 저장
저장 자체는 간단하다. 먼저 위에서 토큰을 발급받아오고 로그인 기능을 구현했다는 가정하에 진행된다<br/>

~~~
전송 링크
https://www.cookiee.net/tools/game_data_sav

패킷 형식
POST

보내야할 패킷
token = 발급받은 토큰
srl = 로그인후 받은 user_srl
ds = 데이터 값 (base64 encode후 전송)
hs = 해쉬 값 (아래 설명)

리턴값
양수 : 전송 성공
음수 : 저장 오류
~~~

# 쿠키 데이터 저장/불러오기
구현에 앞서 로그인이 필요함으로 [쿠키 로그인 API](https://github.com/Waterticket/cookiee_API/blob/master/cookiee_login.md) 를 보고오길 바란다

## 데이터 저장
저장 자체는 간단하다. 먼저 위에서 토큰을 발급받아오고 로그인 기능을 구현했다는 가정하에 진행된다 <br/>
데이터 값은 json 또는 xml을 추천한다 <br/>
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
ct = 데이터 값의 구조 (json, xml)

리턴값
양수 : 전송 성공
음수 : 저장 오류
~~~
<br>
여기서 해쉬 값이란 데이터의 유효성를 증명하기 위해 사용된다 <br/>
해쉬 값 = base64_encode 전의 값 + 토큰의 Client_secret값 <br/>

### 데이터 저장 확인
개발자 센터 -> 쿠키 게임 관리 시스템 -> 게임 옆 리스트 버튼 클릭 -> base64_decode된 값이 나온다 <br/>
데이터의 형식이 json이나 xml 일경우, 자동 정렬해서 보기 쉽게 출력해준다 <br/>
이는 데이터를 저장할때 사용한 ct값으로 정렬된다 <br/>

## 데이터 불러오기

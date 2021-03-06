# 쿠키 스코어보드
로그인을 구현하기 앞서 [쿠키 스코어보드 관리 센터](https://www.cookiee.net/gmscore) 에서 토큰을 발급 받아오길 바란다.<br>

스코어보드는 [쿠키 로그인](/cookiee_login.md) 기능을 이용하면 더욱 확장성을 넓힐수 있다.<br>
되도록이면 이용하는것을 추천한다.

먼저 스코어보드의 용도들에 따라 나누어진다
## 불러오기
불러오기의 방식에는 2가지가 가능하다.
1. 웹 브라우저를 이용하여 스코어를 띄우기
2. JSON을 이용하여 화면에 띄우기
<br>

#### 웹 브라우저

웹브라우저를 이용할 경우 구현은 쉬우나 사용자 기준으로는 불편하게 느낄수 있다.<br>
불러오는 랭킹수는 50개이다

	오픈해야할 링크
	https://www.cookiee.net/score/view/ + 게임 명
	
	패킷 형식
	GET
	
	보내야할 패킷
	게임명 (다른거 없이 게임명만 입력하시오)

ex) 게임명 : Jump!<br><b>
> [https://www.cookiee.net/score/view/jump!](https://www.cookiee.net/score/view/jump!)</b>

![웹스코어보드 예시](/img/score_board_1.PNG)

##### 리턴 값
	Void
<br>

#### JSON 이용 불러오기

JSON을 이용하면 위치와 디자인을 자신의 마음대로 변경할수 있기 때문에 추천한다.<br>불러올수 있는 랭킹은 상위 50개 까지 가능하다.

	체크 링크
    https://www.cookiee.net/score/view_json/ + 게임 토큰
    
    패킷 형식
    GET (POST 지원 예정)
    
    보내야할 패킷
    게임 토큰 (다른거 없이 게임 토큰만 입력하시오)
    count=불러올 랭킹 양 (선택)(최대 50개)

##### 리턴 값

	JSON 형식 리턴
    {"list":
    	[ {
        	"rank":랭크, 
        	"user_srl":유저 srl, 
        	"name":"이름", 
        	"score":스코어, 
        	"time":YYYY-MM-DD hh:mm:ss
        }, {...} ], 
     "count":불러온 총 랭킹 수}
    
이를 이용하면 자신이 원하는만큼 랭킹을 불러올수 있다.<br>

ex) 토큰 : cookie_yszkk74n9d00xn, 불러올 랭킹수 = 7개
>https://www.cookiee.net/score/view_json/<b>cookie_yszkk74n9d00xn</b>&count=<b>7</b>

## 저장하기
스코어 보드 저장은 간편하게 저장할수 있다.

	전송 링크
    https://www.cookiee.net/score/scoreHandlerV2
    
    패킷 형식
    POST
    
    보내야할 패킷
    token= 게임 토큰
    name= 유저 닉네임 (띄어쓰기 허용)
    score= 스코어 (소수점 허용)
    hash= 해시 (아래 설명)
    nchk= 스코어 업데이트 여부 (0/1) (아래 설명)
    member_srl= user_srl(선택) (쿠키 로그인 기능에서 user_srl을 넣으면 됨)


스코어 저장할때에는 필요한 데이터가 많다.<br>
먼저 name같이 한글/특수문자가 들어갈수 있는 문자열은 url encode를 거치고서 등록하는것을 추천한다.

hash는 변조/위조를 검증할때 쓰이게 된다.<br>
(name+score+스코어보드 시크릿 키) 를 sha1화 한것을 hash에 넣으면 된다. (전부 소문자로 변경해야 함)

스코어 업데이트 여부는 같은 유저가 플레이 했을때, 스코어를 새로 등록할지 아니면 원래 있던 랭킹에 점수만 바꿀지 결정하는 것이다.
member_srl을 넣었을때만 사용이 가능하다.

##### 리턴 값
	JSON 형식 리턴
    
    {
        "status": 상태 , 
        "rank":등록한 스코어의 랭킹
    }
    
    상태 값:
    -1 : 스코어, 이름, 해시가 변조되었음 (해시 불일치)
    -2 : 토큰이 유효하지 않음
    -3 : 새 점수가 이전 점수보다 작음 (스코어 업데이트 사용시)
    -4 : 토큰, 이름 또는 스코어가 입력이 되지않음.
    
    1 : 성공적 등록
    2 : 스코어 업데이트 성공 (스코어 업데이트 사용시)
    
    랭킹:
    int 형으로 제공됨
이것으로 스코어보드 API 설명을 마친다.

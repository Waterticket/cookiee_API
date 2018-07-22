# 쿠키 로그인
로그인을 구현하기 앞서 [쿠키 개발자 센터](https://www.cookiee.net/gmdata, "쿠키 개발자 센터로 이동") 에서 토큰을 발급 받아오길 바란다.
## 일반 로그인
일반 로그인은 아이디, 비밀번호로만 이루어져 있기에 구현이 간단하다.\
그러나 많은 이들이 소셜로그인으로 로그인 할경우가 많기에, 범용성 면에서는 떨어진다는것을 염두하여야 한다. 

먼저 아이디와 비밀번호를  각각 id와 pw라는 변수에 받았다고 가정하자.\
일반 로그인은 비밀번호 노출 가능성이 있기에 https, POST 통신밖에 받지 않는다.

	전송 링크
    https://cookiee.net/tools/login_normal
    
    패킷 형식
    POST 
    
    보내야할 패킷
    uid=(유저 아이디)
    ups=(유저 비밀번호)
    
### 리턴 값
	-1 : 아이디 또는 비밀번호가 입력되지 않음
    -2 : Request Method가 POST가 아님
	-3 : 아이디 또는 비밀번호가 일치하지 않음
    -4 : 통신 방식이 https가 아님
    
    이외의 음수 : 알수없는 오류
    이외의 자연수 : User SRL (성공)

이로서 일반로그인은 끝났다.

### 예제
예시는 유니티로 들겠다

	* WWW 클래스 *
	
	WWWForm form = new WWWForm();
    form.AddField("uid", 아이디 String);
    form.AddField("ups", 비밀번호 String);
    WWW www = new WWW("https://cookiee.net/tools/login_normal", form);
    yield return www;
    int result = Convert.ToInt32(www.text);

## 소셜 로그인

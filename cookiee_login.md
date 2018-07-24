# 쿠키 로그인
로그인을 구현하기 앞서 [쿠키 개발자 센터](https://www.cookiee.net/gmdata) 에서 토큰을 발급 받아오길 바란다.<br />

## 일반 로그인
일반 로그인은 아이디, 비밀번호로만 이루어져 있기에 구현이 간단하다.<br />
그러나 많은 이들이 소셜 로그인으로 로그인 할경우가 많기에, 범용성 면에서는 떨어진다는것을 염두하여야 한다. 

먼저 아이디와 비밀번호를  각각 id와 pw라는 변수에 받았다고 가정하자.<br />
일반 로그인은 비밀번호 노출 가능성이 있기에 https, POST 통신밖에 받지 않는다.

	전송 링크
    https://cookiee.net/tools/login_normal
    
    패킷 형식
    POST 
    
    보내야할 패킷
    uid=(유저 아이디)
    ups=(유저 비밀번호)
    tkn=(게임 토큰)
<br />

#### 리턴 값

	-1 : 아이디 또는 비밀번호가 입력되지 않음
	-2 : Request Method가 POST가 아님
	-3 : 아이디 또는 비밀번호가 일치하지 않음
	-4 : 통신 방식이 https가 아님
	
	-99 : 시스템 점검중
	
	이외의 음수 : 알수없는 오류
	이외의 자연수 : User SRL (성공)

이로서 일반로그인은 끝났다.<br /><br />

#### 예제
예시는 유니티로 들겠다

	* WWW 클래스 *
    using System;
    
    IEnumerator Cookiee_ID_Login()
    {
		WWWForm form = new WWWForm();
    	form.AddField("uid", 아이디 String);
    	form.AddField("ups", 비밀번호 String);
    	form.AddField("tkn", 게임 토큰 String);
    	WWW www = new WWW("https://cookiee.net/tools/login_normal", form);
    	yield return www;
    	int result = Convert.ToInt32(www.text);
    	
    	if (result<0){
    		//로그인 실패
    	}else{
    		//로그인 성공
    	}
    }
<br />

## 소셜 로그인
대부분이 이를 위해 이 문서를 볼것이다.<br>
소셜로그인은 현재 외부 브라우저를 이용하여 로그인 하는수밖에 없다.<br><br>

또한 외부 브라우저에서 불러오기에 로그인 체크를 할 방도가 없다.<br>
그렇기에 여기에서는 2개의 웹페이지를 체크하고 있어야 한다.
### 로그인 창
	오픈해야할 링크
    https://www.cookiee.net/login_open.direct
    
    패킷 형식
    GET
    
    보내야할 패킷
    token=게임 토큰
    hash=랜덤 해쉬 (아래 설명)
    
먼저 랜덤 해쉬란, 게임 내에서 랜덤하게 만들어야 하는 문자열이다.<br>
이 해쉬는 웹브라우저에서 로그인을 체크할때 필요하다.<br><br>

랜덤한 해쉬를 만드는 방식은 자유이나, 여기에서 추천하는 방법을 사용하는것을 추천한다.<br>
<b>주의, 절대로! 절대로! 겹치지 말아야할 범위의 문자열을 생성해야한다.<br>
주의, 문자열은 절대 80자가 넘지 않도록 한다.</b>

	1. 원하는 범위내의 절대로 겹치지 않을 수를 생성하고, 이를 sha1으로 암호화 한다.
	ex) 랜덤하게 수 생성 (1000~9999999 * 1~100)

### 로그인 체크
외부 브라우저에서 로그인을 진행할동안, 프로그램에서는 로그인이 완료되었는지 수시로 체크해야 한다.<br>
아래의 링크를 일정 시간 마다 (300ms~500ms) 계속 데이터를 받아와 로그인을 체크해야 한다.<br><br>
추천, 일정시간이 지나면(예 5분) 로그인을 취소하고 처음부터 다시 로그인을 시작한다.

	체크 링크
    https://www.cookiee.net/tools/login_social_check
    
    패킷 형식
    POST
    
    보내야할 패킷
    token=게임 토큰
    hs=랜덤 해쉬(이전에 만든 해쉬)
    
#### 리턴 값

	-1 : 로그인 데이터가 존재하지 않음 (로그인 되지 않음)
	
	-99 : 시스템 점검중
	
	이외의 자연수 : User SRL (로그인 성공)
	이외의 음수 : 알수없는 오류
이렇게 구현해주면 소셜로그인이 가능해진다.

예시는  유니티로 들겠다.
#### 예제
	using System.Security.Cryptography;
	using System.Text;
	using System.Collections;
	using System.Collections.Generic;
	using UnityEngine;
	using UnityEngine.Networking;

	public class LoginTest : MonoBehaviour
	{
	    private string Game_Token = "";
	    private string Random_Hash = "";
	    private int user_srl = -1;

	    void Start()
	    {
		using (SHA1Managed sha1 = new SHA1Managed())
		{
		    var hash = sha1.ComputeHash(Encoding.UTF8.GetBytes((Random.Range(1000, 9999999) * Random.Range(1, 100)).ToString()));
		    var sb = new StringBuilder(hash.Length * 2);

		    foreach (byte b in hash)
			sb.Append(b.ToString("X2"));

		    Random_Hash = sb.ToString().ToLower();
		}

		Social_Login_open();
		StartCoroutine(Social_Login_check()); //0.5초마다 실행
	    }

	    void Social_Login_open()
	    {
		Application.OpenURL("https://www.cookiee.net/login_open.direct?token=" + Game_Token + "&hash=" + Random_Hash);
	    }

	    IEnumerator Social_Login_check()
	    {
		while (true)
		{
		    WWWForm form = new WWWForm();
		    form.AddField("token", Game_Token);
		    form.AddField("hs", Random_Hash);
		    WWW www = new WWW("https://www.cookiee.net/tools/login_social_check", form);
		    yield return www;
		    user_srl = int.Parse(www.text);
		    if (user_srl < 0)
		    {
			StartCoroutine(Social_Login_check());
		    }
		    else
		    {
			//로그인 성공
			print("User_srl : " + user_srl);
		    }

		    yield return new WaitForSeconds(0.5f);
		}
	    }
	}

이상으로 소셜로그인 설명을 마친다.

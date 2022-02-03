---
layout: post
title:  "Spring Boot 로그인 유효성 검증"
date:   2022-01-23T12:12:52-02:00
author: Junsu Noh
categories: Spring
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

## Spring Boot 로그인 유효성 검증하기

검증자체는 쉽게 할 수 있지만 구조를 만들어 두어야 하기 때문에 조금 복잡도가 있을 수 있다.

로그인된 계정은 세션이 가지고 있으면서 관리를 하고 세션의 유효시간이 끝나면 로그인도 끝난다.

시작전에 web.xml에 session이 생성되면 시간을 얼마나 유지시킬지 설정해준다.

```
	<session-config>
        <session-timeout>20</session-timeout> <!--분 단위   -->
    </session-config>	
```



- **1** 컨트롤러에서 현재 로그인이 가능한 상태인지 체크하는 함수를 만들어준다. (서버점검이나 기타 등등 로그인 할 수 없는 환경일 경우엔 로그인을 못하게 해야하기 때문이다.)

**컨트롤러**

```
@GetMapping("/login")
	public String 로그인준비하다() {
		if(로그인아웃업무자.로그인준비가능한가()) {
			return "로그인창";
		}		
		return null;
	}
```

**서비스**

```
@Service
public class 로그인아웃업무자 {
	public boolean 로그인준비가능한가() {
	//현재는 다른 기타사항이 없기 때문에 true를 리턴해준다.
		return true;
	}
```



- **2** 로그인을 하게 만들어주는 컨트롤러를 만들어준다. 세션에 저장된 아이디는 후에 브라우저에서 사용되거나 사용자로그 등 여러가지로 사용할 수 있다.

```
@Controller
public class 로그인아웃컨트롤러 {
@PostMapping("/login")
	public String 로그인하다(String id, String password, HttpSession session) {
		//로그인 성공시
		if(로그인아웃업무자.로그인이가능한가(id, password)) {
			//로그인정보 저장
			session.setAttribute("id", id);
			//메인 페이지로
			return "redirect:/main";
		}
		//로그인 실패시 다시 로그인 페이지
		return "에러로그인창";		
	}
}
```



- **3** 함수를 이어줄 서비스를 만들어준다. 특별한 기능은 아직 없고 매개변수만 전달시켜준다.

```
@Service
public class 로그인아웃업무자 {
	@Autowired 회원업무자 회원업무자;
	
	public boolean  로그인이가능한가(String id, String  password) {
		return 회원업무자.있는가(id, password);
	}
}
```



- **4** 해당하는 아이디가 있는지 확인하는 SQL을 작성한 후 있다면 true를 없다면 false를 리턴해준다.

```
@Repository
public class 회원DAO {
	public boolean 있는가(String id, String password) {
		boolean 있다=  false;
		Connection con = null;		 
	    try{
	        Class.forName("com.mysql.cj.jdbc.Driver");
	        con=DriverManager.getConnection(DBConfig.DBURL, DBConfig.ID, DBConfig.PASSWORD);        
	        PreparedStatement 명령자=con.prepareStatement("select count(*) from member where id=? and password=?");
	        명령자.setString(1, id);
	        명령자.setString(2, password);
	        ResultSet 있냐표 =명령자.executeQuery();
	        if(있냐표.next()) {
	        	if(있냐표.getInt(1)==1) {
	        		있다=true;
	        	}
	        }
	        있냐표.close();
	        con.close();
	   }
	   catch(Exception ex){ ex.printStackTrace(); }
	    return 있다;
	}
}
```



- **5** 사용자에게 보여줄 브라우저에 View(jsp)를 만들어준다.

```
<body>
<h1>로그인</h1>
<form action="/login" method="post">
ID<input type="text" name="id" /><br>
Password<input type="password" name="password" /><br>
<input type="button" value="회원등록" />
<input type="submit" value="로그인" />
</form>
</body>
```



- **6** 아이디나 비밀번호가 존재하지 않을 경우에 나올 View(jsp)를 만들어준다. 기본틀은 로그인 브라우저와 같고 아이디 또는 패스워드가 일치하지 않는다는 메세지만 추가해준다.

```
<body>
<p>아이디 또는 패스워드가 일치하지않습니다.</p>

<h1>로그인</h1>
<form action="/login" method="post">
ID<input type="text" name="id" /><br>
Password<input type="password" name="password" /><br>
<input type="button" value="회원등록" />
<input type="submit" value="로그인" />
</form>
</body>
```



- **7** 로그인에 성공해서 들어갔을 때에 보여줄 메인 페이지창에 대한 컨트롤러를 만들어 준다.

```
@Controller
public class 내용컨트롤러 {
	@GetMapping("/main")
	public ModelAndView 메인페이지를주다(HttpSession session) { 
		ModelAndView mv = new ModelAndView();
		mv.setViewName("contents/main"); // view안에 contents폴더안에 main.jsp	
		//로그인아웃컨트롤러에서 받은 session에 대한 유효성 검증 절차
		boolean 로그인중=(session.getAttribute("id")!=null)?true:false;
		mv.addObject("isLogin",로그인중);
		return mv;
	}
}
```



- **8** 메인 페이지 View(jsp)를 만들어준다.

```
<% boolean isLogin=(boolean)request.getAttribute("isLogin"); %>
<body>
<%if(isLogin){ %> // 세션이 true라면 실행
    <button onclick="location.href='/logOut'">로그아웃</button>
    <button onclick="location.href='/member'">자신의 정보</button>
<%}else{ %>// 세션이 false라면 실행
	<button onclick="location.href='/login'">로그인</button>
<%} %>
<p>메인페이지</p>
</body>
```



- **9** 로그인 후 메인 페이지에서 자신의 정보라는 버튼을 눌렀을 때 나올 페이지에 대한 컨트롤러를 만들어 준다.

```
@Controller
public class Test회원컨트롤러 {
	
	@GetMapping("/member")
	public ModelAndView 회원자신의정보를보다() {
		//회원번호 = session.getAttribute("회원번호"); //맞는 코드지만 나중에 추가할 사항이므로 주석
		ModelAndView mv = new ModelAndView();
		mv.setViewName("member/Test자기정보창");
		return mv;
	}
}
```



- **10** 자신의 정보에 대한 View(jsp)를 만들어 준다.

```
<body>
	<h1>회원정보</h1>
	//아이디, 이름, 이메일 등등...
</body>
```



### 가장 중요한 Interseptor를 추가해 보자 

------

만약 로그인을 하지 않았을 때에 정보창이나 그 안의 내용들을 접근하려 할 때 그 명령을 가로채서 내가 원하는 방향으로 이끌게 해줄 수 있는 개념이다.

filter가 서블릿전에 걸러주는 개념이라면 interseptor는 서블릿은 들어오지만 핸들러 앞에서 걸러주는 느낌이다.

<img src="https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/Inkedinterseptor_LI.jpg" alt="Inkedinterseptor_LI" style="zoom:67%;" />

- **1** 먼저 로그인이 되어있는지 여부를 확인할 클래스를 만들어 주고 HandlerInterceptorAdapter를 상속받아서 사용하였다.  preHandle을 사용하게 되면 컨트롤러 전에 확인하고 postHandle을 사용하면 컨트롤러 후에 확인하게 된다.

```
public class 로그인확인자 extends HandlerInterceptorAdapter {
	@Override
	public boolean preHandle(HttpServletRequest request, 
			                 HttpServletResponse response, 
			                 Object handler)throws Exception {
		//요청이  컨트롤러 닿기전이다.
		//이때 로그인 여부를 확인하자
		HttpSession session = request.getSession(false);
		boolean 로그인중 =false;
		//만약 로그인되었다면
		if(session!=null&& session.getAttribute("id")!=null) {
			로그인중=true;
		}
		//로그인이 안되어 있었다면
		if(로그인중!=true) {
			
			//로그인으로  리다이렉트
			response.sendRedirect("/login");
			return false;
		}
		//true 종료면  요청이  컨트롤러에 도달한다.
		return true;
	}
}
```

로그인이 되어있지 않다면 다시 로그인을 하라는 로그인창으로 이동시키게 된다.



- **2** Interseptor는 WebMvconfigurer에 override해줘야 사용이 가능하므로 override 해준다.

```
@ComponentScan(basePackages="com.stone") //Bean객체 자동 등록
public class MvcConfig implements WebMvcConfigurer {

@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new 로그인확인자())
		        .addPathPatterns("/**")
		        .excludePathPatterns("/main")
		        .excludePathPatterns("/login");
		
	}
}
```

좀 전에 만들어준 클래스를 Interceptor에 등록해주고 메인 페이지나 로그인창은 굳이 로그인을 하지 않아도 괜찮으므로 Interceptor에게 예외를 등록해준다.



이 때 무수히 많은 Bean 객체들을 등록하는 것보다 @ComponentScan을 사용하면 코드 누락에 대한 위험성이 줄어들고 시간 절약과 메모리 절약을 증가시켜 주는 효과를 볼 수 있다. 



basePackages="패키지"를 써주면 그 해당 패키지의 이름을 상위로 가지고 있는 패키지들은 모두 Bean에 자동으로 등록이 된다.



### 이렇게 해서 로그인 유효성 검증에 대한 확인절차와 Interceptor를 사용한 접근제한에 대해서 알아보았다.

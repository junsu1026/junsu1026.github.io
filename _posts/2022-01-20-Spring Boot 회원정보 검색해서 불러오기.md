---
layout: post
title:  "Spring Boot 회원정보 검색해서 불러오기"
date:   2022-01-19T14:25:52-05:00
author: Junsu Noh
categories: Dummy
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

## Spring Boot 회원정보 검색해서 불러오기

- **1** 게시판에서 글을 쓴 회원정보를 DB에 검색해서 가져오는 로직을 위해선 컨트롤러에서 
- @Autowired 어노테이션을 사용해서 서비스 객체를 매핑해주고 
- @GetMapping으로 valueKey가 들어오면 Key에 해당하는 정보를 DB에서 불러와 보여 줄 수 있도록 해준다. 



```
@Autowired
	게시물관리 게시물관리; 
	
	@GetMapping("/member/{no}") 
	public ModelAndView 회원조회하다(@PathVariable int no) {
		Member 찾은회원 = 회원관리자.회원정보조회하다(no);
		ModelAndView mv = new ModelAndView();
	    mv.addObject("member",찾은회원);
		mv.setViewName("회원정보창");
		return mv;
	}
```

- **2** 서비스에서 다시 DAO로 받은 매개변수를 전달해준다.

   

```
@Autowired
	회원DAO 회원DAO;
public Member 회원정보조회하다(int no)//key {
		Member 찾은회원 = 회원DAO.회원조회하기(no);
		return 찾은회원;
	}
```

- **3** DAO를 실행하기전에 회원정보를 담아주는 VO를 만들어준다.

변수와 이름을 설정해주고 빈공간에 마우스 오른쪽 버튼을 누른후 Source -> Generate Getters and Setters -> 모든사항 체크 후 확인만 눌러주면 모든 get,set 메소드를 자동으로 만들어준다. (이클립스 기능)

```
public class Member {
	int no;
	String name;
	String tel;
	String post;
	String address;
	String detailAddress;
	String id;
	String password;
	String email;
	char state;//상태:정상1,휴면2,탈퇴3
	Date rdate;//등록일
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getTel() {
		return tel;
	}
	public void setTel(String tel) {
		this.tel = tel;
	}
	public String getPost() {
		return post;
	}
	public void setPost(String post) {
		this.post = post;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	public String getDetailAddress() {
		return detailAddress;
	}
	public void setDetailAddress(String detailAddress) {
		this.detailAddress = detailAddress;
	}
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public char getState() {
		return state;
	}
	public void setState(char state) {
		this.state = state;
	}
	public Date getRdate() {
		return rdate;
	}
	public void setRdate(Date rdate) {
		this.rdate = rdate;
	}
}

```

- **4** DAO에서 sql문을 사용해 DB에 있는 회원정보를 들어온 매개변수 key를 사용해서 해당되는 정보를 불러온다.



```
public Member 회원조회하기(int no) {
		Connection con = null;	
		 Member member = new Member();
	    try{
	        Class.forName("com.mysql.cj.jdbc.Driver");
	        con=DriverManager.getConnection(DBConfig.DBURL, DBConfig.ID, DBConfig.PASSWORD);// DB주소와 아이디 비밀번호는 따로 클래스를 만들어서 static변수로 불러왔다.
	        PreparedStatement 명령자=con.prepareStatement("select * from member where no=?");
	        명령자.setInt(1, no);
	        ResultSet rs =명령자.executeQuery();
	        while(rs.next()) {
	        String name = rs.getString("name");
	        String tel = rs.getString("tel");
	        String post =	rs.getString("post");
	        String address =	rs.getString("address");
	        String detailAddress =	rs.getString("detailAddress");
	        String id =	rs.getString("id");
	        String password =	rs.getString("password");
	        String email =	rs.getString("email");
	        char state =	rs.getString("state").charAt(0);
	        Date rdate =	rs.getDate("rdate");
	        member.setName(name);
	        member.setTel(tel);
	        member.setPost(post);
	        member.setAddress(address);
	        member.setDetailAddress(detailAddress);
	        member.setId(id);
	        member.setPassword(password);
	        member.setEmail(email);
	        member.setState(state);
	        member.setRdate(rdate);
	        } 
	        rs.close();
	       con.close();
	   }
	   catch(Exception ex){ ex.printStackTrace(); }
		return member;
	} 
```

- **5** 컨트롤러에서 "회원정보창.jsp"로 정보를 전달해주었으니 jsp에서 EL을 사용해서 불러와주면 끝이난다.



```
<body>
<h1>회원정보</h1>
성명:${member.name}<br>
전화번호:${member.tel}<br>
우편번호:${member.post}<br>
주 소:${member.address}<br>
상세 주소:${member.detailAddress}<br>
ID:${member.id}<br>
E-mail:${member.email}<br>
</body>
```

- **6** 서버를 실행해주고 주소창에 http://localhost:(포트번호)/member/1을 검색해주면 내 DB에 존재하는 1번 Key에 해당한 회원 정보를 화면에 보여주게된다.

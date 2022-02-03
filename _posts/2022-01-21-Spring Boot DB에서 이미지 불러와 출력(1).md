---
layout: post
title:  "Spring Boot DB에서 이미지 불러와 출력(1)"
date:   2022-01-20T21:04:00-05:00
author: Junsu Noh
categories: Spring
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---



## Spring Boot DB에서 BLOB 이미지 불러와서 출력하기(1)(mysql)



**DB에서 이미지 파일을 불러와 출력하는건 생각보다 쉽다.** 

이 포스트는 BLOB파일로 mysql에 저장되어있는 이미지 파일을 불러와 출력하는 포스트 입니다.

2가지 방법이 있다. 

첫번째 : **주소 설정 출력**

두번째 : **라이브러리를 활용한 출력**

이 글은 주소 설정 출력 방법이다.



### 1. 주소 설정해서 출력하기

------

View(jsp)에서 이미지 태그안에 들어갈 주소를 설정해줘야 하는데 그러기 위해선 내가 직접 주소를 만들어 넣는 방법이 있다. 

```
<img src="여기 들어갈 주소">
```



- **1** 이미지정보를 가져올 컨트롤러를 만들어준다. 

```
@Controller
public class 이미지컨트롤러 {
	@Autowired 이미지관리자 이미지관리자;

	@GetMapping("/image/{no}")  //예 /image/1<-패스변수
	public ModelAndView 이미지조회하다(@PathVariable int no) {
		ImageVO 찾은이미지 = 이미지관리자.이미지정보를조회하다(no);
		ModelAndView mv=new ModelAndView();
		mv.addObject("image", 찾은이미지);
		mv.setViewName("이미지정보창");
		return mv;
	}
}

```

이미지번호를 전달 받으면 그 번호에 해당하는 이미지 정보를 DB에서 가져오게 하는 메소드를 서비스에 전달한다.



- **2** DAO로 전달할 서비스를 만들어주자

```
@Service
public class 이미지관리자 {
   	@Autowired
	이미지DAO 이미지DAO;

	public ImageVO 이미지정보를조회하다(int no) {
		//업무
		//DB처리
		return 이미지DAO.찾다By번호(no);
	}
}

```



- **3** DAO가 이미지 정보를 가져올 수 있도록 VO를 만들어주자

```
public class ImageVO {
	int no;
	MultipartFile profileFile;//업로드된 파일 매칭요
	byte [] imagefile;          // 프로필사진 (데이터)    
	Date rdate;//등록일
	public int getNo() {
		return no;
	}
	public void setNo(int no) {
		this.no = no;
	}	
	public MultipartFile getProfileFile() {
		return profileFile;
	}
	public void setProfileFile(MultipartFile profileFile) {
		this.profileFile = profileFile;
	}
	public byte[] getimagefile() {
		try {
			if(profileFile!=null && imagefile==null) {
				//업로드된 파일에서 사진 데이터만 추출
				imagefile = profileFile.getBytes();
			}
		}
		catch(Exception ex) {ex.printStackTrace();}
		return imagefile;
	}
	public void setimagefile(byte[] imagefile) {
		this.imagefile = imagefile;
	}
	
	public Date getRdate() {
		return rdate;
	}
	public void setRdate(Date rdate) {
		this.rdate = rdate;
	}
}
```



- **4** DB에 저장되어있는 BLOB형태의 이미지 파일을 불러오는 sql문을 사용해서 DAO의 메소드를 통해 데이터를 가져오자

```
@Repository
public class 이미지DAO {
public ImageVO 찾다By번호(int no) {
		ImageVO 찾은이미지 = null;
		Connection con = null;		 
	    try{
	        Class.forName("com.mysql.cj.jdbc.Driver");
	        con=DriverManager.getConnection(DBConfig.DBURL, DBConfig.ID, DBConfig.PASSWORD);
	        PreparedStatement 명령자=con.prepareStatement("select * from imageVO where no=?"); //테이블명이 imageVO다.
	        명령자.setInt(1, no);
	        ResultSet 이미지표 =명령자.executeQuery();
	        if(이미지표.next()) {
	        	byte[] imagefile=이미지표.getBytes("imagefile");
	        	Date rdate = 이미지표.getDate("rdate"); //등록일
	        	
	        	찾은이미지 = new ImageVO();
	        	찾은이미지.setNo(no);
	        	찾은이미지.setimagefile(imagefile);
	        	찾은이미지.setRdate(rdate);	        	
	        } 
	        이미지표.close();	        
	        con.close();
	   }
	   catch(Exception ex){ ex.printStackTrace(); }
	    return 찾은이미지;
	}
}
```



- **5** 이미지를 출력해줄 View(jsp)를 만들어주자

```
<body>
<h1>이미지 정보</h1>
이미지: <img src="/imagefile/${image.no}" width="200" height="300"/><br> 
Date:${image.rdate }
</body>
```

저 /imagefile/이라는 주소는 직접 만들어서 매핑해줄 것이다. image.rdate는 현재까지만 실행해도 불러올 수가 있다. 하지만 이미지는 불러와지지 않는다. 그래서 이제부터 이미지 매핑을 해줄 것이다.



- **6** 새로운 컨트롤러를 하나 만들어서 매핑과 이미지 정보를 불러올 메소드를 만들어주자

```
@Controller
public class 이미지프로필 {
	@Autowired 이미지관리자 이미지관리자; 
 
	@GetMapping("/imagefile/{이미지번호}")  // 예 /profile/1
	ModelAndView 사진을주다(@PathVariable("이미지번호") int  no){
		
		ImageVO 이미지VO= 이미지관리자.이미지정보를조회하다(no);
		ModelAndView mv = new ModelAndView();
		mv.setViewName("imagefile");
		mv.addObject("image",이미지VO.getimagefile());
		return mv;
	}
}
```

정보를 조회하는 메소드와 VO는 위에 나온 메소드들과 동일하다. 하지만 VO의 이미지파일을 전달해서 도착하는 jsp의 주소가 다르다. 



- **7** "imagefile"이라는 jsp를 만들어서 이미지파일 잘 변환해서 불러올 수 있도록 파일을 만들어주자.

```
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ page trimDirectiveWhitespaces="true" %> //혹시 모를 코드의 빈 공간이 존재하면 삭제해준다.
<%
   //응답(response)객체에 이미지 전송하도록 설정
   byte[] 이미지파일=(byte[])request.getAttribute("image"); 
   response.setContentType("image/png");
   ServletOutputStream 배송자= response.getOutputStream();
      배송자.write(이미지파일);
%>
```

위의 컨트롤러에서 ModelAndView로 보내준 데이터를 받아서 (BLOB파일은 byte[]로만 받을 수 있다.)



이미지 파일로 response 할 수 있도록 해주고 그 내용들을 다시 ServletOutputStream이 밖으로 나갈 수 있도록 도와준다.



*컨트롤러, 서비스, DAO 클래스는 모두 빈에 등록하는 것을 잊지말자 

*저는 server.xml에 path경로를 "/" 이렇게만 해주었습니다.



- localhost:포트번호/image/{이미지번호}를 넣어주면 

![0122](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/0122.PNG)

다음과 같은 창을 볼 수 있게 된다.

다음 글에선 주소가 아니라 라이브러리를 통한 이미지 출력을 포스팅해보려고 한다.

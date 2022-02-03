---
layout: post
title:  "Spring Boot 파일 업로드 방법"
date:   2022-01-17T14:25:52-05:00
author: Junsu Noh
categories: Spring
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---



## Spring Boot 파일 업로드 방법 (1)

우선 파일을 업로드 하기 전에 먼저 알아야 할 부분이 있다.

라이브러리를 추가해야 하는데 주소체계를 짚고 넘어가겠다.

[http://localhost:8080/path/요청]

http는 웹서버의 프로토콜을 뜻하고 (https s가 붙으면 사이트에 필요한 인증키들을 암호화해서 보여주기 때문에 좀 더 안전한 브라우저를 이용할 수 있다.)

localhost는 현재 나의 ip주소다 (127.0.0.1)

8080은 내 프로그램의 포트번호이고 

```
(server.xml의 <connector port=값> 확인)
```

path는 내 프로그램내의 경로설정이다 

```
(server.xml의 <context path=값>확인)
```

예) path="/springmvc" 라고 되어 있으면 localhost:8080/springmvc/list.jsp 와 같은 형식을 만들어줄 수 있다.

요청은 경로안의 실행할 프로그램의 주소다.

maven은 spring에 필요한 라이브러리들을 모두 관리해주기 때문에 좀 더 편하게 파일 업로드 기능을 수행 할 수 있다.

### form태그를 이용한 업로드 방법

- **1** 먼저 폼태그를 만들어준다 프로필 src에 들어가 있는 이미지는 파일을 올리기전 이미지파일을 올려둔 것이다. 

```
<form onsubmit="return 회원등록하다()" action="/member" method="post"> 
성명*<input type="text" name="name" id="name"/><br>
프로필 <img id="profile" src="/img/no_image.jpg" style="width:200px; height:200px;"><br>
     <input type="file" name="profilefile" id="profilefile"/><br> 
```



![이미지 ](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/이미지 .png)

다음과 같은 모양의 폼이 만들어졌다.

- **2** 이미지 파일을 올렸을때 이미지가 미리보기로 보여지도록 세팅한다 (이 부분은 생략가능한 부분이다.)

   ```
   <script type="text/javascript">
   function 그림파일읽어출력하기(이벤트) {
   	let fileInput = 이벤트.target; //해당하는 Selector의 매개변수
   	let 선택된그림파일 = fileInput.files[0];
   	let size = 선택된그림파일.size;
   	
   // 	if(size > 1024*10){
   // 		alert("10K 용량 초과");
   // 		fileInput.value="";
   // 		return ;
   // 	}
   	
   	if(!선택된그림파일.type.match('image.*')){
   		alert("그림이 아닙니다.");
   		fileInput.value="";
   		return ;
   	}
   	let 파일리더 = new FileReader();
   	파일리더.onload = function(한그림파일) {
   		let imgProfile = document.getElementById("profile");
   		imgProfile.src = 한그림파일.currentTarget.result;
   	};
   	파일리더.readAsDataURL(선택된그림파일);
   	return ;
   }
   document.querySelector("#profilefile").addEventListener("change",그림파일읽어출력하기,false);
   </script>
   ```

   profile 이란 id를 가지고 있는 input타입을 선택해서 이벤트발생에 의한 폼필드의 변화가 생기면 함수를 실행하고 해당 이벤트에 대한 value값을 매개변수로 전달합니다.

   매개변수.target을 하게되면 

   ```
   <input type="file" name="profilefile" id="profilefile">
   ```

   이러한 문장 자체를 가져오게된다.

   그래서 .files[0]을 하게 되면 올라온 이미지 한개를 선택하게된다.

   .type.match('image.*')을 하게되면 해당타입이 이미지인 파일을 가져온다

   FileReader 객체를 만들어주고 파일리더.readAsDataURL이 파일의 URL을 읽어들이면 onload가 자동으로 실행되면서 함수실행이 되고 img src의 주소를 받아들인 이미지파일의 주소로 바꾸어 주는 방법이다.



### 환경설정

파일 업로드를 위해서는 라이브러리를 추가해서 하는 방법이 빠르고 쉽다 

- **1** pom.xml에 라이브러리 추가

```
<dependency>
  	 <groupId>commons-io</groupId>
  	 <artifactId>commons-io</artifactId>
  	 <version>2.10.0</version>
  </dependency>
```

- **2** web.xml에 업로드 경로와 파일 사이즈 설정

```
<multipart-config>
		<location>/upload</location>
		<max-file-size>20971520</max-file-size><!-- 최대크기 20M -->
		<max-request-size>41943040</max-request-size><!-- 한번에 올릴 최대 크기 40M -->
		<file-size-threshold>20971520</file-size-threshold><!-- 메모리 -->
</multipart-config>
```

- **3** form태그에 enctype="multipart/form-data" 추가 

```
<form onsubmit="return 회원등록하다()" action="/member" method="post" enctype="multipart/form-data"> 
```

multipart/form-data는 여러개의 파일을 올리기 쉽게 사용하도록 만들어진 방식으로 post 메소드로만 사용이 가능하다.

- **4** mvc파일수정 BeanConfig에 multipart 등록

```
@Bean
	public StandardServletMultipartResolver multipartResolver() {
		return new StandardServletMultipartResolver();
	}
```

- **5** VO에 새롭게 올라갈 파일 변수 작성

```
private MultipartFile profilefile;
public MultipartFile getProfilefile() {
		return profilefile;
	}
	public void setProfilefile(MultipartFile profilefile) {
		this.profilefile = profilefile;
	}
```

- **6** 컨트롤러에서 작동시킬 함수에 매개변수 어노테이션 추가

```
@PostMapping("/member")	
	public ModelAndView 회원등록하다(@ModelAttribute Member 새회원) {
		ModelAndView mv=new ModelAndView();
		return mv;
	}
```

@ModelAttribute 추가시 controller에서 자동으로 VO객체와 매핑을 해줘서 일일이 하나씩 파라미터를 지정해주지 않아도 된다.

### 이러한 환경 설정을 해주면 DAO로 보낼 VO가 완성되었다. 나머지는 2에서 다루겠다.

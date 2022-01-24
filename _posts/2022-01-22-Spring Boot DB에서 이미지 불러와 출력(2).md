---
layout: post
title:  "Spring Boot DB에서 이미지 불러와 출력(2)"
date:   2022-01-22T14:25:52-05:00
author: Junsu Noh
categories: Dummy
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

## Spring Boot DB에서 BLOB 이미지 불러와서 출력하기(2)(mysql)



**DB에서 이미지 파일을 불러오는 방법 (2)** 

이 포스트는 BLOB파일로 mysql에 저장되어있는 이미지 파일을 불러와 출력하는 포스트 입니다.

2가지 방법이 있다. 

첫번째 : **주소 설정 출력**

두번째 : **라이브러리를 활용한 출력**

이 글은 라이브러리를 활용한 출력 방법 이다.

라이브러리를 사용하면 주소 설정보다 좀 더 간략하게 이미지를 불러 올 수 있다.



### 1. 라이브러리를 활용한 출력

------

View(jsp)에서 이미지 태그안에 들어갈 주소를 설정해줘야 하는데 엔코드 방법을 사용해서 넣어보자

```
<img src="여기 들어갈 주소">
```



- 컨트롤러와 서비스 DAO는 이 전 글인 

- https://junsu1026.github.io/dummy/2022/01/21/Spring-Boot-DB%EC%97%90%EC%84%9C-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%B6%88%EB%9F%AC%EC%99%80-%EC%B6%9C%EB%A0%A5(1).html

  에서 설명해두었다.

- 우선 라이브러리를 pom.xml에 설정해주어야 한다.

  https://mvnrepository.com/artifact/net.iharder/base64/2.3.9





- **1** 위 주소에서 코드를 복사해서 pom.xml에 넣어준다

```
<dependency>
    <groupId>net.iharder</groupId>
    <artifactId>base64</artifactId>
    <version>2.3.9</version>
</dependency>
```



- **2** 컨트롤러에 Base64 함수를 추가한 후 String으로 변환해서 ModelAndView에 넣어준다

```
@Controller
public class 회원컨트롤러 {
	@Autowired 이미지관리자 이미지관리자;

	
	@GetMapping("/image/{no}")  //예 /memeber/1<-패스변수
	public ModelAndView 회원조회하다(@PathVariable int no) {
		ImageVO 찾은이미지 = 이미지관리자.이미지정보를조회하다(no);
		ModelAndView mv=new ModelAndView();
		String 이미지엔코드 = (String)Base64.encodeBytes(찾은이 미지.getimagefile()); //이 부분이다.
		mv.addObject("image", 찾은이미지);
		mv.addObject("imageencode", 이미지엔코드); //ModelAndView에 추가
		mv.setViewName("이미지정보창");
		return mv;
	}
}
```



- **3** View(jsp)에서 "data:/png;base64,${Key값}"을 넣어준다

```
<h1>이미지 정보</h1>
이미지: <img src="data:/png;base64,${imageencode}" width="200" height="300"><br> 
Date:${image.rdate }

</body>
```



이제 실행해주면 브라우저에 이미지를 불러오게 된다. **정말 쉽다.**

![0122](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/0122.PNG)


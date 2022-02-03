---
layout: post
title:  "Spring Boot 파일 업로드 방법(2)"
date:   2022-01-18T14:25:52-05:00
author: Junsu Noh
categories: Spring
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---



## Spring Boot 파일 업로드 방법 (2)

- **1** 경로를 지정해줘서 파일이 업로드 되면 내가 원하는 장소로 이동해 줄 수 있도록 만든다.

```
@PostMapping("/member")	
	public ModelAndView 회원등록하다(@ModelAttribute Member 새회원, HttpServletRequest req) {
		try {
			String 절대경로 = 	 	    req.getSession().getServletContext().getRealPath("/");
		System.out.println(절대경로);
		MultipartFile mf = 새회원.getProfileFile();
		String 파일명 = mf.getOriginalFilename();
		File 파일 = new File(절대경로+"upload\\"+파일명);
		mf.transferTo(파일); //파일저장
		}
		catch(Exception e){e.printStackTrace();}
		//회원관리자.저장하다(새회원);
		
		ModelAndView mv=new ModelAndView();
		mv.addObject("name", 새회원.getName());
		mv.setViewName("회원등록알림창");
		return mv;
	}
```

경로를 설정해줘야 하는데 이 때 절대경로를 사용하여서 내가 원하는 곳으로 파일이 오도록 만들어 줘야 하는데 

**MultipartFile 객체를 사용하게 되면 Spring 자체에서 새로운 임시 경로를 복제해서 그곳에서 파일들을 다루게 되는데** 

```
File 파일 = new File(절대경로+"upload\\"+파일명);
```

위의 경로 설정 때문에 브라우저에서는 500번대 에러가 난다 그러면서 아래와 같은 경로가 유효하지 않다고 뜨는데 검은색 부분은 개인의 워크스페이스 경로이고 마지막에 ROOT/upload가 유효하지 않다고 뜨는 것은 위의 경로 설정에서 직접 upload라는 파일 경로를 써주었기 때문이다.

그러므로 

- **2** wepapp의 하위 폴더로 upload라는 폴더를 만들어주고 아래의 경로를 복사해서 그곳에 upload라는 폴더를 만들어주면 되는것이다.

[경로는 C: 부터 ROOT까지를 복사해서 파일탐색기의 경로에 붙여 넣어주면 폴더로 들어가진다.]

![Spring temporary path](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/Spring temporary path.PNG)

폴더를 만들고 다시 실행해보면 위와 같은 500번대 오류는 뜨지 않게 되고 제대로 실행이 가능해진다.  

별개로

```
req.getSession().getServletContext().getRealPath("/");
System.out.println(절대경로);
```

위 메소드들을 사용하면 절대경로를 알아낼 수 있다.

절대경로는 위에 말했다시피 Spring 자체에서 임시경로를 만들어 놓은 것이기 때문에 건드려서 서버에 에러가 나는 일은 없도록 하자

제대로 실행하게 되면 절대경로 안에 존재하는 upload라는 폴더 안에 내가 원하는 파일이 잘 저장된 것을 확인 할 수 있다. 

```
mf.transferTo(파일); 
```

함수 실행시 VO에서 가져온 정보를 MultipartFile이 파일의 경로안으로 파일을 생성시켜주게된다. 

다음은 DB에 여러종류의 확장자를 가진 파일들을 저장하는 방법을 공유하겠습니다.
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

1. 게시판에서 글을 쓴 회원정보를 DB에 검색해서 가져오는 로직을 위해선 컨트롤러에서 Autowired 어노테이션을 사용해서 서비스 객체를 매핑해주고 

   

```
@Autowired
	게시물관리 게시물관리; 
	
	@GetMapping("/member") 
	public ModelAndView 회원조회하다(@PathVariable int no) {
		Member 찾은회원 = 회원관리자.회원정보조회하다(no);
		ModelAndView mv = new ModelAndView();
		
	    mv.addObject("member",찾은회원);
		mv.setViewName("회원정보창");

		return mv;
		
	}
```


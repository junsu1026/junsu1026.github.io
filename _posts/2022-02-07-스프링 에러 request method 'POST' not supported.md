---
layout: post
title:  "스프링 에러 request method 'POST' not supported"
date:   2022-02-06T14:25:52-05:00
author: Junsu Noh
categories: Spring
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

## 스프링 에러 request method 'POST' not supported

Spring를 이용해서 기능을 만들던 중에 다음과 같은 에러가 나버렸다.

그래서 구글링을 했고 

Mapping이 서로 맞지 않아서 에러가 난다고 나와 있어서 View와 Controller 둘 다 모두 PostMapping으로 바꾸어 주었지만 같은 오류가 계속 나버렸다.

머리를 싸매던 끝에 해결 방법을 찾았다.



![405 requset method 'POST' not supported](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/405 requset method 'POST' not supported.PNG)

```
@PostMapping("/modify")
	ModelAndView 게시물수정(@ModelAttribute Board 수정게시물) {
		게시물관리.게시물을수정하다(수정게시물);
		ModelAndView mav = new ModelAndView();
		mav.setViewName("forward:/list");
		return mav;
	}
```

위의 코드가 내 View에서 보낸 Mapping을 받는 Controller코드였다.

문제는 foward를 받아주는 쪽의 Mapping도 연관이 있는 것이었다.

그래서 위의 forward를 받아주는 /list의 Mapping도 PostMapping으로 바꾸어 준 결과 제대로 동작하는 것을 확인 할 수 있었다.



3줄 요약

- View와 Controller의 Mapping이 같은지 확인
- 같은 데도 오류가 난다면 Controller에서 다른 페이지로 forward 해주는지 확인
- forward를 받는 Mapping도 보내는 Controller와 같은 Mapping으로 변경




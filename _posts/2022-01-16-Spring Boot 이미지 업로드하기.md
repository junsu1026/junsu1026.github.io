---
layout: post
title:  "Spring Boot 게시글 이미지 업로드"
date:   2022-01-16T16:10:52-05:00
author: Junsu Noh
categories:
- Dummy
tags: jekyll welcome
cover: "/assets/instacode.png"
---

Spring Boot에서 xml 파일을 건드리지 않고 이미지 파일을 업로드 할 수 있도록 설정을 변경해보자.



xml 파일을 건드리게 되면 오류가 자주 나기 때문에 config 폴더를 새로 만들어서 클래스로 관리하면 오류가 많이 나지 않는다

web.xml 파일 안에 서블릿을 추가 해주면 된다.

```
<servlet>//태그 안에 아래 내용을 넣어주고 세이브해준다.
<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
				config.MvcConfig
				config.BeanConfig
			</param-value>	
		</init-param>
		<load-on-startup>1</load-on-startup>

</servlet>
```





그리고 src/main/java 안에 config라는 패키지를 한개 만들어 준다 위에 코드에 보면 파라미터의 value 값 패키지 명을 config로 했기 때문에 그렇다.

패키지 안에 MvcConfig라는 클래스를 한개 만들어주고 그 안에 이제 인터페이스를 통한 오버라이드를 해줄 것이다.



```@Configuration
package config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.ViewResolverRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@EnableWebMvc
public class MvcConfig implements WebMvcConfigurer {
@Override
public void configureViewResolvers(ViewResolverRegistry registry) {
	registry.jsp("/WEB-INF/views/",".jsp");
}
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
	registry.addResourceHandler("/img/**").addResourceLocations("/img/");
	}
}
```

위와 같은 코드를 작성하면 되는데 

첫번째 오버라이드는 xml에서 경로 설정 해주는 것을 따로 빼서 jsp를 WEB-INF폴더 안에 views 폴더안에 jsp 파일들에 대한 경로 설정을 해준 것이다. 물론 WEB-INF 폴더나 views폴더는 직접 만들어 주어야 한다.



두번째 오버라이드는 xml에서 이미지 경로 설정을 빼서 오버라이드를 해준 것이다. 절대경로 설정으로 하였는데 상대경로보다 절대경로가 좀 더 이해나 작성이 쉽다. 

절대 경로는 wepapp 폴더를 기준으로 하는데 wepapp폴더 안에 img 폴더를 만들어서 그 안에 파일들을 넣어주고 경로 설정을 해주면 끝이난다.

wepapp안에 jsp파일을 만들어 주고 경로 설정을 해보자

```
<body>
<img src="/img/no_image.png">
</body>
```


나는 이미지 파일을 no_image라는 이름으로 저장하고 경로 설정을 하였다.

위와 같이 해주면 성공적으로 이미지 파일이 올라가는 것을 확인할 수 있다.

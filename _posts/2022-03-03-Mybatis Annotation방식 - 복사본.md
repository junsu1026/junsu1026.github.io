---
layout: post
title:  "Mybatis Annotation방식"
date:   2022-03-02T14:12:52-05:00
author: Junsu Noh
categories: Mybatis
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## Mybatis Annotation방식



///이 전글 [Mybatis에 대해서]에 의존성 방법과 web.xml 방법이 나와있다.///

Mybatis 방법에는 xml을 이용하는 방법과 클래스만을 사용해서 Annotation으로 SQL을 관리하는 방법이 있다. 

Mybatis Annotation 방식은 많이 사용되는 방식은 아니지만 클래스를 이용한다는 점에서 직관적으로 확인이 가능하므로 mybatis를 잘 알지 못해도 사용이 가능하다.



사용방법은 간단하다

- Mapper interface를 만들어서 SQL들을 전부다 모아서 관리해주면 된다. 클래스를 만든뒤 그 안에 

@Select, @Insert, @Delete, @Update 등 @를 치고 자동완성 기능을 사용하면 확인이 가능하다.

그중 Insert 방법을 사용해서 해보겠다.

```
@Mapper
public interface IMissionMapper {
						
	//저장
	@Insert("insert into mission(title, contents) values(#{title},#{contents})")
	void save(Mission newMission);
}
```

Mapper 인터페이스를 만든뒤 Insert문을 한개 만들었다.

Service는 생략하고 Controller와 DAO만 사용해보겠다.



- 컨트롤러

```
@Controller
public class Mission_Contolle {
	
	@Autowired IMission_DAO mission_DAO;
	
	//등록
	@GetMapping("/goinsert")
	public ModelAndView itIsregistered(Mission newMission) {

		//1.데이터베이스에 등록
		mission_DAO.save(newMission);
		
		 ModelAndView mv=new ModelAndView();
		 //저장 한 뒤 리스트로 이동
		 mv.setViewName("missonlist"); 
		 
		return mv;
		
		
	}
```



- DAO

```
public interface IMissionDAO {
	//저장
	void save(Mission newMission);
}
```



- DTO

```
public class Mission {
	
	int no;
	String title;
	String contents;
	//getter, setter 생략...
	
```



이렇게만 만들어줘도 DB에 저장이 가능하다

자동으로 Mapper가 DTO와 매핑을 해주기 때문에 번거로운 작업을 안해줘도 된다는 큰 장점이 있다.

#{값1},#{값2}..... 이런식으로 해주면 SQL에 DTO의 값을 자동으로 매핑해주기 때문에 아주 편하다



그 외에도 @Select문은 

이러한 방법으로 들어온 값은 @Results안에 @Result라는 속성값을 설정해주면 가지고 있는 DTO에 자동으로 매핑되어 저장이된다.

다른 SQL문을 섞어서 사용할때는 @One 어노테이션을 사용해서 값을 가져오면 자동으로 매핑이 되어준다.

```
@Select("select * from mission ORDER BY date DESC")
	@Results(value= {
			@Result(property="no",column="no"),
			@Result(property="title", column="title"),
			@Result(property="contents", column ="contents"),
			@Result(property="writer", column="writer",	one=@One(select="com.stone.member.dataservice.IMemberMapper.회원번호를찾다")),
			@Result(property="views", column="views"),
			@Result(property="date", column="date")})
	List<Mission> collectAllNotices();
```



혹시나 어려운점이나 이해가 안되는 점이 있다면 물어봐주시면 성실히 답해드리겠습니다.

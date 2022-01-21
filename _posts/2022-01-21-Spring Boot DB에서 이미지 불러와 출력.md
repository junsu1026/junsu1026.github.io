---
layout: post
title:  "Spring Boot DB에서 이미지 불러와 출력"
date:   2022-01-20T21:04:00-05:00
author: Junsu Noh
categories: Dummy
---



## Spring Boot DB에서 BLOB 이미지 불러와서 출력하기(mysql)



**DB에서 이미지 파일을 불러와 출력하는건 생각보다 쉽다.** 

이 포스트는 BLOB파일로 mysql에 저장되어있는 이미지 파일을 불러와 출력하는 포스트 입니다.

2가지 방법이 있다. 

첫번째 : **주소 설정 출력**

두번째 : **라이브러리를 활용한 출력**



### 1. 주소 설정해서 출력하기

------

View(jsp)에서 이미지 태그안에 들어갈 주소를 설정해줘야 하는데 그러기 위해선 내가 직접 주소를 만들어 넣는 방법이 있다. 

```
<img src="여기 들어갈 주소">
```

- 이미지정보를 가져올 컨트롤러를 만들어주자 

```
@Controller
public class 이미지컨트롤러 {
	@Autowired 이미지관리자 이미지관리자; 
 
	@GetMapping("/profile/{이미지번호}")  // 예 /image/1
	ModelAndView 이미지번호를주다(@PathVariable("이미지번호") int no){
		
		ImageVO 이미지VO = 이미지관리자.이미지정보를조회하다(no);
		ModelAndView mv = new ModelAndView();
		mv.setViewName("imageview");
		mv.addObject("image",이미지VO.getimagefile());
		return mv;
	}
}
```

이미지번호를 전달 받으면 그 번호에 해당하는 이미지 정보를 DB에서 가져오게 하는 메소드를 서비스에 전달한다.

- DAO로 전달할 서비스를 만들어주자



```
@Service
public class 이미지관리자 {
   	@Autowired
	이미지DAO 이미지DAO;
public Member 이미지정보를조회하다(int no) {
		
		//업무
		//DB처리
		return 이미지DAO.찾다(no);
	}
}
```



- DAO가 이미지 정보를 가져올 수 있도록 VO를 만들어주자

```
public class ImageVO {
	int no;
	MultipartFile profileFile;//업로드된 파일 매칭요
	byte [] profile;          // 프로필사진 (데이터)    
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
	public byte[] getProfile() {
		try {
			if(profileFile!=null && profile==null) {
				//업로드된 파일에서 사진 데이터만 추출
				profile = profileFile.getBytes();
			}
		}
		catch(Exception ex) {ex.printStackTrace();}
		return profile;
	}
	public void setProfile(byte[] profile) {
		this.profile = profile;
	}
	
	public Date getRdate() {
		return rdate;
	}
	public void setRdate(Date rdate) {
		this.rdate = rdate;
	}
}
```

dsfdfsssdd


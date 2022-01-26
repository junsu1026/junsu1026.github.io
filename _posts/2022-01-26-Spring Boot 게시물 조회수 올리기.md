---
layout: post
title:  "Spring Boot 게시물 조회수 올리기"
date:   2022-01-25T14:25:52-05:00
author: Junsu Noh
categories: Dummy
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

## Spring Boot 게시물 조회수 올리기

게시물의 조회 수를 올리기 위해서 정리를 먼저 하고 넘어가는게 좋을 것 같아서 정리부터 해보자.



1@ VO만들기

2@ 게시물을 홈페이지 이용자가 클릭

3@ 클릭시 함수 실행

4@ 컨트롤러 -> 서비스 -> DAO순으로 함수 실행과 파라미터 이동

5@ DAO에서 sql문을 사용해 클릭 함수 실행 할 때 마다 조회수 컬럼의 값 1증가

6@ View에서 조회수라는 칸을 만들어서 조회수를 보여줌



간단하게 이렇게 단계별로 추려보고 만들기를 시작하는게 좋다. 개발하는 도중에 어디까지 했지라는 판단이 서기 쉽고 개발 시간이 단축될 수 있다. EX) Class Diagram, ERD etc...

다른 기능들을 같이 넣으면 좀 더 헷갈릴 듯 싶어서 딱 조회수만 올리는 기능을 구현만 하였다.





- **1** VO만들기 (setter 와 getter는 생략하겠다, 코드 양이 쓸데없이 길어진다.) 
- 데이터베이스 설계

```
public class Board {
	int no; //시퀀스
	String title; // 제목
	String contents; // 내용
	Member writer;//객체간의 관계는 참조로 구현한다.
	int views; //조회수
}
```

```
public class Member {
	int no;	//시퀀스
	  String name;
	  String id;
	  String password;
}
```

```
CREATE TABLE IF NOT EXISTS `db5`.`member` (
  `no` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) NOT NULL,
  `id` VARCHAR(45) NOT NULL,
  `password` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`no`))
ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `db5`.`board` (
  `no` INT NOT NULL AUTO_INCREMENT,
  `title` VARCHAR(45) NULL,
  `contents` VARCHAR(45) NULL,
  `writer` INT NOT NULL, 
  `views` INT not null default 0,
  PRIMARY KEY (`no`),
  INDEX `fk_board_member_idx` (`writer` ASC) VISIBLE,
  CONSTRAINT `fk_board_member`
    FOREIGN KEY (`writer`)            
    REFERENCES `db5`.`member` (`no`) 
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


```

두 테이블은 writer라는 키로 연결되어있고 

member테이블의 primary key인 no가 => board의 foreign key 이다. 

이렇게 두 테이블은 하나의 컬럼으로 연결되어 있다.



- **2** 게시물을 홈페이지 이용자가 클릭시 함수 실행 
- Board는 내가 만든 VO다. setter 와 getter를 통해 데이터를 저장하거나 얻어온다.

```
<% List<Board> boards= (List<Board>) request.getAttribute("boards"); %>
//Board라는 게시물을 리스트형으로 한번에 받아온다.
<body>
<!-- 게시물목록출력 -->
<h1>게시물 목록</h1>
<ul>
<% for(int 일련번호=0;일련번호<boards.size();일련번호++){ 
	//list에 저장되어 있는 게시물들을 하나씩 뽑아내는 작업
	Board 게시물 =boards.get(일련번호);//zero base
%>
    <li><%=일련번호+1 %>
    <a href="/board/<%=게시물.getNo() %>"> 
    <%= 게시물.getTitle() %> //클릭버튼
    </a>
    <%=게시물.getWriter().getName()%>  //게시물 등록자 이름
    <%=게시물.getViews() %> </li> // 조회수
<%} %>
</ul>
</body>
```

위 코드는 아래의 화면과 같이 나온다. 

<img src="https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/게시물목록창.PNG" alt="게시물목록창" style="zoom:50%;" />





- **3** 매핑을 통해 컨트롤러에서 View(jsp)에서 넘어오는 파라미터 값으로 함수를 서비스로 전달

```
@Autowired I게시물업무자 게시물업무자;
@GetMapping("/board/{게시물번호}")
	public void 게시물조회수를올리다(@PathVariable int 게시물번호) {
		Board 찾은게시물 = 게시물업무자.조회수증가하다(게시물번호);
	}
```



- **4** 넘어온 파라미터로 함수를 DAO로 전달

```
public void 게시물을조회하다And조회수증가하다(int 게시물번호) {
		//아직은 특별한 기능이 없기 때문에 파라미터 값만 전달시켜 준다.
		Board 게시물 = 게시물DAO.게시물을찾다And조회수증가하다(게시물번호);
	}
```



- **5** 받아온 파라미터를 사용해 해당하는 게시물의 views라는 조회수 컬럼의 값을 1증가시키고 그 게시물에 대한 정보를 받아와서 컨트롤러로 전달시켜준다.

```
public void 게시물을찾다And조회수증가하다(int 게시물번호) {
		Board 게시물 = null; 
		Connection con = null;		 
	    try{
	        Class.forName("com.mysql.cj.jdbc.Driver");
	        con=DriverManager.getConnection(DBConfig.DBURL, DBConfig.ID, DBConfig.PASSWORD);
	        PreparedStatement 명령자=con.prepareStatement("update board set views=views+1 where no=?");
	        명령자.setInt(1, 게시물번호);
	        명령자.executeUpdate();
	        명령자.close();
	        //게시물의 조회수를 올리는 sql 끝
	        }
	    }
		catch(Exception ex){ ex.printStackTrace(); }
	}
```



- **6** View(jsp)에서 조회수 칸을 만든 뒤 게시물 목록에서 클릭시 조회수가 올라가는지 체크

```
<% 
	Board board = (Board)request.getAttribute("board"); //VO를 받아온다.
%>
<body>
//받아온 VO안에 있는 조회수를 출력
조회수: ${board.views }<br>

//다른 기능들은 일부러 삭제하였다.
</body>
```

------



#### 조회수 기능만을 추가한다면 쉽게 할 수 있다. 

#### 하지만 다른 기능들을 같이 또는 한꺼번에 넣게 되면 헷갈릴 수 있는 부분이니 주의를 하면서 차근차근 하나씩 기능들을 추가해주는 것이 좋다.

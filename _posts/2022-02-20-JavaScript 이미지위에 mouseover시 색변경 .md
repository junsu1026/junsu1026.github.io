---
layout: post
title:  "JavaScript 이미지위 색변경"
date:   2022-02-18T14:12:52-05:00
author: Junsu Noh
categories: JavaScript
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## JavaScript 이미지위 색변경 방법



버튼을 클릭하거나 마우스오버 했을때 이미지의 색을 변경하고 싶었는데 자꾸 이미지의 원하는 부분의 색이 변경되지 않고 이미지 뒷부분이 색이 변경되는 바람에 정말 골치가 아팠다.

그래서 계속 찾아본 끝에 https://www.jqueryscript.net이라는 사이트에 올라와있는 글을 보고 사용해보았다.



https://www.jqueryscript.net/other/Simple-jQuery-Plugin-For-Highlighting-Image-Map-Maphilight.html

이 링크로 들어가면 데모버전을 사용해 볼 수 있는데 원하는 부분의 좌표값을 설정해주고 mouseover() 했을때 좌표값의 색이 변하는 것을 확인할 수 있다.



데모버전에 들어가면 이러한 그림이 나오는데 



![mouseover데모](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/mouseover데모.PNG)

```
$(function() {
        $('.map').maphilight();
        $('#squidheadlink').mouseover(function(e) {
            $('#squidhead').mouseover();
        }).mouseout(function(e) {
            $('#squidhead').mouseout();
        }).click(function(e) { e.preventDefault(); });
```



오징어 그림의 얼굴부분에 마우스를 올려도 색이 변하고 

mouse over this to triger a hilight from an external element! 를 눌러도 얼굴 색이 변하는 것을 확인할 수 있다.

화면 아무곳이나 우클릭해서 페이지 소스 보기를 하면 다음과 같은 코드를 볼 수 있는데



![mouseover페이지소스](https://raw.githubusercontent.com/junsu1026/junsu1026.github.io/images/assets/img/mouseover페이지소스.PNG)



이 소스의 javascript부분은 페이지에서 알려준대로 똑같이 되어있지만 

html 부분을 볼 수 있다. map태그의 area에 data-maphilight 사용시에 원하는 색깔로 투명도까지 조절할 수 있다.

정말정말 유용한 기능이므로 알아두면 한번쯤은 사용할 수 있는 기능이다.



```
<area id="squidhead" href="#" shape="circle" coords="290,102,30" alt="Octoface" data-maphilight='{"stroke":false,"fillColor":"ff0000","fillOpacity":0.6}' title="Metadata'd up a bit">

```

위 소스 코드 중 한개를 복사한 것이다.

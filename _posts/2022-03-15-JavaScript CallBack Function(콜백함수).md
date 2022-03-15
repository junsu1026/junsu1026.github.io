---
layout: post
title:  "JavaScript CallBack Function(콜백함수)"
date:   2022-03-14T14:12:52-05:00
author: Junsu Noh
categories: 백준
tags:	jekyll welcome
cover:  "/assets/instacode.png" 
---

## JavaScript CallBack Function(콜백함수)



자바스크립트의 CallBack Function(콜백함수)는 중요하게 다루어져야 할 것이고 이것을 완벽하게 이해한다면

자바스크립트를 사용할 때 어떤 것을 먼저 써야하는지 그리고 작업 속도도 상승될 것이다.

콜백 함수는 동기,비동기와 큰 관련이 있는데 콜백함수는 비동기라고 할 수 있다.

동기 비동기는 Stack, Queue와 큰 연관이 있다. 먼저 JavaScript의 처리방식을 알면된다.

우선 JavaScript는 함수들을 실행할때 Stack으로 전달이 되고 하나씩 차례대로 함수가 실행이 되는데

콜백함수들은 ajax,setTimeout() 등 여러 함수나 방법들이 있는데 콜백함수를 사용하게 되면 

콜백함수만 따로 Stack에서 차례대로 실행되지 않고 Queue에 하나씩 쌓이게 된다. 그리고 Stack의 함수들이 

모두 다 실행되고 나면 그 때 Queue에 있는 콜백함수들을 하나씩 다시 처리하게 된다.

이 때 발생할 수 있는 오류들이 있는데, 

먼저 콜백함수들을 너무 많이 사용하게 되면 Queue에 과부하가 

걸리게 되면서 피라미드처럼 쌓인 콜백함수들이 오작동을 일으킬 수 있다. 그리고

대기상태에서 존재하고 클라이언트가 기다렸다가 해야하는 함수들이 너무 많을 경우 Queue에서 너무 오래 

기다린 Callback함수들이 에러를 일으킬 수 있다.



콜백함수의 예)

```
// #1
console.log('Hello');
// #2
setTimeout(function() {
	console.log('Bye');
}, 3000);
// #3
console.log('Hello Again');
```

결과 : Hello Hello Again Bye



결과의 순서에 집중할 필요가 있는데, setTimeout의 시간을 3000으로 조정했기 때문에 늦게 나온것이라고 생각

될 수 있다. 하지만 그렇지 않고 타임을 0으로 조정해도 Bye가 가장 마지막에 나오게 된다.

그 이유는 위에서 설명한 비동기처리 방식 때문이다. 그래서 비동기 방식의 함수가 너무 많게되면 모두 다 

Queue로 이동하고 그 이후에 함수들이 처리되기 때문에 문제를 일으킬 수 있으므로 주의하면서 사용해야한다.

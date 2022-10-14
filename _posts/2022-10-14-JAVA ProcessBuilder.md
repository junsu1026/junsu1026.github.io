---
layout: post
title:  "JAVA ProcessBuilder"
date:   2022-10-13T14:25:52-05:00
author: Junsu Noh
categories: JAVA
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---



ProcessBuilder를 사용할 때 환경변수를 System.getenv()로 출력하게 되면 (linux 환경) 

PATH/usr/bin:/bin:/usr/sbin:/sbin

SHELL/bin/bash

위와 같은 기본적인 변수 항목만 나오게 된다

그래서 command를 사용하게 될 경우 서버의 환경변수와 동일하게 맞춰줘야 할 필요가 있다.

processBuilder.environment().put(”key”, “value”)

위의 key 값에 PATH를 입력해주고 value에는 리눅스에서 echo $PATH를 치면 나오는 환경변수 전체를 넣어주면 자바가 ProcessBuilder를 실행시킬 때 해당 환경변수를 적용 시킨 체로 실행 해준다.

단,  processBuilder.start(); 전에 환경변수를 맞춰줘야 한다.

String[] cmd = {"sh","-c","digdag run /usr/local/tomcat8/mydag/digdag_test.dig --rerun --project /usr/local/tomcat8/mydag"};

실행명령어를 String  배열로 

ProcessBuilder processBuilder = new ProcessBuilder(cmd);

생성할 때 생성자에 넣어주면 된다. 

그리고 프로세스가 끝날때까지 기다려줘야 하기 때문에

process.waitFor(); 함수를 사용해준다. 해당 함수를 사용하게 되면 결과 값으로 int가 나오게 되는데

해당 숫자로 어떤 에러인지 알 수 있다.

그리고 process.exitValue()를 사용해서도 어떠한 에러인지 파악 할 수 있다. 

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		response.setContentType("text/html;charaset=utf-8");
		PrintWriter out = response.getWriter();

		//로그파일 기록
		String logfile = "/usr/local/tomcat8/test.log";		
		ArrayList<String> al = new ArrayList<String>();		
		LocalTime now = LocalTime.now();
		 DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH시 mm분 ss초");
		 String formatedNow = now.format(formatter);
		 al.add("/////////////////////////");
		al.add(now.format(formatter));	
		Log log = new Log();

		
		try {
            // Run script
			String[] cmd = {"sh","-c","digdag run /usr/local/tomcat8/mydag/digdag_test.dig --rerun --project /usr/local/tomcat8/mydag"};
//명령어
			
			ProcessBuilder processBuilder = new ProcessBuilder(cmd);
//경로설정            processBuilder.directory(new File("/usr/local/tomcat8"));
			processBuilder.environment().put("PATH", "/root/bin:/root/bin:/root/.embulk/bin:/root/bin:/root/.embulk/bin:/root/bin:/root/bin:/root/.embulk/bin:/root/bin:/root/.embulk/bin:/usr/local/rvm/gems/ruby-3.0.0/bin:/usr/local/rvm/gems/ruby-3.0.0@global/bin:/usr/local/rvm/rubies/ruby-3.0.0/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/rvm/bin:/var/lib/snapd/snap/bin:/usr/local/lib/jdk1.8.0_341/bin:/usr/local/lib/jdk1.8.0_341/jre/bin:/usr/local/lib/bin:/root/bin");
			Process process = processBuilder.start();
            
            
            // Read output
            StringBuilder output = new StringBuilder();
            BufferedReader reader = new BufferedReader(
                    new InputStreamReader(process.getInputStream()));

            String line = null;
            while ((line = reader.readLine()) != null) {
                output.append(line);
            }
            al.add(output.toString());
            out.println("____________________"+System.getenv()+"_________________");
            out.print(output.toString());
            int exitCode = process.waitFor();
            String errMsg = null;
            out.print( "ExitCode : " + process.exitValue() );
            if(process.exitValue() == 1){
                errMsg = "광범위한 일반적 에러";
            }else if(process.exitValue() == 126){
                errMsg = "실행불가능. 퍼미션문제 혹은 실행허가 없음";
            }else if(process.exitValue() == 127){
                errMsg = "command not found";
            }
            out.println("에러내용 = "+process.getErrorStream()+"     " );
            
            out.println("\nExited with error code : " + exitCode);
            out.println(errMsg);
        } catch (Exception e) {
        	
            e.printStackTrace();
        } 
		
		out.close();
		
		
		log.makeFile(logfile,al,true);
}
```

[https://www.baeldung.com/java-lang-processbuilder-api](https://www.baeldung.com/java-lang-processbuilder-api)

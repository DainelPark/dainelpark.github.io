---
layout: post
title:  "Android Studio에서 Lombok 사용하기"
date:   2015-12-25 21:15:00
summary: Lombok을 이용한 빠른 Getter/Setter 생성
categories: Android, 코딩
---

이전에 해커톤에서 Spring-Boot를 하면서 Lombok 플러그인을 알게 되었는데 꽤 유용했던 걸로 기억한다. 그래서 이번에 처음부터 다시 하는 Android Project에서도 적용해보기로 했다.

[Maven Repo](https://projectlombok.org/mavenrepo/)로 들어가면 어떻게 세팅하는 지 알 수 있다. Android Project에서 app.gradle에 다음과 같이 추가하면 된다.

	provided "org.projectlombok:lombok:1.16.6"
	
그러면 Lombok이 추가되어 사용할 수 있게 된다.


	import lombok.Data;

	/**
 	* Created by eunhwanpark on 15. 12. 25..
 	*/

	@Data
	public class Test {
    	private String name;
	}

다음과 같이 코드를 작성하면 Getter/Setter가 자동으로 Processing하여 생성된다.

	Test test = new Test();
	test.setName("name");
	
문법적으로 문제의 여지는 없다. NullPointerException이 발생할 이유도 없다. 하지만 실행이 되지 않는다. **Package javax.annotation Does Not Exist**라는 로그와 함께 실행이 되지 않는데 해결 방법은 간단하다. app.gradle에 다음과 같이 추가해주면 된다.

    provided 'org.glassfish:javax.annotation:10.0-b28'

Lombok이 1.16으로 업데이트되면서 생긴 문제라고는 하는데 자세히는 모르겠다. 하지만 어찌됬건 Annotation을 이용해 코딩 량을 줄이고 퀄리티도 높일 수 있었다. Class만 만들어두면 알아서 다 해주는데 개발자 입장에서는 굉장히 유용한 것같다.

PS1. Intellij IDEA에서는 Plugin에서 Lombok을 검색하여 설치해주기만 된다.

PS2. 설치는 당연히 하는 것이고, Enable Annotation Processing을 체크해야 된다. 안 그러면 안된다.
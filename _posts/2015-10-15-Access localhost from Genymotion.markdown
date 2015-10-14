---
layout: post
title:  "Access localhost from Genymotion"
date:   2015-10-15 06:49:00
categories: Android
---

Android Client를 개발하던 도중, Genymotion에서 localhost에서 돌리던 Tomcat 서버에 접근해야할 일이 생겼다. 아직 서버가 Deploy되지 않아서 localhost에서 테스트하고 있었기 때문이다.

	# Constants.java
	
	public static final String ENDPOINT = "http://localhost:8080";
	
위와 같은 코드로 세팅하고서 HTTP Request를 날려보았는데 **java.net.ConnectException: failed to connect to localhost/127.0.0.1 (port 8080): connect failed: ECONNREFUSED (Connection refused)**이라는 로그가 발생하였다. 서버는 동작 중이었기 때문에 적어도 이런 로그는 발생하면 안된다고 생각하였다. 그래서 localhost의 주소가 다를 것이라고 생각했다.

좀 알아보니 Genymotion은 VirtualBox를 이용해서 컴퓨터의 localhost와 달랐다.

**ifconfig virtual_machine**을 통하여 Genymotion의 IP 주소를 직접 알아낸다. 또는 **10.0.3.2**로 IP 주소를 지정해준다. Wi-Fi의  DHCP 주소가 **10.0.3.2**이기 때문이다.

이렇게 수정하면 잘 동작한다. 

**#다만 #맥북_에어 #램 4기가 #이클립스+톰캣+안드로이드_스튜디오+지니모션은 #=이륙**
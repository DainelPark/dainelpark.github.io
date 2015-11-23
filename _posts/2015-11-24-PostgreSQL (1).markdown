---
layout: post
title:  "PostgreSQL (1)"
date:   2015-11-24 03:46:00
categories: postgresql
---

**이 글은 PostgreSQL의 공식 문서를 보면서 제 방식대로 정리한 글입니다. 혹시 문제가 있다면 Pull Request를 부탁드립니다. 원문 글은 [PostgreSQL Documentaion](http://www.postgresql.org/docs/9.4/static/)입니다.**

##1.1 Installation

PostgreSQL을 사용하시기 전에 당연하게도 설치를 해야합니다. 이는 사용하시는 OS 별로 나누어집니다. 필자는 **homebrew**를 이용하여 설치하였습니다. 

##1.2 Architectural Fundamentals

PostgreSQL을 이용하여 무언가를 하기 이전에, 기본적인 PostgreSQL 시스템의 구조를 이해해야만 합니다. 

PostgreSQL은 client/server 모델을 사용합니다. PostgreSQL의 세션은 다음과 같은 협력 프로세스를 통하여 이루어집니다.

- 데이터베이스 파일을 관리하는 서버 프로세스는 Client 앱과 데이터베이스 간의 연결을 받아들입니다. 그리고 Client를 대신하여 데이터베이스에 특정 역할을 수행합니다. 데이터베이스 서버 프로그램을 postgres라고 부릅니다.

- 사용자의 Client (프론트-엔드) 앱은 데이터베이스에 명령을 내려 수행하길 원합니다. 프론트-엔드는 환경에 따라 다양해질 수 있습니다. -> 프론트-엔드는 텍스트 중심의 도구가 될 수도, 그래픽 앱이 될 수도, 데이터베이스에 접근하여 정보를 웹 페이지에 담아 보여주는 웹 서버가 될 수도, 또는 특별한 목적을 가지고 있는 데이터베이스 관리 도구가 될 수 있습니다. 몇 몇 프론트-엔드는 대부분 사용자가 개발한 PostgreSQL의 배포판을 사용합니다.

Client/Server 모델에 따라 Client와 Server는 호스트가 다를 수 있습니다. 이러한 경우 **TCP/IP**를 이용하여 소통(Communicate)합니다. 사용자는 이 점을 꼭 명심해야합니다. 왜냐 하면, Client Machine에서 접근할 수 있는 파일은 Database Server Machine애서 접근할 수 없도록 해야합니다.

PostgreSQL Server는 Client로부터 여러 개의 연결을 조정(Handle)할 수 있습니다. 이를 위해 **forks**라는 새로운 프로세스를 각각의 연결마다 실행합니다. 그 시점부터, Client와 그 새로운 서버 프로세스는 Original Postgres Process의 간섭없이 소통할 수 있습니다. 앞서 말한 것과 같이, 마스터 서버는 항상 실행 상태이며, Client Connection을 기다리지만, Client와 Associated Server(마스터 서버와 그 외를 포함한 문구인 듯합니다.)는 주고 받습니다.

##1.3 Creating a Database

데이터베이스 서버에 접근할 수 있는지 테스트하기 위해서는 데이버베이스를 생성해야합니다. 백 그라운드에서 실행되고 있는 PostgreSQL Server는 많은 데이터베이스를 관리합니다. 전형적으로, 분리된 여러 데이터베이스의 경우 각각의 사용자 별 또는 각각의 프로젝트 별로 사용됩니다.

`mypostgredb`라는 새로운 데이터베이스를 생성하기 위해서 다음과 같은 명령어를 사용합니다.

	$ createdb mydb
	
만약 아무런 응답이 없다면 성공적으로 생성된 것입니다. 만약 **command not found**가 뜬다면 PostgreSQL이 설치된 폴더의 bin으로 들어가셔서 하시면 됩니다.

	createdb: could not connect to database postgres: could not connect to server: No such file or directory
        Is the server running locally and accepting
        connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
        
위와 같은 응답이 떴다면 아직 PostgreSQL Server를 시작하지 않은 것입니다. 시작하고서 다시 실행해주세요.

만약, 생성한 `mypostgredb`를 삭제하고 싶다면 다음과 같은 명령어를 입력하면 됩니다.

	$ dropdb mypostgredb
	
##1.4 Accessing a Database

이전에 데이터베이스를 생성한 적이 있다면, 다음과 같은 방법을 통하여 접근할 수 있습니다.
	
- Running the PostgreSQL interactive terminal program, **psql**이라고 부르며 interactively하게 입력하고, 수정하며, SQL 명령어를 실행할 수 있습니다.

`psql`을 이용하여 생성했던 데이터베이스에 접근해보겠습니다.

	$ psql mypostgredb
	
만약 데이터베이스 이름을 입력하지 않았을 때는, 사용자의 기본 계정 이름으로 될 것이다.

`psql`에서, 우리는 다음과 같은 메시지를 볼 수 있습니다.

	psql (9.3.5)
	Type "help" for help.

	mypostgredb=#
	
저것은 우리가 데이터베이스의 **최고권한(superuser)**을 얻었다는 것을 의미합니다. 최고권한을 얻는 것은 우리가 제어하는데 아무런 제한을 받지 않는 것을 의미합니다. 이 튜토리얼에서는 그렇게 중요한 것이 아닙니다

`psql`의 마지막 줄에 나온 것은 프롬프트입니다. 그리고 이를 통해 SQL 쿼리 명령어를 사용할 수 있습니다.

	mypostgredb=# select version();
                                                              	version                                                              
 	PostgreSQL 9.3.5 on x86_64-apple-darwin14.0.0, compiled by Apple LLVM version 6.0 (clang-600.0.54) (based on LLVM 3.5svn), 64-bit
 	(1 row)

	mypostgredb=# 

`psql`은 SQL 명령어가 아닌 몇 몇의 내부 명령어가 존재한다. 이 명령어들은 `\`로 시작합니다. 예를 들어, PostgreSQL의 문법에 대한 도움을 얻고 싶을 때 다음과 같이 하면 됩니다.

	mypostgredb=#\h
	
`psql`에서 나오고 싶다면 다음과 같이 명령어를 치면 됩니다.
	
	mypostgredb=#\q
	
또 다른 내부 명령어를 알고 싶을 땐 `\?`를 치시면 됩니다. 많은 도움이 될 것입니다.
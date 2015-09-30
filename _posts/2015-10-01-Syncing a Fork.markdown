---
layout: post
title:  "Syncing a fork"
date:   2015-10-01 05:31:00
categories: Git
---

최근에 개인 저장소에 만든 Repository를 조직 저장소로 Fork할 일이 생겼다. 처음에는 개인 저장소의 Repo에 계속 Push를 해서 조직 저장소의 Repo와 Sync를 하려고 했는데, 그냥 나중에는 조직 저장소에 직접 Push했다.


	$ git remote -v
	# List the current remotes
	origin  https://user@bitbucket.org/user/repo.git (fetch)
	origin  https://user@bitbucket.org/user/repo.git (push)
	
터미널에서 `git remote -v`를 치면 위와 같이 나온다. 이제 새로운 remote를 만들어보자.

	$ git remote add upstream https://user@bitbucket.org/organization/repo.git
	
이제 새로운 remote가 만들어졌다. 다시 `git remote -v`를 쳐보자.

	$ git remote -v
	# List the current remotes
	origin  https://user@bitbucket.org/user/repo.git (fetch)
	origin  https://user@bitbucket.org/user/repo.git (push)
	upstream https://user@bitbucket.org/organization/repo.git (fetch)
	upstream https://user@bitbucket.org/organization/repo.git (push)
	
위와 같이 upstream이 생겼다. 이제 `fetch` 명령어를 이용하여 Sync를 해보자.

	$ git fetch upstream
	Already-Up-To-Date가 뜨기도 하고
	* [new branch] master -> upstream/master 이런게 뜨기도 한다.

이제 `checkout`와 `merge` 명령어를 써보자.

	$ git checkout master
	$ git merge upstream/master
	
이러면 Sync가 된다. 그런데 이럴 필요가 굳이 없었다. 처음부터 그냥 Fork한 조직 저장소의 Repo에 Push하면 됬었는데 왜 이렇게 했는지 모르겠다.

	$ git add *
	$ git commit -m "commit message"
	$ git push upstream master
	
새로운 remote를 생성한 후, 위와 같이 명령어를 치면 Fork한 조직 저장소의 Repo에 잘 기록된다.
	
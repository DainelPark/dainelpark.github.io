---
layout: post
title:  "Elasticsearch 사용해보기 (1)"
date:   2016-07-17
---

# 1. 왜 사용하는가?

아무래도 로그 분석 시스템을 개발하는데 ElasticSearch 라는 것을 사용할 것 같다. **'로그 분석 시스템을 개발하고 싶다'**라는 생각을 하고는 있어서 재밌을 것 같다.

# 2. ElasticSearch 설치하기

우선 ElasticSearch를 설치해야하는데 다음과 같이 하면 된다.

	curl -L -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.4/elasticsearch-2.3.4.tar.gz

이러면 elasticsearch-2.3.4.tar.gz를 받게 된다.

> 1. 최소한 JAVA 7 이상이 있어야 한다.
> 
> 2. brew로 설치하지 말라. 버전이 2.2다.

	tar -xvzf elasticsearch-2.3.4.tar.gz
	cd elasticsearch-2.3.4/bin
	./elasticsearch
	
> 만약 Node와 Cluster의 이름을 지정하고 싶다면 `elasticsearch --cluster.name cluster_name --node.name node_name`을 하면 된다.

# 3. ElasticSearch API 사용해보기

ElasticSearch의 Getting Started에서는 Cluster의 Health를 확인하는 것을 시작으로 한다. curl을 이용하여 안내하고 있으며 개발자가 직접 HTTP/REST API를 작성하여 호출할 수도 있다.

> 사실 curl이 무난하잖아.
> 
> 말 그대로 Getting Started이니 command shell window에서 안내하는 것 같다. 그러니 터미널을 키자.

Cluster Health를 사용하기 위하여 [_cat API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)를 사용할 것이다. 

> 기억하자. ElasticSearch가 돌아가는 포트는 기본적으로 9200이다.

	~ -> curl localhost:9200/_cat/health?v
	
을 하면 아래와 같은 상태가 출력된다.

	epoch      timestamp cluster status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
	1468753782 20:09:42  jude    green           1         1      0   0    0    0        0             0                  -                100.0%
	
Jude라는 이름의 Cluster가 돌아가는 것을 확인할 수 있다. node.total, status 같은 것이 나오는 것을 보니 말 그대로 기본적인 정보를 보여주는 것 같다. Status가 Green이면 잘 동작하고 있다는 것을 의미하는 것 같다.

문서를 보니 Status는 Green, Yellow, Red로 나누어지며 Green의 경우 모든 것이 정상 동작하고 있다는 것을 의미하며 Yellow의 경우 모든 데이터가 접근 가능하며, 몇 몇 레플리카가 할당되지 않은 것을 의미한다. Red는 몇 몇 데이터가 어떤 이유로 인하여 접근 불가능한 것을 의미한다. ASAP로 고쳐야하는 상황을 의미하는 것이다.

> 신호등의 느낌이다. 인간적이구만.

Cluster의 Health를 보았으니 Nodes의 리스트를 가져와보자.

	~ -> curl localhost:9200/_cat/node?v
	host      ip        heap.percent ram.percent load node.role master name
	127.0.0.1 127.0.0.1            3          98 1.60 d         *      1
	
Jude라는 Cluster의 1이라는 이름의 Node가 존재하는 것을 알 수 있다.

이제 Node의 리스트를 불러오는 것 처럼 Index의 리스트를 가져와보자.

	~ -> curl -XPUT localhost:9200/indices?v
	health status index    pri rep docs.count docs.deleted store.size pri.store.size
	
아마 이렇게 나올텐데 아직 생성산 Index가 없다는 뜻이다. 그렇다면 Index를 생성해보자.

	~ -> curl -XPUT localhost:9200/customer?pretty

이 명령어를 통하여 customer라는 이름의 Index를 생성하였다. 

> ElasticSearch에서는 pretty-print the JSON response를 위하여 뒤에 `pretty`를 붙인다.

Index를 생성하였다면 다음과 같은 JSON을 볼 수 있다.

	{
  		"acknowledged" : true
  	}
  	
그 후에 다시 Index 리스트를 불러오는 API를 사용해보면 다음과 같은 결과를 볼 수 있다.

	health status index    pri rep docs.count docs.deleted store.size pri.store.size
	yellow open   customer   5   1          0            0       650b           650b
	
	
이 결과는 우리가 이제 customer라고 이름지은 하나의 Index를 생성하였다는 것과 5개의 Primary Shards, 그리고 1개의 Replica를 가지고 있다는 것을 의미한다. 또한 이 안의 Documents가 아예 없다는 것을 의미한다.

> 눈치빠르신 분들이라면 Documents를 보고 뭔가 직감적으로 느끼는 것이 있을 것이다.

이제 Index도 생성했으니 customer의 JSON Documents를 넣어보자.

	~ -> curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
	{
  		"name": "Jude Park"
	}'
	
1은 id를 의미하며 external은 type을 의미한다. 

> ElasticSearch에서는 Type이 필수적으로 들어가야한다고 한다.

어찌됬건 결과는 이렇게 나온다.

	{
  		"_index" : "customer",
  		"_type" : "external",
  		"_id" : "1",
  		"_version" : 1,
  		"created" : true
	}
	
이쁘게 잘 나온다. 이제 Id를 이용하여 데이터를 불러와보자.

	~ -> curl -XGET localhost:9200/customer/external/1?pretty
	{
  		"_index" : "customer",
  		"_type" : "external",
  		"_id" : "1",
  		"_version" : 1,
  		"found" : true,
  		"_source" : {
    		"name" : "Jude Park"
  		}
	}
	
_source에 담겨서 나오는 것을 확인할 수 있다.

# 4. 글을 마치며

이것으로 전부는 아니지만 기본적인 ElasticSearch의 사용법을 익혔다고 생각한다. JSON Documents를 이용하여 데이터를 다룬다는 점은 유연함을 주는 느낌을 받았고 좋았다고 생각한다. 이왕 하는 김에 잘 익혀두면 쓸모가 여러모로 많을 것 같다.
---
layout: post
title:  "PostgreSQL (2)"
date:   2015-12-03 20:20:00
categories: postgresql
---

**이 글은 PostgreSQL의 공식 문서를 보면서 제 방식대로 정리한 글입니다. 혹시 문제가 있다면 Pull Request를 부탁드립니다. 원문 글은 [PostgreSQL Documentation](http://www.postgresql.org/docs/9.4/static/)입니다.**

##2.1 SQL Language Introduction

이번 장에서는 간단한 명령어들을 이용하여 어떻게 SQL을 사용하는지 **Overview**를 보여줄 것입니다.

##2.2 Concepts

PostgreSQL은 관계형 데이터베이스(Relational Database Management System)입니다. 관계는 *Table*에 사용되는 수학적인 용어입니다. Table에 데이터를 보관하는 개념은 오늘날 매우 흔합니다. 하지만 데이터베이스를 조직화하는 방법에는 여러 가지가 있습니다. 

각 Table은 *rows*라는 것의 모음입니다. 주어진 Table에 각각의 row는 *columns*라는 이름을 가지고 있습니다. 그리고 이 각각의 column은 특정한 데이터 타입을 가지고 있습니다. columns은 각 row의 고정된 순서가있는 반면, SQL은 table에 있는 rows의 순서를 보장하지 않습니다.

Table은 데이터베이스로 그룹되어 들어가고 데이터베이스의 모음은 Single PostgreSQL Server에 의하여 관리됩니다.

##2.3 Creating a New Table

이제 Table의 이름을 짓고 생성할 차례입니다. 아래는 예시입니다.

	create table weather(
		city	varchar(80),
		temp_lo	int,
		temp_hi	int,
		prcp	real,
		date	date
	);
	
	-- comment 를 하시면 주석을 넣을 수 있습니다.
	
psql을 이용하여 저렇게 할 수 있습니다. psql은 세미콜론(;)이 입력되기 전까지 계속 엔터가 됩니다.

빈 칸(i.e., spaces, tabs, and newlines)은 자유롭게 SQL 명령에서 사용될 수 있습니다. 이를 통해 위와 같이 좀 더 깔끔한 코드를 작성할 수 있겠지요.

*varchar(80)*은 80자의 문자열을 보관하는 데이터 타입입니다. *int*는 평범한 *integer type*입니다. *real*은 부동소수점을 보관하기 위한 데이터 타입입니다. *date*는 설명이 필요없겠지요?

PostgreSQL은 표준 SQL 타입인 *int*, *smallint(전 안 써봤습니다...)*, *real*, *double precision*, *char(N)*, *varchar(N)*, *date*, *time*, *timestamp* 그리고 *interval*을 지원합니다. 뿐만 아니라 지도 좌표를 보관해주는 데이터 타입 또한 존재합니다. PostgreSQL은 임의의 유저 데이터 타입으로 커스터마이징될 수 있습니다. 따라서 문법에서, 타입의 이름들은 키워드가 아닙니다. SQL 표준의 케이스를 제외하구요.

두 번째 예제는 도시와 관련된 좌표를 보관하는 예제입니다.

	create table cities (
		name	varchar(80),
		location	point
	)

*point* 타입은 PostgreSQL만의 데이터 타입입니다.

마지막으로 더 이상 해당 테이블이 필요없다면 다음과 같은 명령어를 해주세요.

	drop table tablename;
	
##2.4 Populating a Table With Rows

*INSERT* 구문은 데이터를 넣을 때 사용된다.

	insert into weather values ('San Francisco', 46, 50, 0.25, '1994-11-27');
	
모든 데이터 타입은 분명한 입력 형식을 사용합니다. 위의 예시에서와 같이 일반적으로 작은 따옴표(')로 묶어야 합니다.

*point* 타입은 한 쌍의 입력 값을 요구합니다. 아래와 같습니다.

	insert into cities values ('San Francisco', '(-194.0, 53.0)');
	
지금까지는 rows의 값이 무엇인지 명시하지 않았습니다. 아무래도 명시하는 것이 보는 사람들에게도 명확하고 좋을 것이라고 생각합니다.

	insert into weather (city, temp_lo, temp_hi, prcp, date)
    values ('San Francisco', 43, 57, 0.0, '1994-11-29');
    
물론 (city, temp_lo, temp_hi, prcp, date)에서, 예를 들어 prcp는 넣기 싫다면 빼도 됩니다. 그렇다면 null이 되겠죠.
    
##2.5 Querying a Table

Table에서 데이터를 가지고 오기 위해서는 테이블은 쿼리(*queried*)되어야한다. SQL의 *SELECT* 구문은 이를 위해 사용된다.

*weather* 테이블의 모든 rows를 불러오기 위해선 다음과 같이 하면 된다.

	select * from weather;
	
여기서 *이 의미하는 것은 **모든 columns**라는 것이다. 

그리고 **expressions**를 작성할 수 있다. 다음과 같이 할 수 있다.

	select city, (temp_hi+temp_lo)/2 AS temp_avg, date from weather;
	
위와 같은 명령어를 치면 temp_avg에 (temp_hi+temp_lo)/2한 값이 저장된다.

*AND*와 *OR*, *NOT*과 같은 Boolean형의 연산자들이 있지만 생략하도록 하겠다. 그리고 *ORDER BY*를 사용하면 정렬할 수 있다. *DINSTNCT*는 쿼리 결과에서 중복된 rows를 없애는 역할을 한다.

##2.6 Joins Between Tables

지금까지 우리가 하는 쿼리들은 한 번에 하나의 테이블에만 접근하였습니다. 쿼리는 한 번에 여러 개의 테이블에 접근이 가능하고, 테이블의 여러 개의 row들에 접근할 수도 있다. 동일한 태이블 또는 다른 테이블의 여러 row들에 한 번에 접근하는 쿼리는 *join* 쿼리라고 부른다. 예를 들어, 관련된 도시의 위치와 모든 날씨를 가져오고 싶다면 우리는 *weather* 테이블의 각각의 *city* column와 *cities* 테이블의 *name* column의 모든 rows들을 비교해볼 필요가 있다. 그리고는 값이 매치되는 것을 찾는다.

	select * from weather, cities where city = name;
	
	     city      | temp_lo | temp_hi | prcp |    date    |     name      | location
	---------------+---------+---------+------+------------+---------------+-----------
 	San Francisco |      46 |      50 | 0.25 | 1994-11-27 | San Francisco | (-194,53)
 	San Francisco |      43 |      57 |    0 | 1994-11-29 | San Francisco | (-194,53)
	(2 rows)
	
결과 셋을 통하여 두 가지를 생각해볼 수 있다.

- row에는 Hayward라는 도시 이름이 없다. 왜냐 하면 *cities* 테이블에 매칭되지 않았기 때문이다. 그렇기 때문에 *join*은 *weather* 테이블의 매칭되지 않는 rows들을 무시한다.
- 도시의 이름을 가지고 있는 2개의 columns가 있다. 왜냐 하면 *weather*와 *cities*는 연결되어있기 때문이다. 아마 출력 row를 나열하는 것보다 명시 적으로 사용하기를 원할 것입니다. 아래와 같이 하면 됩니다.

		select city, temp_lo, temp_hi, prcp, date, location from weather, cities where city = name;
		
좀 더 명시적으로 할 땐 이런 식으로 하면 좋습니다. 개인적으론 사실  이런 방식이 더 좋다고 생각합니다.

위의 방식은 *Inner Join*이라고 합니다.

	select * from weather inner join cities ON (weather.city = cities.name);
	
둘 다 동일한 쿼리입니다. 문법적으로 보았을 때 위의 쿼리는 사실 적절하지 못하지만 이해하기위해서 보여드리는 쿼리입니다.

*Inner Join*은 일치하지 않는 row는 반환하지 않았습니다. *Outer Join*은 일치하지 않는 row 또한 반환합니다. 그 앞에 *Left*라던지, *Right*을 붙일 수 있습니다. *Left*를 사용할 경우 왼쪽 테이블을 오른쪽 테이블에 비교합니다.. *One-to-Many Relationship*에 유용한 쿼리입니다.

##2.7 Aggregate Functions

그룹 함수라고 직역할 수 있는 *Aggregate Functions*이다. 다른 관계형 데이터베이스와 동일하게 PostgreSQL은 그룹 함수를 지원합니다. 그룹 함수는 여러 입력 row들 중에서 하나의 결과를 도출해냅니다. 예를 들어 *count, sum, avg, max, min*을 통하여 row의 총합, 평균 등을 구할 수 있습니다.

	select max(temp_lo) from weather;

	max
	----
	46
	(1 row)
	
그룹 함수는 *where*에서 사용할 수 없습니다. 그렇기 때문에 `select city from weather where temp_lo = max(temp_lo);`와 같은 쿼리문은 동작하지 않습니다.

그룹 함수는 *Group By*와 함께 쓰일 때 매우 유용합니다. 예를 들어  각 도시에서 관찰된 최대 낮은 온도를 얻을 수 있습니다.

	select city, max(temp_lo) from weather group by city;
	
*Having*을 이용하면 *Group By*에 조건을 넣을 수 있습니다. 집합할 때를 잘 생각하면 될 것같습니다.

##2.8 Updates

이미 존재하는 row의 정보를 *Update*를 이용하여 수정할 수 있습니다.

	update weather set temp_hi = temp_hi - 2,  temp_lo = temp_lo - 2 where date > '1994-11-28';

위와 같은 쿼리문을 수행하면 수정됩니다.

##2.9 Deletions

데이터를 저장한다면 당연히 삭제할 수도 있어야합니다. 특정 row를 지운다고 해봅시다.

	delete from weather where city = 'Hayward';
	
그 후, `select * from weather;`로 삭제된 것을 확인하면 된다.

때로는 테이블 자체를 삭제하고 싶을 수 있습니다.

	delete from weather;
	
이러면 *weather* 테이블이 가지고 있는 모든 정보가 삭제된다.
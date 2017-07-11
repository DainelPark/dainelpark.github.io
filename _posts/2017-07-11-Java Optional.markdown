---
layout: post
title:  "Java 8의 Optional 사용해보기"
date:   2017-07-11
---

### 0. 개요

이번에 외주를 진행하면서 오랜만에 클라이언트 개발을 하게 되었다. 이 글을 작성하고 있는 시점에서 Kotlin 은 합리적인 유행을 이끌어내고 있다. 그 이유 중 하나는 ***Null-Safety*** 라는 점이었다.

자바 언어를 사용하는 많은 개발자 분들께서 겪는 에러 중 하나인 NullPointerException(이하, NPE) 에 대한 문제를 다소 해결할 수 있는 모양이다.

> Null Reference 를 만든 Tony Hoare 이것에 대하여 자신의 billion-dollar mistake 라고 언급한 바 있다.

> [The Billion Dollar Mistake : wikipedia](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)

이번 일은 기간이 짧기 때문에 Kotlin 을 배워서 할 시간을 없을 것 같아 결국 Java 8의 Optional 을 사용하기로 결정하였다.

Optional 을 사용하면서 얻을 수 있는 장점은 다음과 같다.

1. null 의 확인은 더 이상 필요하지 않다.
2. run-time 에서의 NPE는 더 이상 발생하지 않는다. (진짜?)
3. 조금 더 깨끗한 코드의 작성이 가능해진다.

### 1. 구조

Optional 객체의 아이디어는 레퍼런스를 한 번 감싸는 객체를 만들어 Null 체크를 내부로 숨긴 것이다. 즉, 이 객체를 사용하는 외부의 코드에서는 Null 체크를 할 필요가 없어진 것이다. 근본적으론 여전히 Null 체크를 하는 것이다.

```{.java}
	public final class Optional<T> {
		/**
     	* Common instance for {@code empty()}.
     	*/
    	private static final Optional<?> EMPTY = new Optional<>();

    	/**
     	* If non-null, the value; if null, indicates no value is present
     	*/
    	private final T value;

    	/**
     	* Constructs an empty instance.
     	*
     	* @implNote Generally only one empty instance, {@link Optional#EMPTY},
     	* should exist per VM.
     	*/
    	private Optional() {
        	this.value = null;
    	}
    	
    	/**
     	* If a value is present in this {@code Optional}, returns the value,
     	* otherwise throws {@code NoSuchElementException}.
     	*
     	* @return the non-null value held by this {@code Optional}
     	* @throws NoSuchElementException if there is no value present
     	*
     	* @see Optional#isPresent()
     	*/
    	public T get() {
        	if (value == null) {
            	throw new NoSuchElementException("No value present");
        	}
        	return value;
    	}
    	...
	}
```

Optional 객체의 `get()`에서 볼 수 있듯이  null 체크를 내부적으로 하고 있다.

### 2. 코드

기본적인 사용 방법은 다음과 같다.

```{.java}
	Optional<String> hello = Optional.of("Hello, World!");
	System.out.println(hello);
	
	Run -> Optional[Hello, World!]
```
	
실행 결과로는 Optional 객체가 Hello, World! 를 Wrapped 한 채로 나온다. 결과값을 그대로 가져오기 위해서는 `get()` 을 사용하면 된다.

위의 예제에서는 `of()` 를 사용했지만 `ofNullable()` 을 사용하는 것이 더 유연한 선택일지도 모르겠다. 그 이유로는 전자의 경우 null 일 경우 NPE 를 발생시킨다. 하지만 후자를 사용할 경우 `Optional.empty`를 반환한다. 

```{.java}
	Optional.of(null);
	Optional.ofNullable(null);
```
	
위 코드를 실행해보면 각각의 차이가 분명이 나타난다.

이제 우리는 `Optional.empty` 일 때를 다루어야한다. Optional 객체에서는 `ifPresent(Consumer<T>)`를 제공한다. 그 외에도 `isPresent()`를 사용하여 다룰 수는 있지만 이전의 `if (obj != null) ...` 과 다를 것이 없다. 그렇기 때문에 전자의 경우를 사용하는 것이 좋은 것 같다.

또한 Optional 객체에서는 **함수형 프로그래밍이 지원되기 때문에 다루는데 있어 아마 짧으면서도 직관적인 코드를 작성할 수 있을 것 같다.**

그 외에 `orElse()` 와 같은 함수 또한 존재하지만 이 글을 읽는 독자가 직접 다루어보는 것이 좋을 것 같다.

### 3. 결론

많은 자바 개발자들은 지금까지 항상 개발하면서 NPE를 겪어왔을 것이다. 자바 8을 통하여 Optional 객체가 추가됨으로서 `null-safety` 한 코드를 작성할 수 있게 되었다.

개인적인 생각으론, Optional 객체의 사용만으로 NPE를 줄일 수 있다면 그 자체로 가치있는 일이라고 생각한다. 이미 개발이 완료된 프로젝트라면 리팩토링 단계에서 도입을 검토해보아도 나쁘진 않을 것 같다.

> 사실, Java 8을 사용한다면 다 사용하고 있었겠지 (...)

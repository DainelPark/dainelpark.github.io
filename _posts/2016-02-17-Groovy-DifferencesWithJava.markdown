---
layout: post
title:  "Groovy - Differences with Java"
date:   2016-02-17 02:35:00
summary: JVM에서 돌아가는 Groovy, 하지만 자바와 다른 점은 무엇인가?
categories: Groovy
---

## 1. 글을 쓰면서

얼마 전에 지인과 이야기를 하였는데 **"은환씨 성격과 코드의 스타일을 고려해보았을 때 아마도 Groovy가 가장 어울릴 것같아요."**라는 조언을 듣고 계속 봐야지하다가 이제서야 Getting Started를 살펴본다. 이 글은 [Differences with Java](http://www.groovy-lang.org/differences.html)의 나의 해석 스타일에 따라 옮긴 글이다. 그리고 **12. Conversions**의 경우 그냥 홈페이지의 표를 보는게 더 편할 것같다.

## 2. Differences with Java

그루비는 자바 개발자들을 위해 최대한 자연스럽게 만들기 위하여 많은 노력을 기울였다.

이제 아래의 리스트는 자바와 그루비의 주요한 차이점이다.

### 2.1. Default imports

아래의 모든 패키지가 기본적으로 import된다. 


1. java.io.*	
2. java.lang.*
3. java.math.BigDecimal
4. java.math.BigInteger
5. java.net.*
6. java.util.*
7. groovy.lang.*
8. groovy.util.*

## 2.2. Multi-Methods

그루비에서는, 호출되는 메서드는 런타임에서 선택된다. 이것을 **Runtime Dispatch** 또는 **Multi-Methods**라고 불린다. 이것이 의미하는 것은 런타임에서 인자의 타입을 기반으로 메서드가 호출될 것이라는 의미다. 자바에서는 이것과 반대로 메서드들은 컴파일 시간에 선택되는데 이것은 **Declared Types**에 기반한 것이다.

다음의 코드를 보면 자바 언어로 작성된 코드이며 자바와 그루비에서 컴파일이 되지만 다른 동작을 보일 것이다. :
	
	1.
	int method(String arg) {
    	return 1;
	}
	2.
	int method(Object arg) {
    	return 2;
	}
	Object o = "Object";
	int result = method(o);

자바 언어일 경우, 다음과 같이 테스트해봐야할 것이다. :	
	
	assertEquals(2, result);
	
그루비의 경우, 다음과 같다. :

	assertEquals(1, result);
	
왜냐 하면, 자바는 이미 Object로 선언된 정적인 정보 타입을 사용할 것인 반면 메서드가 호출되었을 때 그루비는 런타임에서 선택할 것이기 때문이다.

즉, 위의 예시 코드를 참고하면 String과 함께 호출되었을 때 1번 메서드가 호출되는 것이다.

### 2.3. Array initializers

그루비에서 **`{ ... }`** 는 Closures를 위해 존재한다. 이것은 다음과 같은 문법으로 배열을 초기화할 수 없다는 것이다.

	int[] array = { 1, 2, 3 }
	-> 개인적으로는 이런 방식이 더 이쁘다고 생각한다. [] 형식을 쓰면서도 -_-;;;
	
다음과 같이 써야한다.

	int [] array = [ 1, 2, 3, ]
	
### 2.4. Package scope visibility

Property를 생성하라, 이것은 우리가 흔히 말하는 private field이며 Getter/Setter 메서드와 연관되어 있다.

**`@PackageScope`**라는 어노테이션을 이용하면 package-private field를 생성할 수 있다. :

	class Person {
		@PackageScope String name
	}
	
> 순간 Lombok Plugin이 생각난 것은 왜 일까?

### 2.5. ARM blocks

자바 7의 ARM(Automatic Resource Management) Block은 그루비에서는 지원되지 않는다. 대신에 그루비는 Clousers를 이용한 다양한 메서드를 제공한다. 똑같은 효과를 내면서 좀 더 ** idiomatic**하다. 다음은 예시 코드이다. :

	Path file = Paths.get("/path/to/file");
	Charset charset = Charset.forName("UTF-8");
	try (BufferedReader reader = Files.newBufferedReader(file, charset)) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }

	} catch (IOException e) {
    e.printStackTrace();
	}

위의 코드가 다음과 작성할 수 있다. :

	new File('/path/to/file').eachLine('UTF-8') {
   		println it
	}
	
자바 스타일에 좀 더 가깝게 하자면 다음과 같이 작성할 수 있다. :

### 2.6 Inner classes

Anonymous inner classes 와 Nested classes의 Implementation은 자바를 따르지만 꼭 자바 언어 스펙을 가지고 다르다고 머리 흔들 필요는 없다.

> The implementation done looks much like what we do for groovy.lang.Closure, with some benefits and some differences. Accessing private fields and methods for example can become a problem, but on the other hand local variables don’t have to be final.

(누가 해석 좀 해주세요 ㅠㅠ 대충 이해는 했는데 문장력이...)

### 2.6.1 Static inner classes

Static inner Class의 예시는 다음과 같다. 사실 볼 필요도 없을 것같다. 자바랑 똑같다. :

	class A {
    	static class B {}
	}

	new A.B()

### 2.6.2 Anonymous Inner Classes


	import java.util.concurrent.CountDownLatch
	import java.util.concurrent.TimeUnit

	CountDownLatch called = new CountDownLatch(1)

	Timer timer = new Timer()
	timer.schedule(new TimerTask() {
    	void run() {
        called.countDown()
    	}
	}, 0)

	assert called.await(10, TimeUnit.SECONDS)

**으으... 람다... 람다를 가져와라!!!**

### 2.6.3. Creating Instances of Non-Static Inner Classes

자바에서는 보통 이렇게 한다. :

	public class Y {
    	public class X {}
    	public X foo() {
        	return new X();
    	}
    	public static X createX(Y y) {
        	return y.new X();
    	}
	}
	
그루비에서는 **` y.new X();`**와 같은 문법을 지원하지 않는다. 대신에 아래와 같이 **`new X(y)`**와 같이 작성할 수는 있다.

	public class Y {
    	public class X {}
    	public X foo() {
    	    return new X()
    	}
    	public static X createX(Y y) {
        	return new X(y)
    	}
	}
	
조심해라! 그루비는 인자없이 하나의 인자를 가지고 있는메서드를 호출하는 것을 지원한다. 그 파라미터는 아마 **null**을 가지게 될 것이다.

Basically the same rules apply to calling a constructor. There is a danger that you will write new X() instead of new X(this) for example. Since this might also be the regular way we have not yet found a good way to prevent this problem. **(위험성은 원문으로 봐야 느끼실 것 같아서...)**

### 2.7. Lambdas

자바 8은 람다식과 메서드 레퍼런스를 지원한다.
	
	Runnable run = () -> System.out.println("Run");
	list.forEach(System.out::println)
	
그루비는 람다식 문법을 지원하지는 않지만 Closures가 이를 대신한다.

	Runnable run = { println 'run' }
	list.each { println it } // or list.each(this.&println)
	
개쩌러여...

### 2.8. GStrings

Double-Quoted String Literals가 GString 으로 인식되면 그루비는 아마 컴파일 에러 혹은 묘하게 다른 코드를 제공할 것이다.

일반적으로, 그루비는 **Auto-cast**를 지원한다.

### 2.9. String and Character literals

Singlee-Quoted Literals는 그루비에서는 **`String`**으로 사용되고 Double-Quoted는 String 혹은 GString으로 사용된다.

	assert 'c'.getClass()==String
	assert "c".getClass()==String
	assert "c${1}".getClass() in GString

그루비는 자동적으로 하나의 문자로 구성된 **`String`**을  **`Char`**로 캐스팅해준다. 
> only when assigning to a variable of type char. -> 뭔 말?

Char를 인자로 가지고 있는 메서드를 호출할 때, 우리는 명시적으로 캐스팅해줄 필요가 있다. 혹은 값이 확실히 될 수 있도록 해야한다.

그리고 그루비의 캐스팅 스타일은 조금 더 관대하다. 'c'와 'cx'가 있다고 가정하자. 그러면 C 스타일의 캐스팅은 후자는 Exception에 빠진다. 근데 그루비는 첫 번째 문자만 가져가기 때문에 그냥 넘어간다. 핵관대하넹 ㅋ

### 2.10. Primitives and wrappers

그루비는 객체 (Objects)를 모든 것에 사용하기 때문에 Primitivies를 Wrapping 해야한다. 예를 들어, int -> Integer로, float -> Float 처럼 말이다. :

	int i
	m(i)

	void m(long l) {           
  		println "in m(long)"
	}

	void m(Integer i) {        
  		println "in m(Integer)"
	}
	
### 2.11. Behavior of `==`

> In Java == means equality of primitive types or identity for objects. In Groovy == translates to a.compareTo(b)==0, iff they are Comparable, and a.equals(b) otherwise. To check for identity, there is is. E.g. a.is(b).

>> 난 eqauls(Object o)랑 == 만 있어도 잘 사용할 수 있었다고 생각하는뎅... 그루비라는 언어를 좀 더 사용해보고 왜 더 좋은지 판단해봐야할 듯...


### 2.12. Extra keywords

다음과 같은 키워드가 존재한다. 이 녀석들은 변수 이름으로 쓰지 마라!

1. as
2. def
3. in
4. trait

### 2.13. default must be last in switch case

**`Default`**는 반드시 switch/case문의 마지막에 가도록 해라.
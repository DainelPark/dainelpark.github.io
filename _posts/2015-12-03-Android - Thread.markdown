---
layout: post
title:  "Android - Thread"
date:   2015-12-03 20:58:00
categories: android
---

**이 글은 Android의 공식 문서를 보고 번역한 글입니다. 혹시 문제가 있다면 Pull Request를 부탁드립니다. 원문 글은 [Android Thread](http://developer.android.com/intl/ko/reference/java/lang/Thread.html)입니다.**

## Class Overview

`Thread`는 동시성을 위한 것이다. Thread는 고유의 methods가 호출되면서 그들의 인자와 지역 변수를 가지고 있는 고유의 call stack을 가지고 있다. 각각의 앱은 앱의 시작 시점부터 최소한 하나의 Thread를 가지고 있는데 이를 `Main Thread`라고 하며 `ThreadGroup`에 속해있다. Runtime은 System Thread Group에서 Thread를 유지한다.

새로운 Thread를 생성하기 위해서는 두 가지 방법이 있다. 새로운 클래스를 만들어서 `run()` method를 오버라이드를 하거나 새로운 `Thread`를 만들어서 `Runnable`로 넘기면(pass) 된다. 이 경우 새로운 `Thread`를 호출할 때, 반드시 `start()` method가 호출되어야한다.

각각의 `Thread`는 OS에 의해 스케쥴링되는 Integer Type의 순서가 있다. 새로운 Thread는 부모의 순서를 상속받는다. 이것은 `setPriority(int)` method를 이용하여 정할 수 있다.
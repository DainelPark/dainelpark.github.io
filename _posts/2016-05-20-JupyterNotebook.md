---
layout: post
title: "Jupyter Notebook 을 사용해보기"
date: 2016-05-20
categories:
---

기계 학습을 공부하길 위하여 Jupyter Notebook 을 사용하게 되었다.

![](https://raw.githubusercontent.com/DainelPark/dainelpark.github.io/52c3575ba0b360d37b55acd86b703ad2551d21b7/images/jupyter-notebook.png)

이를 이용하면 이처럼 웹에서 파이썬을 할 수 있다. 

	pip3 install jupyter
	
를 하면 간편하게 설치할 수 있다. 물론 Python 3이 설치되어있어야한다.

	jupyter notebook
	
을 치면 localhost의 8888 포트에서 동작한다. 

	jupyter notebook --ip=0.0.0.0
	
를 하면 외부에서 접속할 수 있게 된다. 동작하는 OS의 방화벽이 다른 IP의 접속을 허가해놓았다면. 

이렇게 환경을 구성해놓았다면 열심히 기계 학습을 공부하면 된다.

 
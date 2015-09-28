---
layout: post
title:  "SDN Network (1) - Mininet"
date:   2015-09-28 04:19:32
categories: SDN
---

## SDN Network란?

SDN은 기존 네트워크 구조의 한계성을 극복하기 위한 그리고 시대가 빠귀면서 변화에 유연하게 반응하기 위한 네트워크 아키텍쳐이다. 기존 네트워크 장비에서 하드웨어와 소프트웨어 기능을 분리한 것이다.

## Mininet 소개

Mininet을 이용하면 하나의 머신에서 네트워크의 호스트, 링크, 그리고 스위치를 구성할 수 있다. `sudo mn`을 이용하여 run하는데 기본적으로 하나의 스위치 그리고 2개의 호스트로 구성해준다.

	Mininet is useful for interactive development, testing, and demos,
	especially those using OpenFlow and SDN. OpenFlow-based network controllers prototyped in Mininet 
	can usually be transferred to hardware with minimal changes for full line-rate execution
	
라고 나와있듯이 Mininet으로 SDN 개념을 이해하기엔 좋을 것같다. 더군더나 최근에는 라즈베리 파이에도 구성할 수 있다. SDN의 경우 사실 일반인들이 접하기 쉽지 않은데 Mininet은 이 장벽을 좀 낮추어주고 있다.[A Network in a Laptop: Rapid Prototyping for
Software-Defined Networks](http://yuba.stanford.edu/~nickm/papers/a19-lantz.pdf)와 같은 논문도 있다. 이 논문은 무려 479회나 인용된 논문이니 읽어봐도 좋을 듯.

## Mininet 사용하기

[Mininet](http://mininet.org/download/)에 들어가면 VM Image가 있다. Virtual Box(소마에서 VMWare 라이센스를 안 줌...)에 Import를 한다. 계정 명과 비밀번호를 입력하고 들어가면 된다.

![](https://raw.githubusercontent.com/DainelPark/dainelpark.github.io/master/images/mininet_loaded.png)

위 사진을 보면 네트워크 인프라를 구성해주는 걸 확인할 수 있다. 컨트롤러가 s1 스위치를 담당하며 s1 스위치가 h1, h2 호스트들을 연결해준다. (**Topology**에 관해서 찾아보면 더 좋을 듯)

`pingall` 명령어를 사용하여 연결이 잘 되었는지 확인할 수 있다.

이외에도 `nodes`, `net` 등의 명령어를 통하여 정보를 확인해볼 수 있다.

Topology를 사용자 정의를 통하여 구성할 수도 있는데 
**mininet VM에 custom 폴더에 예시가 있다.**
폴더에 들어간 후 `sudo mn --custom topo-2sw-2host.py --topo mytopo --test pingall` 명령어를 치면 사용자가 정의한 Topology를 구성하여 mininet을 실행하고 ping이 잘되는지도 알려준다.

Custom Topology에 관한 정보는 [mininet walkthrough](http://mininet.org/walkthrough/)의 예제에서 더 자세히 설명되어있다. 그런데 그래프 이론에 대해서 어느정도 공부는 해야할 듯...?

솔직히 관심 분야가 아니라서 재미도 없고 어떻게 해야하나 걱정도 많이 했는데 조금씩 하면서 이해가 되니까 나름대로 할만한 것같다. 더 열심히 공부를 해봐야할 듯.
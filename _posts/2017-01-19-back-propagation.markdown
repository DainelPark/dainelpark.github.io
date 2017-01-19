---
layout: post
title:  "Back-propagation 이해하기"
date:   2017-01-19
---

## [원문 글](https://mattmazur.com/2015/03/17/a-step-by-step-backpropagation-example/) 을 공부하면서 번역해본 글입니다. 혹시 문제가 있다면 about 의 제 email 로 알려주세요.

### Overview

이 튜토리얼에서, 우리는 각각 2개의 input layer와 hidden layer 그리고 output layer로 구성된 neural network 를 예시로 사용할 것입니다. 덧붙이자면, 각각의 hidden layer와 output layer에서는 bias 를 포함시켰습니다.

다음은, 기본적인 구조입니다.


![](https://matthewmazur.files.wordpress.com/2018/03/neural_network-7.png)

그리고 이 네트워크에서는 초기 weight와 biases 그리고 training inputs/ouputs 가 존재합니다.

![](https://matthewmazur.files.wordpress.com/2018/03/neural_network-9.png)

back-propagation 의 목적은 neural network 가 최적의 weight 를 갖도록 하는 것입니다.

이 튜토리얼동안 우리는 하나의 training set 을 이용할 것입니다. input 은 **0.01, 0.10** 이며 우리는 neural network 를 통하여 **0.01, 0.99** 가 output 으로 나오기를 원합니다.

### The Forward pass

시작과 함꼐, 우리에게 주어진 weight와 bias 그리고 input 을 통하여 예측하는 과정을 보도록 합시다.

우리는 이것을 통하여 각각의 hidden Layer 에 존재한 neuron 들의 *total net input* 을 알아낼 것이고 *activation function(우리는 logistic function 을 사용할 것입니다.)*에 total net input 을 대입하고, 다시 이 과정을 반복하여 output layer 의 neuron 을 도출하도록 할 것입니다.

H(1)을 계산해보도록 하겠습니다.

![](https://s0.wp.com/latex.php?latex=net_%7Bh1%7D+%3D+w_1+%2A+i_1+%2B+w_2+%2A+i_2+%2B+b_1+%2A+1&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=net_%7Bh1%7D+%3D+0.15+%2A+0.05+%2B+0.2+%2A+0.1+%2B+0.35+%2A+1+%3D+0.3775&bg=ffffff&fg=404040&s=0&zoom=2)

이 과정까지 왔다면 우리는 이제 activation function 을 이용하여 H(1)의 output 을 도출할 것입니다.

> 우리가 사용하려는 activation function 의 공식은 1 / 1+e^-t 와 같습니다.

![](https://s0.wp.com/latex.php?latex=out_%7Bh1%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-net_%7Bh1%7D%7D%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-0.3775%7D%7D+%3D+0.593269992&bg=ffffff&fg=404040&s=0&zoom=2)

다음과 같이 도출해줍니다. 이 과정을 H(2)에도 똑같이 하면 됩니다. 그렇다면 다음과 같은 수치를 얻게 됩니다.

![](https://s0.wp.com/latex.php?latex=out_%7Bh2%7D+%3D+0.596884378&bg=ffffff&fg=404040&s=0&zoom=2)

이제 이 과정을 output layer neuron 에게도 우리가 도출해낸 수치를 이용하여 진행할 예정입니다.

다음은 O(1)의 output 입니다.

![](https://s0.wp.com/latex.php?latex=net_%7Bo1%7D+%3D+w_5+%2A+out_%7Bh1%7D+%2B+w_6+%2A+out_%7Bh2%7D+%2B+b_2+%2A+1&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=net_%7Bo1%7D+%3D+0.4+%2A+0.593269992+%2B+0.45+%2A+0.596884378+%2B+0.6+%2A+1+%3D+1.105905967&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=out_%7Bo1%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-net_%7Bo1%7D%7D%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-1.105905967%7D%7D+%3D+0.75136507&bg=ffffff&fg=404040&s=0&zoom=2)

이 과정을 똑같이 진행하면 O(2)는 다음과 같습니다.

![](https://s0.wp.com/latex.php?latex=out_%7Bo2%7D+%3D+0.772928465&bg=ffffff&fg=404040&s=0&zoom=2)

### Calculating the Total Error

우리는 이제 각각의 output neuron 의 error rate 를 계산해야합니다. 다음과 같은 공식을 이용하여 total error rate 를 얻을 수 있습니다.

![](https://s0.wp.com/latex.php?latex=E_%7Btotal%7D+%3D+%5Csum+%5Cfrac%7B1%7D%7B2%7D%28target+-+output%29%5E%7B2%7D&bg=ffffff&fg=404040&s=0&zoom=2)

> 역자 주 : 인공신경망을 공부하면서, 저는 weight 를 일정 범위 안에서 랜덤으로 생성하여 학습시켜봤었습니다. 역시, 랜덤은 랜덤답게, 운 좋으면 맞고  운 나쁘면 계속 틀리는 상황이 발생하였습니다. 사실 당연한 것이죠. 그러니 이걸 일찍 알았으면 좋았을텐데 말이죠...

예를 들어, O(1)이 목표로 하는 결과값은 0.01입니다만 우리의 neural network 에서는 0.75136507 을 결과값으로 주었죠. 그러므로, error rate 는 다음과 같습니다.

![](https://s0.wp.com/latex.php?latex=E_%7Bo1%7D+%3D+%5Cfrac%7B1%7D%7B2%7D%28target_%7Bo1%7D+-+out_%7Bo1%7D%29%5E%7B2%7D+%3D+%5Cfrac%7B1%7D%7B2%7D%280.01+-+0.75136507%29%5E%7B2%7D+%3D+0.274811083&bg=ffffff&fg=404040&s=0&zoom=2)

이제 O(2)에도 진행해보면 다음과 같은 error rate 를 나타내게 됩니다.

![](https://s0.wp.com/latex.php?latex=E_%7Bo2%7D+%3D+0.023560026&bg=ffffff&fg=404040&s=0&zoom=2)

이제 2 개의 error rate 를 더하면 total error rate 를 알 수 있습니다.

![](https://s0.wp.com/latex.php?latex=E_%7Btotal%7D+%3D+E_%7Bo1%7D+%2B+E_%7Bo2%7D+%3D+0.274811083+%2B+0.023560026+%3D+0.298371109&bg=ffffff&fg=404040&s=0&zoom=2)

### The Backwards Pass

우리가 back-propagation 을 사용하며 바라는 목적은 우리가 바라는 target output 에 가깝게 하기 위해 network 에 있는 각각의 weight 를 update 하고 이를 통하여, 각각의 output neuron 에 대한 error rate 를 최소화하는 것입니다.

###### Output Layer

W(5)에 대해 생각해봅시다. 우선적으로 W(5)는 total error rate 에 어떤 영향을 끼쳤을까요? 이를 알아보는 수식은 다음과 같습니다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B5%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

> 역자 주: 저는 편미분을 듣는 공학수학을 F를 맞아서 다시 들어야하는 수준임에도 불구하고 이해합니다. 씁쓸하군요. 하하하!

이 수식은 chain rule 을 이용해야 합니다. 다음과 같이 말입니다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B5%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+out_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+w_%7B5%7D%7D&bg=ffffff&fg=404040&s=2&zoom=2)

시각적으로 보자면, 다음 사진이 우리가 뭘 하고 있는지 알려줄 겁니다.

![](https://matthewmazur.files.wordpress.com/2018/03/output_1_backprop-4.png)

> 역자 주: back-propagation 은 직역하자면 역전파 기법(...)입니다.

일단 얼마나 total error rate 에 영향을 끼쳤는지 한 번 알아봅시다.

![](https://s0.wp.com/latex.php?latex=E_%7Btotal%7D+%3D+%5Cfrac%7B1%7D%7B2%7D%28target_%7Bo1%7D+-+out_%7Bo1%7D%29%5E%7B2%7D+%2B+%5Cfrac%7B1%7D%7B2%7D%28target_%7Bo2%7D+-+out_%7Bo2%7D%29%5E%7B2%7D&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bo1%7D%7D+%3D+2+%2A+%5Cfrac%7B1%7D%7B2%7D%28target_%7Bo1%7D+-+out_%7Bo1%7D%29%5E%7B2+-+1%7D+%2A+-1+%2B+0&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bo1%7D%7D+%3D+-%28target_%7Bo1%7D+-+out_%7Bo1%7D%29+%3D+-%280.01+-+0.75136507%29+%3D+0.74136507&bg=ffffff&fg=404040&s=0&zoom=2)

> 1. 편미분은 다변수 함수의 특정 변수를 제외한 나머지 변수를 상수로 생각한 후, 미분하는 것입니다. 즉 E(total)의 O(2)와 관련된 부분은 0으로 처리됩니다. 상수를 미분하면 0이니까요.
> 2. -(target - out)은 out - target 으로 표현될 수 있습니다.

total net input은 O(1)에 어떤 영향을 끼쳤을까요? 아래 사진으로 알 수 있습니다.

![](https://s0.wp.com/latex.php?latex=out_%7Bo1%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-net_%7Bo1%7D%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+out_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%3D+out_%7Bo1%7D%281+-+out_%7Bo1%7D%29+%3D+0.75136507%281+-+0.75136507%29+%3D+0.186815602&bg=ffffff&fg=404040&s=0&zoom=2)

그리고 W(5)는 O(1)에 어떤 영향을 끼쳤는지 마침내 도출할 수 있게 되었습니다.

![](https://s0.wp.com/latex.php?latex=net_%7Bo1%7D+%3D+w_5+%2A+out_%7Bh1%7D+%2B+w_6+%2A+out_%7Bh2%7D+%2B+b_2+%2A+1&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+w_%7B5%7D%7D+%3D+1+%2A+out_%7Bh1%7D+%2A+w_5%5E%7B%281+-+1%29%7D+%2B+0+%2B+0+%3D+out_%7Bh1%7D+%3D+0.593269992&bg=ffffff&fg=404040&s=0&zoom=2)

이제, 이것을 모두 대입해봅니다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B5%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+out_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+w_%7B5%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B5%7D%7D+%3D+0.74136507+%2A+0.186815602+%2A+0.593269992+%3D+0.082167041&bg=ffffff&fg=404040&s=0&zoom=2)

이제, error rate 를 줄여봅시다.

![](https://s0.wp.com/latex.php?latex=w_5%5E%7B%2B%7D+%3D+w_5+-+%5Ceta+%2A+%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B5%7D%7D+%3D+0.4+-+0.5+%2A+0.082167041+%3D+0.35891648&bg=ffffff&fg=404040&s=0&zoom=2) 

> alpha와 eta, 그리고 epsilon 은 learning rate 로 사용되기도 합니다. 이 글에서는 learning rate 를 0.5 로 정하였습니다.

이 과정을 반복하면 우리는 W(6) ~ W(8)의 새로운 weight 또한 얻을 수 있습니다.

![](https://s0.wp.com/latex.php?latex=w_6%5E%7B%2B%7D+%3D+0.408666186&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=w_7%5E%7B%2B%7D+%3D+0.511301270&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=w_8%5E%7B%2B%7D+%3D+0.561370121&bg=ffffff&fg=404040&s=0&zoom=2)

###### Hidden Layer

이제 W(1) ~ W(2) 의 가중치에 대한 새로운 값을 계산해볼 차례다.

큰 틀에서 우리가 정말 도출해내고자 하는 값은 다음과 같다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+out_%7Bh1%7D%7D%7B%5Cpartial+net_%7Bh1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+net_%7Bh1%7D%7D%7B%5Cpartial+w_%7B1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

이며 시각적으로는 다음과 같다.

![](https://matthewmazur.files.wordpress.com/2015/03/nn-calculation.png)

우리가 output layer 에서 했던 것과 비슷한 과정을 할 것이지만 각 hidden layer 의 ouptut 이 multiple output neuron (따라서 error rate) 에  영향을 끼친다는 것을 설명하기 위해 약간 다를 것입니다.

우리는 ![](https://s0.wp.com/latex.php?latex=out_%7Bh1%7D&bg=ffffff&fg=404040&s=0&zoom=2) 이 ![](https://s0.wp.com/latex.php?latex=out_%7Bo1%7D&bg=ffffff&fg=404040&s=0&zoom=2) 그리고 ![](https://s0.wp.com/latex.php?latex=out_%7Bo2%7D&bg=ffffff&fg=404040&s=0&zoom=2) 에 영향을 끼친다는 것을 이미 알고 있습니다. 그러므로 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 는 각각의 output neuron 들의 영향에 대해 고민을 해볼 필요 가 있습니다.

즉, 수식으로는 다음과 같습니다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%2B+%5Cfrac%7B%5Cpartial+E_%7Bo2%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 부터 하자면

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

우리는 이전 과정에서 계산했던 것을 이용하여 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 를 계산할 수 있습니다.

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+out_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%3D+0.74136507+%2A+0.186815602+%3D+0.138498562&bg=ffffff&fg=404040&s=0&zoom=2)

그리고 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 는 W(5) 와 같습니다.

![](https://s0.wp.com/latex.php?latex=net_%7Bo1%7D+%3D+w_5+%2A+out_%7Bh1%7D+%2B+w_6+%2A+out_%7Bh2%7D+%2B+b_2+%2A+1&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+w_5+%3D+0.40&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+net_%7Bo1%7D%7D+%2A+%5Cfrac%7B%5Cpartial+net_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+0.138498562+%2A+0.40+%3D+0.055399425&bg=ffffff&fg=404040&s=0&zoom=2)

이 과정을 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo2%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 에도 해보면

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Bo2%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+-0.019049119&bg=ffffff&fg=404040&s=0&zoom=2)

를 얻을 수 있습니다.

그러므로,

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+%5Cfrac%7B%5Cpartial+E_%7Bo1%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%2B+%5Cfrac%7B%5Cpartial+E_%7Bo2%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D+%3D+0.055399425+%2B+-0.019049119+%3D+0.036350306&bg=ffffff&fg=404040&s=0&zoom=2)

인 것입니다.

우리는 이제, ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+out_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 를 알고 있고, 이제 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+out_%7Bh1%7D%7D%7B%5Cpartial+net_%7Bh1%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2) 와 ![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+net_%7Bh1%7D%7D%7B%5Cpartial+w%7D&bg=ffffff&fg=404040&s=0&zoom=2) 를 알아야합니다.

![](https://s0.wp.com/latex.php?latex=out_%7Bh1%7D+%3D+%5Cfrac%7B1%7D%7B1%2Be%5E%7B-net_%7Bh1%7D%7D%7D&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+out_%7Bh1%7D%7D%7B%5Cpartial+net_%7Bh1%7D%7D+%3D+out_%7Bh1%7D%281+-+out_%7Bh1%7D%29+%3D+0.59326999%281+-+0.59326999+%29+%3D+0.241300709&bg=ffffff&fg=404040&s=0&zoom=2)

우리가 output neuron 에서 했던 것 처럼 편미분합시다!

![](https://s0.wp.com/latex.php?latex=net_%7Bh1%7D+%3D+w_1+%2A+i_1+%2B+w_2+%2A+i_2+%2B+b_1+%2A+1&bg=ffffff&fg=404040&s=0&zoom=2)

![](https://s0.wp.com/latex.php?latex=%5Cfrac%7B%5Cpartial+net_%7Bh1%7D%7D%7B%5Cpartial+w_1%7D+%3D+i_1+%3D+0.05&bg=ffffff&fg=404040&s=0&zoom=2)

이제 우리는 W(1)을 업데이트 할 수 있습니다.

![](https://s0.wp.com/latex.php?latex=w_1%5E%7B%2B%7D+%3D+w_1+-+%5Ceta+%2A+%5Cfrac%7B%5Cpartial+E_%7Btotal%7D%7D%7B%5Cpartial+w_%7B1%7D%7D+%3D+0.15+-+0.5+%2A+0.000438568+%3D+0.149780716&bg=ffffff&fg=404040&s=0&zoom=2)

다음과 같은 과정을 W(2) ~ W(4) 에도 적용하면 업데이트할 새로운 weight 구할 수 있습니다.
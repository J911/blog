---
layout: post
comments: true
title:  "Sigmoid 함수를 사용한 Logistic regression 기법의 코스트 최적화"
date:   2018-10-31
author: "J911"
categories: deeplearning
cover: "/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/cover.png"
---
로지스틱 회기분석(Logistic regression)에서 cost를 optimize하기 위해서는 활성함수를 미분하여야한다. 

하지만 기존의 계단 형태의 활성함수는 **미분이 불가**한 함수로서 cost optimize하는데 한계가 존재하였다. 그래서 등장한 것이 **시그모이드**함수이다.

![sigmoid](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/sigmoid.png)

시그모이드의 함수를 그리면 다음과 같은데

![sigmoid-graph](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/sigmoid-graph.png)
> 표현의 극대화를 위해 z의 값을 증가시켜 표현하였다.

활성함수를 연속적인 곡선의 형태의 함수로 사용하여 미분이 가능하게되었다.

### Cost optimization
cost는 예측한 값에서 정답과의 차를 계산하여 다음과 같은 공식으로 구할 수 있습니다.
![cost-function](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/cost-function.png)

기존의 Linear 함수![hypothesis-function](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/hypothesis-function.png)를 cost function에 적용해 보았을 때는 곡선 형태의 그래프로 나타나게 된다. 때문에 미분을 이용해 최적의 cost를 계산할 수 있지만 sigmoid 함수를 사용할 경우 울퉁불퉁한 형태의 곡선 그래프가 그려지게 된다. 때문에 미분으로 최적화시키는 것이 쉽지가 않다. 그래서 새로운 cost function이 필요하게 되었다.

![new-cost-function](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/new-cost-function.png)
![c-function](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/c-function.png)

이것이 새로운 cost function이다. 이 함수는 Logistic 함수가 들어와도 부드러운 곡선 형태로 잡아주게 된다. 따라서 새로운 Cost funtion을 사용할 경우 기존에 사용하던 Gradint decent 알고리즘을 그대로 사용할 수 있다.

## [과제 6번] 신경망의 표현
> 1개의 인풋레이어, 1개의 히든레이어, 1개의 아웃풋 레이어를 가진 신경망

![neural-network](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/neural-network.png)

### forward propagation
위의 신경망에서 Input Layer의 뉴런이 다음 뉴런으로 전이되는 과정은 다음과 같이 표현될 수 있다.

![forward-propagation-step1](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/forward-propagation-step1.png)

![forward-propagation](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/forward-propagation-1.png)

그 결과를 k로 나타내면 마지막 노드에도 적용시키면

![forward-propagation-step2](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/forward-propagation-step2.png)

![forward-propagation-step2](/assets/post-assets/2018-10-31-sigmoid-and-cost-optimize/forward-propagation-2.png)

위와 같은 결과를 얻을 수 있게 된다. 이것을 Sigmoid와 같은 활성함수로 처리하게 되면 Predict 값을 구할 수 있게된다.
---
layout: post
comments: true
title:  "혼자 논문읽기: RCNN 논문 분석"
date:   2019-03-24
author: "J911"
categories: deeplearning
cover: "/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/cover.png"
---

대학원진학을 준비하면서 나도 슬슬 논문을 볼줄 알아야겠다는 생각을 시작했다. 

> 참고로 이 블로깅과 영상에서는 내가 이해하고 있는 부분에 대해 정리를 하는 것으로 논문의 모든 내용을 다루지는 않는다.

## 논문 정보
이 포스트는 2013년 11월에 공개된 _Rich feature hierarchies for accurate object detection and semantic segmentation_
라는 제목의 논문을 분석한다.

## R-CNN의 목표 및 이전의 알고리즘
![Picture1](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture1.png)

R-CNN의 목표는 위의 그림과 같이 입력 이미지에 대해서 그 속의 물체를 탐지해 위치를 찾고(localization) 각각의 물체를 분류(classification)하는 `Object Detection`이다.

다음과 같은 두개의 object detection 알고리즘이 이전에 존재하였는데,

![Picture2](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture2.png)


- SIFT(Scalar Invariant Feature Transform)
- DOG(Histograms of Oriented Gradient)

이 두 개의 알고리즘은 모두 물체의 특징점을 찾아서 물체를 탐지하는 알고리즘이다.

논문에서는 이 두개의 알고리즘이 2010 ~ 2012년 동안 아주 느리게 발전해왔다고 한다.

그래서 이 논문은 더 진보적인 모델을 제안하는데, 바로 `R-CNN(Regions with CNN features)`이다.

논문의 초록에서는 R-CNN을 다음과 같이 소개한다.

- 간단하면서도 변형하기 쉽다
- 이전의 object detection 알고리즘의 mAP(평균 정밀도) 보다 30% 성능을 높인 53%의 mAP를 보인다.

![Picture3](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture3.png)

## Object detection with R-CNN
### Three modules
논문에서는 R-CNN이 세가지의 모듈로 구성된다고 한다.

1. 물체의 위치를 파악하는 Region Proposal 모델
2. Region proposal된 각 영역들을 통해 피쳐맵을 추출하는 컨볼루션 뉴럴 네트워크
3. 추출된 피쳐를 통해 classification하는 linear SVM

#### Region proposals
첫번째로 Region proposals을 하는 모듈인데, 물체가 존재할 확률이 있는 영역을 2000개를 추출하는 것이다.
논문에는 Region proposals을 위해 selective search 알고리즘(ss)을 사용한다.

아래 그림을 참고하면 ss가 가장 좋은 성능을 보이는 것을 알 수 있다.
![Picture10](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture10.png)

### Feature extraction
그 다음은 추출된 Region proposals 영역을 통해 피쳐를 추출하는 것인데 여기는 CNN이 사용된다.
네트워크 특성상 고정된 사이즈의 입력만 받을 수 있기 때문에, 각각 나눠진(cropping) Region proposals 영역을 변형(warpping)하여 `227 x 227 pixels`을 만든다.

이렇게 만들어진 입력 이미지는  5개의 컨볼루션 레이어와 2개의 fully-connected 레이어를 거쳐 피쳐가 추출되게 된다.

![Picture7](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture7.png)

최종적으로는 추출된 피쳐를 SVM을 사용해서 Classification하게 된다.

## Test
결과적으로 이 모델을 테스트 하는 과정을 다시 나열하면 다음과 같다.

1. 테스트 이미지에서 대락 2000개의 Region proposal을 추출한다.
2. 추출된 영역을 cropping 하고 warpping하여 CNN을 통과시켜 피쳐를 추출한다.
3. 추출된 피쳐를 SVM을 사용해 분류한다.

![Picture8](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture8.png)
_(source: https://apple.github.io )_

만약에 위와 같이 한 물체에 대해서 여러 박스가 겹쳐(overlap) 발생하게 된다면(IoU, intersection-over-union), Threshold를 조절해 가면서 더 높은 확률의 박스를 최종 결과로 정하게 된다. 

## Trainning
그 다음은 학습에 대한 부분인데 이 논문과 같은 경우에는 `ImageNet`에서 사전 훈련된(pre-trained) CNN 모델을 로드해 사용을 한다.

그리고 `SGD` 를 통해 미세 조정을 하는데 여기에는 128개의 mini-batch를 이용한다.
mini-batch는 positive sample 32개와 negative sample 96개로 이루어진다. 

- positive sample은 IoU가 0.5 이상인 샘플
- negative sample은 그 이외의 샘플

다음으로 SVM을 이용해 학습을 시키는데, 여기에도 positive sample과 negative sample(IoU < 0.3)을 이용해 학습한다.

이렇게 샘플을 나누는 이유는 아무래도 이미지의 정보중 배경이 차지하는 영역이 훨씬 많기 때문에 높은 학습을 위해 적절하게 비율을 맞추는 것이다.

## 결과
![Picture9](/assets/post-assets/2019-03-24-analysis-of-rcnn-paper/Picture9.png)

결과적으로 R-CNN은 VOC 2007과 VOC 2010 버전의 데이터셋에서 타 알고리즘에 비해 높은 mAP를 보이는 것으로 알 수 있다.


## 결론

여기까지가 논문을 정리한 내용이다. 사실 보통의 R-CNN은 Region Proposals 알고리즘이 CPU기반으로 동작하기 때문에 병목현상(bottleneck)이 발생하는 것이다.

추가로 2000개의 Region Proposal이 하나씩 warping된 후 각각 CNN에 입력되기 때문에 굉장히 비효율적이다.

이러한 내용을 배경으로 `Fast R-CNN`과 `Faster R-CNN`이 나오게 되었다.

이 부분은 다음번에 이어서 정리해보도록 하겠다. 


## reference
- Ross Girshick, Jeff Donahue, Trevor Darrell, Jitendra Malik.: Rich feature hierarchies for accurate object detection and semantic segmentation
- J. HOSANG ET AL.: HOW GOOD ARE DETECTION PROPOSALS, REALLY?

## links
- Youtube: [https://youtu.be/8X-Q4-MdIbQ](https://youtu.be/8X-Q4-MdIbQ)
- 슬라이드 다운로드: [https://drive.google.com/file/d/1pXaXvcUNGVgHKvEn39LQBA1Pm-ikUfRR/view](https://drive.google.com/file/d/1pXaXvcUNGVgHKvEn39LQBA1Pm-ikUfRR/view)

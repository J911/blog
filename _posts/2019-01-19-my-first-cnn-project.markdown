---
layout: post
comments: true
title:  "난생처음 CNN 프로젝트 해보기(오버피팅 체험해보기)"
date:   2019-01-19
author: "J911"
categories: deeplearning
cover: "/assets/post-assets/2019-01-19-my-first-cnn-project/cover.png"
---
평소 CNN을 공부하고 이론과 예제 프로젝트만 따라해보다가 
처음으로 나의 프로젝트를 진행해보았다.

간단하게 얼굴의 위치가 왼쪽을 향해있는지 오른쪽을 향해있는지 추적하는 모델을 설계하고자 하였다.

## 데이터 수집
opencv를 통해 나의 얼굴 사진을 캡쳐해 라벨링을 하는 모듈을 개발하였다.

<img src="/assets/post-assets/2019-01-19-my-first-cnn-project/capture.png">

> 'a'를 누르면 왼쪽 사진 캡쳐 'd'를 누르면 오른쪽 사진이 캡쳐되게 개발하였다.

## 모델 설계 

720 * 1280 이미지를 데이터셋으로 하기 때문에 데이터가 너무 크다고 생각이 들어 처음 `MaxPool`을 `6 x 6`으로 하여 해상도를 낮추고 시작하였다.

그리고 2개의 컨블루션 레이어와 MaxPool을 번갈아게 설계하고 fully_connected 레이어를 5개 두어서 모델을 완성하였다.

```python
# ...
conv1 = nn.Conv2d(3, 30, kernel_size=10)
conv2 = nn.Conv2d(30, 100, kernel_size=10)
mp1 = nn.MaxPool2d(6)
mp2 = nn.MaxPool2d(2)

fc1 = nn.Linear(105800, 5000)
fc2 = nn.Linear(5000, 1000)
fc3 = nn.Linear(1000, 500)
fc4 = nn.Linear(500, 50)
fc5 = nn.Linear(50, 10)
fc6 = nn.Linear(10, 2)

# ...
in_size = x.size(0)

x = self.mp1(x) # Dimension collapse
x = F.relu(self.mp2(self.conv1(x)))
x = F.relu(self.mp2(self.conv2(x)))

x = x.view(in_size, -1)
x = F.relu(self.fc1(x))
x = F.relu(self.fc2(x))
x = F.relu(self.fc3(x))
x = F.relu(self.fc4(x))
x = F.relu(self.fc5(x))
x = self.fc6(x)
```

<img src="/assets/post-assets/2019-01-19-my-first-cnn-project/conv_img.png">

> `matplotlib`으로 대강 시각화 해보면 위와 같다


## TEST

### 첫번째 시도
- 학습 데이터셋: 170
- 테스트 데이터셋: 62

#### 결과
```
Epoch: 1 Batch: 1 Loss: 0.703045
Epoch: 1 Batch: 2 Loss: 0.676914
Epoch: 1 Batch: 3 Loss: 0.645690
Epoch: 1 Batch: 4 Loss: 0.600543
Epoch: 1 Batch: 5 Loss: 1.113196
Epoch: 1 Batch: 6 Loss: 1.253301
Test set: Average loss: 0.7283, Accuracy: 33/62 (53%)
Epoch: 2 Batch: 1 Loss: 0.782749
Epoch: 2 Batch: 2 Loss: 0.695042
Epoch: 2 Batch: 3 Loss: 0.691786
Epoch: 2 Batch: 4 Loss: 0.678307
Epoch: 2 Batch: 5 Loss: 0.670357
Epoch: 2 Batch: 6 Loss: 0.678115
Test set: Average loss: 0.6580, Accuracy: 33/62 (53%)
Epoch: 3 Batch: 1 Loss: 0.660232
Epoch: 3 Batch: 2 Loss: 0.659409
Epoch: 3 Batch: 3 Loss: 0.649239
Epoch: 3 Batch: 4 Loss: 0.624184
Epoch: 3 Batch: 5 Loss: 0.604772
Epoch: 3 Batch: 6 Loss: 0.608415
Test set: Average loss: 0.5719, Accuracy: 62/62 (100%)
Epoch: 4 Batch: 1 Loss: 0.577721
Epoch: 4 Batch: 2 Loss: 0.545457
Epoch: 4 Batch: 3 Loss: 0.498851
Epoch: 4 Batch: 4 Loss: 0.480942
Epoch: 4 Batch: 5 Loss: 0.455344
Epoch: 4 Batch: 6 Loss: 0.454041
Test set: Average loss: 0.3973, Accuracy: 60/62 (96%)
Epoch: 5 Batch: 1 Loss: 0.358905
Epoch: 5 Batch: 2 Loss: 0.287745
Epoch: 5 Batch: 3 Loss: 0.243422
Epoch: 5 Batch: 4 Loss: 0.265708
Epoch: 5 Batch: 5 Loss: 0.313908
Epoch: 5 Batch: 6 Loss: 0.101326
Test set: Average loss: 0.1962, Accuracy: 61/62 (98%)
Epoch: 6 Batch: 1 Loss: 0.263859
Epoch: 6 Batch: 2 Loss: 0.146602
Epoch: 6 Batch: 3 Loss: 0.103473
Epoch: 6 Batch: 4 Loss: 0.086840
Epoch: 6 Batch: 5 Loss: 0.016964
Epoch: 6 Batch: 6 Loss: 0.010667
Test set: Average loss: 0.0086, Accuracy: 62/62 (100%)
Epoch: 7 Batch: 1 Loss: 0.010494
Epoch: 7 Batch: 2 Loss: 0.008344
Epoch: 7 Batch: 3 Loss: 0.005243
Epoch: 7 Batch: 4 Loss: 0.005261
Epoch: 7 Batch: 5 Loss: 0.011112
Epoch: 7 Batch: 6 Loss: 0.087835
Test set: Average loss: 0.0127, Accuracy: 62/62 (100%)
Epoch: 8 Batch: 1 Loss: 0.048637
Epoch: 8 Batch: 2 Loss: 0.024537
Epoch: 8 Batch: 3 Loss: 0.005178
Epoch: 8 Batch: 4 Loss: 0.002008
Epoch: 8 Batch: 5 Loss: 0.006720
Epoch: 8 Batch: 6 Loss: 0.060085
Test set: Average loss: 0.0014, Accuracy: 62/62 (100%)
Epoch: 9 Batch: 1 Loss: 0.001239
Epoch: 9 Batch: 2 Loss: 0.066646
Epoch: 9 Batch: 3 Loss: 0.001352
Epoch: 9 Batch: 4 Loss: 0.003270
Epoch: 9 Batch: 5 Loss: 0.007558
Epoch: 9 Batch: 6 Loss: 0.003660
Test set: Average loss: 0.0127, Accuracy: 62/62 (100%)
Epoch: 10 Batch: 1 Loss: 0.016825
Epoch: 10 Batch: 2 Loss: 0.120332
Epoch: 10 Batch: 3 Loss: 0.006820
Epoch: 10 Batch: 4 Loss: 0.069929
Epoch: 10 Batch: 5 Loss: 0.007500
Epoch: 10 Batch: 6 Loss: 0.001166
Test set: Average loss: 0.0028, Accuracy: 62/62 (100%)
```

생각보다 너무 잘 학습되어서 깜짝 놀랐다. 다만 조금 걸렸던 부분은 테스트 셋과 트레이닝셋의 배경이 같아서 다른 배경에서도 잘 예측할까 궁금했다.

일단 나의 생각은 오버피팅되어 테스트셋의 배경이 변경되면 예측이 어려울 것이라고 생각하고 시도해보았다.

### 두번째 시도

학습데이터셋은 같은 배경으로 찍고 테스트 셋의 배경을 바꾸어 가며 수집하였다.

- 학습 데이터셋: 157
- 테스트 데이터셋: 34

### 결과 
어느정도 정확도가 증가하다가(64% -> 83%) 학습이 진행되면서 정확도가 다시 감소(83% -> 53%) 하였다.

예상로 오버피팅이 된 것 같다.
학습과정은 실수로 덤프하지 못하였다. 

### 세번쨰 시도

두번쨰 시도와 깉은 방법으로 데이터 셋을 수집하고 다시 시도해보았다.

- 학습 데이터셋: 172
- 테스트 데이터셋: 30

#### 결과
```
Epoch: 1 Batch: 1 Loss: 0.703790
Test set: Average loss: 0.6380, Accuracy: 22/30 (73%)
Epoch: 2 Batch: 1 Loss: 0.434076
Test set: Average loss: 0.5627, Accuracy: 20/30 (66%)
Epoch: 3 Batch: 1 Loss: 0.094061
Test set: Average loss: 0.6828, Accuracy: 22/30 (73%)
Epoch: 4 Batch: 1 Loss: 0.002223
Test set: Average loss: 1.1096, Accuracy: 22/30 (73%)
Epoch: 5 Batch: 1 Loss: 0.000455
Test set: Average loss: 1.0117, Accuracy: 22/30 (73%)
Epoch: 6 Batch: 1 Loss: 0.001119
Test set: Average loss: 1.1069, Accuracy: 22/30 (73%)
Epoch: 7 Batch: 1 Loss: 0.000200
Test set: Average loss: 1.1529, Accuracy: 22/30 (73%)
Epoch: 8 Batch: 1 Loss: 0.000124
Test set: Average loss: 1.1521, Accuracy: 22/30 (73%)
Epoch: 9 Batch: 1 Loss: 0.000723
Test set: Average loss: 1.1558, Accuracy: 22/30 (73%)
Epoch: 10 Batch: 1 Loss: 0.000423
Test set: Average loss: 1.1927, Accuracy: 22/30 (73%)
```

이번엔 운이 좋게 학습이 잘 되었다. 예측도 어느정도 정확도가 나왓으나, 증가하지 못했다.
이것역시 오버 피팅이 된것 같다는 생각이들었다.

### 네번째 시도

이번에는 테스트셋과 트레이닝셋을 모두 다른 배경으로 학습해보았다.(배경, 조명 위치도 변경해보면서)

- 학습 데이터셋: 187
- 테스트 데이터셋: 46

#### 결과
```
Epoch: 1 Batch: 1 Loss: 0.721018
Test set: Average loss: 0.6847, Accuracy: 27/46 (58%)
Epoch: 2 Batch: 1 Loss: 0.771784
Test set: Average loss: 0.7919, Accuracy: 27/46 (58%)
Epoch: 3 Batch: 1 Loss: 0.910422
Test set: Average loss: 0.7738, Accuracy: 19/46 (41%)
Epoch: 4 Batch: 1 Loss: 0.646221
Test set: Average loss: 0.7621, Accuracy: 19/46 (41%)
Epoch: 5 Batch: 1 Loss: 0.564862
Test set: Average loss: 0.5495, Accuracy: 31/46 (67%)
Epoch: 6 Batch: 1 Loss: 0.315956
Test set: Average loss: 0.4914, Accuracy: 36/46 (78%)
Epoch: 7 Batch: 1 Loss: 0.202533
Test set: Average loss: 0.7273, Accuracy: 27/46 (58%)
Epoch: 8 Batch: 1 Loss: 0.034503
Test set: Average loss: 1.0348, Accuracy: 24/46 (52%)
Epoch: 9 Batch: 1 Loss: 0.006013
Test set: Average loss: 0.9275, Accuracy: 33/46 (71%)
Epoch: 10 Batch: 1 Loss: 0.005301
Test set: Average loss: 1.0383, Accuracy: 32/46 (69%)
```
 
학습이 잘 되나 싶더니 또 어느순간 트레이닝셋에 오버피팅되는 것 같다. 

데이터의 다양성을 더 줘서 더 많은 종류의 데이터로 다시 시도해 봐야겠다.

### 마지막 시도 

<img src="/assets/post-assets/2019-01-19-my-first-cnn-project/capture.gif">

거의 모든 데이터를 다른 위치에서 찍을 수 있도록 움직이면서 데이터를 수집하였다.

- 학습 데이터셋: 188
- 테스트 데이터셋: 47

## 결과

```
Epoch: 1 Batch: 1 Loss: 0.687795
Test set: Average loss: 0.8108, Accuracy: 21/47 (44%)
Epoch: 2 Batch: 1 Loss: 0.740914
Epoch: 2 Batch: 2 Loss: 0.733544
Epoch: 2 Batch: 3 Loss: 0.697777
Epoch: 2 Batch: 4 Loss: 0.647917
Epoch: 2 Batch: 5 Loss: 0.611102
Epoch: 2 Batch: 6 Loss: 0.619349
Epoch: 2 Batch: 7 Loss: 0.652097
Test set: Average loss: 0.7010, Accuracy: 14/47 (29%)
Epoch: 3 Batch: 1 Loss: 0.561028
Epoch: 3 Batch: 2 Loss: 0.562631
Epoch: 3 Batch: 3 Loss: 0.516114
Epoch: 3 Batch: 4 Loss: 0.485835
Epoch: 3 Batch: 5 Loss: 0.450926
Epoch: 3 Batch: 6 Loss: 0.541222
Epoch: 3 Batch: 7 Loss: 0.689737
Test set: Average loss: 1.0924, Accuracy: 26/47 (55%)
Epoch: 4 Batch: 1 Loss: 0.564378
Epoch: 4 Batch: 2 Loss: 0.388238
Epoch: 4 Batch: 3 Loss: 0.505561
Epoch: 4 Batch: 4 Loss: 0.226835
Epoch: 4 Batch: 5 Loss: 0.578987
Epoch: 4 Batch: 6 Loss: 0.370516
Epoch: 4 Batch: 7 Loss: 0.355252
Test set: Average loss: 0.9801, Accuracy: 26/47 (55%)
Epoch: 5 Batch: 1 Loss: 0.354714
Epoch: 5 Batch: 2 Loss: 0.231484
Epoch: 5 Batch: 3 Loss: 0.318201
Epoch: 5 Batch: 4 Loss: 0.172871
Epoch: 5 Batch: 5 Loss: 0.198403
Epoch: 5 Batch: 6 Loss: 0.162258
Epoch: 5 Batch: 7 Loss: 0.194906
Test set: Average loss: 1.3565, Accuracy: 26/47 (55%)
Epoch: 6 Batch: 1 Loss: 0.537755
Epoch: 6 Batch: 2 Loss: 0.875094
Epoch: 6 Batch: 3 Loss: 0.173388
Epoch: 6 Batch: 4 Loss: 0.321832
Epoch: 6 Batch: 5 Loss: 0.271180
Epoch: 6 Batch: 6 Loss: 0.284695
Epoch: 6 Batch: 7 Loss: 0.326817
Test set: Average loss: 0.3649, Accuracy: 45/47 (95%)
Epoch: 7 Batch: 1 Loss: 0.149230
Epoch: 7 Batch: 2 Loss: 0.107245
Epoch: 7 Batch: 3 Loss: 0.153434
Epoch: 7 Batch: 4 Loss: 0.160735
Epoch: 7 Batch: 5 Loss: 0.142821
Epoch: 7 Batch: 6 Loss: 0.221091
Epoch: 7 Batch: 7 Loss: 0.087188
Test set: Average loss: 0.6452, Accuracy: 29/47 (61%)
Epoch: 8 Batch: 1 Loss: 0.105633
Epoch: 8 Batch: 2 Loss: 0.094373
Epoch: 8 Batch: 3 Loss: 0.027781
Epoch: 8 Batch: 4 Loss: 0.194711
Epoch: 8 Batch: 5 Loss: 0.075953
Epoch: 8 Batch: 6 Loss: 0.010384
Epoch: 8 Batch: 7 Loss: 0.016869
Test set: Average loss: 0.2055, Accuracy: 47/47 (100%)
Epoch: 9 Batch: 1 Loss: 0.156465
Epoch: 9 Batch: 2 Loss: 0.078115
Epoch: 9 Batch: 3 Loss: 0.302012
Epoch: 9 Batch: 4 Loss: 0.042025
Epoch: 9 Batch: 5 Loss: 0.148871
Epoch: 9 Batch: 6 Loss: 0.082512
Epoch: 9 Batch: 7 Loss: 0.034108
Test set: Average loss: 0.2895, Accuracy: 39/47 (82%)
Epoch: 10 Batch: 1 Loss: 0.076718
Epoch: 10 Batch: 2 Loss: 0.080760
Epoch: 10 Batch: 3 Loss: 0.081116
Epoch: 10 Batch: 4 Loss: 0.070538
Epoch: 10 Batch: 5 Loss: 0.067576
Epoch: 10 Batch: 6 Loss: 0.056461
Epoch: 10 Batch: 7 Loss: 0.032610
Test set: Average loss: 0.1523, Accuracy: 44/47 (93%)
Epoch: 11 Batch: 1 Loss: 0.026311
Epoch: 11 Batch: 2 Loss: 0.012546
Epoch: 11 Batch: 3 Loss: 0.042134
Epoch: 11 Batch: 4 Loss: 0.011619
Epoch: 11 Batch: 5 Loss: 0.010464
Epoch: 11 Batch: 6 Loss: 0.009680
Epoch: 11 Batch: 7 Loss: 0.024475
Test set: Average loss: 0.1682, Accuracy: 43/47 (91%)
Epoch: 12 Batch: 1 Loss: 0.025384
Epoch: 12 Batch: 2 Loss: 0.011390
Epoch: 12 Batch: 3 Loss: 0.025029
Epoch: 12 Batch: 4 Loss: 0.012071
Epoch: 12 Batch: 5 Loss: 0.010480
Epoch: 12 Batch: 6 Loss: 0.006272
Epoch: 12 Batch: 7 Loss: 0.009081
Test set: Average loss: 0.1429, Accuracy: 43/47 (91%)
Epoch: 13 Batch: 1 Loss: 0.039017
Epoch: 13 Batch: 2 Loss: 0.011924
Epoch: 13 Batch: 3 Loss: 0.004064
Epoch: 13 Batch: 4 Loss: 0.004866
Epoch: 13 Batch: 5 Loss: 0.044984
Epoch: 13 Batch: 6 Loss: 0.003499
Epoch: 13 Batch: 7 Loss: 0.002726
Test set: Average loss: 0.1349, Accuracy: 47/47 (100%)
Epoch: 14 Batch: 1 Loss: 0.014280
Epoch: 14 Batch: 2 Loss: 0.027584
Epoch: 14 Batch: 3 Loss: 0.027804
Epoch: 14 Batch: 4 Loss: 0.004797
Epoch: 14 Batch: 5 Loss: 0.010686
Epoch: 14 Batch: 6 Loss: 0.031061
Epoch: 14 Batch: 7 Loss: 0.024077
Test set: Average loss: 0.1791, Accuracy: 43/47 (91%)
Epoch: 15 Batch: 1 Loss: 0.002389
Epoch: 15 Batch: 2 Loss: 0.004827
Epoch: 15 Batch: 3 Loss: 0.031694
Epoch: 15 Batch: 4 Loss: 0.003825
Epoch: 15 Batch: 5 Loss: 0.006952
Epoch: 15 Batch: 6 Loss: 0.003425
Epoch: 15 Batch: 7 Loss: 0.006715
Test set: Average loss: 0.1674, Accuracy: 43/47 (91%)
```

확실히 데이터가 다양하니 오버피팅 없이 학습이 잘 된것 같다.
(물론, 배경에 대해서만..)

## 결론 

데이터가 너무 편향적인 데이터만으로만 수집되면 학습이 오버피팅되어 제한적이게 동작할 수 밖에 없다. 따라서 학습 데이터를 수집할 경우 많은 종류의 데이터와 많은 양의 데이터가 중요한 것 같다.

github-repo: [https://github.com/J911/binary-classification-CNN](https://github.com/J911/binary-classification-CNN)

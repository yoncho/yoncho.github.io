---
layout: post
title: Object Detection Network [1] RCNN계열 | RCNN, SPPNet, Fast RCNN, Faster RCNN
subtitle: "Region Proposal with Convolution Neural Network | RCNN"
type: "RCNN"
createDate: 2021-03-19
od: true
text: true
author: "yoncho"
post-header: false
header-img: ""
order: 5
---


## RCNN 계열
RCNN은 Region with CNN으로써, Region Proposal(영역 추정)을 기반으로한 Object Detection Network이다.  
계열을 순서대로 나열하면,  
RCNN -> SPPNet -> Fast RCNN -> Faster RCNN 순이다.  
이번 블로그에서는 위 네개의 RCNN들을 설명할 것이다.  
그럼 시작하자 (부릉부릉)~  

## RCNN (Region Proposal with CNN)

![rcnn](https://user-images.githubusercontent.com/44021629/111743898-b453dc00-88cd-11eb-95c3-5e9764e07d4b.PNG)

RCNN은 영역을 추정해주는 stage1과 추정한 영역으로 검출을 수행하는 stage2로 나뉘어져있다.  
stage1에서는 Region Proposal(Selective Search)를 적용해 2000개의 서로다른 region영역을 뽑아온다.  
하지만,, CNN에 input img로 활용하기 위해서는  
반드시, 이미지의 크기가 고정(227x227)되어있어야한다.  
그래서 뽑아온 region영역을 crop과 wrap을 적용해 고정크기로 바꿔준다.  
stage2에서는 받아온 2000개의 고정크기 region 영역들을 미리 훈련된 AlexNet을 통과시켜서  
1개의 input img에서 Feature Map들을 뽑아온다.  
그리고나선 Classification(분류)작업과 Regression(Bounding box)를 진행한다.  
자세히 보면 RCNN의 경우에는 Classification에서 SVM Classifier라는 새로운 알고리즘으로 적용시킨다.  
하지만 SVM을 바로 적용하면, 적중률이 떨어져서, FC Layer를 처음에 Softmax로 훈련시키고  
나온 weight(가중치)를 가지고, 그다음에는 SVM으로 진행한다.  

![rcnn2](https://user-images.githubusercontent.com/44021629/111744635-df8afb00-88ce-11eb-8c4e-b5723e444b71.PNG)
위 사진을 보면, region proposal로 뽑아온 2000개의 region영역들을 각각 고정된 img size(227 x 227)로 바꿔준 후,  
cnn을 들어가서 bounding box regression과 svm의 결과가 나오는것을 볼 수 있다.  

##### 장점과 단점  
장점 :  
동시대의 다른 알고리즘 대비 매우 높은 Detection정확도를 갖고있었다.  
Region Proposal 기반 성능을 입증했다.  
Deep Learning 기반 객체검출 성능 입증했다.  
**단점** :  
너무 느린 Detecion 시간, 2000개의 region 영역들을 각각 cnn을 들어가서 처리해줘야하므로 오랜 시간이 걸린다.  
그리고 너무 복잡한 아키텍처 및 학습 프로세스들이 있다.  


<hr>

<code>#데이터 세트 #MS_COCO #재미있다 :D</code>

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

# 1. RCNN (Regions with CNN)

![rcnn](https://user-images.githubusercontent.com/44021629/111747070-31815000-88d2-11eb-97f3-f500848b906f.PNG)


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

![rcnn2](https://user-images.githubusercontent.com/44021629/111747093-380fc780-88d2-11eb-9cbb-90629887ad25.PNG)  
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

# 2. SPP(Spatial Pyramid Pooling) Net | RCNN 단점을 보완

SPPNet은 RCNN의 개선방안, Region Proposal된 2000개의 영역을 cnn으로 넣어선 안된다.  
SPP는 CNN상에서 Image classification에서 서로 다른 이미지 크기를 고정된 크기로 변환하는 기법이다.  

![spp](https://user-images.githubusercontent.com/44021629/111754076-ec155080-88da-11eb-9479-c92ff1db734e.PNG)
RCNN을 보완하기위해서 Region Proposal로 나온 2000개의 region영역들을 CNN에 넣지않고,  
원본 img를 CNN에 넣어서 나온 Feature Map에 올려보기로 했다.  
하지만, Region영역들의 크기가 재각각이라서 해당 Feature Map들을 1D Flattened FC Input으로 고정적인  
길이의 Input Data를 만들려면 중간에 정재과정이 필요하다.  
이 정재과정을 **SPP**가 하는 것이다.  

SPP(Spatial Pyramid Pooling)는    
Back of Visual Words(BOW)를 기반으로 나온 SPM(Spatial Pyramid Mating)에서 Max-Pooling을 적용한 것이다.  
SPM은 Feature Map에 여러 분면으로 나눈 것들에서 정보를 갖고는 방식인데.  
Max-Pooling을 적용하면, 각 분면에서 최댓값을 갖고오게된다.   
여기서 SPM 및 SPP의 최대 장점은 Input Data의 크기와는 상관없이 **나누는 분면에 따라** 결과 크기가 달라진다는 것이다.  
즉, 기존에 영향을 받았던 서로다른 region 영역의 크기 문제에서 벗어날 수 있는 것이다.  

![spm-mxp](https://user-images.githubusercontent.com/44021629/111754845-cb012f80-88db-11eb-8140-cb69af359906.PNG)
![spp-fcl](https://user-images.githubusercontent.com/44021629/111756623-c473b780-88dd-11eb-87dd-a2c3c39a8920.PNG)
그렇게 되면 각 분면에서 나온 최대값들을 모아서 하나의 고정된 크기의 1D FC Input Data를 만들 수 있는 것이다.  

![spp-net](https://user-images.githubusercontent.com/44021629/111810927-df641d00-8919-11eb-8ad8-c521cd883382.PNG)
최종적인 SPPNet의 구조이다.  
Input Img에 대해서 이미지 자체를 CNN으로 집어 넣음과 동시에  
이미지에 대한 Region Proposal(Selective Search)를 진행한다.  
Img가 들어간 CNN에서의 Feature Map들 위에, Selective Search의 결과물로 나온 2000개의 Region들을 Mapping 시키고  
이를 SPP Layer에 넣어서 고정적인 FC Input Data로 만들어준다.  
그러면 FC Layer를 거쳐서 classification(분류)를 진행하고,  
동시에 Bounding Box Regression을 한다.  

##### RCNN vs SPPNet
![rccvssppnet](https://user-images.githubusercontent.com/44021629/111811322-45e93b00-891a-11eb-90f8-19d4c46d65b6.png)
SPPNet이 시간을 엄청 단축시킬 수 있음을 확인할 수 있고, 또 서로 다른 Region들에 대해 고정적인 FC Input data로 만들 수 있다.  

<hr>

# 3. Fast RCNN | SPPNet을 보완




<hr>




<code>#데이터 세트 #MS_COCO #재미있다 :D</code>

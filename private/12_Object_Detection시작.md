---
layout: post
title: Object Detection 시작_Region Proposal, Selective Search, IOU, NMS, mAP
subtitle: "객체 검출의 시작"
type: "Object Detection"
createDate: 2021-03-13
private: true
text: true
author: "yoncho"
post-header: false
header-img: ""
order: 12
---


# Object Detection 객체 검출

영상, 이미지에서 물체(객체)를 검출 할 수 있다는 것은 어마무시한 행위이다.  
자율주행, 주민등록증 인식, 자동차 번호판 인식등등으로 수많은 행위들을 할 수 있기 때문이다.  
OpenCV(영상처리)를 하고 나서 진행하는 post이기 때문에, OpenCV를 중간중간에 사용하며  
이 파트에서 알고있어야할 중요한 라이브러리는 구글이 풀어놓은 인공지능 라이브러리 괴물, TensorFlow(텐서플로우)입니다.  
물론 PyTorch(파이토치)라는 아이도 유명합니다. 하지만 필자는 텐서플로우를 했고, 자격인증서까지 취득했으므로  
텐서플로우 코드들로 진행겠습니다.  
그럼 지금부터 객체 검출, 오브젝트 디텍션을 시작합니다.  


## Classification / Localization / Detection / Segmentation
Classification[분류] : 이미지상에서 객체가 무엇인지 판별합니다.  
Localization[발견] : 이미지상에서 객체 하나를 bounding box로 지정하여 찾습니다.    
Detection[발견] :  이미지상에서 여러개의 객체를 bounding box로 지정하여 찾습니다.    
Segmentation[분할] : 이미지상에서 여러개의 객체를 pixel단위로 라벨링해서 그룹화시켜서 물체 모양대로 검출한다.    
디텍션보다 발전한 형태이다.   
![computer-vision-problem](https://user-images.githubusercontent.com/44021629/111021413-67748f00-840f-11eb-8984-0beb6ee05f27.png)


## Object Detection History

![object-detection-history](https://user-images.githubusercontent.com/44021629/111021479-f08bc600-840f-11eb-8672-c9509b1e6481.png)

Alex Net을 기점으로 객체 검출에 Deap Learning을 기초로한 method들이 나왔다.  
(two-stage, rcnn계열)RCNN -> SPPNet -> Fast RCNN -> Faster RCNN 순으로 발전했으며,  
two-stage는 첫번째 층에서 영역을 추천해주고 두번째 층에서 분류과 bounding box처리를 진행한다.  
(one-stage)YOLO , SSD, Retina-Net 이 있다.  
one-stage는 영역 추정(Region Proposald)와 분류(Classification)과 Bounding Box를 함께 처리한다.  
  
현재 YOLO 알고리즘이 Real-Time에서 성능이 좋아 자주 쓰이며,  
Real-Time에 한계는 있으나 성능이 가장 좋은 모델은 Retina-Net이다.   

## Object Detection 구성 요소

### 1. 영역 추정 , Region-Proposal (Selective Search)
초기 객체 검출에서 자주 쓰인 방식은 sliding window 방식이다.  
특정 사이즈의 커널을 이미지상에서 천천히 움직여 해당 커널안의 픽셀들의 정보를 갖고 처리한다.  
슬라이딩 윈도우는 추후, Convolution 컨벌루션 연산을 하기위해 쓰이며  
우리가 알아야할 간단한 슬라이딩 윈도우의 정보는 kernel(=window)와 anchor point(중심점) 정도가 있다.  

![sliding_window_example](https://user-images.githubusercontent.com/44021629/111021638-36955980-8411-11eb-8e03-f44433362e01.gif)

방향성은 다르지만 슬라이딩 윈도우보다 개선된 객체를 찾는 방법,   
영역 추정 Resion Proposal 으로 Selective Search가 등장한다.  
**Selective Search**는 빠른 검출(Detection)과 높은 재현율(Recall) 예측 성공을 한다.  
즉, 객체가 있을만한 영역을 잘 찍어준다.  
특징으론, 이미지내의 모든 객체의 크기를 고려하며 색상.재질.크기.무늬 등 다양한 조건을 고려해준다.  
이미지에서 위 조건들로 유사한 영역끼리 그룹화시켜준다.  
그리고 빠른 속도를 자랑한다.  

![step3-660x304](https://user-images.githubusercontent.com/44021629/111021892-b5d75d00-8412-11eb-8904-e0ff74761547.png)
순서 :  
1. 개별 segment된 모든 부분들을 모두 bounding box처리를 해서 region proposal리스트에 추가한다.  
2. 개별 segment된 부분들을 가지고 컬러, 무늬, 크기, 형태에 따라 유사도가 비슷한 것들끼리 그룹화한다.  
3. 위 1, 2번작업들을 반복 수행한다. 
4. 그러다 보면 최종적으로 이미지내에서 객체를 탐지하는 bounding box들이 남는다.  

<hr>

### 2. Detection 을 위한 Deap Learning Network 구성  
![1_V-zPIFD5Lhk-9-DVu3UHgw](https://user-images.githubusercontent.com/44021629/111024237-e7efbb80-8420-11eb-9a9d-1556cb55850a.png)

back-end로써 뒤에서 더 정확히 다루겠지만, 이부분에서는 Feature Extraction (특징 추출), Network Prediction(네트웍 예측) 등이 이뤄진다.  
앞서 설명한 object detection의 history에 등장했던 모델들이 이부분에서 쓰이며,  
대표적으로 RCNN계열, SSD, Retina-NET이 있고, YOLO도 사용하지만 앞 모델들과는 독자적인 길을 걷고있는 아이이다.  
YOLO를 주의깊게 봐야한다. (언제 어마무시하게 성장할지 모르는 아이다. )  
back-bone으로는 Resnet을 범용으로 사용하며, Tensorflow API를 사용할떈 Inception/ Mobile Net을 쓴다.  

### 3. Detection에서 사용하는 평가 지표
IOU, NMS, mAP가 존재한다.    

#### 3-1 IOU (Interest Over Unit)
IOU는 모델이 예측한 결과가 실제값과 얼마나 유사한지 확인해보는 표이다.  

![img](https://user-images.githubusercontent.com/44021629/111022244-f20bbd00-8414-11eb-9b03-43a50c86a348.png)

![3630397627c2537326e88761abdb6ca4](https://user-images.githubusercontent.com/44021629/111022295-36975880-8415-11eb-86a7-195bb940ee88.png)

정확히는 실제 객체의 bounding box와 예측한 bounding box를 가지고 교집합 영역을 합집합 영역으로 나눈 결과값이다.  
1에 가까울수록 예측한 bounding box가 실제 객체의 bounding box와 차이가 없다.  

<code>IOU 함수</code>

```js
import numpy as np

def compute_iou(prec_box, answ_box):
    x1 = np.maximum(prec_box[0], asnw_box[0])
    y1 = np.minimum(prec_box[1], answ_box[1])
    x2 = np.minimum(prec_box[2], answ_box[2])
    y2 = np.maximum(prec_box[3], answ_box[3])

    intersection = np.maximum(x2 - x1, 0) * np.maximum(y2 - y1, 0)

    prec_box_area = (prec_box[2] - prec_box[0]) * (prec_box[3] - prec_box[1])
    answ_box_area = (answ_box[2] - answ_box[0]) * (answ_box[3] - answ_box[1])

    union  = prec_box_area + answ_box_area - intersection

    iou = intersection / union

    return (iou)
```
> - prec_box : 예측한 bounding box, 박스 좌측 상단 좌표(prec_box[0], prec_box[1]), 박스 우측 하단 좌표(prec_box[2], prec_box[3])  
> - answ_box : 실제 bounding box
> - intersection : 예측한 박스와 실제 박스의 교집합 영역
> - union : 예측한 박스와 실제 박스의 합집합 영역


<hr>

#### 3-2 NMS (Non-Max Supression)

NMS는 Detect된 bounding box중에서 가장 적합한 box만 남기고 모두 제거하는 필터역할을 한다.  

<img width="600" alt="nms-1" src="https://user-images.githubusercontent.com/44021629/111023976-7c591e80-841f-11eb-9bd0-01ec88f8236d.png">

NMS를 적용하기 전 이미지를 보면,  
Object Detection알고리즘은 object가 있을만한 위치에  
많은 Detection(즉, bounding box 처리)를 하는 경향이 있다. 
그래서 우리는 불필요한 box들을 제거해줄 필요가 있다.   
NMS를 적용해 우측 이미지를 보면, bounding box가 하나만 남아있는 걸 볼 수 있다.  

![non_max_b_boxes](https://user-images.githubusercontent.com/44021629/111023281-6b0e1300-841b-11eb-9009-3b0788d94df1.png) 

NMS는 confidence threshold(예측 임계값), iou threshold(iou 임계값)을 받아온다.  
수행 로직은 다음과 같다.  
1. Detected된 bounding box별로 특정 confidence threshold 이하의 값을 갖는 bounding box는 모두 제거한다.  
2. 가장 높은 confidence score를 가진 box순으로 내림차순 정렬을 한다.  
3. 높은 confidence score를 가진 box와 겹치는 또 다른 box를 정렬 순서대로 조사하여 iou가 특정 threshold(임계값)보다 큰 box들은 모두 제거한다.  
4. 2, 3번 과정을 정렬순서대로 진행한다.  
5. 남아있는 객체 bounding box를 선택한다.  

<hr>

#### 3-3 mAP (mean Average Precision)
실제 object가 detect된 재현율(Recall)의 변화에 따른 정밀도(Precision)의 값을 평균한 성능 수치이다.  
mAP를 하기위해선 **정밀도**와 **재현율**을 잘 알고있어야한다.  

![image](https://user-images.githubusercontent.com/44021629/111023455-7a419080-841c-11eb-9dd0-b56afc8121ee.png)
> - TN : 틀린 값) 실제값과 동일하게 예측한 것이다. 
> - FP : 실제값과 다르게 예측한 것이다.  
> - FN : 실제값을 예측하지 못 한것이다.  
> - TP : 맞는 값) 실제값을 예측 성공한 것이다.  

##### 정밀도 (= TP / (FP + TP))
예측을 positive로한 대상중에서 예측과 실제값이 positive로 일치한 데이터의 비율이다.  
즉, 예측한 것중에 실제값과 일치한 데이터의 비율을 의미한다.  

##### 재현율 (= TP / (FN + TP))
실제값이 positive한 대상중에서 예측과 실제값이 positive로 일치한 데이터의 비율이다.  
즉, 실제값 중에서 예측에 성공한 실제 데이터의 비율을 의미한다.  

<hr>

confidence threshold(임계값)에 따른 재현율과 정밀도의 변화이다.  
![1_mQ6a-tiHstNaC3lTxSibkA](https://user-images.githubusercontent.com/44021629/111023713-f7213a00-841d-11eb-9b61-75a44ec93e9e.jpeg)
임계값이 높으면, bounding box를 만들 확률이 낮아지고, 정밀도는 상승하며, 재현율은 하락한다.  
임계값이 낮으면, bounding box를 만들 확률이 높아지고, 정밀도는 하락하며, 재현율은 상승한다.  
임계값과 정밀도는 비례하며, 재현율과는 반비례한다.  

![다운로드](https://user-images.githubusercontent.com/44021629/111023794-50896900-841e-11eb-95cd-161afe0c85f6.png)
재현율과 정밀도 그래프를 보면  
빨간색 공간 영역이 AP(평균 예측율)이다.  
객체 하나에 대한 AP가 이렇게 구해진다.  
**우리가 구하는 이미지에서의 mAP는 이미지 상의 모든 객체 AP에 대한 평균이다.**

<hr>

<code>#객체 검출 #재미있다 :D</code>

---
layout: post
title: Object Detection Dataset [2] | MS COCO
subtitle: "MS_COCO_DATASET"
type: "Object Detection"
createDate: 2021-03-14
private: true
text: true
author: "yoncho"
post-header: false
header-img: ""
order: 14
---


### MS COCO DATASET
![coco](https://user-images.githubusercontent.com/44021629/111067254-cd900d80-8506-11eb-8cca-ef69adf00013.png)
MS COCO 데이터 세트는 PASCAL VOC 데이터세트 보다 많은  
80개의 Object Category를 갖고있으며,  
300K Img, 1.5million object를 가지고있어서,  
평균적으로 1개의 img안에 5개의 object들이 들어가있는 데이터 세트이다.  
특히, MS-COCO 2017은 Tensorflow가 나온해의 가장 많은 카테고리를 갖고있는  
데이터 세트였기 때문에,  
Tensorflow Object Detection API & 많은 오픈소스계열 패키지들은  
COCO Dataset으로 pretrained된 모델을 제공하고있다.  

![1_IdOxuGM1UyEevaK1esj50g](https://user-images.githubusercontent.com/44021629/111067370-42fbde00-8507-11eb-910c-9cef82a1d051.png)
COCO 역시,  
Classification/Detection,  
Semantic & Instance Segmentation 을 한다.  

[홈페이지]  
https://cocodataset.org/#home  

### COCO DATASET 구성
![캡처](https://user-images.githubusercontent.com/44021629/111067955-fe257680-8509-11eb-9f23-96e3fe69143c.PNG)
이와 같이 학습용, 검증용 파일에 대해서는 json파일안에 모든 정보가 함께 들어가있다.  
테스트용은 훈련시킨 모델을 테스트해보는 용도이므로 json파일에 정보가 없다.  

### COCO DATASET 사용 과정

필요조건 :  
1. COCOAPI를 다운로드 받고, pycocotools 셋업 필요  
2. cocoapi/PythonAPI로 들어간 뒤, Make install 수행  
3. site-packages에 로드 되는지 확인  

```js
from pycocotools.coco import COCO
import numpy as np
```

데이터 세트 준비 :  
다운로드 위치 : objectPrj/data/coco/    
1. COCO 데이터 다운로드 : http://cocodataset.org/#download  
2. 2017ver. Train img file : wget http://images.cocodataset.org/zips/train2017.zip  
3. 2017ver. Val img file : wget http://images.cocodataset.org/zips/val2017.zip  
4. 2017ver. Train/Val annotation file : http://images.cocodataset.org/annotations/annotations_trainval2017.zip  


<code>COCO API 활용하기 위한, annotation 파일을 로드 [code]</code>

```js
dataDir='../../data/coco'
dataType='val2017'
annFile='{}/annotations/instances_{}.json'.format(dataDir,dataType)
coco=COCO(annFile)
```

<code>Category 정보 가져오기 [code]</code>

```js
cats = coco.loadCats(coco.getCatIds())
```
> {'supercategory': 'person', 'id': 1, 'name': 'person'},  
> {'supercategory': 'vehicle', 'id': 2, 'name': 'bicycle'}...  
> 이런 형태의 카테고리 id별로 세부 정보들을 가져온다.   
  
[추후 더 정확한 내용들을 추가할 예정입니다.]  
  



<hr>

<code>#데이터 세트 #MS_COCO #재미있다 :D</code>

---
layout: post
title: OpenCV 이미지 변환[2]
subtitle: "특징 검출과 데이터 해석을 위한 이미지 전처리"
type: "OpenCV"
createDate: 2021-01-27
opcv: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 8
---

# OpenCV에서 이미지 변환, 이미지 전처리 작업
앞절 이미지 변환[1]에서는 **이미지 확대 & 축소**, **이미지 크기 조절**, **이미지 회전**에 대한 내용을 다뤘으며,  
이미지 변환[2]에서는 영상처리 전처리에 있어서 **중요한** 내용들로  
꼭 다 알아가야한다.  


## 4. 기하학적 변환
기하학전 변환이란 것은 이미지를 인위적으로 변화시킨다는 것을 의미하고, 이미지를 구성하는 좌표들의 위치를 재배치한다고 볼 수 있다.  
기하학적 변환으론 **아핀(affine)변환**, **원근 변환**이 있다.  
> 아핀 변환 : 2x3행렬을 사용, 행렬 곱셈에 벡터 합을 활용 표현 할 수 있는 변환
> 원근 변환 : 3x3행렬을 사용, 호모그래피(뒤틀림, 오목함)로 모델링 할 수 있는 변환 

### 아핀 변환 (Affine)
원래 아핀 변환 행렬의 기본 형태는 원근 변환과 동일한 3x3 행렬이다.  
하지만, 아핀 변환은 **선의 수평성을 유지**하며, **변환 후에도 평행함을 유지**한다.  
아핀 식은 아래와 같다.  
![affine_sik](https://user-images.githubusercontent.com/44021629/106401528-f634da00-6467-11eb-8729-58189f0af431.jpg)

x1, y1은 변환 전 /  x2, y2는 변환 후 이미지의 좌표값이다.  
변환을 하기 위해선 a00, a01, a10, a11, b0, b1 총 6개의 미지수를 알아야한다.  

![affine](https://user-images.githubusercontent.com/44021629/106401786-55dfb500-6469-11eb-90c3-f29baf396418.png)
아핀 변환은 임의의 3개 좌표를 매핑해서 기하학전 변환을 수행한다.  
예를 들어, 위 사진에서 a, b, c에 대한 임의의 좌표 a`와 b`, c`을 가지고  
**아핀 맵 행렬**로 계산해서 아핀 변환을 수행한다.  
그러면 오른쪽 그림처럼 기하학적 변환이 발생한다.  
여기서 중요한게 **아핀 맵 행렬**을 기존좌표와 임의의 좌표들을 가지고 만드는게 중요하다.  
아핀 변환은 6개의 미지수를 구하기위해 3개 픽셀 좌표를 재매핑해 아핀 맵 행렬로 계산하는 것을 의미한다.  

<code>아핀 맵 행렬 생성 함수</code>

```js
M = cv2.getAffineTransform(
  src,
  dst
)
```
> src : 변환전 이미지의 좌표 3개
> dst : 변환후 이미지의 좌표 3개


<code>아핀 변환 함수</code>

```js
dst = cv2.warpAffine(
  src,
  M,
  dsize,
  dst = None,
  flags = None,
  borderMode = None,
  borderValue = None
)
```
> src : 입력 이미지
> M : 생성한 아핀 맵 행렬
> dsize : 출력 이미지 크기
> dst : 출력 이미지
> flags : 보간법
> borderMode : 테두리 외삽법,
> borderValue : 테두리 색상, 변환후 발생하는 빈 공간을 채울 색


<hr>

### 원근 변환
원근 변환은 앞서 말했다시피 3x3행렬이며, 아핀변환과 다른 점은  
수평성이 유지되지않는 다는 점이다.  

![percpect](https://user-images.githubusercontent.com/44021629/106402099-4b261f80-646b-11eb-9582-aa515fa1cee8.jpg)
위 식을 보면, 아핀과 다르게 세 번째 행의 값이 미지수 a20, a21, 1로 변해  
구해줘야할 미지수가 6개에서 8개로 늘어났다.  
추가된 2개의 미지수 a20, a21이 아핀과 원근의 차이점이 된다.  

![per_aft](https://user-images.githubusercontent.com/44021629/106402656-da343700-646d-11eb-8bcc-03bcf4ddbd06.png)
원근 변환은 아핀 변환과 동일한 방법으로, 기존 좌표에서 임의의 좌표 4개 a`, b`, c`,d`까지 이동한걸**원근 맵 행렬**로 계산한 후, 아핀 변환을 진행한다.  

<code>원근 맵 행렬 생성 함수</code>

```js
M = cv2.getPerspectiveTransform(
  src,
  dst
)
```
> src : 변환전 이미지 좌표 4개
> dst : 변환후 이미지 좌표 4개

<code>원근 변환 함수</code>

```js
dst = cv2.warpPerspective(
  src,
  M,
  dsize,
  dst = None,
  flags = None,
  borderMode = None,
  borderValue = None
)
```
> M : 아핀변환과 다른 행렬을 갖고있는 원근 맵 행렬이다.

<hr>

### 아핀 변환과 원근 변환 예제

<code>아핀 변환과 원근 변환</code>

```js
import cv2
import numpy as np

src = cv2.imread("rena.png")
height , width, _ = src.shape

src_pst_pers = np.array([[0.0, 0.0], [width, 0.0], [width, height], [0.0, height]], dtype = np.float32)
dst_pst_pers = np.array([[50.0, 50.0], [200, 30.0], [width-80.0, height-50.0], [0.0, height-40.0]], dtype = np.float32)
src_pst_aff = np.array([[0.0, 0.0], [width, 0.0], [width, height]], dtype = np.float32)
dst_pst_aff = np.array([[50.0, 50.0], [200, 30], [width, height-50]], dtype = np.float32)
M_pers = cv2.getPerspectiveTransform(src_pst_pers, dst_pst_pers)
M_aff = cv2.getAffineTransform(src_pst_aff, dst_pst_aff)

dst_pers = cv2.warpPerspective(src, M_pers, (width, height), borderValue=(0,0,0,0))
dst_aff = cv2.warpAffine(src, M_aff, (width, height), borderValue=(0,0,0,0))

cv2.imshow("dst_pers", dst_pers)
cv2.imshow("dst_aff", dst_aff)

cv2.waitKey(0)
cv2.destroyAllWindows()
```
![aff_pers_rena](https://user-images.githubusercontent.com/44021629/106402959-8b879c80-646f-11eb-8cb1-251b4ad8f3de.PNG)


<hr>
<code>#영상처리 #이미지 변환</code>

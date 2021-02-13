---
layout: post
title: OpenCV 이미지 변환[2]_기하학적 변환, 모폴로지 변환 및 연산
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
**기하학적 변환**, **모폴로지 변환**, **모폴로지 연산**에 대해 배운다.   


## 4. 기하학적 변환
기하학전 변환이란 것은 이미지를 인위적으로 변화시킨다는 것을 의미하고, 이미지를 구성하는 좌표들의 위치를 재배치한다고 볼 수 있다.  
기하학적 변환으론 **아핀 변환**, **원근 변환**이 있다.  
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
예를 들어, 위 사진에서 a, b, c에 대한 임의의 좌표 a'와 b', c'을 가지고  
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

### 원근 변환 (Perspective)
원근 변환은 앞서 말했다시피 3x3행렬이며, 아핀변환과 다른 점은  
수평성이 유지되지않는 다는 점이다.  

![percpect](https://user-images.githubusercontent.com/44021629/106402099-4b261f80-646b-11eb-9582-aa515fa1cee8.jpg)
위 식을 보면, 아핀과 다르게 세 번째 행의 값이 미지수 a20, a21, 1로 변해  
구해줘야할 미지수가 6개에서 8개로 늘어났다.  
추가된 2개의 미지수 a20, a21이 아핀과 원근의 차이점이 된다.  

![per_aft](https://user-images.githubusercontent.com/44021629/106402656-da343700-646d-11eb-8bcc-03bcf4ddbd06.png)
원근 변환은 아핀 변환과 동일한 방법으로, 기존 좌표에서   
임의의 좌표 4개 a', b', c', d'까지 이동한걸**원근 맵 행렬**로 계산한 후,  
아핀 변환을 진행한다.  

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

## 5. 모폴로지 변환
모폴로지 변환은 이미지를 형태학적 관점으로 접근한다.  
주로 영상 내 픽셀값 대체에 사용하는데.  
모폴로지 변환은 **노이즈 제거**, **요소 결합 및 분리**, **강도 피크 검출** 등에 이용할 수 있다.  
단연, **노이즈 제거**할 수 있다는 점이 모폴로지 변환을 많이 사용하는 주 이유이다.  
기본적인 모폴로지 변환으로는 **팽창**과 **침식**이 있다.  
두개 모두 이미지와 커널의 컨벌루션 연산이며, 두개의 기본 연산을 기반으로  
복잡하고 다양한 모폴로지 연산을 구현할 수 있다.  
**모폴로지 변환은 전처리, 후처리과정에서 가장 많이 사용하는 변환**이다.  

### 팽창
커널 영역 안에 존재하는 모든 픽셀의 값을 커널 내부의 **극대값 (local Maximum)**으로 대체한다.  
**구조요소**를 활용해 이웃한 픽셀을 최대값으로 대체한다.  
팽창 연산을 수행하면, 어두운 부분 줄어들고 밝은 부분은 늘어난다.  
커널의 크기와 반복 횟수에 따라 밝은 부분이 늘어나면서  
**스펙클(spackle)**이 커지며 객체 내부의 **홀(holes)**이 사라진다.  
**노이즈 제거 후 줄어든 크기를 복구할때** 주로 사용한다.  

> 스펙클 : 반점, 얼룩
> 홀 : 입력 이미지에서 매핑을 수행하고 나온 결과 이미지에 생기는 빈 공간  

### 침식
커널 영역 안에 존재하는 모든 픽셀의 값을 커널 내부의 **극소값 (local Minimum)**으로
대체한다.  
**구조요소**를 활용해 이웃한 픽셀을 최소값으로 대체한다.  
침식 연산을 수행하면, 어두운 부분은 늘어나고 밝은 부분은 줄어든다.  
커널의 크기와 반복 횟수에 따라 어두운 부분이 늘어나면서  
**스펙클**이 사라지고, 객체 내부의 **홀**이 커진다.  
**노이즈 제거에 주로 사용한다** 

<hr>

모폴로지 변환은 **커널의 크기**와 **커널의 형태(구조 요소)**에 영향을 크게 받는다.  
여기서 커널의 형태 즉, 구조요소에는 직사각형, 타원, 십자모양이 존재한다.  

<code>구조 요소 생성 함수</code>

```js
kernel = cv2.getStructuringElement(
  shape,
  ksize,
  anchor = None
)
```
> shape : 커널의 형태 flag이다. 직사각형(Rect), 십자가(Cross), 타원(Ellipse)가 있다. 자세한건 아래 커널의 형태 flag에 나와있다.    
> ksize : 커널의 크기  
> anchor = None : 고정점은 모폴로지 함수에서 위치 할당이 가능하기에, 이 함수에선 필수 매개변수가 아니다.  


<code>커널의 형태 flag, 구조요소 flag</code>

> - **cv2.MORPH_RECT** : 직사각형 형태, 커널 내부가 모두 1로 설정
> - **cv2.MORPH_CROSS** : 십자가 형태, 커널 내부가 모두 1로 설정
> - **cv2.MORPH_ELLIPSE** : 타원 형태, 커널의 높이와 너비를 축으로하고 커널 내부가 모두 1로 설정  

### 팽창함수와 침식함수

<code>팽창 함수</code>

```js
dst = cv2.dilate(
  src,
  kernel,
  anchor = None,
  iterations = None,
  borderType = None,
  borderValue = None
)
```

<code>침식 함수</code>

```js
dst = cv2.erode(
  src,
  kernel,
  anchor = None,
  iterations = None,
  borderType = None,
  borderValue = None
)
```
> src : 입력 이미지  
> kernel : 입력 형태, 구조 요소  
> anchor : 함수내에서 설정 가능    
> iterations : 반복 횟수  
> borderType : 테두리 외삽법  
> borderValue : 테두리 색상  

<hr>

<code>모폴로지 팽창 , 침식 </code>

```js
import cv2

src = cv2.imread("flower.jpg")

kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (3,3), anchor= (-1, -1))


dst_dilate = cv2.dilate(src, kernel, iterations=3)
dst_erode = cv2.erode(src, kernel, iterations=3)

cv2.imshow("src", src)
cv2.imshow("dst_dilate", dst_dilate)
cv2.imshow("dst_erode", dst_erode)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![erode_dilate](https://user-images.githubusercontent.com/44021629/106407631-e7f2b800-647f-11eb-85b7-5021bf7137ce.PNG)


<hr>

## 6. 모폴로지 연산
모폴로지 연산은 모폴로지 변환의 **팽창**과 **침식**을 기본 연산으로 사용해  
만들 수 있는 복잡하고 다양한 연산을 말한다.  
이미지가 이진화되어있다면, 팽창과 침식만으로도 충분히 좋은 결과를 얻을 수 있지만.. 
그레이스케일이나 다중채널 이미지의 경우 복잡한 연산을 필요로한다.  
그래서 그레이스케일과 다중채널 이미지의 처리를 위해 모폴로지 연산을 사용한다.  

<code>모폴로지 연산 함수</code>

```js
dst = cv2.morphologyEx(
  src,
  op,
  kernel,
  anchor = None,
  iterations = None,
  borderType = None,
  borderValue = None
)
```
> src : 입력이미지  
> **op** : 연산자 flag   
> kernel : 커널의 형태, 구조요소  
> iterations : 반복 횟수  
> borderType : 테두리 외삽법  
> borderValue : 테두리 색상  

여기서 주의 깊게 봐야할 매개변수는 **op(연산자)**이다.  
연산자는 모폴로지 변환 함수를 조합해 수행하는 복합연산자를 뜻한다.  
아래 플래그 정보가 있다.  
<code>연산자 플래그</code>

> - **cv2.MORPH_DILATE** : 팽창 연산
> - **cv2.MORPH_ERODE** : 침식 연산
> - **cv2.MORPH_OPEN** : 열림 연산, 침식 후 팽창 
> - **cv2.MORPH_CLOSE** : 닫힘 연산, 팽창 후 침식
> - **cv2.MORPH_GRADIENT** : 모폴로지 그레이디언트, 팽창img - 침시img
> - **cv2.MORPH_TOPHAT** : 탑햇 연산, 기본img - 열림img
> - **cv2.MORPH_BLACKHAT** : 블랙햇 연산, 닫힘img - 기본img
> - **cv2.MORPH_HITMISS** : 히트미스 연산, 모서리 검출


## 연산자 

### 1. 열림 연산 | 이미지에서 스펙클 제거 
팽창 연사자와 침식 연산자의 조합,  
침식 연산 적용 후 팽창 연산을 적용한다.  
**스펙클**이 사라지고 객체의 크기는 원래대로 돌아온다.  

### 2. 닫힘 연산 | 이미지에서 홀 제거
팽창 연산자와 침식 연산자의 조합,  
팽창 연산 적용 후 침식 연산을 적용한다.  
**홀**이 사라지면서 객체의 크기는 원래대로 돌아온다.  

### 3. 그레이디언트 연산 | 객체의 가장자리 
팽창 연산자와 침식 연산자의 조합,  
팽창 연산자를 적용한 팽창img와 침식 연산자를 적용한 침식img를 각각 생성한 후 감산한다.  
팽창img - 침식img  
밝은 영역의 가장자리를 분리하며 그레이스케일 이미지가 가장 급변하는 곳에서 가장 높은 결과를 반환한다.  

### 4. 탑햇 연산 | 열림 연산에서 사라질 요소 표시
입력이미지와 열림 연산의 조합,  
입력이미지 - 열림적용 이미지  
입력이미지의 객체들이 제외되고 국소적으로 밝았던 부분들이 분리된다.  
열림 연산에서 사라질 기본 이미지의 요소(스펙클, 밝은 부분)들을 표시한다.  


### 5. 블랙햇 연산 | 닫힘 연산에서 사라질 요소 표시
입력이미지와 닫힘 연산의 조합,  
입력이미지 - 닫힘적용 이미지  
입력이미지의 객체들이 제외되고 국소적으로 어두웠던 홀들이 분리된다.    
닫힘 연산에서 사라질 기본 이미지의 요소(홀)들을 표시한다.  

### 6. 히트미스 연산 | 모서리(corner)검출 _ 매우 중요 !!!
히트미스 연산은 다른 연산들과는 다르다.  
**단일 채널 이미지**에서 활용하며, 주로 이진화 이미지에 적용한다.  
전경이나 배경 픽셀의 특정 패턴을 찾는데 사용하고, 구조요소 형태에 큰 영향을 받는다.  
히트미스 연산의 컨벌루션 연산의 커널은 내부 요소가 0 또는 1의 값만 의미가 있다.  
0은 해당 픽셀을 고려하지않음, 1은 해당 요소를 유지하겠다는 뜻이다.  
이 특성 덕분에 모서리를 검출 할 수 있다.  

![hitmiss](https://user-images.githubusercontent.com/44021629/106414435-ecbf6800-648f-11eb-805a-d67119955a72.PNG)



<hr>
이미지 전처리, 후처리로 주로 사용되는 모폴로지 변환, 연산에 대해 알아봤다.  
특히, 모서리 검출할때 주로 쓰인다는 hitmiss  연산 방식은 나중에 도움이 될 수 있을 것 같다.   
<code>#영상처리 #이미지 변환</code>

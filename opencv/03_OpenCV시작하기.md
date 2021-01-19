---
layout: post
title: OpenCV 시작하기
subtitle: "기계의 눈을 담당하는 라이브러리"
type: "OpenCV"
createDate: 2021-01-19
opcv: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 3
---

# OpenCV에서 이미지 요소

opencv에서 이미지는 3가지의 구성 요소 <code>이미지 크기</code>, <code>이미지 정밀도</code>, <code>이미지 채널</code> 로 구성되어있으며, 이 구성 요소를 분리하는 <code>관심 영역</code>, <code>관심 채널</code>이있다.
**이미지의 3가지 요소는 꼭 알고 있어야한다 !!**

1. **이미지 크기**
2. **이미지 정밀도**
3. **이미지 채널**

<hr>

## 1. 이미지 크기
  
이미지의 크기는 이미지의 너비(Width) 와 이미지의 높이(Height)를 의미한다.  
이미지는 행렬(matrix)의 형태로 구성되어있다. 이미지의 크기가 곧 데이터의 크기라 할 수 있다.  
이미지 크기는 **Image Pyramid**나 **Resize**등으로 전처리 과정에서 이미지 크기를 조절한다.  

### 이미지 크기 속성
원본 이미지가 OpenCV에서 설정해준 이미지 크기와 다를 수 있다.  
예를 들어 원본 이미지의 크기는 1400 x 500 인데 OpenCV에서 500 x 300 으로 설정하면 이미지 데이터의 손실이 발생한다. 손실을 방지하기위해서 원본 이미지의 크기를 변경해주는 함수를 적용해줘야한다.

*이미지 누락은 곧 데이터의 누락이라 볼 수 있다.*

### OpenCV에서 이미지 크기 표현 방법

```
import numpy as np 

color = np.zeros((height, width, 3), np.unit8)
gray = np.zeros((rows,cols, 1), 1), np.unis8)

```
```
np.zeros((높이, 너비, 3), np.unit8)
```
*numpy는 행렬이나 일반적으로 대규모 다차원 배열을 쉽게 처리할 수 있게 지원하는 파이썬 라이브러리이다.*  

## 2. 이미지 정밀도

정밀도(Bit depth)란 이미지가 얼마나 많은 색상을 표현할 수 있느냐를 의미한다.  
정밀도는 색상을 표현하는 방법 중 하나다.

```
정밀도 높을수록 데이터의 양이 많아져 정밀한 처리를 할 수 있다.
단, 정확도가 높아지는 것은 아니다. 오히려 정밀도가 높을때 정확도가 떨어질 수 있다.
```

### 비트표현
비트(bit)는 곧 색상의 표현 개수를 설정한다.  
색상을 표현할땐 n-Bit로 포현하며, n은 비트의 수를 의미한다.  
8-Bit는 256가지의 값을 갖게된다. 256가지의 숫자로 색상을 표현한다.  

![img-nbit](https://user-images.githubusercontent.com/44021629/105048507-01265c80-5aaf-11eb-9b36-1e3d61f6b075.PNG)

색상을 표현할 때에는 적어도 8bit여야 유의미한 데이터를 얻게된다.  
그레이스케일이미지에서도 8bit를 사용하여 흑백색상을 원활하게 표현한다.    
*그레이스케일 : 흰색부터 검은색까지 점차적으로 변해가는것*   
*처리하는 단계에 따라 적절하게 bit수 조절*

### OpenCV 정밀도 표현법
OpenCV에서는 8비트 정밀도 이미지를 표현할 때,  
U8 (= np.unit8 | 8-bit unsigned integers)를 가장 많이 활용한다.   
여기서 unsigned는 부호없음을 의미하며, integers는 정수형을 의미한다.  
기본 integers에서 8bit의 범위는-128 ~ 127 까지지만,  
unsigned integers에서 8bit의 범위는 0 ~ 255 까지이다.  
  

```
OpenCV형식       데이터형식               의미                   
---------------------------------------------------------------
np.unit8          byte          8-bit unsigned integers       
np.int8           sbyte         8-bit signed integers
np.uint16         uint16        16-bit unsigned integers
np.int16          int16         16-bit signed integers
np.float32        float         32-bit floating point number
np.double         double        64-bit floating point number
```
  
<code>code</code>
```
color = np.zeros((height, width, 3), np.unit8)
gray = np.zeros((rows,cols, 1), 1), np.unis8)
```
```
np.zeros((height, width, 3), 정밀도)
```
*위 코드에서 color는 색상이미지를,  gray는 그레이스케일이미지를 의미한다.*


## 3. 이미지 채널
채널은 그래픽스 이미지의 색상 정보를 담고있다.
*그래픽스 이미지란, 곧 비트맵을 의미하며 일반적으로 직사각형 격자의 화소, 색의 점으로 표현한 이미지를 말한다.*

![bitmap](https://user-images.githubusercontent.com/44021629/105053226-0e921580-5ab4-11eb-9b94-131aabe62599.jpg)

채널은 일반적으로 Red, Green, Blue로 구성된 RGB채널과 추가로 Alpha채널로 구성된다.  
그 외에도 Hue, Saturation, Vector로 구성된 HSV 채널이 있다.  
색상이미지를 표현할 때에는 3~4개 채널이 존재하고, 흑백 이미지를 표현할 때에는 1개의 채널로 표현한다.  
채널이 2개 이상인 경우에는 다중 채널, 1개인 경우는 단일 채널이라 부른다.  

![rgb0to255](https://user-images.githubusercontent.com/44021629/105056537-a9402380-5ab7-11eb-8247-aad6e8c87f5c.PNG)

색상 표현은 기본 8-bit ( 0 ~ 255 ) 범위에서 보면    
R 채널에서 0은 검은색 ,255는 빨간색을 의미한다.  
즉, 0 ~ 255에서 255에 가까울 수록 채널색을 띄고, 0일수록 검은색을 띈다.  

  
### OpenCV 채널 표현법

<code>code</code> 
```
color = np.zeros((height, width, 3), np.unit8)
gray = np.zeros((rows,cols, 1), 1), np.unis8)
```
```
np.zeros((height, width, 채널수), np.unit8)
```


***중요, OpenCV에서는 RGB형태의 입력 이미지를 받아오고, 처리를 해줄때에는 BGR로 변환시켜주는 함수 cv2.Color_RGB2BGR 옵션을 사용해줘야한다. !! ***


## 4. 관심 영역 & 관심 채널
- **관심 영역** : ROI (Region Of Interest) 의 약자, 이미지를 처리할때 이미지에서 객체를 탐지하거나 검출하는 영역을 지정하는 것이다.  
*영역(Region)을 정해주는게 매우 중요하다*
- **관심 채널** : COI (Channel Of Interest) 의 약자, 특정 채널을 사용해 연산을 진행할때 정하는 채널을 말한다.


## 5. 히스토그램
히스토그램은 데이터의 분포를 몇 개의 구간을 나눠 각 구간에 속한 데이터를 시각적으로 표현한 막대그래프이다.  
이미지의 픽셀 값을 X축으로, 해당 픽셀 값의 사용 횟수를 Y축으로 결정한다면  
이미지의 밝은 정도를 히스토그램으로 확인할 수 있다.  

![histo_crowd](https://user-images.githubusercontent.com/44021629/105062614-253d6a00-5abe-11eb-8b22-3659e3902bb9.png)


위 사진의 경우에는 전반적으로 어두운 사진이라 히스토그램이 0에 치우쳐진 형태로 나타난다.

히스토그램은 3가지 중요 요소를 갖고있다.  
1. **빈도 수(BINS)** : 히스토그램 그래프의 x축 간격 (몇개의 픽셀을 한 막대로 표현할지 정한다.)
2. **차원수 (DIMS)** : 히스토그램을 분석할 이미지의 차원
3. **범위 (RANGE)** : 히스토그램 그래프의 x축 범위

### OpenCV 히스토그램 계산 함수

<code>code</code>
```
hist = cv2.calcHist(
  images,       
  channels,
  mask,
  histSize,
  ranges,
  hist = None          #옵션
  accumulate = False   #옵션
)
```
<code>예시</code>
```
hist = cv2.calcHist([image], [0], None, [256], [0, 256])
```
위 코드는 입력된 image를 단일 채널일 경우 [0], 다중 채널일 경우 [0] = Blue를 의미, 히스토그램의 크기 (빈도수)를 256으로 지정, x축의 범위를 0부터 256까지 지정하고 그 결과를 hist에 저장한다


1. images : 입력된 이미지의 값을 저장하고있고,   
2. channels : 단일채널의 경우 [0]이 입력되어야하고,  다중채널일 경우 R = [2], G = [1], B = [0]이 된다. O 
3. mask : 이미지를 분석할 영역을 따로 설정하는 역할이다. 입력이미지를 그대로 분석할 경우 None값을 입력하면 된다.
4. histSize : 빈도수 (BINS)를 설정하는 것이다.
5. range : 범위 (Range)를 설정하는 것이다.
6. [옵션] hist = None은 중요한것이 아니므로,, 무시하겠다.
7. [옵션] accumulate : 누산은 히스토그램이 누적 반영을 할지 결정하는 것이다. 



<hr>

[비트맵 vs 벡터]  
https://imweb.me/faq?mode=view&category=29&category2=33&idx=71515   
[opencv 색상 조합]   
https://bkshin.tistory.com/entry/OpenCV-7-%E3%85%87%E3%85%87   
https://deep-learning-study.tistory.com/116   
[opencv 히스토그램]   
http://www.gisdeveloper.co.kr/?p=6634  
<code>#영상처리 #재미있다 :D</code>

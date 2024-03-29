---
layout: post
title: OpenCV 이미지 변형
subtitle: "특징 검출과 데이터 해석을 위한 이미지 전처리"
type: "OpenCV"
createDate: 2021-01-22
private: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 6
---

# OpenCV에서 이미지 변형, 이미지 전처리 작업
이미지 데이터를 변형 작업을 해줌으로써,  
우린 이미지에서 중요한 특징들을 얻을 수 있다.  
특히, 나중에 가면 OpenCV와 Tensorflow를 활용해 영상처리 프로젝트를 하게된다면..  
OpenCV는 전처리까지 담당하고 그 뒤로는 인공지능으로 더 자세한 특징 검출 및 물체에 anchor box를 그려준다.  
따라서, 이미지 전처리를 담당하는 OpenCV이미지 변형은 매우 중요한 파트이다.  
이런 전처리 알고리즘으로는 <code>색상 공간 변환</code>, <code>이진화</code>, 
<code>이미지 연산</code>, <code>흐림효과</code> 가 있다.  

여기서 *이미지 연산*을 제외한 **색상 공간  변환**, **이진화**, **흐림효과**에 대해서 정리하겠다.  
이미지 연산은 Python 배열간의 연산과 비슷하다.  

1. **색상 공간 변환**
2. **이진화**
3. **흐림효과**


<hr>

# 1. 색상 공간 변환
색상 공간 변환은 말 그래도 이미지가 갖고있는 본래의 색상 공간에서 다른 색상 공간으로 변환하는 것을 의미한다.  
색상 공간 변환 함수는 입력 이미지의 크기와 정밀도가 변함없이 출력 이미지에도 적용된다.  
대신, 이미지 데이터에 변화는 생길 수 있다.  


<code>색상 공간 변환 함수</code>

```js
dst = cv2.cvtColor(
  src,
  code,
  dstCn = None
)
```
> - **src** : 입력이미지
> - **code** : 색상 변환 코드 **아래 코드 참조**
> - **dstCn** : 출력할 채널 수, None = 자동으로 채널 수를 정해준다.  



<code>색상 변환 코드</code>

> - **cv2.COLOR_RGB2BGR** : RGB -> BGR 색상 변환
> - **cv2.COLOR_RGBA2BGR** : RGB -> GRAYSCALE 색상 변환
> - **cv2.COLOR_BGR2GRAY** : BGR -> GRAYSCALE 색상 변환
> - **cv2.COLOR_GRAY2BGR** : GRAYSCALE -> BGR 색상 변환
> - **cv2.COLOR_RGB2HSV** : RGB -> HSV 색상 변환
> - **cv2.COLOR_원본 이미지 색상2결과이미지 색상**



<code>색상 공간 변환 함수 예제</code>

```js
import cv2

src = cv2.imread("crow.jpg")
dst = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
색상이미지를 그레이스케일(단일채널)로 변환한다.
![rgb2bgr](https://user-images.githubusercontent.com/44021629/105614767-c6a22400-5e0e-11eb-9d89-8b8d046bfddc.PNG)



### 색상 공간 변환에 있어서,,
그레이스케일에서 색상 이미지로 변환한다면, 모든 요소에 같은 가중치를 할당하고  
색상 이미지에서 그레이스케일로 변환한다면, 채널의 수가 감소해 (다중채널 -> 단일채널)  전체 데이터양이 엄청 줄어든다.  
이런 경우를 대비해, 색상에서 그레이스케일 변환은 아래 가중치를 적용한다.  
**[ Y = 0.299 x R + 0.587 x G + 0.114 x B ]**  
그리고 **HSV**와 **HLS**에서 Hue값은 일반적으로 0 ~ 360 사이 값으로 표현한다.  
![hue](https://user-images.githubusercontent.com/44021629/105615015-ad01dc00-5e10-11eb-96e9-a851335ee624.png)  
하지만,, HSV나 HLS공간은 8bit 이미지로 0 ~ 255사이 값만 할당이 가능하다.  
그러면 Hue는 255를 넘어가게되면 문제가 발생한다.. 그래서   
Hue는 원래 범위에서 절반을 나눈 값을 사용해 0 ~ 180의 범위로 사용한다.  
![hue](https://user-images.githubusercontent.com/44021629/105617155-a4190680-5e20-11eb-9fc5-ba49aa47f666.png)


## HSV 색상 공간 
HSV는 Hue(색상), Saturation(채도), Value(명도)로, 색상을 표현하기 가장 간편한 색상 공간이다.  
솔직히, RGB나 BGR은 인간이 인지할 수 있는 범위를 넘어 선 공간이다.  
HSV를 알아보자.  
*Hue(색상) : 색깔의 성질을 의미한다. 빨강, 초록, 파랑 등으로 표현하는게 색의 성질이다.  
*Saturation(채도) : 색의 선명도를 의미한다. 원색에 가까울 수록 채도가 높다고 표현한다.  
*Value(명도) : 색의 밝기이다. 명도가 높을 수록 백색에, 낮을 수록 흑색에 가까워진다.  

![hsv](https://user-images.githubusercontent.com/44021629/105615202-1f26f080-5e12-11eb-9aef-84bd2f69fcdc.png)

OpenCV에서는 색상이 0 ~ 180을, 채도는 0 ~ 255을, 명도는 0 ~ 255의 범위를 갖는다.  
채도와 명도는 선형 그레이디언트형태로 최소, 최대값 설정이 간편하지만,,  
Hue(색상)은 보면 최솟값과 최댓값 모두 빨강색이다. 최소와 최대를 이어붙이면 하나의 원 형태로 Hue원형 모델이 만들어진다.  
![hue_circle](https://user-images.githubusercontent.com/44021629/105615320-f94e1b80-5e12-11eb-8481-43fb2cb4e3c3.gif)

우리가 Hue에서 고려해줘야할 색은 빨간색이다. 빨간색은 최소 빨간색(0에 가까운)과 최대 빨간색(180에 가까운)이 존재한다.   
만약, 빨간색의 범위를 최소값은 170, 최대값은 10으로 설정하면, 오류가 발생한다.  Hue색상 범위에는 문제가 없지만,  최소 최대값에서 문제가 발생한다.  
우리는 이런 경우 **채널 분리**를 해서 낮은 쪽의 빨강 채널과 높은 쪽의 빨강 채널을 나누고 합치는 연산을 해줘야한다.  

![split_channel](https://user-images.githubusercontent.com/44021629/105615641-b17cc380-5e15-11eb-9b54-a7e4b1d45364.png)
원본 이미지에서 red채널, blue채널, green채널로 분리해준다.  

<hr>
<code>채널 분리 함수</code>

```js
mv = cv2.split(src)
```
채널 분리함수는 **다중 채널 이미지(src)**를 **단일 채널 이미지배열(mv)**로 나눈다.  
mv에는 mv[0] (첫번째 채널) , mv[1] (두번째 채널), mv[2] (세번째 채널)이 포함되어있다.  

<code>채널 병합 함수</code>

```js
dst = cv2.merge(mv)
```
채널 병합함수는 **단일 채널 이미지 배열(mv)**를 **다중 채널 이미지(dst)**로 병합한다.  

<hr>

우리가 궁금한건, 다중채널에서 단일채널로 분리했을 때, 해당 채널에서 특정 범위의 값으로 검출해야한다.  예를 들어 빨강채널에선 검출하려는 값이 범위와 일치하면 255, 아니면 0의값으로 할당해줘야한다.  
우리는 이러기 위해서 **배열 요소 범위 설정 함수**를 사용한다.  

<code>배열 요소 범위 설정 함수</code>

```js
dst = cv2.inRange(
  src,
  lowerb,
  upperb
)
```
> - src : 입력 이미지
> - lowerb : 낮은 범위
> - upper : 높은 범위
> - src에서 낮은 범위와 높은 범위 사이에 포함된 값만 255로 변환하고, 포함안된 값은 0으로 변환한다.  


<code>배열 요소 범위 설정 함수 예제</code>

```js
h_red = cv2.inRange(h,0, 5)
```
> - h 채널에서 0 ~ 5 사이의 값이면 255, 아니면 0 으로 채운 배열을 h_red로 반환한다.  

<hr>

<code>Hue에서 공간 색상 검출 예제</code>

```js
import cv2

src = cv2.imread("tomato.jpg")
hsv = cv2.cvtColor(src, cv2.COLOR_BGR2HSV)

h, s, v = cv2.split(hsv)
h_red = cv2.inRange(h, 0, 5)

dst = cv2.bitwise_and(hsv, hsv, mask = h_red)
dst = cv2.cvtColor(dst, cv2.COLOR_HSV2BGR)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
> - src : tomato.jpg를 읽어온다.
> - hsv : src에서 bgr 색상 공간을 hsv 색상 공간으로 변환한다.
> - h ,s ,v : split을 통해 hsv에서 h, s, v로 공간 분할을 했다. 
> - h_red : inRange의 범위 0 ~ 5를 통해 빨간색을 찾겠다는 의미다.
> - bitwise_and(hsv, hsv, mask = h_red) : hsv와 hsv를 and연산하는데, 만약 h_red != 0임을 의미한다. h_red에는 0 ~ 5인 곳에만 255를 반환하고 나머지는 0을 반환해놓은 배열이다. 결국, 반환 결과는 h_red에서 255로 변환된 곳만 hsv에 표시한 것이다.  

<hr>

위의 방식은 HSV에서 Hue만 뽑아와서 검출해준 것이다.  그리고 빨간색도 170이상인 경우가 존재해서,, 0 ~ 5까지만 뽑아오면 170이상의 빨간색들은 모두 무시가 된다.  
결과를 보면 알 수 있을 것이다. 
그래서 우리는 이런 문제를 해결해주기위해 **배열 병합 함수**를 이용한다.  

<code>배열 병합 함수</code>

```js
dst = cv2.addWeighted(
  src1,
  alpha,
  src2,
  beta,
  gamma,
  dtype = None
)
```
> - [ dst = src1 * alpha + src2 * beta + gamma ]
> - src1 : 입력 이미지 1
> - alpha : src1에 대한 가중치
> - src2 : 입력 이미지 2
> - beat : src2에 대한 가중치
> - gamma : 추가 합 

배열 병합 함수는 **알파 블렌딩** ( 이미지위에 다른 이미지를 투명하게 덧씌워 비치게하는 효과)을 구현할 수 있다.  
입력이미지(src1)과 입력이미지(src2)를 아무 변화없이 그대로 사용할 경우, alpha = 1, beta = 1, gamma = 0으로 할당해서 사용하면 된다.  

<br/>
<code>색상 검출 예제</code>

```js
import cv2

src = cv2.imread("tomato.jpg")
hsv = cv.cvtColor(src, cv2.COLOR_BGR2HSV)

h, s, v = cv2.split(hsv)

lower_red = cv2.inRange(hsv, (0, 100, 100), (5, 255, 255))
upper_red = cv2.inRange(hsv, (170, 100, 100), (179, 255, 255))
mix_color = cv2.addWeighted(lower_red , 1.0, upper_red, 1.0, 0.0)

dst = cv2.bitwise_and(hsv, hsv, mask = mix_color)
dst = cv2.cvtColor(dst, cv2.COLOR_HSV2BGR)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
> - 보고가야할 코드
> - lower_red = cv2.Range(hsv, (0,100,100), (5, 255, 255))
> - hsv에서 (0,100,100) ~ (5, 255, 255) 낮은 빨간색을 추출해낸다.
> - upper_red는 (170, 100, 100) ~ (179, 255, 255)까지로, 높은 빨간색을 추출해낸다.
  
**다시한번 보는 Hue**
![hue](https://user-images.githubusercontent.com/44021629/105617155-a4190680-5e20-11eb-9fc5-ba49aa47f666.png)



<hr>


# 2. 이진화
동영상이나 이미지에서 특정 기준으로 픽셀을 분류해야할 때가 있다.  
이때 특정 값보다 높거나 낮으면 검은색 또는 흰색으로 값을 변경해줘야한다.  
기준값에 따라 이분법적으로(검은색 또는 흰색) 구분해 픽셀을 나누는 연산이다.  
**이미지 전처리에선 거의 필수로 사용된다.**  


<code>이진화 함수</code>

```js
retval, dst = cv2.threshold(
  src,
  thresh,
  maxval,
  type
)
```
> - **src** : 입력이미지
> - **thresh** : 임계값,  임계값 보다 낮은 픽셀은 0 또는 원본 픽셀값으로 변경 
임계값 보다 높은 픽셀은 최대값(maxval)로 변경
> - **maxval** : 최대값, 임계값보다 높을시 변경되는 값
> - **type** : 임계값 형식, **임계값 형식 참고**
OpenCV에서는 결과(dst)이미지만 반환되는게 아닌, 설정임계값(retval)까지 반환해준다.  
retval은 함수에 적어준 thresh와 동일한 값이다.    

<code>임계값 형식</code>

> - **cv2.THRESH_BINARY** : 임게값을 초과할 경우 maxval, 아닐경우 0
> - **cv2.THRESH_BINARY_INV** : 임게값을 초과할 경우 0, 아닐경우 maxval  , **INV**는 역을 의미한다.
> - **cv2.THRESH_TOZERO** : 임게값을 초과할 경우 변형없음, 아닐경우 0
> - **cv2.THRESH_MASK** : 검은색 이미지로 변경(마스크용)
> - **cv2.THRESH_OTSU** : 오츠 알고리즘 적용(단일 채널이미지에서만 적용가능)
> - **cv2.THRESH_TRIANGLE** : 삼각형 알고리즘 적용(단일 채널이미지에서만 적용가능)

### 오츠 알고리즘 & 삼각형 알고리즘
입력된 이미지의 밝기분포(히스토그램)을 통해 최[적의 임계값을 찾아 이진화를 적용하는 알고리즘이다.  
(자세히 다루진 않겠다.)  
<hr>


## 적응형 이진화 알고리즘
입력 이미지에 따라 임계값이 스스로 다른 값을 할당할 수 있도록 해주는 알고리즘이다.  
즉, 임계값이 스스로 주변환경에 의해 정해지는 것이다.  
일반 이진화 알고리즘으로는 조명의 변화나 반사가 심한 경우 이미지 내의 밝기 분포가 달라져 상황이 변할때마다 임계값을 바꿔줘야하는 상황이 발생한다.  
이런 변화하는 상황에 임계값을 알아서 바꿔주는게 **적응형 이진화 알고리즘** 이다.  


<code>이진화 함수</code>

```js
dst = cv2.adaptiveThreshold(
  src,
  maxval,
  adaptiveMethod,
  thresholdType,
  blocksize,
  C
)
```
> - src : 이미지
> - maxval : 최댓값
> - **adaptiveMethod** : **적응형 이진화 방식**
> - thresholdType : 임계값 형식 (위 임계값 참고)
> - **blocksize** : 픽셀 주변의 blocksize x blocksize 영역에 대한 가중 평균 계산
> - C : 계산한 가중 평균에 상수 C를 감산한다.  
> 
> 픽셀마다 적응형 임계값 T(x, y)를 설정한다. 수식은 다음과 같다.  
![적응형이진화수식](https://user-images.githubusercontent.com/44021629/105648553-6cc55b00-5eef-11eb-9963-d737aa94d9e7.PNG)

> blocksize는 홀수만 가능하다.  상수 C는 일반적으로 양수를 쓰며, 0과 음수도 사용은 가능하다.  
> **적응형 이진화 방식 (adaptiveMethod)**에 따라 결과가 변한다.  


  
  
  
<code>적응형 이진화 방식</code>

> - **cv2.ADAPTIVE_THRESH_MEAN_C** : blocksize 영역의 모든 픽셀에 평균 가중치를 적용
> - **cv2.ADAPTIVE_THRESH_GAUSSIAN_C** : blocksize 영역의 모든 픽셀에 중심점으로부터의 거리에 대한 가우시안 가중치를 적용

<hr>

<code>적응형 이진화 예제</code>  
src (입력이미지)
![gomtange](https://user-images.githubusercontent.com/44021629/105649213-97fd7980-5ef2-11eb-94fd-148eef0ec0e4.jpg)

```js
import cv2

src = cv2.imread("gomtange.jpg")
gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
binary = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY, 33, -5)

cv2.imshow("binary", binary)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
result(결과)
![after_bear](https://user-images.githubusercontent.com/44021629/105649225-a350a500-5ef2-11eb-9671-9db17aa84c53.PNG)

> - 주요 코드 : binary
> - binary는 적응형 이진화 함수로 gray를 입력이미지, 255를 maxval로 사용했다.
> - 평균가중치를 적용하는 이진화 플래그를 사용, 임계값 형식으론 이진화 임계값보다 높으면 255, 아니면 0을 적용, blocksize는 33이고 상수 C는 -5이다.  
> - 상수 C를 -5(음수)로 사용해 전체 영역이 어두워진다.  
> - 일반적으로 음수를 사용하진 않지만, 특수한 경우 음수가 더 우수한 결과를 보이기도한다.
> - 음수값을 지정할때에는 **임계값 형식**을 **반전 이진화 플래그 (cv2.THRESH_BINARY_INV**를 적용하거나 **이미지 반전연산**을 적용하는 방법은 빛의 반사나 서로 다른 조명이 이미지에 있는경우에 효과가 좋다.



<hr>




# 3. 흐림 효과
흐림 효과는 **블러링(blurring)** 또는 **스무딩(smoothing)**이라 불리며,  
**노이즈**를 줄이거나 외부영향을 최소화 하는데 사용한다.  
단순히 이미지를 흐리게하는게 아니라 노이즈를 제거해서 연산시 계산을 빠르고 정확하게 할 수 있게 도와준다.  
흐림 효과에는 **3가지 주요 매개변수**가 있다.  
<code>커널(kernel)</code>, <code>고정점(anchor point)</code>, <code>테두리 외삽법(borderType)</code>이다.  

그리고 흐림 효과 함수로는 **크게 5가지 함수**가 있다.    
<code>단순 흐림 효과</code>, <code>박스 필터 흐림 효과</code>, <code>중간값 흐림 효과</code>, <code>가우시안 흐림 효과</code>, <code>양방향 필터 흐림 효과</code>
<hr>

## 1. 커널 Kernel | 고정점 Anchor Point | 테두리 외삽법 BorderType 

![kernel_anchorpoint](https://user-images.githubusercontent.com/44021629/105748809-f9225d00-5f85-11eb-8669-18ebdba1d1e1.png)

### 커널
커널은 이미지에서 (x, y)의 픽셀과 해당 픽셀 주변을 포함한 작은 크기의 공간을 의미한다.  
이 영역 안에서 각 픽셀에 대해 특정 수식이나 함수등을 적용한 결과를 통해   
새로운 이미지 데이터를 만드는 알고리즘에서 사용한다.  
일반적으로 3x3, 5x5, 7x7을 많이 활용한다.  
제일 효과가 좋은 커널의 크기라고 한다.   
**영상처리를 비롯해 인공지능 공부를 하면서 많이 보게될 거다**  
새로운 픽셀을 만들어 내기 위해 커널 크기의 화소 값을 이용해 어떤 시스템을 통과해 계산하는 것을   
**컨벌루션 (Convolution)**이라 한다.   
인공지능에서 **CNN**에서 C가 바로 Convolution이다.  
![conv](https://user-images.githubusercontent.com/44021629/105865982-d439f280-6036-11eb-92c2-1ef6540ff25c.png)


### 고정점
커널을 통해 컨벌루션된 값을 할당하는 지점이다.  
커널 안에서 고정점은 하나의 지점을 가지며, 대부분 커널의 중심지점을 고정점으로 사용한다.  
그리고 고정점은 이미지와 어떻게 정렬되는지 알려준다.  
고정점 좌표의 값을 커널에 속해있는 좌표들을 컨벌루션 연산을 통해 새로운 값을 만든후, 고정점 좌표에 대입해주는 방식이다.  


### 테두리 외삽법
하지만 문제가 하나 발생한다.. 만약 (0,0)좌표에서 3x3 커널로 컨벌루션 할려고하면 (-1, -1)등의 처리할 수 없는 좌표가 나타난다.  
이럴때 원본 이미지에 커널 사이즈에 알맞게 테두리에 임의의 값을 구성된 층을 쌓아서,, 원본이미지의 모든 픽셀들이 정상적으로 컨번루션 연산을 수행할 수 있게 도와주는게   
**테두리 외삽법**이다.  

![bordertype](https://user-images.githubusercontent.com/44021629/105866702-9b4e4d80-6037-11eb-975a-db13c61c2c7f.jpg)
위 그림을 보면, (0,0)좌표에서 3x3 커널로 컨벌루션 연산을 해주기위해, 원본 이미지에 0 값으로 입력된 층을 한개 테두리에 만들어줬다.  
그러면 (0, 0)좌표도 정상적인 컨벌루션 연산을 수행할 수 있게된다.  

<code>테두리 유형</code>

> - **cv2.BORDER_CONSTANT** :  고정값으로 픽셀을 확장
>                             iiiii | abcde | iiiii   
> - **cv2.BORDER_REPLICATE** : 테두리 픽셀을 복사해서 확장
>                             aaaaa | abcde | eeeee
> - **cv2.BORDER_REFLECT** : 픽셀을 반사해서 확장
>                             edcba | abcde | edcba

<hr>


## 2. 흐림 효과 함수
![pro_blur](https://user-images.githubusercontent.com/44021629/105879670-ce97d900-6045-11eb-88bd-3757384085f5.png)


### 단순 흐림 효과

<code>단순 흐림 함수</code>

```js
dst = cv2.blur(
  src,
  ksize,
  anchor = None,
  borderType = None
)
```
> - src : 입력 이미지
> - ksize : 커널 사이즈  (ksize x ksize)
> - anchor = None : 고정점이 None, Null , (-1, -1)을 사용하면 커널의 중앙이 고정점임을 의미한다.
> - borderType : 테두리 외삽법

<hr>

### 박스 필터 흐림 효과

<code>박스 필터 흐림 함수</code>

```js
dst = cv2.boxFilter(
  src,
  ddepth,
  ksize,
  anchor = None,
  normalize = None,
  borderType = None
)
```
> - ddepth : 출력 이미지의 정밀도, -1을 의미하면 입력 이미지의 정밀도를 사용한다.
> - normalize : 커널이 영역별 정규화를 할 것인지를 의미한다.  
박스필터는 일반적으로 모든 값이 1의 값을 갖는다, 하지만 normalize(정규화)값을 True로 지정할 경우  
**정규화된 박스 필터**로 변경되며, 커널의 모든 값이 커널의 개수(면적)로 나눈 값이된다.  


![box_filter](https://user-images.githubusercontent.com/44021629/105869762-eddd3900-603a-11eb-85e4-0810fa44fd67.png)
위 정규화 박스는 5x5커널 이므로 모든 커널 값들을 면적인 25의 수로 나눠, 전체 커널 합이 1이 되게끔한다.  
정규화를 하지않으면 박스 필터 내부 커널 값이 모두 1인 상태로 총합은 25가 된다.  

<hr>

### 중간값 흐림 효과

<code>중간값 흐림 함수</code>

```js
dst = cv2.medianBlur(
  src,
  ksize
)
```
> - 중간값 흐림 효과는 고정점 없이, 중심 픽셀 주변으로 커널 크기의 이웃한 픽셀들의 중간값을 사용해 각 픽셀의 값을 변경한다.  여기서 중간값은 평균을 말하는게 아니다.  
예를 들어, [1, 30, 50, 1000, 9000]이 있으면 여기서 중간값은 50을 의미한다. 즉, 순서들로 나열했을때 중간값을 의미한다.

<hr>

### 가우시안 흐림 효과
흐림 효과 함수에서 가장 **유용한** 함수이다.    

<code>가우시안 흐림 함수</code>

```js
dst = cv2.GaussianBlur(
  src,
  ksize,
  sigmaX,
  sigmaY = None,
  borderType = None
)
```
> - sigmaX : x방향의 가우스 커널 표준 편차
> - sigmaY : y방향의 가우스 커널 표준 편차, None값일시 sigmaX와 같이 같아진다. 
> - sigmaX와 sigmaY 모두 0으로 설정하면 커널의 크기에 의해 자동으로 설정된다.  

![gaussian_s](https://user-images.githubusercontent.com/44021629/105871985-37c71e80-603d-11eb-85e5-41f9ff8d1e2b.png)

<code>가우시안 흐림 효과 함수 예제</code>

```js
import cv2

src = cv2.imread("moon_img.png")

dst = cv2.GaussianBlur(src, (7, 7), 0, 0, cv2.BORDER_ISOLATED)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
![gaussian_moon](https://user-images.githubusercontent.com/44021629/105877408-4e707400-6043-11eb-92e0-f625c153cc5c.png)



<hr>


### 양방향 필터 흐림 효과

<code>양방향 필터 흐림 함수</code>
가장자리(Edge)를 선명하게 보존하면서 노이즈를 우수하게 제거한다.  
두종류의 가우시안 필터로 흐림 효과 적용한다.  

```js
dst = cv2.bilateralFilter(
  src,
  d,
  sigmaColor,
  sigmaSpace,
  dst = None,
  borderType = None
)
```
> - d : 지름, 흐림효과를 적용할 각 픽셀 영역의 지름을 의미한다.
> - sigmaColor : 색상 공간에서 사용할 가우시안 커널의 너비를 설정, 값이 클 수록 흐림 효과 적용 범위가 넓어진다.
> - sigmaSpace : 좌표 공간에서 사용할 가우시안 커널의 너비를 설정, 값이 클 수록 인접한 픽셀에 영향을 미친다.


<code>양방향 필터 흐림 효과 함수 예제</code>

```js
import cv2

src = cv2.imread("moon.png")

dst = cv2.bilateralFilter(src, 100, 33, 11, borderType = cv2.BORDER_ISOLATED)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
![moon_af_bf](https://user-images.githubusercontent.com/44021629/105876590-76aba300-6042-11eb-906d-a401c5286efb.png)


<hr>



<code>#영상처리 #이미지 변형</code>

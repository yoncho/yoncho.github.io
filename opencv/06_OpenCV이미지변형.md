---
layout: post
title: OpenCV 이미지 변형
subtitle: "특징 검출과 데이터 해석을 위한 이미지 전처리"
type: "OpenCV"
createDate: 2021-01-22
opcv: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 4
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



<hr>

[]    
 
[]  


<code>#영상처리 #동영상</code>

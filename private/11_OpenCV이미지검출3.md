---
layout: post
title: OpenCV 이미지 검출[3]_직선 검출, 원 검출
subtitle: "주요 특징점 검출, 이미지내의 객체를 검출한다"
type: "OpenCV"
createDate: 2021-03-10
private: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 11
---

## 5. 직선 검출

직선 검출은 이미지내에서 선형적인 부분들을 검출한다.  
직선 도로에서 도로의 차선이라든지, 건물의 외형들을 검출할 수 있다.  
직선 검출 알고리즘은 **허프 변환 (Hough Transform)**을 활용해 직선을 검출한다.  
허프변환은 이미지 내의 어떤 점이라도 선의 일부일 수 있다는 가정하에 직선의 방정식을 이용해 직선을 검출한다.  
OpenCV에서 특히, 이분야에서는 삼각함수를 이용한 직선의 방정식을 구한다.  
(이유는 그냥 직선의 방정식에는 한계들이 존재하기 때문이다.)  

![3함](https://user-images.githubusercontent.com/44021629/110581001-11081600-81ad-11eb-81f1-c7b5079ca6a3.png)

위와 같은 원점으로부터 각도와 거리로 직선의 방정식을 표현할 수 있다.  


![2함](https://user-images.githubusercontent.com/44021629/110581278-9390d580-81ad-11eb-837c-a7e5bd29ffd7.jpg)

위 그림을 설명하겠습니다.  
각도 (0' ~ 180')별로 원점에서 거리와 각도가 동일한 직선을 찾는 것이다.  
p1점을 기준으로 a각도에서 b만큼의 직선까지의 거리가 나온 직선 A1을 기준으로하자,  
그러면 P2점을 기준으로 각도를 0 부터 180 도까지 천천히 올려가면서 a각도일때 p2를 지나는 직선, B1을 찾으면 해당 직선으로부터 원점까지의 거리가 b인경우,  
우린 A1직선과 B1직선을 누적 시켜서 이게 이미지내에서 직선일 경우라고 생각하는 것이다.  

### 3개의 허프 변환
OpenCV에서는 세 종류의 변환을 지원한다.  
**표준 허프 변환**, **멀티 스케일링 허프 변화**, **점진성 확률적 허프 변환**  

#### 표준 허프 변환 & 멀티 스케일링 허프 변환
허프 변환 함수는 표준 허프 변환과 멀티 스케일링 허프 변환을 동시에 지원한다.  

<code>허프 변환 함수</code>

```js
lines = cv2.HoughLines(
	image,
	rho,
	theta,
	threshold,
	srn = None,
	stn = None,
	min_theta = None,
	max_theta = None
)
```
> - image : 입력이미지, 8bit 단일 채널 이미지를 사용하며 일반적으로 이진화 함수나 캐니 엣지 함수의 결과를 인수로 활용한다.  
> - rho : 거리, 단위는 0.0 ~ 1.0을 사용한다.
> - theta : 각도, 라디안을 사용하며 0 ~ 180의 범위에서 사용한다.
> - threshold : 임계값, 직선을 결정하기 위해 만족해야되는 누산 평면의 값 즉, 몇개 이상의 동일한 직선이 존재해야 해당 직선을 이미지에서 중요한 직선으로 인식할 것인지 결정한다. 
> - srn : 거리에 대한 약수, (여기서 부터 멀티 스케일링 허프 변환 변수)
> - stn : 각도에 대한 약수, 거리와 각도는 정규화된 값이 아니므로 멀리 스케일링은 거리와 각도의 값을 조정하기 위해 srn과 stn을 사용한다.  
> - min_theta : 최소 각도,
> - max_theta : 최대 각도, OpenCV에서는 최소, 최대 각도를 이용할 수 있다.  


#### 점진성 확률적 허프 변환
앞에 설명한 변환들은 **모든 점**에서 직선을 찾는다.  
이렇게 할 경우 비교적 많은 시간이 들지만,  
점진성 확률적 허프 변환은 이미지 픽셀중 몇개만 (확률적)  
즉 임의의 점 몇개만 누적해서 계산한다.  
이 알고리즘은 시작점과 끝점을 반환한다.  

<code>확률 허프 변환 함수</code>

```js
lines = cv2.HoughLinesP(
	image,
	rho,
	theta,
	threshold,
	minLineLength = None,
	maxLineGap = None
)
```
> - image : 입력 이미지
> - rho : 거리
> - theta : 각도
> - threshold : 임계값, 모두 허프 변환과 동일한 의미
> - minLineLength : 최소 선 길이, 검출된 직선이 가져야할 최소한의 선 길이, 이 값도 짧은 직선은 취급 안한다.
> - maxLineGap : 최대 선 간격, 검출된 선 사이의 최대 허용 간격, 이 값보다 간격이 좁은 경우 직선으로 취급 안한다. 

<hr>

#### 실습 코드

<code>허프 변환 예제</code>

```js
import cv2
import numpy as np

src = cv2.imread("trump_card_1.jpg")
dst = src.copy()

kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3), (-1, -1))

gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
_, binary = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY)
morp = cv2.dilate(binary, kernel)
morp = cv2.erode(morp, kernel, iterations=3)
morp = cv2.dilate(morp, kernel, iterations=2)
canny = cv2.Canny(morp, 0, 0, apertureSize=3, L2gradient=True)

lines = cv2.HoughLines(canny, 1, np.pi/180, 100, srn= 50, stn=10, min_theta=0, max_theta=np.pi/2)

for i in lines:
	rho, theta = i[0][0], i[0][1]
	a, b = np.cos(theta), np.sin(theta)
	x0, y0 = a*rho, b*rho
	
	scale = src.shape[0] + src.shape[1]

	x1 = int(x0 + scale * -b)
	y1 = int(y0 + scale * a)
	x2 = int(x0 - scale * -b)
	y2 = int(y0 - scale * a)

	cv2.line(dst, (x1, y1), (x2, y2), (0, 255, 255), 2)
	cv2.circle(dst, (x0, y0), 3, (255, 0, 0), 5, cv2.FILLED)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![허프변환결과](https://user-images.githubusercontent.com/44021629/110585249-e7062200-81b3-11eb-85ec-5dfc13a06a20.PNG)


전처리가 진행된 이미지에 멀티 스케일 허프 변환을 적용한 코드이다.  
허프 변환은 모든 점에 대해 직선의 방정식을 구하기 때문에  
최대한 점의 성분을 제거한다.  
그래서 **그레이스케일**, **이진화**, **모폴로지 연산**을 차례대로 적용했으며  
모폴로지 연산을 통해 노이즈를 최대한 제거했다.  
그런 다음, 캐니 엣지를 적용해 가장자리 선분만 남긴다.  
그러면 허프 변환 함수는 가장자리 선분들만 이용해서 직선을 찾는 것이다.  


<hr>


## 6. 원 검출

원 검출은 허프 변환 알고리즘 중 하나인 **허프 원 변환**알고리즘을 활용해 원을 검출한다.  
허프 원 변환 알고리즘은 2차원 평면이 아닌, 3차원 누산 평면을 검출한다.  
2차원 공간 (x, y)에서 3차원 공간 (x, y, z)로 변한 것이다.  
하지만 이러면 N^3 (n은 차원)바이트 메모리를 필요로 하기 때문에,, 우린 3차원을 2차원으로 나눠서 계산 한다.  
(추후 설명은 넘어가겠다. 원 검출은 많이 사용하지 않는다..!?)  

<code>허프 원 변환 함수</code>

```js
circles = cv2.HoughCircles(
	image,
	method,
	dp,
	minDist,
	param1 = None,
	param2 = None,
	minRadius = None,
	maxRadius = None
)
```
> - image : 입력 이미지, 8bit 단일 채널 그레이 스케일 형태 이미지 사용 (이진화 x)
> - method : 검출 방법, 항상 2단계 허프 변환 **21HT**를 사용한다. 
> - dp : 해상도 비율, 원의 중심을 검출하는데 사용하는 누산 평면 해상도, 인수를 1로 지정할 경우 입력이미지와 동일한 해상도를 갖는다. 인수를 2로 지정할 경우 입력 이미지의 1/2 크기로 줄어든다.  
> - minDist : 최소 거리, 검출된 원과 원 사이의 최소 거리
> - param1 : 캐니 엣지 임계값, 허프 변환에서 자체적으로 캐니 엣지를 수행하는데, 이때 사용되는 상위 임계값이다. (하위 임계값은 자동으로 상위임계값의 1/2 값을 갖는다.)
> - param2 : 중간 임계값, 누산 평면에 대한 임계값으로 값이 낮을 수록 많은 원들이 검출된다.
> - minRadius : 최소 반지름
> - maxRadius : 최대 반지름, 원의 반지름 범위


<hr>

<code>허프 원 변환 예제</code>

```js
import cv2

src = cv2.imread("golf_ball.png")
dst = src.copy()

image = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)

circles = cv2.HoughCircles(image, cv2.HOUGH_GRADIENT, 1, 100, param1= 100, param2=35, minRadius=80, maxRadius=120)

for i in circles[0]:
	cv2.circle(dst, (i[0], i[1]), i[2], (255,255,255), 5)


cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
![원결과](https://user-images.githubusercontent.com/44021629/110587107-8c21fa00-81b6-11eb-8783-0d8fb5d4168a.PNG)


<hr>

<code>#영상처리 #직선검출 #원검출</code>

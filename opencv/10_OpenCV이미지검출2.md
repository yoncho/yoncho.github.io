---
layout: post
title: OpenCV 이미지 검출[2]_다각형 근사, 코너 검출,
subtitle: "주요 특징점 검출, 이미지내의 객체를 검출한다"
type: "OpenCV"
createDate: 2021-02-05
opcv: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 10
---

## 3. 다각형 근사

다각형 근사는 검출된 윤곽선의 형상을 분석할 시 정점의 수가 적은 다각형으로 표현하도록 하는 근사방법이다.  
즉, 이미지에서 검출된 그룹화된 윤곽선 정보를 갖고 최대한 적은 정점을 갖는 다각형으로 표현하는 것이다.  
  

다각형 근사는 **더글라스-패커(Douglas-peucker)알고리즘**을 사용한다.  
위 알고리즘은 **근사치 정확도 (Epsilon)**의 값으로 기존의 다각형과 윤곽점이 압축된 다각형의 최대편차를 고려해 최종 다각형을 근사하게된다.  

![다운로드](https://user-images.githubusercontent.com/44021629/109268490-82aba000-784e-11eb-980a-364a4921bc93.gif)

더글라스-패커 알고리즘은 위와같이 적용된다.  

![220px-douglas_peucker](https://user-images.githubusercontent.com/44021629/109268576-9d7e1480-784e-11eb-84af-b9c7224d7c55.png)

위 짧은 영상을 순서대로 정리해놓은 그림이다.  
굵은 점들이 윤곽점, 검은색 실선이 윤곽선들이다.  
1. 두 극점을 선택한 후 선으로 연결한다.  => (a)라는 선 
2. 그리고 해당 선에 근사치 정확도, 즉 epsilon을 적용한다.   
3. 근사치 정확도에 포함되지 않는 점들 중, 선분에서 가장 멀리있는 점을 선택하고 연결한다. => (c)라는 윤곽점을 선택 후 연결  
4. 이젠 그럼 (c)를 기준으로 오른쪽 방향으로 제일 거리가 먼 점을 선택후 연결, 위 1, 2번 작업을 반복한다.  
5. 정점이 적은 다각형을 근사한다.  


<code>다각형 근사 함수</code>

```js
approxCurve = cv2.approxPolyDP(
	curve,
	epsilon,
	closed
)
```
> - curve : 윤곽선, 윤곽선 검출 함수에서 검출된 윤곽선, 배열구성 윤곽점을 활용한다.  
> - epsilon : 근사치 정확도, 입력된 다각형과 반환될 근사 다각형 사이의 최대 편차 간격을 의미한다.  
> - closed : 폐곡선, 시작점과 끝점의 연결 여부, True = 마지막점과 첫번째 점을 연결한다.  
True로 해야 완벽한 다각형이 완성된다.  


<code>다각형 근사 예제</code>

```js
import cv2

src = cv2.imread("gomtange.jpg")
dst = src.copy()

kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))

gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
ret, binary = cv2.threshold(gray, 230, 255, cv2.THRESH_BINARY)
morp = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel, iterations=2)
image = cv2.bitwise_not(morp)

contours, hierarchy = cv2.findContours(image, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)

for i in contours:
	perimeter = cv2.arcLength(i, True)
	epsilon = perimeter * 0.05
	approx = cv2.approxPolyDP(i, epsilon, True)
	cv2.drawContours(dst, [approx], 0, (0,0,255), 3)
	for j in approx:
		cv2.circle(dst, tuple(j[0]), 3, (255,0,0), -1)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![result](https://user-images.githubusercontent.com/44021629/109272093-c0f78e00-7853-11eb-9ae1-d05a072c5749.PNG)

<hr>
 
### (1) 윤곽선 길이 계산
윤곽선의 길이를 계산한다.  
<code>윤곽선 길이 계산 함수</code>

```js
length = cv2.arcLength(
	curve,
	closed
)
```
> - curve : 윤곽선
> - closed : 폐곡선, 윤곽선의 닫힘 여부 (True or False)

### (2) 윤곽선 면적 계산
윤곽선 내부의 면적을 계산한다.  
<code>윤곽선 면적 계산 함수</code>

```js
area = cv2.contourArea(
	contour,
	oriented
)
```
> - contour : 윤곽선
> - oriented : 방향성, 계산된 윤곽선 면적의 부호를 의미한다. 방향성이 참 일 경우, 윤곽선의 방향에 따라 부호가있는 면적 값을 반환하고. 거짓 일 경우, 절댓값으로 계산된 값으로 반환된다.  

### (3) 윤곽선 경계 사각형 (매우 중요)

윤곽선의 경계면을 둘러싸는 사각형을 구한다.  

<code>윤곽선 경계 사각형 함수</code>

```js
boundrect = cv2.boundingRect(
	curve
)
```
> - curve : 윤곽선

### (4) 윤곽선 최소 면적 사각형 (중요)
윤곽선의 경계면을 둘러싸는 최소 크기의 사각형을 계산한다.  

<code>윤곽선 최소 면적 사각형 계산 함수</code>

```js
rect = cv2.minAreaRect(
	points
)
```
> - points : 윤곽선

### (5) 윤곽선 최소 면적 원
윤곽선의 경계면을 둘러싸는 최소 크기의 원을 계산한다.

<code>윤곽선 최소 면적 원 계산 함수</code>

```js
center, radius = cv2.minEnclosingCircle(
	points
)
```
> - points : 윤곽선
 
### (6) 볼록 껍질 (매우매우 중요 !!!)

<code>윤곽선 볼록 껍질 계산 함수</code>

```js
hull = cv2.convexHull(
	points,
	clockwise = None
)
```
> - points : 윤곽선
> - clockwise : 방향, 검출된 볼록 껍질의 볼록점들의 인덱스 순서를 의미.. 참일경우 시계방향으로 정렬, 거짓일 경우 반시계방향으로 정렬한다.   
볼록 껍질 알고리즘은 O(NlogN) 시간 복잡도를 갖는 **스크랜스키(Sklansky)알고리즘**을 이용해 입력된 좌표들의 볼록한 외곽을 찾는다.  

#### 스크랜스키 알고리즘

![sklansky](https://user-images.githubusercontent.com/44021629/109377149-c9f86600-790c-11eb-8b52-1b152c322bc4.gif)

스크린스키는 경계사각형의 정점(Vertex)을 검출한다. 위 그림에선 정점은 T, L, B, R이 된다.  
행당 정점은 볼록점으로 사용한다.  
볼록 껍질의 또 다른 볼록점들은 R1, R2, R3, R4영역에 존재하며, Q영역 내부에는 절대 존재하지 않는다 !!  
그러므로 우린 R 영역에서 볼록점들을 검출하게된다.  
R영역에도 수많은 윤곽점들이 존재하므로 여기서 볼록 껍질을 이루는 볼록점들을 선별하는 과정을 거처야한다.  


![Sklregions2](https://user-images.githubusercontent.com/44021629/109377175-f613e700-790c-11eb-9bba-df7fda16eb9d.gif)

선별 과정으로 위 그림처럼,  
볼록점 시작이 i이며, 다음 번째 볼록점은 i + 1이 된다.  
만약 i가 T,L,B, R중 하나 즉, 정점 중 하나 일 경우, 
i + 1 번째 볼록점과 i + 2 번째 볼록점 를 검출하기 위해  
다음 조건으로 볼록점을 선택한다.  
1. i + 2의 점이 A1영역에 있을경우, i + 2는 i + 1로 활용한다.   
2. i + 2의 점이 A2영역에 있을경우, i + 2점은 볼록점에서 제외한다.  
3. i + 2의 점이 A3영역에 있을경우, i + 2점을 볼록점으로 간주한다.   

만약 i가 L(정점)이라고 가정하면, A1 영역은 R1이고 A2 영역은 Q영역, A3영역은 R2영역이된다.  
이렇게 정점위치에 따라 조건영역이 바뀐다는 점을 알아야한다.  


<hr>

### (7) 윤곽선의 모멘트 (매우 중요 !!!)

**이미지에서 모멘트란, 영상 픽셀 강도에 대한 특정한 가중평균(모멘트) 또는 일반적으로 어떤 물체의 고유한 특성이나 해석을 할 수 있는 기능** 이다.  
윤곽선에서 모멘트는 **공간 모멘트**와 **중심 모멘트 ( +  정규화)**가 있다.  
공간모멘트는 물체의 공간을 나타내는 모멘트로써, 출력시 물체의 최외각 가장자리를 나타내며  
중심모멘트는 물체의 중심을 나타내는 모멘트로써, 물체의 중심에 점을 찍어준다.  

![moment_chair](https://user-images.githubusercontent.com/44021629/109377857-4ab96100-7911-11eb-8397-201dbc360ddb.jpg)


<code>윤곽선의 모멘트 함수</code>

```js
moments = cv2.moments(
	array,
	binaryImage = None
)
```
> - array : 윤곽선이나 이미지
> - binaryImage : 이진화 이미지, 입력된 array 매개변수가 이미지일 경우, 이미지의 픽셀값들을 이진화 처리할지 결정한다.


<hr>



## 4. 코너 검출

코너 검출은 입력이미지에서 코너점을 검출하는 알고리즘이다.  
다각형의 꼭지점을 검출하는 것으로 이해하기 쉬운데, 정확하게는 **트래킹(Tracking), 객체의 움직임을 추적하거나 관찰하는 것**을 하기 좋은 지점을 **코너**라 한다.  
여기서 코너 검출 알고리즘은 높은 도함수를 갖는 지점(가장 두드러지는 코너점)을 계산하고 분석해서 코너 정의에 만족하는 점을 반환한다.  
코너는 **지안보 시와 카를로 토마시의 특징 검출 알고리즘**과 **해리스가 제안한 알고리즘**을 활용해 검출할 수 있다.  

<code>코너 검출 함수</code>

```js
corners = cv2.goodFeaturesToTrack(
	image,
	maxCorners,
	qualityLevel,
	minDistance,
	mask = None,
	blockSize = None,
	useHarrisDetector = None
	k = None
)
```
> - image : 입력이미지, 8비트 또는 최대 32비트 단일 채널 이미지만 입력가능
> - maxCorners : 코너 최댓값, 검출할 최대 코너의 수를 제한한다. 0이하값은 무제한으로 검출하겠다는 소리다.  
> - qualityLevel : 코너 품질, 반환할 코너의 최소 품질을 설정, 코너 품질은 0.0 ~ 1.0 사이의 값을 할당할 수 있으며, **일반적으로 0.01 ~ 0.10 값을 사용한다.** 
예를 들어 코너품질이 0.01이면, 가장 좋은 코너의 강도가 1000이라하면, 1000 * 0.01 = 10이다. 10이하의 코너 강도를 갖는 코너는 무시한다.  
> - minDistance : 최소 거리, 검출된 코너들의 최소 근접거리를 의미한다. 설정된 최소 거리 이상값만 검출한다.  
> - mask : 입력이미지와 같은 차원을 갖는 이미지이며, 마스크 값이 0인곳은 코너를 계산하지않는다.
> - blockSize : 블록크기, 코너를 계산할때 고려하는 코너 주변 영역의 크기
> - useHarrisDetector : 해리스 코너 검출기, 참일시 해리스가 제안한 알고리즘을 사용하고, 거짓일시 지안보 시와 카를로가 제안한 알고리즘을 사용한다.  
> - k : 해리스 측정 계수, 해리스 알고리즘을 사용할때 할당하며 해리스 대각합의 감도 계수를 의미한다.  
  
객체를 인식하기 위해서 코너 검출 알고리즘을 사용한다면, 더 정확한 코너점들을 필요로한다.  
정확한 위치좌표값을 계산하기위해서, 근사 계산을 통한 서브픽셀 세밀화를 진행한다.  
그러면 검출된 코너의 좌표가 (30, 45)로 나왔는데, **서브 픽셀 세밀화**를 하면 (30.5, 45.6)으로 더 정확한 좌표위치를 반환시켜준다.    
코너 픽셀의 세밀화를 진행하면 검출된 코너점의 위치를 **보정**해준다.  

<code>코너 픽셀 세밀화 함수</code>

```js
cv2.cornerSubPix(
	image,
	corners,
	winSize,
	zeroZone,
	criteria
)
```
> - image : 입력이미지
> - corners : 코너 검출을 통해 얻어낸 정수 픽셀의 코너 위치를 담고있는 배열
> - winSize : 검출크기, 코너 위치를 중심으로 검출크기만큼 확장한다. 검출 크기의 인수값이 (n, n)일때 (n * 2 + 1, n * 2 + 1)의 크기로 영역을 검색한다.  
> - zeroZone : 제외크기, 검출영역에서 제외할 부분의 크기를 설정한다. 검출크기와 동일하게 작동하며, (-1, -1)일때에는 제외할 영역이 없음을 의미한다.  
> - criteria : 기준, 코너 픽셀 세밀화 반복 작업의 조건을 설정, 예를 들어 반복횟수가 10회, 정확도가 0.1인경우, 반복횟수가 10회를 달성하거나 정밀도가 0.1을 달성했을시 계산이 종료된다.  


<hr>

<code>코너 검출 및 코너 픽셀 세밀화 예제</code>

```js
import cv2

src = cv2.imread("lego_p.jpeg")
dst = src.copy()

gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
corners = cv2.goodFeaturesToTrack(gray, 100, 0.01, 5, blockSize=3, useHarrisDetector=True, k=0.03)

for i in corners:
	cv2.circle(dst, tuple(i[0]), 3, (255, 0, 0), 5)

criteria = (cv2.TERM_CRITERIA_MAX_ITER + cv2.TERM_CRITERIA_EPS, 30, 0.01)
cv2.cornerSubPix(gray, corners, (5, 5), (-1, -1), criteria)

for i in corners:
	cv2.circle(dst, tuple(i[0]), 3, (0, 0, 255), 5)

cv2.imshow("dst", dst)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![corner_final](https://user-images.githubusercontent.com/44021629/109378492-5ce9ce00-7916-11eb-8c0f-60547bffb2f6.PNG)



<hr>

<code>#영상처리 #이미지검출</code>

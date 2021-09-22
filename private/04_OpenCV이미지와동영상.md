---
layout: post
title: OpenCV 이미지 I/O
subtitle: "이미지 입력 & 출력"
type: "OpenCV"
createDate: 2021-01-20
opcv: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 4
---
# OpenCV에서 이미지 입력 & 출력
OpenCV는 N차원 배열에 대한 복합적 연산을 수행할 수 있지만,  
주된 용도는 **이미지 처리** 다.  

1. **이미지 입력**
2. **이미지 출력**
3. **이미지 저장**

<hr>

## 1. 이미지 입력
OpenCV에서 주로 사용하는 이미지 형식은 JPG, PNG와 같은 래스터 그래픽스 이미지 파일이다.  
(*래스터 그래픽스 이미지란, 앞서 설명한 비트맵 이미지를 말한다.*)
그리고 이미지 파일마다 이미지를 압축/해제/표현 등을 하는 고유한 **포맷**을 갖고있다.    
이런 고유한 포맷을 컴퓨터가 해석할 수 있어야한다.   
(*포맷이란, 파일의 형식(JPEG, JPG, PNG, GIF 등)이고 파일을 형식에 따라 구분해주기위해 파일명 뒤에 붙이는게 확장자(.jpeg, .jpg, .png, .gif)이다.*)   
  확장자마다 파일 시그니처를 갖고있다.    
(*파일 시그니처란, 확장자를 대신할 수 있는 파일 매직 넘버이다. 파일 형식마다 몇개의 바이트가 지정되어있다.*)   
OpenCV에서는 파일 확장자를 보고 파일의 형식을 판단하는게 아니라,   
파일 시그니처 정보를 읽어서 파일의 형식을 판단한다.  

![file_signature_png](https://user-images.githubusercontent.com/44021629/105210337-41104100-5b8e-11eb-9c07-a9a6a0425e60.PNG)
PNG 이미지의 파일 헤더 시그니처는 80 50 4E 47 0D 0A 1A 0A 이다.  
  
<hr>
  
<code>이미지 입력 함수</code>

```js
cv2.imread(
  fileName,
  flags = cv2.IMREAD_COLOR
)
```

- **fileName** : 경로를 포함한 입력 파일의 이름을 의미한다.  경로란 파일의 위치(상대/절대)경로를 의미한다.  
- **flags** : 입력된 파일을 어떻게 해석해서 불러올 것 인지 결정한다.
    
<code>입력 함수의 flags</code>


> - cv2.IMREAD_UNCHANGED : 알파 채널 포함 이미지 반환 
                         (알파채널 없을 경우, 3채널로 반환 )   
> - cv2.IMREAD_GRAYSCALE : 단일 채널, 그레이스케일 이미지로 변환  
> - cv2.IMREAD_COLOR : 다중 채널, 색상 이미지로 반환  
> - cv2.IMREAD_ANYDEPT : 정밀도가 있는 이미지의 경우 16/32비트 이미지로 반환 
                       (존재 하지 않을 경우 기본 8bit 이미지로 반환)  
> - cv2.IMREAD_REDUCED_GRAYSCALE_x : 크기를 1/x로 줄인 후 그레이스케일 적용  
> - cv2.IMREAD_REDUCED_COLOR_x : 크기를 1/x로 줄인 후 색상 이미지로 반환  


<hr>  
  
<code>이미지 입력 함수 예제</code>

```js
import cv2

scr = cv2.imread("OpenCV_Logo.png", cv2.IMREAD_GRAYSCALE)

print(src.ndim, src.shape, src.dtype)

```
<code>결과</code>

```js
2 (739, 600) unit8
```
src는 그레이스케일로 이미지를 변환해서 갖고온 변수이다.  
ndim은 2가 나오는 것은 그레이스케일로 변환해서 차원이 2차원이기 때문이다.  
shape은 이미지의 크기로, 크기에 관해선 작업해준 것이 없기 때문에 원본 이미지와 크기가 동일하다.  
dtype은 이미지가 갖는 기본 unit8 (8bit-unsigned integers)를 갖고있다.  
  
<hr>
  

## 2. 이미지 출력
OpenCV에는 HighGUI라 불리는 라이브러리를 지원한다. HighGUI는 우리가 OpenCV로 처리한 이미지를 윈도우를 생성해서 화면에 이미지를 출력해주게 해준다.  



<code>이미지 출력 함수</code>

```js
cv2.imshow(
  winname,
  ndarray
)
```

- **winname** : 이미지를 출력시켜줄 창의 이름 
- **ndarray** : 이미지 행렬을 의미한다. 즉, 이미지를 읽어와 처리해준 변수를 넣어주면된다.  
  
<code>이미지 출력 함수 예제</code>

```js
import cv2

src = cv2.imread("OpenCV_Logo.png", cv2.IMREAD_GRAYSCALE)

cv2.namedWindow("src", flags = cv2.WINDOW_FREERATIO)
cv2.resizeWindow("src", 400, 200)
cv2.imshow("src", src)
cv2.waitKey(0)
cv2.destroyWindow("src")
)
```
    
<code>결과</code>
![print_img_exam01](https://user-images.githubusercontent.com/44021629/105234306-690f9c80-5bae-11eb-901d-11de3f0c3ab6.PNG)
 
    
<code>HighGUI 윈도우 함수</code>

```js
cv2.namedWindow(winname, flags = None)  :  winname의 이름을 가지고 flags로 설정된 윈도우 생성
cv2.resizeWindow(winname, width, height) : winname의 이름을 갖는 윈도우의 크기를 width, heigh로 설정
cv2.destroyWindow(winname) : winname의 이름을 갖는 윈도우 제거
cv2.destroyAllWindows() : 모든 윈도우 제거
```
  
<code>윈도우 flags</code>

> - cv2.WINDOW_NORMAL : 윈도우 크기 조절 가능, 최대화된 창을 이전 크기로 복원  
> - cv2.WINDOW_AUTOSIZE : 원도우 크기 조절 불가능, 이미지의 크기와 동일하게 표시  
> - cv2.WINDOW_KEEPRATIO : 이미지 비율을 최대한 유지  
> - cv2.WINDOW_FREERATIO : 비율 제한이 없는 경우, 이미지 최대한 확장 가능  

<hr>

## 3. 이미지 저장
이미지는 보통 8bit 3채널 이미지로 저장된다.   
하지만 PNG, TIFF등의 이미지 형식으로 저장하면 16bit 이미지나 알파채널이 포함된 4채널 이미지도 저장할 수 있다.  
이미지 저장 함수는 이미지 파일의 포맷(확장자)을 처리하기 위해 소프트웨어 라이브러리(**코덱**)에 의존한다. 
OpenCV에서는 PNG, JPEG, TIFF등 자체적으로 지원하며, 일부 코덱은 운영체제 코덱을 사용한다.  
  
<code>이미지 저장 함수</code>

```js
cv.imwrite(
  filename,
  img,
  params = None
)
```
  
- **filename** : 경로를 포함한 저장할 이미지의 저장명( + 확장자) 을 의미한다.
- **img** : 저장할 이미지
- **params** : 옵션으로 인코딩될 매개변수를 의미한다.

<code>params 옵션</code>

> - cv2.IMWRITE_JPEG_QUALITY : JPEG화질 (0 ~ 100)  
> - cv2.IMWRITE_JPEG_PROGRESSIVE : JPEG 점차 선명해짐 (0, 1)  
> - cv2.IMWRITE_PNG_COMPRESSION : PNG 압축 (0 ~ 100)  
> - ...more...
   
 <hr>

<code>이미지 저장 함수 예제</code>

```js
save = cv2.imwrite("YONCHO.jpeg", img, (cv2.IMWRITE_JPEG_QUALITY, 100, cv2.IMWRITE_JPEG_PROGRESSIVE, 1))
print(save)
```
<code>결과</code>

```js
True 
```
이미지 저장 함수는 bool(True or False)값을 반환한다.  
저장했으면 True를, 실패했으면 False를 반환한다. 


<hr>

[파일 시그니처 모음]    
http://forensic-proof.com/archives/300   
[OpenCV 이미지 처리]  
https://jaehyeongan.github.io/2020/02/15/OpenCV%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%EA%B8%B0%EC%B4%88-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%B2%98%EB%A6%AC/  


<code>#영상처리 #재미있다 :D</code>

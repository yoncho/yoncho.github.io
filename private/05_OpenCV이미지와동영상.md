---
layout: post
title: OpenCV 동영상 I/O
subtitle: "동영상 입력 & 출력"
type: "OpenCV"
createDate: 2021-01-22
private: true
text: true
author: yoncho
post-header: false
header-img: ""
order: 5
---

# OpenCV에서 동영상 입력 & 출력
OpenCV는 N차원 배열에 대한 복합적 연산을 수행할 수 있지만,  
주된 용도는 **이미지 처리** 다.  
그리고 동영상에서는 프레임당으로 **이미지정보**를 가져와서 처리한다.     


1. **동영상 입력**
2. **동영상 출력**
3. **카메라 출력**
4. **동영상 저장**

<hr>

## 1. 동영상 입력
OpenCV에서는 동영상에서 프레임단위로 이미지를 읽어서 출력한다.  
컴퓨터가 동영상 파일을 읽으려면 동영상 코덱을 읽을 수 있는 라이브러리가 설치되어있어한다.  
OpenCV에서는 FFMPEG를 지원해서 AVI나 MP4등 동영상 파일을 쉽게 읽을 수 있다.  

  
<code>동영상 입력 함수</code>

```js
capture = cv2.VideoCapture(fileName)
```

- **cv2.VideoCapture()** : 동영상 입력하는 OpenCV의 함수이다.  
- **fileName** : 경로를 포함한 입력 동영상 파일명이다.   
<hr>

## 2. 동영상 출력

<code>동영상 출력 예제</code>

```js
import cv2

capture = cv2.VideoCapture("star.mp4")

while True:
  ret, frame = capture.read()

  if(capture.get(cv2.CAP_PROP_POS_FRAMES) == capture.get(cv2.CAP_PROP_FRAME_COUNT)):
    capture.open("star.mp4")
  
  cv2.imshow("VideoFrame", frame)
  if cv2.waitKey(33) == ord('q'): break

capture.release()
cv2.destroyAllWindows()
```

> 1. **VideoCapture** : 현재 입력된 동영상 파일의 정보만 불러온다. 즉, 동영상 파일의 프레임 설정이라든지 코덱 등에 대한 동영상파일을 읽기위한 정보(?)들을 갖고온다.  
> 2. **capture.read()** : 동영상파일에서 프레임을 가져와 압축을 해제한 다음 bool형태의 ret와 ndarray형태의 frame값을 반환한다.  ret(bool)은 capture변수가 정상적으로 프레임을 읽어왔는지 알려주고,  frame(ndarray)에는 현재 프레임정보가 담긴다.  
> 3. **cv2.CAP_PROP_POS_FRAMES** : 현재 프레임의 수
> 4. **cv2.CAP_PROP_FRAME_COUNT** : 총 프레임의 수
> 5. **cv2.imshow(videoName, frame)** : videoName은 윈도우에 표시할 윈도우명이고  frame은 윈도우에 출력해줄 동영상 frame 변수를 의미한다.  
> 6. **cv.waitKey(33) = ord('q')** :  waitKey는 해당 입력된 ms만큼 대기한 후에 다음 프레임을 넘어가는 프레임 대기 변수이다. (ms단위로 쓰이는 이유는 아래 내용에 있다.)  
ord는 python OpenCV가 문자를 처리하지 못 해서 유니코드값으로 변환해주기 위해 사용한다.  즉, 위 if문은 다음 프레임을 넘어가기위한 대기 ms동안 'q'가 입력되었는지 확인해준다.  
> 7. **capture.release()** : 동영상 파일을 닫고 메모리를 해제해주기위해 쓰인다.
> 8. **cv2.destroyAllWindows** : 동영상 재생이 끝나서 모든 윈도우를 닫아주기 위해 쓰인다.  
  

<hr>
   
## FPS (Frame Per Second)

![ezgif com-gif-maker](https://user-images.githubusercontent.com/44021629/105383782-d4ac4500-5c54-11eb-92d5-1944732dd3a9.gif)

동영상을 출력할 때 매우 중요한 요소인 frame은 영상이 바뀌는 속도를 의미한다.  
즉, 화면이 얼마나 부드럽게 재생되느냐인데,,  동영상은 멈춰있는 사진들을 순차적으로 출력시켜 마치 움직이는 것처럼 만든 것이다. 사진의 연속이라할 수 있다.  
그러면 동영상의 부드러움은 **프레임**이 초당 몇 장의 사진을 화면에 보여주냐에 따라 달라진다.  
이를 초당 프레임이라 하며, **FPS**라 한다.  
FPS가 높을수록 화면이 끊기지않고 자연스럽게 출력된다.  

FPS 공식
```js
FPS = 1000 / Interval
```
Interval은 대기할 밀리초 단위를 의미한다. waitKey를 33으로 입력했다면  
FPS = 1000 / 33 = 30.3030 = 30 이된다. 즉 초당 30장의 사진이 화면에 출력되는 것이다.  
python의 경우 cv2.CAP_PROP_FPS를 이용하면 입력된 동영상의 FPS값을 반환해준다.  

<code>동영상 출력 함수</code>

> - capture.isOpened() : 동영상 파일 열기 성공 여부 확인
> - **capture.read()** : 동영상 파일에서 프레임을 읽음  
> - **capture.open(fileName)** : fileName 동영상을 읽음
> - **capture.release()** : 동영상 파일을 닫고 메모리 해제


<code>동영상 출력 속성 함수</code>

> - **cv2.CAP_PROP_POS_MSEC** : 동영상의 현재 위치
> - **cv2.CAP_PROP_POS_FRAMES** : 동영상의 현재 프레임
> - **cv2.CAP_PROP_FPS** : 동영상 프레임 속도
> - **cv2.CAP_PROP_FRAME_COUNT** : 동영상의 총 프레임 수

<hr>

## 3. 카메라 출력
OpenCV로 우리가 무언가를 처리해줄때에는 사실상 이미 촬영된 동영상파일보다는 **실시간**으로 영상 데이터를 받아와서 처리해줘야한다.  
OpenCV에서는 카메라를 읽어올 수 있는 방법이 있다.


<code>카메라 출력 함수</code>

```js
capture = cv2.VideoCapture(index)
```
여기서 index는 카메라의 장치 번호(ID)를 의미한다. 컴퓨터에 내장된 컴퓨터라면 장치번호가 0이고, 만약 내장 카메라가 없으면 외부 연결 카메라 장치번호로 0부터 시작한다. 카메라가 여러대가 연결되어있으면 0, 1, 2 .. 이렇게 카메라수에 맞게 고유번호가 부여된다.    
  


<code>카메라 출력 함수 예제</code>

```js
import cv2

capture = cv2.VideoCapture(0)
capture.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
capture.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)

while True:
  ret, frame = capture.read()
  if ret = True:
    cv2.imshow("videoFrame", frame)
    if cv2.waitKey(33) == ord('q'): break
  else:
    break

capture.release()
cv2.destroyAllWindows()
```



## 4. 동영상 저장
OpenCV에서 동영상 저장할때 프레임의 변경이나 변형을 녹화해서 저장할 수 있다.  
동영상을 저장할때 지원하는 코덱은 운영체제마다 다르다. window(FFMPEG, MSWF, DSHOW), macOS(AVFoundation), linux(FFMPEG)사용된다.  
동영상 저장 함수는 사용할 코덱, 프레임 속도, 프레임 등을 입력해야한다.  

<code>동영상 저장 함수</code>

```js
cv2.imwrite(
  filename,
  fourcc,
  fps,
  framesize,
  isColor = True
)
```

> 1. **filename** : 파일 경로를 포함한 동영상 저장명
> 2. **fourcc** : 동영상 파일 저장할 때 사용할 **압축 코덱**을 의미한다. fourcc는 Four Character Code의 약자로 디지털 포맷 코드를 의미한다.  즉, 동영상 코덱을 구분할때 사용하며, 동영상 인코딩 방식을 의미한다.  
코덱에 따라 압축 방식이 다르며 설정한 확장자에 맞는 코덱을 사용해야한다.  우리가 흔히 아는 AVI확장자 파일에서는 DIVX, XIVD 코덱을 사용한다.  
> 3. **fps** : 저장할 동영상의 프레임 속도를 의미한다.
> 4. **framesize** : 저장할 동영상의 프레임 높이 너비를 의미한다. 보통, 입력된 frame의 높이와 너비를 그대로 사용한다.  
> 5. **isColor** : 저장할 동영상의 프레임을 다중채널로 설정할 것인지 정한다. True = 다중채널(색상이미지)/ False = 단일 채널(흑백이미지)을 의미한다.  

  




<code>동영상 저장 함수 예제</code>

```js
import cv2

capture = cv2.VideoCapture("star.mp4")
width = int(capture.get(cv2.CAP_PROP_FRAME_WIDTH))
height = int(capture.get(cv2.CAP_PROP_FRAME_HEIGHT))
videoWriter = cv2.VideoWriter()
isWrite = False

while True:
  ret, frame = capture.read()
  if(capture.get(cv2.CAP_PROP_POS_FRAMES) == capture.get(cv2.CAP_PROP_FRAME_COUNT)):
    capture.open("star.mp4")

  cv2.imshow("VideoFrame", frame)
  key = cv2.waitKey(33)

  if key == 4:
    fourcc = cv2.VideoWriter_fourcc(*'XVID')
    videoWriter.open("Video.avi", fourcc, 30, (width, height), True)
    isWriter = True
  
  elif key == 24:
    videoWriter.release()
    isWriter = False
  
  elif key == ord('q'): break

  if isWriter == True:
    videoWriter.write(frame)

videoWirter.release()
capture.release()
cv2.destroyAllWindows()
```

> 1. width, height : 읽어온 동영상 프레임의 높이, 너비값을 저장한다.
> 2. videoWriter는 cv2.VideoWriter()를 통해 동영상 녹화 메모리를 할당한다.
> 3. isWrite는 녹화할지 말지 결정해주는 bool(T or F)형태 변수다.
> 4. 만약, 다음 프레임을 넘어가는 대기 시간(33ms)동안 4에 해당하는 값을 누르면, 녹화를 시작한다는 의미다. 
XVID 코덱을 이용하여, 30FPS이고 입력받은 frame의 높이와 너비를 그대로 사용해 다중 채널(색상 이미지)로 녹화를 한다. isWriter = True로 바꿔준다.  
> 5. 만약, 다음 프레임을 넘어가는 대기시간(33ms)동안 숫자 24로 인식하는 값을 누르면, 녹화를 종료한다는 의미다.  
videoWriter에 할당된 메모리를 해제해주고, isWriter를 False로 바꿔준다.
> 6. 만약 isWriter가 True면 해당 frame을 저장하겠다는 것이다. 이렇게 while문을 계속 돌아준다.  




    
<code>동영상 저장 관련 함수</code>

> - **videoWriter.isOpened()** : 동영상 저장 성공 여부 확인
> - **videoWriter.write(ndarray)** : 동영상 파일에 해당 프레임을 저장
> - **videoWriter.open(fileName, fourcc, fps, frameSize, isColor = True)** : 동영상 저장 구조 생성
> - **videoWriter.release()** : 동영상 저장 구조 메모리 해제







<hr>

[]    
 
[]  


<code>#영상처리 #동영상</code>

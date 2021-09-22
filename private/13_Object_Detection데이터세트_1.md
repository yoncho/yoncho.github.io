---
layout: post
title: Object Detection Dataset [1] | PASCAL VOC 2012
subtitle: "PASCAL_VOC_DATASET"
type: "Object Detection"
createDate: 2021-03-14
private: true
text: true
author: "yoncho"
post-header: false
header-img: ""
order: 13
---

### Object Detection Dataset
객체 검출 데이터세트는 어마무시하게 많지만, 대표적인 3가지 데이터 세트를 소개하겠습니다.  
[1] PASCAL VOC 2012 (VOC 2012)  _ XML file    
[2] MS COCO                     _ JSON file    
[3] Google Open Image           _ CSV file  


### PASCAL VOC 2012 DATASET

![pascal2](https://user-images.githubusercontent.com/44021629/111062301-69138500-84eb-11eb-90d9-d31e9af5287d.png)

PASCAL VOC 2012 데이터세트는 Object Detection의 데이터세트에서 베이직으로  
여겨진다. 객체 검출을 공부했다면 기본적으로 아는 데이터 세트라고 할 수 있다.  
20개의 객체 카테고리가 존재하고,  
Classification/Detection,  
Segmentation,  
Action Classification,  
Person Layout  
의 기능을 한다.    

Dataset은 총 5가지의 폴더를 포함하고있습니다.  
1. Annotations : xml 파일들이 들어가있고, Image 한개당 포함하고있는 객체들의 Annotation정보를 갖고있다.    
2. ImagesET : 어떤 이미지를 train, test, trainval, val에 사용할 것인지 매핑정보를 갖고있다.   
3. JPEGImages : Detection/Segmentation에 사용될 원본 이미지로, xml과 파일명동일한 .jpg 파일들이다.    
4. SegmentationClass : Semantic Segmantation에 사용할 masking 이미지들이 있다.   
5. SegmentationObject : Instance Segmantation에 사용할 masking 이미지들이 있다.  

여기서 우리가 자주 사용할 것들은 **1. Annotations 와 3. JPEGImages**이다.  

  
![제목 없음](https://user-images.githubusercontent.com/44021629/111062479-606f7e80-84ec-11eb-9017-f32a811594f3.png)

위 그림처럼 원본 이미지(JPEGImages)와 Annotations정보(Annotation)을 가지고 원본이미지에 bounding box를 시각화 시켜줘야하며, 이렇게 데이터세트를 완성시켜준다.  


### PASCAL VOC DATASET 다운로드 및 정보 확인

<code>데이터세트 다운로드 [shell]</code>  

```js
!wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar
!tar -xvf VOCtrainval_11-May-2012.tar -C ~/objectPrj/data/voc
```
> objectPrj/data/voc/ 디렉터리에 폴더를 다운 받고, 압축해제까지 해준다.   

<code>다운 받은 임의의 원본 이미지 확인하기 [code]</code>

```js
import cv2
import matplotlib.pyplot as plt
%matplotlib inline

img = cv2.imread('../../data/voc/VOCdevkit/VOC2012/JPEGImages/2007_000032.jpg')
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
print('img shape:', img.shape)

plt.figure(figsize=(8, 8))
plt.imshow(img_rgb)
plt.show()
```
![00032](https://user-images.githubusercontent.com/44021629/111062744-2e5f1c00-84ee-11eb-8d47-8c408fd1d009.PNG)

> 공항에 비행기 2대와 정비사 2명의 모습이 보입니다.  

<code>해당 임의의 원본이미지에 대한 Annotation 정보 확인하기 [shell]</code>

```js
!cat ~/objectPrj/data/voc/VOCdevkit/VOC2012/Annotations/2007_000032.xml
```

[2007_000032.xml]  
```js
<annotation>
	<folder>VOC2012</folder>
	<filename>2007_000032.jpg</filename>
	<source>
		<database>The VOC2007 Database</database>
		<annotation>PASCAL VOC2007</annotation>
		<image>flickr</image>
	</source>
	<size>
		<width>500</width>
		<height>281</height>
		<depth>3</depth>
	</size>
	<segmented>1</segmented>
	<object>
		<name>aeroplane</name>
		<pose>Frontal</pose>
		<truncated>0</truncated>
		<difficult>0</difficult>
		<bndbox>
			<xmin>104</xmin>
			<ymin>78</ymin>
			<xmax>375</xmax>
			<ymax>183</ymax>
		</bndbox>
	</object>
	<object>
		<name>aeroplane</name>
		<pose>Left</pose>
		<truncated>0</truncated>
		<difficult>0</difficult>
		<bndbox>
			<xmin>133</xmin>
			<ymin>88</ymin>
			<xmax>197</xmax>
			<ymax>123</ymax>
		</bndbox>
	</object>
	<object>
		<name>person</name>
		<pose>Rear</pose>
		<truncated>0</truncated>
		<difficult>0</difficult>
		<bndbox>
			<xmin>195</xmin>
			<ymin>180</ymin>
			<xmax>213</xmax>
			<ymax>229</ymax>
		</bndbox>
	</object>
	<object>
		<name>person</name>
		<pose>Rear</pose>
		<truncated>0</truncated>
		<difficult>0</difficult>
		<bndbox>
			<xmin>26</xmin>
			<ymin>189</ymin>
			<xmax>44</xmax>
			<ymax>238</ymax>
		</bndbox>
	</object>
</annotation>
```
> 유의 깊게 봐야할 부분은 <object> 부분과 <name>, <bndbox>들이다.  
> <object>는 object 하나의 정보를 나타내는 틀이고,   
> 그 안에 이제 <name>은 object의 이름을 나타내며,   
> <bndbox>안에는 bounding box 좌상단, 우하단 좌표 정보가 있다.    

<hr>

### Annotation XML 파일 정보 파싱하기

<code>다운받은 데이터세트 코드로 불러오기 [code]</code>

```js
import os
import random

VOC_ROOT_DIR ="../../data/voc/VOCdevkit/VOC2012/"
ANNO_DIR = os.path.join(VOC_ROOT_DIR, "Annotations")
IMAGE_DIR = os.path.join(VOC_ROOT_DIR, "JPEGImages")

xml_files = os.listdir(ANNO_DIR)      
```
> - VOC_ROOT_DIR : VOC데이터세트가 다운로드된 디렉터리
> - ANNO_DIR : 데이터세트 디렉터리 안에, Annotations 경로
> - IMAGE_DIR : 데이터세트 디렉터리 안에, JPEGImages 경로
> - xml_files : ANNO_DIR디렉터리 안에 있는 정보들을 리스트로 만든다.  


<code>Annotation에서 객체 이름, 박스 위치 정보확인하기 [code]</code>

```js
import os
import xml.etree.ElementTree as ET

xml_file = os.path.join(ANNO_DIR, '2011_006674.xml')

# XML 파일을 Parsing 하여 Element 생성
tree = ET.parse(xml_file)
root = tree.getroot()

# image 관련 정보는 root의 자식으로 존재
image_name = root.find('filename').text
full_image_name = os.path.join(IMAGE_DIR, image_name)
image_size = root.find('size')
image_width = int(image_size.find('width').text)
image_height = int(image_size.find('height').text)

# 파일내에 있는 모든 object Element를 찾음.
objects_list = []
for obj in root.findall('object'):
    # object element의 자식 element에서 bndbox를 찾음. 
    xmlbox = obj.find('bndbox')
    # bndbox element의 자식 element에서 xmin,ymin,xmax,ymax를 찾고 이의 값(text)를 추출 
    x1 = int(xmlbox.find('xmin').text)
    y1 = int(xmlbox.find('ymin').text)
    x2 = int(xmlbox.find('xmax').text)
    y2 = int(xmlbox.find('ymax').text)
    
    bndbox_pos = (x1, y1, x2, y2)
    class_name=obj.find('name').text
    object_dict={'class_name': class_name, 'bndbox_pos':bndbox_pos}
    objects_list.append(object_dict)

print('full_image_name:', full_image_name,'\n', 'image_size:', (image_width, image_height))

for object in objects_list:
    print(object)

```
> - 결과  
> full_image_name: ../../data/voc/VOCdevkit/VOC2012/JPEGImages/2011_006674.jpg   
> image_size: (500, 375)  
> {'class_name': 'person', 'bndbox_pos': (235, 65, 376, 221)}  




<code>Annotation에서 Bounding box info 이용해서 원본이미지 위에 시각화 [code]</code>

```js
import cv2
import os
import xml.etree.ElementTree as ET

xml_file = os.path.join(ANNO_DIR, '2011_006674.xml')

tree = ET.parse(xml_file)
root = tree.getroot()

image_name = root.find('filename').text
full_image_name = os.path.join(IMAGE_DIR, image_name)

img = cv2.imread(full_image_name)
# opencv의 rectangle()는 인자로 들어온 이미지 배열에 그대로 사각형을 그려주므로 별도의 이미지 배열에 그림 작업 수행. 
draw_img = img.copy()
# OpenCV는 RGB가 아니라 BGR이므로 빨간색은 (0, 0, 255)
green_color=(0, 255, 0)
red_color=(0, 0, 255)

# 파일내에 있는 모든 object Element를 찾음.
objects_list = []
for obj in root.findall('object'):
    xmlbox = obj.find('bndbox')
    
    left = int(xmlbox.find('xmin').text)
    top = int(xmlbox.find('ymin').text)
    right = int(xmlbox.find('xmax').text)
    bottom = int(xmlbox.find('ymax').text)
    
    class_name=obj.find('name').text
    
    # draw_img 배열의 좌상단 우하단 좌표에 녹색으로 box 표시 
    cv2.rectangle(draw_img, (let, topf), (right, bottom), color=green_color, thickness=1)
    # draw_img 배열의 좌상단 좌표에 빨간색으로 클래스명 표시
    cv2.putText(draw_img, class_name, (left, top - 5), cv2.FONT_HERSHEY_SIMPLEX, 0.4, red_color, thickness=1)

img_rgb = cv2.cvtColor(draw_img, cv2.COLOR_BGR2RGB)
plt.figure(figsize=(10, 10))
plt.imshow(img_rgb)
```
> - 아래 그림 처럼, 원본 이미지위에 Annotation정보를 이용해 Bounding box를 그린다.  
![person](https://user-images.githubusercontent.com/44021629/111063347-5439f000-84f1-11eb-8a89-a0354514314a.PNG)


<hr>

<code>#데이터 세트 #PASCAL_VOC #재미있다 :D</code>

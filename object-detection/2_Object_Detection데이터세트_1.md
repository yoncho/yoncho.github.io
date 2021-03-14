---
layout: post
title: Object Detection Dataset [1] | PASCAL VOC 2012
subtitle: "PASCAL_VOC_DATASET"
type: "Dataset"
createDate: 2021-03-14
od: true
text: true
author: "yoncho"
post-header: false
header-img: ""
order: 2
---

### Object Detection Dataset
객체 검출 데이터세트는 어마무시하게 많지만, 대표적인 3가지 데이터 세트를 소개하겠습니다.  
[1] PASCAL VOC 2012 (VOC 2012)  _ XML file    
[2] MS COCO                     _ JSON file    
[3] Google Open Image           _ CSV file  


### PASCAL VOC 2012 DATASET

![pascal2](https://user-images.githubusercontent.com/44021629/111062301-69138500-84eb-11eb-90d9-d31e9af5287d.png)

PASCAL VOC 2012 데이터세트는 Object Detection의 데이터셋에서 베이직으로  
여겨진다. 객체 검출을 공부했다면 기본적으로 아는 데이터 세트라고 할 수 있다.  
20개의 객체 카테고리가 존재하고,  
Classification/Detection,  
Segmentation,  
Action Classification,  
Person Layout  
의 기능을 한다.  
Dataset은 총 5가지의 폴더를 포함하고있다.   
1. Annotations : xml 파일들이 들어가있고, Image 한개당 포함하고있는 객체들의 Annotation정보를 갖고있다.    
2. ImagesET : 어떤 이미지를 train, test, trainval, val에 사용할 것인지 매핑정보를 갖고있다.   
3. JPEGImages : Detection/Segmentation에 사용될 원본 이미지로, xml과 파일명동일한 .jpg 파일들이다.    
4. SegmentationClass : Semantic Segmantation에 사용할 masking 이미지들이 있다.   
5. SegmentationObject : Instance Segmantation에 사용할 masking 이미지들이 있다.  

여기서 우리가 자주 사용할 것들은 **1. Annotations 와 3. JPEGImages**이다.  


![제목 없음](https://user-images.githubusercontent.com/44021629/111062479-606f7e80-84ec-11eb-9017-f32a811594f3.png)

위 그림처럼 원본 이미지(JPEGImages)와 Annotations정보(Annotation)을 가지고 원본이미지에 bounding box를 시각화 시켜줘야하며, 이렇게 데이터세트를 완성시켜준다.  


### PASCAL VOC DATASET example

<code>데이터세트 다운로드</code>  
http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar  
[shell]  

```js
!wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar
!tar -xvf VOCtrainval_11-May-2012.tar -C ~/objectPrj/data/voc
```
> objectPrj디렉터리 밑에 data밑에 voc라는 폴더를 만들고 거기에 다운 받은 voc데이터셋을 압축해제까지 해준다.   

<code>다운 받은 임의의 원본 이미지 확인하기</code>
[code]  

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

<code>해당 임의의 원본이미지에 대한 Annotation 정보 확인하기</code>
[shell]

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




<hr>

<code>#객체 검출 #재미있다 :D</code>

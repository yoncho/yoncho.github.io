---
layout: post
title: 자료구조(1), Stack,Queue
subtitle: Algorithm
type: 자료구조
createDate: 2020-09-22
solution: true
text: true
author: "codeFabian"
post-header: false
header-img: ""
order: 2
---

# Today What I Learned

<hr>

# Stack

**스택 (Stack) 이란 쌓아 올린다는 것을 의미합니다.**

책을 쌓고, 책을 꺼내는 것과 브라우저에서 뒤로가기 버튼을 누르는 것과 같은 원리입니다.

![diagram](https://user-images.githubusercontent.com/46562138/93102414-6ecc1180-f6e6-11ea-8591-ac8e92b2426d.png)

**Stack 의 특징**
**Last In First Out (LIFO)**

스택은 정해진 방향으로만 쌓고, top을 통해서만 접근이 가능합니다.
가장 최근에 들어온 자료가 top에 쌓이며, 자료를 삭제하거나 가져올 때도 top을 통해 가능합니다.

스택에서 top을 통해 **삽입하는 연산을 push**, top을 통해 **삭제하거나 가져오는 연산을 pop**이라고 합니다.

즉, **push** 와 **pop** 을 하는 위치를 **top**이라 하고, 스택의 가장 아랫부분은 **bottom**이라고 합니다.

**스택을 활용한 예시**

스택의 특징인 **LIFO(후입선출)** 은 이러한 분야에서 활용이 가능합니다.

- 실행취소: 가장 나중에 실행된 것 부터 실행을 취소합니다.
- 웹 브라우저 뒤로가기: 가장 나중에 열린 페이지부터 다시 보여줍니다.
- 역순 문자열 만들기

<hr>

# Queue

Queue는 일상생활에서 사람들이 맨 끝에 줄을 서고, 맨 앞에서부터 놀이기구에 탑승하는 것, 카페에서 먼저 온 사람의 주문을 처리하는 것과 같이 **선입선출 (FIFO: first in, first out) 방식의 자료구조** 입니다.

![1200px-Data_Queue svg](https://user-images.githubusercontent.com/46562138/93104468-d6835c00-f6e8-11ea-9b9a-570af12e9263.png)

**Queue 의 특징**
**First In First Out (FIFO)**

큐는 한쪽 끝에서는 **삽입**작업을 하고, 다른 쪽 끝에서는 **삭제**작업이 이루어집니다.
각각 삽입되는 포인터는 **front** 혹은 **head** 라고 부르며, 삭제되는 포인터는 **rear** 혹은 **tail** 이라고 부릅니다.

정리해보면 다음과 같습니다.

- 접근 방법은 가장 첫 원소와 가장 끝 원소로만 가능합니다.
- 가장 먼저 들어온 원소가 가장 먼저 삭제 됩니다.
- **enqueue**: 큐의 뒤에 요소를 추가.
- **dequeue**: 큐의 앞에 있는 요소를 반환 후 삭제

**큐를 활용한 예시**

큐의 특징인 **선입선출(FIFO)**는 간단한 예시로 설명이 가능합니다.

- lol과 같은 게임에서의 게임매칭에서는 대부분 큐를 사용합니다.
- 수강신청
- 놀이공원 줄서기

<hr>

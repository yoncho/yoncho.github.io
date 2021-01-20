---
layout: post
title: 거품정렬
subtitle: Sort
type: 자료구조
createDate: 2021-01-01
solution: true
text: true
author: "codeFabian"
post-header: false
header-img: ""
order: 15
---

### 버블 정렬

![img](https://upload.wikimedia.org/wikipedia/commons/3/37/Bubble_sort_animation.gif)

> 버블 정렬(bubble sort)은 여러 정렬 알고리즘(삽입 정렬, 퀵 정렬, 병합 정렬, 기수 정렬 등) 중 가장 기본적인 알고리즘입니다.  
> 이웃한 두 원소의 대소관계를 비교하여 필요에 따라 교환을 반복하는 알고리즘으로 단순 교환 정렬이라고도 부릅니다.

#### 장 단점

- 장점: 구현이 쉽다, 코드가 직관적이다
- 단점: 굉장히 비효율적이다

#### 시간복잡도

- Best: O(n^2)
- AVG: O(n^2)
- Worst: O(n^2)

---

### Code

```js
const bubbleSort = function (arr) {
  // TODO: 여기에 코드를 작성합니다.
  let swap;
  do {
    swap = false;
    for (let i = 0; i < arr.length; i++) {
      if (arr[i] > arr[i + 1]) {
        let tmp = arr[i];
        arr[i] = arr[i + 1];
        arr[i + 1] = tmp;
        swap = true;
      }
    }
  } while (swap);
  return arr;
};
```

---

### 풀이 전 알아야 할 부분

[do...while](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/do...while) 문을 사용하였습니다.  
do = 구문, while = 조건식  
구문은 테스트 조건이 참일 때마다 한번 이상 실행이 됩니다. 루프 내에서 반복실행을 원한다면 {} 을 사용합니다.  
조건식은 루프가 실행될 때마다 평가되는 식입니다. 조건식이 참이였다면 구문이 다시 실행됩니다.

---
layout: post
title: 선택정렬
subtitle: Sort
type: 자료구조
createDate: 2021-01-02
solution: true
text: true
author: "codeFabian"
post-header: false
header-img: ""
order: 16
---

## 선택정렬

> 선택정렬은 알맞은 위치를 정해놓고, 어떤 원소를 넣을지 선택하는 알고리즘 입니다. (해당 내용은 오름차순정렬입니다.)

![img](https://upload.wikimedia.org/wikipedia/commons/9/94/Selection-Sort-Animation.gif)

## 시간복잡도

최선의 경우, 평균, 최악의 경우일때 모두 O(n^2) 으로 동일합니다.

## Code

```js
function selectionSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    let small = i;
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[small]) {
        small = j;
      }
    }
    let temp = arr[i];
    arr[i] = arr[small];
    arr[small] = temp;
  }
  return arr;
}
```

## 풀이

- 들어갈 위치를 선택합니다.
- i+1 번째 데이터부터 선택한 위치의 값과 비교를 합니다.
- 현재 선택한 자리의 값보다 순회 중인 값이 작다면, 위치를 갱신합니다.
- 두번째 반복문이 끝난 뒤에, small의 자리에 위치에 들어갈 값을 치환합니다.

## 장단점

**장점**

- 정렬을 위한 비교횟수는 많지만, 거품정렬 보다는 비교적 효율적입니다.
- 거품정렬과 동일하게, 정렬하고자 하는 배열안에서 교환하는 방식이기 때문에 다른 메모리공간이 요구되지 않습니다.

**단점**

- 시간복잡도가 O(n^2) 로 비효율적입니다.

## 콘솔로그로 알아보는 자리변화

```js
/*
selectionSort([34,22,10,19,17])
console.log([i, j, small])
[0,1,1]
[0,2,2]
[0,3,2]
[0,4,2]
--> small 인덱스는 2 
--> swap

*/
```

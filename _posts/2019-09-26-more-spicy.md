---
layout: post
title: "[프로그래머스] 힙(Heap) - Lv.2 - 더 맵게 (Python) / heapq"
author: Yurim Koo
categories:
  - Algorithm
tags:
  - 알고리즘
  - 프로그래머스
  - Python
  - Algorithm
  - Heap
  - Heap Sort
  - Python heapq
  - Heapq
  - Heap Queue
  - Priority Queue
  - 우선순위 큐
  - 파이썬 큐
  - 파이썬 힙
  - 파이썬 힙 큐
  - 파이썬 힙 정렬
comments: true
---

## 문제
매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다.  
모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.  

```섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)```  

Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.  
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때,  
모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.  

#### 제한 사항

- scoville의 길이는 1 이상 1,000,000 이하입니다.
- K는 0 이상 1,000,000,000 이하입니다.
- scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
- 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.

#### 입출력 예

|scoville|K|return|
|:--:|:--:|:--:|
|[1, 2, 3, 9, 10, 12]|7|2|

입출력 예 설명
1. 스코빌 지수가 1인 음식과 2인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.  
새로운 음식의 스코빌 지수 = 1 + (2 * 2) = 5  
가진 음식의 스코빌 지수 = [5, 3, 9, 10, 12]

2. 스코빌 지수가 3인 음식과 5인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.  
새로운 음식의 스코빌 지수 = 3 + (5 * 2) = 13  
가진 음식의 스코빌 지수 = [13, 9, 10, 12]

모든 음식의 스코빌 지수가 7 이상이 되었고 이때 섞은 횟수는 2회입니다.  

<br>

## 나의 풀이

<pre><code>import heapq

def solution(scoville, k):
    heapq.heapify(scoville)
    i = 0
    while scoville[0] < k:
        if len(scoville) > 1:
            heapq.heappush(scoville, heapq.heappop(scoville) + (heapq.heappop(scoville) * 2))
            i += 1
        else:
            return -1
    return i
</code></pre>

Python의 내장 모듈인 `heapq`를 이용해 풀었습니다.  
`heapq`를 이용하지 않으니 효율성 테스트를 통과하지 못하더라구요!

`heapq`는 힙 정렬(Heap Sort)을 Python에서 쉽게 사용할 수 있는 함수들로 구성되어 있습니다.  
힙 정렬과 heapq 모듈에 대해 포스팅 예정이니 기다려주세요 ;)  



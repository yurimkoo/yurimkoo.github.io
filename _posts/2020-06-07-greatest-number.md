---
layout: post
title: "[프로그래머스] 정렬 - Lv.2 - 가장 큰 수"
author: Yurim Koo
categories:
  - Algorithm
tags:
  - 알고리즘
  - 프로그래머스
  - Python
  - Algorithm
  - sort
  - sorted
  - Python sort
  - Python sorted
  - Python sorted reverse
  - Python 정렬 
  - 파이썬 정렬 
  - 파이썬 내림차순 정렬
  - 프로그래머스 가장 큰 수 python
  - 프로그래머스 가장 큰 수
comments: true
---

## 문제
0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

제한 사항
numbers의 길이는 1 이상 100,000 이하입니다.
numbers의 원소는 0 이상 1,000 이하입니다.
정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

#### 입출력 예

|numbers|return|
|:--:|:--:|
|[6, 10, 2]|"6210"|
|[3, 30, 34, 5, 9]|"9534330"|

<br>

## 나의 풀이

<pre><code>def solution(numbers):
    numbers = sorted(list(map(str, numbers)), key=lambda x: x*5, reverse=True)
    return str(int(''.join(numbers)))
</code></pre>

숫자로 이루어진 배열을 큰 수부터 정렬하여 문자열로 반환하는 문제입니다.  
각 배열을 문자열로 변경하고 문자열 곱하기를 해준 뒤 내림차순으로 정렬하면 원하는 결과를 얻을 수 있습니다.  

첫번째 입출력 예로 설명해보면, 아래와 같습니다.  
<pre>1) [6, 10, 2]  
2) ['6', '10', '2']  
3) ['66666', '1010101010', '22222']  
4) ['66666', '22222', '1010101010']</pre>
문자열은 숫자형과 달리 첫 글자가 큰 순서대로, 문자열의 길이가 긴 순서대로 크기 비교가 가능하다는 것을 이용했습니다.  

그리고 마지막에 결과값 리턴시 정수형으로 바꾼 후 문자열로 한 번 더 바꾸는 이유는 '0'의 케이스를 대비하기 위해서입니다. ['0', '0', '0', '0']이 주어졌을 때 `join`한 결과값이 '0000'이므로 이를 숫자 '0'으로 만들어주기 위해 두 번의 변경을 거칩니다.  


---
layout: post
title: "[프로그래머스] 완전탐색 - Lv.2 - 소수 찾기 (Python) / loop, 에라토스테네스의 체, 약수 구하기"
author: Yurim Koo
categories:
  - Algorithm
tags:
  - 알고리즘
  - 프로그래머스
  - Python
  - Algorithm
  - 완전탐색
  - Full Search
  - Python full search
  - Python for loop
  - Python while loop
  - Python loop
  - Python 완전탐색 
  - 파이썬 완전탐색
  - 파이썬 for 문 
  - 파이썬 while 문 
  - 파이썬 반복문 
  - 파이썬 루프 
  - Python 소수 찾기
  - python 에라토스테네스의 체
  - python 약수 
comments: true
---

## 문제
한자리 숫자가 적힌 종이 조각이 흩어져있습니다. 흩어진 종이 조각을 붙여 소수를 몇 개 만들 수 있는지 알아내려 합니다.  

각 종이 조각에 적힌 숫자가 적힌 문자열 numbers가 주어졌을 때, 종이 조각으로 만들 수 있는 소수가 몇 개인지 return 하도록 solution 함수를 완성해주세요.  

#### 제한 사항

- numbers는 길이 1 이상 7 이하인 문자열입니다.
- numbers는 0~9까지 숫자만으로 이루어져 있습니다.
- 013은 0, 1, 3 숫자가 적힌 종이 조각이 흩어져있다는 의미입니다.  

#### 입출력 예

|numbers|return|
|:--:|:--:|
|"17"|3|
|"011"|2|

입출력 예 설명  
예제 #1
[1, 7]으로는 소수 [7, 17, 71]를 만들 수 있습니다.  

예제 #2
[0, 1, 1]으로는 소수 [11, 101]를 만들 수 있습니다.  
- 11과 011은 같은 숫자로 취급합니다.  

<br>

## 나의 풀이

<pre><code>from itertools import permutations

def solution(s):
    answer = 0

    new_s = list(s)
    for i in range(2,len(s)+1):
        pm = list(permutations(s, i))
        for j in pm:
            if len(j) <= len(s):
                new_s.append(''.join(j))
    new_s = list(set([int(x) for x in new_s]))
    
    if new_s.count(1):
        new_s.remove(1)
    if new_s.count(0):
        new_s.remove(0)

    for x in new_s:
        i = 2
        while i*i <= x: 
            if x % i == 0:
                answer -= 1
                break
            i+=1
        answer += 1
    
    return answer
</code></pre>

일단 주어진 텍스트를 가지고 모든 경우의 수를 만들어내야 합니다.  
그러기 위해서 내장 모듈인 `itertools`의 `permutations` 라이브러리를 사용했습니다.  
(자매품 `combinations`, 참고: [itertools](https://docs.python.org/ko/3/library/itertools.html?highlight=permutations#itertools.permutations))  

`itertools.permutations(iterable[, r])`은 iterable로 만들 수 있는 길이 r의 순열을 return해줍니다.  
r을 지정하지 않은 경우 r은 iterable의 길이로 설정됩니다.  

경우의 수를 다 구했으면 이제 소수를 찾는데, 소수에 0과 1은 해당하지 않으므로 삭제해줍니다.  
많은 사람들이 '에라토스테네스의 체'를 사용해서 알고리즘을 풀었습니다.  

**에라토스테네스의 체**
1. 2부터 소수를 구하고자 하는 구간의 모든 수를 나열한다. 
2. 2는 소수이므로 prime number 배열에 2를 쓴다. 
3. 자기 자신을 제외한 2의 배수를 모두 지운다.
4. 남아있는 수 가운데 3은 소수이므로 prime number 배열에 3을 쓴다. 
5. 자기 자신을 제외한 3의 배수를 모두 지운다.
6. 남아있는 수 가운데 5는 소수이므로 prime number 배열에 5를 쓴다. 
7. 자기 자신을 제외한 5의 배수를 모두 지운다.
8. 남아있는 수 가운데 7은 소수이므로 prime number 배열에 7을 쓴다. 
9. 자기 자신을 제외한 7의 배수를 모두 지운다.
10. 위의 과정을 반복하면 구하는 구간의 모든 소수가 남는다.  

간단히 원리를 설명하면 위와 같은데, 이 방식을 이용하면 효율적으로 소수를 찾을 수 있다고 합니다.  
위 알고리즘을 구현하는 코드는 아래와 같습니다.  

<pre><code>def prime_list(n):
    # 에라토스테네스의 체 초기화: n개 요소에 True 설정(소수로 간주)
    sieve = [True] * n

    # n의 최대 약수가 sqrt(n) 이하이므로 i=sqrt(n)까지 검사
    m = int(n ** 0.5)
    for i in range(2, m + 1):
        if sieve[i] == True:           # i가 소수인 경우 
            for j in range(i+i, n, i): # i이후 i의 배수들을 False 판정
                sieve[j] = False

    # 소수 목록 산출
    return [i for i in range(2, n) if sieve[i] == True]
</code></pre>

출처: [위키백과](https://ko.wikipedia.org/wiki/%EC%97%90%EB%9D%BC%ED%86%A0%EC%8A%A4%ED%85%8C%EB%84%A4%EC%8A%A4%EC%9D%98_%EC%B2%B4)

저는 에라토스테네서의 체 방식을 사용해 문제를 풀지는 않았고,  
가장 쉬운 접근으로 약수를 찾는 방법을 사용해 해결했습니다.  
소수는 약수를 1과 자기 자신만 가지므로 약수의 길이가 2로 일정합니다.  

2, 3은 소수이므로 4부터 반복을 시작해서 자기 자신 외에 나누어 떨어지는 수가 있으면 break를,  
나누어 떨어지는 수가 없으면 answer에 +1을 하는 식입니다.  

이상, 소수 찾기였습니다!  
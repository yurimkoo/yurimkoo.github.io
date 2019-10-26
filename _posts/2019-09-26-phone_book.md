---
layout: post
title: "[프로그래머스] 해시(Hash) - Lv.2 - 전화번호 목록 (Python) / sort, 정규표현식, startswith"
author: Yurim Koo
categories:
  - Algorithm
tags:
  - 알고리즘
  - 프로그래머스
  - Python
  - Algorithm
  - sort
  - Hash
  - Python sort
  - Python Hash
  - Python prefix
  - Python startswith
  - Python regex
  - Python 정규표현식
  - 파이썬 정렬
  - 파이썬 sort
  - 파이썬 접두어
  - 파이썬 접두사 
  - 파이썬 정규표현식
comments: true
---

## 문제
전화번호부에 적힌 전화번호 중, 한 번호가 다른 번호의 접두어인 경우가 있는지 확인하려 합니다.  
전화번호가 다음과 같을 경우, 구조대 전화번호는 영석이의 전화번호의 접두사입니다.  

- 구조대 : 119
- 박준영 : 97 674 223
- 지영석 : 11 9552 4421  

전화번호부에 적힌 전화번호를 담은 배열 phone_book 이 solution 함수의 매개변수로 주어질 때,  
어떤 번호가 다른 번호의 접두어인 경우가 있으면 false를 그렇지 않으면 true를 return 하도록 solution 함수를 작성해주세요.  

#### 제한 사항

- phone_book의 길이는 1 이상 1,000,000 이하입니다.
- 각 전화번호의 길이는 1 이상 20 이하입니다.  

#### 입출력 예

|phone_book|return|
|:--:|:--:|
|["119", "97674223", "1195524421"]|false|
|["123", "456", "789"]|true|
|["12", "123" , "1235", "567", "88"]|false|

입출력 예 설명  
입출력 예 #1  
앞에서 설명한 예와 같습니다.  

입출력 예 #2  
한 번호가 다른 번호의 접두사인 경우가 없으므로, 답은 true입니다.  

입출력 예 #3  
첫 번째 전화번호, “12”가 두 번째 전화번호 “123”의 접두사입니다. 따라서 답은 false입니다.  

<br>

## 나의 풀이

<pre><code>def solution(phone_book):
    phone_book.sort()

    min_word = phone_book.pop(0)
    phone_book = [phone[:len(min_word)] for phone in phone_book]

    if min_word in phone_book:
        return False
    else:
        return True
</code></pre>

실무에서는 텍스트 비교, 텍스트 포함 여부 등을 다룰 때 **정규표현식**을 주로 사용합니다.  
하지만 이번 문제에서는 모듈을 사용하지 않고 풀기 위해 정규표현식을 사용하지 않았습니다.  

제 아이디어는 정렬을 사용해 제일 작은 전화번호의 길이만큼 모두 슬라이싱하고 'in'을 통해 비교하는 것인데요.  
반복문을 사용하기 싫어서 이런 방식으로 풀었습니다.  
이 방법 외에도 `startswith()`를 사용하는 등의 다른 방법도 다양하게 있습니다.  

만약 정규표현식을 사용하게 된다면 아래와 같은 풀이도 가능하겠네요.  
(정규표현식 포스팅 [보러가기](https://yurimkoo.github.io/analytics/2019/10/26/regular_expression.html))
<pre><code>import re

phone_book.sort()
min_word = phone_book.pop(0)

# 반복문 사용 X
if sum(list(map(lambda x: re.search("^" + min_word, x), phone_book))) > 0:
  return False
else:
  return True

# 반복문 사용 O
for x in phone_book:
  if bool(re.search("^" + min, x)):
    return False
  else:
    return True
</code></pre>

물론 이 외에도 다양한 방법이 있다는 것! 잊지 마세요 :D  

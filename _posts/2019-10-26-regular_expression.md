---
layout: post
title: "정규표현식 (Regular Expression) 이해하기"
author: Yurim Koo
categories:
  - Analytics
tags:
  - 데이터 분석
  - 데이터 전처리
  - 텍스트 마이닝 
  - 자연어 처리 
  - 정규표현식
  - Data Analytics
  - Data Preprocessing
  - Text Mining
  - Natural Language Processing
  - NLP
  - Regular Expression
  - Regex
  - Regexp
  - Python re
  - R stringr 
  - SQL REGEXP  
comments: true
---

## 정규표현식 이해하기

<br>

### 1. 정규표현식(Regular Expression)이란?

**정규표현식**은 특정한 규칙을 가진 문자열의 집합을 표현하는데 사용하는 형식 언어입니다.  
문자열을 처리하는 방법 중의 하나로, 특정한 조건의 문자를 '검색'하거나 '치환'하는 과정을 매우 간편하게 처리할 수 있도록 해주는 수단입니다.  

정규표현식은 간단한 쓰임부터 고급 스킬까지 다양한 사용법이 있는데요.  
간단한 사용법만 알아두어도 텍스트 데이터를 다루는데 아주 큰 도움이 될 것입니다.  
정규표현식은 SQL부터 스크립트 언어까지 다양한 곳에서 활용할 수 있기 때문이지요.  

<br>

### 2. 정규표현식의 '패턴(Pattern)'

<figure>
  <img data-action="zoom" src='{{ "/assets/img/regular_exp/1.png" | relative_url }}' alt='absolute'>
</figure>

정규표현식은 **패턴**을 사용해서 문자열을 처리합니다.  
기본 원리는 위 그림과 같이 대상 문자열에 정규표현식을 적용해서 찾을 문자열을 검색해내는 것입니다.  
조건에 맞는 문자열을 찾은 이후 추출 또는 치환할 수 있습니다.  

<br>

### 3. 패턴의 종류

정규표현식 패턴은 종류가 아주 많습니다.  
각 패턴을 개별적으로 사용할 수도 있지만 **함께 혼합**해서 사용할 때 그 효과가 배가 됩니다.  
이 글에서는 텍스트 데이터를 다룰 때 자주 쓰이는 패턴 몇 가지만 소개해보도록 하겠습니다.  

#### 매칭 

|패턴|기능|사용 예시|설명|
|:--:|:--:|:--:|:--:|
|`.`|문자 하나|`"..."`|문자열의 길이가 세 글자 이상인 것을 찾음.|
|`|`|또는 (OR). `|`로 구분된 문자에 해당하는 문자열을 찾음.|`"데이터|데이타"`|'데이터' 또는 '데이타'에 해당하는 문자열을 찾음.|
|`[]`|`[]` 안에 나열된 패턴에 해당하는 문자열을 찾음.|`"[123]d"`|대상 문자열에서 '1d' 또는 '2d' 또는 '3d'인 문자열을 찾음.|
|`^`|시작하는 문자열을 찾음.|`"^안녕"`|대상 문자열에서 '안녕'으로 시작하는 문자열을 찾음.|
|`$`|끝나는 문자열을 찾음.|`"잘가$"`|대상 문자열에서 '잘가'로 끝나는 문자열을 찾음.|

#### 수의 제한 

|패턴|기능|사용 예시|설명|
|:--:|:--:|:--:|:--:|
|`*`|0회 이상 나타나는 문자|`"a*"`|'a'가 0번 이상 등장하는 문자열을 찾음. 'b', 'a', 'aa' 모두 해당.|
|`+`|1회 이상 나타나는 문자|`"국+"`|'국'이 1번 이상 등장하는 문자열을 찾음. '한국', '미역국', '국거리' 모두 해당.|
|`{m,n}`|m회 이상 n회 이하 나타나는 문자|`"치{1,2}"`|'치'가 1회 이상 2회 이하 등장하는 문자열을 찾음. '치커리', '치카치카' 모두 해당.|
|`?`|0~1회 나타나는 문자|`"[가나다]?"`|'가' 또는 '나' 또는 '다'가 0~1회 등장하는 문자열을 찾음. '가지마', '나라', '안녕' 모두 해당.|

#### 문자 그룹 

|패턴|기능|사용 예시|설명|
|:--:|:--:|:--:|:--:|
|`[A-z]` 또는 `[:alpha:]` 또는 `\a`|알파벳 대문자 또는 소문자인 문자열을 찾음|`"[A-z]+"`|대상 문자열에서 알파벳이 한 개 이상인 문자열을 찾음|
|`[0-9]` 또는 `[:digit:]` 또는 `\d`|숫자인 문자열을 찾음|`"^[0-9]+"`|한 개 이상의 숫자로 시작하는 문자열을 찾음|

#### 부정 

|패턴|기능|사용 예시|설명|
|:--:|:--:|:--:|:--:|
|`[^문자]`|괄호 안의 문자를 포함하지 않은 문자열을 찾음|`"[^블로그]"`|'블' 또는 '로' 또는 '그'를 포함하지 않는 문자열을 찾음. '블루', '로그', '그리고' 모두 제외됨.|

<br>

### 4. 정규표현식의 활용 

#### MySQL - REGEXP

SQL에서 정규표현식을 활용하면 기본 연산자보다 복잡한 문자열 조건을 걸어 데이터를 검색할 수 있습니다.  
정규표현식을 사용하지 않는 SQL 연산자와 정규표현식을 활용할 수 있는 `REGEXP`를 통해 비교해보겠습니다.  

**(1) '안녕' 또는 '하이'로 시작하는 문자열을 찾고 싶을 때**

<pre><code># 정규표현식을 사용하지 않을 때 
SELECT *
FROM tbl  
WHERE data LIKE '안녕%' OR data LIKE '하이%';

# 정규표현식을 사용할 때 
SELECT *
FROM tbl
WHERE data REGEXP ('^안녕|^하이');</code></pre>

**(2) 길이 7글자인 문자열 중 2번째 자리부터 abc를 포함하는 문자열을 찾고 싶을 때**

<pre><code># 정규표현식을 사용하지 않을 때
SELECT *
FROM tbl
WHERE CHAR_LENGTH(data) = 7 AND SUBSTRING(data, 2, 3) = 'abc';

# 정규표현식을 사용할 때
SELECT *
FROM tbl
WHERE data REGEXP ('^.abc...$');</code></pre>

**(3) 텍스트와 숫자가 섞여 있는 문자열에서 숫자로만 이루어진 문자열을 찾고 싶을 때**

<pre><code># 정규표현식을 사용하지 않을 때
SELECT *
FROM tbl
WHERE data LIKE ??????????

# 정규표현식을 사용할 때
SELECT *
FROM tbl
WHERE data REGEXP ('^[0-9]+$'); 
-- OR data REGEXP ('^\d$') 
-- OR data REGEXP ('^[:digit:]$');</code></pre>

정규표현식을 사용하면 쿼리문이 아주 간단해지면서, 복잡한 검색 조건까지 만족시킬 수 있습니다.  

#### REGEXP in R, Python

R과 Python에서도 텍스트 데이터를 처리할 때 정규표현식을 이용할 수 있습니다.  

**R**

|함수|기능|사용 예시|반환값|
|:--:|:--:|:--:|:--:|
|`grep`|패턴과 일치하는 문자열의 index 반환|`grep('안녕', '안녕하세요')`|1|
|`grepl`|패턴과 일치하는 문자열 여부 (TRUE/FALSE) 반환|`grepl('안녕', '안녕하세요')`|TRUE|
|`gsub`|패턴과 일치하는 문자열을 치환할 때|`gsub(' +', '', '안 녕  하세  요?')`|'안녕하세요?|

간단하게 내장 함수의 기능들만 소개했습니다만, R에서 텍스트 데이터를 다룰 때 아주 유용한 `stringr`이라는 패키지가 존재합니다. ([stringr 문서 바로가기](https://cran.r-project.org/web/packages/stringr/stringr.pdf))  

**Python**

Python에서는 `re`라는 모듈이 정규식을 다룰 때 활용됩니다. ([re 문서 바로가기]())  
텍스트를 정규식 객체로 돌려주는 `compile` 함수를 통해 먼저 패턴을 만들어 보겠습니다.  

`p = re.compile('[a-z]+')`

|함수|기능|사용 예시|반환값|
|:--:|:--:|:--:|:--:|
|`match`|문자열의 처음부터 정규식과 매치되는지 조사|`p.match('python').group()`|'python'|
|`search`|문자열 전체를 검색하여 정규식과 매치되는지 조사|`p.search('123 python').group()`|'python'|
|`findall`|정규식과 매치되는 모든 문자열을 리스트로 반환|`p.findall('hello my name is')`|['hello', 'my', 'name', 'is']|

`match`와 `search`는 그냥 리턴하면 객체 값이 반환되므로 `.group()`을 붙여주어야 결과 값을 확인할 수 있습니다.  

<br>

### 5. 정규표현식 테스팅을 위한 유용한 사이트 

마지막으로 정규표현식 패턴을 실제 적용하기 전 테스트를 해보고 싶다면 아래 사이트를 추천합니다.  

#### (1) RegExr

**[https://regexr.com/](https://regexr.com/)**  

'Expression'에 정규식 패턴을 입력하고 'Text'에 대상 문자열을 넣으면 색깔로 해당되는 문자열을 보여줍니다.  
더불어 아래에 설명 등의 다양한 기능도 제공하고 있습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/regular_exp/5.png" | relative_url }}' alt='absolute'>
</figure>

#### (2) REGEXPER

**[https://regexper.com/](https://regexper.com/)**  

정규표현식 패턴을 입력하면 대상 문자열에서 어떠한 원리로 매칭시키게 되는지 도식화해서 보여줍니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/regular_exp/2.png" | relative_url }}' alt='absolute'>
</figure>

<br>

정규표현식에 대해 기본적인 부분을 알아보았습니다.  
정규표현식의 고급 패턴에 대한 포스팅도 기대해주세요 :D ! 

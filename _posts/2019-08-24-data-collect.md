---
layout: post
title: "데이터 수집 - 크롤링을 통한 네이버 장소 정보 수집하기 (with BeautifulSoup)"
author: Yurim Koo
categories:
  - Python
tags:
  - Python

comments: true
---

데이터 분석은 크게 4단계로 나뉩니다.  

**1) 데이터 수집**   
2) 데이터 전처리  
3) 데이터 적재  
4) 데이터 분석  

데이터 분석의 가장 시초가 되는 데이터를 수집하는 방법에 대해 알아보겠습니다.  

-----

### 크롤링 (Crawling)

크롤링(crawling) 혹은 스크래핑(scraping)은 웹 페이지의 소스를 가져와 그 내용에서 필요한 정보를 추출해내는 행위입니다.

크롤링하는 방법은 다양합니다.
- 웹 페이지의 소스를 그대로 가져와 원하는 정보를 직접 파싱
- 정보가 필요한 웹 페이지에서 제공하는 오픈API(Rest API)를 이용하여 데이터 추출
- 브라우저를 프로그래밍으로 조작해서 필요한 데이터 추출 

이 글에서는 웹 페이지의 소스를 그대로 가져와 원하는 정보를 직접 파싱하는 방법을 알아볼 것입니다.  

<br>

### curl 이용 방식과 태그 파싱 방식 (Requests와 BeautifulSoup)

웹 페이지의 소스에는 다양한 HTML/CSS 태그들이 존재합니다. 때로는 JSON 형식으로 된 소스가 첨부되어 있기도 하지요.  

여러 가지 방식으로 존재하는 소스를 파싱하는 방법은 크게 2가지로 나뉩니다.  
- curl 기반 방식
- 태그 파싱 방식  

#### curl 

curl이란, 다양한 프로토콜로 데이터를 전송해볼 수 있는 명령어 기반의 컴퓨터 프로그램입니다.  
클라이언트에서 커맨드 라인이나 소스코드로 손 쉽게 웹 브라우저 처럼 활동할 수 있도록 해주는 기술입니다.  
curl 방식으로 크롤링을 하게 되면, 쉽게 말해 **웹 페이지에 접근한 클라이언트인 척**을 하는 것입니다.  

curl 방식의 크롤링을 위해서는 웹 페이지, 네트워크에 대한 이해가 필요합니다.  
클라이언트인 척을 하기 위해 필요한 페이지를 가져오기 전 단계의 행동을 코드로 표현해야 하지요.  
헤더, 쿠키, url 등에 대한 기본 지식이 필요합니다. (관련 정보는 본 블로그의 [HTTP 카테고리 (첫 글로 이동)](https://yurimkoo.github.io/http/2019/07/30/http-the-definitive-guide-1-1.html)에 실려 있습니다!)  

curl 방식의 크롤링을 위해 Python의 `requests` 라이브러리가 필요합니다.  
사용자인 척을 해야 하므로, Request URL, Request Method, Headers 등의 정보를 웹과 동일하게 만들어야 하는데요.  
이 힌트는 크롬의 '검사' 기능의 'Network'에서 얻을 수 있습니다.  

이 방식은 다소 복잡할 수 있으므로 다른 글에서 소개하도록 하겠습니다.  

#### 태그 파싱 

웹 페이지의 소스에 존재하는 HTML/CSS 태그를 파싱하여 원하는 데이터를 가져오는 방식입니다.  
기본적인 HTML/CSS 태그에 대한 이해가 필요합니다.  

태그 파싱 방식의 크롤링을 위해 Python의 `requests`, `BeautifulSoup` 라이브러리가 필요합니다.  
`requests` 라이브러리를 이용해 웹 페이지의 소스를 가져오고, 그 안의 태그를 `BeautifulSoup`를 이용해 파싱하는 방식입니다.  

<br> 

### BeautifulSoup를 이용해 네이버 장소의 상세페이지를 가져와보자   

크롤링 방식을 알리기 위한 예시로 네이버 지도에서 강남역 교보문고의 정보를 가져와보겠습니다.  
네이버에는 다양한 오픈API가 있으며, 해당 기능을 이미 제공하는 API가 존재합니다. ([네이버 오픈API 바로가기](https://developers.naver.com/docs/search/local/))  
본 방식은 사용법을 알리기 위한 예시입니다. :)  

#### 1) 라이브러리 설치  

<pre><code>pip intall requests
pip install bs4</code></pre>

먼저 `requests`와 BeautifulSoup 라이브러리 `bs4`를 설치합니다.  
(라이브러리에 대한 자세한 소개는 [requests](http://docs.python-requests.org/en/master/), [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)으로)  

#### 2) 기본 가정  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/crawling-naver/1-1.png" | relative_url }}' alt='absolute'>
</figure>

- 가져올 페이지: 교보문고 강남점 ([https://map.naver.com/local/siteview.nhn?code=11779539](https://map.naver.com/local/siteview.nhn?code=11779539))
- 가져올 정보: 장소명, 카테고리, 설명, 전화번호, 지번 주소, 도로명 주소, 홈페이지, 이용시간, 리뷰 수 

#### 3) 
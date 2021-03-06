---
layout: post
title: "HTTP 완벽 가이드 [1-2] - URL과 리소스"
author: Yurim Koo
categories:
  - HTTP
tags:
  - HTTP
  - Web Development
  - Web
  - HTTP The Definitive Guide
  - 네트워크
  - URL
  - 리소스
  - resource
  - URN
comments: true
---

'[HTTP 완벽가이드](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788966261208)' 책의 내용을 요약 정리한 시리즈입니다.  
(사진 출처: 인터넷 교보문고)
<figure>
  <img data-action="zoom" src='{{ "/assets/img/http-1-1/1-0.png" | relative_url }}' alt='absolute'>
</figure>

<br>

**HTTP 시리즈**

- [HTTP 완벽 가이드 [1-1] - HTTP 개관](https://yurimkoo.github.io/http/2019/07/30/http-the-definitive-guide-1-1.html)  
- HTTP 완벽 가이드 [1-2] - URL과 리소스
- [HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (1)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-1.html)  
- [HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (2)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-2.html)  
- [HTTP 완벽 가이드 [1-4] - 커넥션 관리](https://yurimkoo.github.io/http/2019/08/23/http-the-definitive-guide-1-4.html)  

<br>

------
# 1부. HTTP: 웹의 기초 
## 2장. URL과 리소스 

<br>
### 2.1 URL과 리소스 
- URL은 Uniform Resource Locator의 약자로, 인터넷의 리소스를 가리키는 표준 이름 
- URL은 웹 상의 리소스가 어디에 있고 어떻게 접근할 수 있는지 알려주는 역할 

<br>

### 2.2 인터넷의 리소스 탐색하기 

<figure>
  <img data-action="zoom" src='{{ "/assets/img/http-1-2/1-1.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>


- URL은 리소스의 위치를 설명해서 리소스를 식별 
- HTTP 명세에서는 URI(Uniform Resource Identifier)를 더 일반화 된 개념의 리소스 식별자로 사용하지만,  
실제로 HTTP 애플리케이션은 URL을 URI의 한 부분으로 취급 
- 대부분의 URL은 '스킴://서버위치/경로' 구조

#### 1) URL 문법 
- 대부분의 URL은 일반 URL 문법을 따름 
> <스킴>://<사용자이름>:<비밀번호>@<호스트>:<포트>/<경로>;<파라미터>?<질의>#<프래그먼트>  

|컴포넌트|설명|기본값|
|--|------|--|
|스킴|서버에 접근하는 프로토콜 지시| |
|사용자 이름|몇몇 스킴은 리소스에 접근하기 위해 사용자 이름이 필요|anonymous|
|비밀번호|사용자의 비밀번호|<이메일주소>|
|호스트|리소스를 호스팅하는 서버의 호스트 명이나 IP 주소| |
|포트|리소스를 호스팅하는 서버가 열어 놓은 포트 번호. 많은 스킴이 기본 포트를 가지고 있음 (HTTP는 80)|스킴에 따라 상이|
|경로|서버 내 리소스가 어디에 있는지 가리킴| |
|파라미터|이름/값을 쌍으로 가짐| |
|질의|스킴에서 애플리케이션에 파라미터를 전달하는데 쓰임. URL의 끝에 '?'로 구분| |
|프래그먼트|리소스의 조각이나 일부분을 가리킴. 클라이언트에서만 사용. URL의 끝에 '#'으로 구분| |

**(1) 스킴: 사용할 프로토콜**

- 주어진 리소스에 어떻게 접근하는지 알려주는 중요한 정보
- 스킴 컴포넌트는 알파벳으로 시작해야 하고 URL의 나머지 부분들과 ':'로 구분
- 대소문자를 가리지 않음

**(2) 호스트와 포트**

- 리소스를 호스팅하고 있는 장비와, 그 장비 내에서 리소스에 접근할 수 있는 서버가 어디에 있는지 알려주는 역할
- 호스트 컴포넌트는 접근하려고 하는 리소스를 가지고 있는 인터넷 상의 호스트 장비를 가리킴; 호스트 명이나 IP 주소로 제공 
- 포트 컴포넌트는 서버가 열어 놓은 네트워크 포트를 가리킴; 내부적으로 TCP 프로토콜을 사용하는 HTTP는 기본 포트로 80을 사용

**(3) 사용자 이름과 비밀번호**

> _ftp://ftp.prip.ai.mit.edu/pub/gnu # 사용자 이름, 비밀번호 없음_  
> _ftp://**joe:joespasswd**@www.joes-hardware.com/sales_info.txt # 사용자 이름, 비밀번호 있음_

- 애플리케이션이 FTP 서버처럼 사용자 이름과 비밀번호를 요구하는 URL 스킴을 사용할 때, 그 값들이 삽입되지 않을 경우 기본 값을 넣어 놓음
- ':' 문자로 사용자 이름과 비밀번호를 연결
- '@' 문자로 URL과 사용자 이름과 비밀번호 컴포넌트를 분리 

**(4) 경로**

- URL의 경로 컴포넌트는 리소스가 있는 서버의 위치를 알려줌
- 계층적 파일 시스템 경로와 유사한 구조
- HTTP URL에서 '/'를 기준으로 경로조각으로 나뉘고, 각 경로조각은 자체만의 파라미터 컴포넌트를 가질 수 있음 

**(5) 파라미터**

- 애플리케이션이 서버에 정확한 요청을 하기 위해 필요한 입력 파라미터를 받는데 사용
- 이름/값 쌍의 리스트로, URL 나머지 부분들로부터 ';'로 구분하여 URL에 기술

> _ftp://prep.ai.mit.edu/pub/gnu;**type=d**_

- 위 예시의 경우, 이름은 'type'이고, 값은 'd'인 `type=d`라는 단 한개의 파라미터를 전달

> _http://www.joes-hardware.com/hammers;**sale=false**/index.html;**graphics=true**_

- 위 예시의 경우, hammers와 index.html이라는 두 개의 경로조각이 존재 
- hammers 경로조각은 `sale=false` 파라미터를 가지고, index.html 경로조각은 `graphics=true` 파라미터를 가짐

**(6) 질의 문자열**

> _https://stackoverflow.com/search?**q=github**_

- URL의 질의 컴포넌트는 게이트웨이를 가리키는 URL의 경로 컴포넌트와 함께 전달
- 위 질의는 [stack overflow](https://stackoverflow.com) 검색창에 'github'라고 입력했을 때의 결과인데, 질의 컴포넌트로 `q=github`이 쓰였음을 알 수 있음 
- 편의상 많은 게이트웨이가 '&'로 나뉜 '이름=값' 쌍 형식의 질의 문자열을 활용

**(7) 프래그먼트**

- 리소스의 특정 부분을 가리킬 수 있도록, URL은 HTML 문서에 있는 특정 이미지나 일부분을 가리킬 수 있음 
- 프래그먼트는 URL의 오른쪽에 '#' 문자에 이어서 존재 
- 일반적으로 HTTP 서버는 객체의 일부가 아닌 전체만 다루기 때문에 클라이언트는 서버에 프래그먼트를 전달하지 않음
- 브라우저가 서버로부터 전체 리소스를 내려 받은 후, 프래그먼트를 사용하여 사용자가 보고자 하는 리소스의 일부를 보여줌

> _https://docs.python.org/ko/3/howto/logging.html#**a-simple-example**_  

- 위 URL에서 '#'을 기준으로 입력하든, 하지 않든 보여지는 페이지는 같지만 보여지는 위치가 다름 

<br>

### 2.3 단축 URL

#### 1) 절대 URL & 상대 URL

- 절대 URL: 리소스에 접근하는데 필요한 모든 정보를 가짐
- 상대 URL: 모든 정보를 담고 있지는 않은 URL을 짧게 표현하는 방식
  - 필요한 모든 정보를 얻기 위해서는 기저 (base) URL 사용
  - 스킴, 호스트 및 다른 컴포넌트를 모두 입력하지 않아도 됨
  - 한 마디로, 기저 URL을 제외한 부분을 서술한 것이라고 생각하면 쉬움 

> _http://www.joes-hardware.com # 기저 URL_  
> _./hammers.html # 상대 URL_  
> => _http://www.joes-hardware.com/hammers.html # 새로운 절대 URL_  

상대 URL을 절대 URL로 변환하는 방법은 아래와 같음.

**(1) 기저 URL 찾기**

- 리소스에서 명시적으로 제공
  - <BASE> HTML 태그를 이용해 명시적으로 기저 URL 기술 

- 리소스를 포함하고 있는 기저 URL
  - 찾고자 하는 리소스가 명시되지 않은 리소스에 포함된 경우, 해당 리소스의 URL을 기저 URL로 사용 가능 

- 기저 URL이 없는 경우
  - 보통 이런 경우는 절대 URL만으로 이루어져 있다는 뜻이지만 불완전하거나 깨진 URL일 수도 있음 

**(2) 상대 참조 해석하기 (=URL 분해하기)**

- 기저 URL을 찾았으면 상대 URL과 절대 URL을 각각 컴포넌트 조각으로 분리 (=URL 분해하기)
- 상대 URL을 절대 경로 형태로 변환하는 알고리즘에 따라 변환 
  - 간단하게 말하면, 스킴, 컴포넌트, 파라미터 등이 부재일 경우 기저 URL의 것을 상속  

#### 2) URL 확장

- URL의 일부만 입력해도 자동으로 URL을 확장해주는 기능

**(1) 호스트명 확장**

- 예를 들어, 주소 입력란에 'naver'만 입력하면, 브라우저는 호스트명에 자동으로 'www.'와 '.com'을 붙여서 'www.naver.com'을 만듦 
- 특정 브라우저는 확장할 URL을 찾지 못하면 확장을 포기하기 전에 다른 대안을 제시
- 단, 프락시와 다른 HTTP 애플리케이션에 문제 발생 가능성 존재 

**(2) 히스토리 확장**

- 과거 사용자가 방문한 웹 사이트의 기록을 저장해놓고 불러오는 방식
- URL을 입력하면, 앞 글자들을 포함하는 완결된 형태의 URL을 선택하게 해줌
- 예를 들어, 'www.nav'까지 입력하면 브라우저는 'www.naver.com'을 보여줄 것 

<br>

### 2.4 안전하지 않은 문자 

- 안전한 전송: 정보가 유실될 위험 없이 URL을 전송할 수 있다는 것
- 전자메일에 사용되는 SMTP (Simple Mail Transfer Protocol) 등에 의해 문자가 제거되는 일을 피하고자 상대적으로 작고 일반적으로 안전한 **알파벳 문자**만 포함하도록 허락
- 알파벳 이외의 문자를 포함할 수 있도록 이스케이프 기능 추가 

#### 1) URL 문자 집합

- 역사적으로 많은 애플리케이션이 US-ASCII 문자 집합 사용; 만들어진 지 오래되어 표현 가능한 문자의 수가 적음
- URL이 특정 이진 데이터를 포함해야 하는 경우 존재 
- 따라서 **이스케이프 문자열**을 쓸 수 있도록 설계 
  - US-ASCII에서 사용이 금지된 문자들로, 특정 문자나 데이터를 인코딩할 수 있게 함으로써 이동성과 완성도를 높임 

#### 2) 인코딩 체계

- URL의 안전하지 않은 문자를 인코딩하기 위해 퍼센티지 기호(%)로 시작해 ASCII 코드로 표현되는 두 개의 16진수 숫자로 이루어진 이스케이프 문자로 변환 

|문자|ASCII 코드|URL의 예|
|--|:--|:--|
|~|126 (0x7E)|http://github.yurimkoo.io/%7Eyur|
|빈 문자|32 (0x20)|http://github.yurimkoo.io/more%20about.html|
|%|37 (0x25)|http://github.yurimkoo.io/100%25happy.html|  

#### 3) 문자 제한  

- URL 내에서 특별한 의미로 예약된 문자 존재
- 예약 문자를 다른 목적으로 사용하려면 그 전에 반드시 인코딩 필요

|문자|선점 및 제한|
|:--:|:--|
|%|이스케이프 토큰|
|/|경로 세그먼트 분리|
|?|질의 문자열 구획 문자|
|;|파라미터 구획 문자|
|:|스킴, 이름/비밀번호, 호스트/포트 구획 문자|
|@&=|특정 스킴에서 의미가 있기 때문에 선점|
|[]\~|게이트웨이 등 여러 전송 에이전트에서 불안전하게 다루기 때문에 제한됨|

<br>

### 2.5 웹에서 쓰이는 일반 스킴의 종류

종류는 많지만 대표적인 세 개만 서술 

- http
  - 일반 URL 포맷을 지키는 하이퍼텍스트 전송 프로토콜
  - 기본 포트값: 80

- https
  - http와 거의 같음
  - 단, 커넥션 양 끝단에서 암호화하기 위해 넷스케이프에서 개발한 보안 소켓 계층(SSL; Secure Socket Layer)을 사용
  - 기본 포트값: 443

- ftp (File Transfer Protocol; 파일 전송 프로토콜) 
  - FTP 서버에 있는 파일을 올리거나 내리고, FTP 서버 디렉토리의 콘텐츠 목록을 가져오는데 활용 가능
  - 웹 애플리케이션이 데이터에 접근하는 용도의 스킴으로 사용 

<br>

### 2.6 URL의 미래, URN

- URL의 장점과 단점
  - 장점 
    - 모든 객체에 이름을 지을 수 있음 
    - 새로운 포맷을 쉽게 추가할 수 있음 
    - 인터넷 프로토콜 간의 일관된 작명 규칙을 제공
  - 단점
    - 특정 시점의 위치를 알려주는 역할을 하므로 리소스가 옮겨지면 더는 사용할 수 없음
- 이러한 단점을 극복하기 위해; **URN(Uniform Resource Name)** 등장 
  - URN은 객체가 이동해도 항상 객체를 가리킬 수 있는 이름 제공
  - 지속 통합 자원 지시자(Persistent Uniform Resource Locator, PURL)를 사용하면 URL로 URN의 기능을 제공할 수 있음 

<br>
---
layout: post
title: "HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (1)"
author: Yurim Koo
categories:
  - HTTP
tags:
  - HTTP
  - Web Development
  - Web
  - HTTP The Definitive Guide
  - 네트워크
  - HTTP 메시지
  - HTTP Message
  - HTTP 메서드 
  - HTTP Methods
  - HTTP 상태 코드
  - HTTP Status Code
  - HTTP 헤더
  - HTTP Headers 
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
- [HTTP 완벽 가이드 [1-2] - URL과 리소스](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-2.html)
- HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (1)
- [HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (2)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-2.html)  
- [HTTP 완벽 가이드 [1-4] - 커넥션 관리](https://yurimkoo.github.io/http/2019/08/23/http-the-definitive-guide-1-4.html)  

<br>

------
# 1부. HTTP: 웹의 기초 
## 3장. HTTP 메시지 

<br>
### 3.1 메시지의 흐름 
- HTTP 메시지는 HTTP 애플리케이션 간에 주고 받은 데이터의 블록들
- 데이터의 블록들은 메시지의 내용과 의미를 설명하는 텍스트 메타 정보로 시작
- 메시지는 클라이언트, 서버, 프락시 사이를 흐름
- 인바운드, 아웃바운드, 업스트림, 다운스트림은 메시지의 방향을 의미 

#### 1) 메시지는 원 서버 방향을 인바운드로 하여 송신된다

<figure>
  <img data-action="zoom" src='{{ "/assets/img/http-1-3/1-1.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

- 인바운드: 메시지가 원 서버로 향하는 이동 
- 아웃바운드: 모든 처리가 끝난 뒤 메시지가 사용자 에이전트로 돌아오는 것 

#### 2) 다운스트림으로 흐르는 메시지

- 모든 메시지는 다운스트림으로 흐름 
- 즉, 메시지의 발송자는 수신자의 업스트림이 됨 

<br>

### 3.2 메시지의 각 부분

- HTTP 메시지는 단순한, 데이터의 구조화된 블록 
- 클라이언트 요청이나 서버 응답 중 하나를 포함
- 시작줄, 헤더, 본문으로 구성 
  - 시작줄 & 헤더: 줄 단위로 분리된 아스키(ASCII) 문자열. 각 줄은 캐리지 리턴과 개행 문자로 구성된 두 글자의 줄바꿈으로 끝나고, 이 줄바꿈 문자열을 CRLF라고 칭함.
  - 본문은 선택적인 데이터 덩어리. 텍스트나 이진 데이터를 포함할 수도 있고, 그냥 비어 있을 수도 있음.

#### 1) 메시지 문법

- 요청과 응답은 구조가 동일하나 시작줄에서만 문법이 다름 
- 요청 메시지: 웹 서버에 어떤 동작을 요구
  - 형식
<pre><code>[메시지] [요청 URL] [HTTP 버전]  
[헤더]

[본문]  

ex)  
GET /specials/saw-blade.gif HTTP/1.0  
Host: www.joes-hardware.com </code></pre>

- 응답 메시지: 요청의 결과를 클라이언트에게 돌려줌 
  - 형식
<pre><code>[HTTP 버전] [상태 코드] [사유 구절]  
[헤더]

[본문]  

ex)  
HTTP/1.0 200 OK  
Content-Type: image/gif  
Content-Length: 8572 </code></pre>

**메서드**
- 클라이언트 측에서 서버가 리소스에 대해 수행해주길 바라는 동작으 의미
- `GET`, `POST`, `HEAD`와 같이 한 단어로 되어 있음

**요청 URL**
- 요청 대상이 되는 리소스를 지칭 
- 참고: [HTTP 완벽 가이드 [1-2] - URL과 리소스](https://github.yurimkoo.io/2019-08-08-http-the-definitive-guide-1-2.md)

**버전**
- HTTP 버전 
- 형식: `HTTP/<메이저>.<마이너>`

**상태 코드**
- 요청 중에 무엇이 일어났는지 설명하는 세 자리의 숫자 

**사유 구절(reason-phase)**
- 숫자로 된 상태 코드의 의미를 이해할 수 있게 도와주는 짧은 문구

**헤더들**
- 이름, 콜론(:), 선택적인 공백, 값, CRLF가 순서대로 나타나는 0개 이상의 헤더들
- HTTP/1.1과 같은 몇몇 버전의 HTTP는 요청이나 응답에 어떤 특정 헤더가 포함되어야만 유효한 것으로 간주

**엔터티 본문**
- 임의의 데이터 블록을 포함 
- 필수 요소가 아니므로 간혹 메시지는 CRLF로 끝남 

<br>

#### 2) 시작줄 

- 모든 HTTP 메시지는 시작줄로 시작
- 요청 메시지의 시작줄은 무엇을 해야 하는지, 응답 메시지의 시작줄은 무슨 일이 일어났는지 말해줌 

**요청줄**
- 서버에서 어떤 동작이 일어나야 하는지 설명해주는 메서드
- 그 동작에 대한 대상을 지칭하는 요청 URL
- 클라이언트가 어떤 HTTP 버전으로 말하고 있는지 서버에게 알려주는 HTTP 버전으로 구성되어 있음 

**응답줄**
- 응답 메시지는 수행 결과에 대한 상태 정보와 결과 데이터를 클라이언트에게 돌려줌 

**메서드**
- HTTP 명세는 공통 요청 메서드의 집합을 정의하고 있음
- 요청 메시지에 따라 본문이 있을 수도 있고 없을 수도 있음
- 확장 메서드: HTTP 명세를 확장하여 추가로 구현된 메서드 

|메서드|설명|본문 여부|
|:--:|:--:|:--:|
|GET|서버에서 어떤 문서를 가져온다| |
|HEAD|서버에서 어떤 문서의 헤더만 가져온다| |
|POST|서버가 처리해야 할 데이터를 보낸다|O|
|PUT|서버에 요청 메시지의 본문을 저장한다|O|
|TRACE|메시지가 프락시를 거쳐 서버에 도달하는 과정을 추적한다| |
|OPTIONS|서버가 어떤 메서드를 수행할 수 있는지 확인한다| |
|DELETE|서버에서 문서를 제거한다| |


**상태 코드와 사유 구절**
- 상태 코드는 각 응답 메시지의 시작줄에 담겨 반환 
- 숫자로 된 코드 + 문자열로 된 사유 구절: 서로 일대일 대응 

|전체 범위|정의된 범위|분류|
|:--:|:--:|:--:|:--:|
|100-199|100-101|정보|
|200-299|200-206|성공|
|300-399|300-305|리다이렉션|
|400-499|400-415|클라이언트 에러|
|500-599|500-505|서버 에러|

**버전 번호**
- HTTP 애플리케이션이 자신이 따르는 프로토콜의 버전을 상대방에게 말해주기 위한 수단 
- 어떤 애플리케이션이 지원하는 가장 높은 HTTP 버전을 가리킴 
  - `HTTP/1.1`의 의미는 HTTP/1.1의 메시지가 아니라, HTTP/1.1까지 이해할 수 있음을 의미 
- HTTP 버전을 비교할 때는 '.'을 기준으로 분리하여 진행해야 함 
  - `HTTP/2.22`는 `HTTP/2.3`보다 큼. '.'을 기준으로 2는 동일하지만, 22와 3 중 22가 더 크기 때문임.

<br>

#### 3) 헤더 

- 요청과 응답 메시지에 추가 정보를 더함 
- 헤더 분류
  - 일반 헤더: 요청과 응답 양쪽에 모두 나타날 수 있음
  - 요청 헤더: 요청에 대한 부가 정보를 제공
  - 응답 헤더: 응답에 대한 부가 정보를 제공
  - Entity 헤더: 본문 크기와 콘텐츠, 혹은 리소스 그 자체를 서술 
  - 확장 헤더: 명세에 정의되지 않은 새로운 헤더 

|헤더의 예|설명|
|:--:|:--:|
|Date: Tue, 3 Oct 2019 03:20:49 KST|서버가 응답을 만들어 낸 시각|
|Content-length: 4942|4942 바이트의 데이터를 포함한 엔터티 본문|
|Content-type: image/gif|엔터티 본문은 GIF 이미지|
|Accept: image/gif, image/jpeg, text/html|클라이언트는 GIF, JPEG, HTML을 받아들일 수 있다|

<br>

#### 4) 엔터티 본문

- HTTP 메시지의 세번째 부분은 선택적인 엔터티 본문 
- 이미지, 비디오, HTML 문서, 소프트웨어 애플리케이션, 신용카드 트랜잭션, 전자우편 등 여러 종류의 디지털 데이터를 수송할 수 있음 

<br>

#### 5) 버전 0.9 메시지의 제약 

- HTTP 버전 0.9는 초기 버전이며 굉장히 단순한 프로토콜로 되어 있음 
- HTTP/0.9 메시지도 요청과 응답으로 이루어져 있으나 
  - 요청은 메서드와 요청 URL을 가지고 있을 뿐이며 
  - 응답은 오직 엔터티로만 되어 있음 
- 버전 정보, 상태 코드, 사유 구절, 헤더가 포함되어 있지 않음을 유의 

<br>

(2)편에서 계속됩니다.  
[HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (2)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-2.html) 편 바로가기 

<br>
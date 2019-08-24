---
layout: post
title: "HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (2)"
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
- [HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (1)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-1.html)
- HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (2)
- [HTTP 완벽 가이드 [1-4] - 커넥션 관리](https://yurimkoo.github.io/http/2019/08/23/http-the-definitive-guide-1-4.html)  

<br>

------
# 1부. HTTP: 웹의 기초 
## 3장. HTTP 메시지  

(1)편과 이어집니다.  
[HTTP 완벽 가이드 [1-3] - HTTP 메시지 - (1)](https://yurimkoo.github.io/http/2019/08/08/http-the-definitive-guide-1-3-1.html) 편 바로가기  

<br>

### 3.3 메서드

- 모든 서버가 모든 메서드를 구현하지는 않음
- 그렇다고 하더라도 메서드는 대부분 제한적으로 사용 

#### 1) 안전한 메서드 (Safe Method)

- HTTP는 안전한 메서드라고 불리는 메서드의 집합을 정의 
- `GET`, `HEAD`는 안전하다고 정의할 수 있는데, HTTP 요청의 결과로 서버에 어떤 작용도 없기 때문 
- 안전한 메서드의 목적은, 서버에 영향을 줄 수 있는 안전하지 않은 메서드가 사용될 때 사용자에게 알려줄 수 있는 HTTP 애플리케이션을 만들도록 하는 것에 있음 

#### 2) GET

- 가장 흔하게 쓰이는 메서드
- 주로 서버에게 리소스를 달라고 요청하기 위해 쓰임 

#### 3) HEAD

- 정확히 `GET`처럼 행동하지만, 서버는 응답으로 헤더만을 돌려줌. 엔터티 본문은 반환되지 않음 
  - 리소스를 가져오지 않고도 타입 등을 알아낼 수 있음 
  - 응답의 상태 코드를 통해 개체가 존재하는지 확인 가능
  - 헤더를 확인하여 리소스가 변경됐는지 검사할 수 있음 
- HTTP/1.1 준수를 위해서는 `HEAD` 메서드가 반드시 구현되어 있어야 함

#### 4) PUT

- 서버가 요청의 본문을 가지고 요청 URL의 이름대로 새 문서를 만들거나, 이미 URL이 존재한다면 본문을 사용해서 교체
- 콘텐츠를 변경할 수 있게 해줌; 사용자에게 로그인 요구 

#### 5) POST

- 서버에 입력 데이터를 전송하기 위해 설계
- HTML 폼을 지원하기 위해 흔히 사용되며, 채워진 폼에 담긴 데이터는 서버로 전송되고 서버는 이를 모아 필요로 하는 곳에 보냄 

#### 6) TRACE

- 클라이언트에게 자신의 요청이 서버에 도달했을 때 어떻게 보이게 되는지 알려줌
- 목적지 서버에서 '루프백(loopback)' 진단을 함; 서버는 요청 메시지를 본문에 넣어 `TRACE` 응답을 되돌려줌 
- 주로 진단할 때나 프락시/타 애플리케이션 요청에 어떤 영향을 미치는지 확인하고자 할 때 사용 
- 서버의 엔터티 본문을 리턴받지 않고 서버가 받은 요청이 그대로 엔터티 본문에 들어 있음 

#### 7) OPTIONS

- 서버에게 여러 가지 종류의 지원 범위에 대해 물어봄; 특정 리소스에 어떤 메서드가 지원되는지 

#### 8) DELETE

- 서버에게 요청 URL로 지정한 리소스를 삭제할 것을 요청
- 단, 삭제 수행 보장은 못함; 서버가 클라이언트에게 알리지 않고 요청 무시하는 것을 허용하기 때문 

#### 9) 확장 메서드

- HTTP/1.1 명세에 정의되지 않은 메서드로, 필요에 따라 확장해도 문제가 없도록 설계되어 있기 때문에 과거 구현한 소프트웨어의 오동작을 유발하지는 않음 
- 'Be conservative in what you send, be liberal in what you accept.' (엄격하게 보내고 관대하게 받아들여라)
  - 프락시는 종단 간(end-to-end) 행위를 망가뜨리지 않을 수 있다면 알려지지 않은 메서드가 담긴 메시지를 다운스트림 서버로 전달하려고 시도, 그렇지 않다면 `501 Not Implemented` 상태 코드로 응답해야 함 

<br>

### 3.4 상태 코드 

- 상태 코드는 클라이언트에게 그들의 트랜잭션을 이해할 수 있는 쉬운 방법을 제공 

#### 1) 100-199: 정보성 상태 코드

- HTTP/1.1에서 도입 

|상태 코드|사유 구절|의미|
|:--:|--|--|
|100|Continue|요청의 시작 부분 일부가 받아들여졌으며, 클라이언트는 나머지를 계속 이어서 보내야 함. 이것을 보낸 후, 서버는 반드시 요청을 받아 응답해야 함.|
|101|Switching Protocols|클라이언트가 Upgrade 헤더에 나열한 것 중 하나로 서버가 프로토콜을 바꾸었음.|

#### 2) 200-299: 성공 상태 코드

|상태 코드|사유 구절|의미|
|:--:|--|--|
|200|OK|요청 정상. 엔터티 본문은 요청된 리소스를 포함하고 있음.|
|201|Created|서버 개체를 생성하라는 요청(ex. `PUT`)을 위한 것. 서버는 상태 코드를 보내기 전 반드시 객체를 생성해야 함.|
|202|Accepted|요청은 받아들여졌으나 서버는 아직 어떤 동작도 수행하지 않음. 요청 처리 완료에 대한 보장도 없음.|
|204|No Content|응답 메시지는 헤더와 상태줄은 포함하지만, 엔터티 본문은 포함하지 않음.|
|206|Partial Content|부분 혹은 범위 요청이 성공했음.|

#### 3) 300-399: 리다이렉션 상태 코드

- 클라이언트가 관심있어 하는 리소스에 대해 다른 위치를 사용하라고 말해주거나 그 리소스의 내용 대신 다른 대안 응답을 제공 

|상태 코드|사유 구절|의미|
|:--:|--|--|
|300|Multiple Choices|클라이언트가 동시에 여러 리소스를 가리키는 URL을 요청할 경우, 그 리소스의 목록과 함께 반환함. 사용자는 목록에서 원하는 하나를 선택할 수 있음.|
|301|Moved Permanently|요청한 URL이 옮겨졌을 때 사용함. 응답에 Location 헤더에 현재 리소스의 URL을 포함해야 함.|
|302|Found|`301` 상태 코드와 같음. 그러나 Location 헤더로 주어진 URL을 리소스를 임시로 가리키기 위한 목적으로 사용해야 함.|
|303|See Other|클라이언트에게 리소스를 다른 URL에서 가져와야 한다고 말할 때 사용함. 새 URL은 응답 메시지의 Location 헤더에 들어있음.|
|304|Not Modified|리소스가 수정되지 않았음을 의미함. 엔터티 본문을 가져서는 안 됨.|
|305|Use Proxy|리소스가 반드시 프락시를 통해서 접근되어야 함을 나타냄.|

#### 4) 400-499: 클라이언트 에러 상태 코드

|상태 코드|사유 구절|의미|
|:--:|--|--|
|400|Bad Request|클라이언트가 잘못된 요청을 보냈다고 말해줌.|
|401|Unauthorized|리소스를 얻기 전에 클라이언트에게 스스로를 인증하라고 요구하는 내용의 응답을 반환함.|
|403|Forbidden|요청이 서버에 의해 거부되었음.|
|404|Not Found|요청한 URL을 찾을 수 없음.|
|405|Method Not Allowed|지원하지 않는 메서드로 요청됨.|
|408|Request Timeout|클라이언트 요청을 완수하는 시간이 오래 걸리는 경우, 이 상태 코드로 응답하고 연결을 끊을 수 있음.|
|409|Conflict|요청이 리소스에 대해 일으킬 수 있는 몇몇 충돌을 지칭하기 위해 사용함.|
|410|Gone|`404`와 비슷하나, 서버가 한 때 그 리소스를 갖고 있었다는 점이 다름.|

#### 5) 500-599: 서버 에러 상태 코드

|상태 코드|사유 구절|의미|
|:--:|--|--|
|500|Internal Server Error|서버가 요청을 처리할 수 없는 에러를 만났을 때 사용함.|
|501|Not Implemented|클라이언트가 서버의 능력을 넘은 요청을 했을 때 사용함.|
|502|Bad Gateway|프락시나 게이트웨이처럼 행동하는 서버가 그 요청 응답 연쇄에 있는 다음 링크로부터 가짜 응답에 맞닥뜨렸을 때 사용함.|
|503|Service Unavailable|현재는 서버가 요청을 처리해줄 수 없지만 나중에는 가능함.|
|504|Gateway Timeout|`408`과 비슷하지만, 다른 서버에 요청을 보내고 응답을 기다리다 타임아웃이 발생한 게이트웨이나 프락시에서 온 응답이라는 점이 다름.|
|505|HTTP Version Not Supported|서버가 지원할 수 없거나 지원하지 않으려고 하는 버전의 프로토콜로 된 요청을 받았을 때 사용함.|

<br>

### 3.5 헤더

#### 1) 일반 헤더 (General Headers)

- 메시지에 대한 아주 기본적인 정보를 제공 
- `Connection`, `Date`, `MIME-Version`, `Trailer chunked transfer`, `Transfer-Encoding`, `Upgrade`, `Via` 등을 포함 

#### 2) 요청 헤더 (Request Headers)

- 요청 메시지에서만 의미를 갖는 헤더 
- `Client-IP`, `From`, `Host`, `Referer`, `User-Agent` 등을 포함
- 서버에게 자신의 선호와 능력을 알려주는 Accept 관련 헤더; `Accept`, `Accept-Charset`, `Accept-Encoding`, `Accept-Language` 등을 포함
- 트랜잭션을 보다 안전하게 만들기 위한 요청 보안 헤더; `Authorization`, `Cookie` 등을 포함 
- 프락시 기능을 도울 수 있는 프락시 요청 헤더; `Max-Forwards`, `Proxy-Authorization`, `Proxy-Connection` 등을 포함 

#### 3) 응답 헤더 (Response Headers)

- 클라이언트가 응답을 더 잘 다루고 추후 더 나은 요청을 할 수 있게 도와줌 
- `Age`, `Public`, `Server`, `Warning` 등을 포함 
- 요청 보안 헤더와 비슷한 응답 보안 헤더; `Set-Cookie` 등을 포함 

#### 4) 엔터티 헤더 (Entity Headers)

- 엔터티와 그것의 내용물에 대한 - 개체의 타입, 메서드 등 - 광범위한 정보 제공 
- `Allow`, `Location` 등을 포함 
- 엔터티의 콘텐츠에 대한 구체적인 정보를 제공하는 콘텐츠 헤더; `Content-Base`, `Content-Encoding`, `Content-Lauguage`, `Content-Length`, `Content-Type` 등을 포함 




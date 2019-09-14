---
layout: post
title: "R로 MySQL 사용하기 - RMySQL 패키지"
author: Yurim Koo
categories:
  - R
tags:
  - MySQL
  - RMySQL
  - MySQL in R
  - R MySQL
  - R SQL
  - SQL in R
  - dbGetQuery()
  - dbSendQuery()
comments: true
---

### R로 DB 데이터 조작하기

데이터 분석으로 많이 사용하는 R!  

분석한 결과를 SQL 파일로 뽑아내지 않고 DB에 바로 넣거나,  
DB 속 데이터를 수정하거나,  
필요 없는 데이터를 삭제하면 참 편리하겠죠?  

R에서 MySQL를 사용해 DB내 데이터를 조작하는 방법에 대해 알아보겠습니다.  

([Python으로 MySQL 사용하기 - PyMySQL 라이브러리 with Pandas](https://yurimkoo.github.io/python/2019/09/14/connect-db-with-python.html) 글 보러가기)

<br>

### 간단히 DML(Data Manipulation Language)에 대해 알아보자 

SQL은 세 가지 큰 범주의 언어로 나뉩니다.  
- DDL(Data Definition Language); 데이터 정의어
- DML(Data Manipulation Language); 데이터 조작어
- DCL(Data Control Language); 데이터 제어어 

이 글에서 주로 사용할 **DML**은 아래와 같은 명령문을 가지고 있습니다.  

|명령어|설명|
|:--:|:--|
|SELECT|하나 이상의 테이블에서 데이터를 '조회'하기 위해 사용|
|INSERT|테이블에 한 개 이상의 행을 추가|  
|UPDATE|테이블에서 한 개 이상의 행을 변경|
|DELETE|테이블에서 한 개 이상의 행을 삭제|  

`SELECT`는 단순히 데이터를 조회하고 추출할 때만 사용하고,  
`INSERT`, `UPDATE`, `DELETE`는 데이터를 직접 변경하기 위해 사용합니다.  

<br>

### R에서 MySQL를 사용할 수 있는 환경 구축하기

이제 R에서 MySQL을 사용할 수 있도록 설정해보겠습니다.  

#### RMySQL 패키지 설치 

다양한 커넥션 패키지가 존재하지만, 이 글에서는 `RMySQL` 패키지를 이용해 DB와 연결하도록 하겠습니다.  

<pre><code>install.packages("RMySQL")
library(RMySQL)</code></pre>

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-1.png" | relative_url }}' alt='absolute'>
</figure>

설치 후 패키지를 로딩까지 완료했습니다.  
엄청 간단하죠 🤭  

#### DB 연결 - dbConnect() 함수 

이제 DB와 연결시켜 보겠습니다.  
지난 번 만들었던 도로명주소 DB를 예시로 설명하도록 하겠습니다.  
([도로명주소 개발자센터의 오픈 데이터를 이용해 MySQL 주소DB 구축하기 (DB 툴 추천 - Sequel Pro)](https://yurimkoo.github.io/db/2019/08/24/mysql-juso-db.html) 글 바로가기)

<pre><code>jusoDB <- dbConnect(
  MySQL(),
  user = 'root',
  password = '{설정한 비밀번호}',
  host = '127.0.0.1',
  dbname = 'juso-db'
)
dbSendQuery(jusoDB, 'set character set "utf8"')
</code></pre>

`dbConnect()` 함수를 이용하면 MySQL host내 DB와 직접 연결할 수 있습니다.  
- `MySQL()`: SQL 종류 중 MySQL을 사용할 것이라고 선언
- `user`: user name
- `password`: 설정한 패스워드
- `host`: DB가 존재하는 host 
- `dbname`: 연결할 데이터베이스 이름 

모두 따옴표로 묶어주어 텍스트로 인자를 넘겨야 합니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-2.png" | relative_url }}' alt='absolute'>
</figure>

연결이 완료되면 변수 목록에 연결이 생성됩니다.  

그리고 `dbSendQuery(jusoDB, 'set character set "utf8"')` 문을 통해 DB의 캐릭터 셋을 UTF-8로 지정해주어야 합니다. 
이 설정이 없으면 R로 데이터를 불러올 때 문자를 제대로 가져오지 못할 수도 있습니다.  

<br>

### 데이터 조작하기 

이제 DB 연결이 끝났으니 실제로 DB내 데이터를 조작해보겠습니다.  

#### 데이터 조회 - SELECT - dbGetQuery()

<pre><code>busan_jibun <- dbGetQuery(
  jusoDB,
  "SELECT * FROM `busan-jibun`;"
)
</code></pre>

SELECT 명령을 위해 `dbGetQuery()` 함수를 사용합니다.  
`conn` 파라미터에 연결할 DB의 변수명을 선언하고 조회할 데이터의 SELECT 문을 써줍니다.  
테이블명에 `-`가 들어있기 때문에 에러를 방지하고자 <code>`</code>로 묶어주었습니다.  

또, 조회한 데이터를 확인하기 위해 변수에 할당해주었습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-3.png" | relative_url }}' alt='absolute'>
</figure>

짜잔! 넣어놓은 10만 개의 데이터가 잘 불려진 것을 확인할 수 있습니다.  

#### 데이터 삽입/변경/삭제 - INSERT/UPDATE/DELETE - dbSendQuery()

데이터의 삽입/변경/삭제는 모두 `dbSendQuery()` 함수를 이용합니다.  
`conn` 파라미터에 연결할 DB의 변수명을 선언하고 그 다음 삽입/변경/삭제에 대한 SQL 문을 써줍니다.  

데이터 삽입/변경/삭제를 명령할 때에는 `dbGetQuery()` 사용과 다르게 변수에 할당하지 않습니다.  
명령을 DB에 전송한 후 결과를 데이터로 받아오는 것이 아니기 때문입니다.  

**INSERT**

<pre><code>dbSendQuery(
  jusoDB,
  "INSERT INTO `busan-jibun` (관리번호, 일련번호, 시도명, 시군구명) 
  VALUES ('1234567891234567891234567', '1', '서울특별시', '강남구');"
)
</code></pre>  

(실수인 척을 하고) 부산 지번 주소 테이블에 서울특별시의 주소를 넣어보도록 하겠습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-4.png" | relative_url }}' alt='absolute'>
</figure>

`<MySQLResult:109887544,0,3>`와 같은 명령 전달에 대한 결과가 리턴되었습니다.  
실제 DB에서 확인하면 서울특별시 데이터가 들어간 것을 볼 수 있습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-5.png" | relative_url }}' alt='absolute'>
</figure>

(조회한 데이터를 변수에 할당하지 않으면 결과만 리턴됩니다.)  


**UPDATE**

<pre><code>dbSendQuery(
  jusoDB,
  "UPDATE `busan-jibun`
  SET 시도명 = '부산광역시', 시군구명 = '해운대구'
  WHERE 시도명 != '부산광역시';"
)
</code></pre>

이제 방금 넣은 '서울특별시 강남구' 데이터를 '부산광역시 해운대구'로 변경해보도록 하겠습니다.  

이 예시에서는 서울특별시에 대한 데이터가 한 개이기 때문에 `WHERE` 절에 저렇게 적어주었지만,  
보통 작업을 할 때는 Primary Key 등을 통해 좀 더 명시적으로 변경할 데이터를 지정해주는 것이 안전합니다.  
##### (저 문의 원래 해석은 '시도명이 부산광역시가 아닌 데이터를 일괄적으로 시도명, 시군구명을 부산광역시 해운대구로 바꿔라' 입니다. 실무에서는 매우 위험하겠죠!)  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-6.png" | relative_url }}' alt='absolute'>
</figure>  

아까 넣은 데이터가 '부산광역시 해운대구'로 변경된 것을 확인했습니다.  


**DELETE**

<pre><code>dbSendQuery(
  jusoDB,
  "DELETE FROM `busan-jibun`
  WHERE id = 100001;"
)</code></pre>

이제 실수로 넣어서 완결성이 없는 데이터를 삭제해보도록 하겠습니다.  
위에서 데이터의 Primary Key인 `id`가 100001인 것을 확인할 수 있으므로 `WHERE` 절에 명시적으로 삭제할 행을 지정해줍니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-r/1-7.png" | relative_url }}' alt='absolute'>
</figure>  

쨘. 실수로 넣은 데이터는 테이블에서 제거되었습니다!  

-----

이렇게 간단하게 MySQL과 R을 연동하여 DB의 데이터를 조작하는 방법에 대해 알아봤습니다.  
이 기본 사용법을 좀 더 응용하면 분석 결과나 전처리 결과를 바로 DB에 반영하는 것도 쉽겠지요?  
DDL, DCL, 트랜잭션 등과 관련한 더 많은 함수는 [RMySQL Documentation (바로가기)](https://cran.r-project.org/web/packages/RMySQL/RMySQL.pdf)를 참조하세요!  


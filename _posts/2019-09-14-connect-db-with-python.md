---
layout: post
title: "Python으로 MySQL 사용하기 - PyMySQL 라이브러리 with Pandas"
author: Yurim Koo
categories:
  - Python
tags:
  - MySQL
  - PyMySQL
  - MySQL in Python
  - Python MySQL
  - Python SQL
  - SQL in Python
  - pymysql execute
  - pymysql executemany
  - pymysql commit
  - pymysql cursor
  - pymysql db connect
  - pymysql placeholder
comments: true
---

### Python으로 DB 데이터 조작하기

데이터 분석에 많이 사용하는 Python!  

분석한 결과를 SQL 파일로 뽑아내지 않고 DB에 바로 넣거나,  
DB 속 데이터를 수정하거나,  
필요 없는 데이터를 삭제하면 참 편리하겠죠?  

Python에서 Pandas와 MySQL를 사용해 DB내 데이터를 조작하는 방법에 대해 알아보겠습니다.  

([R로 MySQL 사용하기 - RMySQL 패키지](https://yurimkoo.github.io/r/2019/09/14/connect-db-with-r.html) 글 보러가기)

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

### Python에서 MySQL를 사용할 수 있는 환경 구축하기

이제 Python에서 MySQL을 사용할 수 있도록 설정해보겠습니다.  

#### PyMySQL 패키지 설치 

다양한 커넥션 패키지가 존재하지만, 이 글에서는 `PyMySQL` 패키지를 이용해 DB와 연결하도록 하겠습니다.  

<pre><code>pip install PyMySQL</code></pre>

<pre><code>import pymysql</code></pre>

터미널로 라이브러리를 설치하고 Python에서 라이브러리 로딩까지 완료했습니다.  
엄청 간단하죠 🤭  

#### DB 연결 - connect()  

이제 DB와 연결시켜 보겠습니다.  
지난 번 만들었던 도로명주소 DB를 예시로 설명하도록 하겠습니다.  
([도로명주소 개발자센터의 오픈 데이터를 이용해 MySQL 주소DB 구축하기 (DB 툴 추천 - Sequel Pro)](https://yurimkoo.github.io/db/2019/08/24/mysql-juso-db.html) 글 바로가기)

<pre><code>juso_db = pymysql.connect(
    user='root', 
    passwd='{설정한 비밀번호}', 
    host='127.0.0.1', 
    db='juso-db', 
    charset='utf8'
)
</code></pre>

`connect()` 함수를 이용하면 MySQL host내 DB와 직접 연결할 수 있습니다.  
- `user`: user name
- `passwd`: 설정한 패스워드
- `host`: DB가 존재하는 host 
- `db`: 연결할 데이터베이스 이름 
- `charset`: 인코딩 설정

모두 따옴표로 묶어주어 텍스트로 인자를 넘겨야 합니다.  

#### cursor 설정 - cursor()

<pre><code>cursor = juso_db.cursor(pymysql.cursors.DictCursor)</code></pre>

연결한 DB와 상호작용하기 위해 `cursor` 객체를 생성해주어야 합니다.  

다양한 커서의 종류가 있지만,  
- Python에서 데이터 분석을 주로 `pandas`로 하고
- RDBMS(Relational Database System)를 주로 사용하기 때문에  

데이터 분석가에게 익숙한 데이터프레임 형태로 결과를 쉽게 변환할 수 있도록  
딕셔너리 형태로 결과를 반환해주는 `DictCursor`를 사용하겠습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-python/1-1.png" | relative_url }}' alt='absolute'>
  <center><figcaption>cursor 결과 비교</figcaption></center>
</figure>

`DictCursor`가 아닌 일반 `cursor`를 사용하면 결과가 일반적으로는 튜플 형태로 리턴됩니다.  

<br>

### 데이터 조작하기 

이제 DB 연결이 끝났으니 실제로 DB내 데이터를 조작해보겠습니다.  
PyMySQL의 모든 데이터 조작은 `execute()`와 `fetch()` 계열을 사용합니다.  

#### 데이터 조회 - SELECT 

<pre><code>sql = "SELECT * FROM `busan-jibun`;"
cursor.execute(sql)
result = cursor.fetchall()
</code></pre>

SELECT 명령을 위해 SQL 문을 따로 변수에 넣어주고 `cursor.execute(sql)` 을 사용해 SQL를 실행합니다.  
실행한 결과를 `fetchall()`을 이용해 받아옵니다.  

|메서드|설명|
|:--:|:--|
|fetchall()|모든 데이터를 한 번에 가져올 때 사용|  
|fetchone()|한 번 호출에 하나의 행만 가져올 때 사용|
|fetchmany(n)|n개만큼의 데이터를 가져올 때 사용|

#### 결과를 pandas 데이터프레임으로 변환

<pre><code>import pandas as pd

result = pd.DataFrame(result)
result</code></pre>

`pandas`를 import 하고 아까 불러왔던 결과를 데이터프레임 형태로 만들면  
더 익숙한 형태로 데이터를 조작할 수 있습니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-python/1-2.png" | relative_url }}' alt='absolute'>
</figure>

짜잔! 넣어놓은 10만 개의 데이터가 잘 불려진 것을 확인할 수 있습니다.  

#### 데이터 삽입/변경/삭제 - INSERT/UPDATE/DELETE 

데이터 삽입/변경/삭제를 명령할 때에는 SELECT문 사용과 다르게 변수에 할당하지 않습니다.  
명령을 DB에 전송한 후 결과를 데이터로 받아오는 것이 아니기 때문입니다.  

**INSERT**

<pre><code>sql = '''INSERT INTO `busan-jibun` (관리번호, 일련번호, 시도명, 시군구명) 
    VALUES ('1234567891234567891234567', '1', '서울특별시', '강남구');'''

cursor.execute(sql)
juso_db.commit()
</code></pre>  

(실수인 척을 하고) 부산 지번 주소 테이블에 서울특별시의 주소를 넣어보도록 하겠습니다.  
`execute()`를 이용해 INSERT 쿼리를 실행한 후, `commit()`을 날려줍니다.  
커밋을 날리지 않으면 `execute()`를 아무리 해도 결과가 DB에 반영되지 않습니다.  

처음에 DB 커넥션을 만들 때 `autocommit=True` 파라미터를 넣어줄 수도 있지만, 개인적으로 추천하지는 않습니다.  
보다 안전하게 사용하기 위해 변경할 쿼리문을 한 번 더 확인하고 commit 문을 직접 써주는 것을 추천합니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-python/1-3.png" | relative_url }}' alt='absolute'>
</figure>

실제 확인하면 서울특별시 데이터가 들어간 것을 볼 수 있습니다.  
참고로, `fetchone()` 함수를 사용한 후 pandas를 사용해 결과를 데이터프레임으로 변경하기 위해서는  
<pre><code>result = pd.DataFrame(result, index=[0])</code></pre>
로 인덱스를 명시해주어야 에러가 나지 않고 올바르게 리턴됩니다.  


**UPDATE**

<pre><code>sql = '''UPDATE `busan-jibun`
  SET 시도명 = '부산광역시', 시군구명 = '해운대구'
  WHERE 시도명 != '부산광역시';'''
cursor.execute(sql)
juso_db.commit()
</code></pre>

이제 방금 넣은 '서울특별시 강남구' 데이터를 '부산광역시 해운대구'로 변경해보도록 하겠습니다.  

이 예시에서는 서울특별시에 대한 데이터가 한 개이기 때문에 `WHERE` 절에 저렇게 적어주었지만,  
보통 작업을 할 때는 Primary Key 등을 통해 좀 더 명시적으로 변경할 데이터를 지정해주는 것이 안전합니다.  
##### (저 문의 원래 해석은 '시도명이 부산광역시가 아닌 데이터를 일괄적으로 시도명, 시군구명을 부산광역시 해운대구로 바꿔라' 입니다. 실무에서는 매우 위험하겠죠!)  
<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-python/1-4.png" | relative_url }}' alt='absolute'>
</figure>  

아까 넣은 데이터가 '부산광역시 해운대구'로 변경된 것을 확인했습니다.  


**DELETE**

<pre><code>sql = '''DELETE FROM `busan-jibun`
  WHERE `법정동코드` IS NULL;'''
cursor.execute(sql)
juso_db.commit()</code></pre>

이제 실수로 넣어서 완결성이 없는 데이터를 삭제해보도록 하겠습니다.  
위에서 언급한 것과 같이 실무에서 데이터 삭제를 해야할 때는 명시적으로 Primary Key를 이용하는 것이 안전합니다!  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/connect-db-with-python/1-5.png" | relative_url }}' alt='absolute'>
</figure>  

불러올 데이터가 없는 것을 확인할 수 있지요.  
실수로 넣은 데이터는 테이블에서 제거되었습니다!  

<br>

### Tip. execute()/executemany()에 Placeholder 사용하기

만약 DB내 데이터에 대해 대량 삽입/변경/삭제가 필요한데, 조건이 다 다르다면?  
**Placeholder**를 이용할 수 있습니다!  

Placeholder란, 위와 같이 동적 SQL문을 구성할 때 유용하게 사용할 수 있는 기능인데요.  
동적 값이 들어갈 위치에 `%s`를 이용해 SQL문을 만들어 놓고,  
execute() 계열 메서드 첫번째 파라미터에는 SQL을, 두번째 파라미터에 실제 데이터를 리스트나 튜플 형태로 넣어주면 됩니다.  

<code>excute(SQL, a-data)</code>  
<code>executemany(SQL, multiple-data)</code>

하나의 데이터만 적용시킬 거라면 execute()를,  
여러 개의 데이터를 한 번에 대량으로 적용시킬 거라면 executemany()를 사용하면 됩니다.  

Placeholder의 특징은, 
- 두번째 파라미터에 들어간 데이터 순서대로 SQL이 적용되고, 
- 특히 문자의 경우 따옴표 등의 특수문자들이 자동으로 이스케이프(Escape)되어 처리됩니다. (완전 간편!)  
- 문자열, 숫자 등에 관계 없이 대치할 값은 모두 `%s`로 쓰입니다. (일반 문자열에서 사용하는 `%s`, `%d`와는 다름)
- `%s`는 컬럼 값을 대치할 때만 사용할 수 있습니다.  

예시를 들어보겠습니다.  

#### 1) `execute()`

<pre><code>data = ('ramen', 1)

# SELECT 
sql = "SELECT * FROM `food` WHERE name = %s AND id = %s;"
cursor.execute(sql, data)

# DELETE
sql = "DELETE FROM `food` WHERE `name` = %s AND `id` = %s;"
cursor.execute(sql, data)
db.commit()
</code></pre>

문자열 값이여도 %s를 따옴표로 감싸지 않아도 됩니다.  
`%s` 위치대로 `data`가 구성되어야 제대로 동작하니 유의하세요!  

#### 2) `executemany()`

<pre><code>data = [['ramen', 1], ['fried rice', 2], ['chicken', 3]]

# INSERT 
sql = "INSERT INTO `food`(name, id) VALUES (%s, %s);"
cursor.executemany(sql, data)
db.commit()

# UPDATE 
sql = "UPDATE `food` SET `name` = %s WHERE `id` = %s;"
cursor.executemany(sql, data)
db.commit()
</code></pre>

이차원 배열 혹은 이차원 튜플을 사용하여 데이터를 구성합니다.  
데이터 순서대로 DB에 적용됩니다.  
반복문 + execute() 메서드를 사용하는 것보다 **훨-씬** 속도와 메모리 면에서 효율적입니다.  
(몇 십 만에서 몇 백 만 데이터 UPDATE시 반복문 + execute()는 몇 시간, executemany()는 몇 분 컷!)  

-----

이렇게 간단하게 MySQL과 Python을 연동하여 DB의 데이터를 조작하는 방법에 대해 알아봤습니다.  
이 기본 사용법을 좀 더 응용하면 분석 결과나 전처리 결과를 바로 DB에 반영하는 것도 쉽겠지요?  
더 많은 함수는 [PyMySQL Documentation (바로가기)](https://pymysql.readthedocs.io/en/latest/index.html)를 참조하세요!  
한국어 설명은 [예제로 배우는 파이썬 프로그래밍 - Python DB API](http://pythonstudy.xyz/python/article/201-Python-DB-API) 이 사이트를 참조하세요!  

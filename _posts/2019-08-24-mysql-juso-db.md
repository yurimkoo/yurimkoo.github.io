---
layout: post
title: "도로명주소 개발자센터의 오픈 데이터를 이용해 MySQL 주소DB 구축하기 (DB 툴 추천 - Sequel Pro)"
author: Yurim Koo
categories:
  - Python
tags:
  - MySQL
  - MySQL install 
  - MySQL 설치
  - MySQL Server 
  - MySQL Config
  - MySQL 환경설정
  - 도로명주소 개발자센터
  - 오픈DB
  - 오픈데이터
  - Open DB
  - Open Data
  - Free DB Tool
  - Sequel Pro
  - 무료 DB 툴 
  - DB GUI Tools
comments: true
---

### 도로명주소 개발자센터 

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-1.png" | relative_url }}' alt='absolute'>
</figure>

[도로명주소 개발자센터](http://www.juso.go.kr/addrlink/main.do?cPath=99MM)는 행정안전부 주소정책과서 운영하는 개발자 도구 사이트입니다.  
도로명주소와 관련된 다양한 API와 DB를 제공하고 있습니다. 좋은 자료들이 많아서 업무에 잘 활용하고 있는 중입니다!  
최근 데이터에 질문이 생겨 문의글을 남겼는데, 직접 전화 연락을 통해 친절하게 대응해주셨던 좋은 경험이 있습니다. :D  

이곳에서 제공하는 [오픈 데이터 셋](http://www.juso.go.kr/addrlink/addressBuildDevNew.do?menu=mainJusoDb)을 이용해 로컬에 MySQL DB를 구축해보도록 하겠습니다.  
건물DB, 주소DB, 위치정보요약DB 등 다양한 목적의 주소DB들이 존재합니다.  

<br>

### 주소DB 파일 내려받고 인코딩 설정하기 

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-2.png" | relative_url }}' alt='absolute'>
</figure>

이 글에서는 **주소DB**를 이용하겠습니다. 주소DB는 여러 건물이 하나의 주소에 해당하면 하나의 주소만을 내려주기 때문에 주소의 기본 집합이라고 보면 되겠습니다. 주소DB를 내려 받으면 여러 분류의 파일이 존재하는데, '지번_'으로 시작된 파일을 사용할 것입니다.  

주소DB의 2019년 7월 전체분을 내려 받습니다.  

내려받은 파일을 살펴보면 인코딩이 깨져 있는 것을 확인할 수 있습니다.  
터미널에서 파일이 있는 경로로 이동한 뒤 다음과 같은 명령어를 실행해 인코딩을 UTF-8로 바꿔줍니다.  

<pre><code>find . -type f -name '*.txt' -exec sh -c 'iconv -f CP949 -t UTF-8 "$0" > "$0.tmp"' '{}' \; -exec mv -f '{}.tmp' '{}' \;</code></pre>

- `find . -type f -name '*.txt'`: 현재 경로의 파일 목록에서 '*.txt' 형식의 파일만 얻는 명령  
- `sh -c '{}'`: 쉘 스크립트 실행  
  - `iconv -f CP949 -t UTF-8 '{}' > '{}'`: 현재 인코딩과 변환할 인코딩, 현재 파일명과 변환할 파일명을 지정 ($0은 매개변수)  
  - `mv -f`: .tmp로 바꿨던 임시 파일명 원래대로 복구  

이 과정을 거치면 한글이 잘 보입니다!  

<br>

### MySQL을 설정해보자

이제 내려받은 파일로 로컬에 DB를 구축해보도록 하겠습니다.  

#### MySQL 설치 및 설정 

<pre><code>brew update
brew install mysql</code></pre>

설치가 모두 끝났으면 `mysql.server start` 명령어를 이용해 MySQL 서버에 접속합니다.  
그 뒤, 간단한 설정을 위해 `mysql_secure_installation` 명렁어를 실행합니다.  

<pre><code>mysql.server start
mysql_secure_installation</code></pre>

이 명령을 실행하면 다음과 같은 설정에 대한 질문이 등장합니다.  
- **Would you like to setup VALIDATE PASSWORD component?**
  - Yes: 복잡한 비밀번호 설정
  - No: 간단한 비밀번호 설정 `V`
- **Remove anonymous users?**
  - Yes: 접속하는 경우 -u 옵션 필요 `V`
  - No: 접속하는 경우 -u 옵션 불필요 
- **Disallow root login remotely?**
  - Yes: 원격접속 불가능 `V`
  - No: 원격접속 가능
- **Remove test database and access to it?**
  - Yes: Test 데이터베이스 제거 `V`
  - No: Test 데이터베이스 유지
- **Reload privilege tables now?**
  - Yes: 적용 `V`
  - No: 미적용 

#### 접속 확인 

<pre><code>mysql -uroot -p
status</code></pre>

'All Done!'과 함께 모든 설정을 마쳤으면 처음에 설정한 비밀번호와 함께 로그인 합니다.  
그 후 `status` 명령어를 실행하여 DB의 character 설정이 모두 UTF-8로 되어 있는지 확인합니다.  
아래와 같다면 잘 설정되어 있는 것입니다!  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-3.png" | relative_url }}' alt='absolute'>
</figure>

##### (MySQL 환경 설정에 대한 도움글: [macOS MySQL 설치 및 설정 사용법](https://whitepaek.tistory.com/16))

<br>

### DB에 데이터를 넣어보자 

이제 MySQL 설정까지 모두 끝났으니 실제로 DB를 생성해 데이터를 넣는 작업을 할 것입니다.  
DB를 만들고 데이터를 넣는 방법은 크게 3가지입니다.  
- 터미널 MySQL 서버에서 직접 실행
- Python, R 등 다른 언어와 연동해서 사용 
- DB 툴 사용 

다른 방법은 다른 포스팅에서 설명하도록 하고, 이 글에서는 가장 간편한 **DB 툴을 사용하는 방법**을 다뤄보겠습니다!

#### DB 툴 사용 - Sequel Pro 

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-4.png" | relative_url }}' alt='absolute'>
</figure>

제가 사용하는 무료 DB 툴인 Sequel Pro를 소개합니다! (~~DB 모양이 핫케익 같이 맛있게 생겼습니닷 XD~~)  
[다운로드](https://sequelpro.com/download) 페이지에서 툴을 내려받고 실행합니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-5.png" | relative_url }}' alt='absolute'>
</figure>

DB에 접속하는 세 가지 방법 중 TCP/IP를 사용해 접속하도록 하겠습니다.  
- `Name`: DB 커넥션에 대한 별명이라고 보시면 됩니다.
- `Host`: localhost이므로 127.0.0.1을 입력합니다. 
- `Username`: root 혹은 설정한 username을 입력합니다.
- `Password`: 설정한 비밀번호를 입력합니다.

그리고 Connection을 누르면 GUI로 MySQL 서버에 접속하게 됩니다!  

#### 데이터베이스와 테이블 손쉽게 생성하기

| ![Image Alt 텍스트]({{"/assets/img/juso-db/1-6.png"| relative_url}}) | ![Image Alt 텍스트]({{"/assets/img/juso-db/1-7.png"| relative_url}}) |

`Add Database` 버튼을 눌러 마음에 드는 이름을 지정하고 데이터베이스를 생성해줍니다.  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-8.png" | relative_url }}' alt='absolute'>
</figure>

이제 테이블을 생성해볼까요!  
`+` 버튼을 누른 뒤 마음에 드는 이름을 쓰면 바로 테이블이 완성됩니다.  
그 다음, Table Structure에서 필드와 필드값 등을 자유자재로 설정해주면 됩니다.  

단, 주소DB는 필드가 많고 데이터의 첫 줄에 필드명이 기입되어 있지 않아, GUI로 설정해도 좋지만 SQL DDL(Data Definition Language)를 사용해보겠습니다. 일단 간단하게 주소DB의 지번 주소 데이터만 넣을 것이므로 Primary Key와 Foreign Key 설정은 무시하겠습니다.  

<pre><code>CREATE TABLE `busan-jibun` (
	관리번호 VARCHAR(25) NOT NULL,
	일련번호 VARCHAR(3) NOT NULL,
	법정동코드 VARCHAR(10),
	시도명 VARCHAR(20),
	시군구명 VARCHAR(20),
	법정읍면동명 VARCHAR(20),
	법정리명 VARCHAR(20),
	산여부 VARCHAR(1) COMMENT '0:대지, 1:산',
	지번본번_번지 INT DEFAULT 0,
	지번부번_호 INT DEFAULT 0,
	대표여부 VARCHAR(1) COMMENT '0:일반, 1:대표(지번)',
	id INT AUTO_INCREMENT PRIMARY KEY
);
</code></pre>

##### (참고: `id`는 Sequel Pro Data Import 편의상 맨 마지막에 기재했습니다.)

<br>

#### 데이터 Import 하기

테이블이 예쁘게 만들어졌으니, 이제 다운 받은 데이터를 Import 해보겠습니다.  

- [File - Import] 메뉴에 들어가, '지번_부산광역시.txt' 파일을 선택한 후 Format을 'csv'로, 인코딩은 'utf-8', **'Field Terminated by'를 `|`로** 바꿔줍니다. 
- 다음으로 넘어가면 필드가 뒤죽박죽 Auto Detect 되어 있는 것이 보이는데, 테이블을 만들 때 필드를 파일에 위치한 순서대로 만들어줬기 때문에 **'Align field names by: `File Order`'**로 설정해주면 제자리를 잘 찾아갑니다.  
- 아까도 언급했듯, 첫 줄이 필드명이 아니기 때문에 **'First line contains field names' 체크박스는 해제**합니다.  
- **'Import Method: `INSERT`'**로 잘 설정되어 있는지 확인 후 Import를 누르면 테이블에 데이터가 샤라락 들어갑니다!  

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-10.png" | relative_url }}' alt='absolute'>
  <center><figcaption>데이터가 열심히 들어가는 중...</figcaption></center>
</figure>

<figure>
  <img data-action="zoom" src='{{ "/assets/img/juso-db/1-11.png" | relative_url }}' alt='absolute'>
  <center><figcaption>완성!</figcaption></center>
</figure>

<br>

-----

클릭 몇 번 만으로 손쉽게 나만의 DB 테이블을 만들고,  
몇 십 만에서 몇 백 만개의 데이터를 짧은 시간 안에 DB에 저장하는 방법을 알아봤습니다.  
추후 R, Python 같은 언어에서 MySQL 서버와 연동하여 데이터를 조작하는 포스팅도 할 예정입니다! :D  

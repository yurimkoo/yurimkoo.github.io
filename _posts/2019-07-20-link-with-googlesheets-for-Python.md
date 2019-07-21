---
layout: post
title: "gspread를 이용하여 Python에서 구글 시트 연동하기"
author: Yurim Koo
categories:
  - Python
tags:
  - Python
  - gspread
  - googlesheets
  - 파이썬
  - 파이썬 구글 시트
  - python googlesheets
  - python gs
  - Oauth2client
  - PyOpenSSL
comments: true
---

같은 주제 하에 각기 다른 사람이 작성한 내용을 취합해 하나의 파일로 만들어 본 경험이 다들 있으실 겁니다. 그렇다고 DB 테이블로 관리하기에는 너무 가볍고, 첨부파일로 주고 받기에는 내용의 변동이 너무 잦아서 번거로울 때 한 번쯤 이용해 봤을 구글 시트.  

[구글 스프레드 시트](https://www.google.com/intl/ko_kr/sheets/about/)는 구글에서 제공하는 무료 웹 기반 소프트웨어 중 하나입니다. 구글 시트 외에도 구글 문서, 구글 프레젠테이션 등을 제공하고 있습니다.  

csv 파일을 내려 받을 필요 없이 구글 시트 내의 내용을 Python에서 바로 불러오고, 사용하는 방법을 알아봅시다.  

구글 시트를 연동하는 데에도 다양한 방법이 있겠지만, 이 포스팅에서는 '[gspread](https://github.com/burnash/gspread)' 패키지를 이용하는 방법을 소개합니다.  

참고로, R을 이용한 구글 시트 연동은 복잡한 인증 절차 및 권한 설정을 안 해줘도 된다는 장점이 있습니다.  
(R 연동과 비교하기: [**googlesheets를 이용하여 R에서 구글 시트 연동하기** 글 보러 가기](http://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-R.html)) 


<br>

------

<br>
### 1. 사용 환경 셋업하기 
#### 1) 구글 사용자 인증 설정  

Python에서 구글 시트를 연동하기 위해서는 따로 구글 사용자 인증 설정이 필요합니다.  

**(1) (프로젝트가 없다면) [GCP 사용자 인증정보](https://console.cloud.google.com/projectselector2/apis/credentials?hl=ko&_ga=2.167447614.-1289008455.1563620716&pli=1&supportedpurview=project) 사이트에 접속하여 프로젝트를 생성**

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-1.png" | relative_url }}' alt='absolute'>
  <center><figcaption>프로젝트 생성</figcaption></center>
</figure>

<br>
**(2) 생성한 프로젝트 하에서 'API 사용자 인증 정보 만들기 > 서비스 계정 키' 생성**  

- 새 서비스 계정 만들기를 선택하여 이름과 역할을 지정해주고 JSON 유형의 키를 생성하면 JSON 파일이 내려받아집니다.
- 추후 Python 코드 내에서 JSON 파일을 불러와 보안 설정을 하게 됩니다.

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-2.png" | relative_url }}' alt='absolute'>
  <center><figcaption>서비스 계정 키 생성</figcaption></center>
</figure>

<br>
**(3) API 사용 설정**
- 사용자 인증 정보 생성을 완료했다면 왼쪽 메뉴의 '대시보드'로 들어가 '+ API 및 서비스 사용 설정' 메뉴로 들어갑니다.
- 첫 화면의 [G Suite - Google Drive API, Google Sheets API] 메뉴로 들어가서 '사용 설정'을 눌러 API 사용을 활성화 시켜줍니다.

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-4.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

- 만약, 이 단계를 건너뛴다면 실제 시트를 연동할 때 아래와 같은 에러 메시지가 나타납니다.
- 이 메시지를 마주치면 아래 "extendedHelp" 부분의 링크로 들어가 API를 활성화 시켜주면 됩니다.
<pre><code>APIError: {
 "error": {
  "errors": [
   {
    "domain": "usageLimits",
    "reason": "accessNotConfigured",
    "message": "Access Not Configured. Drive API has not been used in project {your project} before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/drive.googleapis.com/overview?project={your project} then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.",
    "extendedHelp": "https://console.developers.google.com/apis/api/drive.googleapis.com/overview?project={your project}"
   }
  ],
  "code": 403,
  "message": "Access Not Configured. Drive API has not been used in project {your project} before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/drive.googleapis.com/overview?project={your project} then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry."
 }
}
</code></pre>

<br>
#### 2) 패키지 설치

- 구글 사용자 인증 설정이 모두 완료됐다면, 실제 연동하기 위한 환경을 구성해봅시다.
- 터미널을 이용해 ```oauth2client```, ```gspread```, ```PyOpenSSL``` 패키지를 설치합니다.  
- [oauth2client](https://oauth2client.readthedocs.io/en/latest/) 가이드에 들어가면 더 이상 업데이트가 없을 것이니
```google-auth```를 사용하라고 안내가 나오는데, ```gspread```가 아직 ```google-auth``` 지원을 안 해서 ```oauth2client```를 사용하도록 합니다.
(따로 Class를 설정하여 ```google-auth```를 사용하는 방법을 시도하려면 [여기](https://stackoverflow.com/questions/51618127/credentials-object-has-no-attribute-access-token-when-using-google-auth-wi)에서 도움을 받으세요.)

<pre><code>pip install --upgrade oauth2client
pip install gspread
pip install PyOpenSSL
</code></pre>

<br>
#### 3) 연동하고자 하는 구글 시트에 'client_email' 접근 권한 부여  
- 앞서 내려 받은 JSON 파일을 열어보면 아래와 같습니다.

<pre><code>{
  "type": "service_account",
  "project_id": "python-linkage-sample",
  "private_key_id": "...",
  "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n",
  "client_email": "...@python-linkage-sample.iam.gserviceaccount.com",
  "client_id": "...",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/...%40python-linkage-sample.iam.gserviceaccount.com"
}
</code></pre>

- 'client_email' 부분을 복사하여 연동하고 싶은 시트에서 접근 권한을 부여합니다. 
- 이 부분이 사실 제일 ~~귀찮은~~ 부분입니다. 연동하고 싶은 시트마다 접근해서 권한을 부여해야 하니까요. -,-
- 권한 부여는 일반적으로 시트를 공유하는 방법과 같습니다. 메일 대신 'client_email'을 써주면 됩니다.

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-3.png" | relative_url }}' alt='absolute'>
  <center><figcaption>시트에서 접근 권한 부여</figcaption></center>
</figure>

<br>
이로써 환경 셋업은 완료입니다!  
<br>

### 2. 실제 사용해보기 
#### 1) 코드내 환경 셋업

- 앞서 내려 받은 JSON 파일을 불러와 구글 시트와 연동합니다.

<pre><code>from oauth2client.service_account import ServiceAccountCredentials
import gspread

scope = ['https://spreadsheets.google.com/feeds',
         'https://www.googleapis.com/auth/drive']
credentials = ServiceAccountCredentials.from_json_keyfile_name(
        '{your JSON key file path}', scope)
gc = gspread.authorize(credentials)
</code></pre>

<br>
#### 2) 실제 파일 읽어보기 
- 임시로 샘플데이터를 생성했습니다.

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-5.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

- ```get_all_values()``` 함수를 이용해 코드를 실행하면 리스트 형태의 값이 리턴됩니다.

<pre><code>gc1 = gc.open("python-linkage-sample").worksheet('시트1')
gc2 = gc1.get_all_values()
print(gc2)
</code></pre>
<code>[['id', 'product', 'price'], ['1', 'book', '1000'], ['2', 'pencil', '100'], ['3', 'pen', '300'], ['4', 'notebook', '500'], ['5', 'eraser', '50']]<code>

- ```pandas``` 패키지를 활용해 데이터 프레임으로 받아올 수도 있습니다.
<pre><code>gc2 = pd.DataFrame(gc2, columns=gc2[0])
gc2 = gc2.reindex(gc2.index.drop(0))
print(gc2)
</code></pre>

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-7.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

<br>
#### 3) 셀 업데이트 하기 
- 셀을 지정하고 업데이트 할 내용을 넣습니다.

<pre><code>gc1.update_acell('B2', 'novels')
</code></pre>
<code>{'spreadsheetId': '1jC0tOnSm4LMMenJbdibxhC2dvxYl8SBmwi8Ne-sKByI',
 'updatedRange': "'시트1'!B2",
 'updatedRows': 1,
 'updatedColumns': 1,
 'updatedCells': 1}
​</code>
<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-8.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

<br>
더 많은 Usage는 [gspread Github](https://github.com/burnash/gspread)와 [gspread Guide](https://gspread.readthedocs.io/en/latest/)를 참조하세요.  
R을 이용해 구글 시트와 연동하는 것은 훨씬 간단합니다. [**googlesheets를 이용하여 R에서 구글 시트 연동하기** 포스팅](http://yurimkoo.github.io/r/2019/07/20/link-with-googlesheets-for-R.html)을 참조하세요.

---
layout: post
title: "googlesheets를 이용하여 R에서 구글 시트 연동하기"
author: Yurim Koo
categories:
  - R
tags:
  - R
  - Rstudio
  - googlesheets
  - R 구글 시트
  - R googlesheets
  - R gs
comments: true
---

같은 주제 하에 각기 다른 사람이 작성한 내용을 취합해 하나의 파일로 만들어 본 경험이 다들 있으실 겁니다. 그렇다고 DB 테이블로 관리하기에는 너무 가볍고, 첨부파일로 주고 받기에는 내용의 변동이 너무 잦아서 번거로울 때 한 번쯤 이용해 봤을 구글 시트.  

[구글 스프레드 시트](https://www.google.com/intl/ko_kr/sheets/about/)는 구글에서 제공하는 무료 웹 기반 소프트웨어 중 하나입니다. 구글 시트 외에도 구글 문서, 구글 프레젠테이션 등을 제공하고 있습니다.  

csv 파일을 내려 받을 필요 없이 구글 시트 내의 내용을 Python에서 바로 불러오고, 사용하는 방법을 알아봅시다.  

구글 시트를 연동하는 데에도 다양한 방법이 있겠지만, 이 포스팅에서는 '[googlesheets](https://cran.rstudio.com/web/packages/googlesheets/vignettes/basic-usage.html)' 패키지를 이용하는 방법을 소개합니다.  

참고로, R을 이용한 구글 시트 연동은 복잡한 인증 절차 및 권한 설정을 안 해줘도 된다는 장점이 있습니다.  
(Python 연동과 비교하기: [**gspread를 이용하여 Python에서 구글 시트 연동하기** 글 보러 가기](http://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-Python.html)) 

<br>

------

<br>
### 1. 사용 환경 셋업하기 
#### 1) 패키지 설치하기 

- R에서는 Python과 다르게 구글에서 복잡한 사용자 인증 설정을 할 필요가 없습니다. 패키지를 설치하면 자동으로 인증하는 링크가 연결됩니다.  

- R Studio 혹은 R 콘솔에서 다음 명령어를 실행합니다. 설치가 완료되면 불러옵니다.  

- 만일 콘솔이나 터미널에서 명령 실행시, CRAN 미러를 설정하라고 나오는데 한국 중 하나를 골라서 선택해주면 됩니다.  

<pre><code>install.packages('googlesheets')
library(googlesheets)</code></pre>

<br>
#### 2) 사용자 인증하기 

- R에서 코드를 실행하면 아래와 같은 문구가 나오면서 자동으로 웹 인증으로 연결됩니다.

<pre><code>gs_auth(new_user = TRUE)</code></pre>
<code>Disabling .httr-oauth by renaming to .httr-oauth-SUSPENDED <br>
Removing google token stashed internally in 'googlesheets'.<br>
Waiting for authentication in browser...<br>
Press Esc/Ctrl + C to abort</code>

| ![Image Alt 텍스트]({{"/assets/img/link-with-googlesheets-r/1.png"| relative_url}}) | ![Image Alt 텍스트]({{"/assets/img/link-with-googlesheets-r/2.png"| relative_url}}) |

- 연동에 사용할 계정을 선택하고 접근을 허용하면 다음과 같은 화면이 나오면서 인증이 완료됩니다.

| ![Image Alt 텍스트]({{"/assets/img/link-with-googlesheets-r/3.png"| relative_url}}) |  


<br>
### 2. R에서 실행하기
#### 1) 구글 시트 불러오기

- 임시로 샘플데이터를 생성했습니다. 
- Python에서 구글 시트 연동하기 [글](http://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-Python.html)을 작성하면서 만든 샘플이라 타이틀에 Python이라고 적혀 있는 것은 이해해주세요. :>

<figure>
  <img data-action="zoom" src='{{ "/assets/img/link-with-googlesheets/link-with-gs-5.png" | relative_url }}' alt='absolute'>
  <center><figcaption></figcaption></center>
</figure>

- ```gs_title()```: 읽어 올 시트의 타이틀을 지정하여 불러옵니다.
- ```gs_read(ss, ws, ...)```: 스프레드 시트와 워크 시트를 지정해 직접 데이터를 불러옵니다.

<pre><code>gSht <- gs_title('python-linkage-sample')
gSht <- gs_read(gSht)
print(gSht)
</code></pre>

<code>     id product  price <br>
1     1 novels    1000 <br>
2     2 pencil     100 <br>
3     3 pen        300 <br>
4     4 notebook   500 <br>
5     5 eraser      50</code>

- 데이터가 잘 불려오는 것을 확인하실 수 있습니다.
- 기본적으로 데이터프레임 형태로 리턴되기 때문에 바로 사용하기 용이합니다.
- Tip) 해당 시트의 정보가 attr 객체로 변수에 함께 담겨져 있습니다. attr 객체가 보기 싫다면 아래 함수를 이용해 attr을 해제해 줄 수 있습니다. ([attr - R Documentation 참조하기](https://www.rdocumentation.org/packages/base/versions/3.6.1/topics/attr))
<pre><code>stripAttributes <- function(data){
  attr <- names(attributes(data))
  good <- c("names", "row.names", "class")
  for(i in attr[!attr %in% good]){
    attr(data, i) <- NULL
  }
  return(data)
}
gSht <- stripAttributes(gSht)
</code></pre>

<br>
#### 2) 시트에 값 업데이트 하기

- ```gs_edit_cells(ss, ws, input, anchor, ...)```: 원하는 범위에 값을 업데이트 합니다.  
여기서 주의해야 할 점은, ss 파라미터에 시트 이름을 적는게 아닌, ```gs_title()```로 불러 온 시트 객체를 넣어주어야 합니다.

<pre><code>gSht <- gs_title('python-linkage-sample')
# gSht <- gs_read(gSht) # don't use 
gs_edit_cells(gSht, input = 'hello!', anchor = 'B2')</code></pre>

<br>
#### 3) 시트 업로드하기 (덮어쓰기하기)

- 불러 온 시트에서 'new'라는 컬럼을 추가해서 다시 업로드 해 봅시다.
- ```dplyr``` 패키지를 사용해서 열을 추가하도록 하겠습니다. 데이터를 용이하게 다룰 수 있게 도와주는 패키지입니다. ([dplyr - R Documentation 참조하기](https://www.rdocumentation.org/packages/dplyr/versions/0.7.8))

<pre><code>library(dplyr)
new_gs <- gSht %>% bind_cols(`new` = c(T, T, F, F, F))
print(new_gs)
</code></pre>
<code>     id product  price new  <br>
1     1 hello!    1000 TRUE <br>
2     2 pencil     100 TRUE <br>
3     3 pen        300 FALSE <br>
4     4 notebook   500 FALSE <br>
5     5 eraser      50 FALSE</code>

- 이제 새로운 내용이 담긴 시트를 업로드 해보겠습니다. 
- 여기서 조금 불편한 점은 R 객체를 바로 시트로 올리지 못하고 파일로 저장해서 올려야 한다는 것입니다.
- ```시트 업로드```: 새로운 파일 이름 생성해서 업로드 -> 파일 이름의 새로운 구글 시트 생성
- ```시트 덮어쓰기```: 구글 시트와 같은 이름으로 파일 이름 지정해서 ```overwrite = T``` 옵션주고 업로드 -> 해당 시트 내용이 일괄 업데이트 (덮어쓰기)

<pre><code># 시트 업로드 
fwrite(new_gs, 'sample.csv')
gs_upload(file = 'sample.csv', overwrite = F) 

# 시트 덮어쓰기 
fwrite(new_gs, 'python-linkage-sample.csv')
gs_upload(file = 'python-linkage-sample.csv', overwrite = T)
</code></pre>

| ![시트 업로드]({{"/assets/img/link-with-googlesheets-r/4.png"| relative_url}}) | ![시트 덮어쓰기]({{"/assets/img/link-with-googlesheets-r/5.png"| relative_url}}) |
| :--: | :--: | 
| 시트 업로드 결과 | 시트 덮어쓰기 결과 |

<br>
#### Tip

- ```googlesheets``` 패키지는 ```%>%``` (파이프 연산자)가 지원됩니다.
- 위에서 실행한 코드를 한 줄로 불러올 수도 있습니다!

<pre><code>gSht <- gs_title('python-linkage-sample') %>% gs_read()
print(gSht)
</code></pre>

<br>
이로써 R과 구글 시트 연동이 끝났습니다. 참 쉽죠? 더 많은 기능은 [이 자료 (googlesheets Basic Usage)](https://cran.rstudio.com/web/packages/googlesheets/vignettes/basic-usage.html#authorization-using-oauth2)를 활용해보세요.
Python에서 구글 시트와 연동하는 방법이 궁금하시다면 [**gspread를 이용하여 Python에서 구글 시트 연동하기** 포스팅](http://yurimkoo.github.io/python/2019/07/20/link-with-googlesheets-for-Python.html)를 참조하세요.

<br>
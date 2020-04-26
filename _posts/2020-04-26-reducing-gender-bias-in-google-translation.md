---
layout: post
title: "[Google AI Blog] [번역] 구글 번역에서 성 편향을 줄이기 위한 확장 가능한 접근 방식"
author: Yurim Koo
categories:
  - Analytics
tags:
  - Google AI Blog
  - NLP
  - Natural Language Process
  - Google Translation
  - Gender Bias in Google Translation
  - NMT
  - Neural Machine Translation
  - Precision and Recall
  - Recall
  - Syntactic Parser 
  - Language Model
  - Seq2Seq
  - Transformer-based Sequence-to-Sequence
  - Translation
  - 구글 AI 블로그 
  - 구글 블로그 번역
  - 구글 번역
  - 자연어 처리
  - 구글 번역 성 편향
  - 신경망 번역 시스템 
  - 정밀도와 재현율
  - 재현율
  - 구문 분석
  - 언어 모델
  - 변형 seq2seq
  - 번역글
  - 번역글 포스팅
comments: true
# toc: true
---

<h3>A Scalable Approach to Reducing Gender Bias in Google Translate</h3>

언어 번역을 위한 머신러닝 모델은 훈련 데이터에 반영된 사회적 편향에 의해 왜곡될 수 있습니다. 하나의 예로 성 편향은 성별 언어와 그렇지 않은 언어를 번역할 때 더 명확해집니다. 예를 들어, 구글 번역은 역사적으로 터키어 "그/그녀는 의사이다."를 남성적인 형태로, "그/그녀는 간호사이다."를 여성적인 형태로 번역했습니다.  

불공정한 편향을 만들거나 강화하지 않는다는 중요성을 강조하는 구글의 원칙에 따라, 2018년 12월 성별 번역을 발표했습니다. 이 기능은 원어에서 성별 중립적인 검색어를 번역할 때 여성 및 남성 번역 모두에 대한 옵션을 제공합니다. 이 작업을 위해 성별 중립 쿼리 감지, 성별 번역 생성, 정확도 검사와 관련된 3단계 접근 방식을 개발했습니다. 이 접근 방식을 사용하여 터키어에서 영어로 구문과 문장을 성별에 맞게 번역할 수 있게 되었으며, 이제는 구글 번역에서 가장 인기 있는 스페인어-영어 번역에 이 접근 방식을 확장했습니다.  

<center><img src="https://1.bp.blogspot.com/-bhM1nk5gSP0/XqCL8Px8y0I/AAAAAAAAF1A/J0VINz3WhiADmNKdpwB-31DD0CWUfR4VgCLcBGAsYHQ/s640/before-after.png" width="70%" height="70%" title="사례" style="display: inline;"></center>

<center><font size="2" color="grey">좌: 성별 중립적 영어 문구를 스페인어로 번역한 초기 사례 (이 경우 편향된 예만 제공됩니다.)</font></center>
<center><font size="2" color="grey">우: 새로운 번역은 여성과 남성 번역 옵션을 모두 제공합니다.</font></center>  

<br> 

그러나 이 방법이 더 많은 언어에 적용되면서 확장에 문제가 있다는 것이 분명해졌습니다. 특히, [NMT](https://en.wikipedia.org/wiki/Neural_machine_translation) (neural machine translation) 시스템을 사용하여 독립적으로 남성 및 여성 번역을 생성하면 [recall](https://en.wikipedia.org/wiki/Precision_and_recall) (재현율)이 줄어들어 적격 쿼리의 최대 40%에 대해 성별 번역이 표시되지 않습니다. 왜냐하면 두 번역이 성 관련 현상을 제외하고는 정확히 일치하지 않았기 때문입니다. 추가적으로, 각 언어의 성 중립성을 감지하기 위해 분류기를 생성하는 것은 데이터 중심적이었습니다.  

오늘, 새로운 영어-스페인어의 성별 번역 기능 출시와 함께, 성 편향을 해결하기 위해 초기 번역을 다시 쓰거나 편집하여 극적으로 다른 패러다임을 사용하는 개선된 접근법을 발표합니다. 이 접근법은 성별 중립성 검출기가 필요하지 않기 때문에 특히 성 중립 언어에서 영어로 번역할 때 더 확장 가능합니다. 이 방법을 사용하여 핀란드어, 헝가리어 및 페르시아어를 영어로 번역하는 것을 포함하도록 성별 번역을 확장했습니다. 또한 새로운 재작성 기반 방법을 사용하여 이전 터키어-영어 시스템을 교체했습니다.  

<br> 

<h3>재작성 기반 성별 번역</h3>

재작성 기반 방법의 첫 번째 단계는 초기 번역을 생성하는 것입니다. 그런 다음 성별 중립 소스 문구가 성별 번역을 생성한 사례를 식별하기 위해 번역을 검토합니다. 이 경우 문장 수준의 재작성자를 적용하여 대체 성별 변환을 생성합니다. 마지막으로, 최초의 번역과 재작성된 번역 모두 검토하여 유일한 차이점이 성별인지 확인합니다.

<center><img src="https://1.bp.blogspot.com/-bI20JjyYU1E/XqCMCVjkEDI/AAAAAAAAF1E/YkfKSMrsktol3Wy5LzC4ZwNJrMYNqC0ZgCLcBGAsYHQ/s640/pipeline.png" width="70%" height="70%" title="방식의 변경" style="display: inline;"></center>

<center><font size="2" color="grey">위: 원래 접근 방식</font></center>
<center><font size="2" color="grey">아래: 새로운 재작성 기반 번역 방식</font></center>  

<br> 

<h3>재작성자</h3>

재작성자를 구축하는 데는 문구 쌍으로 구성된 수백만 건의 훈련 예제를 생성하는 작업이 포함되었으며, 각각에는 남성 및 여성 번역이 모두 포함되어 있습니다. 이러한 데이터는 쉽게 이용할 수 없었기 때문에 이 목적에 부합하는 새로운 데이터 셋을 생성했습니다. 대규모 단일 언어 데이터 셋부터 시작해서 성별 대명사를 남성에서 여성으로 또는 그 반대로 교환하여 후보 재작성을 프로그래밍으로 생성했습니다. 문맥에 따라 여러 유효한 후보자가 있을 수 있기 때문에 (예: 여성 대명서 "her"은 "him" 또는 "his"로 매핑될 수 있고, 남성 대명사 "his"는 "her" 또는 "hers"로 매핑될 수 있음) 올바른 것을 선택하기 위한 매커니즘이 필요했습니다. 
이 문제를 해결하기 위해 [syntactic parser](https://en.wikipedia.org/wiki/Parsing) (구문 분석기) 또는 [language model](https://en.wikipedia.org/wiki/Language_model) (언어 모델)을 사용할 수 있습니다. 왜냐하면 구문 분석 모델은 각 언어로 레이블링 된 데이터 셋을 사용한 훈련이 필요하기 때문에, 비지도 방식으로 학습할 수 있는 언어 모델보다 확장성이 떨어집니다. 

<center><img src="https://1.bp.blogspot.com/-DXxHVlBDsmw/XqCC8KckpnI/AAAAAAAAF00/gAxZroAUC2AlH0Emtk62mceGELZ8nFfGwCLcBGAsYHQ/s640/Table.png" title="방식의 변경" width="70%" height="70%" style="display: inline;"></center>

<center><font size="2" color="grey">이 테이블은 데이터 생성 프로세스를 보여줍니다.</font></center>
<center><font size="2" color="grey">입력부터 시작하여 후보를 생성한 다음 언어 모델을 사용하여 하나를 선택합니다.</font></center>

<br> 

위의 데이터 생성 프로세스는 남성 입력에서 여성 출력으로 또는 그 반대로 진행되는 교육 데이터를 생성합니다. 이 두 방향의 데이터를 병합하고 [transformer-based sequence-to-sequence](https://en.wikipedia.org/wiki/Seq2seq) 모델을 훈련시킵니다. 모델의 견고성을 높이기 위해 훈련 데이터에 구두점 및 대소문자 변형을 도입했습니다. 우리의 최종 모델은 요청된 성별의 재작성자를 99% 안정적으로 생성할 수 있습니다.

<br> 

<h3>평가</h3>

새로운 번역 시스템과 기존 시스템 사이의 편향의 상대적인 감소를 측정하는 bias reduction (편향 감소)이라는 새로운 평가 방법을 고안했습니다. 
여기서의 편향은 소스에서 지정되지 않은 번역에서 성별을 선택하는 것입니다. 예를 들어, 현재 시스템이 시간의 90%의 편향을 가지고 새로운 시스템이 시간의 45%의 편향을 가지면, 이는 50%의 상대적 편향 감소를 초래합니다. 이 측정법을 사용하면, 새로운 접근 방식은 헝가리어, 핀란드어 및 페르시아어에서 영어로 번역할 때 편향이 90% 이상 감소합니다. 기존 터키어-영어 번역의 편향 감소율은 새로운 접근 방식으로 인해 60%에서 95%로 향상되었습니다. 이 시스템은 평균 정확도 97%로 성별 번역을 실행합니다(즉, 성별 번역을 표시하기로 결정한 경우 시간의 97%에 해당).  

<center><img src="https://1.bp.blogspot.com/-uNZay4wiO1o/XqCMH_uAIaI/AAAAAAAAF1I/JaFKa8Mt5kI7kc1BD8P0JoWaubpOhPQ9wCLcBGAsYHQ/s640/new-langs.png" width="70%" height="70%" style="display: inline;"></center>

<br> 

이로 인해 성별에 따른 번역의 품질을 높이고 언어 쌍을 4개 더 확대하여 처음 출시 한 이후로 크게 발전했습니다. 구글 번역에서 성 편향을 해결하기 위해 노력하고 있으며, 이 작업을 문서 수준의 번역으로 확대할 계획입니다.  

<br> 

<h3>감사의 말</h3>

이러한 발전은 다음을 포함한 많은 사람들의 노력 덕분에 성공적으로 이루어졌습니다.: _Anja Austermann, Jennifer Choi‎, Hossein Emami, Rick Genter, Megan Hancock, Mikio Hirabayashi‎, Macduff Hughes, Tolga Kayadelen, Mira Keskinen, Michelle Linch, Klaus Macherey‎, Gergely Morvay, Tetsuji Nakagawa, Thom Nelson, Mengmeng Niu, Jennimaria Palomaki‎, Alex Rudnick, Apu Shah, Jason Smith, Romina Stella, Vilmos Urban, Colin Young, Angie Whitnah, Pendar Yousefi, Tao Yu_  

<br>

--- 

<br>

<h4>References</h4>

- 원문: [A Scalable Approach to Reducing Gender Bias in Google Translate](https://ai.googleblog.com/2020/04/a-scalable-approach-to-reducing-gender.html) / Wednesday, April 22, 2020 / Posted by Melvin Johnson, Senior Software Engineer, Google Research

<br>

최대한 원문과 다르지 않게 옮기려고 노력하였습니다.

<br>
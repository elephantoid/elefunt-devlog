---
title: "[IBM C:louders] 11월 미션! Watson Studio for ML"
description: "IBM Cloud로 머신러닝 2"
layout: post
toc: false
comments: true
search_exclude: true
categories: [IBM Clouders]
image: "/images/ibm_clouders.png"
---

![]({{ site.baseurl }}/images/hexo/head_m.PNG)

# 11월 미션 IBM Cloud 관련 기술 포스팅

IBM Cloud에 매우 다양한 기술들이 있습니다.

그 중에 할만한게 뭐 없을까 IBM 블로그를 둘러보다가 ...  
"공개의료데이터 분석을 통해 데이터 통찰력 향상하기"가 있었습니다.  
저번 달에 MAIC에서 주관하는 심박수를 활용하여 수술중 저혈압을 예측하는 대회가 있었는데요
그 때가 떠오르면서 이번엔 어떤 의료 데이터인지 궁금해졌습니다.

그래서 이번 주제는 **"Watson Studio for ML"**입니다

# 요약

오피오이드(opioid) 과다 복용에 의한 사망, 처방자 유형, 처방전과 같은 Kagle 데이터를 기반으로 scikit-learn과 Python (IBM Watson Studio)을 사용하여 오피오이드(opioid) 처방을 예측합니다. 이 패턴을 통해 Watson Studio 노트북에서 데이터를 탐색하고, Pixie Dust를 사용해서 여러가지 초기 결과를 시각화 할 수 있습니다. 초기 탐색 후에는 scikit-learn을 사용하여 여러 모델을 훈련하고 오피오이드(opioid) 처방을 가장 정확하게 예측할 수있는 방법을 파악합니다.

# WorkFlow

<img width="500" alt="flow" src="https://user-images.githubusercontent.com/70086728/99982267-b3161500-2ded-11eb-8658-877a2dca7fd2.png">

1. Watson Studio 서비스에 로그인하십시오.
2. Watson Studio에 데이터 자산으로 데이터를 업로드하십시오.
3. Watson Studio에서 노트북을 시작하고 이전에 작성한 데이터 자산을 입력하십시오.
4. Pixie Dust로 데이터 시각화를 생성하십시오.
5. pandas로 데이터를 탐색하십시오.
6. scikit-learn으로 기계 학습 모델을 훈련시킵니다.
7. 예측 성능을 평가하십시오.

## Watson Studio to make a New Project

### New Project

<img width="850" alt="watson-1" src="https://user-images.githubusercontent.com/70086728/99982506-fcfefb00-2ded-11eb-8b67-2e1bb4812db4.PNG">

먼저 Watson Studio로 접속하신 후 빨간 네모를 누르시면 새로운 프로젝트를 생성할 수 있습니다.

> notice: 블로그에 있는 참조 페이지는 오래되서 지금과 약간 다를 수 있습니다.

<img width="850" alt="watson-2" src="https://user-images.githubusercontent.com/70086728/99982794-5830ed80-2dee-11eb-82c4-1e2a189f4fa3.PNG">

두 가지 중에서 저는 어떤한 Form도 갖고 있지 않기 때문에 빈 프로젝트를 생성했습니다.

### Load Data

<img width="942" alt="data" src="https://user-images.githubusercontent.com/70086728/99983186-e0af8e00-2dee-11eb-9c92-52b96421bf2c.PNG">

Asset(자산)카테고리의 우측에 보시면 데이터를 로드할 수 있는 곳이 있습니다.  
Drop방식이라 편리해요!

그렇게 추가를 하시면 Asset에 데이터를 확인할 수 있습니다.

<img width="773" alt="asset_data" src="https://user-images.githubusercontent.com/70086728/99983484-3f750780-2def-11eb-995d-14b60e69aa38.PNG">

### New Notebook

사실 노트북을 불러오는 방식은 두 가지가 있습니다.

1. 블로그 내용대로 Github에서 가져온다.
2. 로컬 ~.ipynb 파일을 로드한다.

저는 두번째 방식을 택했습니다.

<img width="771" alt="new_notebook-1" src="https://user-images.githubusercontent.com/70086728/99983545-5582c800-2def-11eb-9999-d0e3d8c707d4.PNG">

빨간 네모에 로컬에 있던 test.ipynb를 추가했습니다.  
python 설정환경은 여러가지가 있는데 그 중에 필요하신 것을 선택하세요.

마지막에 Create를 누르시면

<img width="600" alt="notebook-1" src="https://user-images.githubusercontent.com/70086728/99983648-777c4a80-2def-11eb-9a29-cb5fbfe5b0b2.PNG">

# Kaggle

<img width="720" alt="kaggle-1" src="https://user-images.githubusercontent.com/70086728/99983928-d5109700-2def-11eb-8608-08b667633727.PNG">

이제 데이터를 찾으러 Kaggle에 접속합니다.  
Data 탭을 누르시고 검색하시면 바로 나와요!

<img width="583" alt="kaggle_data-1" src="https://user-images.githubusercontent.com/70086728/99983938-d772f100-2def-11eb-83a5-c5365efa257a.PNG">

캐글의 장점!!! 미리 데이터셋이 어떤 컬럼과 변수명으로 되어있는지 심지어 시각화까지 해줍니다

# Machine Learning

사실 처음에 Asset에 분명 데이터를 추가했는데 파일 경로를 모르겠어서 매우 당황스러웠습니다...

진짜 !ls를 해도 아무것도 안보이고 막막하던 차에 엄청난 기능을 알게 되었습니다.

<img width="955" alt="load_dataset_in_notebook" src="https://user-images.githubusercontent.com/70086728/99984727-caa2cd00-2df0-11eb-8568-076b2e7aae5e.PNG">

우측 상단에 있는 빨간 네모를 클릭하시면 옆에 저렇게 새로운 창이 보입니다.

그리고 Pandas DataFrame을 눌러주시면!!

<img width="936" alt="res" src="https://user-images.githubusercontent.com/70086728/99984854-ec9c4f80-2df0-11eb-920d-fcf4a8742294.PNG">

자동으로 Pandas로 데이터를 읽고 .head()까지 해줍니다.  
처음에 너무 신기해서 몇 번을 다시 해봤는지 모르겠네요.

이렇게 데이터를 로드했으니 Kaggle Notebook을 참고하여 학습했습니다.

<img width="849" alt="accuracy" src="https://user-images.githubusercontent.com/70086728/99985010-1e151b00-2df1-11eb-872b-fd9869777848.PNG">

Accuracy는 이렇게 나왔고 그 높은 3개를 택해서 plot도 작게 그려봤습니다.  
\*In sample = train set, Out sample = test set

### In Sample

<img width="851" alt="plt1" src="https://user-images.githubusercontent.com/70086728/99985205-43098e00-2df1-11eb-80ef-f93fcf540eff.PNG">

### Out Sample

<img width="856" alt="plt2" src="https://user-images.githubusercontent.com/70086728/99985242-456be800-2df1-11eb-8917-a660e314f5fe.PNG">

# 후기

처음으로 IBM Cloud를 활용해 기계학습을 해봤습니다. 제가 선택한 python 문제인지는 모르겠습니다만 Colab에서는 얼마나 또 성능이 차이날지 확인은 해보고 싶어집니다.  
처음에 NoteBook안에서 파일 경로를 몰라서 엄청 해맸던게 아휴... 그런데 또 그런 꿀기능이 저런 곳에 숨어있었다니 생각지도 못했습니다. 천천히 하나씩 해보면서 어디까지 가능한가 궁금하기도 합니다.

-끝-

# References

[IBM기술블로그](https://developer.ibm.com/ko/patterns/analyze-open-medical-data-sets-to-gain-insights//)

[DATA받는곳(Kaggle)](https://www.kaggle.com/apryor6/us-opiate-prescriptions/notebooks)

[참고한NoteBook](https://www.kaggle.com/jiashenliu/quick-and-dirty-attempt-on-voting-classifier)

[노트북에서데이터로드및액세스](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/load-and-access-data.html)

[Loading data from your local system](https://cloud.ibm.com/docs/Db2whc?topic=Db2whc-load_local)

---
layout: post
title:  "Time Series Forecasting"
date:   2022-09-06T14:25:52-05:00
author: Junsu Noh
categories: Machine Learning
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---



## 시계열(Time-Series) 데이터란?

<aside>
💡 -. 시간의 흐름에 따라 순차적으로(sequentially) 기록된 데이터를 가리킨다.

💡 -. 관측된 시계열 데이터를 분석하여 미래를 예측하는 문제가 바로 시계열 예측 문제이다.

💡 -. ex. 경제 지표, 상품의 수요 등을 예측하는데 사용된다.

</aside>

![예측 이미지](https://user-images.githubusercontent.com/90672736/188826230-6011e411-cefe-4895-adfa-0a927639908a.png)

## 시계열을 구성하는 요소는 크게 총 4가지 (****components)****

- **추세**(Trend) : 추세는 장기간 데이터의 일반적인 경향을 보여줍니다. 추세는 부드럽고 일반적인 장기적인 경향입니다. 짧은 구간에서 다른 변동을 보일 수 있지만 전체 추세는 상향, 하향 또는 안정적이어야합니다. 인구, 농업 생산, 제조 품목, 출생 및 사망자 수, 산업 또는 공장 수, 학교 또는 대학 수는 일종의 운동 경향을 보여주는 예입니다.
- **순환**(Cyclic Variations ) : 1 년 이상 지속되는 시계열의 변동을 순환이라고 합니다. 이 변동은 1 년 이상의 주기를 갖습니다. Business cycle이라고 불리기도 합니다.
- **계절성**(Seasonal Variations ) : 1 년 미만의 기간에 걸쳐 규칙적이고 주기적으로 나타나는 변동입니다. 이러한 변동은 자연의 힘이나 사람이 만든 관습으로 인해 시작됩니다. 다양한 계절 또는 기후 조건은 계절 변화에 중요한 역할을합니다. 농작물 생산량은 계절에 따라 달라지고, 여름에 선풍기와 에어컨의 판매량이 높아지고 겨울에 판매량이 낮아지는 특징을 보이는 것을 계절성이라고 합니다.
- **불규칙 변동요인**(Random or Irregular movements) : 이 변동은 예측할 수없고 제어 할 수없고 예측할 수 없으며 불규칙합니다.

![Untitled (5)](https://user-images.githubusercontent.com/90672736/188826388-8019f7c8-10d1-4f9d-8755-4efb6710d19b.png)

## 구성 요소 예시:

![Untitled (6)](https://user-images.githubusercontent.com/90672736/188826512-f49a88b5-cff9-4541-ac90-7be16986f50d.png)

**추세**

![Untitled (7)](https://user-images.githubusercontent.com/90672736/188826650-d39e7df5-8f48-4b11-b3ea-f99805701a5c.png)

**순환**

![Untitled (8)](https://user-images.githubusercontent.com/90672736/188826734-0e2f9d1f-9629-408e-8b88-280e6511f215.png)

**계절성**

![Untitled (9)](https://user-images.githubusercontent.com/90672736/188826795-2ade765f-e2b3-42e6-9592-ea7d16c5f36e.png)

**예측불가**

![Untitled (10)](https://user-images.githubusercontent.com/90672736/188826835-93c88db7-c67f-4450-8b2f-f577ac1dba1f.png)

### 시계열 예측의 문제점 4가지

### 1. 예측값은 완벽할 수 없으며, 항상 변동의 가능성을 내포.

따라서 시계열 예측에 있어 **불확실성(uncertainty)**이 고려되어야 한다. 다시 말해 예측값에 대해 적합한 **확률 분포 모델(probability distribution)**이 고려되어야 한다. 단순히 어떤 에측 값을 제공한다기 보다는 그 예측값의 불확실성(또는 신뢰도)에 대해 정량적인 평가가 함께 고려되어야 예측 모델로써 더욱 가치가 있다.

### 2. 시계열 데이터에 숨겨진 여려 형태의 패턴을 찾아 학습하는 문제.

보통 trend,seasonality,cycle 등의 유형으로 패턴을 구분하곤 한다. 대게 시계열 데이터에는 이러한 패턴이 복잡하게 섞여있고 데이터의 길이가 불충분 하거나, 노이즈, 아웃라이어 데이터로 인해 손쉽게 패턴을 구분해 내어 찾기가 어렵다.

### 3. 다변량 시계열 데이터(multiple time-series)를 다뤄야 하는 경우가 많아지고 있다.

일변량 : 변수가 1개

![Untitled (11)](https://user-images.githubusercontent.com/90672736/188826921-4a134fa6-9665-4323-a9cf-7f85d1715485.png)

다변량 : 변수가 2개 이상

![Untitled (12)](https://user-images.githubusercontent.com/90672736/188826983-2b36f6dc-1ed0-4c9e-b53a-e791e806087d.png)

과거에는 주로 다변량의 시계열 데이터(unvariate time-series)의 분석과 예측 문제가 주로 다뤄졌지만 근래에는 다변량 시계열 데이터를 다뤄야 하는 경우가 많아지고 있다.

순차적으로 관찰된 수많은 변수들에 대해, 이 변수들간의 상관 관계를 학습할 수 있어야 한다. 가령, 어떤 상품의 수요가 다른 상품들의 수요 변화에 영향을 받거나, 어떤 지역의 택시 수요가 다른 지역의 택시 수요와 일정한 시간 차이를 두고 상관 관계를 보일 수 있다. **이 변수가 적게는 수백에서 많게는 수백만에 이를 수 있으므로, 이를 효율적으로 처리하고 학습할 수 있는 알고리즘이 매우 중요해 지고 있다.**

### 4. 시계열 데이터에는 노이즈 데이터 또는 관찰되지 못한 기간이 종종 존재한다.

이는 측정하고 데이터를 기록하는 과정에서의 오류나 예측치 못한 상황으로 인해 발생할 수 있다. 예를 들어 상품의 품절로 인하여 장기간 판매량이 없는 경우, 해당 상품에 대한 실제 수요를 판매량으로 유추할 수 없는 경우이다.

**시계열 예측 문제에 있어서는 이에 대한 적절한 전처리 과정이 매우 중요하다.**

## 시계열 예측 예시

**Facebook의 Prophet 오픈소스 라이브러리**

<aside>
💡 일변량 시계열 데이터셋(univariate time series datasets)을 예측하기 위해 설계된 오픈소스 라이브러리이다.

기본 적으로 추세나 계절적 구조를 가진 데이터에 대해 모델에 적합한 하이퍼 파라미터들을 자동으로 찾을 수 있도록 설계되었고 사용하기 쉽다. (추세, 계절성에 특화)

</aside>

- 하이퍼 파라미터 : 모델링할 때 사용자가 직접 세팅해줘야 하는 값

모델 평가 과정

MAE(Mean Absolute Error) = **평균 절대 오차**

실제 정답 값과 예측 값의 차이를 절댓값으로 변환한 뒤 합산하여 평균을 구한다. 특이값이 많은 경우에 주로 사용된다. 값이 낮을수록 좋다.

## **시계열 구성요소 간의 구성(composition)**

![image](https://user-images.githubusercontent.com/90672736/188827051-29f3822e-e1ba-4e24-8b10-dd2cdc883d09.png)

시계열 데이터를 구성하는 4가지 구성요소는 단일로 시계열을 구성할 수도 있고 가법, 승법 또한 가능합니다.

원본 데이터가 일정하게 안정적인 그래프를 그린다면 '**가법**', 증가하거나 감소하는 형태를 띈다면 '**승법**'으로 선택하면 된다.

- **가법 모형**(addictive model) : 구성요소 간 독립적이고 가정하여 각 구성요소를 더하는 모형
- **승법 모형**(multiplicative model) : 구성요소 간 독립적이지 않고 상호작용 한다고 가정하여 구성요소 간 곱해주는 모형

참고로 여러 구성요소가 하나의 시계열 모형으로 있는 것을 여러개로 나누는 것을 ‘시계열 분해(de-composition)’이라고 합니다.

### **3. 시계열 예측 딥러닝 모델에 영향을 주는 요소**

**1) 데이터 수** : 늘릴수록 성능 향상

**2) history data** : 이전 시점을 얼마만큼 사용하는지에 따라 성능 변화 없음 (어차피 자체적으로 현재 시점에서 가까운 데이터에 대한 가중치를 높게 설정하기 때문)

**3) future data** : 현재 시점을 기준으로 더 멀수록 예측의 정확도가 낮아짐

**4) 이상치 필터링** : 이상치를 필터링할수록 성능이 눈에 띄게 성능이 향상됨 but 이상치를 필터링할 경우, 학습한 데이터 중 이상치가 없어 이상에 대한 예측이 불가함, 이상치가 축적되었을 때 학습한다.

참조 : [https://facebook.github.io/prophet/docs/installation.html](https://facebook.github.io/prophet/docs/installation.html)

[http://datacrew.tech/time-series시계열-forecasting-시리즈-1-기본-개념/](http://datacrew.tech/time-series%EC%8B%9C%EA%B3%84%EC%97%B4-forecasting-%EC%8B%9C%EB%A6%AC%EC%A6%88-1-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90/)

[https://dining-developer.tistory.com/25](https://dining-developer.tistory.com/25)

[https://leedakyeong.tistory.com/entry/Python-날씨-시계열-데이터Kaggle로-ARIMA-적용하기](https://leedakyeong.tistory.com/entry/Python-%EB%82%A0%EC%94%A8-%EC%8B%9C%EA%B3%84%EC%97%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0Kaggle%EB%A1%9C-ARIMA-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)

[https://skyeong.net/290](https://skyeong.net/290)








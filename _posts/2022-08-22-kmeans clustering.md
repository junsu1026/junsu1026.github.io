---
layout: post
title:  "kmeans clustering"
date:   2022-08-21T14:25:52-05:00
author: Junsu Noh
categories: Machine Learning
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---




## 개요

<aside>
💡 K-means machine learning은 머신러닝 종류의 비지도 학습에 속하는 machine learning이다. ( 정답이 주어지지 않고 공부를 시키는 방법 )

💡 K-means는 분류 문제 해결에 있어 사용되는 가장 인기 있는 방법의 비지도 머신러닝 알고리즘 입니다.

**K-means는 레이블이 지정되지 않은 데이터를 유사한 기능, 공통 패턴을 기반으로 클러스터라고 하는 다양한 그룹으로 분리합니다** .

</aside>
    

## 클러스터링이란?

-. 개, 고양이, 새 등과 같은 다양한 동물의 레이블이 지정되지 않은 다변수 데이터 세트가 N개 있다고 가정합니다. ***유사한 특징과 특성을 기반으로 데이터 세트를 다양한 그룹으로 분리하는 기술을 클러스터링이라고*** 합니다.

-. 형성되는 그룹이 클러스터로 입니다. 클러스터링 기법은 이미지 인식, 스팸 필터링 등 다양한 분야에서 활용되고 있습니다.

-. **클러스터링은 데이터 세트 내부에 숨겨진 공통 패턴을 기반으로 감독자 없이 다변수 데이터를 다양한 그룹으로 분리할 수 있기 때문에**  머신 러닝의 비지도 학습 알고리즘에서 사용됩니다 .

## ****K는 알고리즘을 의미합니다.****

Kmeans Algorithm은 n개의 데이터 세트 그룹을 유사성과 해당 특정 하위 그룹의 **중심**으로부터의 **평균 거리**를 기반으로 **k개**의 하위 그룹/클러스터로 나누는 반복 알고리즘입니다 .

- 메모
    
    kmeans는 비계층적 군집화 방법 중 distance(거리) 기반 알고리즘에 속한다.
    kmenas는 k개의 init centroid를 설정해놓고, 각각의 데이터를 가까운 centroid cluster로 할당한 후,
    그리고 cluster 내 centorid를 update하고, 다시 각각의 데이터를 가까운 centroid cluster로 할당하는 과정을 반복한다.
    이 과정을 centorid가 변하지 않을 때 까지 반복 수행된다. (=즉 각 centroid cluster에 할당된 데이터가 더이상 변하지 않을 때 까지)
    (혹은 iteration 횟수를 지정해두기도 한다.)
    
    kmeans에서 중요한 변수는 군집의 개수인
    
    **K ( 중심점의 갯수)**
    
    와
    
    **init centroid (어디를 중심점으로 둘건지)**
    
    이다. init centroid가 어디인지에 따라 최종 수렴된 clustering(무리) 결과가 달라질 수 있기 떄문에
    일부 데이터를 sampling해 hierarchical clusteirng을 수행한 후, 이에 기반해 init centorid를 지정하기도 한다.
    
    k-means++ : 하나의 무작위 중심점을 배정하고 두번째 중심점을 첫번째 중심점과 멀리 떨어진 곳에 배치, 그리고 다음 중심점을 1,2번째 중심점과 멀리 떨어지도록 배치하는 방식이다. 즉, 중심점 선정에 있어서 무작위로 진행하는 것이 아니라 좀 더 신중하게 설정하는 것이다. 이런 방식을 통해서 k-means의 단점을 보완할 수 있다.
    
    **n_init** 
    : 서로 다른 중심점에서 k-means 알고리즘이 실행되는 횟수로, 기본값은 10이다. 관성의 관점에서 n_init 번 연속 실행 결과에서 가장 좋은 결과가 최종결과가 된다
    
    **n_clusters**
    
    : 기본값은 8이다. 군집의 갯수 설정.
    
    **init**
    
    : 초기화 메소드로 기본값은 ‘k-means++’ 이다.
    


### K-means clustering 순서

**1단계:** K 값을 선택하여 형성할 클러스터 수를 결정합니다.

**2단계:** 중심으로 작용할 임의의 K 점을 선택합니다.

**3단계:** 무작위로 선택한 점(Centroid)에서 사전 정의된 클러스터를 형성할 가장 가까운/가장 가까운 중심까지의 거리를 기반으로 각 데이터 점을 할당합니다.

**4단계:** 각 클러스터의 새 중심을 배치합니다.

**5단계:** 3단계를 반복하여 각 데이터 포인트를 각 클러스터의 가장 가까운 새로운 중심에 재할당합니다.

**6단계:** 재할당이 발생하면 4단계로 이동하고 그렇지 않으면 7단계로 이동합니다.

**7단계** : 마침


# K-means Clustering 과정 ⭐

- 원하는 군집의 갯수 (K)와 초기 값(seed)들을 정해 seed 중심으로 군집을 형성
- 각 데이터를 거리가 가장 가까운 seed가 있는 군집으로 분류
- 각 군집의 seed값을 다시 계산
- 모든 개체가 군집으로 할당될 때까지 위와 같은 과정을 반복

| 장점  | 단점 |
| --- | --- |
| -. 알고리즘이 단순하며, 빠르게 수행되어 분석 방법 적용이 용이하다.
-. 계층적 군집분석에 비해 많은 양의 데이터를 다룰 수 있다.
-. k-means 는 다른 군집화 알고리즘과 비교하여 매우 적은 계산 비용을 요구하면서도 안정적인 성능을 보인다.
-. 내부 구조에 대한 사전 정보 없이도 의미 있는 자료구조를 찾을 수 있다.
-. 다양한 형태의 데이터에 적용 가능하다. | -. 군집의 수, 가중치와 거리 정의가 어렵다.
-. seed값에 따라 결과가 달라질 수 있다.(항상 일정한 결과x)
-. 사전에 주어진 목적이 없으므로 결과 해석에 어려움이 있을 수 있다.
-. 노이즈나 이상치의 영향을 많이 받는다.
-. 볼록한 형태가 아닌 군집(ex: U형태 군집)이 존재할 경우 성능이 떨어진다.
-. 이상치에 민감하여 군집 경계의 설정이 어렵다. |

## 최적의 군집 수 결정 방법

<aside>
💡 군집과 군집의 거리는 멀수록 좋고 군집내에서의 데이터들의 거리는 가까울수록 좋다. 
그렇기 때문에 inertia, 즉 군집에 포함된 데이터의 퍼져있는 정도가 낮을수록 좋은 군집이다

목표 : inertia수치를 최소화 하면서 군집의 수도 최소화 하는것

그것의 방법이 elbow method를 사용하는것
(그래프의 모양이 팔꿈치 같다고해서 elbow method이다)

</aside>

yellowbrick 라이브러리를 사용하면 쉽게 구할 수 있다.

```python
from yellowbrick.cluster.elbow import kelbow_visualizer
model = KMeans()
visualizer = kelbow_visualizer(model, X, k=(1,10))
```

위의 코드는 range를 통해서 군집형성의 개수를 1개부터 10개까지 형성하여 반복학습을 진행한다. 그리고 각 군집의 개수 모델마다 군집 내 분산을 확인한다. 이때, 군집의 분산이 작을수록 좋은데 군집 내 분산이 완만하게 줄어드는 지점이 k이다. 군집이 증가하면 당연하게 증가할수록 당연하게 분산은 적어질 수밖에 없다. 하지만, 일정 수준 이상 줄어드는 것이 제대로된 군집형성을 의미하진 않는다.

최적의 초기 

시나리오 : 2022년1월15일 이후에 구매한 이용자를 대상으로 kmeans를 실행한 후 미래에 구매를 많이 할 것 같은 사람들에게 마케팅을 실행한다.

고객의 데이터로 고객을 유형별로 분류하여 분류된 고객군에 맞는 마케팅을 할 수 있는 방법

## 참고

[https://datascienceschool.net/03 machine learning/16.02 K-평균 군집화.html](https://datascienceschool.net/03%20machine%20learning/16.02%20K-%ED%8F%89%EA%B7%A0%20%EA%B5%B0%EC%A7%91%ED%99%94.html)

[https://datavizpyr.com/add-legend-to-scatterplot-colored-by-a-variable-with-matplotlib-in-python/](https://datavizpyr.com/add-legend-to-scatterplot-colored-by-a-variable-with-matplotlib-in-python/)

[https://todayisbetterthanyesterday.tistory.com/60](https://todayisbetterthanyesterday.tistory.com/60)

[https://nicola-ml.tistory.com/1](https://nicola-ml.tistory.com/1)

[https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)

[https://tobigs.gitbook.io/tobigs/data-analysis/undefined-3/python-2-1#1.-preprocessing-eda](https://tobigs.gitbook.io/tobigs/data-analysis/undefined-3/python-2-1#1.-preprocessing-eda)

[https://www.analyticsvidhya.com/blog/2021/04/k-means-clustering-simplified-in-python/](https://www.analyticsvidhya.com/blog/2021/04/k-means-clustering-simplified-in-python/)

[https://steadiness-193.tistory.com/285](https://steadiness-193.tistory.com/285)

[https://github.com/rwguerra/Machine-Learning-with-Python-IBM](https://github.com/rwguerra/Machine-Learning-with-Python-IBM)



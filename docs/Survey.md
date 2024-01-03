---
layout: page
title: A Survey on the Fairness of Recommender Systems
description: >
   To fill this gap, we review over 60 papers published in top conferences/journals, including TOIS, SIGIR, and WWW.

use_math: true
sitemap: false
---
# **0&1. Abstract & Introduction**
---

사용자의 background 에 관계없이 공정한 추천을 제시해야하므로 fairness는 추천시스템에서도 매우 중요하다.

Survey 에서 집중하고 있는 포인트는 다음과 같다.

1. 공정성이 중요하다면, 추천시스템에서의 fairness는 정확히 어떻게 정의되는가?

2. fairness 를 어떤 관점으로 분류할 수 있을까?

3. fairness를 측정할 metric은 무엇이 있을까?

5. 불공정성을 해소하기 위한 method은 어떤 것이 있는가?

6. 실험을 위한 데이터셋은 무엇이 있는가?

7. future work

# **2. DEFINITIONS OF FAIRNESS IN RECOMMENDATION**
---
추천시스템에서 말하는 '공정하다' 라는 뜻의 정의는 무엇일까?

사실 '공정'이라는 단어는 추상적인 단어이면서도 상대적인 단어이기 때문에 딱 잘라 말하기는 굉장히 어렵다.
그렇기 때문에 추천시스템의 공정성을 연구하는 다양한 논문에서도 각자 다른 방식으로 'fairness'를 해석하고 있다.

우선 과정을 중시하는 공정성과 결과를 중시하는 공정성이 있다.
- Process Fairness(Procedure justice): 모델이 인종,성별같은 요소를 학습 피처에 넣느냐 안 넣느냐, 혹은 그 외의 학습요소들이 편향되지는 않았는가? 등을 체크
- Outcome Fairness(Distributive justice): 모델을 통과해서 나온 결과물이 특정 인종에게 유리하게 작용하는가? 등을 체크

우리는 outcome 쪽에 더 초점을 둘 것이고, outcome fariness은 다시 target과 concept를 기준으로 나눌 수 있다.

자세한 분류는 아래의 이미지와 같다.

<img width="1071" alt="스크린샷 2024-01-02 오후 3 30 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9bed107c-7895-43e2-8d22-6b0a04327ce3">

그러나 정의가 여러개 있으므로 자연스레 어떤 정의가 우선시 되어야 하는가? 라는 의문이 따라옴. 대부분의 연구는 single definition만을 다루기 때문에 이부분은 더 탐구여지가 남아있다.

또한 저자는 공정성 Bias, Diversity, and Privacy 등의 조건을 만족하면서도, 너무 얽매이지도 말아야 한다고 언급하고 있다.(공정이라는 개념은 참 어려운 것 같다...)

fairness의 정의가 정리되어 있는 테이블을 첨부하겠다.

<img width="1152" alt="스크린샷 2024-01-02 오후 3 37 20" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ee4df7eb-4a14-427e-a1a3-59a4444d81b3">

# **3. VIEWS OF FAIRNESS IN RECOMMENDATION**
---


1. Subject: 주체가 누구냐? 

<center>
<img width="484" alt="스크린샷 2024-01-03 오전 10 34 04" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/fe785d74-e2dd-447e-8926-b2adaab242a5">
</center>
{: width="68%", height="68%"}

- 사람이 공정한 대접을 받아야 하냐, 아이템이 공정한 대접을 받아야 하냐? 아니면 둘다냐?

2. Granularity(세분성): 공정성의 범위는 어느 정도가 적당하냐? 
<center>
<img width="481" alt="스크린샷 2024-01-03 오전 10 36 25" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/e88488d7-748e-4a81-9b79-1989964dc7ce">
</center>
{: width="68%", height="68%"}

- 단일 추천리스트가 공정성을 만족해야하느냐, 아니면 특정 시간동안 누적된 추천 리스트가 공정한 분포를 가지느냐로 구분

3. Optimization Object: 모델과 사용자의 상호작용 중 어느쪽을 더 중시할거냐?

<center>
<img width="485" alt="스크린샷 2024-01-03 오전 10 37 13" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/16ad348e-7c7b-4533-9da5-eac4960c7d42">
</center>
{: width="68%", height="68%"}

- 모델이 사용자에게 제공하는 output이 공정해지느냐, 아니면 추천에 의한 reaction이 공정하게 나오는 것을 목표로 하는가?

# **4. MEASUREMENTS OF UNFAIRNESS IN RECOMMENDATION**
---

### *Metrics for CO*

Consistent fairness의 목적은 input이 비슷하면 output이 비슷해야한다는 것이다. 이 사실에 기반해 각 개인 혹은 각 그룹간의 utility inconsistency를 확인할 수 있는 metrics 를 사용한다.
*utility 라는 표현을 사용했는데, 효용성 혹은 퍼포먼스 정도로 생각하는게 좋을 듯 하다.

|Metrics|Subject|Details|
|------------------|---|---|
|Absoloute Difference|둘 다|-|
|KS statistic|user|Area|
|rND,rKL, and rRd|item|measure item exposure fairness|
|Pairwise Ranking Accuracy Gap|item|PRAG measures the unfairness of pairwise ranking accuracy|
|Value Unfairness and its variants|user|measure inconsistency in signed prediction error between two user groups|
|Variance|||
|Min-Max Difference|||
|F-statistic of ANOVA|||
|Gini coefficient|||
|Jain’s index|||
|Entropy||-|
|Min-Max Ratio||-|
|Least Misery.||-|


### *Metrics for CA*

Calibrated fairenss 측정 시에는 그룹의 merit 분포 $p_f$ 와 utility 분포 $p$ 를 비교한다.

그러니까 가지고 있는 merit와 비슷한 utiltiy 분포를 가지고 있어야 한다.-> merit 가 utility 를 따라가야한다.


|Metrics|Subject|설명|
|------------------|---|---|
|Skew|||
|KL-divergence||-|
|NDKL|||
|JS-divergence||symmetric version of KL-div|
|Overall Disparity|||
|Generalized Cross entropy||measures the average disparity of the proportion of the utility and merit among different groups.|
|L1-norm||-|

#### <u> *Co 와 CA의 몇몇 metrics들은 서로 interconvertible 하다.*</u>


### *Metrics for Others*



|Metrics|Subject|설명|
|------------------|---|---|
|Envy-free Fairness|||
|CF|||
|RMF|||
|MSF|||
|PR|||

나중에 읽어보자.

# **5. METHODS FOR FAIR RECOMMENDATION**
---

공정한 추천을 위해서 사용할 수 있는 메소드나 알고리즘은 어떤 것이 있을까?

크게 세 종류로 나눌 수 있다.

1. Data-oriented method: 데이터를 건드려서 불공정성을 해결하는 것
2. Ranking methods: 모델이 공정한 추천을 학습할 수 있도록 만드는 것
3. Re-ranking: ouput이 공정할 수 있도록 건드리는 것

## Data-oriented methods

다양하지는 않음

Re-sampling dataset -> 성능 별로 -> Adding fake data -> 시간소요 너무 큼

데이터를 만지는 것은 pipeline 앞부분 건드는 거기 때문에 컨트롤하기는 용이하지만, modeling 파트에서 무슨일이 벌어질지 모르기 때문에 성능향상에 있어서는 케바케다.

## Ranking methods

- regularization-based methods
- adversarial learning-based methods 
- reinforcement learning-based methods

#### regularization-based methods

모델에 제약을 주는 방식. 
Loss function은 
$L = L_{rec} + \lambda L_{fair} $ 꼴

point-wise

pair-wise




#### adversarial learning-based methods

adverserial learning 이란? 

Loss function은 
$ min_{R} \space max_{D} \space L(R,D) = L_{R} - \lambda L_{D}$ 

Recommender system의 입장에서는 당연히 Loss 를 줄여야하고, Discriminator 는 짭과 찐을 잘 구별해서 Loss를 키워야 한다.


#### reinforcement learning-based methods

장기적인 관점에서의 최대한의 공정성을 추구하려고 한다는 특징이 있다.

강화학습을 이용한 추천시스템의 공정성 구축은 다른 방법과 비교해서 장기적이고 유동적으로 학습을 할 수 있다는 점이다.

그러나 안정성이 떨어지고 offline-data를 가지고 평가하기는 어렵다는 단점도 있다.

#### Others

use transfer learning, use contextual framework, add high var noise to VAE 등등의 방법을 사용 가능.


## Re-ranking methods

장: 중간 모델이 아닌 결과를 조정하는 방식이기 때문에 가장 직관적인 해결 방법이다.

단: candidate set이 작다면, performance 부분에서 손실이 있을 수 있다.

<img width="1172" alt="스크린샷 2024-01-03 오후 3 32 54" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/df391c88-4d12-4219-8f26-a2c5472a1e84">

#### Slot-wise

- general recommendation scenario

- dynamic ranking scenario
#### User-wise

-

-


#### Global-wise


# **6. DATASETS FOR FAIRNESS RECOMMENDATION STUDY**

### <span style="color:red"> 정말 친절하게도 어떤 데이터셋이 어떤 attribute을 포함하고 있는지 정리 해놓으셨단다!! </span>


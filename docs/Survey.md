---
layout: page
title: A Survey on the Fairness of Recommender Systems
description: >
   The research on fairness in recommendation is scattered.To fill this gap, we review over 60 papers published in top conferences/journals, including TOIS, SIGIR, and WWW.

use_math: true
sitemap: false
---
# **1. Abstract & Introduction**
---
~~Survey Paper의 양은 어마무시 했다...~~

게다가 아직 연구를 위한 연구같은 느낌이 짙어 전체적으로 추상적인 내용을 다루어 조금 어려운 점이 있었다.

해당 논문은 현재까지 진행된 추천시스템의 공정성 연구를 소개하고 앞으로의 방향성을 잡아 주는 논문이라고 볼 수 있다.

추천시스템은 당연히 그 목적 자체가 Uitility 의 최대화라고 볼 수 있지만, 최근 들어 사용자의 background 에 관계없이 공정한 추천을 제시해야한다는 의견이 강조되고 있다. 따라서 fairness-aware 추천시스템, 즉 공정성을 반영한 추천시스템은 매우 중요하다.
> Utility란? 클릭률 등 모델의 퍼포먼스에 대한 사용자의 만족도? 정도로 해석.

> Fairness-related attributes란? gender, age, race 등 차별의 원인이 될 수 있는 특성들을 의미

<br/>
Survey Paper 에서 소개하는 포인트는 다음과 같다.

1. 추천시스템에서 fairness 라는 것의 정확한 정의가 무엇이냐?

2. fairness를 어떤 관점으로 분류할 수 있을까?

3. fairness를 측정할 metric은 무엇이 있을까?

5. 불공정성을 해소하기 위한 method은 어떤 것이 있는가?

6. 실험을 위한 데이터셋은 무엇이 있는가?

7. 앞으로의 연구방향성은 무엇이 있는가?

  
# **2. Definitions**
---
추천시스템에서 말하는 '공정하다' 라는 뜻의 정의는 무엇일까?

사실 '공정'이라는 단어는 추상적인 단어이면서도 상대적인 단어이기 때문에 딱 잘라 말하기는 굉장히 어렵다.
그렇기 때문에 추천시스템의 공정성을 연구하는 다양한 논문에서도 각자 다른 방식으로 'fairness'를 해석하고 있다. 다른 domain 에서도 공통적으로 사용하는, fairness의 정ㅇ

<span style="background-color:orange"> 첫번째 </span>, 우선 과정 속 공정성을  중시하는 공정성과 결과의 공정성을 중시하는 공정성이 있다.

- Process Fairness (Procedure justice): 모델이 인종,성별같은 요소를 학습 피처에 넣느냐 안 넣느냐, 혹은 그 외의 학습요소들이 편향되지는 않았는가? 등을 체크

- Outcome Fairness (Distributive justice): 모델을 통과해서 나온 결과물이 특정 인종에게 유리하게 작용하는가? 등을 체크

우리는 outcome 쪽에 더 초점을 둘 것이고, outcome fariness은 다시 target과 concept를 기준으로 나눌 수 있다.

자세한 분류는 아래의 이미지와 같다.

<img width="1071" alt="스크린샷 2024-01-02 오후 3 30 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9bed107c-7895-43e2-8d22-6b0a04327ce3">

<br/>
<span style="background-color:orange"> 두번째 </span>, target을 기준으로 2차 분류가 됐다면 개개인이 모두 공정해야하느냐, 그룹별로 공정성이 보장이 되느냐로 나눌 수 있다.
<br/>
그리고 기존 연구들은 보통 그룹 공정성을 확보하는 식으로 연구를 진행해왔다. 사실 개개인도 어떻게 보면 1인 그룹으로 여길 수도 있기 때문에 큰 개념에서는 group 이 individuals 의 개념을 덮고 있다고 봐도 무방하다.

<span style="background-color:orange"> 세번째 </span>, concept를 기준으로 나눌 수 있다. outcome이 각 개인 혹은 그룹과 어떤 관계를 맺을 수 있는가를 목표로 한다. 이중 CO 와 CA 에 대해서는 연구가 많이 진행된 상태이다.

* CO: 비슷한 사람끼리는 output도 비슷해야 한다 + 서로 다른 그룹 간의 output이 비슷해야 한다.

* CA: 결과값이 특정 능력이나 가치에 정확히 대응되어야 한다고 주장한다.
단, merit(가치)는 그때그때 다르다.

* Counterfactual Fairness: 한 개인의 소속 그룹이 바뀌더라도 그 output이 바뀌어서는 안된다.

* Rawlsian Maximin Fairness: 여러 그룹 들의 outcome value 가 있을 때 가장 worst 한 놈을 극대화 해야한다. 다시 말해 저점을 최대한 끌어올린다는 개념이다.


그러나 각 분류 별로 정의가 여러개 있으므로 자연스레 어떤 정의가 우선시 되어야 하는가? 라는 의문이 따라옴. 대부분의 연구는 single definition을 충족 시키는 법을 다루기 때문에 이부분은 더 탐구여지가 남아있다.

또한 저자는 공정성 Bias, Diversity, and Privacy 등의 조건을 만족하면서도, 너무 얽매이지도 말아야 한다고 언급하고 있다.(공정이라는 개념은 참 어려운 것 같다...)

fairness의 정의가 정리되어 있는 테이블은 다음과 같다.

<img width="1152" alt="스크린샷 2024-01-02 오후 3 37 20" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ee4df7eb-4a14-427e-a1a3-59a4444d81b3">

# **3. Classification of Fairness Issues**
---
공정성을 할당하려고 하는데 어느 범위까지 할당할 것이냐? 에 대한 주제를 다루고 있다.

1. Subject: 주체가 누구냐? 

   <img width="484" alt="스크린샷 2024-01-03 오전 10 34 04" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/fe785d74-e2dd-447e-8926-b2adaab242a5">{: width="34%", height="34%"}

   사람이 공정한 대접을 받아야 하냐, 아이템이 공정한 대접을 받아야 하냐? 아니면 둘다냐? joint 를 고려하는 것이 best겠지만 그렇게 쉬운 일이 아니기 때문에 아직 연구가 많이 없다.

2. Granularity(세분성): 공정성이 개별적이냐 전체적이냐? 

   <img width="481" alt="스크린샷 2024-01-03 오전 10 36 25" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/e88488d7-748e-4a81-9b79-1989964dc7ce">{: width="34%", height="34%"}
   
   단일 unit(추천리스트)이 공정성을 만족해야하느냐, 아니면 누적된 리스트들의 분포가 공정한 분포를 가지느냐로 구분.

   사실 single-list 마다 공정성을 만족시키는 것은 쉽지 않아서 몇몇 기존 연구들은 averaging 기법을 사용하기도 한다.

3. Optimization Object: 모델의 output과 사용자의 reaction 중 어느쪽을 더 중시할거냐?


   <img width="485" alt="스크린샷 2024-01-03 오전 10 37 13" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/16ad348e-7c7b-4533-9da5-eac4960c7d42">{: width="34%", height="34%"}
   
   모델이 사용자에게 제공하는 output이 공정해지느냐, 아니면 추천에 의한 사용자 reaction이 공정하게 나오는 것을 목표로 하는가?

   사람들의 feedback은 변덕이 심하고 예측하기가 매우 까다롭기 때문에 impact-base 가 treatment-base 보다 연구하기 어렵다.

# **4. Metrics**
---
대부분의 연구가 outcome definition 기반 CO or CA view를 다루고 있기 때문에 이 두 가지 위주로 먼저 설명

### *Metrics for CO*

Consistent fairness의 목적은 individual 간의 input이 비슷하면 output이 비슷해야한다는 것이다. 이 사실에 기반해 각 개인 혹은 각 그룹간의 utility inconsistency를 확인할 수 있는 metrics 를 사용한다.

### For 2 or more groups

|Metrics|Subject|Details|
|------------------|---|---|
|Absoloute Difference|both|-|
|KS statistic|user|nonparametric test used to determine the equality of two distributions -> high-order inconsistency를 반영가능|
|rND,rKL, and rRd|item|measure item exposure fairness|
|Pairwise Ranking Accuracy Gap|item|PRAG measures the unfairness of pairwise ranking accuracy|
|Value Unfairness and its variants|user|measure inconsistency in signed prediction error between two user groups|
|Variance||group or individual -level 어디서든 사용가능|
|Min-Max Difference|both|difference between the maximum and the minimum of all allocated utilities.|
|F-statistic of ANOVA||determine any statistically significant differences between the mean values of three or more independent groups|

### For individual fairness
|Metrics|Possible subject|Details|
|---------------|---|---|
|Gini coefficient|both|more closer to 0, more fairer so, 흔히 쓰는 로렌츠곡선으로 식을 이해하면 됨|
|Jain’s index|both|transition from network traffic to fair recommendation|
|Entropy|both|use minus sign here, and the lower, the fairer|
|Min-Max Ratio|both|min utility to max utility|
|Least Misery|both|minimum of all allocated utility|


### *Metrics for CA*

Calibrated fairenss 측정 시에는 그룹의 merit 분포 $p_f(v_{i}) = \frac {Merit(v_{i})} {\sum_{j} Merit(v_{j})} $ 와 utility 분포 $ p(v_{i}) = \frac {f(v_{i})} {\sum_{j} f(v_{j})}$ 를 비교한다.

공정하려면, 그 그룹(개인)이 가지고 있는 merit와 비슷한 utiltiy 분포를 가지고 있어야 한다.

|Metrics|Subject|Brief explain|objective|
|------------------|---|---|---|
|Skew|item, can be applied to user|분포가 어느 한쪽으로 쏠려서는 안된다. 누구는 조금 일하면서 많이 벌고, 누구는 많이 일하지만 조금 버는 것을 지양|maximin + minimax|
|KL-divergence|item, can be applied to user|두 분포의 차이를 계산하는 대표적인 metric|lower|
|NDKL|only item|1 to K까지 의 top-i ranking KL-div의 summation 기반|lower|
|JS-divergence|item, can be applied to user|symmetric version of KL-div|lower|
|Overall Disparity|item, can be applied to user|모든 그룹들 간의 p/p_f 비율 차를 계산한 후 average 계산|lower|
|Generalized Cross entropy|both|두 확률분포가 서로 어떻게 다른가를 측정|higher|
|L1-norm|item, can be applied to user|treat the merit and utility distributions as vectors and then use the L1-norm to calculate the distance|lower|

#### *<span style="color:#FFE6E6">Co 와 CA의 몇몇 metrics들은 서로 interconvertible 하다. </span>*

1. 예를 들어 확률 분포 $ p_f $ 가 flat 한 분포라면, 모든 사람이 가지고 있는 merit가 동일하다는 뜻이다. 따라서 similar individuals -> similar output 이라는 것과 같아져 CO statement로 이해할 수도 있다.
2. CO defintion에서 $f (v)$ 를 $  \frac{p(v)}{p_{f}(v)} $ 로 바꾼다면 CA statement와 동일해진다고 이해할 수 있다. 

### *Metrics for Others*

   요약하면 다음과 같다.

|Metrics|Subject|설명||
|------------------|---|---|---|
|Envy-free Fairness|Group|m-envy-free group num to entire group G 로 계산||
|Envy-free Fairness|General|Envy-degree를 통해 계산|lower|
|Metrics for Counterfactual Fairness(CF)||predict fairness-related attributes based on user embeddings because user embeddings are independetn from fairness-related attributes||
|Metrics for Rawlsian Maximin Fairness (RMF)||utility of the worst case or bottom n%|higher|
|Metrics for Maximin-shared Fairness(MSF)||proportion of individuals satisfying this condition, where the maximin share for every item is a constant value|higher|
|Metrics for Process Fairness(PR)||fairness-related attributes 로부터 독립적인 fair representation을 측정한다.||

# **5. Methods**
---

공정한 추천을 위해서 사용할 수 있는 메소드나 알고리즘은 어떤 것이 있을까?

크게 세 종류로 나눌 수 있다.

1. Data-oriented method: 데이터를 건드려서 불공정성을 해결하는 것
2. Ranking methods: 모델이 공정한 추천을 학습할 수 있도록 만드는 것
3. Re-ranking: 공정성을 위해 output을 건드리는 것

## Data-oriented methods

데이터를 만지는 것은 pipeline 앞부분 수정하기 때문에 컨트롤하기는 쉬울 수 있지만, latter modeling 파트를 거치며 퍼포먼스가 오염될 수 있기 때문에 아직 효용적인 방식이 존재하지 않는다.

- Ekstrand: Re-sampling dataset -> 성능 별로

- Rastegarpanah: data poisoning + add antidote data -> antidote data 수정하는 방향으로 학습 -> 시간소요 너무 큼



## Ranking methods

추천 모델 자체를 수정하거나 optimization target을 변경해 fair representation을 높이는 방식. 데이터 지향 방식과 비교했을 때 더 직관적이다는 장점이 있다. 그렇지만 re-rank 과정에서 공정성이 저하될 수도 있다.

<details>
<summary>Regularization-based methods</summary>

<!-- summary -->
---
   흔히 알고 있는 모델에 regularization term을 주어 제약을 주는 방식.
   기본 Loss function에 fairness와 관련된 loss term을 추가해 

   $L = L_{rec} + \lambda L_{fair} $ 꼴로 나타낼 수 있다.

   Direct 방식과 indirect 두가지 방식 중 골라 쓸 수 있는데, direct 방식은 은 앞서 언급한 fairness evaluation metrics 을 regularization term에 직접 넣는 방식이다. 단, 당연히 미분가능한 것만 넣을 수 있다.

   Indirect 방식은 paper에서 자체적으로 정의한 Loss 를 넣는 방식이다. 주로 indirect 방식이 더 나은 결과를 보였다고 한다.

</details>

<details>
<summary>Adversarial learning-based methods</summary>

<!-- summary -->
---
   Generator와 discriminator 가 경쟁적으로 학습하는 그 방식이다. 한 가지 차이점은 Recommender system에서는 generator 대신 filter module을 둔다는 것이다. Filter module이 unfair information를 가리고, discriminator가 unfair information을 제거한 representation에 대해서 판별을 진행할 때, fair attribute을 구별해내지 못할 때야 공평성이 성립되었다고 볼 수 있다.

   Loss function은 일반적으로 아래와 같다.

   $ min_{R} \space max_{D} \space L(R,D) = L_{R} - \lambda L_{D}$

</details>

<details>
<summary>Reinforcement learning-based methods</summary>

<!-- summary -->
---
   강화학습을 이용한 추천시스템의 공정성 구축은 다른 방법과 비교해서 장기적이고 유동적으로 학습을 할 수 있다는 점이다.

   강화학습에서처럼 agent가 fairness를 학습을 하면 fair reward를 극대화하는 방향으로 action과 state를 계속 update 한다. 그 다음에 critic network 가 이를 평가하는 방식으로, feedback과 update을 반복하다보면 fairness에 수렴한다는 아이디어이다.

   그러나 안정성이 떨어지고 offline-data를 가지고 평가하기는 어렵다는 단점도 있다.

</details>

<details>
<summary>Others</summary>

<!-- summary -->
---
   use transfer learning, use contextual framework, add high var noise to VAE 등등의 방법을 사용 가능.

</details>

## Re-ranking methods

이미 model을 거쳐서 output이 나왔는데 굳이 re-ranking로 재조정을 해야하는가?
> 최근 trend가 바뀌어 사용자가 추천리스트에 대해 negative feedback을 줄 수도 있고, 사용자가 속한 그룹이 최근에 바뀌었을 수도 있기 때문에 re-rank 과정이 필요하다.

<img width="1172" alt="스크린샷 2024-01-03 오후 3 32 54" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/df391c88-4d12-4219-8f26-a2c5472a1e84">

### Slot-wise

Slot-wise 방식은 일반적으로 Greedy-algorithm을 사용하여 처음 주어진 list를 re-rank 한다. 한 번에 한 사용자에게 list를 제공하되, 매순간 fairness score를 최대화하는 방식으로 물건을 선택해 리스트를 채워나간다.
직관적이고, 다루기 쉽다는 장점이 있지만, 쉽게 local optimum에 갇힐 수 있다는 점도 고려해야한다.

### User-wise

리스트에다가 item 하나하나 넣기보다 최적화 알고리즘을 통해 re-rank된 리스트 중 유저에게 가장 최적화된 list를 제공해주는 아이디어.

### Global-wise methods

User-wise 방식과 마찬가지로 최적화 알고리즘을 통해 유저에게 가장 최적화된 list를 제공해주는 방식은 비슷하다. 그러나 다수의 유저에게 동시다발적으로 다수의 리스트를 제공한다는 차이점이 있다. amortized fairness를 중시하는 관점으로서 제공된 리스트 그룹 전체의 분포가 공정한지에 더 초점을 두고있다.

# **6. Datasets**

<img width="681" alt="스크린샷 2024-01-04 오후 2 26 47" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f1c8721c-97bc-4e31-b459-64ec5bb19571">{:width= "90%", :height= "85%"}<br/>
<span style="color:red"> 친절하게 실험에 사용된 데이터셋이 어떤 attribute을 포함하고 있는지 표로 정리 해놓으셨다 </span>
각 데이터셋의 자세한 설명은 생략.

일반적으로 그룹을 나눌 때, 데이터셋의 사용자 성별 혹은 물건 인기도같은 specific 한 특성으로 그룹을 나눈다. <br/>
그렇지만 만약 기준이 될만한 뚜렷한 특성이 없을 경우는 어떻게 할까??
   - <span style="background-color:orange"> 첫번째 </span>, Rawlsian maximin fairness처럼 아예 그룹보다 개인레벨에서 공정성을 따지는 연구를 하거나, 
   - <span style="background-color:orange"> 두번째 </span>, 사용자 history나 item 인기도 등을 고려해 따로 그룹을 분할하는 방식을 선택한다.

# **7. Future Directions**

## 7.1 Definition

사실 '공평'의 정확한 정의도 아직 잡혀있지 않으며 그 많은 정의들끼리 우선순위에서 충돌할 수 있기 때문에 이런 점 위주로 보완해 나갈 수 있지 않을까?

## 7.2 Evaluation
- Fair comparison between different fairness methods
   * 공정성 연구에서의 어느정도 standard 가 있어야 한다. data preprocessing, hyperparameter, metrics 등에서 누군가는 기준을 잡아주어야 한다.

- Dataset for new emerging scenarios
   * 예를 들어, 쇼츠비디오 같은 경우 기존의 데이터셋과는 아예 다른 새로운 형태의 데이터셋이기 때문에 이런 비디오들이 공정성 기준에 부합하는가?

## 7.3 Algorithm Design
- A win-win for fairness and accuracy
   * 정확성과 공정성 사이에 어느정도의 trade-off 가 있을 수 있다. 두 기준이 모두 윈윈하는 알고리즘을 구성해야 할 것임.

- Fairness for both user and item
   * 자명한 사실이지만, 사용자와 아이템 어느쪽도 소외되는 일이 없어야함.
   * joint fairness를 만족시키려면,
      
      1. data-oriented methods 는 one-sided fairness 만 고려하므로 누군가가 연구를 헤줘야할 듯. -> 사실 저자도 별 생각없어서 책임전가하는 느낌임.
      2. 이미 사용되고 있는 Pareto method 등을 이용해 multi-objective learning 을 수행.
      3. 현존하는 re-rank 방식은 non-learnable 한 non-parameteric algorithm을 사용한다. 누군가가 learnable re-ranking algorithms 을 고안해 낸다면 one-sided fairness를 해결할 수 있을 것.

- Fairness beyond accuracy
   * criterion의 기준인 accuracy에만 집착하지말고 만족도 상승과 관련된 다양한 measurement를 사용해보자.

- Causal inference for fairness
   Causal inference in recommendation 이 

   > Causal inference 란? 어떤 사건 또는 조건이 다른 사건에 어떠한 영향을 미치는지를 이해하고 추론하는 통계학과 인과 관계에 관한 분야.

   Causal infernece in recommender system 를 위해서는 아래의 두가지 문제를 해결해야 한다고 한다.
   *  ID와 additional attributes 가 model과 causal 관계를 잘 구축할 수 있도록 그래프를 구성해야 한다. 
   * causal graphs에서 unfair factors 의 영향을 어떻게 줄일 것이냐?

- Fairness with missing data
   * 공정성과 관련된 정보가 누락되거나 조작된 경우 공정성을 향상시킬 수 있는 방안을 모색할 필요가 있다. 몇가지 시도가 있었으나, 아직 여지가 남아있는 부분이다.

- Fairness in a real system
   * 현실세계의 추천시스템은 주로 recall, ranking, and re-ranking 3 phase로 구성되어 있고 현실세계와 복잡한 상호작용을 끊임없이 한다는 높은 자유도를 가진다. 따라서 각 단계가 공정성을 보장할 수 있도록 시스템을 만드는 것이 중요하다.

## 7.4 Explanation
공정성을 달성하려면 불공정성이 왜 발생하는 지를 아는 것이 중요하겠지? 이는 앞서 언급한 causal inference 와도 연결되는 개념으로 꽤나 중요하다. 그러나 아직 이런 근본적인 문제를 다루려는 연구가 활발하지 않다.





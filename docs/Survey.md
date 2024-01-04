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

### Slot-wise

- Group recommendations

Serbos: greed-based approach를 사용. 매 순간, 함수 $f_G(P,i)$ 를 최대화하는 item을 list에 추가하는 방식

Lin: gredd-based approach는 동일하지만 Pareto efficiency 에 기반한 fairness-recommendation performance 둘 다 동시에 증가시킬 수 있는 

$ \lambda SW(g,I) + (1-\lambda) F(g,I)$ 

를 증가시키는 방식을 사용

Sacharidis: 더 나아가 Pareto-optimal items candidate 를 찾은 뒤, linear aggregation 을 통해 순위 점수를 생성해 항목이 Top-K에 랭크될 확률을 추정한다.

Kaya: 간단히 말해 fairness-recommendation performance 둘 중 어느것도 희생하지 않는 선에서 fixed-length recommendation lists 를 벗어나 fairness of different positions simultaneously를 고려한다.
-> 그대로 옮겨 적긴 했는데 무슨 소린지 잘 이해가 안 됨.

- general recommendation scenario

기존 방법은 두 가지 주요 관점에서 공정성을 도입하는데, 하나는 공정성 constrint를 만족시키면서 utility 을 극대화하는 것이고, 다른 하나는 공정성과 utility(효용)을 함께 최적화하는 것입니다.
-> ??? 무슨 말장난 하는 것도 아니고...

전자: 최대한의 fairness constraint를 만족시켜야한다는 관점

후자: 공정성과 utility 간의 trade-off 가 존재

Zehlike: FA*IR 를 제시.

FA*IR는 무엇이냐?: 두 그룹에 할당된 item queue가 2개 있다고 하자. 하나는 protect 큐이고 다른 하나는 unprotected 버전이다. 이때 각 queue는 relevance 순으로 나열되어 있다. 만약 두 그룹간의 공정성이 충족되지 못한다면 protected queue에서 가장 상관성이 높은 아이템을 뽑아서 리스트에 추가한다. 공정성을 만족한다면 그냥 전체에서 상관성이 높은 아이템을 뽑아서 리스트에 추가한다.

Geyik: FA*IR을 3개 이상의 그룹에 사용가능하도록 만듦.


tradeoff between fairness and recommendation performance(=utility)를 연구한 논문은 무엇이 있을까? 일단 기본 form은 아래와 같다.

$ i^{*} = argmax_{i \in (R-C)} \space \lambda P(u,i) + (1-\lambda) F(u,i,C) $ 

P는 performance, F는 fairness 관련 점수이다. 참고로 F는 논문마다 다르다.

매번 리스트에 어떤 item을 추가할지 선택할 때(slot-wise) 위의 function을 최대화하는 $i^{*}$ 를 선택한다.

Steck: 공정성 점수는 사용자 u의 이력 내 다양한 항목 그룹에 대한 분포와 추천 목록의 항목 그룹에 대한 분포 간의 KL-divergence로 정의된다.

Karako and Manggala: Maximal Marginal Relevance 라는 것을 도입해, 새 아이템 i가 두 그룹 간의 임베딩 차이에 어떻게 기여하는지를 item fairness score의 기준으로 삼았다.

Liu: 개인화된 re-ranking method를 제시한다. 사용자의 다양한 취향을 존중? 해준다는 것을 이해.

Sonboli: Liu의 아이디어에 기반해 item attribute에 따라 또 취향은 천차만별이 된다. 따라서 personalized fairness score 를 고려할 때 multiple item attributes을 고려해 주었고, better trade-off 를 보였다고 한다.

- dynamic ranking scenario

Morik: proportional controller에 기반한 방식을 제안했다. recommendation per- formance and fairness를 결합하면 linear startegy를 사용햇고 ranking 의 수가 충분히 크다면 fairness를 보장할 수 있다는 것을 증명했다.

$\sigma_{\tau} = argmax_{d \in D} \space (\hat{R}(d \mid x) + \lambda err_{tau}(d)) $

Yang and Ai: marginal fairness를 고려한다. 한 item을 리스트에 추가할 때 마다 공정성이 얼마나 커지느냐를 고려. marginal fairness를 최대화하는 그룹은 가장 낮은 utility/merit ratio을 가지고 있다. -> 그만큼 공정하시다는 것.

요약하면, Slot-wise 방식은 한 사용자에게 제시할 list의 공정성을 최대화하는 방향으로 item by item으로 선택해 리스트를 채워나간다. 
굉장히 직관적이고, 다루기 쉽지만 local optimum에 갇힐 수 있다는 점도 고려해야 되겠다.

### User-wise

리스트에다가 item 깔짝깔짝 넣기보다 유저에게 최적화된 list를 통째로 선택해주자! 라는 취지로 사용한다. 여기서 중요한 점은 re-ranking problem을 integer programming으로 바꿀 수 았다는 점이다.

그렇다면 integer programming 은 무엇이냐?: GPT에 따르면,

 "정수계획법"은 계획을 세울 때 어떤 자원을 어떻게 할당할지를 결정하는 수학적인 방법 중 하나입니다. 주로 선형 프로그래밍이나 최적화 문제를 해결하는 데 사용됩니다. 정수계획법은 이런 문제를 해결할 때 변수들이 정수값만을 가질 수 있도록 제약을 두는 방법입니다.

라고한다. 자세한 건 더 알아봐야겠지만 변수를 정수로 제한한 상태로 최적화를 구상한다는 의미로 이해했다.

#### - group recommendation scenario

- Lin: slot-wise 에서 사용했던 objective function은 그대로 사용하되, 
'''
$ \lambda SW(g,I) + (1-\lambda) F(g,I) \space s.t \space \sum_{i}X_{i} = K, X_{i} \in 0,1 $ 
'''

라는 정수 제약조건을 걸었다. 

사실 Np-hard에 속하는 문제라고, X를 0부터 1사이의 확률로 바꾸어서 푼다고 한단다.

#### general recommendation scenario

- Biega
   - ILP를 사용해 어느정도 constraint를 걸어둠으로써 추천시스템의 성능저하를 막는다.

- Singh and Joachims
   - 문제를 선형 프로그래밍 문제로 공식화하고 확률적 순위 관점에서 해결한다.

- Mehrotra:

User-wise re-rank 방식은 사용자에게 개별적으로 최적화가 진행된 리스트를 추천해준다. slot-wise에 비하면 local-optimum에서 벗어날 수 있지만 시간이 좀 더 걸린다는 차이점이 있다. Global-wise와 비교하면 각 개인별로 리스트가 제공되지만 여전히 suboptimal의 가능성은 여전하다. 

### Global-wise methods

앞의 두 방식은 매번 각 user마다 할당되는 single 리스트를 생성하지만 global-wise는 multiple lists 를 만든다는 차이점이 있다.

핵심 아이디어: global-optimum 에 더 다가갈 수 있겠지?? 여기서는 Mathematical programming 을 주 framework로 사용한다. variable을 0,1 로 제한해 사용자에게 추천하냐 마냐로 결정한다.


Li: integer programming에 기반한 solving 방식을 제안.

Fu: Li의 방식 +  knowledge graphs 이용

Mansoury:  

Sürer: 




joint fairness 방식 


Patro: phase 1 에서는 노출 제약을 두고 사용자에게 greedy-recommend, phase 2에서는 제약을 없애고 추천이 빈약한 아이들에게 관련성 높은 물건들을 추천해준다.
->  envy-free fairness for users and maximin-shared fairness for items. 을 동시에 달성

Wu:  improve item fairness at the group level and user fairness at the individual level 에서 Pareto와의 차이점.
뭔소리야??

요약하면, 전역적 영향을 고려하고 매번 여러 목록의 순위를 다시 매깁니다.



# **6. DATASETS FOR FAIRNESS RECOMMENDATION STUDY**

### <span style="color:red"> 정말 친절하게도 어떤 데이터셋이 어떤 attribute을 포함하고 있는지 정리 해놓으셨단다!! </span>


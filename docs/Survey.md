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
~~뻥 안치고 처음 읽어보는 Review Paper의 양에 읽다가 죽을번 했다...~~

해당 논문은 현재까지 진행된 추천시스템의 공정성 연구를 소개하고 앞으로의 방향성을 잡아 주는 논문이라고 볼 수 있다.


추천시스템은 당연히 그 목적 자체가 Uitility 의 최대화라고 볼 수 있지만, 최근 들어 사용자의 background 에 관계없이 공정한 추천을 제시해야한다는 의견이 강조되고 있다. 따라서 fairness-aware 추천시스템, 즉 공정성을 반영한 추천시스템은 매우 중요하다.
> Utility란? 클릭률 등 모델의 퍼포먼스에 대한 사용자의 만족도? 정도로 해석.

> Fairness-related attributes란? gender, age, and race 등 차별의 원인이 될 수 있는 특성들을 의미

<br/>
Survey Paper 에서 집중하고 있는 포인트는 다음과 같다.

1. 추천시스템에서 fairness 라는 것의 정확한 정의가 무엇이냐?

2. fairness 를 어떤 관점으로 분류할 수 있을까?

3. fairness를 측정할 metric은 무엇이 있을까?

5. 불공정성을 해소하기 위한 method은 어떤 것이 있는가?

6. 실험을 위한 데이터셋은 무엇이 있는가?

7. 앞으로의 연구방향성은 무엇이 있는가?

  
# **2. Definitions of Fairness in Recommendation**
---
추천시스템에서 말하는 '공정하다' 라는 뜻의 정의는 무엇일까?

사실 '공정'이라는 단어는 추상적인 단어이면서도 상대적인 단어이기 때문에 딱 잘라 말하기는 굉장히 어렵다.
그렇기 때문에 추천시스템의 공정성을 연구하는 다양한 논문에서도 각자 다른 방식으로 'fairness'를 해석하고 있다.

1. 우선 과정 속 공정성을  중시하는 공정성과 결과의 공정성을 중시하는 공정성이 있다.
- Process Fairness (Procedure justice): 모델이 인종,성별같은 요소를 학습 피처에 넣느냐 안 넣느냐, 혹은 그 외의 학습요소들이 편향되지는 않았는가? 등을 체크

- Outcome Fairness (Distributive justice): 모델을 통과해서 나온 결과물이 특정 인종에게 유리하게 작용하는가? 등을 체크

우리는 outcome 쪽에 더 초점을 둘 것이고, outcome fariness은 다시 target과 concept를 기준으로 나눌 수 있다.

자세한 분류는 아래의 이미지와 같다.

<img width="1071" alt="스크린샷 2024-01-02 오후 3 30 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9bed107c-7895-43e2-8d22-6b0a04327ce3">

<br/>
2. target을 기준으로 2차적으로 나누어 졌다면, 개개인이 모두 공정해야하느냐, 그룹별로 공정성이 보장이 되느냐로 나눌 수 있다.
<br/>
그리고 기존 연구들은 보통 그룹 공정성을 확보하는 식으로 연구를 진행해왔다. 사실 개개인도 어떻게 보면 1인 그룹이라고 볼 수도 있기때문에 크게 신경쓰지 않아도 될 것같다.

3. concept를 기준으로 나눌 수 있다. outcome이 각 개인 혹은 그룹과 어떤 관계를 맺을 수 있는가를 목표로 한다. 이중 CO 와 CA 가 선행연구가 꽤 있던 부분이다.

* CO: input 이 비슷하면 output도 비슷해야 한다.

* CA: 결과값이 특정 능력이나 가치에 정확히 대응되어야 한다고 주장한다.
단, merit는 그때그때 다르다.

* Rawlsian Maximin Fairness: 여러 그룹 들의 outcome value 가 있을 때 가장 worst 한 놈을 극대화 해야한다. 다시 말해 저점을 최대한 끌어올린다는 개념이다.

그러나 정의가 여러개 있으므로 자연스레 어떤 정의가 우선시 되어야 하는가? 라는 의문이 따라옴. 대부분의 연구는 multi 보다 single definition만을 다루기 때문에 이부분은 더 탐구여지가 남아있다.

또한 저자는 공정성 Bias, Diversity, and Privacy 등의 조건을 만족하면서도, 너무 얽매이지도 말아야 한다고 언급하고 있다.(공정이라는 개념은 참 어려운 것 같다...)

fairness의 정의가 정리되어 있는 테이블은 다음과 같다.

<img width="1152" alt="스크린샷 2024-01-02 오후 3 37 20" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ee4df7eb-4a14-427e-a1a3-59a4444d81b3">

# **3. VIEWS OF FAIRNESS IN RECOMMENDATION**
---
추천시스템을 어떤 관점으로 분류할 수 있을까?

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

#### - Group recommendations

- Serbos 
   - greed-based approach를 사용. 매 순간, 함수 $f_G(P,i)$ 를 최대화하는 item을 list에 추가하는 방식

- Lin
   - gredd-based approach는 동일하지만 Pareto efficiency 에 기반한 fairness-recommendation performance 둘 다 동시에 증가시킬 수 있는 

      $ \lambda SW(g,I) + (1-\lambda) F(g,I)$ 

      를 증가시키는 방식을 사용

- Sacharidis
   - Lin보다 더 나아가 Pareto-optimal items candidate 를 찾은 뒤, linear aggregation 을 통해 순위 점수를 생성해 항목이 Top-K에 랭크될 확률을 추정한다.

- Kaya
   - fairness-recommendation performance 둘 중 어느것도 희생하지 않는 선에서 fixed-length recommendation lists 를 벗어나 fairness of different positions simultaneously를 고려. -> 그대로 옮겨 적긴 했는데 무슨 소린지 잘 이해가 안 됨...

#### - general recommendation scenario

두가지 관점이 존재.

첫번째는, 

How to maximize utility satisfying fairness constraints??

즉, performance 적인 부분은 어느정도 희생하되, 최대한의 fairness constraint를 만족시켜야한다는 관점이다.

- Zehlike: 
   - FA*IR 를 제시.

   >FA*IR 이란? 
   두 그룹에 할당된 item queue가 2개 있다고 하자. 하나는 protect 큐이고 다른 하나는 unprotected 버전이다. 이때 각 queue는 relevance 순으로 나열되어 있다. 만약 두 그룹간의 공정성이 충족되지 못한다면 protected queue에서 가장 상관성이 높은 아이템을 뽑아서 리스트에 추가한다. 공정성을 만족한다면 그냥 전체에서 상관성이 높은 아이템을 뽑아서 리스트에 추가한다.

- Geyik: FA*IR을 3개 이상의 그룹에 사용가능하도록 만듦.


두번째는 how to optimize the tradeoff between fairness and recommendation performance(=utility)?

즉,공정성과 utility 간의 trade-off 를 최적화하는 관점이다.

P는 performance, F는 fairness 관련 점수이다. 참고로 F는 논문마다 다르다.
일단 기본 form은 아래와 같다.

$ i^{*} = argmax_{i \in (R-C)} \space \lambda P(u,i) + (1-\lambda) F(u,i,C) $ 

매번 리스트에 어떤 item을 추가할지 선택할 때(slot-wise) 위의 function을 최대화하는 $i^{*}$ 를 선택한다.

- Steck
   - 공정성 점수는 사용자 u의 이력 내 다양한 항목 그룹에 대한 분포와 추천 목록의 항목 그룹에 대한 분포 간의 KL-divergence로 정의된다.

- Karako and Manggala 
   - Maximal Marginal Relevance 라는 것을 도입해, 새 아이템 i가 두 그룹 간의 임베딩 차이에 어떻게 기여하는지를 item fairness score의 기준으로 삼았다.

- Liu 
   - 개인화된 re-ranking method를 제시한다. 사용자의 다양한 취향을 존중? 해준다는 것을 이해.

- Sonboli
   - Liu의 아이디어에 기반해 item attribute에 따라 또 취향은 천차만별이 된다. 따라서 personalized fairness score 를 고려할 때 multiple item attributes을 고려해 주었고, better trade-off 를 보였다고 한다.

#### - dynamic ranking scenario

- Morik 
   - proportional controller에 기반한 방식을 제안했다. recommendation per- formance and fairness를 결합하면 linear startegy를 사용햇고 ranking 의 수가 충분히 크다면 fairness를 보장할 수 있다는 것을 증명했다.

      $\sigma_{\tau} = argmax_{d \in D} \space (\hat{R}(d \mid x) + \lambda err_{tau}(d)) $

- Yang and Ai 
   - marginal fairness를 고려한다. 한 item을 리스트에 추가할 때 마다 공정성이 얼마나 커지느냐를 고려. marginal fairness를 최대화하는 그룹은 가장 낮은 utility/merit ratio을 가지고 있다. -> 그만큼 공정하시다는 것.

요약하면, Slot-wise 방식은 한 사용자에게 제시할 list의 공정성을 최대화하는 방향으로 item by item으로 선택해 리스트를 채워나간다. 
굉장히 직관적이고, 다루기 쉽지만 local optimum에 갇힐 수 있다는 점도 고려해야 되겠다.

### User-wise

리스트에다가 item 깔짝깔짝 넣기보다 유저에게 최적화된 list를 통째로 선택해주자! 라는 취지로 사용한다. 여기서 중요한 점은 re-ranking problem을 integer programming으로 바꿀 수 았다는 점이다.

그렇다면 integer programming 은 무엇이냐?: GPT에 따르면,

> "정수계획법"은 계획을 세울 때 어떤 자원을 어떻게 할당할지를 결정하는 수학적인 방법 중 하나입니다. 주로 선형 프로그래밍이나 최적화 문제를 해결하는 데 사용됩니다. 정수계획법은 이런 문제를 해결할 때 변수들이 정수값만을 가질 수 있도록 제약을 두는 방법입니다.

라고한다. <br/> 
자세한 건 더 알아봐야겠지만 변수를 정수로 제한한 상태로 최적화를 구상한다는 의미로 이해했다.

#### - group recommendation scenario

- Lin
   - slot-wise 에서 사용했던 objective function은 그대로 사용하되,
   
      $ \lambda SW(g,I) + (1-\lambda) F(g,I) \\ s.t \space \sum_{i}X_{i} = K, X_{i} \in 0,1 $ 
   
      라는 정수 제약조건을 걸었다. 

      사실 조건을 만족하면서 식을 최대화하는 것은 Np-hard에 속하는 문제라고 하며, X를 0부터 1사이의 확률로 바꾸어서 푼다고 한단다.

#### - general recommendation scenario

- Biega
   - ILP를 사용해 어느정도 constraint를 걸어둠으로써 추천시스템의 성능저하를 막는다.

- Singh and Joachims
   - 문제를 선형 프로그래밍 문제로 공식화하고 확률적 순위 관점에서 해결한다.

- Mehrotra:

User-wise re-rank 방식은 사용자에게 개별적으로 최적화가 진행된 리스트를 추천해준다. slot-wise에 비하면 local-optimum에서 벗어날 수 있지만 시간이 좀 더 걸린다는 차이점이 있다. Global-wise와 비교하면 각 개인별로 리스트가 제공되지만 여전히 suboptimal의 가능성은 여전하다. 

### Global-wise methods

앞의 두 방식은 매번 각 user마다 할당되는 single 리스트를 생성하지만 global-wise는 multiple lists 를 만든다는 차이점이 있다. 이렇게 하면 global-optimum 에 더 다가갈 수 있겠지?? 라는 생각으로 접근한다.

핵심 아이디어:  여기서는 Mathematical programming 을 주 framework로 사용한다. variable을 0,1 로 제한해 사용자에게 추천하냐 마냐로 결정한다.


One side fairness
----
*Mathmatical Programming based*

- User fairness
   - Li
      - integer programming에 기반한 solving 방식을 제안.

   - Fu
      - Li의 방식 +  knowledge graphs 이용



- for item fairness
   - Sürer 

*Other than programming-based*

   - Mansoury
      - !!

   - Zhu
      - !!

Joint fairness
----
   - Patro
      - phase 1 에서는 노출 제약을 두고 사용자에게 greedy-recommend, phase 2에서는 제약을 없애고 추천이 빈약한 아이들에게 관련성 높은 물건들을 추천해준다. ->  envy-free fairness for users and maximin-shared fairness for items. 을 동시에 달성
   
   - Wu
     - Wu:  improve item fairness at the group level and user fairness at the individual level 에서 Pareto와의 차이점. -> 뭔소리야??

요약하면, 전역적 영향을 고려하고 매번 여러 목록의 순위를 다시 매긴다.

# **6. DATASETS FOR FAIRNESS RECOMMENDATION STUDY**

#### <span style="color:red"> 정말 친절하게도 어떤 데이터셋이 어떤 attribute을 포함하고 있는지 표로 정리 해놓으셨단다!! </span>

<img width="681" alt="스크린샷 2024-01-04 오후 2 26 47" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f1c8721c-97bc-4e31-b459-64ec5bb19571">{:width= "90%", :height= "85%"}<br/>
각 데이터셋의 자세한 설명은 생략.

일반적으로 그룹을 나눌 때, 데이터셋의 사용자 성별 혹은 물건 인기도같은 specific 한 특성으로 그룹을 나눈다. 그렇지만 기준이 될만한 뚜렷한 특성이 없을 경우 <br/>첫번째, Rawlsian maximin fairness처럼 아예 그룹보다 개인레벨에서 공정성을 따지는 연구를 하거나, <br/>두번째, 사용자 history나 item 인기도 등을 고려해 따로 그룹을 분할하는 방식을 선택한다.

# **7. FUTURE DIRECTIONS**

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
   * causal 하다란?
   > 나도 잘 모름

   * 
- Fairness with missing data
   * 공정성과 관련된 정보가 누락되거나 조작된 경우 공정성을 향상시킬 수 있는 방안을 모색할 필요가 있다. 몇가지 시도가 있었으나, 아직 여지가 남아있는 부분이다.

- Fairness in a real system
   * 현실세계의 추천시스템은 주로 recall, ranking, and re-ranking 3 phase로 구성되어 있고 현실세계와 복잡한 상호작용을 끊임없이 한다는 높은 자유도를 가진다. 따라서 각 단계가 공정성을 보장할 수 있도록 시스템을 만드는 것이 중요하다.

## 7.4 Explanation
공정성을 달성하려면 불공정성이 왜 발생하는 지를 아는 것이 중요하겠지? 그러나 아직 이런 근본적인 문제를 다루려는 연구가 활발하지 않다...

# **8. CONCLUSION**
...



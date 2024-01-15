---
layout: page
title: Equity of Attention:Amortizing Individual Fairness in Rankings
description: >

use_math: true
sitemap: false
---
# **0. Before we start,**
---
- Position bias란?
    > 기존의 연구들에 의하면, 사람들이 추천 시스템으로 부터 ranked-list 를 제공받은 경우 아이템과 유저의 상관관계는 rank가 낮아질 수록 linear 하게 감소하지만, 아이템이 사용자로 부터 받는 관심은 exponentional 하게 감소한다. 이로 부터 발생하는 편향을 position bias 라고 부르기로 한다.

- Higher than rank k
    > 혼선을 방지하기 위해서 'higher than rank k' 라고 함은 1 부터 k-1 위 까지의 subject들을 의미함을 먼저 짚고 넘어간다.

- Score rating is proportional to Relevance 
    > Rating 이 Relevance를 잘 반영한다는 가정하에 설명을 하겠다. 그리고 여기서 relevance가 merit, 즉 subject(item)의 worthiness를 결정한다고 가정한다.

# **1. Introduction**
---

item이 가지고 있는 merit 에 비례해서 outcome value가 결정되어야하는 calibrated fairness definition의 관점에서는 position bias가 발생은 사회적 자원인 Searcher's attention을 제대로 분배하지 못한다는 불공정성을 반영한다.
item이 relevance에 따른 마땅히 받아야할 attention을 사용자로부터 온전히 받지 못했기 때문이다.

<p>
<img width="320" alt="스크린샷 2024-01-14 오전 12 19 55" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ef37d34b-3de0-4083-80aa-8aed5bf054e9">

<img width="320" alt="image" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/45d4459e-f904-4ec2-92e1-757c4fd7c86b">
</p>

위의 이미지는 실험에서 사용한 Airbnb의 attention 과 relevance 의 top-10 분포이다. 사실 제네바 데이터들은 top 300 까지 거의 동일한 relevance를 가지고 있지만 대부분의 사용자 attention은 top 5 정도까지가 독식하는 것을 확인할 수 있다. 

논문의 제목인 equity은 equality 와는 다른 개념이다. equity는 한국말로 '형평성' 이라는 뜻으로, equity of attention은 가지고 있는 relevance 만큼의 attention을 온전히 할당 받을 때 fair하다는 의미이다.

그렇다면 어떻게 공평성을 달성할 수 있다는 말인가??

Paper 에서는 amortized fairness를 사용한 individual item의 equity를 충당할 수 있도록 re-ranking 을 사용한다.

무슨 말이냐 하면, 

single ranking list에서는 이미 position bias 가 존재하므로 랭킹 리스트를 1,...,m 번째까지 shuffle 하고 m 번째 까지 누적된 attention 과 relevance를 비교하겠다는 의미이다. 그리고 여기서의 shuffle은 re-ranking 이라고 말하며, Integer Linear Programming 을 사용해 최적화된 re-ranked list를 제공한다.(우리가 직접 최적화를 진행할 필요는 없고, gurobi라는 프로그램을 사용한다.)

# **2. Equity of attention Fairness**
---

논문에서 언급한 fairness의 기준인 Calibrated fairness에 따르면 item이 relevance 에 비례하게 attention을 챙겨야 한다. 그렇지만 position bias 때문에 한 single list에서는 위의 식을 만족시키는 불가능하다. 따라서 amoritzed definition을 사용하여 다시 정의한다면,
<center>
<img width="200" alt="스크린샷 2024-01-15 오후 4 40 49" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/6d3c0541-8c59-4947-83f8-ee7be6a9e19a">
</center>

위의 정의를 만족하기 위해서는 매 iteration 별로 ranking 순서를 변경해야한다. 그래서 만약 이번에 attention을 제대로 못 받았다면 돌아오는 다음 turn에 부족한 attention을 보충받을 수 있기 때문이다.

# **3. Ranking with Equity-of-attention**
---
### 3.1 Measuring (un)fairness

각 item 별로 Cumulative Attention과 Relevance의 L1-norm을 계산하고 모든 item에 대해서 더한다.

$unfairness(\rho_1, \rho_2, ..., \rho_{m}) = \sum_{i=1}^{n}\left\vert A_i - R_i \right\vert$ 

### 3.2 Measuring ranking quality

DCG는 간단히 말하면 아이템 순위에 따라 차등적인 gain을 부여해 top-k 개의 rank에서 관련성이 높은 item이 더 높은 순위를 차지할 때 더 높은 점수를 얻을 수 있는 평가지표이다. IDCG는 가장 이상적으로 item이 배치되었을 때 얻을 수 있는 DCG 점수이다. 그리고 NDCG는 DCG/IDCG이다.<br/>
자세한 설명은 아래의 출처를 참고.

그러나 우리는 재배치된 아이템 리스트가 정답이냐 아니냐가 중요한 것이 아니라 원래의 아이템 랭킹 리스트보다 얼마나 더 다양해졌냐를 알고 싶기때문에 원래의 순위 $\rho$ 와 re-ranked 순위 $\rho^{*}$ 의 DCG 를 사용한 새로운 NDCG를 사용한다.

<center>
<img width="320" alt="스크린샷 2024-01-15 오후 4 55 59" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/7a2c93b1-6bc3-4edb-9ead-416b1e3682b7">
</center>

[출처: https://data-scient2st.tistory.com/193]

### An ILP-based fair ranking mechanism and trade-offs

이 매커니즘의 최대약점이자, 최적화 프로그램을 사용하는 이유는 fairness와 quality 간의 tradeoff 가 있다는 것이다. 따라서 hyperparameter 로 설정한 최소한의 ranking quality를 만족하는 선에서 unfairness를 최소화 하는 방향으로 빌드업한다.

다음과 같이 가정하자.
- 1 부터 $l-1$ 번째 까지 ranking이 끝났고, 그에 따른  cumulative attention $A^{l-1}_i$ 와 cumulative relevance $R^{l-1}_i$ 가 정의되어 있다. 
- 현재 $l$ 번째 ranking list를 re-rank 하는 중이다.
- IDCG는 re-rank 전의 current ranking의 DCG@k(ρ) 이다. 

<img width="504" alt="스크린샷 2024-01-14 오전 2 08 16" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/069d34f9-38fb-4503-a63a-0d3b88d8f501">

현재(누적x) 재배치된 X의 relevance가 가장 이상적인 배치의 DCG값의 최소한 $\theta$ % 는 만족해야한다.

#### ILP with candidate pre-filtering 

그런데 그 수많은 item 들에 대해서 re-rank를 해주는 것은 매우 비효율적일 것이다. 따라서 매 iteration 마다 re-rank하기 전에 그럴 싸한 candidate 들을 효율적으로 추려내줄 수 있는 방법이 없을까?

- 첫번째로 떠오르는 방법은 당연히 top-relevance item n 개를 뽑는 방식이다. 물론 ranking quality는 확실하게 보장되지만 승자독식 구조에서 이 방식으로 fairness 를 향상시킬 수 있을지는 의문이다. 예를 들어 relevance 가 거의 uniform에 가까운 분포라면 n+1 위부터는 너무 손해잖아.

-  두번째로 떠오르는 방식은 relevance score에 비해서 attention을 적게 받은 아이템 n 개를 뽑자는 방식이다. 공평성을 확실하게 높일 수는 있지만, 아다리가 잘 안맞아서 ILP가 제한 조건을 어기지 않는 이상 solution을 못 찾을 수도 있다는 문제가 발생.(relevance가 낮은 아이템을 최상위 순위에 놓게 되면 당연히 ranking quality는 급격하게 저하될 것이니)

그렇다면 두가지 방식을 혼재해서 최악의 상황을 모면 해야한다. </br>
k 개는 top-k high relevance score를 가지는 애들 먼저 뽑고, 나머지 t-k 개는 A_i − (R_i + r_i ) 가 가장 낮은 애들 중에서 뽑는다.
이렇게 하면 최악의 경우(attention을 낮게 받은 아이템 중 그 어떤 것들도 top-k rank에 놓아줄 수 없는 경우)가 왔을 때, top relevance 를 가지는 k개를 top-k rank에 올려놓을 수 있다.

## 4. Experiments
---
- 왜 relevance가 linear 한 선으로 표기되는 것인가?
    > fairness는 무시한 채 계속 같은 ranking state를 유지하므로 일정하게 unfairness가 쌓이기 때문이다.

- 왜 그래프에서는 $NDCG>=\theta$ 표기?
    > 어차피 식 (3) 에서 오른쪽 IDCG를 왼쪽으로 넘기면 그게 $NDCG>= \theta$ 이므로 같은 표기라는 것을 알 수 있다.

<img width="1297" alt="스크린샷 2024-01-15 오후 5 29 05" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f1bd9ce4-b21c-4fa5-9786-35fe888a79d1">

결론만 얘기하자면, 

- Uber 와 같이 1등이 모든 attention을 가져가는 경우는 Figure 3,4,5 를 보면된다. Uniform relevance 가 아니면 완전히 unfairness를 해결하기는 어려울 수도 있다. 

- Figure 6 혹은 7 이 Geometric attention을 가진 real-world의 상황과 가장 비슷한 환경에서 unfairness를 최소화한 것이라고 해석할 수 있다. 특히 Figure 8처럼 attention과 relevance의 분포가 비슷하다면, 높은 constraint 에도 unfairness를 줄일 수 있었다.



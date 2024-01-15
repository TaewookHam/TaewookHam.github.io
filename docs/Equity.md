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
<img width="440" alt="스크린샷 2024-01-14 오전 12 19 55" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ef37d34b-3de0-4083-80aa-8aed5bf054e9">

<img width="440" alt="image" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/45d4459e-f904-4ec2-92e1-757c4fd7c86b">
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
<img width="302" alt="스크린샷 2024-01-15 오후 4 40 49" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/6d3c0541-8c59-4947-83f8-ee7be6a9e19a">
{:width= "50%", height= "50%"}
</center>
위의 정의를 만족하기 위해서는 매 iteration 별로 ranking 순서를 변경해야한다. 그래서 만약 이번에 attention을 제대로 못 받았다면 돌아오는 다음 turn에 부족한 attention을 보충받을 수 있기 때문이다.

# **3. Ranking with Equity-of-attention**
---
### 3.1 Measuring (un)fairness

각 item 별로 Cumulative Attention과 Relevance의 L1-norm을 계산하고 모든 item에 대해서 더한다.

$unfairness(\rho_1, \rho_2, ..., \rho_{m}) = \sum_{i=1}^{n}\left\vert A_i - R_i \right\vert$ 

### 3.2 Measuring ranking quality
DCG를 사용해 원래의 순위 $\rho$ 와 re-ranked 순위 $\rho^{*}$ 의 DCG 간 NDCG를 metric으로 사용.
잘 모르겠으면 아래의 출처를 확인
[출처: https://data-scient2st.tistory.com/193]

### 3.3 Optimizing fairness-quality tradeoffs

fairness와 quality 의 tradeoff 가 있다.(no free lunch!!)
ranking quality에 constraint를 걸고 unfairness를 최소화 하는 방향으로 빌드업.

### 3.4 An ILP-based fair ranking mechanism

$l$ 번째 ranking list를 re-rank 하는 과정에 있다고 가정하면,


<img width="504" alt="스크린샷 2024-01-14 오전 2 08 16" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/069d34f9-38fb-4503-a63a-0d3b88d8f501">

여기서 constraint 의 의미는 

현재(누적x) 재배치된 X의 relevance가 가장 이상적인 배치의 DCG값의 최소한 $\theta$ % 는 만족해야한다.

그럼 여기서 드는 의문점 cumulative A_i와 R_i 를 넣어둘 리스트? 혹은 딕셔너리 같은 것이 있는가??

#### ILP with candidate pre-filtering 

모든 후보군들에 대해서 re-rank를 해주는 것은 매우 비효율적이므로
pre-filtered 된 subset들에 대해서만 re-rank 해준다. 다양한 방법사용가능.
그러나 문제가 있는데 첫번째는 만약 top-relevance 만을 가지고 pre-filtering 을 할 경우, ranking quality는 보장되지만 fairness gain을 별로다. 예를 들어 relevance 가 거의 uniform에 가까운 분포라면 잘린 애들이 너무 손해보는 구조...
두번째 문제는 앞에서 minimize 해야하는 목적함수로 먼저 filtering 을 해주면, ILP가 제한 조건을 아예 벗어나지 않고서는 solution을 못 찾을 수도 있다는 문제가 발생.

해결책으로 l번째 ranking에서 top-k high relevance score를 가지는 애들 먼저 뽑고, 나머지 t-k 개는 A_i − (R_i + r_i ) 가 가장 낮은 애들 중에서 뽑는다.

## 4. Experiments
---
여기서 궁금한 점은 왜 relevance가 linear 한 선으로 표기되는 것이냐?
그에 대한 답은 "Unfairness for this method always increases linearly by a constant factor incurred by the single ranking"

두번째 궁금한 점은 그래프에서는 왜 또 NDCG EGR?
차피 식(3) 에서 오른쪽 IDCG를 왼쪽으로 넘기면 그게 NDCG>= \theta임.

Figure 3 같은 경우에는 cycle로 나오는 것은 이해함.
처음에는 unfairness 0으로 시작했다가 재배치 들어가면서 1등에 오르는 애들은 하나인데 제대로 attention 못 받는 애들은 엄청나게 많음 그렇기 때문에 unfairness가 증가하다가 반정도 1등 돌아가면서 해보면 그제서야 unfairness가 줄어들기 시작 그리고 n번째 되면 unfairness를 모두 없앨 수 있다. 

Figure 4 같은 경우 fairness 올리려면 무조건 quality loss 가 있어야 함.
왜냐하면 relevance가 tie인 애들이 없어서 재배치 한다. ==  무조건 loss가 발생한다 임
해결책이 아니라 그냥 그렇다~ 이거잖아?

Figure 5 왜???가 안 써져있음... 그래서 내 생각을 적어보자면 relevance 분포가 exponential 하기 때문에 조금만 re-rank를 해도 loss 가 매우 커짐 그래서 높은 \theta를 충족시켜줄 수 없음-> loss에 관대해지도록 theta를 설정해주려면 결국 우리실험에서는 0.0 밖에 답이 없더라.그리고 0.0은 relevance heuristic 이 아니더냐.

<img width="430" alt="스크린샷 2024-01-14 오후 11 19 49" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/c64cc1ac-ddd6-4401-874b-df3e9a8b5214">

Performance of the Objective heuristic on the syn- thetic Uniform dataset under the geometric attention model with different attention cut-off points.

fdf

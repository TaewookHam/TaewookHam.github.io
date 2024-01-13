---
layout: page
title: Equity of Attention:Amortizing Individual Fairness in Rankings
description: >

use_math: true
sitemap: false
---

들어가기전 분류
Calibrated fairness - merit 에 비례 해서 outcome value 가 결정되어야 한다.
Item fairness
Amortized fairness
user-wise re-ranking -> ILP 사용

여기서 attention은 사람들의 관심, 집중도 등으로 이해.

Position bias 란 무엇이고 왜 생기는가? 사람들이 top-rank 된 아이템에만 관심을 주기때문에 발생한다.
근데 당연히 ranking 이면 낮은게 더 많은 attention을 받아야 하는거 아닌가? -> 그게 맞긴해. 근데 여기서 생기는 문제는 충분히 사용자와 높은 relevance를 갖는 item마저도 lower-ranked 된다면 결국 item 제공자는 손해된다. 
여기서 의문점 -> 어느정도까지 낮으면 lower-ranked 된거로 치는거냐? 구체적인 등수까지는 안나오고 여러가지 item들의 relevance가 미세하게 차이가 나서 ranking 되었을 때 attention 차이는 꽤나 큰 경우를 의미!

equity: 형평성
기존의 의문점이 조금 해결되었다. 차이와 차별을 어떻게 구별할 수 있을까? 공평성과 형평성은 다르다는 데에서 해결의 실마리를 찾을 수 있다. 
랭킹 리스트를 제공해줄 때, relevance에 비례해서 attention을 받는 것을 목적으로 하는 method를 제시할 것이다. 이때 amortized fairness를 기준으로 삼을 것이다.
여기서 두가지 의문점  -> 
1. single ranking this goal is infeasible 하다고 했는데, 정확히 어떤 기준으로 amortized 된다는 것인가?
    - 추천 리스트를 계속 재생성하는 방식을 채택해서 일정 시간 동안 누적된 추천 리스트의 amortized fairness를 확인한다. 큰 수의 법칙에 의거하고 있다는 거네 결국에는? position bias 가 있기 때문에 리스트 랭킹리스트 하나만으로는 불가능하기 때문에 
        -> 다시 여기서 의문점 : 근본적인 bias 문제를 해결한 것은 아니네?  결국 추천리스트 혹은 페이지의 지속적인 업데이트를 전재로 하는 것인데, 업데이트를 하기 전에 사용자가 bias 된 선택을 하는 경우는 대비가 불가능 한 것 아닌가? 
        -> 두번째 의문점 : 그래 좋다 이거야. 그러면 over time 마다 attention이 바뀔 수는 있겠지만, relevance는 매 time마다 바뀔 수 있어?? 아니면 relevance는 매번 고정인거고 attention이 바뀌는 거에만 focus 가 되는 것이냐?


2. 그렇다면 감소하는 꼴의 차이에 집중할 것이냐? 아니요


2.1
Definition은 CA concept-based definition을 사용한다. 그렇지만 amortize로 사용. 

2.2
item provider에 입장에서 봤을 때, ranking 자체가 목적이 아니라 attention이라는 사회적 자원의 공정한 할당의 수단이 되는 것이다. 
여기서 기준이 되는 distributive norms이 바로 equity가 될 수 있다. 

2.3
relevance가 모두 같아서 attention이 모두 같아야하는 특수 케이스

2.4
기존 연구들은 individual 은 x 고  group 위주의 공정성을 탐구했다.
사실 bias는 각 개인마다 가지고 있으며, 그 사람이 어느 그룹에 속해있는지와는 관계없자나?
우리는 이런점에 차별점을 두었다.

3.0

Metric으로 L1-norm을 사용. -> 그러나 개인간의 불공정 분배와는 무관? -> 무슨 말임??

다른 metric은 어떤 걸 사용가능할까?

3.1
ranking quality 자체는 보장된 상태에서 unfairness 작업을 해야하기 때문에 이부분은 조심해야함.

3.2
Measurement로는 DCG, NDCG 등이 사용되니까 알아보고 정확하게 무엇을 의미하고 어떤 장 단점이 있으며 어떤 점을 반영하고 있는지 알기.

3.3
fairness와 quality 의 tradeoff 가 있다.
이 중에서 ranking quality를 어느정도 포기하고 unfairness 를 최소화하는 방향으로 빌드업을 하겠다.



---
subject to: '어떤 제약조건' 하 를 의미
viability: 생존 능력
aggravate: 화나게 하다.
quantify: 수치화 하다. 라는 의미

---

# **1. Introduction**
---
Position bias란?
> 기존의 연구들에 의하면, 사람들이 추천 시스템으로 부터 ranked-list 를 제공받은 경우 아이템과 유저의 상관관계는 rank가 낮아질 수록 linear 하게 감소하지만, 아이템이 사용자로 부터 받는 관심은 exponentional 하게 감소한다. 이로 부터 발생하는 편향을 position bias 라고 부르기로 한다.

item이 가지고 있는 merit 에 비례해서 outcome value가 결정되어야하는 calibrated fairness definition의 관점에서는 position bias가 발생하면 unfair 하다고 볼 수 있다.
item이 마땅히 받아야할 attention을 사용자로부터 온전히 받지 못했기 때문이다.

<img width="440" alt="스크린샷 2024-01-14 오전 12 19 55" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ef37d34b-3de0-4083-80aa-8aed5bf054e9">

따라서 논문의 제목인 equity은 equality 와는 다른 개념이다. equity of attention은 가지고 있는 relevance 만큼의 attention을 온전히 할당 받을 때 fair하다 라고 할 수 있다.

들어가기전 하위 종목으로 분류를 하자면,

- Calibrated fairness: merit 에 비례 해서 outcome value 가 결정되어야 한다.
- Item fairness
- Amortized fairness: single ranking list에서는 position bias 가 존재하므로 amortized list 들의 누적된 unfairness를 측정.
- user-wise re-ranking -> ILP 사용

# **1. Equity-of-attention Fairness**
---
Notations are as followed.

<img width="643" alt="스크린샷 2024-01-14 오전 12 28 32" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/7525a4d3-31cf-462b-8fe0-f81ee1949c71">

논문에서 언급한 fairness의 기준인 Calibrated fairness에 따르면 item이 relevance 에 비례하게 attention을 챙겨야 하므로, Definition을 다음과 같이 세울 수 있다.

$ \frac{a_{i1}}{r_{1i}} = \frac{a_{i2}}{r_{12}}$

그렇지만 position bias 때문에 한 single list에서는 위의 식을 만족시키는 불가능하다. 따라서 amoritzed definition을 사용하여 다시 정의한다면,

$\frac{\sum^{m}_{l=1} a^{l}_{i1}}{\sum^{m}_{l=1}r^{l}_{1i}} = \frac{\sum^{m}_{l=1} a^{l}_{i2}}{\sum^{m}_{l=1}r^{l}_{12}}$ 




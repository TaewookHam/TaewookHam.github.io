---
layout: page
title: Fairness in Recommendation Ranking through Pairwise Comparisons
description: >

use_math: true
sitemap: false
---

KDD Introduction: [https://www.youtube.com/watch?v=s5iJ-LdDvq8](https://www.youtube.com/watch?v=s5iJ-LdDvq8)

---

## **1.Problem Statement**
---
- Problem(risk): 추천시스템이 특정 그룹에 속한 아이템을 under-rank 하는 것.

    - Problem in rec-sys: point-wise 방식을 사용한 prediction 과 그 이후의 ranking construction 은 gap이 있다. -> 무슨 gap? Point-wise 방법은 주로 개별 아이템에 대한 사용자의 선호도를 예측하는 데 중점을 둔 반면, Pairwise 방법은 주로 여러 아이템 간의 상대적인 순위를 학습하는 데 중점을 두기 때문이다.

    - Problem in env: ever changing dynamics in the system -> can be biased 

    --> 그리고 이 문제들은 추천 시스템의 공정성 문제도 그대로 품고 있는 문제들이다!!

그래서 논문에서 pairwise recommendation fairness metric 을 사용하기로 한다.

Contribution 은 Pairwise fairness의 개념을 사용해 regularization method를 사용했고, large-scal production 이 가능케 했다는 점이다.


## **3. Pairwise Fairness for Recommendation**
---
### 3.1 Recommendation Environment

* cascading recommender system 뒤에 ranker 가 온다고 명시한 점을 주의 

$M$ 개의 전체 아이템 군 $J$ 로부터 검색시스템이 $M'$ 개의 아이템 셋 $R$ 을 리턴(pre-filtering 같은 개념으로)한다. 그 중에서 추천시스템이 $M' \geq K$ 인 K 개의 item으로 구성된 Personalized item을 제공한다. 
query 는 $ \textbf{q} = (\textbf{u},\textbf{c})$ 로 구성되었는데 각각 user features $\textbf{u}_i$ 와 context feature $\textbf{c}$ 를 의미한다. 

단, 이때 $j \in J$ 를 만족하는 각 아이템 j들은 fature vector $v_{j}$ 를 갖고 있다. 

### 중요!
ranker 가 클릭,평점 매기기, 머무르는 시간 등의 사용자 피드백에 기반해서 랭킹을 매긴다. 이때, 사용자 피드백을 y 와 z로 나눌 수 있다. 
$y \in {0,1}$ 는 사용자가 item을 클릭했는지를 의미하고, z는 클릭 후 사용자의 참여 혹은 적극성(engagement)의 정도를 의미한다.

그리고 이 모든 것을 historical example $D = {(\textbf{q},\textbf{v},y,z)}$ 라는 것으로 만들고, 이게 바로 train_set 이다. (만약 y=0이면 z는 무조건 0)

마지막으로 함수 $f_{\theta}$ 로 $(\hat{y}, \hat{z})$ 를 label $(y,z)$ 에 최대한 가깝게 예측하고,
나온 $(\hat{y}, \hat{z})$를 이용한 monotonic function $g(\hat{y}, \hat{z})$ 가  최종적으로 rank를 뽑아낸다. 

의문점: 랭크를 먼저 매겨야 되는거 아님? 사용자 피드백이 없으면 랭크를 어떻게 매김? -> 과거에 어떤 시스템이 랭크를 매겼었고 그에 따른 사용자 피드백이 있다고 가정.


### 3.2 Motivating Fairness Concerns
우리가 집중할 것은 특정 아이템 그룹이 under-ranked 되는 것을 방지하기 위함이다. 
추가적으로 각 그룹마다 sensitive atribute을 포함하는지를 반영한 binary variable $s \in {0,1}$ 이 있다고 하자.

user group concerns 이건 대충 우리 실험에서 일단 사용자 그룹은 무시한다는 뜻으로 받아 들일 수 있음.

### 3.3 Pairwise Fairness Metric
그럼 under-ranked 되었다라는 것이 정확히 어떤 상황을 의미하는가?
기존의 false positive, 혹은 false negative 같은 metric으로는 측정하기 어렵다.<br/> 논문에 언급된 예시로 이해해보자.

모델이 $\hat{y} = 0.6$ 로 예측했을 때, 0.6 이라는 수치는 높을 수도 있고, 낮을 수도 있다. 즉, 0.6이라는 숫자 하나만으로는 판단할 수 없는 상대적인 수치이다. 따라서  같은 query 가 주어졌을 때 다른 item 의 예측 $\hat{y}$ 끼리 비교해야 0.6이라는 수치가 어느정도로 정확한 거 인지 알 수 있다. -> unde r-rank 된 상황은 pairwise comparison으로 상대적인 metric을 사용해야 한다. 

의문점: 그렇다면 under-rank 되었다는 뜻이 ranking quality를 나타내는 거야 fairness를 나타내는거야? -> 뭔 남의 다리 긁는 소리임 당연히 fairness 적으로 under-rank된 것을 의미. 제목을 잘 읽자

다음은 pairwise accuracy 초기 정의이다. 

<img width="563" alt="스크린샷 2024-01-18 오전 10 45 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/03e23f3a-1c9a-4c35-8905-cab93fb1acd9">

j와 j' 모두 relevant item으로 판단되어 랭크에 올라간다면, Pairwise accuracy는 클릭된 아이템j 가 클릭되지 않은 아이템j'보다 높게 랭크될 확률을 의미한다.

<img width="618" alt="스크린샷 2024-01-18 오전 11 04 08" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/920fb0ba-4755-466b-b6d7-a96752234489">

특정 두 그룹 (S = 0 및 S = 1)에 속한 항목들에 대한 PairwiseAccuracy를 비교하려고 한다. 이때 어느 그룹에서든 클릭된 애들이 더 높게 랭크되는 확률은 같아야 한다.

문제는 condition에 z가 없다는 것인데 z 가 서로 같다고 가정을 하지 않으면, 클릭이 되었어도 특정그룹에서는 z=0.1 이라면, 클릭되었다 라는 이유 만으로 하이랭크될 이유는 없다. 따라서 z가 같다는 조건이 있어야 함-> 좀 더 생각해볼 여지 있음

여기서 user engagement z 까지 고려해서 Definition을 세우면 다음과 같다.

의문점: 저거 intra-group def 아니야? -> 정확히는 intra-group을 포함하고 있겠지 j'이 어떤 그룹에 속하는지는 명시되지 않았으니까.

1. Definition 1

    <img width="449" alt="스크린샷 2024-01-18 오전 11 38 07" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/2bc21839-557f-497d-8039-db57c26daea1">

    분명 pairwise accuracy를 잘 반영한 식이지만, 두 가지 경우의 mis-orderings 를 구분하지 못한다는 단점이 있다.

    그 예를 들어보면 다음과 같다. 

    - A1이 클릭된 첫번째 case: [A2, A3, B1, A1, B2, B3]
    - B1이 클릭된 첫번째 case: [A1, A2, A3, B1, B2, B3]

    둘 다 Pairwise Accuracy 가 2/5이지만 차이점은

    - 첫번째가 A2,A3,A1 순으로 배치 되어 그룹 내(intra-group) 내에서 item의 불공정성이 발생하는 경우
    - 두번째는 사용자가 group B에 속한 아이템 B1에 관심을 보였음에도 불구하고 group A를 group B 보다 항상 우선시 하여 그룹 간(inter-group) item 불공정성이 발생할 수 있다는 것이다.

    따라서 각각에 따른 metric 을 새롭게 정의해야할 필요가 있다.(우리는 두번째 경우가 더 심각한 상황이라고 인정하고 두번째 경우를 완화하는데에 더 초점을 둘 것이다.)

2. Definition 2 (Intra-Group Acc)
    
    <img width="600" alt="스크린샷 2024-01-18 오후 3 57 15" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/397f3f6f-3744-48e9-a6e1-57e4a221c321">
    
    아이템 $j$ 와 $j'$ 가 같은 그룹에 속해 있을 때( $s_j = s_{j'}$ ) , $j$ 가 $j'$ 보다 높게 랭크될 확률

    <img width="600" alt="스크린샷 2024-01-18 오후 4 12 42" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/b8de75ba-7b56-4936-9f66-9dc0ef79578d">


3. Definition 3 (Inter-Group Acc)

    <img width="600" alt="스크린샷 2024-01-18 오후 3 57 29" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/48f17594-e760-448c-8379-4eafcbfbb23d">

    아이템 $j$ 와 $j'$ 가 같은 그룹에 속해 있지 않을 때( $s_j \neq s_{j'}$ ) , $j$ 가 $j'$ 보다 높게 랭크될 확률

    <img width="600" alt="스크린샷 2024-01-18 오후 4 12 52" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/63c0f80f-7275-4c63-a2e8-dd071c7e56f5">


4. Decomposing the overall pairwise accuracy

    현실은 당연히 두 Pairwise fairness를 모두 만족해야 하므로, Definition 1 에 두 경우가 명확하게 표현될 수 있도록 세분화 해야한다.

    <img width="688" alt="스크린샷 2024-01-18 오후 4 24 59" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f21b7058-e6f0-4a6b-bcd6-e538dbca803c">

    위와 같이 각 definition 들의 weighted sum 의 형태로 표현할 수 있다. 이때 weight는 해당 형태의 pair를 볼 확률에 의해 결정된다.
    예를 들어 위의 6 item list 의 경우는 intra-prob 가 2/5, inter-prob가 3/5 일 것이다.

    식이 헷갈리면 종이에 천천히 적어보면 바로 이해가능.
    $\tilde{s} = 1$ 로 대입해서 적어보았다. 

    ![IMG_1915](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/0c1a2c4a-7a13-4339-9692-576f5e6098e5)

    1. $\tilde{s}' = \tilde{s}$ 일 경우 둘 다 같은 그룹
    2. $\tilde{s}' = 1- \tilde{s}$ 일 경우 둘 이 다른 그룹
    

engaement는 왜자꾸 같다고 설정? -> 당연한 것. engage가 높은(혹은 낮은) 애들끼리 비교해야 의미있는 비교니까

그룹이 무조건 2개라고 가정 -> 만약 그룹이 세 개 이상되면???

그러니까 특정 아이템이 group s에 속한다고 해서 차별받으면 안 된다는 거지?

그리고 s=1에서의 j 와 s=0에서의 j는 같은거임 다른거임? 특정 아이템 j가 어디에 있든지 라는 건가?

그럼 Definition 2 and 3로  PA 를 측정하면 어케 되는 거?
각각 0, 0 인거??

그럼 여기서 의문점 클릭된 애들끼리, 그리고 클릭 안 된 애들끼리의 랭킹은 어떻게 하는건데??

### 3.4 Measurement

데이터셋 선택에서 어떻게 bias를 최소화 했는가? 에 대한 설명.

사용자 선호도는 하루아침에 바뀔정도로 dynamic 하므로 전형적으로 바로전 recommended item에 기반한 사용자 피드백을 관찰. -> 그러면 당연히 바로전 rec sys에 의존적이게 되어 bias 가 발생하겠지!!

unbiased estimaotr가 필요하므로 일부의 randomized experiments 으로 실험을 진행한다.(왜냐하면 unbiased 하다는 건 일부 데이터셋으로 estimate해도 전체 데이터셋으로 estimate 했을 때와 결과가 다르지 않다는 것을 의미)

이것도 결국 bias 를 피하기 위해서  데이터셋은 추천페이지(slate) 에서 2페이지, 3페이지 등을 사용.
이때 비교 대상이 될 두 아이템 j와 j'은 랜덤하게 선택. 또한 2페이지,3페이지에서의 ordering 또한 랜덤이다. 

결국 click 되는 아이템은 극히 일부일 것이고, 그 클릭되는 아이템의 query와 선택된 pair, 어떤 아이템이 클릭되었는지 추 후의 engagement 는 어땠는지를 전부 기록. z를 discrete 하게 만든다.

* 결국 여기서 내가 궁금한 것은
1. 어떤 무언가가 유저의 과거 기록을 기반으로 (q,v,y,z)를 이미 다 준비 해놓음 -> 함수 g(f())를 기반으로 랭크를 매김(y_hat 과 z_hat) -> 여기서 생긴 slate 2,3 에서 각각 임의의 아이템 2개를 뽑고 pair로 묶어서 유저에게 보여줌 -> 유저가 특정 아이템들을 클릭할 수도 있고 아닐 수도 있음 -> 클릭했다면, 그에 따른 y와 z 가 생기자나? 이건 뭐냐고?

2. 우리가 이미 (q,v) 는 갖고 있으므로 -> 함수 g(f())를 기반으로 랭크를 매김(y_hat 과 z_hat) -> 여기서 생긴 slate 2,3 에서 각각 임의의 아이템 2개를 뽑고 pair로 묶어서 유저에게 보여줌 -> 유저가 특정 아이템들을 클릭할 수도 있고 아닐 수도 있음 -> 클릭했다면, 그에 따른 y와 z 가 생기자나 -> 이게 실제 레이블 y와 z 가 되는거??? -> 그렇다 치자, 그러면 unclicked item이 클릭되었을 경우 engagements를 모른다는 거는 뭐야? -> 사실 처음엔 모든게 unclicked item 이 자나

그니까 실제 레이블 y와 z가 뭐냐고??
2번임.

## **4. Theoretical Analysis**

position 을 이런 식으로 표현이 가능하다.

<img width="538" alt="스크린샷 2024-01-18 오후 1 28 07" src="https://github.com/TaewookHam/TaewookHam.github.io/
assets/117107025/2fd8e694-f599-42b1-92a7-fd99a5515008">

이때 $\mathbb{1}$ 의 의믜는 식을 만족하는지를 나타내는 binary classifier 정도로 이해하면 되겠다.

그리고 이 식을 뒤의 Theorem들을 증명하는 데에 써보자.

### 4.1 Ranking Interpretation


Theorem1. *If a recommender system achieves pairwise fairness, then the expected position of a clicked item with z engagement is the same across groups.*

<img width="724" alt="스크린샷 2024-01-19 오전 11 29 41" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f4c69d86-325f-46dd-a010-b275de545fa6">

position equation 과 Definition 1의 내용에 기반해 식을 따라가다보면 충분히 이해가 가능한 증명이다. -> 결론적으로 아이템 j가 어느 그룹에 속해 있든간에 클릭유무와 engagement z에 기반했을 때 평균 rank가 같아야 한다. 

좀 뜬금없지만...
position rank의 식을 inter,intra 부분으로 쪼개어 표현도 가능하다. 

<img width="730" alt="스크린샷 2024-01-19 오전 11 37 18" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/226a5404-f74c-4776-8d47-557d1b54f2f9">

이렇게 각 케이스의 확률을 빼도 결과는 같아지겠구나~ 정도만 이해하면 충분할 듯.

### 4.2 Relation to Pointwise Metrics

pairwise metrics 은 pointwise metrics 와 충돌하게 된다. 
결론만 챙기자면 pointwise metrics의 대표인 Calibration 과 MSE 모두 pairwise에는 적절치 않다.

~~증명은 너무 어렵다~~

## 5. Pairwise Regularization to Improve Fairness
---
지금까지는 전부 우리의 타겟을 얘기했으니까 이제부터는 이 목표를 달성하기 위한 모델 construction을 제시.

regularization + low correlation between group membership and predictions

residual between clicked and unclicked 와
group membership of the clicked item 간의
correlation을 계산한다.
A
B 식의 관계를 잘 생각해보기.

의문점: 상관관계는 표현할 수 있지만 정확도 파악은 안되는거네? 사실 그 정확도는 앞의 loss function에서 해주지 않을까? 
의문점2: 그리고 절대값 상관관계는 미분이 안되지 않나? 그러면 학습은 어케함?? 다른 방법이 잇는듯 이거는 더 찾아보자...
의문점3: 둘이 클릭이 다른 경우는 이해 했어 근데 둘 다 클릭을 한 경우는 어떻게 이해할 수 있을까? -> 그건 우리 관심 밖이니까 식에 반영을 안 해놓은듯?


왜 씀? 걍 써보니까 이 방식이 strong 하고 easy to use!!

아니 P 가 pairs of tuples 로 구성되어 있다면 이때의 j와 j'은 각각 click unclick 이야 아니면 그건 상관이 없는거야?
근데 뒤에 설명보니까 그건 상관없는 거 같은데




## 6. Experiments
---
cascading recommender system: 여러 단계로 구성된 추천 시스템을 나타내는 용어입니다

기본 모델로 y와 z를 예측하는 pointwise 방식으로 학습된 multi-layer neural network 를 사용.

subgroup: sensitive o
not subgroup: sensitive x

일단 전체적으로 실험 결과는 engagement가 낮으면 subgroup에 속해 있는 애들이 클릭이 되었음에도 그렇지 않은 애들보다 더 under-ranked 된다는 결과를 보여준다.

<img width="576" alt="스크린샷 2024-01-20 오후 10 09 50" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/fbf294f6-9189-492e-aa4e-32750a9c9d00">

Figure 3.a 에서 확인가능하듯 sensitive attributes를 포함한 subgroup 내에서의 우선순위 결정에 baseline system이 더 어려움을 겪었음을 알 수 있다.

<img width="578" alt="스크린샷 2024-01-20 오후 10 09 59" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9dc0689b-162b-4a56-a35a-93300a79863b">

<img width="577" alt="스크린샷 2024-01-20 오후 10 16 58" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/a7498b93-3318-4170-b7ba-2dd6a82a0099">

Figure 1.a 를  보면 inter-group 간의 편차가 상당히 큼을 알 수 있다. 
이 말은 즉, 사용자가 특정 아이템에 관심을 보였음에도, 그 아이템이 특정 group에 속해 있다는 이유로 under-ranked 될 수 있다는 의미이다.


---
정확하게 이해가 안 되는 점이
그룹으로 나눈다는게 정확하게 무엇인지? s를 기준으로 나눈다는거?
그리고 sensitive attribute 이라는 게 정확히 뭔데? 이것도 민감한 거라서 비공개야?





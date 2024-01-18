---
layout: page
title: Fairness in Recommendation Ranking through Pairwise Comparisons
description: >

use_math: true
sitemap: false
---

CO base definition
그 중에서 Pairwise Ranking Accuracy Gap metric을 사용

only applicable to two groups 
item
single
impact-base
regularization

coalescence: 동의
pervasive: 퍼진
salient: 현저한
corpus: 집단
succinctness: 간결함
in tension with: 두 가지 개념 또는 가치가 서로 충돌하거나 상충하는 상태

---
목표가 뭐라고? 특정 그룹의 아이템들이 under-rank 되는 것을 해결하기


## **3. Pairwise Fairness for Recommendation**
---
### 3.1 Recommendation Environment
* recommender system 이랑 ranker 를 혼재해서 사용한다는 점을 주의 

$M$ 개의 전체 아이템군 $J$ 로부터 검색시스템이 $M'$ 개의 아이템 셋 $R$ 을 리턴(pre-filtering 같은 개념으로)하면 추천시스템은 그 중에서 $M' \geq K$ 인 K 개의 item으로 구성된 Personalized item을 제공한다. 
query 는 $ \textbf{q} = (\textbf{u},\textbf{c})$ 로 구성되었는데 각각 user features $\textbf{u}_i$ 와 context feature $\textbf{c}$ 를 의미한다. 

ranker 가 클릭,평점 매기기 등의 사용자 피드백에 기반해서 랭킹을 매긴다. 
사용자 피드백을 y 와 z로 나눌 수 있다. 
$y \in {0,1}$ 는 사용자가 item을 클릭했는지, z는 클릭 후 사용자의 참여 혹은 적극성(engagement) 을 의미한다.

### 3.2 Motivating Fairness Concerns
우리가 집중할 것은 특정 아이템 그룹이 under-ranked 되는 것을 방지하기 위함이다. 
추가적으로 각 그룹마다 sensitive atribute을 포함하는지를 반영한 binary variable $s \in {0,1}$ 이 있다고 하자.

user group concerns 이건 뭔소린지 모르겠어

### 3.3 Pairwise Fairness Metric
그럼 under-ranked 되었다라는 것이 정확히 어떤 상황을 의미하는가?
기존의 false positive, 혹은 false negative 같은 metric으로는 측정하기 어렵다.
뭔소리? 예시로이해가능.
따라서 positive rate으로 under-ranked 된거를 파악하려면 각 아이템에 대해 point-wise 하게 해야되고, 이는 같은 query 가 주어졌을 때 다른 item 의 예측 $\hat{y}$ 까지 비교해야한다.(뭔말알) -> 그래서 pairwise로 하겠다!

<img width="563" alt="스크린샷 2024-01-18 오전 10 45 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/03e23f3a-1c9a-4c35-8905-cab93fb1acd9">

Pairwise accuracy는 클릭된 아이템이 'relevant&클릭되지 않은 아이템' 보다 높게 랭크될 확률을 의미한다. -> 당연한 걸 왜 거창하게 써놓음??

<img width="618" alt="스크린샷 2024-01-18 오전 11 04 08" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/920fb0ba-4755-466b-b6d7-a96752234489">

특정 두 그룹 (S = 0 및 S = 1)에 속한 항목들에 대한 PairwiseAccuracy를 비교하려고 한다. 이때 어느 그룹에 속하든 간에 pairwise accuracy 차이를 줄이는 것이 타겟이 된다.

higher or lower than: 위의 표현식이 성립되지 않을 수도 있다를 의미 -> 그래서 맞춰줘야 하는 것.

여기서 user engagement z 까지 고려해서 Definition을 세우면 다음과 같다.

- Definition 1

    <img width="449" alt="스크린샷 2024-01-18 오전 11 38 07" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/2bc21839-557f-497d-8039-db57c26daea1">

    분명 pairwise accuracy를 잘 반영한 식이지만, 두 가지 경우의 mis-orderings 를 구분하지 못한다는 단점이 있다.

    그 예를 들어보면 다음과 같다. 

    - A1이 클릭된 첫번째 case: [A2, A3, B1, A1, B2, B3]
    - B1이 클릭된 첫번째 case: [A1, A2, A3, B1, B2, B3]

    둘 다 Pairwise Accuracy 가 2/5이지만 차이점은

    - 첫번째가 A2,A3,A1 순으로 배치 되어 그룹 내(intra-group) 내에서 item의 불공정성이 발생하는 경우
    - 두번째는 사용자가 group B에 속한 아이템 B1에 관심을 보였음에도 불구하고 group A를 group B 보다 항상 우선시 하여 그룹 간(inter-group) item 불공정성이 발생할 수 있다는 것이다.

    따라서 각각에 따른 metric 을 새롭게 정의해야할 필요가 있다.

        <img width="632" alt="스크린샷 2024-01-18 오후 3 57 15" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/397f3f6f-3744-48e9-a6e1-57e4a221c321">

    

그럼 새로운 관점에서 PA 를 측정하면 어케 되는 거?
각각 0, 0 인거??

그럼 여기서 의문점 클릭된 애들끼리, 그리고 클릭 안 된 애들끼리의 랭킹은 어떻게 하는건데??

### 3.4 Measurement
위의 definition을 만족하면 목표를 달성했다라고 가정을 할 때 얼마나 이 목표를 만족시켰냐에 관한 'degree' 는 측정하기 까다롭다.
더욱이 사용자 선호도는 하루아침에 바뀔정도로 dynamic 하므로 전형적으로 바로전 recommended item에 기반한 사용자 피드백을 관찰. -> 그러면 당연히 바로전 rec sys에 의존적이게 되어 bias 가 발생하겠지!!

unbiased estimaotr가 필요하므로 일부의 아이템셋으로 실험을 진행한다.(왜냐하면 unbiased estimator라면 전체 중 일부를 가지고 estimate해도 unbiase 한 결과가 나와야하므로)
데이터셋은 추천페이지(slate) 에서 2페이지, 3페이지 등을 사용. 덜 bias 되기를 원하니까
이때 비교 대상이 될 두 아이템 j와 j'은 랜덤하게 선택. 2페이지,3페이지에서의 ordering 또한 랜덤이다. 


## **4. Theoretical Analysis**

추천시스템 알고리즘 보다 랭킹에 더 가깝다. 
position 을 이런 식으로 표현이 가능하다.

### 4.1 Ranking Interpretation

<img width="538" alt="스크린샷 2024-01-18 오후 1 28 07" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/2fd8e694-f599-42b1-92a7-fd99a5515008">

그리고 이 식을 뒤의 Theorem들을 증명하는 데에 써보자.

Theorem1. If a recommender system achieves pairwise fairness, then the expected position of a clicked item with z engagement is the same across groups
따라서 우리는 pairwise 추천 공정성을 position of a clicked and engaged with item가 평균적으로는 어느 그룹에 속해 있는지에 의존해서는 안 된다는 개념과 동일하게 해석할 수 있음을 알 수 있다.

### 4.2 Relation to Pointwise Metrics

pairwise metrics 은 pointwise metrics 와 충돌하게 된다. 
결론만 챙기자면 pointwise metrics의 대표인 Calibration 과 MSE 모두 pairwise에는 적절치 않다.
~~증명은 너무 어렵다~~

## 5. Pairwise Regularization to Improve Fairness
---
지금까지는 전부 우리의 타겟을 얘기했으니까 이제부터는 이 목표를 달성하기 위한 모델 construction을 제시.


## 6. Experiments
---
기본 모델로 multi-layer neural network trained in a pointwise-fashion 사용.

subgroup: sensitive o
not subgroup: sensitive x

---
정확하게 이해가 안 되는 점이
그룹으로 나눈다는게 정확하게 무엇인지? s를 기준으로 나눈다는거?





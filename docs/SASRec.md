---
layout: page
title: Self-Attentive Sequential Recommendation
description: >
   At each time step, SASRec seeks to identify which items are ‘relevant’ from a user’s action history, and use them to predict the next item.
use_math: true
sitemap: false
---
## **0&1 Abstract & Introduction**
일상 속에서, 혹은 자연어 처리 분야에서 문맥상의 흐름, 즉 context를 고려해서 그 다음에 올 content를 예측하는 것은 중요하다. 이는 추천시스템에서도 마찬가지로 사용자의 최근 기록을 토대로 다음에 어떤 item을 추천할지 결정하기도 한다. 이에 사용되는 방식은 크게 두가지가 있다.
- Markov Chains(MCs): by making strong simplifying assumptions, perform well in high- sparsity settings, but may fail to capture the intricate dynamics of more complex scenarios.

- Recurrent Neural Networks(RNNs): while expressive, require large amounts of data

이 두가지 방식의 장점을 취합하고 단점을 보완한 방식이 이번 논문에서 소개할 SASRec 이다.
먼저 소개하자면, RNN처럼 long-term 메모리에 기반하고, MC처럼 short-term에 기반한 decision을 내린다. 이 과정에서 self-attention을 사용해 사용자의 history 중 어떤 item이 사용자와 관련성이 높은지 파악하고 그 아이템을 추천 item을 예측하는 데에 사용한다.
결과적으로 sparse dataset과 dense dataset에서 모두 좋은 성능을 보여주었다고 한다.


![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/17468523-030d-4e04-802f-f2fa8cec3a40)
{: width="70%" height="50%"}

## **3. Methodology**

---

### *A. Embedding Layer*

기본 세팅은 다음과 같다.

Transform the Training Sequence,$(S^u_{1},S^u_{2},..S^u_{|S^u|-1},) $
->$s = (s_1,s_2,...s_n)$
- item Embedding Matrix $M \in R^{I\times d}$
- input Embedding Matrix $E \in R^{n\times d}, where E_i = M_{s_i}$
즉, 전체 아이템을 담고 있는 행렬 M에서 우리의 i번째 input을 가져온 형태라는 것이다.

#### *Positional Encoding*
Transformer에서 sequential input을 순서대로 받아들이지 않고 한 번에 위치 정보를 받아들이기 위한 방식으로 postion embedding P를 처음 input embedding layer에 더해준다.

$\hat{E} = \begin{bmatrix}
M_{s_1}+ P_1\\
M_{s_2}+ P_2\\ 
...\\ 
M_{s_n}+ P_4
\end{bmatrix}
$
여기서 P는 learnable position embedding layer로서 원래의 transformer 논문에서 사용되었던 삼각함수를 사용한 fixed position embedding을 사용하려 했으나 자신들의 연구에서 성능을 저하시키는 것을 발견, learnable 한 방식을 채택하게 되었다.

### *B. Self Attention Block*
Attention 과 마찬가지로 Q,K,V 값을 생성하고 Self attention layer에 통과시킨다.

$ \textbf{S} = SA(\hat{E}) = Attention(\hat{E}W^Q,\hat{E}W^K,\hat{E}W^V) $

식이 가지는 의미를 간단하게 설명하자면, 
1. Embedding layer를 각각 Key, Query, Value set으로 나눈다.
2. i번째 item (Key)와 j번째 item (Query) 간의 관계를 inner product로 나타내고 그 관계성을 attention coefficient로 나타내어 j번째 item(Value)에 곱해준다. 당연하게 두 아이템 간의 관계가 높을 수록 inner product 값이 크게 나타날 것이고 j번째 item의 vector에 더 가중치를 준다.
단 이때 project matrix $W$가 차원을 바꾸지는 않고 여전히 d 차원으로 차원보존한다. 논문에 따르면 projection 은 모델을 더 flexible하게 만든다고 한다. 예를 들어 $i th$ Query와 $j th$ Key간 관계와 $j th$ Query와 $i th$ Key간 관계

그렇지만 attention 에서 흔히 얘기하는 cheating 을 막아야하므로 masked self attention 과 유사하게 $i < j$ 인 경우, $i th$ Query와 $j th$ Key간 관계는 block 해야한다.

또한 모델에서는 한 데이터 포인트끼리 loss를 계산하는 Point-Wise Feed-Forward Network을 사용하기로 했다.

F_i = FFN(S_i) = ReLU(S_iW^{(1)} + b^{(1)}W^{(2)}) + b^{(2)}

물론 논문에서는 attention layer의 i번째 row만 FNF에 통과시켰지만 사실 상 matrix calculation을 통해서 통째로 계산할 것이다.

### *C. Stacking Self-Attention Blocks*
self-attention block을 한 번 통과 한 $F_i$는 다른 이전 아이템들에 대한 정보를 집약하고 있을 것이다. 한 번 만 통과하는 것보다 block을 여러번 통과시킨다면 더 복잡한 표현관계를 나타낼 수 있지 않을까? 싶어서 Self-Attention layer를 stack 하기로 했다. b번째 까지 stack 할 수 있다고 하지만 논문에서는 실제 2번만 stack 했다고 한다. 그리고 Gradient descent를 막기 위해서 Residual Connection사용하였다. 

##### - Residual Connection

<span style="font-size:85%"> 주로 Gradient descent를 없애고 모델의 표현력을 높이기 위해서 사용한다. 논문에 따르면, useful low-layer feature를 high-layer 까지 안전하게 전달해주기 위해서 Residual Connection를 사용한다고 한다. 우리 모델은 가장 최근에 사용자가 접촉한 item이 유용하다고 가정하기 때문에, low layer에서 판명된 이 정보가 attention layer를 여러번 거쳐도 마지막까지 보존될 수 있도록 하기 위함이다. 
</span>   

##### - Layer normalization
Layer Normalization은 data sample 단위로 평균(mean)과 표준편차(std)를 계산해서 정규화를 실행한다. 다시 말해, 한 item의 latent dimension이 얼마이든 한 배치 안에서 배치사이즈 N만큼의 평균과 표준편차를 각각 N번 계산해서 각 item 별로 정규화를 진행한다. 이때 zero-mean, unit-variance로 normalization을 진행한다. 이 방식은 모델을 안정화하고 학습을 가속시키는데에 알맞다고 한다.

$LayerNorm(x) = \alpha * \frac{ \textbf{x} -\mu}{\sqrt{\sigma^2}+\epsilon}+ \beta$ 

아마 위의 식에서 알파와 베타값을 조절해서 zero-mean, unit-variance로 조절해주는 듯 하다.

##### - Dropout

오버피팅을 방지하기 위해서 embedding layer $\hat{E}$에 dropout을 적용한다고 한다.

### *D. Prediction Layer*
위의 과정을 하는 이유는 궁긍적으로 사용자의 최근 history를 고려해서 어떤 아이템에 가장 관심을 가질지를 예측하는 것이다. 그렇다면 예측은 어떻게 하는 것일까?

##### - Shared Item Embedding:
b 번의 layer를 거치고 나온 output $F^{{b}}_t가 있을 때 , $F_t^{{b}}*N_i^{t}$

$ r_{i,t} $ = $ F_t^{{b}}N_i^{t} $ , where $F_t^{{b}}$ is $N \times d$ and $N_i^{{t}}$ is single item embedding $ d \times |I|$

그러므로 $ r_{i,t} $ 값이 클 수록 높은 t 시간까지 아이템 i와 해당유저가 높은 관계성을 가진다고 얘기할 수 있다.

$ r_{i,t} $ 을 내림차순으로 정렬해서 높은 score를 가지는 item을 추천해줄 수 있다.

##### - Explicit User Modeling
$r_{u,i,t} $ = $ (U_u + F_t^{{b}})M_i^{t} $ 처럼 explicit 한 정보를 담고있는  User embedding 을 생성해주어 따로 추가해줄 수 있지만 실험적으로 큰 성능향상을 찾아내진 못했다.


### *E. Network Training*
만약 input의 길이가 우리가 다루는 n보다 길다면 가장 최근의 n개의 데이터를 input으로 사용하고,
반대로 n보다 짧다면 padding을 이용해서 model input을 조절할 수 있다.
그렇게 해서 만들어진 sequence $s = (s_1,s_2,...s_n)$
를 입력하는데, 각 input이 어느것이냐에 따라 아래와 같은 output을 확인할 수 있다.


$ o_t = 
\left\{\begin{matrix}   
<pad> \\ 
s_{t+1} \\ 
S^u_{|S_u|}
\end{matrix}\right.
$

이 때 $S^u_{|S_u|}$는 우리가 가장 마지막에 뽑아내는 prediction이라고 이해하면 된다.

그리고 loss function으로는 binary cross entropy loss 를 채택했다.

![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/aab65897-9be3-4026-9923-28455ae0e3ff)

모든 모든 시간 순번 t 에 대해서 그 다음 output을 예측한다. 여기서 조금 헷갈릴 수 있는데 $S_u$는 sequence item을 의미하고 t의 경우 그 sequence item 보다 먼저 나왔던 item t들(자기 자신도 포함)을 의미한다. 
각 epoch 마다 각 순서에서 각 time step 마다 하나의 negative sample j를 만들어서 loss 계산에 포함시켰다
### *F. Complexity Analysis*
- Space Complexity: 우리 모델에서 학습 가능한 parameter 수는 1)self attention, 2) FFN, 3) layer normalization 이고 space complexity 는 $O(|I|d + nd + d^2)$ 따른다. 이는 기존의 연구에 비해서 적고 d의 차원이 낮을 것을 고려하면 it does not grow with the number of users 이다.
- Time Complexity: 계산 시간의 대부분은 attention 과 FNN layer 때문이다. 그리고 그 중 dominant한 부분은 attention이라고 이해할 수 있다 .따라서 시간 복잡도를 $O(n^2d + nd^2) $ ~ $O(n^2d)$로 나타낼 수 있다. attention은 어차피 병렬적으로 계산이 진행되기 때문에 우리의 모델은 RNN 기반의 $O(n)$보다 10배정도 빠른 계산성능을 보였다.
- Handing Long Sequences: 유감스럽게도, 아주 긴 sequence에 대해서는 그닥 유효한 성능을 보이지 못했다고 한다. future work로 1) 시간적으로 가까운 곳에 attention 집중 2) long sequence를 쪼개서 처리 등을 제안했다.


### *G. Discussion*
결국 SASRec는 전형적인 Collaborative Filtering 의 일반화 버전이라고 이해할 수 있다.
- Reduction to Existing Models: SASRec 에서 사용된 다양한 기법들을 요리조리 뺀게 결국에는 기존의 FMC 혹은 FISM이다.


- MC-based Recommendation: first-order MCs, high-order MCs 두 가지 존재, high-order는 order L을 훈련 전 명시해주어야하고, L이 길다고 반드시 performance 향상이 보장되지 않는다는 단점 존재. 그러나 SASRec는 sparse 할 때는 좀 더 long-term을 참고해서, dense 할 때는 바로 전의 데이터를 참고하는 방식으로 L의 명시성문제를 해결했다.

- RNN-based Recommendation: attention의 등장으로 입지가 많이 줄었고, 우리의 SASRec는 RNN base model을 완전히 대체할 수 있다. 또한 maximum path length 또한 압도적으로 효율적이다.

### **4\. Experiment & Conclusion**


<div class="gumroad-product-embed" data-gumroad-product-id="nuOluY"><a href="https://gumroad.com/l/nuOluY">Loading…</a></div>



[^1]: If you are a fan of the old two-column layout, or don't like modern design tropes such as mega headlines, Hydejack lets you revert these changes on a case-by-case basis via configuration options.

[^2]:
      Search was mainly tested for English and German. Please let me know about issues in other languages. 
      While I've tried to find a multi-language solution, most showed drastically worse  results for the English base case.
      If you're technically inclined, you can adopt the code located in `_includes/js/search-worker.js` to your needs.



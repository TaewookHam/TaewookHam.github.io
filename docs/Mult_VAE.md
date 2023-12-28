---
layout: page
title: Variational Autoencoders for Collaborative Filtering
description: >
   
use_math: true
sitemap: false
---
## **0&1 Abstract & Introduction**

#### - collaborative signal 이란?

&ensp; 예를 들어 쿠팡에서 사용자나 아이템에 대한 embedding feature vector 혹은 matrix를 생성한다고 하자. 이때 기존의 전형적인 MF나 deep-learning MF을 활용한 방식은 '사용자가 어떤 아이템에 관심을 가지고, 그것을 소비했냐?' 라는 정보를 담지 못한다는 단점이 있다. 논문에서는 그 정보를 collaborative signal, latent in user-item interactions라고 표현하고 있다.

#### - high-order connectivities란?

&ensp; 기존 MF 방식에서 사용했던 단순히 누구누구가 무슨 영화를 봤다를 표현하는 것을 넘어서 user-item bipartite 그래프 상에서 다단계의 복잡한 연결관계를 표현한 것. 

#### - 기존 방식의 한계점

&ensp; 기존의  비슷한 특징을 가진 사용자는 비슷한 취향을 가지고 있을 것이다라는 가정을 기반으로 아이템을 추천해주었다. 여기서 embedding + interation modeling 이 필수다. 그러나 기존의 다양한 접근의 연구방식들은 사용자 간, 혹은 아이템 간의 유사성을 완벽하게 캐치해내기에는 collaborative signal을 잘 담아내지 못했다.  사실 기존 연구는 유저 임베딩을 할 때 어떤 서술적인 정보만 참고할 뿐이지, (예를 들면, 쿠팡 회원가입시 기재하는 개인정보들이라 생각하자), 내가 야구와 테니스를 좋아하는지는 전혀 고려하지 않는다는 것이다.
그래서 임베딩에 정보가 부족하니까 이 interaction을 알아내기 위한 함수를 따로 만들어야 하는 불편함이 있었다.

---

&ensp; 논문의 궁극적인 목적은 collaborative signal을 embedding 과정에 포함시키는 것이다. 그렇게 함으로써 high-order connectivity를 효과적으로 표현해낼 수 있다.

<img width="602" alt="스크린샷 2023-12-26 오후 11 18 45" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ced67827-c8ee-4c72-bba6-43096fc00099">
{: width="70%" height="70%"}



&ensp; toy exmaple은 어렵지 않으므로 자세하게 설명하지는 않겠다. 간단히 말해, $ i_4 \rightarrow u_2 \rightarrow i_2 \rightarrow u_1 $ 처럼 나타낼 수 있는 high-order connectivity 를 추천시스템에 담아낼 수 있는 모델을 만드는 것이 해당 논문의 방향성이라고 이해할 수 있다. 

&ensp; 그렇다면 어떻게 목적을 달성할거냐? 바로 Embedding propagation을 사용하는 방식을 사용할 것이다.

#### - embedding propagation 이란?

&ensp; we devise an embedding propagation layer, which refines a user’s (or an item’s) embedding by aggregating the embeddings of the interacted items (or users). 

&ensp; 사용자가 상호작용한 item의 정보를 취합하여 그 사용자의 embedding을 생성한다. 그리고 이 작업을 여러번 반복(propagation layer를 거친다)한다. 
Figure 1; 에서 layer를 몇 번 쌓았냐에 따라 $ l $ 로 표기해두었다. 실험 결과에도 나오지만 layer가 어느 정도까지는 늘어날 수록 좋은 성능을 보인다고 한다.


## **2. Methodology**
---
기본적으로 3 개의 framework로 구성,
-
-
-

### *2.2. Embedding Propagation Layers*

Intuitively, the interacted items provide direct evidence on a user’s preference(한국어로 어떻게 표현할지 몰라서 그대로 사용),
이와 비슷하게 아이템을 소비하는 사용자는 아이템의 특징으로 간주되어 두 아이템의 협업 유사성을 측정하는 데 사용될 수 있습니다.
message construction and message aggregation 을 사용해 propagtion을 구성한다. 

Message Construction
---


Message Aggregation:
---



#### *Positional Encoding*
Transformer에서 sequential input을 순서대로 받아들이지 않고 한 번에 위치 정보를 받아들이기 위한 방식으로 postion embedding P를 처음 input embedding layer에 더해준다.

$\hat{E} = \begin{bmatrix} M_{s_1}+ P_1 \\ M_{s_2}+ P_2 \\  ... \\ M_{s_n}+ P_4 \end{bmatrix}$ 

여기서 P는 learnable position embedding layer로서 원래의 transformer 논문에서 사용되었던 삼각함수를 사용한 fixed position embedding을 사용하려 했으나 자신들의 연구에서 성능을 저하시키는 것을 발견, learnable 한 방식을 채택하게 되었다.

### *B. Self Attention Block*
Attention 과 마찬가지로 Q,K,V 값을 생성하고 Self attention layer에 통과시킨다.

$\textbf{S} = SA(\hat{E}) = Attention(\hat{E}W^{Q},\hat{E}W^{K},\hat{E}W^{V})$ 

식이 가지는 의미를 간단하게 설명하자면, 
1. Embedding layer를 각각 Key, Query, Value set으로 나눈다.
2. i번째 item (Key)와 j번째 item (Query) 간의 관계를 inner product로 나타내고 그 관계성을 attention coefficient로 나타내어 j번째 item(Value)에 곱해준다. 당연하게 두 아이템 간의 관계가 높을 수록 inner product 값이 크게 나타날 것이고 j번째 item의 vector에 더 가중치를 준다.
단 이때 project matrix $W$ 가 차원을 바꾸지는 않고 여전히 d 차원으로 차원보존한다. 논문에 따르면 projection 은 모델을 더 flexible하게 만든다고 한다. 예를 들어 $i th$ Query와 $j th$ Key간 관계와 $j th$ Query와 $i th$ Key간 관계

그렇지만 attention 에서 흔히 얘기하는 cheating 을 막아야하므로 masked self attention 과 유사하게 $i < j$ 인 경우, $i th$ Query와 $j th$ Key간 관계는 block 해야한다.

또한 모델에서는 한 데이터 포인트끼리 loss를 계산하는 Point-Wise Feed-Forward Network을 사용하기로 했다.

$F_i = FFN(S_i) = ReLU(S_iW^{(1)} + b^{(1)}W^{(2)}) + b^{(2)}$

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
b 번의 layer를 거치고 나온 output $F_t^{b}$ 가 있을 때 , $F_t^{b}*N_i^{t}$

$ r_{i,t} = F_t^{b} N_i^{t} $ , where $F_t^{b}$ is $N \times d$ and $N_i^{t}$ is single item embedding $d \times \left\vert I \right\vert$ 

그러므로 $ r_{i,t} $ 값이 클 수록 높은 t 시간까지 아이템 i와 해당유저가 높은 관계성을 가진다고 얘기할 수 있다.

$ r_{i,t} $ 을 내림차순으로 정렬해서 높은 score를 가지는 item을 추천해줄 수 있다.

##### - Explicit User Modeling
$r_{u,i,t} $ = $ (U_u + F_t^{b} )M_i^{t} $ 처럼 explicit 한 정보를 담고있는  User embedding 을 생성해주어 따로 추가해줄 수 있지만 실험적으로 큰 성능향상을 찾아내진 못했다.


### *E. Network Training*
만약 input의 길이가 우리가 다루는 n보다 길다면 가장 최근의 n개의 데이터를 input으로 사용하고,
반대로 n보다 짧다면 padding을 이용해서 model input을 조절할 수 있다.
그렇게 해서 만들어진 sequence $s = (s_1,s_2,...s_n)$ 를 입력하는데, 각 input이 어느것이냐에 따라 아래와 같은 output을 확인할 수 있다.

<center>
<img width="405" alt="스크린샷 2023-12-22 오후 1 59 42" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/4f05e45d-db47-47d7-84f5-88a614491505">
</center>

이 때 $S^u_{S_u}$ 는 우리가 가장 마지막에 뽑아내는 prediction이라고 이해하면 된다.

그리고 loss function으로는 binary cross entropy loss 를 채택했다.

![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/aab65897-9be3-4026-9923-28455ae0e3ff){: width="70%" height="70%"}

모든 모든 시간 순번 t 에 대해서 그 다음 output을 예측한다. 여기서 조금 헷갈릴 수 있는데 $S_u$ 는 sequence item을 의미하고 t의 경우 그 sequence item 보다 먼저 나왔던 item t들(자기 자신도 포함)을 의미한다. 
각 epoch 마다 각 순서에서 각 time step 마다 하나의 negative sample j를 만들어서 loss 계산에 포함시켰다
### *F. Complexity Analysis*
- Space Complexity: 우리 모델에서 학습 가능한 parameter 수는 (1)self attention, (2) FFN, (3) layer normalization 이고 space complexity 는 $O(Id + nd + d^2)$ 따른다. 이는 기존의 연구에 비해서 적고 d의 차원이 낮을 것을 고려하면 it does not grow with the number of users 이다.
- Time Complexity: 계산 시간의 대부분은 attention 과 FNN layer 때문이다. 그리고 그 중 dominant한 부분은 attention이라고 이해할 수 있다 .따라서 시간 복잡도를 $O(n^2d + nd^2) $ ~ $O(n^2d)$로 나타낼 수 있다. attention은 어차피 병렬적으로 계산이 진행되기 때문에 우리의 모델은 RNN 기반의 $O(n)$보다 10배정도 빠른 계산성능을 보였다.
- Handing Long Sequences: 유감스럽게도, 아주 긴 sequence에 대해서는 그닥 유효한 성능을 보이지 못했다고 한다. future work로 (1) 시간적으로 가까운 곳에 attention 집중 (2) long sequence를 쪼개서 처리 등을 제안했다.


### *G. Discussion*
결국 SASRec는 전형적인 Collaborative Filtering 의 일반화 버전이라고 이해할 수 있다.
- Reduction to Existing Models: SASRec 에서 사용된 다양한 기법들을 요리조리 뺀게 결국에는 기존의 FMC 혹은 FISM이다.


- MC-based Recommendation: first-order MCs, high-order MCs 두 가지 존재, high-order는 order L을 훈련 전 명시해주어야하고, L이 길다고 반드시 performance 향상이 보장되지 않는다는 단점 존재. 그러나 SASRec는 sparse 할 때는 좀 더 long-term을 참고해서, dense 할 때는 바로 전의 데이터를 참고하는 방식으로 L의 명시성문제를 해결했다.

- RNN-based Recommendation: attention의 등장으로 입지가 많이 줄었고, 우리의 SASRec는 RNN base model을 완전히 대체할 수 있다. 또한 maximum path length 또한 압도적으로 효율적이다.

### **4\. Experiment & Conclusion**
논문에서는 아래의 네 가지 질문에 답하는 방식으로 결과를 보여주고 있다.(유독 중국인들이 사용한 논문에서 이런 방식으로 Experiment 를 전개하는 방식을 즐겨 사용하는 듯하다. 어느 포인트에 초점을 맞출지 알려주는 것 같아서 좋은 것 같다.)
- RQ1: Does SASRec outperform state-of-the-art models including CNN/RNN based methods?
- RQ2: What is the influence of various components in the SASRec architecture?
- RQ3: What is the training efficiency and scalability (regard- ing n) of SASRec?
- RQ4: Are the attention weights able to learn meaningful patterns related to positions or items’ attributes?

Amazon, Steam, MovieLens 의 데이터 셋을 사용. 이때 Amazon은 sparse 하고, Steam과 MovieLens는 dense dataset에 해당된다.
user implicit interact로서 review 혹은 rating을 사용했다.(주로 해당 interact는 주로 explicit으로 사용하는 데 특이하다.)
우리 모델은 sequence를 중시하므로 action history의 timestamp도 훈련에 사용하였다.

비교군은
1. 순서 관계없이 feedback 만 고려하는 모델들
2. 순서를 고려하는 모델들
3. 순서를 고려하는 deep learning 모델들


Recommender Systems 에 자주 사용되는 Metric Hit@10과 NDCG@10을 짧게 설명하자면
- Hit@10: 실제 $S_u$ 번째 item이 우리가 예측한 score $r$ 의 top 10안에 들어갈 확률
- NDCG@10: NDCG@K는 가장 이상적인 추천 조합 대비 현재 모델의 추천 리스트가 얼마나 좋은지를 나타내는 지표이다. 그리고 정규화를 함으로써 NDCG는 0~1사이의 값을 가지게 된다. 
[출처: https://sungkee-book.tistory.com/11]

Evaluation에서 각 user 당 100가지의 negative items를 샘플링하고 ground truth 1개 를 결합한 총 101개의 item을 ranking하는 방식을 사용해 Hit@10과 NDCG@10을 평가하기로 한다.

비교 결과는 다음과 같다.

<img width="1190" alt="스크린샷 2023-12-22 오전 10 51 58" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/c53b19ec-75de-4c84-b252-d2b362a6211d">

a부터 e군과의 비교가 non-deep model과의 비교, f부터 b군과의 비교가 deep model과의 비교이다.


- RQ1: SASRec는 어떤 형태의 데이터셋에서든 다른 모델들을 뛰어넘었다.
이에 대해 저자는 SASRec가 different dataset에 따라 different range를 참고하여 추천을 해주기 때문에 모델이 좋은 성능을 보이다고 한다.


- RQ2: 모델 components 들을 하나씩 제거해가는 ablation study로 성능을 비교해하면서 퍼포먼스가 어떻게 변화하는지 관찰해보자.(학교 수업 프로젝트 중 ablation study가 무엇인지 몰라 보고서에 적지 않았다가 크게 감점된 적이 있었다. 그 뒤로 절대 안 까먹지 않게 되었다.)
<center>
<img width="581" alt="스크린샷 2023-12-22 오전 11 56 05" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/72ea9e31-2f0f-49d1-9b10-55087be5216b">
</center>
{: width="70%" height="70%"}


눈에 띄눈 부분은 item embedding 을 공유하지 않을 때 오버피팅이 발생한다는 것과 attention layer를 아예 삭제했을 때 자명하게도 성능이 매우 감소한다는 사실이다.

성능은 검증했으니 학습속도와 수렴속도는 어떨지 측정해보자.
- RQ3: 학습속도는 앞서 언급했듯 병렬로 처리되는 $O(n^2d)$ 기 때문에 빠르다는 것을 입증했다. 또한 결과에서 확인 할 수 있듯 다른 비교군에 비해 압도적으로 빠르게 목표치로 수렴하는 것을 볼 수 있다.

<center>
<img width="510" alt="스크린샷 2023-12-22 오후 12 02 53" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/5ef8e0ca-3d7b-414a-87a5-da137c0752d9">
</center>
{: width="70%" height="70%"}


그렇다면 attention weight들은 과연 모델을 학습할 때 정말로 유효한 역할을 할까?

아래의 히트맵을 살펴보자.
<center>
<img width="1237" alt="스크린샷 2023-12-22 오후 12 05 29" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/38a3ad89-f15c-42a7-bec0-96572339de9c">
</center>
{: width="70%" height="70%"}


1. (a)&(c)를 비교했을 때 sparse한 아마존 beauty set을 다룰 때 더 최근 아이템에 더 focus를 두는 것을 확인할 수 있었다.
2. (b)&(c)를 비교했을 때는 PE을 넣어주었을 때 모델이 더 집중해야할 곳을 잘 캐치해내는 것을 확인할 수 있었다.
3. high-layer의 attention block이 더 최근 기록에 의존한다는 사실을 확인할 수 있었다. 왜냐하면 실험 상 첫번째 block에서  멀리 떨어진 초창기 데이터를 이미 처리했기 때문에 두번째 block은 이를 고려하지 않아도 된다.


*<center>Overall, the visualizations show that the behavior of our self-attention mechanism is adaptive, position-aware, and hierarchical</center>*


결론을 내리자면,
RNN 없이 전체 user sequence를 모델링해서 사용자의 다음 추천을 예측하고 제안한다.attention 매커니즘을 이용해서 adaptively considers consumed items 하고, 이 과정에서 속도와 정확성 모두 잡았다는 의의를 가진다.


---
layout: page
title: Neural Graph Collaborative Filtering
description: >
   We develop a new recommendation framework Neural Graph Collaborative Filtering (NGCF), which exploits the user- item graph structure by propagating embeddings on it.
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
- embedding layer with initailization
- multiple embedding propagation layers
- prediction layer

### *2.1. Embedding Propagation Layers*

<img width="286" alt="스크린샷 2023-12-28 오후 5 38 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/6259b09b-32ef-4965-a1e0-b731c6f1c9c7">

&ensp; 기존 MF 방식이라면, 이 임베딩 matrix를 곧바로 layer에 집어 넣겠지만, NGCF에서는 propagation을 통한 refine 과정을 거친 후에 prediction layer에 넣는다.


### *2.2. Embedding Propagation Layers*

&ensp; 사실 이 부분은 Neural Graph Convolution 과 같다. 해당 algorithm은 한 layer를 거치면서 한 node & 그 node와 1hop 떨어진 Neighbor들의 정보들을 이용해서 그 node의 정보를 업데이트하는 방식이다. 이런 방식을 채택한다면 특정한 사용자들의 정보가 한 item의 feature에 포함될 수도 있고 어떤 상품들의 정보가 한 사람의 feature를 구성하는데에 관여할 수도 있다. 어떻게 정보를 전이시킬지는 아래의 message construction 과정에 서술되어 있다.


Message Construction
---

<img width="354" alt="스크린샷 2023-12-28 오후 6 05 16" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/7f0f6f37-d0c3-48c7-8849-90fa46e7e8fb">

&ensp; 아이템 i에서 유저 u로 흐르는 메세지 m을 논문에서는 위와 같은 식으로 구성했다. 
$ W_1 e_{i} $  로 item의 정보를 학습시켜서 u로 넘기고, u와 i의 연관성 혹은 interaction을 캐치하기 위해서 $ W_2 (e_{i} * e_{u})  $  로 element-wise multiplication을 적용했다. 마지막으로 학습된 두 message를 더하고, Laplacian normalization term으로 그 합을 나누어 주었다. (GCN 과 동일한 과정) 
이를 $ p_{ui} $ term이라고 일컫으며, 두가지 관점으로 해석이 가능하다.

- 기계학습의 관점: 사용자가 전에 상호작용했던 아이템이 현재 선호도에 얼마나 영향을 줄 수 있을지를 나타내는 것
- 메세지 전파의 관점: path lengh가 길어질 수록 전파되는 정보가 점점 줄어드는 것을 표현한 decay factor 라고 해석가능.

Message Aggregation
---
&ensp; 사용자 u와 인접한 item들의 정보를 agrregate 하는 단계이다.

<img width="335" alt="스크린샷 2023-12-28 오후 6 17 39" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/0d8a4577-2c49-4ed3-9794-0dc1731cd0bd">

&ensp; 위의 식에서 알 수 있듯이 자기자신으로 부터 온 message와 주위의 모든 item으로 부터 받은 message를 결합해주고 nonlinear term에 통과시켜주면 끝이다.
위 첨자 $ l $ 은 몇번째 layer인지를 나타내는 것이고 당연히 k 번째 layer 는 k hop 떨어진 node의 정보까지 취합할 수 있도록 도와주는 역할을 갖고 있다.
사용자 u1에 대해서 message aggregate을 1번 한다면 item 4에 대한 정보를 u1의 임베딩 벡터에 담을수 없겠지만 3번 한다면 담을 수 있다!

<img width="527" alt="스크린샷 2023-12-28 오후 6 21 08" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/52f02a74-010e-4b91-a280-9bb89757c3e5">


Propagation Rule in Matrix Form
---

&ensp; 위에서 언급한 과정을 통합하고 모든 노드에 대해 병렬적으로 처리해야하므로 matrix form을 빌려 계산을 진행한다.

<img width="484" alt="스크린샷 2023-12-28 오후 6 26 51" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f3924464-ea99-4ae9-a89c-d18f2aa55c5f">

$where $ 

$ L = D^{-1/2} A D^{-1/2} $ 

### *2.3. Model Prediction*

&ensp; L 번의 layer를 거친 경우, 한 사용자 u 에 대해서 l 개의 임베딩 벡터가 만들어지고 해당 벡터들을 각각 다른 부분으로 부터 온 메세지를 중요시하기 때문에 사용자 취향을 복합적으로 담고 있다. 
이를 반영해주기 위해서 prediction layer에서는 취향을 알고 싶은 사용자 와 item의 피처 벡터들을 모두 concatenate 해주고 inner product 해주면 땡이다.

$ y_{pred} = e^{*T}_{u} e^{*}_{i} $ 

&ensp; 결과물로써 $ L \times L $ matrix가 나오고 해석하면 $l \in L$ 을 만족하는 사용자 u 의 $l$ 번째 embedding 과 $l' \in L$ 아이템 i 의 $l^{'}$ 간의 점수를 알아볼 수 있다.

### *2.4. Optimization*
&ensp; Loss function으로 BPR Loss를 사용한다.
BPR 손실함수는 observed interaction 이 unobserved interaction보다 더 큰 점수를 가져가야한다는 것을 전제로 한다.


<img width="343" alt="스크린샷 2023-12-28 오후 7 18 55" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/b9bc0f6f-1610-49bb-9bbd-f060ba3244b0">

&ensp; $i$ 는 obseved interaction이 있는 item으로 부터, $j$ 는 그렇지 않은 경우를 나타낸다.

&ensp; 그리고 그 외의 parameter 인 $E$ 와  1~L번째 까지 $ W$는 $\theta$ parameterized L2 norm 으로 뭉뚱그려놓았다.



## **4\. Experiment & Conclusion**

&ensp; 논문에서는 아래의 세 가지 질문에 답하는 방식으로 결과를 보여주고 있다.
- RQ1: How does NGCF perform as compared with state-of-the-art CF methods?
- RQ2: How do different hyper-parameter settings (e.g., depth of layer, embedding propagation layer, layer-aggregation mechanism, message dropout, and node dropout) affect NGCF?
- RQ3: How do the representations benefit from the high-order connectivity?

<img width="598" alt="스크린샷 2023-12-28 오후 10 41 08" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/e4a896bc-b724-4110-a02b-fa222ff29008">

&ensp; (RQ1) 표에서 확인할 수 있듯 NGCF with 3 layers 가 모든 데이터 셋과 metrics에서 비교군을 압도했다. 

<img width="568" alt="스크린샷 2023-12-28 오후 10 53 35" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/a477e959-326c-4a11-a8ee-ddf0f0922669">

&ensp; (RQ2) 첫째로, NGDF의 layer 수를 조절해보았을 때 대체로 layer가 깊을 수록 성능이 향상되었다. 그러나 Yelp 셋은 오버피팅이 일어나기도 하였다.

&ensp; 두번째로, NGCF의 embedding propagation이 성능에 얼마나 큰 영향을 주는지 알아보기 위해서 실험을 해보았다. message passing function 부분인 $m_{u \leftarrow i}$ 를 다른 알고리즘으로 대체해서 사용했다.

비교 결과는 다음과 같다.

<img width="532" alt="스크린샷 2023-12-28 오후 11 03 35" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9f973156-2e48-4393-b396-d9fb5723d237">

&ensp; 이를 통해서 성능향상을 위해 노드 간의 message passing이 매우 중요하고, layer-aggregation mechanism이 중요하다는 것을 알 수 있다.

&ensp; 마지막으로, dropout의 성능테스트를 해보았다. 둘 중에서 node dropout이 비교적 더 좋았다고 한다.



<img width="573" alt="스크린샷 2023-12-28 오후 11 09 52" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/06af7d01-282c-4850-a6ef-d7b1bd7138b7">

&ensp;  (RQ3) NGCF와 MF를 비교했을 때 둘 다 아이템들의 클러스터링이 확실하게 되었고 사용자와 뚜렷하게 연결된 점을 확인할 수 있었다. 그리고 이 사항은 NGCF-3에서 더욱 도드라진다. 결국 NGCF가 collaborative signal을 더 잘 표현했다고 볼 수 있다.
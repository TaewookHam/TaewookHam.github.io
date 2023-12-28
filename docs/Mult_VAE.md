---
layout: page
title: Variational Autoencoders for Collaborative Filtering
description: >
   
use_math: true
sitemap: false
---
## **0&1 Abstract & Introduction**


## **2. Methodology**
---


## **4\. Experiment & Conclusion**
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


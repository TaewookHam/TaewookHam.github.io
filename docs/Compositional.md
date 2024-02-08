---
layout: page
title: Compositional Fairness Constraints for Graph Embeddings
description: >

use_math: true
sitemap: false
---

ENC : Embedding Function

Heterogeneous Graph이란?: 이종그래프
    
    그래프 내의 노드 및 에지가 여러가지 유형에 속하는 그래프 

noise contrastive estimation란?
    
    모델이 실제 데이터 샘플과 인공적으로 생성된 노이즈 샘플을 구별할 수 있도록 하는 것입니다

parity: 동등성 

Embedding Lookup 이란?
    
    "Embedding lookup"은 임베딩된 벡터를 찾기 위한 과정을 나타냅니다. 주어진 단어에 대한 임베딩 벡터를 얻기 위해 룩업(lookup) 연산을 수행합니다. 이는 사전에 학습된 임베딩 행렬에서 해당 단어의 행을 찾아오는 과정입니다.



## **1.Introduction**
---
Social graph 에서 만들어진 embedding을 학습할 때는 sensitive attribute 을 포함시킬지 말지를 결정할 수 있게 만드는 것은 중요하다.

Learning low-dimensional embeddings 중요 -> fairness constraint 가 요구되었을 때 학습하는 방법은 x -> 우리가 제안하겠다. 
how? Compositional, Adversarial 하면서 invaraint to sensitive attribute 하게 

학습하는 그래프 임베딩이 특정 민감한 속성에 대해 변하지 않도록 학습되었다고 설명.

각자의 attribute을 넘어서 극단적으로 individual edge edge 까지도 무시할 수 있도록 fair한 것까지 필요하다.

Contributions
1. Adversarial filter 
2. -> 그리고 optional+combinational 하게 적용가능
3. Unseen data 에도 적용가능 

## **3. Preliminaries**
---
- Notatoins
    **edge triple(그냥 edge 아님!)** : $e = <u,r,v> \in \mathcal{E}$ , where $u,v \in \mathcal{V}$ and $r \in \mathcal{R}$

    각 노드는 particular type $\mathcal{T} \subseteq \mathcal{V}$ 에 속한다고 가정한다. 그리고 relation $r$ 은 각 노드 type에 따라서 결정된다.

- Relation Prediction Task

    Train Edge $\mathcal{E_{train}} \subset \mathcal{E}$ 와 negative edge $\mathcal{\bar{E}}$ 를 가정 한다면, 다음과 같이 scoring function s 를 학습시키는 것을 목표로 한다.

    $s(e) > s(e')$ 

    학습된 score function이 이상적으로는 negative edge 보다 실제 edge에 높은 점수를 부여할 수 있어야한다.

- Embedding-based Models

    함수 s 는 엠베딩 된 $e$ 를 input으로 받고 그 edge 가 실제 그래프에 존재할 likelihood 를 계산한다 (i.e. s(e) above).

    <img width="403" alt="스크린샷 2024-01-25 오후 3 25 01" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/7aec1617-1539-41ed-b69b-f9f81b2e2424">
    
    여기서 target은 function s 로서 negative 와는 다르게, positive e에 더 가까운 특성을 s 가 학습할 수 있도록 한다. 

    찾아보니까 margin + positive - negative 이런 꼴이던데?

- Fairness

    편의를 위해서 모든 user가 $\mathcal{T^*}$ 타입이라고 가정한다. 그리고 K 개의 sensitive attributes set $\mathcal{A_k}$ 를 가지고 있다고 가정한다. 
    그리고 노드와 에지가 다른 어떤 attributes 도 가지고 있지 않다고 가정하자.
    -> 여기서 의문점: 아니 다른 어떤 attributes도 없으면 어떻게 추천을 할 수 있는거임?? 일단 할 수 있다 치자 

## **4. Invariant Graph Embeddings**
---
원래는 Eq(3) 처럼 recommendation(score of the edge) 이  sensitive attribute과 어떠한 관계도 없는 것을 다음과 같이 표현할 수 있다. 

<img width="511" alt="스크린샷 2024-01-25 오후 3 50 09" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f4708d71-912e-4f05-b7d3-d3502e9c0152">

그러나 가능한 모든 edge 들에 이런 independence를 부여하는 것을 intractable 하므로 한 가지 가정을 추가한다.

    (Assumption) score function s(⟨z_u, r, z_v ⟩)가 오직 u의 임베딩 z_u를 통해서만 u에 의존한다고 가정한다면 -> 표현 불변성이라고 부르는 아래의 식을 적용하여 모든 에지 예측에 대해 Eq(3)의 독립성을 보장할 수 있습니다. 

<img width="444" alt="스크린샷 2024-01-25 오후 3 51 09" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/3ea70c7a-246c-4895-9ba2-cfc5f5cffc83">

--> 사실 잘 이해 안 됨

### 4.2. Model Definition

- Compositional Encoder
    
    각 sensitive attribute K 개마다 filter function 이 존재.
    그리고 combine 가능 -> 사실 여기는 매우 자명
    u 마다 bernoulli binary mask가 있어서 매번 S를 결정해준다. 

    그리고 이렇게 random 하게 mask를 뽑아주는 것은 unseen combination에 대해서도 invariant embedding 을 만들 수 있다는 장점이 있다. 

- Adversarial Loss 

    Adversarial Loss를 사용: $L_{edge} + \lambda\sum \sum$ 꼴 

    <img width="574" alt="스크린샷 2024-01-25 오후 4 34 23" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/0625a171-59c1-46a5-ae4b-ab65337ef282">

    k 번째 sensitive attribute을 예측하려고 하는 discriminator $D_k$

    T 개의 minibatch 는 embedding 을 위해서 나머지 T' 개는 discriminator를 위해서 사용
-----

정리하자면

그래프 임베딩을 통해서 사용자에게 아이템을 추천해줄 때, 사용자가 자신의 민감정보와(ex 성별) 관계없이 영화를 추천받을 수 있어야 한다. 

추천시스템이 결국 edge prediction 임.

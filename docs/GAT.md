---
layout: page
title: Graph Attnetion Networks
description: >
   We present graph attention networks (GATs), novel neural network architectures...
use_math: true
sitemap: false
---
![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/43926d34-174d-42f7-88d9-c63423724c46){: width="80%", height="80%"}

## **0. Abstract**
---
&ensp; Graph Convolutional Network에서 k-layer를 거친 한 노드는 기본적으로 k만큼 떨어진 곳에 있는 neighbor node의 정보까지 얻을 수 있다는 특징이 있다.
해당 특징을 이용한다면 추가적인 matrix 연산이나 그래프 구조를 알 필요없이 특정 노드에 가중치를 부여할 수 있다.(어디에 attention 할지 특정할 수 있기 때문에)
그래프 연구의 Graph Convolution Network 와 NLP분야에서 적용된 Attention 기법을 혼합한다면 transductive 뿐만 아니라 inductive learning 까지도 GCN으로 해결할 수 있다는 것이 해당 논문에 전하고자 하는 얘기다.

#### *Inductive Learning vs Transductive Learning*  
&ensp;  inductive learning은 학습 과정에서 test set에 대해서는 아예 모르는 상태로 train set만 가지고 general한 규칙을 생성하는 것을 의미하고, transductive learning은 학습 시 train set과 test set모두를 이용하는 방식이다. 다만 test set의 label은 관측만 했지, 레이블은 알지 못하는 상태이며, 학습이 진행되는 과정에서 train data의 정보와 레이블 정보등을 활용해서 test set의 레이블을 추론할 수 있다. inductive는 general 하게 적용될 수 있는 predictive model을 만들고, transductive는 model을 만들지 않는다는 특징을 가지고 있다.

&ensp; 그래프 관점으로 적용시켜보자면, Transductive learning 이란 그래프 상의 일부 노드와 에지의 ground truth를 아는 상태에서 나머지 노드와 에지의 값을 추정하는 것이다. 즉, known 노드와 에지를 가지고 supervised leaning을 해서 연결된 unknown 노드와 에지를 prediction 하는 방식을 의미한다. Inductive learning 이란 ground truth를 알고있는 graph(들)에 대해서 모델을 학습한 후, 전혀 새로운 graph에 대해 노드와 에지의 값을 추정하는 것. 우리가 일반적으로 알고 있는 supervised learning의 형태.

  [출처: https://komputervision.wordpress.com/2020/03/24/inductive-learning-vs-transductive-learning/]

## **1. Intro**
---
&ensp; CNN은 grid-like structure를 사용해서 다양한 분야의 머신러닝 문제를 해결한 뛰어난 알고리즘이다. 그러나 현실세계의 문제들은 grid-architecture로만 해결할 수 없는 것들이 많다. 그리고 그중에 하나가 graph 분야이다. 그렇기 때문에 Neural Network를 그래프에 적용해서 다양한 문제를 해결해보는 노력들이 과거로 부터 쭉 이어져왔다. 대표적으로 recursive neural networks to acyclic graph, GNN이 있었고, 후에 spectral 과 spatial 기법, 두 분야로 쪼개지게 된다. 오늘 우리가 살펴 볼 기법은 spatial한 GCN과 Self-attention을 적용한 GAT이다.
GAT는 attention의 다음 특징을 상속받는다.

- parellelizable : transformer가 단어를 serial로 받아들이는 것이 아니라 문장하나를 통째로 받아들여 단어를 parallel 하게 처리하듯, 우리도 병렬적으로 노드와 에지 정보를 받아들일 수 있다.
- specifying arbitrary weights : transformer가 어떤 word에 집중할지 표현할 수 있는 것처럼 우리도 weight를 줄 수 있다.
- inductive : translator가 inductive해야 하는 것은 매우 자명하다. 이것처럼 우리의 GAT도 아예 처음보는 그래프에도 적용가능하도록 만들어야한다.


## **2\. GAT Architecture**
---
&ensp;  Attention의 구조를 이해했다면 GAT의 구조는 자명하게 이해할 수 있다.
input layer가  $ \textbf{h} = \left\{h_1, h_2, ...,h_N\right\}$ 다음과 같이 구성되어 있다고 하자. 이때 각각의 벡터 $h_i$는 $F \times 1$ 차원의 벡터이다.
그리고 Convolution weigth matrix $W$는 $F' \times F$ 라고 하자.
그렇다면 input이 한 번의 convolution layer를 거치게 된다면 $\textbf{h}'= W\textbf{h}$ $(F' \times N)$ 의 output feature vectors matrix가 나올것이다. 

&ensp; 이때 graph attention은 다음의 과정을 거친다.

1. 두 feature vector $h_i$와 그와 맞닿아 있는 j번째 벡터 $h_j$가 서로 얼마만큼의 연관성을 가지는지 알아보기 위해서 두 벡터를 이어 붙인 후 attention function $a$에 input으로 입력한다. 입력에 대한 output으로 attention coefficient $e_{ij}$를 얻는다. 단, 이때 attention function $a$는 FNN + activation function 으로 구성되어 있다.
2. 일반적으로 $i$ th node에 연결된 노드는 한 개가 아닐 것이다. $i$ th node에 연결된 노드가 k개 있다고 할 때 모든 k개의 노드에 대해서 각각의 $e_{ik}$를 구해 준 뒤, softmax를 취해 [0,1] 사이의 값으로 normalization을 해준다.
3. node $j$가 node $i$와 얼마나 연관이 있는지 이제 알았다. attention 에서 Q*K에서 연관성을 알아낸 뒤 Value 값에 곱해주 듯, 각각의 $e_{ij}$를 처음의 $h'_j$에 곱해주고 summation을 해주어 single attention layer를 거친 $h'_i$를 구해줄 수 있다.
4. 그러나 Vaswani et al. (2017)의 Transformer 논문에서는 multi-head attention 을 사용해 K개의 컨셉을 가진 attention을 진행해주어 안정성과 성능을 동시에 높이는 방식을 사용해준다.  3번의 과정을 하나의 i에 대해서 K번 반복한 후 모든 output vector를 이어 붙여주면 끝이다. 이어붙인다면 $K \times F'$의 차원을 갖게 될 것이다.
 (각 head 당 feature dimension =  $d_{model}/K$)
5. 그러나 마지막 layer에서는 벡터의 차원을 $K \times F'$가 아닌 $1 \times F'$ 로 맞추어 주어야하므로 concatenation 대신 averaging을 사용한다는 차이점이 있다.



## **2-2\. Related works**

GAT의 complexity는 다음과 같이 표기할 수 있다. O(|V|FF' + |E|F')
- GCN과는 달리 node마다 importance weight를 두고 계산한다.
- 미리 그래프의 구조를 몰라도 사용가능하다.


### **3\. Experiment & Conclusion**

실험결과에 대해서는 자세하게 다루지 않겠다.

GAT가 기존 연구보다 더 뛰어난 점은 계산 효율적이면서도 GCN을 포함하는 기존 연구들보다 동등하거나 그 이상의 성능을 보여줬다는 점이다.
모델 자체가 어떤 노드에 더 가중치를 주어야하는지 알고 있다. 또한 그래프의 전체 구조를 미리 알고 있을 필요가 없으므로 기존 spectral-based analysis의 문제점을 해결했다는 점에서 의의가 있다.

<div class="gumroad-product-embed" data-gumroad-product-id="nuOluY"><a href="https://gumroad.com/l/nuOluY">Loading…</a></div>



[^1]: If you are a fan of the old two-column layout, or don't like modern design tropes such as mega headlines, Hydejack lets you revert these changes on a case-by-case basis via configuration options.

[^2]:
      Search was mainly tested for English and German. Please let me know about issues in other languages. 
      While I've tried to find a multi-language solution, most showed drastically worse  results for the English base case.
      If you're technically inclined, you can adopt the code located in `_includes/js/search-worker.js` to your needs.



---
layout: page
title: Attention Is All You Need
image: 
  path: https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/0b692673-43be-4185-a40c-ff828aa4cd75
description: >
  We propose a new simple network architecture, the Transformer, 
  based solely on attention mechanisms, 
  dispensing with recurrence and convolutions entirely.
sitemap: false
---

협업 필터링이라고 하는 Collaborative filtering 에 Neural network 를 덧입히고자 하는 노력에서 나온 것이 이번 논문에서 소개하고 있는 NCF이다.

Implicit feedback을 기반으로 하는 Collaboratvie Filtering는 사용자의 선호도를 잘 반영하지 못한다는 한계점이 있다.

다음과 같이 M by N으로 구성된 User-Item interaction matrix Y가 있을 때,

implicit feedback 이라 함은 1일 때 상호작용이 있었고 0일 때 상호작용이 없었다로 구분한다. 이때 binary number가 user의 선호도를 나타내지는 않는다고 한다.

이러한 단점을 논문에서는 'noisy signal' 이 껴있다고 언급하고 있고, 관측되지 않은 부분의 선호도를 예측해 랭킹을 매겨야하는 추천시스템에서는 이러한 단점은 치명적일 수 밖에 없다.

[##_Image|kage@uWkui/btsBXLq2xh8/lx1BmpKKgKlPYdCFfPk1TK/img.png|CDM|1.3|{"originWidth":1142,"originHeight":174,"style":"alignLeft","width":466,"height":71,"filename":"스크린샷 2023-12-13 오후 12.54.27.png"}_##]

### **2.1 Learning from Implicit Data**

---

model-based 추천시스템에서 사용하는 interaction function f의 파라미터 $\\theta$를 추정하는 방식은 크게 두 가지가 있다.(후에 더 구체화 시킬 것.)

-   첫번째는 pointwise learning 방식이다. 각각의 $y\_{ui}$ 한 포인트마다 예측값 $\\hat{y}\_{ui}$ 와 비교하는 방식이다(Squared loss).이 negative data의 부재를 해결하기 위해, 관측되지 않은 부분은 전부 negative로 여기거나, 관측되지 않은 부분에서 negative를 샘플링한다.
-   두번째는 pairwise learning 방식이다. positive, negative 쌍끼리 비교하고 관측된 엔트리 $\\hat{y}\_{ui}$ 가 관측되지 않은 $\\hat{y}\_{uj}$ 간의 margin을 최대화 한다는 전략이다.

NCF는 interaction function f를 parametrize하므로 둘 다 사용가능하다. -> 왜?

그러나 이번 기회에는 pointwise 방식을 사용한 loss function을 사용할 것. 

### **2.2 Matrix Factorization**

---

Recommender Systems에서 행렬분해라 함은 아래와 같이 나타낼 수 있다.

User와 Item의 latent space dimenstion 을 K라고 했을 때, M x K, K x N 인 두개의 matrix로 분해가능.

아래의 예시는 강의에서 들었던 ppt자료의 일부이다. 

[##_Image|kage@JevRk/btsBJevHxGc/ZDNvKKGEckiVYohzKSjmi1/img.png|CDM|1.3|{"originWidth":1878,"originHeight":1000,"style":"alignCenter","width":593,"height":316}_##]

[##_Image|kage@EFtCF/btsBRQATHFH/NG8Nlj3s36K43vsbEMkA4K/img.png|CDM|1.3|{"originWidth":608,"originHeight":114,"style":"alignCenter","width":224,"height":42,"caption":"Matrix factorization","filename":"스크린샷 2023-12-13 오후 2.52.34.png"}_##]

u,i번째 matrix의 숫자를 예측하기위해 factorization과 inner product를 사용하는 것은 자명하다. 그러나 단순히 inner product만 사용한다면 MF의 표현성에 제약을 두게 된다. 논문에서는 아래의 예시를 들어 한계점을 시사한다.

내용을 요약해서 말하자면, 좌측 하단과 같은 고차원의 matrix를 저차원의 latent space로 매핑했을 때 차원이 축소되는 과정에서 벡트들 간의 관계가 명확히 표현되 지 못할 수도 있게 된다. 그렇다고 latent space의 차원을 늘린다면 오버피팅이 생길 가능성이 높아진다.

[##_Image|kage@bhZdzz/btsBMQgYUjH/6S4paoHTVfhKgO3j42zUi1/img.png|CDM|1.3|{"originWidth":1094,"originHeight":494,"style":"alignCenter","width":505,"height":228,"filename":"스크린샷 2023-12-13 오후 2.55.11.png"}_##]

이 문제를 해결하기 위해서 앞서 DNN을 사용해 generality를 잃지 않으면서도 표현력을 높일 수 있는 방법을 채택하였다.

## **3\. Neural Collaborative Filtering**

### **3.1 General Framework**

---

기본적인 NCF의 큰 틀은 아래와 같다.

[##_Image|kage@Wjs8L/btsBTZxMv73/AN6pfctu0g6myRg1tetsyk/img.png|CDM|1.3|{"originWidth":1156,"originHeight":686,"style":"alignCenter","width":487,"height":289,"filename":"스크린샷 2023-12-13 오후 4.45.19.png"}_##]

feature vector $v^{U}\_{u}$, $v^{I}\_{i}$ 가장 아래쪽의 sparse input layer에 해당.

그리고 그위에 sparse -> dense 로 만드는 fully connected layer 역할을 하는 embedding layer가 존재. 이때 P와 Q는 latent factor matrix라고 한다.

그리고 만들어진 embedding vector를 NN의 input으로서 본격적으로 사용가능하다.

설명한 내용을 수식으로 표현하면 다음과 같다.

[##_Image|kage@bQHMTK/btsBT19IeXy/MF4AtnZbDLjj3D9Xsd2Bx1/img.png|CDM|1.3|{"originWidth":1076,"originHeight":72,"style":"alignCenter","width":579,"height":39,"caption":"NCF&amp;rsquo;s predictive model","filename":"스크린샷 2023-12-13 오후 4.50.23.png"}_##]

#### **3.1.1 Loss**

---

[##_Image|kage@MZHRG/btsBR9mHh6w/KaRuoTHJDUMboLhm4IlDXK/img.png|CDM|1.3|{"originWidth":692,"originHeight":134,"style":"alignCenter","width":392,"height":76,"filename":"스크린샷 2023-12-13 오후 4.53.14.png"}_##]

pointwise loss로 원래는 weighted squared sum error를 사용하려 했으나 이 loss는 관측치들이 가우시안 분포를 따라야한다는 가정을 만족해야한다. 우리는 0 또는 1의 binary value를 다루고 있으므로 이를 변형해 확률적인 접근 방식으로 pointwise NCF학습을 해야한다. 

$y\_{ui}$ 가 1일 때, i가 u와 관련이 있다고 보자.

그리고 $\\hat{y}\_{ui}$가 관련있을 확률 p라고 정의한다.

각 point 마다 p를 최대화 해야하므로 likelihood를 최대화하는 것과 같다.

정리하면 아래와 같이 cross-entropy식과 같다는 것을 확인 가능하다.

[##_Image|kage@u8ckW/btsBXKMT7yg/YAq0sxWjKdqRYpiXQT5XFk/img.png|CDM|1.3|{"originWidth":974,"originHeight":140,"style":"alignCenter","width":410,"height":59,"caption":"likelihood of probability","filename":"스크린샷 2023-12-13 오후 4.58.42.png"}_##][##_Image|kage@bqhG2j/btsBY8GvStp/hbnx1bKHZO4FKI3nstDQK1/img.png|CDM|1.3|{"originWidth":1042,"originHeight":262,"style":"alignCenter","width":441,"height":111,"caption":"same with binary cross entropy loss","filename":"스크린샷 2023-12-13 오후 4.58.55.png"}_##]

정리하면, 사용자와 아이템간의 연관성이 있냐 없냐를 학습하고, 학습된 파라미터를 기반으로 관측되지 않은 부분을 예측할 수 있도록 만들어야 하는데, 이 과정에서 binary value 1일 확률을 맞추어야 하는 것이다.

논문에서는 이를 기반으로 하는 세 가지 framework를 제시하는데 1) GMF 2)MLP 3)NeuMF 를 순서대로 소개한다. 차례대로 살펴보자. 각 프레임워크는 굉장히 이해하기 쉽다.

### **3.2 Generalized Matrix Factorization (GMF)**

inner product를 사용한 MF의 General form이다.

[##_Image|kage@ooEX5/btsBR8nNUix/yJcN1bSEWOCezDpMeFcSZk/img.png|CDM|1.3|{"originWidth":504,"originHeight":90,"style":"alignCenter","width":258,"height":46,"filename":"스크린샷 2023-12-13 오후 5.16.23.png"}_##]

여기서 $h^T$가 \[1,1,,...,1,1\]이고 $a\_{out}$이 identity function이라면 specific한 MF와 같은 form이라는 것을 알 수 있다.

그러나 우리는 deep network 방식을 곁들이기로 했으므로 $a\_{out}$에 sigmoid를, $h$에 log loss 로부터 학습된 weight들을 사용한다.

### **3.3 Multi-Layer Perceptron (MLP)**

흔히 알고 있는 Multi-layer Perceptron 방식으로 input으로 concatenated vector 를 사용한다는 것이 특이점.

[##_Image|kage@BwlQA/btsBXJnhp4Q/gTQQKmvUPI7FUvriI9vTT0/img.png|CDM|1.3|{"originWidth":652,"originHeight":426,"style":"alignCenter","width":347,"height":227,"filename":"스크린샷 2023-12-13 오후 5.26.58.png"}_##]

### **3.4 NeuMF**

[##_Image|kage@oTWiW/btsBVDAMQiC/YxkJKl2ebr0gNk88yDQkp0/img.png|CDM|1.3|{"originWidth":1260,"originHeight":822,"style":"alignCenter","width":418,"height":273,"filename":"스크린샷 2023-12-13 오후 5.28.25.png"}_##]

더 flexible 한 융합모델을 제시하기 위해서 GMF 용 임베딩 벡터와 MLP용 임베딩 벡터 두 쌍을 user 와 item에서 각각 생성한다는 것이 특징.

예시용 architecture에서 확인할 수 있듯, 두 framework를 parallized하게 돌려 output layer $\\phi^{GMF}$, $\\phi^{MLP}$를 만든다. NeuMF layer에서 이어 붙인다. 그리고 final layer에서 GMF의 미리 학습된 hyperparameter h<- $\\alpha$ 와 함께 activate 해주면 완성.

[##_Image|kage@bRMjLf/btsBUMx0gFJ/AkPwOTHKbyVsetcE5ce96k/img.png|CDM|1.3|{"originWidth":1130,"originHeight":364,"style":"alignCenter","width":484,"height":156,"filename":"스크린샷 2023-12-13 오후 5.32.57.png"}_##]

### **4\. Experiment & Conclusion**

자명하게도 기존의 state-of-art보다 더 나은 성능을 보여주었고 hidden layer가 깊어질 수록 더 좋은 성능을 보여주었다고 결과를 말하고 있다.

MF에다가 NN을 적용시키는 과정에서(Neural Network architectures for collaborative filtering) Linear + Non-linear의 장단점을 잘 보완해 뛰어난 성능을 보여준 NeuMF를 만든 것에 의이가 있다. 

그리고 이 모델은 다른 추천시스템을 위한 딥러닝 모델 개발에도 사용할 수 있다는 장점이 있다.

<div class="gumroad-product-embed" data-gumroad-product-id="nuOluY"><a href="https://gumroad.com/l/nuOluY">Loading…</a></div>



[^1]: If you are a fan of the old two-column layout, or don't like modern design tropes such as mega headlines, Hydejack lets you revert these changes on a case-by-case basis via configuration options.

[^2]:
      Search was mainly tested for English and German. Please let me know about issues in other languages. 
      While I've tried to find a multi-language solution, most showed drastically worse  results for the English base case.
      If you're technically inclined, you can adopt the code located in `_includes/js/search-worker.js` to your needs.



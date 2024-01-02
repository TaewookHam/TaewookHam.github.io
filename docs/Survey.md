---
layout: page
title: A Survey on the Fairness of Recommender Systems
description: >
   To fill this gap, we review over 60 papers published in top conferences/journals, including TOIS, SIGIR, and WWW.

use_math: true
sitemap: false
---
# **0&1. Abstract & Introduction**
---

사용자의 background 에 관계없이 공정한 추천을 제시해야하므로 fairness는 추천시스템에서도 매우 중요하다.

Survey 에서 집중하고 있는 포인트는 다음과 같다.

1. 공정성이 중요하다면, 추천시스템에서의 fairness는 정확히 어떻게 정의되는가?

2. fairness issues를 어떻게 분류할 수 있을까?

3. fairness를 측정할 metric은 무엇이 있을까?

5. 불공정성을 해소하기 위한 method은 어떤 것이 있는가?

6. 실험을 위한 데이터셋은 무엇이 있는가?

7. future work

# **2. DEFINITIONS OF FAIRNESS IN RECOMMENDATION**
---
추천시스템에서 말하는 '공정하다' 라는 뜻의 정의는 무엇일까?

사실 '공정'이라는 단어는 추상적인 단어이면서도 상대적인 단어이기 때문에 딱 잘라 말하기는 굉장히 어렵다.
그렇기 때문에 추천시스템의 공정성을 연구하는 다양한 논문에서도 각자 다른 방식으로 'fairness'를 해석하고 있다.

우선 과정을 중시하는 공정성과 결과를 중시하는 공정성이 있다.
- Process Fairness(Procedure justice): 모델이 인종,성별같은 요소를 학습 피처에 넣느냐 안 넣느냐, 혹은 그 외의 학습요소들이 편향되지는 않았는가? 등을 체크
- Outcome Fairness(Distributive justice): 모델을 통과해서 나온 결과물이 특정 인종에게 유리하게 작용하는가? 등을 체크

우리는 outcome 쪽에 더 초점을 둘 것이고, outcome fariness은 다시 target과 concept를 기준으로 나눌 수 있다.

자세한 분류는 아래의 이미지와 같다.

<img width="1071" alt="스크린샷 2024-01-02 오후 3 30 37" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/9bed107c-7895-43e2-8d22-6b0a04327ce3">

그러나 정의가 여러개 있으므로 자연스레 어떤 정의가 우선시 되어야 하는가? 라는 의문이 따라옴. 대부분의 연구는 single definition만을 다루기 때문에 이부분은 더 탐구여지가 남아있다.

또한 저자는 공정성 Bias, Diversity, and Privacy 등의 조건을 만족하면서도, 너무 얽매이지도 말아야 한다고 언급하고 있다.(공정이라는 개념은 참 어려운 것 같다...)

fairness의 정의가 정리되어 있는 테이블을 첨부하겠다.

<img width="1152" alt="스크린샷 2024-01-02 오후 3 37 20" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/ee4df7eb-4a14-427e-a1a3-59a4444d81b3">

# **3. VIEWS OF FAIRNESS IN RECOMMENDATION**
---

- Subject:

- Granularity(세분성):

- Optimization Object:

# **4. MEASUREMENTS OF UNFAIRNESS IN RECOMMENDATION**
---

- Metrics for CO

- Metrics for CA

- Metrics for Others

# **5. METHODS FOR FAIR RECOMMENDATION**
---
1. Data-oriented method: 데이터를 건드려서 불공정성을 해결하는 것
2. Ranking methods: 모델이 공정한 추천을 학습할 수 있도록 만드는 것
3. Re-ranking: ouput이 공정할 수 있도록 건드리는 것


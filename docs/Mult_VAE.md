---
layout: page
title: Variational Autoencoders for Collaborative Filtering
description: >
   We extend variational autoencoders (vaes) to collaborative filtering for implicit feedback.
use_math: true
sitemap: false
---

## **들어가기전**

1\. multinomial distribution이란?

-   다항분포 : 실험결과가 k개인 확률 실험을 n번 반복하였을 때, 각 범주에 속하는 횟수를 확률변수로 하는 분포

2\. Paper의 목적

-   VAE를 사용해서 기존의 linear 한 collaborative filtering 을 non-linear하게 표현함으로써  확률분포를 이용해 사용자 취향을 예측할 수 있다.
-   기존 top-N ranking loss를 multinomial likelihoods를 이용함으로써 더 정확하게 근사할 수 있다.

## **2\. Method**

### **2.1 Model**

![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/3b2eedba-7c8c-4989-b23b-000ec7c7aac6)

paper 에서 encoder 와 decoder 의 framework를 간략하게 위와 같은 식으로 표현하였다. 이를 이미지로 표현하면 아래와 같다.
[출처: https://huidea.tistory.com/297]

![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/1c92ebd8-8fb0-45aa-a243-989d033a9814) 

어떤 한 user의 latent vector $z_{u}$ 가 가우시안 분포를 따른다고 가정한다. 이는 뒷 부분에서 VAE의 가정으로 설명할 것이다. 이 때 함수 $f_{\theta}(z_{u})$ 는 latent vector 를 I 차원으로 맵핑하는데 $(f_{\theta}(z_{u}) \in R^{K \times I})$ , 총 $I$ 개의 각 아이템과 유저 u가 관련있을 확률로 맵핑시킨다고 이해하면 된다.  그리고 이 확률분포를 따르는 다항분포를 $x_{u} \leftarrow  Mult(N_u,\pi(z_{u}) )$ 라고 정의했다.

VAE와 마찬가지로 latent space를 생성하기 위해 sampling 하는 부분을 encoding part, 함수 $f_{\theta}()$ 가 적용되어 x로 되돌리는 부분을 decoding part라 이해할 수 있다.


원래의 VAE에서는 디코더 분포 p 를 베르누이 분포 혹은 가우시안 분포로서 가정했지만 해당 paper에서는 세가지를 제시한다. 그리고 핵심 아이디어는 당연히 multinomial distribution이다. 그리고 logistic log-likelihood는 사실 multinomial log-likelihood의 특별케이스이다.

- Multinomial log-distribution(cross-entropy loss for multi-class classification)
- Gaussian log-likelihood
- logistic log-likelihood(cross-entropy loss for binary classification)


![image](https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/88aaa347-4890-4a14-96b0-9b6b17e08cbd)


Multinomial distribution은 $x_u$ 가 0이 아닌 항에 대해서 더 보상을 주는 방식이다. 위의 framewrok 그림을 보면 input x에서 원래 까만 부분은 output $\pi(x)$ 에서 'mass'를 더 많이 할당 받았다. 그러나 이 mass라는 것은 결국 normalize된 확률이고, 모든 아이템 I 가 전체 확률 1을 나누어 먹어야 한다. 그렇기 때문에 모델은 더 많이 클릭할 것만 같은 item에 더 큰 확률을 부여하게 된다. 아이템은 한 사용자에 대해 상대적인 확률을 각각 가지고 있기 때문에 줄 세우기 가능, top N 개를 사용자에게 추천해주면 끝. -> 이러한 이유로 저자는 multinomial distribution이 추천 시스템에 사용되기에 알맞다고 판단.

### **2.2 Variational Inference**

VAE를 알고 있다는 가정하에 논문을 읽으며 내가 헷갈렸던 부분 위주로 설명하겠다.
([https://arxiv.org/abs/1312.6114](https://arxiv.org/abs/1312.6114))

### *Encoder*
z를 샘플링할 때,
각 사용자마다 latent dim K 개 만큼의  $\mu_{u}$와 $\sigma_{u}$ 를 만들어주는 것은 매우 번거로운 일이다. 이에 대한 해결책으로 우리는 function $g_{\phi}()$ 를 만들고 이것을 inference model(data-dependent function)이라고 부른다. 이렇게 하면 그냥 함수(사실을 뉴럴넷이지만)에 어떤 input $x_u$ 를 넣으면 알아서 $\mu_{u}$와 $\sigma_{u}$ 를 K개 뽑아줄 수 있다!

User $x_u$가 주어졌을 때, latent vector $z_u$를 샘플링할 분포 $q_{\phi}(z_u|x_u)$를 다음과 같이 표현할 수 있다.

<img width="419" alt="스크린샷 2024-01-02 오전 12 21 23" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/cb138790-d5b5-442a-92fe-3eea22f8f27c">
{:width: "50%", height: "50%"}

VAE를 읽어보며 정말 헷갈렸던 부분은 z를 어떤 차원으로 몇개 샘플링하느냐에 관련된 부분이었다.

정확히 말하자면,

"Neural net $q$ 를 이용해  한 user 당 latent dimesion K개 만큼 mean 과 var를 만든다. 그리고 그 개수에 맞춰서 $\epsilon$ 을 생성해 곱해준다."

그래서 만약 batch size가 100이라고 한다면, mean.size() 는 [100,50], var.size() 도 [100,50]이 되는 것이다.

### *Decoder*

디코더 부분은 별거 없음.

기본적인 ELBO form을 살짝 변형한 방식을 사용했다. KL-divergence 부분에 parameter $\beta$를 붙어주었는데, regularization을 줄이는 방향으로 조절했다.

논문처럼 $\beta < 1$ 로 바꿔준 경우,

AE가 가진 단점처럼, 기존과 다른 새로운 사용자를 manifold 상에서 mapping 하고 generate 를 잘하지 못할 수도 있다. 그러나, 

어차피 최종목표는 Lowe bound의 최대화해서 훌륭한 생성모델을 만드는 것이 아니라 latent space를 잘 구축하여 좋은 추천시스템을 만드는 것이기 때문에 괜찮다. annealing 방식으로 최적의 $\beta$ 값을 찾았을 때의 결과는 다음과 같았다. 

<img width="669" alt="스크린샷 2024-01-02 오전 12 42 16" src="https://github.com/TaewookHam/TaewookHam.github.io/assets/117107025/f8ca1a61-523b-433c-96b0-ca97469d1db8">


## **2.3 A taxonomy of autoencoders**

실험에서 사용할 모델은 Mult-DAE와 Mul-VAE 두가지이고, 이 두모델이 기존의 SOTA들보다 우수한 성능을 보였다.

-   Mult-VAE : $ \textbf{z} = \mu_{\phi}(\textbf{x})  $
-   Mult-DAE : $ \textbf{z} = g_{\phi}(x) $


VAE는 input을 벡터로 인식해서 NN에 집어 넣고 결과물로 벡터를 얻는다고 한다면, DAE는 사용자 input을 하나의 포인트 그 자체로 생각하고 그에 알맞는 latent space를 뱉어낸다고 볼 수 있다.

## **2.4 Prediction**

일반적으로 Matrix Factorization과 같은 CF model은 latent factor를 알아내기 위해서 일련의 최적화 과정을 거쳐야 한다. 오토인코더를 사용하면, 자연스럽게 latent space를 최적화하는 과정에서 사용자에게 알맞는 예측을 제시할 수 있다는 장점이 있다.

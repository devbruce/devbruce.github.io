---
title: "[ML] Matrix Factorization(MF)"
author: devbruce
date: 2022-07-21
categories: [ML]
tags: [ml, recsys, mf, matrix-factorization, cf, collaborative-filtering]
math: true
---

이 포스트는 [Albert Au Yeung - Matrix Factorization: A Simple Tutorial and Implementation in Python](https://albertauyeung.github.io/2017/04/23/python-matrix-factorization.html) 를 한국어로 번역하고, 일부 내용과 코드를 수정한 글 입니다.  

> 문제의 소지가 있거나 잘못된 내용이 있을 경우 피드백 부탁드리겠습니다.
{: .prompt-warning }

최근들어, 웹에는 너무 많은 정보가 있음은 말할 필요가 없을 것입니다. 검색엔진이 우리에게 약간의 도움을 주고 있습니다만, 이보다 더 좋은 것은 묻지않고도 흥미로운 것을 추천해주는 것입니다. 실제로 [Quora](https://www.quora.com/)에서 가장 인기 있는 질문과 답변의 목록에서부터 [Amazon](https://www.amazon.com)의 더 개인화된 추천까지, 우리는 보통 웹에서 많은 추천을 받습니다.  

추천은 다양한 알고리즘으로부터 생성될 수 있습니다. User-based 또는 Item-based 협업필터링(CF: Collaborative Filtering)은 단순하고 직관적이지만, MF(Matrix Factorization)는 User와 Item 사이의 숨겨진 interaction인 latent feature를 발견하기 때문에 효과적입니다. 당연하게도 MF는 단순히 행렬을 다루기 위한 수학적 도구이기 때문에, data의 숨겨진 무언가를 찾는 많은 시나리오에 적용될 수 있습니다.  

이 튜토리얼에서는 MF의 기본 개념(basic idea)과 수학적 개념(mathematics)을 살펴보고, Python으로 간단하게 구현한 코드를 제공할 것입니다. 추천시스템에서 아이템에 대한 유저의 평점 데이터(1 ~ 5 범위의 정수 점수)를 다룬다고 가정하고 진행하겠습니다.

<br>

## Basic Idea

Matrix Factorization은 이름에서 알 수 있듯이, 행렬을 분해합니다. 분해되어 만들어지는 행렬은 2개이며, 2개의 행렬을 행렬곱(Matrix multiplication)하여 원본 행렬로 복구할 수 있습니다.  
위에서 언급했듯이, MF는 서로 다른 2개의 entity 사이에서 latent feature를 구하는 것에 사용됩니다.(더 복잡한 tensor factorization을 통해 2개 이상의 entity를 고려할 수 있습니다.) 한가지 확실한 응용은 협업필터링(CF)에서 평점을 예측하는 것입니다.  
[Netflix](https://www.netflix.com) 또는 [MovieLens](https://movielens.org)와 같은 추천시스템에는 유저그룹과 아이템셋이 있습니다. 시스템에서 각 유저가 일부 아이템에 부여한 평점이 주어졌을 때, 그들이 아직 평가하지 않은 아이템에 대해서 어떻게 평가할지 예측하여 그들에게 추천을 할 수 있습니다.  
이 경우, 모든 정보는 존재하는 평점에 대한 행렬로 주어집니다.

다음의 상황을 가정해봅시다.  

- 유저: 5명
- 아이템: 10개
- 평점 범위: 1 ~ 5

이 경우, 행렬은 다음과 같을 것입니다.  
(hypen(`-`)은 유저가 해당 영화를 평가하지 않은 것을 의미합니다.)

|       | $D_1$ | $D_2$ | $D_3$ | $D_4$ |
|:-----:|:-----:|:-----:|:-----:|:-----:|
| $U_1$ |   5   |   3   |   -   |   1   |
| $U_2$ |   4   |   -   |   -   |   1   |
| $U_3$ |   1   |   1   |   -   |   5   |
| $U_4$ |   1   |   -   |   -   |   4   |
| $U_5$ |   -   |   1   |   5   |   4   |

비어있는 평점을 예측하는 것은 빈 칸(`-`)을 채우는 태스크로 볼 수 있습니다. 이 문제를 해결하기 위해 MF를 활용하는 직관(intuition)은 유저가 아이템을 어떻게 평가하는지를 결정하는 몇몇의 latent feature가 있을 것이라는 것입니다. 2명의 유저가 특정 영화에 높은 평점을 주었고, 2명 전부 해당 영화의 배우를 좋아했거나, 해당 영화가 2명이 전부 선호하는 액션 영화인 경우를 예로 들 수 있습니다.

따라서 latent feature를 발견한다면, 특정 유저와 특정 아이템에 대해서 평점을 예측할 수 있습니다.  
왜냐하면 유저와 관련있는 feature는 아이템과 관련있는 feature와 매칭이 되기 때문입니다.

다른 feature를 발견하기 위해선 **feature의 갯수가 유저의 수와 아이템의 수 보다 작을 것**이라고 가정합니다. 이 가정을 이해하는 것은 어려운 일이 아닙니다. 왜냐하면 각 유저가 unique feature에 연결되는 것은 명백히 합리적이지 않습니다. 그리고 이 경우에는 각 유저들이 다른 유저들에 의해 평가된 아이템들에 대해서 흥미를 가지지 않게되므로, 추천을 하는 것에 의미가 없을 것입니다. 아이템에도 동일한 논리가 적용됩니다.

<br>

## The Maths of Matrix Factorization

이제 Matrix factorization의 수학에 대해서 이야기해보겠습니다.  
첫 번째로, User 행렬인 $U$, Item 행렬 $D$ 를 선언합니다.  

행렬 $\mathbf{R}$ (Size: Users(Row) $\times$ Items(Column))은 유저가 아이템에 대해 부여한 평점을 모두 보유하고 있습니다.  
이제 $K$개의 latent feature를 구해봅시다.  
이를 위해서는 다음 2개의 행렬을 구해야 합니다.

- $\mathbf{P}$ (Size: Users(Row) $\times$ K\-LatentFeatures(Column))
- $\mathbf{Q}$ (Size: Items(Row) $\times$ K\-LatentFeatures(Column))

$\mathbf{P}$, $\mathbf{Q}$의 행렬곱(Matrix multiplication)은 $\mathbf{R}$에 근사합니다.  

$$ \mathbf{R} \approx \mathbf{P} \times \mathbf{Q}^T = \hat{\mathbf{R}} $$

이 때 $\mathbf{P}$의 각 행은 **유저와 feature** 사이의 연관성의 강도를 나타냅니다.  
유사하게도, $\mathbf{Q}$의 각 행은 **아이템과 feature** 사이의 연관성의 강도를 나타냅니다.  
$u_i$를 통해 아이템 $d_j$의 예측평점을 구하기 위해서는 그들의 벡터를 내적(Dot product)합니다.

$$ \hat{r}_{ij} = p_i^T q_j = \sum_{k=1}^K{p_{ik} q_{kj}} $$

이제 $\mathbf{P}$와 $\mathbf{Q}$를 얻기위한 방법을 찾아야 합니다.  
이 문제에 접근하기 위한 한 가지 방법은 다음과 같습니다.

1. 임의의 값으로 채운 2개의 행렬 생성
2. 생성된 두 행렬의 행렬곱의 값과 $\mathbf{M}$의 차이값 계산
3. 반복하며 차이값 최소화

여기서의 차이값은 실제 평점과 예측 평점사이의 차이를 말하며 보통 **error**라고 불립니다.  
이는 아래의 식으로 계산될 수 있습니다.

$$ e_{ij}^2 = (r_{ij} - \hat{r}_{ij})^2 = (r_{ij} - \sum_{k=1}^K{p_{ik}q_{kj}})^2 $$

여기서 **squared error**를 사용합니다.  
예측평점이 실제 평점보다 높거나 낮을 수 있기 때문입니다.  
(Sqaured Error를 사용하는 이유는 여러가지가 존재합니다. 해당 글에선 다루지 않겠습니다.)

Error를 최소화하기 위해 변경해야할 값 $p_{ik}$ 와 $q_{kj}$ 의 방향(direction)을 알아야 합니다.  
다시 말해, 현재 값의 gradient(기울기, 변화도, 경사도...)를 알아야 합니다.  
그러므로, 위의 식을 두 개의 변수 $p_{ik}$, $q_{kj}$에 대해 따로 수행합니다.

$$ \frac{\partial}{\partial p_{ik}}e_{ij}^2 = -2(r_{ij} - \hat{r}_{ij})(q_{kj}) = -2 e_{ij} q_{kj} $$

$$ \frac{\partial}{\partial q_{ik}}e_{ij}^2 = -2(r_{ij} - \hat{r}_{ij})(p_{ik}) = -2 e_{ij} p_{ik} $$

$p_{ik}$, $q_{kj}$의 gradient를 따로 구했다면, Update rule은 다음과 같습니다.

$$ p'_{ik} = p_{ik} - \alpha \frac{\partial}{\partial p_{ik}}e_{ij}^2 = p_{ik} + 2\alpha e_{ij} q_{kj} $$

$$ q'_{kj} = q_{kj} - \alpha \frac{\partial}{\partial q_{kj}}e_{ij}^2 = q_{kj} + 2\alpha e_{ij} p_{ik} $$

$\alpha$는 최소값에 접근하는 비율을 결정하는 상수입니다.(\=\= Learning rate)  
보통 $\alpha$는 0.0002와 같이 작은 값으로 설정합니다.  
만약 너무 큰 값으로 설정하면, 최소값을 지나치고 최소값 주변에서 왔다갔다 할 수 있기 때문입니다.(\=\= Overshooting)  

지금쯤 어떤 의문이 생길지 모릅니다.  
만약 행렬 $\mathbf{P}$, $\mathbf{Q}$를 찾았을 때, 이전에 보지 못한 모든 평점의 예측값은 0이 되지 않을까?  
사실상, $\mathbf{R}$을 완전히 재현하는 $\mathbf{P}$, $\mathbf{Q}$를 만든 것이 아닙니다.  
대신, 관측된 user-item 평점 세트의 error를 최소화 시킨 것 뿐입니다.  

1. $(u_i, d_j, r_{ij})$로 구성된 tuple $T$를 학습 데이터로 설정($T$는 관측된 user-item 평점 세트)  
2. 모든 $e_{ij}$ for $(u_i, d_j, r_{ij}) \in T$ 에 대해서 최소화

나머지 알려지지 않은 것들에 대해서는 유저, 아이템 그리고 feature의 연관성을 학습하면 그들의 값을 결정할 수 있습니다.  

위의 업데이트 식을 통해, error가 최소값에 수렴할 때 까지 반복적으로 연산을 수행할 수 있습니다.  
다음의 식을 통해 전반적인 error를 계산하고, 언제 멈출지 결정할 수 있습니다.

$$ E = \sum_{(u_i, d_j, r_{ij}) \in T}{e_{ij}} = \sum_{(u_i,d_j,r_{ij}) \in T}{(r_{ij} - \sum_{k=1}^K{p_{ik}q_{kj}})^2} $$

<br>

## Regularization

기본 알고리즘의 일반적인 확장은 overfitting을 피하기 위한 regularization이 있습니다.  
다음 식과 같이 파라미터 $\beta$를 추가하고, squared error를 수정하면 됩니다.  

$$ e_{ij}^2 = (r_{ij} - \sum_{k=1}^K{p_{ik}q_{kj}})^2 + \frac{\beta}{2} \sum_{k=1}^K{(||P||^2 + ||Q||^2)} $$

새로운 파라미터 $\beta$는 다음과 같습니다.

- $\mathbf{P}$, $\mathbf{Q}$가 큰 수를 포함하지 않고도 $\mathbf{R}$에 괜찮은 근사치를 제공합니다.
- user-feature와 item-feature 백터들의 크기를 제어하는 곳에 사용됩니다.  
- $\beta$는 0.02 정도로 설정됩니다.

새로운 Update rule은 위에서 설명한 것과 유사한 절차를 통해 구할 수 있으며 다음과 같습니다.  

$$ p'_{ik} = p_{ik} + \alpha \frac{\partial}{\partial p_{ik}}e_{ij}^2 = p_{ik} + \alpha(2 e_{ij} q_{kj} - \beta p_{ik} ) $$

$$ q'_{kj} = q_{kj} + \alpha \frac{\partial}{\partial q_{kj}}e_{ij}^2 = q_{kj} + \alpha(2 e_{ij} p_{ik} - \beta q_{kj} ) $$

<br>

## Adding Biases

유저가 아이템에 부여한 평점을 예측할 때, 평점이 어떻게 생성되는지 고려하는 것은 유용합니다. 위에서 언급한 것처럼, latent factor 기반의 유저 선호도와 latent factor 기반의 아이템의 특성은 매칭되는 것을 기반으로 평점이 생성되는 것으로 가정하고 있습니다. 사실상, 추가적인 요소를 고려하는 것은 더 도움이 될 것입니다.

예를 들어, 평점이 생성되었을 때, 몇몇의  **biases**는 평점에 기여할 수 있을 것입니다. 특히, 모든 유저는 각자의 bias, 즉 아이템을 다른 사람보다 상대적으로 더 높게 또는 더 낮게 평가하는 경향이 있습니다. 영화 평점에서는, 만약 유저가 serious 영화를 주로 본다면 아주 지루하지 않은 영화를 즐기는 유저보다 상대적으로 낮은 평점을 주는 경향이 있습니다. 평가되는 아이템에도 동일한 아이디어가 적용됩니다.  

따라서 평점 예측과 마찬가지로, 더 좋은 평점 생성 모델을 만들기 위해 이러한 편향을 추가할 수 있습니다.

$$ \hat{r}_{ij} = b + bu_i + bd_j + \sum_{k=1}^k{p_{ik} q_{kj}} $$

- $b$: Global bias(모든 평점의 평균)
- $bu_i$: 유저 $i$의 bias
- $bd_j$: 아이템 $j$의 bias

위에서 언급한 절차와 마찬가지로, 유저 bias와 아이템 bias의 update rule을 쉽게 구할 수 있습니다.

$$ bu'_i = bu_i + \alpha \times (e_{ij} - \beta bu_i) $$

$$ bd'_j = bd_j + \alpha \times (e_{ij} - \beta bd_j) $$

실제로, factorization 과정은 bias가 추가되었을 때 더 빠르게 수렴합니다.

<br>

## A Simple Implementation in Python

> Git Repository: [devbruce/matrix-factorization-py](https://github.com/devbruce/matrix-factorization-py)

위에서 설명한 대로, Update rule을 구했다면 알고리즘을 구현하는 것은 간단해집니다. 다음은 Python으로 SGD(Stochastic Gradient Descent)을 활용하여 구현한 Matrix Factorization입니다. 해당 구현에는 [Numpy](https://numpy.org) 모듈이 필요합니다.

```python
from typing import List

import numpy as np


class MatrixFactorization:
    
    def __init__(self, R: np.ndarray, K: int, alpha: float, beta: float = 0.02):
        """
        Args:
            R (np.ndarray): User-Item rating matrix
            K (int): Number of latent dimensions
            alpha (float): Learning rate
            beta (float): Regularization parameter
        """
        self.R = R
        self.num_users
        self.num_items = R.shape
        self.K = K
        self.alpha = alpha
        self.beta = beta
        
        # Create a training samples(Not np.nan indices)
        self.train_ds = np.argwhere(~np.isnan(self.R))
        
    def train(self, epochs: int) -> List[np.float64]:
        # Initialize user and item latent feature matrixes
        self.P = np.random.normal(scale=1./self.K, size=(self.num_users, self.K))
        self.Q = np.random.normal(scale=1./self.K, size=(self.num_items, self.K))
        
        # Initialize the biases
        self.b_u = np.zeros(self.num_users)
        self.b_i = np.zeros(self.num_items)
        self.b = np.mean(self.R[~np.isnan(self.R)])
        
        # Perform stochastic gradient descent for number of iterations
        hist = []
        for epoch in range(1, epochs+1):
            mse = self.sgd()
            hist.append(mse)
            print(f'Epoch: {epoch:3} | MSE: {mse:.4f}')
        return hist

    def sgd(self) -> np.float64:
        """
        Update weights with Stochastic Gradient Descent
        
        Returns:
            np.float64: Total mean squared error
        """
        train_ds = self.train_ds.copy()
        np.random.shuffle(train_ds)
        
        total_mse = 0.0
        for i, j in train_ds:  # user i, item j
            label = self.R[i, j]
            pred = self.get_pred_rating(i, j)
            e = label - pred
            
            # Update biases
            self.b_u[i] += self.alpha * (e - self.beta * self.b_u[i])
            self.b_i[j] += self.alpha * (e - self.beta * self.b_i[j])
            
            # Create copy of row of P since we need to update it but use older values for update on Q
            P_i = self.P[i, :].copy()
            
            # Update user and item latent feature matrices
            self.P[i, :] += self.alpha * (e * self.Q[j, :] - self.beta * self.P[i,:])
            self.Q[j, :] += self.alpha * (e * P_i - self.beta * self.Q[j,:])
            
            total_mse += np.square(e)
        total_mse = np.sqrt(total_mse)
        return total_mse

    def get_pred_rating(self, i: int, j: int) -> np.float64:
        """
        Get the predicted rating of user i and item j
        """
        pred = self.b + self.b_u[i] + self.b_i[j] + (self.P[i, :] @ self.Q[j, :].T)
        return pred
    
    def get_pred_full_matrix(self) -> np.ndarray:
        """
        Get the full matrix using the resultant biases, P and Q
        """
        return self.b + self.b_u[:,np.newaxis] + self.b_i[np.newaxis:,] + (self.P @ self.Q.T)
```

이제 위에서 언급했던 예시(유저: 5명, 아이템: 10개 평점)를 코드에 적용해보고 결과를 확인해봅시다.  

```python
NP_SEED = 1234
np.random.seed(NP_SEED)


R = np.array([
    [     5,      3, np.nan, 1],
    [     4, np.nan, np.nan, 1],
    [     1,      1, np.nan, 5],
    [     1, np.nan, np.nan, 4],
    [np.nan,      1,      5, 4],
])

mf = MatrixFactorization(
    R=R,
    K=2,        # Number of latent features
    alpha=0.1,  # Learning Rate
    beta=0.01,
)
```

먼저 학습에 사용되는 데이터를 확인해보겠습니다.

```python
mf.train_ds
```

```text
array([[0, 0],
       [0, 1],
       [0, 3],
       [1, 0],
       [1, 3],
       [2, 0],
       [2, 1],
       [2, 3],
       [3, 0],
       [3, 3],
       [4, 1],
       [4, 2],
       [4, 3]])
```

출력된 학습 데이터셋은 `np.nan`이 아닌 인덱스의 집합임을 확인할 수 있습니다. 즉, 학습에는 `np.nan`을 제외한 값들을 통해 학습이 진행됩니다.  
이제 학습을 해보겠습니다.

```python
hist = mf.train(epochs=20)
```

```text
Epoch:   1 | MSE: 7.0449
Epoch:   2 | MSE: 6.0203
Epoch:   3 | MSE: 4.9740
Epoch:   4 | MSE: 3.5499
Epoch:   5 | MSE: 2.1640
Epoch:   6 | MSE: 1.3316
Epoch:   7 | MSE: 0.9654
Epoch:   8 | MSE: 0.7514
Epoch:   9 | MSE: 0.6056
Epoch:  10 | MSE: 0.4900
Epoch:  11 | MSE: 0.4061
Epoch:  12 | MSE: 0.3264
Epoch:  13 | MSE: 0.2771
Epoch:  14 | MSE: 0.2320
Epoch:  15 | MSE: 0.1920
Epoch:  16 | MSE: 0.1633
Epoch:  17 | MSE: 0.1410
Epoch:  18 | MSE: 0.1167
Epoch:  19 | MSE: 0.1020
Epoch:  20 | MSE: 0.0836
```

학습된 mf를 통해 최종 예측 결과를 확인해봅니다.

```python
pred_matrix = mf.get_pred_full_matrix()
print(pred_matrix.round(2))
```

```text
[[4.99 2.99 3.31 1.02]
 [3.99 2.21 2.82 1.01]
 [1.03 0.98 4.48 4.98]
 [1.   0.84 4.52 3.98]
 [1.18 1.04 4.97 4.  ]]
```

기존 평점의 경우 실제 값에 매우 가까운 근사치를 보이고, 학습하지 못한 unknown 값들에 대해서는 예측값을 얻었습니다. 이 간단한 예시에서 $D_3$, $D_4$, $D_5$를 선호하는 나머지 유저들과 다르게 $D_1$, $D_2$에 높은 평점을 준 $U_1$과 $U_2$가 유사한 취향을 가지고 있음을 알 수 있습니다. Latent feature의 갯수 $K$가 2이면, 알고리즘은 유저와 아이템을 2개의 다른 feature로 연관시키고, 예측 또한 이 연관성을 따릅니다. 그 예로, 아이템 $D_3$에 대한 $U_4$의 예측 평점은 4.52임을 확인할 수 있습니다. 왜냐하면 유저 $U_4$, $U_5$ 둘 다 $D_4$에 높은 평점을 부여했기 때문입니다.

<br>

## Further Information

지금까지 matrix factorization의 직관적 의미와 협업필터링(CF: Collaborative filtering)에서의 사용법에 대해서 이야기하였습니다. 사실상 MF는 다양한 방식이 존재합니다. 중요한 것은 위에서 예시로 나온 행렬 $\mathbf{P}$, $\mathbf{Q}$는 모든 원소가 음수가 아니어야 한다는 것입니다. 이러한 경우를 [non-negative matrix factorization(NMF)](https://en.wikipedia.org/wiki/Non-negative_matrix_factorization)라고 부릅니다. NMF의 장점은 결과 행렬의 의미가 직관적이라는 것입니다. 모든 원소가 음수가 아니기에, 행렬곱을 통해 원본 행렬로 돌아가는 과정에서 빼기(Subtraction) 연산이 포함되지 않습니다.즉 latent feature의 선형 결합에 의해서 오리지날 데이터를 생성하는 과정으로 간주될 수 있습니다.

<br>

## References

- GitHub Repository: [devbruce/matrix-factorization-py](https://github.com/devbruce/matrix-factorization-py)
- <kbd>Original</kbd>Post: [Albert Au Yeung - Matrix Factorization: A Simple Tutorial and Implementation in Python](https://albertauyeung.github.io/2017/04/23/python-matrix-factorization.html)
- <kbd>Original</kbd>Github Repository: [albertauyeung/matrix-factorization-in-python](https://github.com/albertauyeung/matrix-factorization-in-python)
- <kbd>Original</kbd>[IPython Notebok](https://nbviewer.jupyter.org/github/albertauyeung/matrix-factorization-in-python/blob/master/mf.ipynb)

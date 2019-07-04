---
title: "[ML] Linear Regression With Multiple Variables"
excerpt: 
last_modified_at: 2019-07-04

categories:
  - MachineLearning

tags:
  - machine_learning
  - linear_regression
  - vectorization
  - gradient_descent_algorithm
  - feature_scaling
  - polynomial_regression
  - normal_equation

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

# Linear Regression With Multiple Variables

## Example of Training Set

| Size ($ x_1 $) | Age (years) ($ x_2 $) | Number of Rooms ($ x_3 $) | Price ($ y $) |
|:--------------:|:---------------------:|:-------------------------:|:-------------:|
|      1706      |           2           |             8             |      163      |
|      1321      |           3           |             6             |      122      |
|       601      |           7           |             3             |       57      |
|       370      |           11          |             2             |       32      |
|       180      |           8           |             2             |       15      |

<br>

### Hypothesis

$ h_\theta (x) = \theta _0 x _0 + \theta _1 x _1 + \theta _2 x _2 + \theta _3 x _3 \quad (x _0 = 1 ) $  

$ h_\theta (x) = \theta ^T X $

<br>

### Vectorization Process

$$ \theta = \begin{bmatrix} \theta _0 \\\ \theta _1 \\\ \theta _2 \\\ \theta _3 \end{bmatrix} \qquad X = \begin{bmatrix} x _1 \\\ x _2 \\\ x _3 \end{bmatrix} $$

<br>

$$ \theta ^T = \begin{bmatrix} \theta _0 & \theta _1 & \theta _2 & \theta _3 \end{bmatrix} $$

<br>

현재 $ \theta $ 와 $ X $ 를 통해 $ h _\theta (x) $ 를 만들기는 불가능하다.  
($ \theta _0 $ 의 존재때문에 내적이 불가능한 상황이다.)  
따라서 $ X $ 의 첫번째 요소로 1 을 추가하여 $ \theta _0 $ 를 표현할 수 있도록 수정한다.  
> 이에따라 $ X $ 의 갯수는 n 이 아닌 n + 1 개가 된다는 점을 유의한다.  

<br>

$$ X = \begin{bmatrix} 1 \\\ x _1 \\\ x _2 \\\ x _3 \end{bmatrix} $$

$ X $ 의 첫번째 요소인 1 은 편의상 $ x _0 $ 으로 표현한다.  
따라서 아래와 같이 표현된다.  

<br>

$$ X = \begin{bmatrix} x _0 \\\ x _1 \\\ x _2 \\\ x _3 \end{bmatrix} $$

<br>

최종적으로 hypothesis 를 표한하면 다음과 같다.  

<br>

$$ h_\theta (x) = \begin{bmatrix} \theta _0 & \theta _1 & \theta _2 & \theta _3 \end{bmatrix} \begin{bmatrix} x _0 \\\ x _1 \\\ x _2 \\\ x _3 \end{bmatrix} = \theta _0 x _0 + \theta _1 x _1 + \theta _2 x _2 + \theta _3 x _3 $$

<br>

$$ h_\theta (x) = \theta ^T X $$

<br>

## Summary

<table>
    <tr>
        <td rowspan="2">Hypothesis<br>($ x_0 = 1 $)</td>
        <td>$ h_\theta (x) = \theta _0 x_0 + \theta _1 x_1  + . . . + \theta_n x_n $</td>
    </tr>
    <tr>
        <td>$ h_\theta (x) = \theta ^T X $</td>
    </tr>
    <tr>
        <td>Parameters</td>
        <td>$ \theta _0 $, $ \theta _1 $, . . . , $ \theta _n $ </td>
    </tr>
    <tr>
        <td>Cost Function (MSE)</td>
        <td>$ J(\theta) = \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $</td>
    </tr>
    <tr>
        <td>Goal</td>
        <td>minimize $ J(\theta) $</td>
    </tr>
</table>

<br><br>

## Gradient Descent Algorithm

$$ \theta _j := \theta _j - \alpha  \frac{1}{m} \sum _{i=1}^{m} (h _\theta (x) ^{(i)} - y^{(i)}) x _j ^{(i)} $$

- Simultaneously update for every $ j \quad (j = 0, ..., n) $

<br>

### Feature Scaling

Gradient Descent Algorithm 의 속도를 향상시키기 위한 방법 중 하나이다.  
> $ \theta $ 는 feature 가 작은 범위를 가지고 있을 때 빠르게 descend 하기 때문이다.  

$$ x _i :=  \frac{x _i  - \mu _i}{s _i} $$

- $ \mu _i $ : feature(i) 의 평균값

- $ s _i $ : feature(i) 의 (max - min) 또는 standard deviation(표준편차)

<br><br>

## Polynomial Regression

- $ h_\theta (x) = \theta _0 + \theta _1 x _1 $ : 1차 (Linear)

- $ h _\theta (x) = \theta _0 + \theta _1 x _1 + \theta _2 x _1 ^2 $ : 2차 (Convex)

- $ h _\theta (x) = \theta _0 + \theta _1 x _1 + \theta _2 x _1 ^2 + \theta _3 x _1 ^3 $ : 3차

3차의 경우 $ x_2 = x _1 ^2 $ , $ x _3 = x _1 ^3 $ 으로 볼 수 있다.  
이에따라 feature 의 범위의 크기가 크게 달라지므로 **Feature Scaling** 이 매우 중요하다.

> - $ h_\theta (x) = \theta _0 + \theta _1 x _1 + \theta _1 \sqrt{x _1} $ : Square root function

<br><br>

## Normal Equation

| Size ($ x_1 $) | Age (years) ($ x_2 $) | Number of Rooms ($ x_3 $) | Price ($ y $) |
|:--------------:|:---------------------:|:-------------------------:|:-------------:|
|      1706      |           2           |             8             |      163      |
|      1321      |           3           |             6             |      122      |
|       601      |           7           |             3             |       57      |
|       370      |           11          |             2             |       32      |
|       180      |           8           |             2             |       15      |

<br>

$$ X = \begin{bmatrix} 1 & 1706 & 2 & 8 \\\ 1 & 1321 & 3 & 6 \\\ 1 & 601 & 7 & 3 \\\ 1 & 370 & 11 & 2 \\\ 1 & 180 & 8 & 2 \end{bmatrix} \qquad Y = \begin{bmatrix} 163 \\\ 122 \\\ 57 \\\ 32 \\\ 15 \end{bmatrix} $$

<br>

$$ X \theta = Y $$

<br>

위의 식을 통해서 $ \theta = X ^{-1} Y $ 를 구할 수 있다.  
하지만, 위와 같이 양변에 $ X ^{-1} $ 을 곱할 수 있는 경우는  
$ X ^{-1} $ 이 존재하는 경우에 한해서이다.  
> 즉 $ X $ 의 역행렬이 존재한다는 전제가 필요하다.

$ X $ 의 역행렬이 존재하기 위해서는 $ X $ 가 Sqaure Matrix (정방행렬) 이어야 한다.  
따라서 역행렬이 존재할 수 있도록 $ X \theta = Y $ 식의 양변에 $ X^T $ 를 곱해준다.  

> $ X $ 에 $ X^T $ 를 곱하게 되면 Square Matrix 형태가 된다.

<br>

$$ X^T X \theta = X^T Y $$

<br>

이제 양변에 $ X^T X $ 의 역행렬을 곱하여 $ \theta $ 를 구한다.

<br>

$$ \theta = (X^T X)^{-1} X^T Y $$

<br>

$ X^T X $ 가 역행렬이 없는 경우는 거의 없다.  
그러나 역행렬이 없는 경우(non-invertible)은 다음의 경우에 해당한다.

- Redundant features (Linearly Dependent)
  - To Solve: Delete redundant features

<br>

- Too many features (ex. $ m \leq n $)
  - To Solve: Delete some features, Regularization

<br>

> Octave 에서는 역행렬 구하는 함수가 2 가지 존재한다. (`pinv`, `inv`)  
> `pinv` (pseudo inverse): 역행렬이 없어도 역행렬을 구해준다.  

<br><br>

## Gradient Descent vs Normal Equation

|                   | Gradient Descent | Normal Equation |
|-------------------|:----------------:|:---------------:|
| Learning Rate     |       Need       |     No need     |
| Iteration         |       Need       |     No need     |
| Feature Scaling   |       Need       |     No need     |
| Many Features (n) |    Works well    |       Slow      |

<br><br>

# Reference

| Lecture       |          Machine Learning (Offered By Stanford)          |
|---------------|:--------------------------------------------------------:|
| Instructor    |                         Andrew Ng                        |
| Coursera Link | [Link](https://www.coursera.org/learn/machine-learning?) |

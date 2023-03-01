---
title: "[ML] Gradient Descent Algorithm For Linear Regression With One Variable"
excerpt: 
last_modified_at: 2019-05-10

categories:
  - MachineLearning

tags:
  - machine_learning
  - gradient_descent_algorithm
  - learning_rate
  - cost_function
  - linear_regression
  - convex_function
  - global_minimum
  - local_minimum
  - python
  - tensorflow

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

# Gradient Descent Algorithm <br> For Linear Regression With One Variable

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)
![tensorflow-version-1.13.1](https://img.shields.io/badge/TensorFlow-v1.13.1-brightgreen.svg)

<br>

## Linear Regression With One Variable

<table>
    <tr>
        <td>Hypothesis</td>
        <td>$ h_\theta (x) = \theta _0 + \theta _1 x $</td>
    </tr>
    <tr>
        <td>Parameters</td>
        <td>$ \theta _0 $, $ \theta _1 $</td>
    </tr>
    <tr>
        <td>Cost Function (MSE)</td>
        <td>$ J(\theta _0, \theta _1) = \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $</td>
    </tr>
    <tr>
        <td>Goal</td>
        <td>minimize $ J(\theta _0, \theta _1) $</td>
    </tr>
</table>

<br><br>

## Gradient Descent Algorithm <br> (For Linear Regression With One Variable)

- Cost Function 의 **최솟값**을 구하기 위해 단계적으로 조금씩 접근하는 방법

- 아래의 식을 반복하여 최솟값에 수렴되게 한다. (for $ j = 0 $ and $ j = 1 $)

$$ \theta _j := \theta _j - \alpha \frac{\delta}{\delta \theta _j} J(\theta _0, \theta _1) $$



- $ \alpha $: Learning Rate

- $ \frac{\delta}{\delta \theta _j}J(\theta _0, \theta _1) $ : $ J(\theta _0, \theta _1) $ 을 $ \theta _j $ 에 대해서 편미분

<br><br>

### Visualization Example of Gradient Descent Algorithm Process

![ml-02-gradient_descent](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-02-gradient_descent.png?raw=true){: width="400px"}  

<br>

- 기울기가 음수일 경우 $ x $ 증가

- 기울기가 양수일 경우 $ x $ 감소

- 기울기의 (절대값)크기에 따라 이동량도 비례한다.

<br><br>

### Global Minimum, Local Minimum

<br>

![ml-02-minimum](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-02-minimum.png?raw=true){: width="400px"}  

<br>

함수가 복잡해질수록 Gradient Descent Algorithm 을 통해  
도달한 지점이 최솟값(Global Minimum)이 아닐 수 있다.  
(차원이 증가할수록 위의 이미지처럼 Local Minimum 으로 접근할 가능성이 커진다.)  
이러한 문제를 예방하기 위해서 출발지점이 다르게 여러번 시도한다.  

- 출발지점이 다르다는 것은 **$ \theta $ (Parameter)의 초기값**을 다르게 설정해준다는 의미이다.  

- Gradient Descent Algorithm 이 출발점에 관계없이  
최솟값 (Global Minimum) 에 도달하는 함수를 Convex Function 이라고 한다.  

- Linear Regression 의 Cost Function 은 항상 Convex Function 이다.

<br><br>

### Learning Rate

데이터를 변형없이 학습시키면, **마지막에 학습시킨 데이터에 편향되어 parameter ($ \theta $) 가 업데이트된다.**  
이러한 편향되는 값을 조정하기 위한 scalar value 를 Learning Rate 라고 한다.  

$$ (0 < lr < 1) $$

- 값이 지나치게 클 경우 Gradient Descent 과정에서 오버슈팅이 발생할 수 있다.  

- 값이 지나치게 작을 경우 Gradient Descent 과정에서 Local Minimum 으로 수렴하거나,  
변화량이 작아 학습이 원활하게 진행되지 않을 수 있다.

<br><br>

### Simultaneous Update

- 업데이트할 각각의 Parameter 는 **동시**에 업데이트 되어야 한다.  
(서로의 업데이트에 독립적이어야 한다.)

**Update Pseudo Code**  

tmp0 := $ \theta _0 - \alpha \frac{\delta}{\delta \theta _0} J(\theta _0, \theta _1) $  
tmp1 := $ \theta _1 - \alpha \frac{\delta}{\delta \theta _1} J(\theta _0, \theta _1) $  
$ \theta _0 $ := tmp0  
$ \theta _1 $ := tmp1

> $ \theta _0 $ 를 업데이트 한 값은 tmp0 에 저장한다.  
> $ \theta _1 $ 을 업데이트 연산에는 업데이트 된 $ \theta _0 $ 인 tmp0 가 아닌,  
> 업데이트가 되기 전 $ \theta _0 $ 로 연산하여 tmp1 에 저장한다.  
> 그리고 각각의 parameter를 각각의 업데이트 값인  
> tmp0 과 tmp1 으로 동시에 업데이트시킨다.

<br><br>

### Implementation

Linear Regression With One Variable 이기 때문에  
두 가지 Parameter $ \theta _0 $, $ \theta _1 $ 를 동시에 업데이트하면 된다.

따라서 $ j = 0 $ 과 $ j = 1 $ 두 가지 경우로 분류하여 공식을 도출해보자.

Gradient Descent Algorithm 의 식은 다음과 같다.  

<br>

$$ \theta _j := \theta _j - \alpha \frac{\delta}{\delta \theta _j} J(\theta _0, \theta _1) $$

<br>

$ J(\theta _0, \theta _1) = \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $ 이기 때문에 위의 식을 다음과 같이 변형 가능하다.  

<br>

$$ \theta _j := \theta _j - \alpha \frac{\delta}{\delta \theta _j} \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $$

<br>

$ h_\theta (x) = \theta _0 + \theta _1 x $ 이기 때문에 위의 식은 다음과 같이 변형 가능하다.

<br>

$$ \theta _j := \theta _j - \alpha \frac{\delta}{\delta \theta _j} \frac{1}{2m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)})^2 $$

<br><br>

#### Case1 : j = 0

$$ \theta _0 := \theta _0 - \alpha \frac{\delta}{\delta \theta _0} \frac{1}{2m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)})^2 $$


$$ \theta _0 := \theta _0 - \alpha  \frac{1}{m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)}) $$

<br><br>

#### Case2 : j = 1

$$ \theta _1 := \theta _1 - \alpha \frac{\delta}{\delta \theta _1} \frac{1}{2m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)})^2 $$


$$ \theta _1 := \theta _1 - \alpha  \frac{1}{m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)})x^{(i)} $$

<br><br>

#### Result Update Formula

$$ \theta _0 := \theta _0 - \alpha  \frac{1}{m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)}) $$

$$ \theta _1 := \theta _1 - \alpha  \frac{1}{m} \sum _{i=1}^{m} (\theta _0 + \theta _1 x^{(i)} - y^{(i)})x^{(i)} $$

<br><br>

#### Implementation Code of Tensorflow (Python3)

##### 직접 구현

```python
import tensorflow as tf


x_data = [1, 3, 5]
y_data = [1, 3, 5]

# Set Parameter
theta_0 = tf.Variable(tf.random_normal([1]), name='theta_0')
theta_1 = tf.Variable(tf.random_normal([1]), name='theta_1')

X = tf.placeholder(tf.float32)  # Input Variable
Y = tf.placeholder(tf.float32)  # Output Variable

hypothesis = theta_0 + (theta_1 * X)

# Cost Function 을 직접 미분하여 Gradient Descent Algorithm 을 구현
cost = (1/2) * tf.reduce_mean(tf.square(hypothesis - Y))  # Cost Function
cost_derivative_theta_0 = tf.reduce_mean(theta_0 + (theta_1 * X) - Y)  # theta_0 에 대해서 편미분
cost_derivative_theta_1 = tf.reduce_mean((theta_0 + (theta_1 * X) - Y) * X)  # theta_1 에 대해서 편미분

# Gradient Descent -= learning_rate * cost_derivative
learning_rate = 0.1
gradient_descent_theta_0 = theta_0 - (learning_rate * cost_derivative_theta_0)
gradient_descent_theta_1 = theta_1 - (learning_rate * cost_derivative_theta_1)

# Update (Simultaneous Update)
theta_0_train = theta_0.assign(gradient_descent_theta_0)
theta_1_train = theta_1.assign(gradient_descent_theta_1)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for step in range(500+1):
        cost_val, theta_0_val, theta_1_val = sess.run(
            [cost, theta_0_train, theta_1_train], feed_dict={X: x_data, Y: y_data}
        )
        if step % 100 == 0:
            print(f'Step: {step:<6}, Cost: {cost_val:.6f}, theta_0: {theta_0_val}, theta_1: {theta_1_val}')
```

```
Step: 0     , Cost: 4.637724, theta_0: [0.49622792], theta_1: [1.0882671]
Step: 100   , Cost: 0.000309, theta_0: [0.05081665], theta_1: [0.98668855]
Step: 200   , Cost: 0.000004, theta_0: [0.00583276], theta_1: [0.9984721]
Step: 300   , Cost: 0.000000, theta_0: [0.00066951], theta_1: [0.9998246]
Step: 400   , Cost: 0.000000, theta_0: [7.684264e-05], theta_1: [0.99997985]
Step: 500   , Cost: 0.000000, theta_0: [8.817839e-06], theta_1: [0.9999976]
```

<br>

##### Use GradientDescentOptimizer of Tesorflow

```python
import tensorflow as tf


x_data = [1, 3, 5]
y_data = [1, 3, 5]

# Set Parameter
theta_0 = tf.Variable(tf.random_normal([1]), name='theta_0')
theta_1 = tf.Variable(tf.random_normal([1]), name='theta_1')

X = tf.placeholder(tf.float32)  # Input Variable
Y = tf.placeholder(tf.float32)  # Output Variable

hypothesis = theta_0 + (theta_1 * X)
cost = (1/2) * tf.reduce_mean(tf.square(hypothesis - Y))  # Cost Function
train = tf.train.GradientDescentOptimizer(learning_rate=0.1).minimize(cost)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for step in range(500+1):
        cost_val, _ = sess.run([cost, train], feed_dict={X: x_data, Y: y_data})
        if step % 100 == 0:
            theta_0_val, theta_1_val = sess.run([theta_0, theta_1], feed_dict={X: x_data, Y: y_data})
            print(f'Step: {step:<4}, Cost: {cost_val:.7f}, theta_0: {theta_0_val}, theta_1: {theta_1_val}')
```

```
Step: 0   , Cost: 0.4154058, theta_0: [1.7083766], theta_1: [0.5792478]
Step: 100 , Cost: 0.0045590, theta_0: [0.19533622], theta_1: [0.94883144]
Step: 200 , Cost: 0.0000601, theta_0: [0.02242081], theta_1: [0.9941268]
Step: 300 , Cost: 0.0000008, theta_0: [0.00257347], theta_1: [0.9993259]
Step: 400 , Cost: 0.0000000, theta_0: [0.00029542], theta_1: [0.99992263]
Step: 500 , Cost: 0.0000000, theta_0: [3.3890854e-05], theta_1: [0.99999106]
```

<br><br>

# Reference

| Lecture       |          Machine Learning (Offered By Stanford)          |
|---------------|:--------------------------------------------------------:|
| Instructor    |                         Andrew Ng                        |
| Coursera Link | [Link](https://www.coursera.org/learn/machine-learning?) |

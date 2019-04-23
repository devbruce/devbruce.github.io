---
title: "[ML] Gradient Descent Algorithm"
excerpt: 
last_modified_at: 2019-03-30

categories:
  - MachineLearning

tags:
  - machine_learning
  - gradient_descent_algorithm
  - derivative
  - learning_rate
  - cost_function

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

# Gradient Descent Algorithm

![python-version-3.7.1](https://img.shields.io/badge/python-v3.7.1-blue.svg)
![tensorflow-version-1.13.1](https://img.shields.io/badge/TensorFlow-v1.13.1-brightgreen.svg)

<br>

Cost Function 의 **최솟값**을 구하기 위해 단계적으로 조금씩 접근하는 방법  

Gradient Descent 를 활용한 Weight 를 업데이트 시키기 위한 식은 다음과 같다.

<br>

$$ W := W -\alpha \frac{\delta}{\delta W}cost(W, b) $$

<br>

- $ \frac{\delta}{\delta W}cost(W, b) $ : $ cost(W, b) $ 을 $W$ 에 대해서 편미분
- $ \alpha $: Learning Rate

<br><br>

Gradient Descent 를 활용한 Bias 를 업데이트 시키는 식은 아래와 같다.

<br>

$$ b := b -\alpha \frac{\delta}{\delta b}cost(W, b) $$

<br>

- $ \frac{\delta}{\delta b}cost(W, b) $ : $ cost(W, b) $ 을 $b$ 에 대해서 편미분
- $ \alpha $: Learning Rate

<br><br>

## Image Example

<br>

![dl-02-gradient_descent](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-02-gradient_descent.png?raw=true){: width="500px"}  

<br>

- 기울기가 음수일 경우 $x$ 증가

- 기울기가 양수일 경우 $x$ 감소

- 기울기의 (절대값)크기에 따라 이동량도 비례한다.

<br><br>

## Global Minimum, Local Minimum

<br>

![ds-02-minimum](https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-02-minimum.png?raw=true){: width="500px"}  

<br>

함수가 복잡해질수록 경사하강법을 통해 도달한 지점이 최솟값(Global Minimum)이 아닐 수 있다.  
(차원이 증가할수록 위의 이미지처럼 Local Minimum 으로 접근할 가능성이 커진다.)  
이러한 문제를 예방하기 위해서 출발지점이 다르게 여러번 시도한다.  

- 출발지점이 다르다는 것은 **W (weight)의 초기값**을 다르게 설정해준다는 의미이다.  

- Gradient Descent Algorithm 이 출발점에 관계없이  
최솟값 (Global Minimum) 에 도달하는 함수를 Convex Function 이라고 한다.

<br><br>

## Implementation Process

Cost Function 이 **MSE**(= Mean Square Error *평균제곱오차*) 의 형태인 경우 다음과 같다.  

<br>

$$ H(x) = Wx + b $$

<br>

$$ cost(W, b) = \frac{1}{m} \sum_{i=1}^m (H(x^{(i)}) - y^{(i)})^2 $$  

```python
# Python Implementation Code For TensorFlow
hypothesis = X*W + b  # (= tf.matmul(X, W) + b)
cost = tf.reduce_mean(tf.square(hypothesis - Y))
```

<br>

Cost Function 은 다음과 같이 표현할 수 있다.  

<br>

$$ cost(W, b) = \frac{1}{m} \sum_{i=1}^m (Wx^{(i)} +b - y^{(i)})^2 $$  

<br>

해당 식을 미분하기 위해 편의상 $\frac{1}{2}$ 을 곱한다. (결과값에 영향을 주지 않는다.)  

<br>

$$ cost(W, b) = \frac{1}{2m} \sum_{i=1}^m (Wx^{(i)} +b - y^{(i)})^2 $$  

<br>

이제 위의 식을 $W$ 에 대해 편미분한다. (미분 방법에 대한 설명은 생략)

<br>

$$ \frac{\delta}{\delta W}cost(W, b) = \frac{1}{m} \sum_{i=1}^m (Wx^{(i)} +b - y^{(i)})x^{(i)} $$  

```python
# Python Implementation Code For TensorFlow
cost_derivative_W = tf.reduce_mean((X*W + b - Y) * X)
```

<br>

업데이트 될 W 의 결과는 다음과 같다.

$$ W := W -\alpha \frac{1}{m} \sum_{i=1}^m (Wx^{(i)} +b - y^{(i)})x^{(i)} $$

```python
# Python Implementation Code For TensorFlow
hypothesis = X*W + b
cost_derivative_W = tf.reduce_mean((X*W + b - Y) * X)
gradient_descent_W = W - (learning_rate * cost_derivative_W)
W_train = W.assign(gradient_descent_W)
```

> $b$ (Bias) 또한 업데이트 해야하므로 Cost Function 을 b 에 대해 편미분하여  
> 위와 동일한 과정을 수행한다.

<br>

### Python Implementation Code Example

```python
import tensorflow as tf


x_data = [1, 2, 3]
y_data = [1, 2, 3]

W = tf.Variable(tf.random_normal([1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')
X = tf.placeholder(tf.float32)
Y = tf.placeholder(tf.float32)

hypothesis = X*W + b

# Cost Function 을 직접 미분하여 Gradient Descent Algorithm 을 구현
cost = tf.reduce_mean(tf.square(hypothesis - Y))
cost_derivative_W = tf.reduce_mean((X*W + b - Y) * X)  # W 에 대해서 편미분
cost_derivative_b = tf.reduce_mean(X*W + b - Y)  # b 에 대해서 편미분

# Gradient Descent -= learning_rate * cost_derivative
learning_rate = 0.1
gradient_descent_W = W - (learning_rate * cost_derivative_W)
gradient_descent_b = b - (learning_rate * cost_derivative_b)

# Update
W_train = W.assign(gradient_descent_W)
b_train = b.assign(gradient_descent_b)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    feed_dict={X: x_data, Y: y_data}
    for step in range(1000+1):
        cost_val, W_val, b_val = sess.run(
            [cost, W_train, b_train], feed_dict=feed_dict
        )
        if step % 100 == 0:
            print(f'Step: {step:<5}, Cost: {cost_val:.7f}, W: {W_val}, b: {b_val}')
```

```
Step: 0    , Cost: 1.2016736, W: [1.6824749], b: [-1.0806379]
Step: 100  , Cost: 0.0175198, W: [1.1518828], b: [-0.34526518]
Step: 200  , Cost: 0.0015602, W: [1.045325], b: [-0.10303453]
Step: 300  , Cost: 0.0001389, W: [1.013526], b: [-0.03074778]
Step: 400  , Cost: 0.0000124, W: [1.0040364], b: [-0.00917576]
Step: 500  , Cost: 0.0000011, W: [1.0012045], b: [-0.00273821]
Step: 600  , Cost: 0.0000001, W: [1.0003594], b: [-0.00081709]
Step: 700  , Cost: 0.0000000, W: [1.0001073], b: [-0.00024391]
Step: 800  , Cost: 0.0000000, W: [1.0000321], b: [-7.291967e-05]
Step: 900  , Cost: 0.0000000, W: [1.0000097], b: [-2.1884187e-05]
Step: 1000 , Cost: 0.0000000, W: [1.0000029], b: [-6.4763844e-06]
```

<br><br>

## Gradient Descent Algorithm Of TensorFlow

Gradient Descent Algorithm 은 TesorFlow 에서 구현되어있으므로  
Cost Function 을 직접 미분한 식을 입력할 필요없다.   
`tf.train.GradientDescentOptimizer` 에 Learning Rate 를 인수로 전달한 뒤,  
`minimize` 메서드에 Cost Function 을 전달하기만 하면 된다.

```python
# Python Implementation Code For TensorFlow
hypothesis = X*W + b
cost = tf.reduce_mean(tf.square(hypothesis - Y))  # MSE
optimizer = tf.train.GradientDescentOptimizer(learning_rate=learning_rate).minimize(cost)
```

<br>

### Python Implementation Code Example

```python
import tensorflow as tf


x_data = [1, 2, 3]
y_data = [1, 2, 3]

W = tf.Variable(tf.random_normal([1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')
X = tf.placeholder(tf.float32)
Y = tf.placeholder(tf.float32)

hypothesis = X*W + b
cost = tf.reduce_mean(tf.square(hypothesis - Y))
learning_rate = 0.1
train = tf.train.GradientDescentOptimizer(learning_rate=learning_rate).minimize(cost)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    feed_dict={X: x_data, Y: y_data}
    for step in range(1000+1):
        cost_val, _ = sess.run([cost, train], feed_dict=feed_dict)
        if step % 100 == 0:
            W_val, b_val = sess.run([W, b], feed_dict=feed_dict)
            print(f'Step: {step:<5}, Cost: {cost_val:.7f}, W: {W_val}, b: {b_val}')
```

```
Step: 0    , Cost: 9.2820406, W: [0.8087169], b: [0.08375764]
Step: 100  , Cost: 0.0000229, W: [0.9945698], b: [0.01234425]
Step: 200  , Cost: 0.0000002, W: [0.99952346], b: [0.00108322]
Step: 300  , Cost: 0.0000000, W: [0.9999582], b: [9.504527e-05]
Step: 400  , Cost: 0.0000000, W: [0.9999963], b: [8.372167e-06]
Step: 500  , Cost: 0.0000000, W: [0.99999964], b: [7.984058e-07]
Step: 600  , Cost: 0.0000000, W: [1.], b: [5.9308178e-08]
Step: 700  , Cost: 0.0000000, W: [1.], b: [5.9308178e-08]
Step: 800  , Cost: 0.0000000, W: [1.], b: [5.9308178e-08]
Step: 900  , Cost: 0.0000000, W: [1.], b: [5.9308178e-08]
Step: 1000 , Cost: 0.0000000, W: [1.], b: [5.9308178e-08]
```

> `train` 은 `None` 을 반환한다.

<br><br>

## Learning Rate

데이터를 변형없이 학습시키면, **마지막에 학습시킨 데이터에 편향되어 weight 가 업데이트된다.**  
이러한 편향되는 값을 조정하기 위한 scalar value 를 learning rate 라고 한다.  

$$ (0 < lr < 1) $$

- 값이 지나치게 클 경우 Gradient Descent 과정에서 오버슈팅이 발생할 수 있다.  

- 값이 지나치게 작을 경우 Gradient Descent 과정에서  
Local Minimum 을 최솟값으로 인식하거나, 변화량이 작아 학습이 원활하게 진행되지 않을 수 있다.

<br><br>

# Reference

- [모두를 위한 머신러닝/딥러닝 강의](http://hunkim.github.io/ml/)

---
title: "[ML] Definition, Linear Regression With One Variable"
excerpt: 
last_modified_at: 2019-05-08

categories:
  - MachineLearning

tags:
  - machine_learning
  - definition
  - machine_learning_algorithm
  - supervised_learning
  - unsupervised_leraning
  - linear_regression
  - hypothesis
  - parameter
  - cost_function
  - mean_square_error
  - MSE

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

# What is Machine Learning ?

## Machine Learning Definition

### Arthur Semuel (1959)

```
Field of study that gives computers the ability to learn  
without being explicitly programmed.  
```

<br>

### Tom Mitchell (1998)

```
A computer program is said to learn from  
experience E with respect to some task T  
and some performance measure P,  
if its performance on T, as measured by P,  
improves with experience E.
```

- E: Experience
- T: Task
- P: Performance

<br><br>

## Machine Learning Algorithm

### Supervised Learning (지도학습)

Given "Right Answers"

- **Regression**: Predict *Continuous* valued output

- **Classification**: Predict *Discrete* valued output

<br>

### Unsupervised Learning (비지도학습)

Not given "Right Answers"

- Clustering Algorithm

<br>

### Others

- Reinforcement Learning

- . . .

<br><br>

# Linear Regression With One Variable

**Training Set**  

| Size ($ x $) | Price ($ y $) |
|:--------:|:---------:|
|   1706   |    163    |
|   1321   |    122    |
|    601   |     57    |
|    370   |     32    |
|    180   |     15    |

- **m** : Number of training examples (\= 5)

- **n** : Number of features (\=1)

- $ x $ : "Input" variable, Features

- $ y $ : "Output" variable, "Target" variables, True Values 

- $ (x^{(i)}, y^{(i)}) $ : $ i $-th training set

<br>

## Hypothesis

$ h_\theta (x) = \theta _0 + \theta _1 x $  

$ \theta _i $ : Parameters

> Training Set 을 학습시켜 Parameter 인 $ \theta _i $ 를 최적화 시키는 것이 목표

<br>

## Cost Function

**Mean Square Error (MSE)**  

Hypothesis 의 결과값 $ h_\theta (x ^{(i)} $) 과 실제값 ($ y^{(i)} $) 의  
차의 제곱을 한 뒤 평균을 구한 값을 의미한다. 식으로 표현하면 다음과 같다.  

$$ \frac{1}{m} (h_\theta (x^{(i)}) - y^{(i)})^2 $$

Cost Function 은 각 트레이닝 데이터의 MSE 값을 모두 더한 값으로 정의한다.  
즉 위의 식에서 $ 1\leq i \leq m $ 의 값을 모두 더하는 형태가 된다.  
따라서 식은 아래와 같이 표현된다.  

$$ \sum _{i=1}^{m} \frac{1}{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $$

$$ = \frac{1}{m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $$

위의 식에 $ \frac{1}{2} $ 를 곱해준다.  

$$ \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $$

> $ \frac{1}{2} $ 을 곱한 것은 Gradient Descent Algorithm 을 적용할 때 해당식을  
> 미분하게 되는데, **계산의 편의를 위한 것**이다.  
> 결과에는 영향을 크게 미치지 않는다.

<br>

위에서 도출된 식을 Cost Function $ J(\theta _0, \theta _1) $ 라고 정의한다.  
최종적으로 Cost Function 을 나타내면 다음과 같다.

<br>

$$ J(\theta _0, \theta _1) = \frac{1}{2m} \sum _{i=1}^{m} (h _\theta (x^{(i)}) - y^{(i)})^2 $$

<br>

이렇게 나타난 Cost Function $ J(\theta _0, \theta _1) $ 를  
최소화(minimize) 시키는 것이 최종 목표(Goal)가 된다.  

<br>

## Note

- Linear Regression 의 Cost Function 은 항상 Convex Function 이다.

- 2개의 Parameter $ \theta _0 $, $ \theta _1 $ 를 가진 Cost Function $ J(\theta _0, \theta _1) $ 은  
그래프로 표현하면 3차원의 Convex Function 이 나타나게 된다.

<br>

## Summary

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

# Reference

| Lecture       |          Machine Learning (Offered By Stanford)          |
|---------------|:--------------------------------------------------------:|
| Instructor    |                         Andrew Ng                        |
| Coursera Link | [Link](https://www.coursera.org/learn/machine-learning?) |

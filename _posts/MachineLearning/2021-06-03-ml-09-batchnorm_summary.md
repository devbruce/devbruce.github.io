---
title: "[ML] Batch Normalization Summary"
excerpt: 
last_modified_at: 2021-06-03

categories:
  - MachineLearning

tags:
  - machine_learning
  - deep_learning
  - batch_normalization
  - internal_covariate_shift

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

# Batch Normalization

Internal Covariate Shift 을 해결하기 위해 등장

<br>

## Internal Covariate Shift

Network에서 각 노드의 출력값의 분포가 Training iteration 마다 변화되는 현상  
(Input: mini-batch 데이터)

<br>

### Covariate Shift

- Training Dataset 의 입력분포와 Test Dataset 의 입력분포가 달라지는 현상  

**Internal Covariate Shift** 와 같은 개념은 아니므로 주의  
{: .notice--warning}

<br><br>

## Calculation Process

> - batch-size 의 크기는 4 이고, 각 mini-batch 에 해당하는 데이터 $ d _1 $, $ d _2 $, $ d _3 $, $ d _4 $ 가 있다고 가정  
- Network 의 많은 node 중 예시로 들 node 한 개를 `n` 이라고 명명

<br>

- Network 에 mini-batch data($ d _1 $, $ d _2 $, $ d _3 $, $ d _4 $)를 Input 데이터로 입력  
→ 노드 `n` 의 출력값 $ o _1 $,  $ o _2 $, $ o _3 $, $ o _4 $ 을 구한다.

<br>

- $ o _1 $,  $ o _2 $, $ o _3 $, $ o _4 $ 의 평균($ \mu $) 과 표준편차($ \sigma $) 를 구한다.

<br>

- $ o _1 $,  $ o _2 $, $ o _3 $, $ o _4 $ 를 위에서 구한 $ \mu $, $ \sigma $ 를 활용하여 Z-transform 으로  
$ z _1 $,  $ z _2 $, $ z _3 $, $ z _4 $ 를 구한 뒤, 각각 Activation Function 에 Input 으로 전달
  - Z-transform 과정에서 bias 가 사라지는 문제가 발생한다.  
  이를 해결하기 위해 Batch Normalization Layer 에서 자체적으로 학습 파라미터 $ \beta $ 를 추가하여 bias 를 대체한다.  
  $ z _1  + \beta $  
  $ z _2  + \beta $  
  $ z _3  + \beta $  
  $ z _4  + \beta $  
  <br>  
  - Network 의 정교함을 위해 학습 파라미터 $ \gamma $ 추가  
  $ \gamma z _1  + \beta $  
  $ \gamma z _2  + \beta $  
  $ \gamma z _3  + \beta $  
  $ \gamma z _4  + \beta $  

<br>

- 학습 파라미터 $ \beta $, $ \gamma $ 는 각 노드마다 가지게 되는 학습 파라미터이다.

<br>

### Training Process

mini-batch 데이터($ d _1 $, $ d _2 $, $ d _3 $, $ d _4 $)들의 평균($ \mu $) 과 표준편차($ \sigma $) 을 구하기 위해서  
출력값 $ o _1 $, $ o _2 $, $ o _3 $, $ o _4 $ 을 저장하고, Z-transform 으로 Normalization 한 뒤 Forward-Propagation 이 진행된다.  

**→ 계산량과 메모리 사용량이 증가한다.**
{: .notice--warning}

<br>

### Test Process

mini-batch size 가 2 이상인 경우도 있겠지만, Test는 일반적으로 mini-batch의 사이즈가 1 이다.  
mini-batch 의 사이즈가 1 인 경우 입력값의 평균($ \mu $)과 표준편차($ \sigma $)를 구하는 것에 의미가 없다.  

→ 일반적으로 Training Data 평균들의 평균,분산들의 평균을 사용하여  
Normalization 프로세스가 진행된다.
{: .notice--info}

<br><br>

## Result

### Advantage

- 모델 Regularization 효과 발생

- 초기 Learning Rate 를 크게해도 학습이 가능 (by Normalization)  
→ 학습속도 향상

<br><br>

### Disadvantage

- 메모리 사용량이 크다.  

- mini-batch size가 작을 경우 적용이 어렵다.

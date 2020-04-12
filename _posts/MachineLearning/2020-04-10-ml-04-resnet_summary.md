---
title: "[ML] ResNet Summary"
excerpt: 
last_modified_at: 2020-04-10

categories:
  - MachineLearning

tags:
  - resnet
  - pre-activation
  - deep-learning
  - pytorch

---

<script type="text/x-mathjax-config">
	MathJax.Hub.Config({
	  tex2jax: {
	    inlineMath: [ ['$','$'], ['\\(','\\)'] ]
	  }
	});
</script>

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>

![torch-version-1.4.0](https://img.shields.io/badge/torch-v1.4.0-brightgreen)

# ResNet Summary

- Paper Title: Deep Residual Learning for Image Recognition

- Link: <https://arxiv.org/abs/1512.03385>

## Citation

```
@misc{he2015deep,
    title={Deep Residual Learning for Image Recognition},
    author={Kaiming He and Xiangyu Zhang and Shaoqing Ren and Jian Sun},
    year={2015},
    eprint={1512.03385},
    archivePrefix={arXiv},
    primaryClass={cs.CV}
}
```

<br>

## Intro

- Network 의 depth 가 클수록 (Deep Network) 좋은 퍼포먼스를 보임

- Network 의 depth 가 커지면 `Gradient Vanishing / Exploding` 이 발생.  
→ 지금까지 Normalized initialization, Intermediate normalization layer 로 addressed 됨.  

- Deeper network 가 converging 가능하게 되면서 새로운 문제 `Degradation` 발생.  
→ Network 의 depth 가 증가할수록 acc 가 saturated (포화)되어 급격하게 degrade (감소)

- `Degradation` 은 예상과 다르게 overfitting 이 원인이 아니다.  
적절한 Deep model 에 더 많은 layer 를 추가하면 더 높은 Training Error 가 발생하였고,  
우리의 실험에서 완전히 증명되었다고 언급 (?).  

- `Degradation` (of training accuracy) 은 모든 시스템이 유사하게 최적화하기 쉽지 않다는 것을 보여준다.  
( Page 1 마지막 문단의 시작말이며, 해당 문단은 아직 이해 X )

- `Degradation` 문제를 해결하기 위해 *deep residual learning framework* 를 다룬다고 언급.
 
<br><br>

<center><img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-04-fig2.png?raw=true" width="400" /></center>

<br>

Underlying (근본적인) Mapping  $ H(x) $ 로 표시된 쌓인 비선형 레이어가 있을 때  
해당 $ H(x) $ 를 또 다른 Mapping $ F(x) := H(x) - x $ 로 fit 시킬 수 있다.  

- $ H(x) = F(x) + x $ : Original Mapping (Unreferenced Mapping)

- $ F(x) = H(x) - x $ : Residual Mapping

- 해당 논문에서는 Residual Mapping 이 더 optimize 하기 쉽다고 가정한다.

- 극단적으로, Identity Mapping 이 Optimal 일 때,  
나머지 (Residual) 에 해당하는 $ x $ 를 $ 0 $ 으로 만드는 것이 Identity Mapping 에 fit 시키는 것보다 쉽다.

- $ F(x) + x $ 는 feedforward neural network 의 `short connection` 으로 인지될 수 있다.  
(Figure 2 이미지 참조)

<br>

**short connection**: Identity Mapping

- Extra parameter (x)
- Extra computation complexity (x)

<br><br>

## 3.1 Residual Learning

- Multiple nonlinear layers 가 복잡한 학습도 근사 가능하다고 가정한다면  
$ H(x) - x $ (Residual Function) 도 근사 가능하다.  
즉 $ F(x) = H(x) -x $ 가 근사 가능하며,  
Original Function 인 $ H(x) $ 는 $ H(x) = F(x) + x $ 가 된다.

- `Degradation` 문제는 solver 가 multiple nonlinear layers 에 의해서  
Identity Mapping 을 근사하는 것에 어려움을 가진다는 것을 보여준다.  
Residual Learning 재공식화를 통해서, 만약 Identity Mapping 이 optimal 일 때,  
Identity Mapping 에 가까워지기 위해서 solver 는 간단하게 multiple nonlinear layer 의 weight 를 $ 0 $ 으로 만든다.

- 실제로는 Identity Mapping 이 optimal 은 아니겠지만,  
우리의 재공식화는 문제의 precondition (전처리...?)를 돕는다.  
만약 optimal function 이 Zero Mapping 보다 Identity Mapping 에 가깝다면,  
solver 는 새로운 함수를 학습하는 것 보다 Identity Mapping 를 참조하여 작은 변화를 더 쉽게 찾을 수 있게된다.

<br><br>

## Architecture

<center><img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-04-fig3.png?raw=true" width="500" /></center>

### Plain Network

- Feature map 출력 사이즈가 동일하면, filter 의 갯수도 동일하게 유지

- Feature map 출력 사이즈가 반으로 줄어들 경우 (Conv with **Stride 2**),  
filter 의 갯수는 2배로 증가 (Layer 마다 Time Complexity 유지를 위해)  

> Downsampling 은 Conv with **Stride 2** 만을 사용하므로  
> 3x3 Conv 의 경우에도 padding=1 을 주어 Feature Map Size 를 유지한다.

- End of Network: Global Average Pooling, 1000-way fully-connected layer with softmax

<br>

### Residual Network

- 기본적인 조건은 위의 Plain Network 와 동일

- shortcut connection 추가

<br>

### Shortcut Connection

- input 과 output 이 동일한 dimension 일 경우 (**Identity Shortcut**)  
(Solid line in architecture image)

- output 의 dimension 이 input 보다 증가한 경우 (Dotted line in architecture image)
  - Option A: (**Zero Padding**) → No extra parameter
  - Option B: 1 x 1 Conv (**Projection Shortcut**) → Extra parameter

<br>

해당 논문에서는 shortcut 의 사용방법에 따른 성능비교를 보여준다.

- (A) Increasing Dimension 에 Zero Padding 을 활용한 Shortcut 을 사용 (나머지는 모두 Identity Shortcut)

- (B) Increasing Dimension 에 Projection Shrotcut 을 사용 (나머지는 모두 Identity Shortcut)

- (C) 모든 Shortcut 을 Projection Shortcut 으로 사용

퍼포먼스 비교결과: A < B < C (B is slightly better than A, C is marginally better than B)

(C) 는 extra parameter 에 의한 결과라고 언급하며,  
(A), (B), (C) 에서의 작은 차이를 통해 알 수 있는 것은, Projection Shortcut 이 degradation 문제를  
address 하는 것의 본질이 아니라는 것을 보여준다.  
따라서 해당 논문에서는 모델크기, memory/time complexity 를 줄이기 위해 (C) 를 사용하지 않는다.

<br><br>

### Deeper Bottleneck Architectures

**Residual Block For Bottleneck (3 Layer)**  

Layer 가 깊어지면 training time 이 증가하는 것을 고려하여 Residual Block 을 개선  

<br>

<center><img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-04-fig5.png?raw=true" width="500" /></center>

<br>

- 1 x 1 Layer: Dimension 을 줄이고 늘리는(복구하는) 것을 담당

- 3 x 3 Layer: leaving the 3×3 layer a bottleneck with smaller input/output dimensions


- **Identity Shortcut (parameter-free) 가 bottleneck architecture 에서 중요하다.**  
  - Identity shortcut 이 projection (1 x 1 conv) 으로 대체될 경우  
  time complexity 와 model size 가 두배로 증가할 수 있다.  

<br><br>

### Number of Layers

<center><img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-04-table1.png?raw=true" width="1200" /></center>

- Residual Unit (3x3, 3x3) 을 사용할 경우 **(unit 갯수 * 2) + 2** (Init Conv, FC)  
(18-layer, 34-layer 에서 사용, Table 1 참조)

- Bottleneck Residual Unit (1x1, 3x3, 1x1) 을 사용할 경우 **(unit 갯수 * 3) + 2** (Init Conv, FC)  
(50-layer 이상부터 사용, Table 1 참조)

<br><br>

## Subsequent Paper

- Paper Title: Identity Mappings in Deep Residual Networks

- Link: <https://arxiv.org/abs/1603.05027>

### Citation

```
@misc{he2016identity,
    title={Identity Mappings in Deep Residual Networks},
    author={Kaiming He and Xiangyu Zhang and Shaoqing Ren and Jian Sun},
    year={2016},
    eprint={1603.05027},
    archivePrefix={arXiv},
    primaryClass={cs.CV}
}
```

<br>

### Simple Summary

<center><img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-04-sub_fig1_left.png?raw=true" width="300" /></center>

- Shortcut Connection 의 값은 변환없이 "Clean" 하게 유지하는 것이 최적화에 유리  
(a) original 의 경우 skip connection path 에 `ReLU` 가 있어 (b) proposed 보다 clean 하지 못하다.  

<br>

- (a) Post-activation (original)
  - Form: **`Conv3x3` + `BN` + `ReLU` + `Conv3x3` + `BN` + `(Shortcut connection)` + `ReLU`**  

<br>

- (b) Pre-activation (**Proposed**)
  - Form: **`BN` + `ReLU` + `Conv3x3` + `BN` + `ReLU` + `Conv3x3` + `(Shortcut connection)`**

<br><br>

### ResNet Code Implementation with Pytorch

- Code Link: <https://github.com/DevBruce/torch-implementation/blob/master/resnet.py>

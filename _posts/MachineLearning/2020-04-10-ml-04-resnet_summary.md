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

### ResNet Code Implementation with Pytorch (pre-activation)

```python
import torch
import torch.nn as nn
from torchsummary import summary


def preact_conv_block(in_channels, out_channels, *args, **kwargs):
    ret = nn.Sequential(
        nn.BatchNorm2d(in_channels),
        nn.ReLU(),
        nn.Conv2d(in_channels, out_channels, *args, **kwargs),
    )
    return ret


class global_avg_pool2d(nn.Module):
    def forward(self, x):
        _, _, h, w = x.shape  # x.shape == (B, C, H, W)
        return nn.AvgPool2d(kernel_size=(h, w))(x)


class PreactResidualUnit(nn.Module):
    expansion = 1
    
    def __init__(self, in_channels, channels, stride):
        super().__init__()
        self.preact_conv_block1 = preact_conv_block(in_channels, channels, kernel_size=3, stride=stride, padding=1, bias=False)
        self.preact_conv_block2 = preact_conv_block(channels, channels, kernel_size=3, stride=1, padding=1, bias=False)
        
        if not (stride == 1 and in_channels == channels*self.expansion):  # not (Keep Feature Size and Keep number of filters)
            self.shortcut = preact_conv_block(in_channels, channels*self.expansion, kernel_size=1, stride=stride, bias=False)  # Projection Shortcut
            
    def forward(self, x):
        shortcut = self.shortcut(x) if hasattr(self, 'shortcut') else x
        
        out = self.preact_conv_block1(x)
        out = self.preact_conv_block2(out)
        out += shortcut
        return out
    

class PreactBottleneckResidualUnit(nn.Module):
    expansion = 4
    
    def __init__(self, in_channels, channels, stride):
        super().__init__()
        self.preact_conv_block1 = preact_conv_block(in_channels, channels, kernel_size=1, bias=False)
        self.preact_conv_block2 = preact_conv_block(channels, channels, kernel_size=3, stride=stride, padding=1, bias=False)
        self.preact_conv_block3 = preact_conv_block(channels, channels*self.expansion, kernel_size=1, bias=False)
        
        if not (stride == 1 and in_channels == channels*self.expansion):  # not (Keep Feature Size and Keep number of filters)
            self.shortcut = preact_conv_block(in_channels, channels*self.expansion, kernel_size=1, stride=stride, bias=False)  # Projection Shortcut
            
    def forward(self, x):
        shortcut = self.shortcut(x) if hasattr(self, 'shortcut') else x
        
        out = self.preact_conv_block1(x)
        out = self.preact_conv_block2(out)
        out = self.preact_conv_block3(out)
        out += shortcut
        return out
    

class PreactResNet(nn.Module):
    def __init__(self, block, num_blocks, num_classes, init_channels=3):
        super().__init__()
        self.init_channels = init_channels
        self.in_channels = 64
        
        self.init = nn.Sequential(
            nn.Conv2d(self.init_channels, self.in_channels, kernel_size=7, padding=3, stride=2),
            nn.MaxPool2d(kernel_size=3, padding=1, stride=2),
        )
        self.encoder = nn.Sequential(
            self._make_layer(block, num_blocks[0], out_channels=64, stride=1),  # Final out channels is (out_channels * block.expansion)
            self._make_layer(block, num_blocks[1], out_channels=128, stride=2),
            self._make_layer(block, num_blocks[2], out_channels=256, stride=2),
            self._make_layer(block, num_blocks[3], out_channels=512, stride=2),
        )
        self.decoder = nn.Sequential(
            global_avg_pool2d(),
            nn.Flatten(),
            nn.Linear(512*block.expansion, num_classes),
            nn.Softmax(),
        )

    def _make_layer(self, block, num_blocks, out_channels, stride):
        strides = [stride] + [1]*(num_blocks-1)  # ex) (num_blocks == 3 and stride == 2) --> [2, 1, 1]
        layers = []
        for stride in strides:
            layers.append(block(self.in_channels, out_channels, stride))
            self.in_channels = out_channels * block.expansion
            
        return nn.Sequential(*layers)

    def forward(self, x):
        out = self.init(x)
        out = self.encoder(out)
        out = self.decoder(out)
        return out
    

def PreactResNet18():  # (2 + 2 + 2 + 2) * 2 + 2 = 18
    return PreactResNet(block=PreactResidualUnit, num_blocks=[2, 2, 2, 2], num_classes=10)

def PreactResNet34():  # (3 + 4 + 6 + 3) * 2 + 2 = 34
    return PreactResNet(block=PreactResidualUnit, num_blocks=[3, 4, 6, 3], num_classes=10)

def PreactResNet50():  # (3 + 4 + 6 + 3) * 3 + 2 = 50
    return PreactResNet(block=PreactBottleneckResidualUnit, num_blocks=[3, 4, 6, 3], num_classes=10)

def PreactResNet101():  # (3 + 4 + 23 + 3) * 3 + 2 = 101
    return PreactResNet(block=PreactBottleneckResidualUnit, num_blocks=[3, 4, 23, 3], num_classes=10)

def PreactResNet152():  # (3 + 8 + 36 + 3) * 3 + 2 = 152
    return PreactResNet(block=PreactBottleneckResidualUnit, num_blocks=[3, 8, 36, 3], num_classes=10)


summary(model=PreactResNet50().cpu(), input_size=(3, 224, 224), device='cpu')
```

```
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv2d-1         [-1, 64, 112, 112]           9,472
         MaxPool2d-2           [-1, 64, 56, 56]               0
       BatchNorm2d-3           [-1, 64, 56, 56]             128
              ReLU-4           [-1, 64, 56, 56]               0
            Conv2d-5          [-1, 256, 56, 56]          16,384
       BatchNorm2d-6           [-1, 64, 56, 56]             128
              ReLU-7           [-1, 64, 56, 56]               0
            Conv2d-8           [-1, 64, 56, 56]           4,096
       BatchNorm2d-9           [-1, 64, 56, 56]             128
             ReLU-10           [-1, 64, 56, 56]               0
           Conv2d-11           [-1, 64, 56, 56]          36,864
      BatchNorm2d-12           [-1, 64, 56, 56]             128
             ReLU-13           [-1, 64, 56, 56]               0
           Conv2d-14          [-1, 256, 56, 56]          16,384
PreactBottleneckResidualUnit-15          [-1, 256, 56, 56]               0
      BatchNorm2d-16          [-1, 256, 56, 56]             512
             ReLU-17          [-1, 256, 56, 56]               0
           Conv2d-18           [-1, 64, 56, 56]          16,384
      BatchNorm2d-19           [-1, 64, 56, 56]             128
             ReLU-20           [-1, 64, 56, 56]               0
           Conv2d-21           [-1, 64, 56, 56]          36,864
      BatchNorm2d-22           [-1, 64, 56, 56]             128
             ReLU-23           [-1, 64, 56, 56]               0
           Conv2d-24          [-1, 256, 56, 56]          16,384
PreactBottleneckResidualUnit-25          [-1, 256, 56, 56]               0
      BatchNorm2d-26          [-1, 256, 56, 56]             512
             ReLU-27          [-1, 256, 56, 56]               0
           Conv2d-28           [-1, 64, 56, 56]          16,384
      BatchNorm2d-29           [-1, 64, 56, 56]             128
             ReLU-30           [-1, 64, 56, 56]               0
           Conv2d-31           [-1, 64, 56, 56]          36,864
      BatchNorm2d-32           [-1, 64, 56, 56]             128
             ReLU-33           [-1, 64, 56, 56]               0
           Conv2d-34          [-1, 256, 56, 56]          16,384
PreactBottleneckResidualUnit-35          [-1, 256, 56, 56]               0
      BatchNorm2d-36          [-1, 256, 56, 56]             512
             ReLU-37          [-1, 256, 56, 56]               0
           Conv2d-38          [-1, 512, 28, 28]         131,072
      BatchNorm2d-39          [-1, 256, 56, 56]             512
             ReLU-40          [-1, 256, 56, 56]               0
           Conv2d-41          [-1, 128, 56, 56]          32,768
      BatchNorm2d-42          [-1, 128, 56, 56]             256
             ReLU-43          [-1, 128, 56, 56]               0
           Conv2d-44          [-1, 128, 28, 28]         147,456
      BatchNorm2d-45          [-1, 128, 28, 28]             256
             ReLU-46          [-1, 128, 28, 28]               0
           Conv2d-47          [-1, 512, 28, 28]          65,536
PreactBottleneckResidualUnit-48          [-1, 512, 28, 28]               0
      BatchNorm2d-49          [-1, 512, 28, 28]           1,024
             ReLU-50          [-1, 512, 28, 28]               0
           Conv2d-51          [-1, 128, 28, 28]          65,536
      BatchNorm2d-52          [-1, 128, 28, 28]             256
             ReLU-53          [-1, 128, 28, 28]               0
           Conv2d-54          [-1, 128, 28, 28]         147,456
      BatchNorm2d-55          [-1, 128, 28, 28]             256
             ReLU-56          [-1, 128, 28, 28]               0
           Conv2d-57          [-1, 512, 28, 28]          65,536
PreactBottleneckResidualUnit-58          [-1, 512, 28, 28]               0
      BatchNorm2d-59          [-1, 512, 28, 28]           1,024
             ReLU-60          [-1, 512, 28, 28]               0
           Conv2d-61          [-1, 128, 28, 28]          65,536
      BatchNorm2d-62          [-1, 128, 28, 28]             256
             ReLU-63          [-1, 128, 28, 28]               0
           Conv2d-64          [-1, 128, 28, 28]         147,456
      BatchNorm2d-65          [-1, 128, 28, 28]             256
             ReLU-66          [-1, 128, 28, 28]               0
           Conv2d-67          [-1, 512, 28, 28]          65,536
PreactBottleneckResidualUnit-68          [-1, 512, 28, 28]               0
      BatchNorm2d-69          [-1, 512, 28, 28]           1,024
             ReLU-70          [-1, 512, 28, 28]               0
           Conv2d-71          [-1, 128, 28, 28]          65,536
      BatchNorm2d-72          [-1, 128, 28, 28]             256
             ReLU-73          [-1, 128, 28, 28]               0
           Conv2d-74          [-1, 128, 28, 28]         147,456
      BatchNorm2d-75          [-1, 128, 28, 28]             256
             ReLU-76          [-1, 128, 28, 28]               0
           Conv2d-77          [-1, 512, 28, 28]          65,536
PreactBottleneckResidualUnit-78          [-1, 512, 28, 28]               0
      BatchNorm2d-79          [-1, 512, 28, 28]           1,024
             ReLU-80          [-1, 512, 28, 28]               0
           Conv2d-81         [-1, 1024, 14, 14]         524,288
      BatchNorm2d-82          [-1, 512, 28, 28]           1,024
             ReLU-83          [-1, 512, 28, 28]               0
           Conv2d-84          [-1, 256, 28, 28]         131,072
      BatchNorm2d-85          [-1, 256, 28, 28]             512
             ReLU-86          [-1, 256, 28, 28]               0
           Conv2d-87          [-1, 256, 14, 14]         589,824
      BatchNorm2d-88          [-1, 256, 14, 14]             512
             ReLU-89          [-1, 256, 14, 14]               0
           Conv2d-90         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-91         [-1, 1024, 14, 14]               0
      BatchNorm2d-92         [-1, 1024, 14, 14]           2,048
             ReLU-93         [-1, 1024, 14, 14]               0
           Conv2d-94          [-1, 256, 14, 14]         262,144
      BatchNorm2d-95          [-1, 256, 14, 14]             512
             ReLU-96          [-1, 256, 14, 14]               0
           Conv2d-97          [-1, 256, 14, 14]         589,824
      BatchNorm2d-98          [-1, 256, 14, 14]             512
             ReLU-99          [-1, 256, 14, 14]               0
          Conv2d-100         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-101         [-1, 1024, 14, 14]               0
     BatchNorm2d-102         [-1, 1024, 14, 14]           2,048
            ReLU-103         [-1, 1024, 14, 14]               0
          Conv2d-104          [-1, 256, 14, 14]         262,144
     BatchNorm2d-105          [-1, 256, 14, 14]             512
            ReLU-106          [-1, 256, 14, 14]               0
          Conv2d-107          [-1, 256, 14, 14]         589,824
     BatchNorm2d-108          [-1, 256, 14, 14]             512
            ReLU-109          [-1, 256, 14, 14]               0
          Conv2d-110         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-111         [-1, 1024, 14, 14]               0
     BatchNorm2d-112         [-1, 1024, 14, 14]           2,048
            ReLU-113         [-1, 1024, 14, 14]               0
          Conv2d-114          [-1, 256, 14, 14]         262,144
     BatchNorm2d-115          [-1, 256, 14, 14]             512
            ReLU-116          [-1, 256, 14, 14]               0
          Conv2d-117          [-1, 256, 14, 14]         589,824
     BatchNorm2d-118          [-1, 256, 14, 14]             512
            ReLU-119          [-1, 256, 14, 14]               0
          Conv2d-120         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-121         [-1, 1024, 14, 14]               0
     BatchNorm2d-122         [-1, 1024, 14, 14]           2,048
            ReLU-123         [-1, 1024, 14, 14]               0
          Conv2d-124          [-1, 256, 14, 14]         262,144
     BatchNorm2d-125          [-1, 256, 14, 14]             512
            ReLU-126          [-1, 256, 14, 14]               0
          Conv2d-127          [-1, 256, 14, 14]         589,824
     BatchNorm2d-128          [-1, 256, 14, 14]             512
            ReLU-129          [-1, 256, 14, 14]               0
          Conv2d-130         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-131         [-1, 1024, 14, 14]               0
     BatchNorm2d-132         [-1, 1024, 14, 14]           2,048
            ReLU-133         [-1, 1024, 14, 14]               0
          Conv2d-134          [-1, 256, 14, 14]         262,144
     BatchNorm2d-135          [-1, 256, 14, 14]             512
            ReLU-136          [-1, 256, 14, 14]               0
          Conv2d-137          [-1, 256, 14, 14]         589,824
     BatchNorm2d-138          [-1, 256, 14, 14]             512
            ReLU-139          [-1, 256, 14, 14]               0
          Conv2d-140         [-1, 1024, 14, 14]         262,144
PreactBottleneckResidualUnit-141         [-1, 1024, 14, 14]               0
     BatchNorm2d-142         [-1, 1024, 14, 14]           2,048
            ReLU-143         [-1, 1024, 14, 14]               0
          Conv2d-144           [-1, 2048, 7, 7]       2,097,152
     BatchNorm2d-145         [-1, 1024, 14, 14]           2,048
            ReLU-146         [-1, 1024, 14, 14]               0
          Conv2d-147          [-1, 512, 14, 14]         524,288
     BatchNorm2d-148          [-1, 512, 14, 14]           1,024
            ReLU-149          [-1, 512, 14, 14]               0
          Conv2d-150            [-1, 512, 7, 7]       2,359,296
     BatchNorm2d-151            [-1, 512, 7, 7]           1,024
            ReLU-152            [-1, 512, 7, 7]               0
          Conv2d-153           [-1, 2048, 7, 7]       1,048,576
PreactBottleneckResidualUnit-154           [-1, 2048, 7, 7]               0
     BatchNorm2d-155           [-1, 2048, 7, 7]           4,096
            ReLU-156           [-1, 2048, 7, 7]               0
          Conv2d-157            [-1, 512, 7, 7]       1,048,576
     BatchNorm2d-158            [-1, 512, 7, 7]           1,024
            ReLU-159            [-1, 512, 7, 7]               0
          Conv2d-160            [-1, 512, 7, 7]       2,359,296
     BatchNorm2d-161            [-1, 512, 7, 7]           1,024
            ReLU-162            [-1, 512, 7, 7]               0
          Conv2d-163           [-1, 2048, 7, 7]       1,048,576
PreactBottleneckResidualUnit-164           [-1, 2048, 7, 7]               0
     BatchNorm2d-165           [-1, 2048, 7, 7]           4,096
            ReLU-166           [-1, 2048, 7, 7]               0
          Conv2d-167            [-1, 512, 7, 7]       1,048,576
     BatchNorm2d-168            [-1, 512, 7, 7]           1,024
            ReLU-169            [-1, 512, 7, 7]               0
          Conv2d-170            [-1, 512, 7, 7]       2,359,296
     BatchNorm2d-171            [-1, 512, 7, 7]           1,024
            ReLU-172            [-1, 512, 7, 7]               0
          Conv2d-173           [-1, 2048, 7, 7]       1,048,576
PreactBottleneckResidualUnit-174           [-1, 2048, 7, 7]               0
global_avg_pool2d-175           [-1, 2048, 1, 1]               0
         Flatten-176                 [-1, 2048]               0
          Linear-177                   [-1, 10]          20,490
         Softmax-178                   [-1, 10]               0
================================================================
Total params: 23,520,522
Trainable params: 23,520,522
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.57
Forward/backward pass size (MB): 288.86
Params size (MB): 89.72
Estimated Total Size (MB): 379.16
----------------------------------------------------------------
```

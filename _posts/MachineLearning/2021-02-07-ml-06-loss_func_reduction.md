---
title: "[ML] Reduction of Loss Functions"
excerpt: 
last_modified_at: 2021-02-07

categories:
  - MachineLearning

tags:
  - python
  - tensorflow
  - pytorch
  - reduction
  - lose_function

---

![tensorflow-2.4.0](https://img.shields.io/badge/tensorflow-v2.4.0-orange)
![torch-version-1.7.0](https://img.shields.io/badge/torch-v1.7.0-brightgreen)

# TensorFlow

```python
import tensorflow as tf
```

<br>

## MeanSquaredError

> Docs: <https://www.tensorflow.org/api_docs/python/tf/keras/losses/MeanSquaredError>

- `reduction` 의 default 는 `auto` 이며, `tf.distribute.Strategy` 와 같이 쓰이는 경우가 아닐 경우,  
`sum_over_batch_size` 가 default 가 된다.

```python
mse_default = tf.keras.losses.MeanSquaredError(reduction='sum_over_batch_size') 

mse_sum = tf.keras.losses.MeanSquaredError(reduction='sum')
mse_none = tf.keras.losses.MeanSquaredError(reduction='none')


targets = tf.constant([[1., 2., 3., 4.], [1., 2., 3., 4.]])
preds = tf.constant([[4., 5., 6., 7.], [3., 4., 5., 6.]])
```


```python
mse_default(targets, preds).numpy()
>>> 6.5
```

```python
mse_sum(targets, preds).numpy()
>>> 13.0
```

```python
mse_none(targets, preds).numpy()
>>> array([9., 4.], dtype=float32)
```

<br>

## BinaryCrossentropy

```python
bce_default = tf.keras.losses.BinaryCrossentropy(reduction='sum_over_batch_size')

bce_sum = tf.keras.losses.BinaryCrossentropy(reduction='sum')
bce_none = tf.keras.losses.BinaryCrossentropy(reduction='none')


targets = tf.constant([[0, 0, 0, 1], [0, 0, 0, 1], [0, 0, 0, 1], [0, 0, 0, 1]])
preds = tf.constant([[0.2, 0.1, 0.1, 0.6], [0.2, 0.2, 0.2, 0.4], [0.2, 0.1, 0.1, 0.6], [0.2, 0.2, 0.2, 0.4]])
```

```python
bce_default(targets, preds).numpy()
>>> 0.3163013
```

```python
bce_sum(targets, preds).numpy()
>>> 1.2652051
```

```python
bce_none(targets, preds).numpy()  # 1 batch-wise
>>> array([0.2361724 , 0.39643016, 0.2361724 , 0.39643016], dtype=float32)
```

<br><br>

# PyTorch

## MSELoss

> Docs: <https://pytorch.org/docs/stable/generated/torch.nn.MSELoss.html>

- 실제 연산은 reduction 값에 의해 Sum Squared Error 가 될 수도 있다.  
따라서 Sqaured Error 가 더 적절할 것으로 보인다.  
이름이 MSE 인 이유는 reduction 을 특별히 변경하지 않을 경우 mean 연산을 수행하기도 하며,  
보편적으로 Squared Error 연산은 MSE (Mean Sqaured Error) 로 통용되기 때문이 아닐까 추측된다.  

- `reduction='none'` 을 주게 될 경우, Error 값을 element-wise 로 연산한 값을 반환한다. (shape 보존)  
이 때 값은 mean squared error 값이 아닌, **squared error 값**인 것을 확인할 수 있다.  
(Document 를 통해서도 연산식에서 mean 연산이 없음을 확인할 수 있다.)

```python
import torch
import torch.nn as nn


r_mean = nn.MSELoss(reduction='mean')  # default
r_sum = nn.MSELoss(reduction='sum')
r_none = nn.MSELoss(reduction='none')  # element-wise


preds = torch.tensor([[1., 2., 3., 4.], [1., 2., 3., 4.]])
targets = torch.tensor([[4., 5., 6., 7.], [3., 4., 5., 6.]])
```

```python
r_mean(preds, targets).numpy().item()
>>> 6.5
```

```python
r_sum(preds, targets).numpy().item()  # == (9*4 + 4*4)
>>> 52.0
```

```python
r_none(preds, targets).numpy()
>>> array([[9., 9., 9., 9.],
           [4., 4., 4., 4.]], dtype=float32)
```

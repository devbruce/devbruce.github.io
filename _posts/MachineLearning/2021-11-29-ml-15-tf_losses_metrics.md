---
title: "[ML] TensorFlow Losses and Metrics"
excerpt: 
last_modified_at: 2021-11-29

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - loss_function
  - metric
  - cross_entropy
  - accuracy

---

# TensorFlow Loss Functions and Metrics

![tensorflow-v2.6.0](https://img.shields.io/badge/TensorFlow-2.6.0-orange) 

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Losses and Metrics](https://www.youtube.com/watch?v=JxuQCXTqMCM&t)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

- Cross entropy를 직접 계산하는 방법에 대해서 참고가 되었던 글: [Cross-entropy for classification](https://towardsdatascience.com/cross-entropy-for-classification-d98e7f974451)(Author: [Vlastimil Martinek](https://medium.com/@martinekvlastimil))

<br><br>

## Import TensorFlow

```python
import tensorflow as tf


# Save some gpu memories
physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
```

<br><br>

## Losses

### BinaryCrossentropy

```python
from tensorflow.keras.losses import BinaryCrossentropy


# Batch size: 2
preds = tf.constant([[0.3], [0.8]], dtype=tf.float32)  # shape: (2, 1)
labels = tf.constant([[0], [1]], dtype=tf.float32)   # shape: (2, 1)

# tf.keras.losses.BinaryCrossentropy
bce = BinaryCrossentropy()
bce_loss_tf = bce(y_true=labels, y_pred=preds)

# Manual
bce_loss_manual = -1 * (labels * tf.math.log(preds) + (1 - labels) * tf.math.log(1 - preds))
bce_loss_manual = tf.reduce_sum(bce_loss_manual, axis=1)
bce_loss_manual = tf.reduce_mean(bce_loss_manual)

# Print results
print('=== BinaryCrossentropy ===')
print(f'* tf.keras.losses.BinaryCrossentropy: {bce_loss_tf:.4f}')
print(f'* Manual: {bce_loss_manual:.4f}')
```

```
=== BinaryCrossentropy ===
* tf.keras.losses.BinaryCrossentropy: 0.2899
* Manual: 0.2899
```

<br><br>

### CategoricalCrossentropy

```python
from tensorflow.keras.losses import CategoricalCrossentropy


# Batch size: 2, n_class: 3
preds = tf.constant([[0.1, 0.6, 0.3], [0.2, 0.7, 0.1]], dtype=tf.float32)  # shape: (2, 1)
labels = tf.constant([[0, 0, 1], [0, 1, 0]], dtype=tf.float32)   # shape: (2, 1)

# tf.keras.losses.CategoricalCrossentropy
cce = CategoricalCrossentropy()
cce_loss_tf = cce(y_true=labels, y_pred=preds)

# Manual
cce_loss_manual = -1 * labels * tf.math.log(preds)
cce_loss_manual = tf.reduce_sum(cce_loss_manual, axis=1)
cce_loss_manual = tf.reduce_mean(cce_loss_manual)

# Print results
print('=== CategoricalCrossentropy ===')
print(f'* tf.keras.losses.CategoricalCrossentropy: {cce_loss_tf:.4f}')
print(f'* Manual: {cce_loss_manual:.4f}')
```

```
=== CategoricalCrossentropy ===
* tf.keras.losses.CategoricalCrossentropy: 0.7803
* Manual: 0.7803
```

<br><br>

### SparseCategoricalCrossentropy

CategoricalCrossentropy 와 다른 점은  
label이 one-hot encoding이 아닌 class index(int)를 받는다는 것이다.  
{: .notice--info}

```python
from tensorflow.keras.losses import SparseCategoricalCrossentropy


# Batch size: 2, n_class: 3
preds = tf.constant([[0.1, 0.6, 0.3], [0.2, 0.7, 0.1]], dtype=tf.float32)  # shape: (2, 1)
labels = tf.constant([2, 1], dtype=tf.int32)                               # shape: (2, 1)

# tf.keras.losses.SparseCategoricalCrossentropy
scce = SparseCategoricalCrossentropy()
scce_loss_tf = scce(y_true=labels, y_pred=preds)

# Manual
scce_loss_manual = 0
for idx, cls_idx in enumerate(labels):
    pred = preds[idx]
    target_pred = pred[cls_idx]
    scce_loss_manual += -1 * tf.math.log(target_pred)
scce_loss_manual /= len(preds)

# Print results
print('=== SparseCategoricalCrossentropy ===')
print(f'* tf.keras.losses.SparseCategoricalCrossentropy: {scce_loss_tf:.4f}')
print(f'* Manual: {scce_loss_manual:.4f}')
```

```
=== SparseCategoricalCrossentropy ===
* tf.keras.losses.SparseCategoricalCrossentropy: 0.7803
* Manual: 0.7803
```

<br><br>

## Metrics

### CategoricalAccuracy

```python
from tensorflow.keras.metrics import CategoricalAccuracy


# Batch size: 2, n_class: 3
preds = tf.constant([[0.1, 0.6, 0.3], [0.2, 0.7, 0.1]], dtype=tf.float32)  # shape: (2, 1)
labels = tf.constant([[0, 0, 1], [0, 1, 0]], dtype=tf.float32)             # shape: (2, 1)

cat_acc = CategoricalAccuracy()
acc = cat_acc(y_true=labels, y_pred=preds)

# 첫번째 item 오답, 두번째 아이템 정답
print(f'Acc: {acc * 100:.2f}%')
```

```
Acc: 50.00%
```

<br><br>

### SparseCategoricalAccuracy

```python
from tensorflow.keras.metrics import SparseCategoricalAccuracy


# Batch size: 2, n_class: 3
preds = tf.constant([[0.1, 0.6, 0.3], [0.2, 0.7, 0.1]], dtype=tf.float32)  # shape: (2, 1)
labels = tf.constant([2, 1], dtype=tf.int32)                               # shape: (2, 1)

sparse_cat_acc = SparseCategoricalAccuracy()
acc = sparse_cat_acc(y_true=labels, y_pred=preds)

# 첫번째 item 오답, 두번째 아이템 정답
print(f'Acc: {acc * 100:.2f}%')
```

```
Acc: 50.00%
```

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Losses and Metrics](https://www.youtube.com/watch?v=JxuQCXTqMCM&t=1409s)

- [Cross-entropy for classification](https://towardsdatascience.com/cross-entropy-for-classification-d98e7f974451)(Author: [Vlastimil Martinek](https://medium.com/@martinekvlastimil))

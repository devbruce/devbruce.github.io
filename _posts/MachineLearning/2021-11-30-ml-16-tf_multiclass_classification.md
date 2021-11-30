---
title: "[ML] TF Multiclass Classification Model Implementation"
excerpt: 
last_modified_at: 2021-11-30

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - multiclass
  - classification
  - cross_entropy
  - cmap

---

# TF Multiclass Classification Model Implementation

![tensorflow-v2.6.0](https://img.shields.io/badge/TensorFlow-2.6.0-orange) 

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Multiclass Classification](https://www.youtube.com/watch?v=oIwHNy7GXdQ)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

<br><br>

## Import Libraries

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.cm as cm


# Save some gpu memories
physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
    

# Set Random Seed
SEED = 7777
tf.random.set_seed(SEED)
np.random.seed(SEED)
```

<br><br>

## Create Dataset

```python
n_train, n_feature = 200, 2
n_class = 10
noise = 0.05

train_x = tf.zeros(shape=(0, n_feature))
train_y = tf.zeros(shape=(0,), dtype=tf.int32)

for cls_idx in range(n_class):
    cx, cy = tf.random.uniform(minval=-1, maxval=1, shape=(2, ))
    xs = cx + noise * tf.random.normal(shape=(n_train, 1))
    ys = cy + noise * tf.random.normal(shape=(n_train, 1))
    
    coords = tf.concat((xs, ys), axis=1)
    labels = cls_idx * tf.ones(shape=(n_train,), dtype=tf.int32)
    
    train_x = tf.concat((train_x, coords), axis=0)
    train_y = tf.concat((train_y, labels), axis=0)    
    
train_y = tf.one_hot(train_y, depth=n_class, dtype=tf.int32)

# tf.data.Dataset
train_ds = tf.data.Dataset.from_tensor_slices((train_x, train_y))
train_ds = train_ds.shuffle(n_train)
```

`tf.one_hot`: shape=(N,) 형태, dtype은 int형만 처리 가능  
{: .notice--info}

<br><br>

## Visualize Dataset

```python
from collections import defaultdict


train_ds_viz = train_ds.batch(1)
cmap = cm.get_cmap('rainbow', lut=n_class)

data_per_cls_idx = defaultdict(lambda:tf.zeros(shape=(0, n_feature), dtype=tf.float32))
for x, y in train_ds_viz:
    cls_idx = tf.where(y[0] == 1).numpy().item()
    data_per_cls_idx[cls_idx] = tf.concat((data_per_cls_idx[cls_idx], x), axis=0)
    
    
fig, ax = plt.subplots(figsize=(12, 8))
for cls_idx in data_per_cls_idx.keys():
    xs = data_per_cls_idx[cls_idx][:, 0]
    ys = data_per_cls_idx[cls_idx][:, 1]
    ax.scatter(xs, ys, alpha=0.3, color=cmap(cls_idx), label=f'Class Index: {cls_idx}')
    
ax.set_title(f'Multi Class Dataset(n_class: {n_class})', fontdict={'size': 20})
ax.set_xlabel('x axis', fontdict={'size': 15})
ax.set_ylabel('y axis', fontdict={'size': 15})
ax.set_xlim([-1.2, 1.2])
ax.set_ylim([-1.2, 1.2])
ax.legend(fontsize=15)
ax.grid()
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-16-multiclass_ds.png?raw=true" width="600" />

<br><br>

## Build Model

```python
class MultiClassifier(tf.keras.models.Model):
    def __init__(self):
        super().__init__()
        self.dense = tf.keras.layers.Dense(units=n_class)
        self.softmax = tf.keras.layers.Activation('softmax')
        
    def call(self, x):
        x = self.dense(x)
        x = self.softmax(x)
        return x
    
    
model = MultiClassifier()
model.build(input_shape=(None, 2))
model.summary()
```

```
Model: "multi_classifier"
_________________________________________________________________
Layer (type)                 Output Shape              Param #   
=================================================================
dense (Dense)                multiple                  30        
_________________________________________________________________
activation (Activation)      multiple                  0         
=================================================================
Total params: 30
Trainable params: 30
Non-trainable params: 0
_________________________________________________________________
```

<br><br>

## Train Model

```python
from tensorflow.keras.losses import CategoricalCrossentropy
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.metrics import Mean
from tensorflow.keras.metrics import CategoricalAccuracy


# Hyper Parameters
EPOCHS = 50
LR = 1e-2
BATCH_SIZE = 16

# Data Loader
train_ds = train_ds.batch(BATCH_SIZE)

# Loss function and Optimizer
loss_func = CategoricalCrossentropy()
optimizer = Adam(learning_rate=LR)

# Metrics
loss_metric = Mean()
acc_metric = CategoricalAccuracy()

# Training
for epoch in range(1, EPOCHS+1):
    for x, y in train_ds:
        with tf.GradientTape() as tape:
            preds = model(x)
            loss = loss_func(y, preds)

        grads = tape.gradient(loss, model.trainable_variables)
        optimizer.apply_gradients(zip(grads, model.trainable_variables))

        loss_metric(loss)
        acc_metric(y, preds)
    
    template = 'Epoch: {:>2} | Train Loss: {:.2f}, Train Acc: {:.2f}%'
    print(template.format(epoch, loss_metric.result(), acc_metric.result() * 100))
    
    loss_metric.reset_states()
    acc_metric.reset_states()
```

```
Epoch:  1 | Train Loss: 2.16, Train Acc: 18.90%
Epoch:  2 | Train Loss: 1.76, Train Acc: 31.70%
Epoch:  3 | Train Loss: 1.59, Train Acc: 31.25%
Epoch:  4 | Train Loss: 1.48, Train Acc: 33.60%
Epoch:  5 | Train Loss: 1.39, Train Acc: 36.30%
Epoch:  6 | Train Loss: 1.34, Train Acc: 38.60%
Epoch:  7 | Train Loss: 1.28, Train Acc: 43.35%
Epoch:  8 | Train Loss: 1.23, Train Acc: 44.40%
Epoch:  9 | Train Loss: 1.20, Train Acc: 46.15%
Epoch: 10 | Train Loss: 1.16, Train Acc: 47.25%
Epoch: 11 | Train Loss: 1.14, Train Acc: 47.25%
Epoch: 12 | Train Loss: 1.10, Train Acc: 48.50%
Epoch: 13 | Train Loss: 1.07, Train Acc: 50.50%
Epoch: 14 | Train Loss: 1.06, Train Acc: 51.35%
Epoch: 15 | Train Loss: 1.03, Train Acc: 52.25%
Epoch: 16 | Train Loss: 1.02, Train Acc: 52.70%
Epoch: 17 | Train Loss: 1.00, Train Acc: 54.25%
Epoch: 18 | Train Loss: 0.98, Train Acc: 54.45%
Epoch: 19 | Train Loss: 0.97, Train Acc: 55.60%
Epoch: 20 | Train Loss: 0.95, Train Acc: 57.35%
Epoch: 21 | Train Loss: 0.93, Train Acc: 57.75%
Epoch: 22 | Train Loss: 0.92, Train Acc: 59.10%
Epoch: 23 | Train Loss: 0.91, Train Acc: 59.60%
Epoch: 24 | Train Loss: 0.89, Train Acc: 60.30%
Epoch: 25 | Train Loss: 0.88, Train Acc: 63.50%
Epoch: 26 | Train Loss: 0.87, Train Acc: 63.30%
Epoch: 27 | Train Loss: 0.86, Train Acc: 64.15%
Epoch: 28 | Train Loss: 0.85, Train Acc: 65.90%
Epoch: 29 | Train Loss: 0.83, Train Acc: 65.95%
Epoch: 30 | Train Loss: 0.82, Train Acc: 68.60%
Epoch: 31 | Train Loss: 0.82, Train Acc: 67.30%
Epoch: 32 | Train Loss: 0.80, Train Acc: 69.75%
Epoch: 33 | Train Loss: 0.79, Train Acc: 69.35%
Epoch: 34 | Train Loss: 0.79, Train Acc: 70.35%
Epoch: 35 | Train Loss: 0.78, Train Acc: 71.55%
Epoch: 36 | Train Loss: 0.77, Train Acc: 71.55%
Epoch: 37 | Train Loss: 0.76, Train Acc: 72.55%
Epoch: 38 | Train Loss: 0.75, Train Acc: 73.15%
Epoch: 39 | Train Loss: 0.74, Train Acc: 73.40%
Epoch: 40 | Train Loss: 0.73, Train Acc: 73.75%
Epoch: 41 | Train Loss: 0.72, Train Acc: 74.70%
Epoch: 42 | Train Loss: 0.72, Train Acc: 74.50%
Epoch: 43 | Train Loss: 0.71, Train Acc: 75.45%
Epoch: 44 | Train Loss: 0.71, Train Acc: 75.25%
Epoch: 45 | Train Loss: 0.70, Train Acc: 75.55%
Epoch: 46 | Train Loss: 0.69, Train Acc: 75.95%
Epoch: 47 | Train Loss: 0.69, Train Acc: 76.25%
Epoch: 48 | Train Loss: 0.68, Train Acc: 77.35%
Epoch: 49 | Train Loss: 0.67, Train Acc: 77.95%
Epoch: 50 | Train Loss: 0.66, Train Acc: 78.50%
```

<br><br>

## Visualize Inference Results

```python
n_test = 100

xs, ys = np.meshgrid(np.linspace(-1.2, 1.2, n_test), np.linspace(-1.2, 1.2, n_test))
x_flatten, y_flatten = xs.flatten(), ys.flatten()

test_x = np.hstack((x_flatten.reshape(-1, 1), y_flatten.reshape(-1, 1)))  # shape: (10000, 2)
test_preds_raw = model(test_x).numpy()                      # Raw predictions(softmax output)
test_preds = np.argmax(test_preds_raw, axis=1)

color_dict = {i:cmap(i) for i in range(n_class)}
color_arr = [color_dict[pred] for pred in test_preds]

ax.scatter(x_flatten, y_flatten, c=color_arr, alpha=0.2)
fig
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-16-multiclass_preds.png?raw=true" width="600" />

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Multiclass Classification](https://www.youtube.com/watch?v=oIwHNy7GXdQ)

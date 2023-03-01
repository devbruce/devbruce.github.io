---
title: "[ML] TensorFlow MNIST Classification Example"
excerpt: 
last_modified_at: 2021-12-01

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - classification
  - mnist

---

# TensorFlow MNIST Classification Example

![tensorflow-v2.6.0](https://img.shields.io/badge/TensorFlow-2.6.0-orange) 

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [MNIST Classification](https://www.youtube.com/watch?v=x5LhoSZ7_AI)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

<br><br>

## Import Libraries

```python
import numpy as np
import matplotlib.pyplot as plt
from termcolor import colored

import tensorflow as tf
import tensorflow_datasets as tfds

from tensorflow.keras.models import Sequential, Model
from tensorflow.keras.layers import Flatten, Dense, Activation

from tensorflow.keras.losses import SparseCategoricalCrossentropy
from tensorflow.keras.optimizers import SGD, Adam

from tensorflow.keras.metrics import Mean, SparseCategoricalAccuracy


# Save some gpu memories
physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
    

# Set random seed
SEED = 7777
tf.random.set_seed(SEED)
np.random.seed(SEED)
```

<br><br>

## Get MNIST Dataset via TFDS

```python
def get_mnist_ds():
    (train_val_ds, test_ds), ds_info = tfds.load(
        name='mnist',
        shuffle_files=True,
        as_supervised=True,
        split=['train', 'test'],
        with_info=True
    )
    
    n_train_val = ds_info.splits['train'].num_examples
    train_ratio = 0.8
    n_train = int(n_train_val * train_ratio)
    n_val = n_train_val - n_train
    
    train_ds = train_val_ds.take(n_train)
    val_ds = train_val_ds.skip(n_train)
    return train_ds, val_ds, test_ds
    
    
def standardization(train_batch_size, test_batch_size):
    global train_ds, val_ds, test_ds
    
    def stnd(imgs, labels):
        imgs = tf.cast(imgs, tf.float32) / 255.
        return imgs, labels
    
    train_ds = train_ds.map(stnd).batch(train_batch_size)
    val_ds = val_ds.map(stnd).batch(test_batch_size)
    test_ds = test_ds.map(stnd).batch(test_batch_size)
```

<br><br>

## Build Model

```python
class MnistClassifier(Model):
    def __init__(self):
        super().__init__()
        self.flatten = Flatten()
        self.d1 = Dense(64, activation='relu')
        self.d2 = Dense(10, activation='softmax')
        
    def call(self, x):
        x = self.flatten(x)
        x = self.d1(x)
        x = self.d2(x)
        return x
```

<br><br>

## Metrics

```python
def load_metrics():
    global train_mean_loss_metric, train_acc_metric
    global val_mean_loss_metric, val_acc_metric
    global test_mean_loss_metric, test_acc_metric
    
    train_mean_loss_metric = Mean()
    val_mean_loss_metric = Mean()
    test_mean_loss_metric = Mean()
    
    train_acc_metric = SparseCategoricalAccuracy()
    val_acc_metric = SparseCategoricalAccuracy()
    test_acc_metric = SparseCategoricalAccuracy()
```

<br><br>

## Train, Validation, Test

```python
@tf.function
def train():
    global train_ds, model, loss_func, optimizer
    global train_mean_loss_metric, train_acc_metric
    
    for imgs, labels in train_ds:
        with tf.GradientTape() as tape:
            preds = model(imgs)
            loss = loss_func(labels, preds)
            
        grads = tape.gradient(loss, model.trainable_variables)
        optimizer.apply_gradients(zip(grads, model.trainable_variables))
        
        train_mean_loss_metric(loss)
        train_acc_metric(labels, preds)
        
        
@tf.function
def validation():
    global val_ds, model, loss_func
    global val_mean_loss_metric, val_acc_metric
    
    for imgs, labels in val_ds:
        preds = model(imgs)
        loss = loss_func(labels, preds)
        
        val_mean_loss_metric(loss)
        val_acc_metric(labels, preds)
        
        
@tf.function
def test():
    global test_ds, model, loss_func
    global test_mean_loss_metric, test_acc_metric
    
    for imgs, labels in test_ds:
        preds = model(imgs)
        loss = loss_func(labels, preds)
        
        test_mean_loss_metric(loss)
        test_acc_metric(labels, preds)
```

<br><br>

## Training process printer

```python
def train_printer():
    global epoch
    global train_mean_loss_metric, train_acc_metric
    global val_mean_loss_metric, val_acc_metric
    
    print(colored('Epoch', 'green'), epoch)
    template = 'Train Loss: {:.4f}\t Train Accuracy: {:.2f}%\n' + \
    'Validation Loss: {:.4f}\t Validation Accuracy: {:.2f}%\n'
    
    print(template.format(
        train_mean_loss_metric.result(),
        train_acc_metric.result() * 100,
        val_mean_loss_metric.result(),
        val_acc_metric.result() * 100))
          
    train_acc_metric.reset_states()
    train_mean_loss_metric.reset_states()
    val_acc_metric.reset_states()
    val_mean_loss_metric.reset_states()
```

<br><br>

## Training

```python
# Hyper Parameters
EPOCHS = 10
LR = 1e-3
TRAIN_BATCH_SIZE = 16
TEST_BATCH_SIZE = 32

# Dataset
train_ds, val_ds, test_ds = get_mnist_ds()
standardization(TRAIN_BATCH_SIZE, TEST_BATCH_SIZE)

# Model
model = MnistClassifier()

# Loss function
loss_func = SparseCategoricalCrossentropy()

# Optimizer
optimizer = SGD(learning_rate=LR)

# Metrics
load_metrics()

# Training
for epoch in range(1, EPOCHS+1):
    train()
    validation()
    train_printer()
    
test()

# Print test result
template = '\n==== Test Result =====\nTest Loss: {:.4f}\t Test Accuracy: {:.2f}%\n'
print(template.format(test_mean_loss_metric.result(), test_acc_metric.result() * 100))
test_mean_loss_metric.reset_states()
test_acc_metric.reset_states()
```

```
Epoch 1
Train Loss: 1.5341	 Train Accuracy: 59.79%
Validation Loss: 0.9643	 Validation Accuracy: 79.12%

Epoch 2
Train Loss: 0.7588	 Train Accuracy: 82.84%
Validation Loss: 0.6286	 Validation Accuracy: 85.13%

Epoch 3
Train Loss: 0.5610	 Train Accuracy: 86.09%
Validation Loss: 0.5120	 Validation Accuracy: 87.40%

Epoch 4
Train Loss: 0.4777	 Train Accuracy: 87.53%
Validation Loss: 0.4530	 Validation Accuracy: 88.27%

Epoch 5
Train Loss: 0.4308	 Train Accuracy: 88.43%
Validation Loss: 0.4168	 Validation Accuracy: 88.96%

Epoch 6
Train Loss: 0.4002	 Train Accuracy: 89.09%
Validation Loss: 0.3920	 Validation Accuracy: 89.45%

Epoch 7
Train Loss: 0.3782	 Train Accuracy: 89.52%
Validation Loss: 0.3736	 Validation Accuracy: 89.85%

Epoch 8
Train Loss: 0.3612	 Train Accuracy: 89.92%
Validation Loss: 0.3593	 Validation Accuracy: 90.16%

Epoch 9
Train Loss: 0.3476	 Train Accuracy: 90.25%
Validation Loss: 0.3476	 Validation Accuracy: 90.47%

Epoch 10
Train Loss: 0.3362	 Train Accuracy: 90.54%
Validation Loss: 0.3377	 Validation Accuracy: 90.64%


==== Test Result =====
Test Loss: 0.3192	 Test Accuracy: 91.22%
```

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [MNIST Classification](https://www.youtube.com/watch?v=x5LhoSZ7_AI)

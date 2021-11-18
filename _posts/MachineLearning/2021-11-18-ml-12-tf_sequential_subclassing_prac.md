---
title: "[ML] TF Regression Model Implementation"
excerpt: 
last_modified_at: 2021-11-18

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - sequential
  - subclassing

---

# TF Regression Model Implementation

![tensorflow-v2.7.0](https://img.shields.io/badge/TensorFlow-2.7.0-orange)

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Model Implementation](https://www.youtube.com/watch?v=94M23cNeRBA&t=1783s)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

```python
import tensorflow as tf
import matplotlib.pyplot as plt


# Save GPU memory
physcial_devices = tf.config.list_physical_devices('GPU')
for physical_device in physcial_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
    
    
tf.random.set_seed(1234)
```

<br>

## Create Temporary Dataset

```python
train_data_size = 1000
test_data_size = 300

# Target Value
target_w = 3
target_bias = 1

# Train Dataset
noises = 0.2 * tf.random.normal(shape=(train_data_size, 1), dtype=tf.float32)
x_train = tf.random.normal(shape=(train_data_size, 1), dtype=tf.float32)
y_train = target_w * x_train + target_bias + noises

# Test Dataset
noises_test = 0.2 * tf.random.normal(shape=(test_data_size, 1), dtype=tf.float32)
x_test = tf.random.normal(shape=(test_data_size, 1), dtype=tf.float32)
y_test = target_w * x_test + target_bias + noises_test
```

<br>

## Visualize Train Dataset

```python
fig, ax  = plt.subplots(figsize=(8, 8))
ax.scatter(x_train.numpy(), y_train.numpy())
ax.set_title('Train Dataset with Noise', fontdict={'fontsize': 20})
ax.tick_params(labelsize=20)
ax.grid()
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-12-train_reg_viz.png?raw=true" width="400" />

<br><br>

## Sequential and Keras API

### Model Implementation

- Case 1(with input layer)

```python
model = tf.keras.models.Sequential(
    [
        tf.keras.layers.Input(shape=(1,), name='input_layer1'),
        tf.keras.layers.Dense(units=1, activation='linear', name='dense_layer1'),
    ],
    name='seq_prac',
)
```

- Case 2(with input layer)

```python
model = tf.keras.models.Sequential(name='seq_prac')
model.add(tf.keras.layers.Input(shape=(1,), name='input_layer1'))
model.add(tf.keras.layers.Dense(units=1, activation='linear', name='dense_layer1'))
```

- Case 3(No input layer, Use build)

```python
model = tf.keras.models.Sequential(
    [
        tf.keras.layers.Dense(units=1, activation='linear', name='dense_layer1'),
    ],
    name='seq_prac',
)
model.build(input_shape=(None, 1))
```

- Case 4(No input layer, Use build)

```python
model = tf.keras.models.Sequential(name='seq_prac')
model.add(tf.keras.layers.Dense(units=1, activation='linear', name='dense_layer1'))

model.build(input_shape=(None, 1))
```

<br>

### Model Summary

```python
model.summary()
```

```
Model: "seq_prac"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 dense_layer1 (Dense)        (None, 1)                 2         
                                                                 
=================================================================
Total params: 2
Trainable params: 2
Non-trainable params: 0
_________________________________________________________________
```

<br>

### Trainable Variables

```python
import pprint


pprint.pprint(model.trainable_variables)
```
```
[<tf.Variable 'dense_layer1/kernel:0' shape=(1, 1) dtype=float32, numpy=array([[-1.7177762]], dtype=float32)>,
 <tf.Variable 'dense_layer1/bias:0' shape=(1,) dtype=float32, numpy=array([0.], dtype=float32)>]
```

학습가능한 변수(trainable_variables)로 2개(초기화된 weight 1개와 bias 1개)를 확인할 수 있다.  

```python
print(type(model.trainable_variables[0]))
print(type(model.trainable_variables[1]))
```

```
<class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
<class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
```

그리고 두 개의 weight와 bias는 업데이트 가능한 `tf.Variable` 객체임을 확인할 수 있다.

<br><br>

### Model Compile

```python
model.compile(loss='mean_squared_error', optimizer='SGD')
```

만약, Optimizer의 Hyper parameter(ex. Learning rate) 또는 loss function 등을  
구체적으로 지정하고 싶은 경우 아래와 같이 지정할 수 있다.  

```python
loss_func = tf.keras.losses.mean_squared_error
optimizer = tf.keras.optimizers.SGD(learning_rate=1e-2)

model.compile(loss=loss_func, optimizer=optimizer)
```

<br><br>

### Training with Keras API

- Model Implementation → Model Build → Model Compile → Model Fit(Training)

```python
model.fit(x_train, y_train, epochs=5)
```

model.fit의 parameter 중, verbose 값에 따라서 training progress를 다르게 보여준다.

- `verbose='auto'`(default) == `verbose=1`

```
Epoch 1/5
32/32 [==============================] - 0s 1ms/step - loss: 7.4052
Epoch 2/5
32/32 [==============================] - 0s 1ms/step - loss: 2.0795
Epoch 3/5
32/32 [==============================] - 0s 1ms/step - loss: 0.6053
Epoch 4/5
32/32 [==============================] - 0s 1ms/step - loss: 0.1996
Epoch 5/5
32/32 [==============================] - 0s 1ms/step - loss: 0.0845
```

- `verbose=2`

```
Epoch 1/5
32/32 - 0s - loss: 3.9415 - 134ms/epoch - 4ms/step
Epoch 2/5
32/32 - 0s - loss: 1.1138 - 28ms/epoch - 891us/step
Epoch 3/5
32/32 - 0s - loss: 0.3382 - 29ms/epoch - 916us/step
Epoch 4/5
32/32 - 0s - loss: 0.1245 - 30ms/epoch - 923us/step
Epoch 5/5
32/32 - 0s - loss: 0.0637 - 30ms/epoch - 922us/step
```

- `verbose=3`

```
Epoch 1/5
Epoch 2/5
Epoch 3/5
Epoch 4/5
Epoch 5/5
```

<br>

### Evaluate

```python
model.evaluate(x_test, y_test)
```

- `verbose=1`(Default)

```
10/10 [==============================] - 0s 1ms/step - loss: 0.0565
0.05654487758874893
```

- `verbose=2`

```
10/10 - 0s - loss: 0.0565 - 17ms/epoch - 2ms/step
0.05654487758874893
```

- `verbose=3`

```
0.05654487758874893
```

<br><br>

## Subclassing and GradientTape

```python
import tensorflow as tf


class LinearPredictor(tf.keras.models.Model):
    def __init__(self):
        super().__init__()
        self.dense1 = tf.keras.layers.Dense(units=1, activation='linear')
        
    def call(self, x):
        x = self.dense1(x)
        return x
        
        
model = LinearPredictor()
```

<br>

### Model Build

```python
model.build(input_shape=(None, 1))
```

<br>

### Model Summary

```python
model.summary()
```

```
Model: "linear_predictor"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 dense_1 (Dense)             multiple                  2         
                                                                 
=================================================================
Total params: 2
Trainable params: 2
Non-trainable params: 0
_________________________________________________________________
```

model build 단계에서 `input_shape`를 지정했음에도 Output Shape가 명확하게 나오지 않는다.(`multiple`)  
Subclassing 방식의 한계인 것인지, 다른 방법이 있는 것인지는 아직 파악하지 못했다.  
{: .notice--info}

<br>

### Trainable Variables

```python
model.trainable_variables
```

```
[<tf.Variable 'dense_2/kernel:0' shape=(1, 1) dtype=float32, numpy=array([[-0.7056452]], dtype=float32)>,
 <tf.Variable 'dense_2/bias:0' shape=(1,) dtype=float32, numpy=array([0.], dtype=float32)>]
```

학습가능한 변수(trainable_variables)로 2개(초기화된 weight 1개와 bias 1개)를 확인할 수 있다.

```python
print(type(model.trainable_variables[0]))
print(type(model.trainable_variables[1]))
```

```
<class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
<class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
```

그리고 두 개의 weight와 bias는 업데이트 가능한 `tf.Variable` 객체임을 확인할 수 있다.

<br>

### Training with GradientTape

Subclassing으로 구현한 모델도 위의 예제처럼 Keras API 사용가능  
(Model Implementation → Model Build → Model Compile → Model Fit(Training))  
{: .notice--info}

```python
from termcolor import colored


# Model Definition
model = LinearPredictor()

# Hyper Parameters
EPOCHS = 10
LR = 1e-2

# Loss Function
loss_obj = tf.keras.losses.MeanSquaredError()

# Optimizer
optimizer = tf.keras.optimizers.SGD(learning_rate=LR)


# Training Iteration
for epoch in range(1, EPOCHS+1):
    for x, y in zip(x_train, y_train):
        # Add batch dimension
        x = tf.expand_dims(x, axis=0)  # shape=(1,) ==> shape=(1, 1)
        
        # Forward Propagation
        with tf.GradientTape() as tape:
            pred = model(x)
            loss = loss_obj(y, pred)
            
        # Back Propagation
        grads = tape.gradient(loss, model.trainable_variables)
        
        # Update Parameters
        optimizer.apply_gradients(zip(grads, model.trainable_variables))
        
    print(colored('Epoch: ', 'green'), epoch)
    
    template = 'Train Loss: {:.4f}'
    print(template.format(loss))
```

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Model Implementation](https://www.youtube.com/watch?v=94M23cNeRBA&t=1783s)

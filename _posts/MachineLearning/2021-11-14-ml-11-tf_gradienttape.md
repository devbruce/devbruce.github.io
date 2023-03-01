---
title: "[ML] tf.GradientTape, Regression"
excerpt: 
last_modified_at: 2021-11-14

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - gradienttape
  - regression

---

# TensorFlow tf.GradientTape, Regression

![tensorflow-v2.7.0](https://img.shields.io/badge/TensorFlow-2.7.0-orange)

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Gradient Tape in Tensorflow](https://www.youtube.com/watch?v=pbCeMzN2rcc&t=716s)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

<br>

- tf.GradientTape: Forward Propagation의 연산값을 저장해주는 API  
연산값들에 대한 정보가 있어야 Back Propagation 과정의 Chain Rule을 적용할 수 있다.  

```python
import tensorflow as tf


t1 = tf.Variable([1, 2, 3], dtype=tf.float32)
t2 = tf.Variable([10, 20, 30], dtype=tf.float32)


with tf.GradientTape() as tape:
    t3 = t1 * t2
    
print('t1: ', t1.numpy())
print('t2: ', t2.numpy())
print('t3: ', t3.numpy())
```
```
t1:  [1. 2. 3.]
t2:  [10. 20. 30.]
t3:  [10. 40. 90.]
```

위의 예시코드는 Forward Propagation 연산값(`t3 = t1 * t2`)을 GradientTape로 저장한 것이다.  
tape 객체를 통해 다음을 구할 수 있다.  

- t3에 대한 t1의 미분계수(기울기, 변화량)
- t3에 대한 t2의 미분계수(기울기, 변화량)

```python
gradients = tape.gradient(t3, [t1, t2])

t1_differential_coefficient = gradients[0]
t2_differential_coefficient = gradients[1]

print('t3에 대한 t1의 미분계수: ', t1_differential_coefficient.numpy())
print('t3에 대한 t2의 미분계수: ', t2_differential_coefficient.numpy())
```
```
t3에 대한 t1의 미분계수:  [10. 20. 30.]
t3에 대한 t2의 미분계수:  [1. 2. 3.]
```

<br>

`tape.gradient`는 한번만 사용 가능하다.  
두번 이상 해당 API를 call할 경우, 다음과 같은 에러가 발생한다.  
{: .notice--danger}

```
RuntimeError: A non-persistent GradientTape can only be used to compute one set of gradients (or jacobians)
```

<br><br>

- `tf.Variable`이 아닌 `tf.constant`의 미분계수를 구하려고 할 경우, `None`이 반환된다.  
`tf.constant`는 업데이트가 필요한 Tensor가 아니기 때문에 Back Propagation을 진행하지 않기 때문이다.

```python
import tensorflow as tf


t1 = tf.constant([1, 2, 3], dtype=tf.float32)
t2 = tf.Variable([10, 20, 30], dtype=tf.float32)


with tf.GradientTape() as tape:
    t3 = t1 * t2
    
print('t1: ', t1.numpy())
print('t2: ', t2.numpy())
print('t3: ', t3.numpy())
```
```
t1:  [1. 2. 3.]
t2:  [10. 20. 30.]
t3:  [10. 40. 90.]
```

```python
gradients = tape.gradient(t3, [t1, t2])

t1_differential_coefficient = gradients[0]  # dt1
t2_differential_coefficient = gradients[1]  # dt2

print('t3에 대한 t1의 미분계수(dt1): ', t1_differential_coefficient)
print('t3에 대한 t2의 미분계수(dt2): ', t2_differential_coefficient.numpy())
```
```
t3에 대한 t1의 미분계수:  None
t3에 대한 t2의 미분계수:  [1. 2. 3.]
```

<br><br>

## tf.GradientTape Example

```python
import tensorflow as tf


t1 = tf.Variable([1, 2, 3], dtype=tf.float32)
t2 = tf.Variable([10, 20, 30], dtype=tf.float32)

with tf.GradientTape() as tape:
    t3 = t1 * t2
    t4 = t3 + t2
    
gradients = tape.gradient(t4, [t1, t2, t3])
dt1, dt2, dt3 = gradients[0], gradients[1], gradients[2]

print('t1: ', t1.numpy())
print('t2: ', t1.numpy())
print('t3: ', t1.numpy())
print('t4: ', t1.numpy())
print()
print('dt1: ', dt1.numpy())
print('dt2: ', dt2.numpy())
print('dt3: ', dt3.numpy())
```
```
t1:  [1. 2. 3.]
t2:  [1. 2. 3.]
t3:  [1. 2. 3.]
t4:  [1. 2. 3.]

dt1:  [10. 20. 30.]
dt2:  [2. 3. 4.]
dt3:  [1. 1. 1.]
```

<br><br>

## Regression with tf.GradientTape

```python
import tensorflow as tf
import matplotlib.pyplot as plt


# Save some gpu memories
physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
    
    
w_target, b_target = 5, 3
x_data = tf.random.normal(shape=(1000,), dtype=tf.float32)
y_data = w_target * x_data + b_target

# Initialize weights
w = tf.Variable(0, dtype=tf.float32)
b = tf.Variable(0, dtype=tf.float32)

# Loss function
def loss_func(target, pred):
    return (target - pred) ** 2
```

```python
# Training Hyper Parameters
EPOCHS = 2
LR = 1e-2

# Logging weights for Visualization
w_trace, b_trace = [w.numpy()], [b.numpy()]

# Training code
for _ in range(EPOCHS):
    for x, y in zip(x_data, y_data):
        with tf.GradientTape() as tape:
            pred = w*x + b
            loss = loss_func(target=y, pred=pred)
        
        gradients = tape.gradient(loss, [w, b])
        
        # Update weights
        w.assign_sub(LR * gradients[0])
        b.assign_sub(LR * gradients[1])
        
        # Logging values for visualization
        w_trace.append(w.numpy())
        b_trace.append(b.numpy())
        
# Visualization
fig, ax = plt.subplots(figsize=(20, 10))
ax.plot(w_trace, label='Weight')
ax.plot(b_trace, label='Bias')

# Visualization Parameters
ax.tick_params(labelsize=15)
ax.legend(fontsize=20)
ax.set_title(f'Weight: {w.numpy():.2f}, Bias: {b.numpy():.2f}')
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-11-reg_viz_result.png?raw=true" width="600" />

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Gradient Tape in Tensorflow](https://www.youtube.com/watch?v=pbCeMzN2rcc&t=716s)

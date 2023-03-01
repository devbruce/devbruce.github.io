---
title: "[ML] TensorFlow Variable and Constant"
excerpt: 
last_modified_at: 2021-11-12

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - Variable
  - constant

---

# TensorFlow Variable and Constant

![tensorflow-v2.7.0](https://img.shields.io/badge/TensorFlow-2.7.0-orange)

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Constant and Variable Tensors](https://www.youtube.com/watch?v=griGQ8Cu4Qs&t=107s)를 수강하고 개인적으로 정리한 내용입니다.  
예시코드는 강의내용 그대로 사용하지않고, 수정하였음을 참고하여주시기 바랍니다.

<br>

모델 학습 Flow

- DataLoader → Data → (Model → Loss → Optimizer → Model 반복)
  - Data: 학습 X → immutable(tf.constant, EagerTensor)
  - Model의 Weight, Bias: 학습 O → Mutable(tf.Variable, ResourceVariable)

```python
import tensorflow as tf
import numpy as np


# Save some gpu memories
physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
```

```python
t_var = tf.Variable([1, 2, 3])
t_const = tf.constant([1, 2, 3])

print(f't_var: {t_var}')
print('t_var(type): ', type(t_var))
print('=' * 80)
print(f't_const: {t_const}')
print('t_const(type): ', type(t_const))
```
```
t_var: <tf.Variable 'Variable:0' shape=(3,) dtype=int32, numpy=array([1, 2, 3], dtype=int32)>
t_var(type):  <class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
================================================================================
t_const: [1 2 3]
t_const(type):  <class 'tensorflow.python.framework.ops.EagerTensor'>
```

- tf.Variable의 type: `<class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>`
- tf.constant의 type: `<class 'tensorflow.python.framework.ops.EagerTensor'>`

<br><br>

- Python data type 또는 numpy의 ndarray는 tensorflow의 tensor로 변환가능하다.

```python
tensor_list_const = tf.constant([1, 2, 3])
tensor_np_const = tf.constant(np.array([1, 2, 3]))

print(f'tensor_list_const: {tensor_list_const}')
print('tensor_list_const(type): ', type(tensor_list_const))
print('=' * 80)
print(f'tensor_np_const: {tensor_np_const}')
print('tensor_np_const(type): ', type(tensor_np_const))
```
```
tensor_list_const: [1 2 3]
tensor_list_const(type):  <class 'tensorflow.python.framework.ops.EagerTensor'>
================================================================================
tensor_np_const: [1 2 3]
tensor_np_const(type):  <class 'tensorflow.python.framework.ops.EagerTensor'>
```

```python
tensor_list_var = tf.Variable([1, 2, 3])
tensor_np_var = tf.Variable(np.array([1, 2, 3]))

print(f'tensor_list_var: {tensor_list_var}')
print('tensor_list_var(type): ', type(tensor_list_var))
print('=' * 80)
print(f'tensor_np_var: {tensor_np_var}')
print('tensor_np_var(type): ', type(tensor_np_var))
```
```
tensor_list_var: <tf.Variable 'Variable:0' shape=(3,) dtype=int32, numpy=array([1, 2, 3], dtype=int32)>
tensor_list_var(type):  <class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
================================================================================
tensor_np_var: <tf.Variable 'Variable:0' shape=(3,) dtype=int64, numpy=array([1, 2, 3])>
tensor_np_var(type):  <class 'tensorflow.python.ops.resource_variable_ops.ResourceVariable'>
```

<br>

- Variable과 constant는 컨버팅 가능하다. (양방향으로 가능하다.)  

```python
tensor_const = tf.constant([1, 2, 3])
tensor_var = tf.Variable([1, 2, 3])

# Constant to Variable
cvt_const2var = tf.Variable(tensor_const)

# Variable to Constant
cvt_var2const = tf.constant(tensor_var)
cvt_var2const_2 = tf.convert_to_tensor(tensor_var)

print('cvt_const2var: ', cvt_const2var)
print('=' * 80)
print('cvt_var2const: ', cvt_var2const)
print('cvt_var2const_2: ', cvt_var2const_2)
```
```
cvt_const2var:  <tf.Variable 'Variable:0' shape=(3,) dtype=int32, numpy=array([1, 2, 3], dtype=int32)>
================================================================================
cvt_var2const:  tf.Tensor([1 2 3], shape=(3,), dtype=int32)
cvt_var2const_2:  tf.Tensor([1 2 3], shape=(3,), dtype=int32)
```

<br>

- Tensor의 사칙연산 결과는 EagerTensor(constant)가 된다.

```python
add_const_const = tensor_const + tensor_const
add_const_var = tensor_const + tensor_var
add_var_var = tensor_var + tensor_var

print('add_const_const(type): ', type(add_const_const))
print('=' * 80)
print('add_const_var(type): ', type(add_const_var))
print('add_var_var(type): ', type(add_var_var))
```

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Constant and Variable Tensors](https://www.youtube.com/watch?v=griGQ8Cu4Qs&t=107s)

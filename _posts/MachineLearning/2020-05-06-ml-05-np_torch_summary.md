---
title: "[ML] Numpy & PyTorch Summary"
excerpt: 
last_modified_at: 2020-05-06

categories:
  - MachineLearning

tags:
  - python
  - numpy
  - pytorch
  - tensor

---

![numpy-version-1.18.1](https://img.shields.io/badge/numpy-1.18.1-yellow)
![torch-version-1.4.0](https://img.shields.io/badge/torch-v1.4.0-brightgreen)

# Numpy & PyTorch Summary

```python
import numpy as np
import torch


arr = np.random.randn(1, 2, 3, 4)
tensor = torch.randn(1, 2, 3, 4)
```

<br><br>

## Shape 확인 및 변환

### Numpy

- `shape`, `ndim`, `size`, `reshape()`

**Note:** numpy 의 reshape 의 반환값은 `view()` 가 적용된다.  
즉 `copy()` 가 되는 형태가 아니기 때문에 base array 를 참조하여 메모리를 공유한다.
{: .notice--warning}

```python
print(arr.shape)
print(arr.ndim)
print(arr.size)
```
\>\>\> `(1, 2, 3, 4)`  
\>\>\> `4`  
\>\>\> `24`  

```python
new_arr = arr.reshape(-1, 3)
print(new_arr.shape)
```
\>\>\> `(8, 3)`

<br>

### PyTorch

- `size()`, `dim()`, `numel()`, `view()`, `ndim`, `shape`, `reshape()`  

```python
print(tensor.size())
print(tensor.size(0))
print(tensor.size(1))
print(tensor.size(2))
print(tensor.size(3))
print(tensor.dim())
print(tensor.numel())
```
\>\>\> `torch.Size([1, 2, 3, 4])`  
\>\>\> `1`  
\>\>\> `2`  
\>\>\> `3`  
\>\>\> `4`  
\>\>\> `4`  
\>\>\> `24`  

```python
new_tensor = tensor.view(-1, 3)
print(new_tensor.size())
```
\>\>\> `torch.Size([8, 3])`

<br><br>

## 차원 변환

### Numpy

```python
# 차원 전치
new_arr = arr.transpose()  # == arr.T
print(new_arr.shape)
```
\>\>\> `(4, 3, 2, 1)`  

```python
# 차원 순서 변환
new_arr = arr.transpose(2, 0, 1, 3)
print(new_arr.shape)
```
\>\>\> `(3, 1, 2, 4)`  

```python
# 차원 수가 1인 차원 제거
new_arr = arr.squeeze()
print(new_arr.shape)
```
\>\>\> `(2, 3, 4)`  

```python
# 차원 추가
new_arr = np.expand_dims(arr, axis=-1)
print(new_arr.shape)
```
\>\>\> `(1, 2, 3, 4, 1)`  

```python
# 차원 추가 2
new_arr = arr[:, :, :, :, np.newaxis]
print(new_arr.shape)
```
\>\>\> `(1, 2, 3, 4, 1)`  

<br>

### PyTorch

```python
# 차원 순서 변환
new_tensor = tensor.permute(2, 0, 1, 3)
print(new_tensor.size())
```
\>\>\> `torch.Size([3, 1, 2, 4])`

```python
# 차원 순서 변환 (선택 2개)
new_tensor = tensor.transpose(dim0=1, dim1=2)  # numpy 와 다르게 2개의 차원만 변경가능
print(new_tensor.size())
```
\>\>\> `torch.Size([1, 3, 2, 4])`

```python
# 차원 수가 1인 차원 제거
new_tensor = tensor.squeeze()
print(new_tensor.size())
```
\>\>\> `torch.Size([2, 3, 4])`

```python
# 차원 추가
new_tensor = tensor.unsqueeze(dim=-1)
print(new_tensor.size())
```
\>\>\> `torch.Size([1, 2, 3, 4, 1])`  

```python
# tensor 가 2차원일 경우 t() 를 통해 전치가능
tensor = torch.randn(3, 5)
print(tensor.size())

new_tensor = tensor.t()
print(new_tensor.size())
```
\>\>\> `torch.Size([3, 5])`  
\>\>\> `torch.Size([5, 3])`  

<br><br>

## 데이터 타입 확인 및 변환

### Numpy

- `astype()`, `dtype`

```python
new_arr = arr.astype(np.int32)
print(new_arr.dtype)
```
\>\>\> `int32`

<br>

### PyTorch

- `type()`, `dtype`, `short()`, `int()`, `long()`,  `half()`, `float()`, `double()`, `bool()`

```python
new_tensor = tensor.type(dtype=torch.int32)
print(new_tensor.type())
print(new_tensor.dtype)
```
\>\>\> `torch.IntTensor`  
\>\>\> `torch.int32`

```python
new_tensor1 = tensor.short()
new_tensor2 = tensor.int()
new_tensor3 = tensor.long()
new_tensor4 = tensor.half()
new_tensor5 = tensor.float()
new_tensor6 = tensor.double()
new_tensor7 = tensor.bool()
print(new_tensor1.type())
print(new_tensor1.dtype)
print(new_tensor2.type())
print(new_tensor2.dtype)
print(new_tensor3.type())
print(new_tensor3.dtype)
print(new_tensor4.type())
print(new_tensor4.dtype)
print(new_tensor5.type())
print(new_tensor5.dtype)
print(new_tensor6.type())
print(new_tensor6.dtype)
print(new_tensor7.type())
print(new_tensor7.dtype)
```
\>\>\> `torch.ShortTensor`  
\>\>\> `torch.int16`  
\>\>\> `torch.IntTensor`  
\>\>\> `torch.int32`  
\>\>\> `torch.LongTensor`  
\>\>\> `torch.int64`  
\>\>\> `torch.HalfTensor`  
\>\>\> `torch.float16`  
\>\>\> `torch.FloatTensor`  
\>\>\> `torch.float32`  
\>\>\> `torch.DoubleTensor`  
\>\>\> `torch.float64`  
\>\>\> `torch.BoolTensor`  
\>\>\> `torch.bool`  

<br><br>

## PyTorch 유사 메서드 비교

- `torch.Tensor` == `torch.FloatTensor`  

<br>

- `torch.tensor` vs `torch.as_tensor`  
`as_tensor`: copy 를 피해 참조 (data type 이 호환되고, device 가 동일한 경우).  
즉 `numpy.ndarray` 인 경우 copy 한다.  
이런 경우를 방지하기 위해 `.from_numpy()` 메서드를 사용한다.  
`numpy.ndarray` 의 `dtype` 을 그대로 가져오며 copy 를 피할 수 있다.  

<br>

- `torch.chunk` vs `torch.split`  
  - `torch.chunk`: view of the input tensor
  - `torch.split`: view of the original tensor

<br>

- `torch.nn.relu` vs `torch.nn.functional.relu` \-\-> Just code style  
  - `torch.nn.relu`: modules for the activations  
  - `torch.nn.functional.relu`: functional calls  

<br>
  
- `torch.nn.ConvTranspose2d` vs `torch.nn.Upsample` \-\-> Have Learing Parameters or not  
  - `torch.nn.ConvTranspose2d`: Have Learning Parameters
  - `torch.nn.Upsample`: No Learning Parameters

<br>
  
- `view()` vs `reshape()` ([Documentation Link](https://pytorch.org/docs/master/tensors.html?highlight=view#torch.Tensor.view))
  - `tensor.contiguous().view(${some shape})` == `tensor.reshape`  
  - `reshape()`: tensor 가 contiguous 하지 못하면 copy 를 해서라도 동작  

<br><br>

## Numpy 유사 메서드 비교

- `np.array` vs `np.asarray`  
  - `np.array`: copy array
  - `np.asarray`: Reference data if data is compatible with ndarray

- `arr.flatten()` vs `arr.ravel()`  
  - `arr.flatten()`: Return copy of original array
  - `arr.ravel()`: Return only reference/view of original array

<br><br>

## clip (clamp)

### Numpy

```python
new_arr = np.clip(arr, 0, 10)
# --> new_arr = np.clip(a=arr, a_min=0, a_max=10, out=None)
```

### PyTorch

```python
new_tensor = torch.clamp(tensor, 0, 10)
# --> new_tensor = torch.clamp(input=tensor, min=0, max=10, out=None)
```

<br><br>

## where (get indices on specific conditions)

**`np.where`, `torch.where` 사용법 동일**  

조건에 해당하는 값의 **Index** 를 반환한다.  

- ndim 만큼의 길이를 가지는 tuple 반환

- tuple 의 첫번째 요소인 array 는 첫번째 차원의 Index
- tuple 의 두번째 요소인 array 는 두번째 차원의 Index
- tuple 의 세번째 요소인 array 는 세번째 차원의 Index  
- . . .

다차원의 경우, 해당값의 정확한 좌표를 구하기 위해서는  
`zip()` 함수를 활용할 수 있다.  

<br>

### Example 1d array

```python
import numpy as np


arr_1d = np.random.rand(10)
print(arr_1d)
```

```
# Out
[0.49931615 0.72081879 0.33509536 0.13864185 0.48212933 0.34469938
 0.29396133 0.19329217 0.71502539 0.6183096 ]
```

```python
result = np.where(arr_1d < 0.5)
print(type(result))
print(len(result))
print(result[0])  # 조건에 해당하는 값의 index
```
\>\>\> `<class 'tuple'>`  
\>\>\> `1`  
\>\>\> `[0 2 3 4 5 6 7]`  

<br>

### Example 3d array

```python
import numpy as np

arr_3d = np.random.rand(1, 2, 3)
print(arr_3d)
```
```
# Out
[[[0.65868097 0.70970589 0.9627755 ]
  [0.16858234 0.08135782 0.29145379]]]
```

```python
result = np.where(arr_3d < 0.5)
print(type(result))
print(len(result))
```
\>\>\> `<class 'tuple'>`  
\>\>\> `3`  

```python
print_form = 'result[0]: {} | type(result[0]): {} | result[0].dtype: {}'
print(print_form.format(result[0], type(result[0]), result[0].dtype))
print(print_form.format(result[1], type(result[1]), result[1].dtype))
print(print_form.format(result[2], type(result[2]), result[2].dtype))
```

```
# Out
result[0]: [0 0 0] | type(result[0]): <class 'numpy.ndarray'> | result[0].dtype: int64
result[0]: [1 1 1] | type(result[0]): <class 'numpy.ndarray'> | result[0].dtype: int64
result[0]: [0 1 2] | type(result[0]): <class 'numpy.ndarray'> | result[0].dtype: int64
```

<br>

- 조건을 만족하는 값의 좌표 구하기

```python
pts = list(zip(*map(lambda arr : list(arr), result)))
print(pts)
```
\>\>\> `[(0, 1, 0), (0, 1, 1), (0, 1, 2)]`

<br><br>

## Pytorch Method

### `index_select`

- `torch.index_select(input, dim, index, out=None)`

```python
x = torch.rand(5, 3)
print(x)
```
```
# Out
tensor([[0.4063, 0.2598, 0.2011],
        [0.2355, 0.2475, 0.2283],
        [0.1244, 0.3219, 0.2943],
        [0.0240, 0.4658, 0.9061],
        [0.6218, 0.1086, 0.2883]])
```

<br>

#### Example 1

```python
a = torch.index_select(x, 0, torch.LongTensor([1, 3]))
print(a)
```

```
# Out
tensor([[0.2355, 0.2475, 0.2283],
        [0.0240, 0.4658, 0.9061]])
```

<br>

#### Example 2

```python
b = torch.index_select(x, 1, torch.LongTensor([0, 2]))
print(b)
```

```
# Out
tensor([[0.4063, 0.2011],
        [0.2355, 0.2283],
        [0.1244, 0.2943],
        [0.0240, 0.9061],
        [0.6218, 0.2883]])
```

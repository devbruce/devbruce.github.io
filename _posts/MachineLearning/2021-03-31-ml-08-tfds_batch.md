---
title: "[ML] Handling TFDS batch"
excerpt: 
last_modified_at: 2021-03-31

categories:
  - MachineLearning

tags:
  - python
  - tfds
  - tensorflow
  - pascalvoc

---

# Handling TFDS batch

![tensorflow-v2.4.1](https://img.shields.io/badge/TensorFlow-2.4.1-orange)
![tfds-v4.2.0](https://img.shields.io/badge/TFDS-4.2.0-magenta)

<br>

## TFDS 를 통해 한개의 데이터 반환받기

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

import tensorflow as tf
import tensorflow_datasets as tfds


(train,), ds_info = tfds.load(name='voc/2007', split=['train'], with_info=True)

data = next(iter(train))
img = data['image']
plt.imshow(img)
```

<img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-08-viz.jpg?raw=true" width="200" />

```python
label = data['objects']['bbox']


print(label)
>>> tf.Tensor(
[[0.14375    0.0437018  0.97083336 0.7763496 ]
 [0.14583333 0.24164525 0.57916665 0.6066838 ]
 [0.6        0.5244216  0.8541667  0.76606685]
 [0.56041664 0.5012854  0.7395833  0.6863753 ]], shape=(4, 4), dtype=float32)
```

<br>

### Visualization with GT

```python
img_with_gt = img.numpy().copy()
img_height, img_width, _ = img_with_gt.shape
for l in label:
    y_min, x_min, y_max, x_max = l.numpy().tolist()
    y_min, x_min = round(y_min * img_height), round(x_min * img_width)
    y_max, x_max = round(y_max * img_height), round(x_max * img_width)
    cv2.rectangle(img_with_gt, (x_min, y_min), (x_max, y_max), (0, 255., 0), 2)
    
plt.imshow(img_with_gt)
```

<img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-08-viz_with_gt.jpg?raw=true" width="200" />

<br><br>

## batch method

```python
train_batch = train.batch(10)
```

여기까지는 batch size 설정이 문제없어 보인다.  
하지만 아래의 코드와 같이 데이터 반환을 시도하면 Error 를 반환한다.

```python
batch_data = next(iter(train_batch))

>>> InvalidArgumentError: Cannot batch tensors with different shapes in component 0. First element had shape [480,389,3] and element 1 had shape [375,500,3].
```

이미지의 사이즈가 동일하지 않기 때문에 batch 형태로 데이터가 반환되지 않는다.  

**이를 해결하기 위해 `padded_batch` 메서드를 활용한다.**
{: .notice--info}

<br><br>

## padded\_batch method

```python
(train,) = tfds.load(name='voc/2007', split=['train'], with_info=False)
train_padded_batch = train.padded_batch(10)

padded_batch_data = next(iter(train_padded_batch))
>>> <tf.Tensor: shape=(10, 500, 500, 3), dtype=uint8, numpy=
array([[[[101, 102,  32],
         [131, 132,  64],
         [106, 108,  43],
         ...,
         [  0,   0,   0],
         [  0,   0,   0],
         [  0,   0,   0]],

        [[117, 120,  49],
         [124, 127,  58],
         [131, 133,  68],
         ...,
```

10 개의 이미지가 동일한 shape 인 500 x 500 x 3 으로 반환된다.  
한 개의 이미지를 시각화해보면 기존 size에서 zero padding 이 추가되어 resize 된 것을 확인 할 수 있다.  

**Notice**  
Batch 단위로 반환되는 이미지의 shape 는 각각의 Batch 마다 달라진다.  
반환되는 shape 는 각 Batch 의 Maximum Height, Maximum Width, Maximum Channel 이다.
{: .notice--warning}

```python
img = padded_batch_data['image'][0]
plt.imshow(img)
```

<img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-08-viz_padded.jpg?raw=true" width="200" />

<br>

### Case 1. Image & Box Coordinates

**유의사항**

- padding 이 추가되어 이미지 사이즈가 변경되더라도, 상대좌표값이 이에 맞게 변경되는 것이 아니다.  
- 박스 좌표의 갯수도 zero padding 이 추가되어 변경된다.

```python
label = padded_batch_data['objects']['bbox'][0]


print(label)
>>> tf.Tensor(
[[0.14375    0.0437018  0.97083336 0.7763496 ]
 [0.14583333 0.24164525 0.57916665 0.6066838 ]
 [0.6        0.5244216  0.8541667  0.76606685]
 [0.56041664 0.5012854  0.7395833  0.6863753 ]
 [0.         0.         0.         0.        ]], shape=(5, 4), dtype=float32)
```

<br>

#### Visualization with GT

```python
img_with_gt = img.numpy().copy()
img_height, img_width, _ = img_with_gt.shape
for l in label:
    y_min, x_min, y_max, x_max = l.numpy().tolist()
    y_min, x_min = round(y_min * img_height), round(x_min * img_width)
    y_max, x_max = round(y_max * img_height), round(x_max * img_width)
    cv2.rectangle(img_with_gt, (x_min, y_min), (x_max, y_max), (255, 0., 0), 2)
    
plt.imshow(img_with_gt)
```

<img src="https://github.com/DevBruce/DevBruce.github.io/blob/master/_posts/MachineLearning/images/ml-08-viz_padded_with_gt.jpg?raw=true" width="200" />  

**Notice**  
전처리 없이 사용할 경우 잘못된 좌표가 되는 것을 확인할 수 있다.
{: .notice--danger}

<br>

### Case 2. Class Index

Class Index 데이터 또한 zero padding 이 추가된다.  
여기서 주의해야 할 점은, Class Index 가 0 인 class 의 경우 zero padding 과 동일한 값이어서 구분이 쉽지않다.  
(VOC Dataset 의 경우에 'aeroplane' 의 class index 가 0 이다.)  

```python
import tensorflow_datasets as tfds


(train,), ds_info = tfds.load(name='voc/2007', split=['train'], with_info=True)
train = train.padded_batch(16)
data = next(iter(train))
imgs, bboxes, class_indices = data['image'], data['objects']['bbox'], data['objects']['label']


print(class_indices)
>>> tf.Tensor(
[[12 14 12 14  0  0  0  0  0]
 [ 6  6  0  0  0  0  0  0  0]
 [ 2  0  0  0  0  0  0  0  0]
 [16  0  0  0  0  0  0  0  0]
 [18  0  0  0  0  0  0  0  0]
 [14 14  0  0  0  0  0  0  0]
 [ 1 17 14  0  0  0  0  0  0]
 [16  0  0  0  0  0  0  0  0]
 [ 8  8  8  8 10  0  0  0  0]
 [ 0  0  0  0  0  0  0  0  0]
 [ 4  8  8 10  8 15  4  0  0]
 [10 14 14 14 14  8  8  4  4]
 [ 6  0  0  0  0  0  0  0  0]
 [17  0  0  0  0  0  0  0  0]
 [14 14 14 14 15 15 15  0  0]
 [17 14  0  0  0  0  0  0  0]], shape=(16, 9), dtype=int64)
```
`class_indices` 의 10번째 행의 값이 모두 0 인 것을 알 수 있는데,  
aeroplane 클래스 + zero padding 이 합쳐진 것이다.  

aeroplane 이 몇개인지 알기 위해서는 box 좌표값을 통해서 전처리가 필요할 것으로 보인다.  

<br><br>

## Conclusion

`padded_batch` 를 사용할 경우, zero padding 에 의해 변경된 데이터에 대해서 전처리가 필요하다.  

- box 좌표의 경우 원본 이미지 사이즈에 대한 relative 값이기 때문에  
padding 에 의해 사이즈가 변경된 것을 고려해서 전처리

- Class Index 가 0 인 클래스에 대한 전처리


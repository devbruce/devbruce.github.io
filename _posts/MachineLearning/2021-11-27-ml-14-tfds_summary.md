---
title: "[ML] TensorFlow Datasets Summary"
excerpt: 
last_modified_at: 2021-11-27

categories:
  - MachineLearning

tags:
  - python
  - tf
  - tensorflow
  - tfds

---

# TensorFlow Datasets(TFDS)

![tensorflow-v2.7.0](https://img.shields.io/badge/TensorFlow-2.6.0-orange) 
![tfds-v4.4.0](https://img.shields.io/badge/tfds-4.4.0-red)

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Dataset Load1](https://www.youtube.com/watch?v=JBphrtdjclo), [Dataset Load2](https://www.youtube.com/watch?v=4V8P24-rQgM)를 수강하고  
[TensorFlow Datasets Guide](https://www.tensorflow.org/datasets/overview)를 참고하여 개인적으로 정리한 내용입니다.  
예시코드 및 정리문구는 강의내용 그대로 사용하지않고, 개인적으로 수정한 부분이 있음을 참고하여주시기 바랍니다.  

<br><br>

## Load Datasets via tf.keras.datasets

```python
import pprint
import tensorflow as tf


physical_devices = tf.config.list_physical_devices('GPU')
for physical_device in physical_devices:
    tf.config.experimental.set_memory_growth(device=physical_device, enable=True)
    
    
pprint.pprint(dir(tf.keras.datasets))
```

```
['__builtins__',
 '__cached__',
 '__doc__',
 '__file__',
 '__loader__',
 '__name__',
 '__package__',
 '__path__',
 '__spec__',
 '_sys',
 'boston_housing',
 'cifar10',
 'cifar100',
 'fashion_mnist',
 'imdb',
 'mnist',
 'reuters']
```

<br>

```python
from tensorflow.keras.datasets import mnist
from tensorflow.keras.datasets import fashion_mnist


(train_imgs, train_labels), (test_imgs, test_labels) = mnist.load_data()
print('* ====== MNIST ======')
print(f'train_imgs.shape: {train_imgs.shape}')
print(f'train_labels.shape: {train_labels.shape}')
print()
print(f'test_imgs.shape: {test_imgs.shape}')
print(f'test_labels.shape: {test_labels.shape}')

(train_imgs, train_labels), (test_imgs, test_labels) = fashion_mnist.load_data()
print('\n\n* ====== Fashion MNIST ======')
print(f'train_imgs.shape: {train_imgs.shape}')
print(f'train_labels.shape: {train_labels.shape}')
print()
print(f'test_imgs.shape: {test_imgs.shape}')
print(f'test_labels.shape: {test_labels.shape}')
```

```
* ====== MNIST ======
train_imgs.shape: (60000, 28, 28)
train_labels.shape: (60000,)

test_imgs.shape: (10000, 28, 28)
test_labels.shape: (10000,)


* ====== Fashion MNIST ======
train_imgs.shape: (60000, 28, 28)
train_labels.shape: (60000,)

test_imgs.shape: (10000, 28, 28)
test_labels.shape: (10000,)
```

<br><br>

## Install TFDS

- The stable version, released every few months.

```bash
pip install tensorflow-datasets
```

- Released every day, contains the last versions of the datasets.

```bash
pip install tfds-nightly
```

<br><br>

## Find available datasets

```python
import tensorflow_datasets as tfds


tfds.list_builders()
```

`tf.keras.datasets`보다 더 많은 종류의 데이터셋을 확인할 수 있다.  
{: .notice--info}

<br><br>

## Dataset Build

### 1. tfds.load

```python
import tensorflow_datasets as tfds


dataset, ds_info = tfds.load(
    name='mnist',
    shuffle_files=False, # True일 경우 train, validation, test 각각 전부 shuffle (Default: False)
    with_info=True,      # tfds.core.DatasetInfo 객체 반환 (Default: False)
)

n_train = ds_info.splits['train'].num_examples
n_test = ds_info.splits['test'].num_examples

train_ds = dataset['train'].shuffle(n_train).batch(8).take(1)
test_ds = dataset['test']

for data_item in train_ds:
    imgs, labels = data_item['image'], data_item['label']
    print('imgs.shape: ', imgs.shape)
    print('labels.shape: ', labels.shape)
```

```
imgs.shape:  (8, 28, 28, 1)
labels.shape:  (8,)
```

<br>

- `as_supervised=True` 활용

```python
import tensorflow_datasets as tfds


dataset, ds_info = tfds.load(
    name='mnist',
    shuffle_files=False, # True일 경우 train, validation, test 각각 전부 Shuffle (Default: False)
    as_supervised=True,  # Data Item이 dict가 아닌 (imgs, labels) 튜플 형태로 반환
    with_info=True,      # tfds.core.DatasetInfo 객체 반환 (Default: False)
)

n_train = ds_info.splits['train'].num_examples
n_test = ds_info.splits['test'].num_examples

train_ds = dataset['train'].shuffle(n_train).batch(8).take(1)
test_ds = dataset['test']

for imgs, labels in train_ds:
    print('imgs.shape: ', imgs.shape)
    print('labels.shape: ', labels.shape)
```

```
imgs.shape:  (8, 28, 28, 1)
labels.shape:  (8,)
```

<br>

- `split` 활용

```python
import tensorflow_datasets as tfds


(train_ds, test_ds), ds_info = tfds.load(
    name='mnist',
    split=['train', 'test'],
    shuffle_files=False, # True일 경우 train, validation, test 각각 전부 Shuffle (Default: False)
    as_supervised=True,  # Data Item이 dict가 아닌 (imgs, labels) 튜플 형태로 반환
    with_info=True,      # tfds.core.DatasetInfo 객체 반환 (Default: False)
)

n_train = ds_info.splits['train'].num_examples
train_ds = train_ds.shuffle(n_train).batch(8).take(1)

for imgs, labels in train_ds:
    print('imgs.shape: ', imgs.shape)
    print('labels.shape: ', labels.shape)
```

```
imgs.shape:  (8, 28, 28, 1)
labels.shape:  (8,)
```

<br>

- numpy 객체로 반환(`tfds.as_numpy(ds)`)

```python
import tensorflow_datasets as tfds


(train_ds, test_ds), ds_info = tfds.load(
    name='mnist',
    split=['train', 'test'],
    shuffle_files=False, # True일 경우 train, validation, test 각각 전부 Shuffle (Default: False)
    as_supervised=True,  # Data Item이 dict가 아닌 (imgs, labels) 튜플 형태로 반환
    with_info=True,      # tfds.core.DatasetInfo 객체 반환 (Default: False)
)

n_train = ds_info.splits['train'].num_examples
train_ds = train_ds.shuffle(n_train).batch(8).take(1)

for imgs, labels in tfds.as_numpy(train_ds):
    print('imgs type: ', type(imgs))
    print('labels type: ', type(labels))
```

```
imgs type:  <class 'numpy.ndarray'>
labels type:  <class 'numpy.ndarray'>
```

<br><br>

### 2. tfds.builder

`tfds.load` is a thin wrapper around `tfds.core.DatasetBuilder`.  
You can get the same output using the `tfds.core.DatasetBuilder` API

```python
import tensorflow_datasets as tfds


builder = tfds.builder('fashion_mnist')

# 1. Create the tfrecord files (no-op if already exists)
builder.download_and_prepare()

# 2. Load the `tf.data.Dataset`
train_ds, test_ds = builder.as_dataset(split=['train', 'test'], shuffle_files=True)
```

`tfds.load`의  `with_info`와 같은 param이 없는 차이점이 있다.  
{: .notice--info}

<br>

### 3. CLI

```bash
tfds build ${DatasetName}
```

<br><br>

## Visualization

### tfds.as_dataframe

```python
import tensorflow_datasets as tfds


dataset, ds_info = tfds.load('mnist', split='train', with_info=True)
tfds.as_dataframe(dataset.take(6), ds_info)
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-14-as_dataframe.png?raw=true" width="200" />

<br>

### tfds.show_examples

```python
import tensorflow_datasets as tfds


dataset, ds_info = tfds.load('mnist', split='train', with_info=True)
fig = tfds.show_examples(dataset, ds_info)
```

<img src="https://github.com/devbruce/devbruce.github.io/blob/master/_posts/MachineLearning/images/ml-14-show_examples.png?raw=true" width="400" />

<br><br>

## Reference

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Dataset Load1](https://www.youtube.com/watch?v=JBphrtdjclo)

- [인프런 - Tensorflow 사용메뉴얼](https://www.inflearn.com/course/%ED%85%90%EC%84%9C%ED%94%8C%EB%A1%9C-%EB%94%A5%EB%9F%AC%EB%8B%9D-%EA%B5%AC%ED%98%84) - [Dataset Load2](https://www.youtube.com/watch?v=4V8P24-rQgM)

- [TensorFlow Datasets Guide](https://www.tensorflow.org/datasets/overview)

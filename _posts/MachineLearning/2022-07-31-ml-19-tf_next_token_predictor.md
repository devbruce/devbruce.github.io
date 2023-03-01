---
title: "[ML] Next Token Predictor Implementation with TF2"
excerpt: 
last_modified_at: 2022-07-31

categories:
  - MachineLearning

tags:
  - machine learning
  - deep learning
  - rnn
  - tensorflow
  - tokenizer
  - many-to-one

---

# Next Token Predictor Model Implementation

![tf2-2.9.1](https://img.shields.io/badge/TensorFlow-2.9.1-orange)

## Intro

이 포스트는 [\[딥러닝을 이용한 자연어 처리 입문\] 08-6: RNN을 이용한 텍스트 생성(Text Generation using RNN)](https://wikidocs.net/45101)을 참고하여 내용을 변형 및 추가하여 정리한 글입니다. 문제 소지가 있을 경우 언제든지 피드백 부탁드리겠습니다.
{: .notice--danger}

해당 포스트는 RNN(many-to-one)구조를 활용하여 다음에 나올 token을 예측하는 모델을 간단하게 구현해볼 것입니다. 사용한 프레임워크는 **TensorFlow2**이며. 다양한 커스터마이징을 할 수 있도록 **모델 구현 방식은 Subclassing**, 학습 과정 과정 또한 Keras API가 아닌, **`tf.GradientTape()`**를 활용하였습니다.

<br>

## Preparing Dataset

```python
sentence_1 = '푸른 바다 위에서 배가 항해하는 것이 보입니다.'
sentence_2 = '맛있어 보이는 배가 나무에 매달려 있습니다.'
sentence_3 = '저기에 아주 큰 배가 오고 있습니다.'
sentences = [sentence_1, sentence_2, sentence_3]
```

데이터셋은 위의 3개 문장으로 구성하였습니다. 당연히 위의 문장 그대로를 학습데이터로 사용할 수는 없습니다. 모델이 이해할 수 있게 해당 문장들을 전처리(preprocessing)하여 인코딩을 해주어야 합니다. 이를 위해 TensorFlow의 내장 패키지를 활용해보겠습니다.

<br>

## Tokenizing

```python
import pprint
import tensorflow as tf


# Set Seed(동일한 결과를 얻기 위한 seed 설정)
SEED = 7777
tf.random.set_seed(SEED)

# 1. Define tokenizer
tokenizer = tf.keras.preprocessing.text.Tokenizer()

# 2. Token to Integer
tokenizer.fit_on_texts(sentences)
```

가장 먼저, 문장을 tokenizing을 위한 tokenizer를 정의합니다. `tf.keras.preprocessing.text.Tokenizer()`에는 다양한 parameter를 통해 tokenizing 방식을 정의할 수 있습니다.(참고 Link: [Tokenizer document](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing/text/Tokenizer))

정의한 tokenizer에 준비한 문장들을 `fit`(학습)합니다.  
tokenizer에 Integer encoding된 token 결과를 확인해보면 다음과 같습니다.

```python
pprint.pprint(tokenizer.word_index, indent=4, sort_dicts=False)
```
```
{   '배가': 1,
    '있습니다': 2,
    '푸른': 3,
    '바다': 4,
    '위에서': 5,
    '항해하는': 6,
    '것이': 7,
    '보입니다': 8,
    '맛있어': 9,
    '보이는': 10,
    '나무에': 11,
    '매달려': 12,
    '저기에': 13,
    '아주': 14,
    '큰': 15,
    '오고': 16}
```
준비한 문장의 각 token이 정수로 mapping된 결과를 확인할 수 있습니다.  

이제 문장을 학습을 위한 형태로 변환해보겠습니다.  

```python
def create_train_samples(sentence:str) -> list:
    # string sentence를 Integer encoding(List[int])으로 변환
    integer_encoded = tokenizer.texts_to_sequences([sentence])[0]
    
    train_samples = []
    for i in range(1, len(integer_encoded)):
        sliced_sample = integer_encoded[:i+1]
        train_samples.append(sliced_sample)
    return train_samples


train_samples = []
for sentence in sentences:
    train_samples.extend(create_train_samples(sentence))
    
pprint.pprint(train_samples)
```
```
[[3, 4],
 [3, 4, 5],
 [3, 4, 5, 1],
 [3, 4, 5, 1, 6],
 [3, 4, 5, 1, 6, 7],
 [3, 4, 5, 1, 6, 7, 8],
 [9, 10],
 [9, 10, 1],
 [9, 10, 1, 11],
 [9, 10, 1, 11, 12],
 [9, 10, 1, 11, 12, 2],
 [13, 14],
 [13, 14, 15],
 [13, 14, 15, 1],
 [13, 14, 15, 1, 16],
 [13, 14, 15, 1, 16, 2]]
```
변환된 리스트들이 무슨 의미인지 혼란스러울 수 있습니다. 위에서 정수로 maaping된 token들을 기억하시나요?  
위에서 maaping된 값을 통해 위의 결과를 디코딩 해보면 다음과 같습니다.

```
[['푸른', '바다'],
 ['푸른', '바다', '위에서'],
 ['푸른', '바다', '위에서', '배가'],
 ['푸른', '바다', '위에서', '배가', '항해하는'],
 ['푸른', '바다', '위에서', '배가', '항해하는', '것이'],
 ['푸른', '바다', '위에서', '배가', '항해하는', '것이', '보입니다'],
 ['맛있어', '보이는'],
 ['맛있어', '보이는', '배가'],
 ['맛있어', '보이는', '배가', '나무에'],
 ['맛있어', '보이는', '배가', '나무에', '매달려'],
 ['맛있어', '보이는', '배가', '나무에', '매달려', '있습니다'],
 ['저기에', '아주'],
 ['저기에', '아주', '큰'],
 ['저기에', '아주', '큰', '배가'],
 ['저기에', '아주', '큰', '배가', '오고'],
 ['저기에', '아주', '큰', '배가', '오고', '있습니다']]
```

이렇게 준비한 이유는 학습을 위해서 입니다.  
첫 번째 데이터 `['푸른', '바다']` 는 `['푸른']`을 input으로, label로 `['바다']`를 주어 학습시킬 것 입니다.
두 번째 데이터 `['푸른', '바다', '위에서']`는 `['푸른', '바다']`를 input으로, label로 `['위에서']` 주어 학습시킵니다.
나머지 데이터도 동일하게 마지막 token은 label로, 나머지 데이터는 input으로 사용합니다.  

그러기 위해서 input과  label을 분리할 필요가 있습니다. 아래에서 해당 과정에 대해서 설명하겠습니다.  

<br>

## Split Train data and Label

### Get timesteps

일반적으로 RNN Layer의 input shape는 `(batch_size, timesteps, input_dim)`입니다. 현재 데이터에서 timesteps는 학습에 사용될 문장 중 가장 긴 값이 됩니다. 그렇기에 다음과 같이 timesteps 값을 구할 수 있습니다.  

이 때, 현재 데이터 상태는 label(list의 마지막 element)을 포함하고 있으므로 -1을 해줍니다.  
{: .notice--info}

```python
timesteps = len(max(train_samples, key=len)) - 1
```

### Add padding to data

일반적으로 모델은 input으로 동일한 shape를 필요로 합니다. 따라서 padding을 추가하는 작업을 해줍니다.  

지금은 label로 사용할 마지막 element가 포함되어 있으므로 `maxlen`에 timesteps+1 을 전달합니다.  
{: .notice--info}

```python
train_samples_padded = tf.keras.preprocessing.sequence.pad_sequences(train_samples, maxlen=timesteps+1)

print(train_samples_padded)
```
```
[[ 0  0  0  0  0  3  4]
 [ 0  0  0  0  3  4  5]
 [ 0  0  0  3  4  5  1]
 [ 0  0  3  4  5  1  6]
 [ 0  3  4  5  1  6  7]
 [ 3  4  5  1  6  7  8]
 [ 0  0  0  0  0  9 10]
 [ 0  0  0  0  9 10  1]
 [ 0  0  0  9 10  1 11]
 [ 0  0  9 10  1 11 12]
 [ 0  9 10  1 11 12  2]
 [ 0  0  0  0  0 13 14]
 [ 0  0  0  0 13 14 15]
 [ 0  0  0 13 14 15  1]
 [ 0  0 13 14 15  1 16]
 [ 0 13 14 15  1 16  2]]
```

### Split

```python
train_x, train_y = train_samples_padded[:, :-1], train_samples_padded[:, -1]
```
위의 코드를 통해 마지막 element를 label로 split을 진행합니다. 하지만 여기서 끝이 아닙니다. 분리된 label은 단순한 integer입니다. 다음 token 예측을 위해서는 학습한 token 중 어떤 token이 가장 적절할지 classification 문제로 이어집니다. 즉, 모든 단어 중 softmax를 통과한 값이 가장 큰 단어가 next token임을 예측하는 프로세스이기 때문에 label one-hot encoding이 필요합니다.

### Label One-hot Encoding

One-hot encoding을 위해선 현재 사용하는 데이터에서의 token의 총 갯수가 필요합니다. 총 token의 갯수는 tokenzier 객체를 통해 구할 수 있습니다. 
One-hot encoding이 된 결과를 확인해보면 다음과 같습니다.  

아래의 코드에서 `n_tokens `에 +1을 해준 이유는, index가 0인 padding token을 고려한 것입니다.  
{: .notice--info}

```python
n_tokens = len(tokenizer.word_index) + 1
train_y = tf.keras.utils.to_categorical(train_y, num_classes=n_tokens)

print(train_y)
```

```
[[0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0. 0.]
 [0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0. 0. 0.]
 [0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1. 0.]
 [0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 1.]
 [0. 0. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.]]
```

이제 학습을 위한 `train_x`, `train_y` 준비가 완료되었습니다.

<br>

## Modeling

이제 모델링을 할 차례입니다.

- 각 token 임베딩을 위한 embedding layer를 추가합니다.  
(이를 통해 각 token 관계를 학습할 수 있습니다.)

- 문장 문맥 파악을 위한 rnn layer를 연결시킵니다.  
(`return_sequences=False`이므로 마지막 state만 출력합니다. 즉, `many-to-one`구조가 됩니다.)

- rnn layer에서 나온 결과를 token classification으로 다루기 위해  
총 token 갯수만큼 unit을 가진 dense 레이어를 연결시킵니다.

- dense layer output을 확률값으로 근사시키는 softmax activation을 적용합니다.

위의 과정을 Subclassing으로 구현하면 다음과 같습니다.

```python
class NextTokenPredictor(tf.keras.models.Model):
    
    def __init__(self, n_tokens:int, embed_dim:int, rnn_units:int):
        super().__init__()
        self.embed_layer = tf.keras.layers.Embedding(n_tokens, embed_dim)
        self.rnn_layer = tf.keras.layers.SimpleRNN(units=rnn_units)
        self.dense_layer = tf.keras.layers.Dense(units=n_tokens)
        self.softmax = tf.keras.layers.Softmax()
        
    def call(self, x):
        x = self.embed_layer(x)
        x = self.rnn_layer(x)
        x = self.dense_layer(x)
        x = self.softmax(x)
        return x
```

<br>

### Model Build

이제 위의 모델을 build 하겠습니다.  
token을 8차원으로 임베딩을 하기위해 `EMBED_DIM`은 8로, RNN의 UNIT 갯수를 16으로 설정하였습니다.  
(모델의 하이퍼 파라미터이므로 자유롭게 변경해보면서 결과를 보셔도 괜찮습니다.)

```python
EMBED_DIM = 8
RNN_UNITS = 16

model = NextTokenPredictor(
    n_tokens=n_tokens,
    embed_dim=EMBED_DIM,
    rnn_units=RNN_UNITS,
)

model.build(input_shape=(None, timesteps))
model.summary()
```
```
Model: "next_token_predictor"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 embedding (Embedding)       multiple                  136       
                                                                 
 simple_rnn (SimpleRNN)      multiple                  400       
                                                                 
 dense (Dense)               multiple                  289       
                                                                 
 softmax (Softmax)           multiple                  0         
                                                                 
=================================================================
Total params: 825
Trainable params: 825
Non-trainable params: 0
_________________________________________________________________
```

<br>

## Training

학습을 위한 하이퍼 파라미터 설정, loss function, optimizer, metrics 객체 정의 및 `tf.data`를 활용하여 Data Loader(Data Generator)를 정의합니다.

```python
# Train Hyper-params
EPOCHS = 16
BATCH_SIZE = 6
LEARNING_RATE = 1e-1  # 0.1

# Loss function
loss_obj = tf.keras.losses.CategoricalCrossentropy()

# Optimizer
optimizer = tf.keras.optimizers.Adam(learning_rate=LEARNING_RATE)

# Metrics
loss_metric = tf.keras.metrics.Mean()
acc_metric = tf.keras.metrics.CategoricalAccuracy()

# tf.data
n_train = train_x.shape[0]
train_ds = tf.data.Dataset.from_tensor_slices((train_x, train_y))
train_ds = train_ds.shuffle(n_train).batch(BATCH_SIZE)
```

이제 위에서 정의한 값들을 통해서 학습을 진행합니다.

```python
for epoch in range(1, EPOCHS+1):
    for x, y in train_ds:
        # Forward Propagation
        with tf.GradientTape() as tape:
            preds = model(x)
            loss = loss_obj(y, preds)
            
        # Back Propagation
        grads = tape.gradient(loss, model.trainable_variables)
        
        # Update Parameters
        optimizer.apply_gradients(zip(grads, model.trainable_variables))
        
        loss_metric(loss)
        acc_metric(y, preds)
        
    template = 'Epoch: {:2} | Train Loss: {:.4f} | Train Accuracy: {:.2f}%'
    
    epoch_loss = loss_metric.result()
    epoch_acc = acc_metric.result()
    print(template.format(epoch, epoch_loss, epoch_acc*100))
    
    loss_metric.reset_states()
    acc_metric.reset_states()
```
```
Epoch:  1 | Train Loss: 2.7867 | Train Accuracy: 0.00%
Epoch:  2 | Train Loss: 2.8667 | Train Accuracy: 6.25%
Epoch:  3 | Train Loss: 1.7041 | Train Accuracy: 56.25%
Epoch:  4 | Train Loss: 1.0061 | Train Accuracy: 75.00%
Epoch:  5 | Train Loss: 0.4361 | Train Accuracy: 93.75%
Epoch:  6 | Train Loss: 0.2649 | Train Accuracy: 93.75%
Epoch:  7 | Train Loss: 0.1114 | Train Accuracy: 100.00%
Epoch:  8 | Train Loss: 0.0752 | Train Accuracy: 100.00%
Epoch:  9 | Train Loss: 0.0358 | Train Accuracy: 100.00%
Epoch: 10 | Train Loss: 0.0186 | Train Accuracy: 100.00%
Epoch: 11 | Train Loss: 0.0119 | Train Accuracy: 100.00%
Epoch: 12 | Train Loss: 0.0090 | Train Accuracy: 100.00%
Epoch: 13 | Train Loss: 0.0064 | Train Accuracy: 100.00%
Epoch: 14 | Train Loss: 0.0050 | Train Accuracy: 100.00%
Epoch: 15 | Train Loss: 0.0042 | Train Accuracy: 100.00%
Epoch: 16 | Train Loss: 0.0037 | Train Accuracy: 100.00%
```

<br>

## Inference

학습한 모델을 통해서 최종 추론을 해보겠습니다. 아래의 `get_next_words` 함수가 이 역할을 수행합니다. `init_token`으로 주어진 token이 모델의 첫 input이 됩니다. 이후, 추론된 token은 다음 추론을 위한 input에 추가되어 next token을 예측하는데 활용됩니다. 예를들면 다음과 같습니다.  

- Iteration 1: `init_token='푸른'` → Model → '바다'
- Iteration 2: ' 푸른 바다' → Model → '위에서'
- Iteration 3: '푸른 바다 위에서' → Model → '배가'
- ...

```python
def get_next_tokens(init_token:str, n_iter:int, timesteps:int, model:NextTokenPredictor, token2idx:dict) -> list:
    if init_token not in token2idx:
        raise Exception(f'Not trained token({init_token})')
        
    idx2token = {idx:token for token, idx in token2idx.items()}
    ret = [init_token]
    
    for _ in range(n_iter):
        # Preprocessing
        integer_encoded = tokenizer.texts_to_sequences([init_token])[0]
        integer_encoded_padded = tf.keras.preprocessing.sequence.pad_sequences([integer_encoded], maxlen=timesteps)
        
        # Inference a next token
        pred_softmax_result = model.predict(integer_encoded_padded)
        pred_idx = pred_softmax_result.argmax(axis=1).item()
        
        # Index to token
        pred_word = idx2token.get(pred_idx)
        ret.append(pred_word)
        
        init_token = init_token + ' ' + pred_word

    return ret
```

```
pred_sentence: list = get_next_tokens(
    init_token='푸른',
    n_iter=6,
    timesteps=timesteps,
    model=model,
    token2idx=tokenizer.word_index,
)
print(*pred_sentence)
```
```
푸른 바다 위에서 배가 항해하는 것이 보입니다
```

지금까지 TensorFlow2를 활용한 Next token Predictor를 구현해보았습니다.


<br>

## Reference

- [\[딥러닝을 이용한 자연어 처리 입문\] 08-6: RNN을 이용한 텍스트 생성(Text Generation using RNN)](https://wikidocs.net/45101)

- [TensorFlow Document](https://www.tensorflow.org/api_docs)

---
layout: post
title: Advanced Multilayer Perceptron
categories: TIL
---

Multilayer perceptron (MLP)을 개선하기 위한 테크닉들.

* Weight Initialization
* Nonlinearity (Activation function)
* Optimizaers
* Batch Normalization
* Dropout (Regularization)
* Model Ensemble



아래는 기본적인 MLP 코드이다.

```python
# import
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from keras.datasets import mnist
from keras.models import Sequential
from keras.utils.np_utils import to_categorical
from keras.models import Sequential
from keras.layers import Activation, Dense
from keras import optimizers
from keras.layers import Dropout
import numpy as np
from keras.wrappers.scikit_learn import KerasClassifier
from sklearn.ensemble import VotingClassifier
from sklearn.metrics import accuracy_score
from keras.layers import BatchNormalization

# mnist에서 dataset 불러오기.
(X_train, y_train), (X_test, y_test) = mnist.load_data()
print(X_train.shape)
print(y_train.shape)

# train-set에서 첫번째 데이터(숫자) 확인하기
plt.imshow(X_train[0])
plt.show()
print('Label: ', y_train[0])
# test-set에서 첫번째 데이터(숫자) 확인하기
plt.imshow(X_test[0])    
plt.show()
print('Label: ', y_test[0])

# reshaping X data: (n, 28, 28) => (n, 784)
X_train = X_train.reshape((X_train.shape[0], -1))
X_test = X_test.reshape((X_test.shape[0], -1))

# use only 33% of training data to expedite the training process
X_train, _ , y_train, _ = train_test_split(X_train, y_train, test_size = 0.67, random_state = 7)

# converting y data into categorical (one-hot encoding)
y_train = to_categorical(y_train)
y_test = to_categorical(y_test)

print(X_train.shape, X_test.shape, y_train.shape, y_test.shape)

model = Sequential()

model.add(Dense(50, input_shape = (784, )))
model.add(Activation('sigmoid'))
model.add(Dense(50))
model.add(Activation('sigmoid'))
model.add(Dense(50))
model.add(Activation('sigmoid'))
model.add(Dense(50))
model.add(Activation('sigmoid'))
model.add(Dense(10))
model.add(Activation('softmax'))

sgd = optimizers.SGD(lr = 0.001) #Adam하면 금방 올라감
model.compile(optimizer = sgd, loss = 'categorical_crossentropy', metrics = ['accuracy'])

history = model.fit(X_train, y_train, batch_size = 256, validation_split = 0.3, epochs = 200, verbose = 0)

plt.plot(history.history['accuracy'])
plt.plot(history.history['val_accuracy'])
plt.legend(['training', 'validation'], loc = 'upper left')
plt.show()

results = model.evaluate(X_test, y_test)

print('Test accuracy: ', results[1])
```



---

## One-hot encoding
> [Wikicods.net][https://wikidocs.net/22647]
>
> [Code cleaner][https://cleancode-ws.tistory.com/67]

하나의 집합을 구성하는 요소별로 인덱스를 부여하는 것이 one-hot encoding이다. 예를 들어 사과, 배, 감을 구분하기 위해 아래와 같이 데이터를 변환해 주는 것이다.

사과: [1,0,0] / 배: [0,1,0] /감: [0,0,1]

여기서 '왜 사과:1 / 배: 2 / 감: 3 과 같은 방식으로 변환하지 않는가?' 라는 의문을 가질 수 있다. 하지만 이와 같이 변환했을 경우 연산이 가능해지며(ex. 감-사과=배) 숫자의 크기가 데이터 분석에 영향을 줄 수 있게된다. 따라서 범주형 데이터는 one-hot encoding을 수행한다.

one-hot encoding의 한계에는 요소의 개수가 많아져 집합이 커질수록 벡터를 저장하기 위한 공간이 계속 늘어난다는 점이 있다. 또한, 자연어 처리와 같은 분야에서 단어 간의 유사도를 표현하지 못한다는 단점이 있다. 예를 들어 의자, 책상, 냉장고를 one-hot encoding 하면 의자가 책상과 냉장고라는 단어 중 어떤 단어와 더 연관이 있는지 알 수 없다.

~~keras에서는 `to_categorical()` 함수를 사용해서 one-hot encoding을 수행 할 수 있다.~~

~~한편 one-hot encoding 된 것들을 원래대로 돌려주는 함수는 없는데, 이는 np.argmax() 함수를 사용하면 알 수 있다.(내용 추가 필요)~~



---
## Weight initialization (가중치 초기화)

> [reniew's blog][https://reniew.github.io/13/]
>
> [곰가드의 라이브러리][https://gomguard.tistory.com/184?category=712467]: 해당 글 말고 이전 글들부터 읽어보면 도움이 많이 된다.

기본적으로 초기값의 위치에 따라 학습 속도 혹은 정확한 학습이 가능하다. saddle point와 같은 local minimum에 갇히지 않는다.

perceptron은 선형 함수의 결과값(y, y=wx+b)를 activation function (활성화 함수)의 입력값으로 전달하게 되는데, 기본적인 sigmoid 함수의 경우 입력값이 [-5, 5] 범위 내에 있어야 출력값의 유의미한 변화가 있다. 만약 sigmoid의 입력값이 -5 이하이거나 5 이상이면 0 아니면 1의 값만 출력하게 된다. 따라서 wx+b(=y) 값을 [-5,5] 범위~~(아마 99%던지 뭔 기준이 있겠지)~~ 내에 들어올 수 있도록 w 값을 매우 작은 값으로 초기화 해주는 기법이 Weight initialization이다. 앞서 설명한 이유 외에도 w 값의 업데이트는 w의 미분값(w의 변화량)을 인자(변수)로 가지고 있는데 sigmoid 함수의 경우 [,-5], [5,]의 범위에서 변화량이 0에 수렴하기 때문에 w 값의 업데이트가 의미가 없어지게 된다.

Activation func. 의 입력값인 wx+b의 범위를 제한하기 위한 목적이므로, 코드를 작성할 때도 아래와 같이 Activation func.을 수행하기 전에 weight initialization을 수행한다.



```python
def mlp_model():
    model = Sequential()
    
    model.add(Dense(50, input_shape = (784, ), kernel_initializer='he_normal'))
    # `he_normal`이라는 초기화 방법 사용
    model.add(Activation('sigmoid'))    
    
    model.add(Dense(50, kernel_initializer='he_normal'))
    model.add(Activation('sigmoid'))    
    
    model.add(Dense(50, kernel_initializer='he_normal'))
    model.add(Activation('sigmoid'))    
    
    model.add(Dense(50, kernel_initializer='he_normal'))
    model.add(Activation('sigmoid'))    
    
    model.add(Dense(10, kernel_initializer='he_normal'))
    model.add(Activation('softmax'))
    
    sgd = optimizers.SGD(lr = 0.001)
    model.compile(optimizer = sgd, loss = 'categorical_crossentropy', metrics = ['accuracy'])
    
    return model
```



Activation func.의 대표격인 sigmoid 함수는 Xavier로, Relu 함수는 HE 방식으로 초기화 한다. 각 방식 모두 uniform distribution과 normal distribution 두 가지 방식을 가지고 있다.

wx+b의 값을 [-5,5]범위 내에 들어오게 하는 방법 중에 weight initialization이 w 값을 조절한다면, x 값의 범위를 제한하는 방식이 batch normalization이다.


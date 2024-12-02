# 모델 저장 및 체크포인트 실습 프로젝트

이 프로젝트는 **TensorFlow**를 활용하여 모델을 저장하고 불러오는 방법, 그리고 **ModelCheckpoint** 콜백을 사용하여 모델 훈련 중 가중치를 저장하는 방법을 실습하는 예제입니다. 또한, 저장된 모델을 다시 불러와 테스트 데이터를 평가하는 방법도 다룹니다.

## 프로젝트 개요

이 프로젝트의 목표는:

1. **모델 저장**: 훈련한 모델을 `.keras` 형식으로 저장하고 나중에 불러와서 재사용합니다.
2. **모델 불러오기**: 저장된 모델을 불러와서 이전 훈련 상태와 가중치를 그대로 활용합니다.
3. **체크포인트 콜백 사용**: 훈련 도중 **체크포인트**를 설정하여 주기적으로 가중치를 저장하고, 훈련이 중단되었을 때 저장된 가중치로 복구할 수 있도록 합니다.
4. **모델 평가**: 저장된 모델을 불러와 테스트 데이터로 모델 성능을 평가합니다.

## 프로젝트 실행 방법

1. **모델 저장 및 불러오기**
    - 먼저 훈련된 모델을 `model.save()` 메서드를 사용하여 파일로 저장합니다.
    - 이후 `tf.keras.models.load_model()` 메서드를 사용하여 저장된 모델을 불러옵니다.
    
    ```python
    python
    코드 복사
    # 모델 저장
    model.save('새폴더/model1.keras')
    
    # 저장된 모델 불러오기
    불러온모델 = tf.keras.models.load_model('새폴더/model1.keras')
    
    # 모델 요약 출력
    불러온모델.summary()
    
    ```
    
2. **체크포인트 사용**
    - 모델 훈련 중 **가중치 저장**을 위해 `ModelCheckpoint` 콜백을 설정합니다.
    - `save_weights_only=True`로 설정하면 훈련 중 가중치만 저장됩니다.
    - `save_freq='epoch'`로 설정하여 매 epoch마다 가중치를 저장합니다.
    
    ```python
    python
    코드 복사
    # 체크포인트 콜백 설정
    콜백함수 = tf.keras.callbacks.ModelCheckpoint(
        filepath='체크포인트/mnist.weights.h5',  # 가중치 파일 경로
        save_weights_only=True,  # 가중치만 저장
        save_freq='epoch'  # 매 epoch마다 저장
    )
    
    ```
    
3. **모델 훈련 및 체크포인트 저장**
    
    모델을 훈련하면서 체크포인트 콜백을 활용하여 훈련 중에 가중치를 주기적으로 저장할 수 있습니다. `fit()` 함수에 콜백을 전달합니다.
    
    ```python
    python
    코드 복사
    model.fit(trainX, trainY, epochs=10, callbacks=[콜백함수])
    
    ```
    
4. **모델 불러오기 및 평가**
    - 훈련이 중단된 후, 저장된 가중치를 불러와 모델을 평가할 수 있습니다.
    - `model.load_weights()`를 사용하여 저장된 가중치를 불러옵니다.
    
    ```python
    python
    코드 복사
    # 모델 정의
    model2 = tf.keras.Sequential([
        tf.keras.layers.Flatten(input_shape=(28, 28)),
        tf.keras.layers.Dense(128, activation='relu'),
        tf.keras.layers.Dense(10, activation='softmax'),
    ])
    
    # 모델 컴파일
    model2.compile(loss='sparse_categorical_crossentropy', optimizer='adam', metrics=['acc'])
    
    # 저장된 가중치 불러오기
    model2.load_weights('체크포인트/mnist.weights.h5')
    
    # 모델 평가
    model2.evaluate(testX, testY)
    
    ```
    

## 파일 구조

```bash
bash
코드 복사
프로젝트 폴더/
│
├── 새폴더/
│   └── model1.keras        # 저장된 모델 파일
│
├── 체크포인트/
│   └── mnist.weights.h5    # 저장된 가중치 파일
│
├── train.py                # 모델 훈련 스크립트
├── test.py                 # 모델 평가 스크립트
└── README.md               # 프로젝트 설명 파일

```

## 주요 기능

1. **모델 저장 및 불러오기**: 모델을 훈련한 후 `.keras` 파일로 저장하고, 추후에 모델을 불러와 재사용 가능.
2. **ModelCheckpoint**: 훈련 도중 주기적으로 가중치를 저장하고, 훈련이 중단된 경우 가중치 복구 가능.
3. **모델 평가**: 저장된 모델과 가중치를 사용하여 새로운 데이터에서 모델 성능을 평가합니다.

## 필수 라이브러리

이 프로젝트를 실행하기 위해서는 다음의 라이브러리가 필요합니다.

```bash
bash
코드 복사
pip install tensorflow

```

## 활용 방법

- **모델 저장**: 훈련된 모델을 `.keras` 파일로 저장하여, 후에 재훈련 없이 모델을 바로 사용할 수 있습니다.
- **체크포인트**: 훈련 중 예기치 않은 종료나 중단 시 체크포인트에서 가중치를 불러와 훈련을 이어갈 수 있습니다.
- **모델 평가**: 저장된 모델을 불러와 테스트 데이터로 성능을 평가하고, 다른 데이터셋에 적용할 수 있습니다.

## 참고 사항

- **모델 저장 위치**와 **파일 경로**는 프로젝트 구조에 맞게 수정할 수 있습니다.
- 훈련에 사용되는 데이터셋과 테스트 데이터는 별도로 준비해야 합니다.

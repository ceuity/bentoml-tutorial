# bentoml-tutorial

## BentoML 설치

```bash
pip install bentoml
```

## 분류 모델 서비스 정의

`@artifacts()` 는 예측 서비스에 필요한 모델을 정의한다.

`@env()` 는 예측 서비스에 필요한 종속성 및 환경 설정을 지정한다.

`@api` 는 예측 서비스에 액세스하기 위한 endpoint이다. Inference API를 정의한다.

## 배포를 위한 예측 서비스 저장

예측 서비스 클래스로 학습된 모델을 패키징 한 다음 배포를 위해 해당 클래스 인스턴스를 BentoML 형식으로 디스크에 저장한다.

BentoML은 기본적으로 모든 패키지 모델 파일을 `~/bentoml/repository/{service_name}/{service_version}` 디렉토리에 저장한다.

BentoML은 YataiService라는 모델 관리 구성 요소와 함께 제공되며, 이는 팀이 웹 UI 및 API를 통해 패키지 모델을 관리하고 액세스 할 수 있는 중앙 허브를 제공한다.

## Inference Server Running

```bash
$ bentoml serve [model_name]:[tag]
```

## Inference

- CLI

    ```bash
    $ bentoml run IrisClassifier:latest predict --input '[[5.1, 3.5, 1.4, 0.2]]'
    ```

- curl

    ```bash
    curl -i \
      --header "Content-Type: application/json" \
      --request POST \
      --data '[[5.1, 3.5, 1.4, 0.2]]' \
      http://localhost:5000/predict
    ```

## Containerize

```bash
$ bentoml containerize [model_name]:[tag] -t [name]
```

## 적응형 마이크로 배칭

Tensorflow 모델을 서빙하는 동안 개별 모델 추론 요청을 모아서 한 번에 처리하는 것이 성능에 좋다. 특히 GPU와 같은 하드웨어 가속기가 제공하는 높은 처리량을 활용하려면 일괄 처리가 필요하다.

- 인바운드 요청 : 사용자 클라이언트의 요청
- 아웃바운트 요청 : 업스트림 모델 서버에 대한 요청
- mb_max_batch_size : 배치의 최대 크기. 기본값 1000
- mb_max_latency : 서비스의 지연 시간 목표(ms). 기본값 10000
- 아웃바운드 세마포어 : 세마포어는 병렬 처리 정도로 동시에 처리되는 최대 배치 수를 나타낸다.
- 예상 시간 : 모델 서버가 배치를 실행하는데 걸리는 예상 시간이다. 대기열의 기록 데이터 및 현재 배치 크기에서 유추된다.

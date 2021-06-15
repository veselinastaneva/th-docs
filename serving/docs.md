## Make Predictions

### <a id="how-to-predict-getting-started"></a> Getting Started

Някакво въведение

### <a id="how-to-predict-python-sdk"></a> Python SDK

#### Simple Predictions

Тук трябва да обишем, че на всеки един деплойнът модел без никаква допълнителна настройка може да се правят тези ndarray predictions. 


```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="user/teachable",
    environment="production",
    serving_key="your-serving-key-here"
)

predictions = teachable.predict([[0.03, 0.05]])
print(predictions)
```

#### Advanced predictions with Features Validation

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="user/teachable",
    environment="production",
    serving_key="your-serving-key-here"
)

features = {
    "age": 0.03,
    "sex": 0.05
}

predictions = teachable.predict(features, order='desc', limit=10, threshold=0.5)
print(predictions)
```

Допълнителни параметри:
- order: [desc|asc] - сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а.
- limit: [int] - колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а.
- threshold: [float, min: 0.0 max: 1.0 - да върне всички класове които конфиденса на модела е над този трешхолд.]

#### Making Predictions on specific deployment version

Тук обясняваме, че понеже поддържаме различни енваирмънти и версии на деплоймънтите може да правим и предикшъни към специфична версия и енваиърмънт.

```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="user/teachable",
    environment="my-custom-environment",
    version=3,
    serving_key="your-serving-key-here"
)

predictions = teachable.predict([[0.03, 0.05]])
print(predictions)
```


### <a id="how-to-predict-rest-api"></a> REST API

#### Simple Predictions

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.


```bash
curl -X 'POST' \
  'https://serve.teachablehub.com/v1/user/teachable/predict/?environment=production' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "ndarray": [[0.3,0.5]]
}'
```

#### Advanced predictions with Features Validation

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

```bash
curl -X 'POST' \
  'https://serve.teachablehub.com/v1/user/teachable/predict/?environment=production' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "features": [
    {"age": 0.3, "sex": 0.5}
  ]
}'
```

Допълнителни параметри:
- order: [desc|asc] - сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а.
- limit: [int] - колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а.
- threshold: [float, min: 0.0 max: 1.0 - да върне всички класове които конфиденса на модела е над този трешхолд.]



#### Making Predictions on specific deployment version

Тук обясняваме, че понеже поддържаме различни енваирмънти и версии на деплоймънтите може да правим и предикшъни към специфична версия и енваиърмънт.

```bash
curl -X 'POST' \
  'https://serve.teachablehub.com/v1/user/teachable/predict/?environment=my-custom-environment&version=3' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "ndarray": [[0.3,0.5]]
}'
```

# Make Predictions

## <a id="how-to-predict-getting-started"></a> Getting Started

Teachables могат да бъдат интегрирани във всяка една платформа, чрез SDK или Rest API. И тнт локуми

### Setup Serving Keys

Тук кратка информация за serving кийс, че чрез тях контролираш, кой може да прави предикшъни към кой environment за по-добро секюрити и тнт. Добри примери за serving keys са `production`, `staging`, `rails-backoffice`, `ios-app-production` etc.

- Create a new serving key (евентуално линк към ключовете)


## <a id="how-to-predict-python-sdk"></a> Python SDK

Най-бързия и удобен начин да си интегрираш teachablehub platfrom в твоя python software е чрез Python SDK. ...

### Install Python SDK

За ползват python sdk-то. трябва да го инсталират с тази команда. Изисква минимална пайтън версия Python 3.4+

```
pip install teachablehub
```


### Simple Predictions

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

### Advanced predictions with Features Validation

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

#### Additional Params:

Със всеки един предикшън могат да се добавят и следните Params. Те рабоят за Teachables с дефинирани classes.

| Param     |      Values      |  Description |
|-----------|:-------------:|:------|
| order     |  `desc` or `asc` **default**: `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` **default**: `-1`  |   колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` **min**: `0.0` **max** `1.0` **default**: `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |


### Making Predictions on specific deployment version

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

### Errors

The SDK raise the following exceptions:

- `MissingTeachableError` - Teachable is required for every prediction
- `UnsuccessfulRequestError` - General Serving API errors 4xx and 5xx
- `UnauthorizedError` - Wrong Serving Key or configuration.

## <a id="how-to-predict-rest-api"></a> REST API

### Simple Predictions

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

### Advanced predictions with Features Validation

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

### Making Predictions on specific deployment version

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

### Additional Query Params:

Със всеки един предикшън към Rest API могат да се подават и следните Query Params

| Param     |      Values      |  Description |
|-----------|:-------------:|:------|
| order     |  `desc` or `asc` **default**: `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` **default**: `-1`  |   колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` **min**: `0.0` **max** `1.0` **default**: `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |

### Example Responses:

With Classes defined:

```javascript
{
  "request_id": "51b5597c-6c12-4559-948f-8a789756af9d",
  "predictions": [
    {
      "className": "Setosa",
      "probability": 0.4830337783311345
    },
    {
      "className": "Virginica",
      "probability": 0.31854067893251076
    },
    {
      "className": "Versicolour",
      "probability": 0.1984255427363547
    }
  ]
}
```

Wihout Classes defined:

```javascript
{
  "request_id": "e1e22637-0222-43a5-850d-a5640493e952",
  "predictions": [
    274.6187975893929
  ]
}
```

### Errors

The TeachableHub's Serving API respond with the following HTTP Response codes and messages:

| Code     |      Message      |
|-----------|:-------------|
| 403     |  No serving key provided |
| 401     |  You are not authorized to access {environment} environment of {user}/{teachable_name} |
| 400     |  Bad request for various reasons. For more information read the error messages. |
| 501     |  Not Implemented features or methods. |


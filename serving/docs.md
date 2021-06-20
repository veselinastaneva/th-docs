# How to make Predictions

## <a id="how-to-predict-getting-started"></a> Getting Started

Teachables могат да бъдат интегрирани във всяка една платформа, чрез SDK или Rest API. И тнт локуми

### 1. Setup Serving Keys

Тук кратка информация за serving кийс, че чрез тях контролираш, кой може да прави предикшъни към кой environment за по-добро секюрити и тнт. Добри примери за serving keys са `production`, `staging`, `rails-backoffice`, `ios-app-production` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/serving-keys/new", type: "primary", size: "medium", title: "Create a new Serving Key" } }}


<a id="how-to-predict-python-sdk"></a>

### 2. Install Python SDK

```
pip install teachablehub
```

За ползват python sdk-то. трябва да го инсталират с тази команда. Изисква минимална пайтън версия Python 3.4+

### 3. Start making Predictions

Тук трябва да обишем, че на всеки един деплойнът модел без никаква допълнителна настройка може да се правят тези ndarray predictions. 


```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    serving_key="your-serving-key-here"
)
{{#if(deployment_has_features_sample)}}
features = {{deployment_features_sample}}

predictions = teachable.predict(features)
{{/}}
{{#if(!deployment_has_features_sample)}}
predictions = teachable.predict({{deployment_ndarray_sample}})
{{/}}
print(predictions)
```

<br/><br/>

## Advanced Predictions Guide

### Advanced predictions with Features Validation

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    serving_key="your-serving-key-here"
)

features = {{deployment_features_sample}}

predictions = teachable.predict(features, order='desc', limit=10, threshold=0.5)
print(predictions)
```

#### `.predict()` Additional Params:

Със всеки един предикшън могат да се добавят и следните Params. Те рабоят за Teachables с дефинирани classes.

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| order     | `string` |  `desc` or `asc` | `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |


### Making Predictions on specific deployment version

Тук обясняваме, че понеже поддържаме различни енваирмънти и версии на деплоймънтите може да правим и предикшъни към специфична версия и енваиърмънт.

{{#if(deployment_has_features_sample)}}
```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    version={{deployment_version}},
    serving_key="your-serving-key-here"
)


features = {{deployment_features_sample}}

predictions = teachable.predict(features)
print(predictions)
```
{{/}}

{{#if(!deployment_has_features_sample)}}
```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    version={{deployment_version}},
    serving_key="your-serving-key-here"
)

predictions = teachable.predict({{deployment_ndarray_sample}})
print(predictions)
```
{{/}}

### Errors

The SDK raise the following exceptions:

| Error     |      Reason      |
|-----------|:-------------|
| `MissingTeachableError`     |  Teachable is required for every prediction |
| `NoThAuthMethodError`     |  Missing Serving Key param in the initializer |
| `UnsuccessfulRequestError`     |  General Serving API errors 4xx and 5xx |
| `UnauthorizedError`     |  Wrong Serving Key or configuration |

## <a id="how-to-predict-rest-api"></a> REST API

{{serving_api_base_url}}

You can play around with your teachables via the {{button: { to: "https://serve-teachablehub-dev.scalabl.cloud/docs#/predictions/predict__user___teachable__predict__post", type: "info", size: "small", title: "Swagger UI" } }}
 or following the examples below.

### Simple Predictions

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.


```bash
curl -X 'POST' \
  '{{serving_api_base_url}}/{{handler}}/{{teachable}}/predict/?environment={{deployment_environment}}&version={{deployment_version}}' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "ndarray": {{deployment_ndarray_sample}}
}'
```

### Advanced predictions with Features Validation

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

```bash
curl -X 'POST' \
  '{{serving_api_base_url}}/{{handler}}/{{teachable}}/predict/?environment={{deployment_environment}}&version={{deployment_version}}' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "features": [
    {{deployment_features_sample}}
   ]
}'
```

### Making Predictions on specific deployment version

Тук обясняваме, че понеже поддържаме различни енваирмънти и версии на деплоймънтите може да правим и предикшъни към специфична версия и енваиърмънт.

```bash
curl -X 'POST' \
  '{{serving_api_base_url}}/{{handler}}/{{teachable}}/predict/?environment={{deployment_environment}}&version={{deployment_version}}' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "ndarray": {{deployment_ndarray_sample}}
}'
```

### Additional Query Params:

Със всеки един предикшън към Rest API могат да се подават и следните Query Params

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| environment     | `string` |  `your-teachable-env` | `production` | към deployment от кой енв ще се прави предикшън  |
| version     | `int` |  **min**: `0` **max** `2000` | `0` | към коя версия на deployment ще се прави предикшън  |
| order     | `string` |  `desc` or `asc` | `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |

### Example Responses:

With Classes defined:

```javascript
{{deployment_prediction_result}}
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
| 400     |  Bad request for various reasons. For more information read the error messages. |
| 401     |  You are not authorized to access {{deployment_environment}} environment of {{handler}}/{{teachable}} |
| 403     |  No serving key provided |
| 422     |  Validation Error |
| 500     |  Unhandled internal server error. Please open a ticket to report it. |
| 501     |  Not Implemented features or methods. |


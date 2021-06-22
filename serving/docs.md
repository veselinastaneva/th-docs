# How to make Predictions

## <a id="how-to-predict-getting-started"></a> Getting Started

Teachables могат да бъдат интегрирани във всяка една платформа, чрез SDK или Rest API. И тнт локуми


### 1. Setup Serving Keys

Тук кратка информация за serving кийс, че чрез тях контролираш, кой може да прави предикшъни към кой environment за по-добро секюрити и тнт. Добри примери за serving keys са `production`, `staging`, `rails-backoffice`, `ios-app-production` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/serving-keys/new", type: "primary", size: "medium", title: "Create a new Serving Key" } }}


<a id="how-to-predict-python-sdk"></a>

### 2. Install Python SDK

За ползват python sdk-то. трябва да го инсталират с тази команда. Изисква минимална пайтън версия Python 3.4+

```
pip install teachablehub
```


### 3. Start making Predictions

Тук трябва да обишем, че на всеки един деплойнът модел без никаква допълнителна настройка може да се правят тези ndarray predictions. 

{{#if(deployment_has_features_sample)}}
```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
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
    serving_key="your-serving-key-here"
)

predictions = teachable.predict({{deployment_ndarray_sample}})
print(predictions)
```
{{/}}

<br/><br/>

# Advanced Predictions Guide

## Predictions on the Latest Version

text

```python
teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    serving_key="your-serving-key-here"
)
```

## Predictions on a Specific Version

text

```python
teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    version={{deployment_version}},
    serving_key="your-serving-key-here"
)
```


## Predictions with the Features Dict

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

```python
from teachablehub.clients import TeachableHubPredictAPI

teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    version={{deployment_version}},
    serving_key="your-serving-key-here"
)

features = {{deployment_features_sample}}

predictions = teachable.predict(features, order='desc', limit=10, threshold=0.5)
print(predictions)
```

### `.predict(ndarray_or_features, **kwargs)` Additional Params:

The `teachable.predict` method can work both with ndarrays (as well as np.ndarray) and features dict. In order to make much more useful it has support of couple additional kwargs that will eliminate some boring tasks for you when working with the classification models.

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| order     | `string` |  `desc` or `asc` | `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |


## Making Predictions on specific deployment version

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

## Errors

The SDK raise the following exceptions:

| Error     |      Reason      |
|-----------|:-------------|
| `MissingTeachableError`     |  Teachable is required for every prediction |
| `NoThAuthMethodError`     |  Missing Serving Key param in the initializer |
| `UnsuccessfulRequestError`     |  General Serving API errors 4xx and 5xx |
| `UnauthorizedError`     |  Wrong Serving Key or configuration |

# <a id="how-to-predict-rest-api"></a> REST API

{{serving_api_base_url}}

> You can play around with your teachables via the [TeachbaleHub's Serving API Swagger UI](https://serve-teachablehub-dev.scalabl.cloud/docs#/predictions/predict__user___teachable__predict__post) or following the examples below.

<br />

## Authentication

The TeachableHub's Serving API uses Serving Keys to authenticate your requests. Authentication to the Serving API is performed via `X-Serving-Key` header. Provide your Serving Key as the value to this header.

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

{{button: { to: "/{{handler}}/{{teachable}}/settings/serving-keys", type: "primary", size: "normal", title: "Manage your Serving Keys" } }}

<br />

## Query Params:

Със всеки един предикшън към Rest API могат да се подават и следните Query Params

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| environment     | `string` |  `your-teachable-env` | `production` | към deployment от кой енв ще се прави предикшън  |
| version     | `int` |  **min**: `0` **max** `2000` | `0` | към коя версия на deployment ще се прави предикшън  |
| order     | `string` |  `desc` or `asc` | `desc` | сортира резултат когато имаме много класове. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | колко класа да върне. това работи единственно, когато teachable-a има дефинирани classes в deployment-а. |
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` |    да върне всички класове които конфиденса на модела е над този трешхолд. |

<br />

## Making Prediction Requests

Тук трябва да опишем, че TH поддържа schema validation, която гарантира, че всеки един предикшън към модела, ще бъде с правилните features, по правилния начин и ще връща правилни релзултати всеки път. От друга страна, по този начин всеки един в екипа ще знае, модела колко и какви фийчъри очаква.

{{#if(deployment_has_features_sample)}}
```bash
curl -X 'POST' \
  '{{serving_api_base_url}}/{{handler}}/{{teachable}}/predict/?environment={{deployment_environment}}&version={{deployment_version}}' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "features": [{{deployment_features_sample}}]
}'
```
{{/}}

{{#if(!deployment_has_features_sample)}}
```bash
curl -X 'POST' \
  '{{serving_api_base_url}}/{{handler}}/{{teachable}}/predict/?environment={{deployment_environment}}&version={{deployment_version}}' \
  -H 'X-Serving-Key: your-serving-key' \
  -H 'Content-Type: application/json' \
  -d '{
  "ndarray": {{deployment_ndarray_sample}}
}'
```
{{/}}

<br />

## Prediction Response

This is an actual prediction response of `v{{deployment_version}}` of the `{{handler}}/{{teachable}}`

> Keep in mind that this response can change based on the deployment configuration. To be aware with all the possible prediction responses please visit the [TeachbaleHub's Serving API Swagger Docs](https://serve-teachablehub-dev.scalabl.cloud/docs#/predictions/predict__user___teachable__predict__post)

```javascript
{{deployment_prediction_result}}
```
<br />

## Errors

The TeachableHub's Serving API uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a prediction failed, etc.). Codes in the 5xx range indicate an error with the TeachableHub's Serving API servers (these are rare).

> Examples of the Errors Responses you can find at the [TeachbaleHub's Serving API Swagger Docs](https://serve-teachablehub-dev.scalabl.cloud/docs#/predictions/predict__user___teachable__predict__post)


| Code     |      Message      |
|-----------|:-------------|
| 400     |  Bad request. There are several different error titles returned for 400 errors, which will return an explanation in the “detail” field. |
| 401     |  You are not authorized to access {{deployment_environment}} environment of {{handler}}/{{teachable}} |
| 402     |  Request Failed. The parameters were valid but the request failed. |
| 403     |  No serving key provided |
| 409     |  Features Schema Validation Error. |
| 422     |  Validation Error |
| 500     |  Unhandled internal server error. Please open a ticket to report it. |
| 501     |  Not Implemented features or methods. |



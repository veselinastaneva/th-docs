{{header: { title: "How to make Predictions" } }}

<a id="how-to-predict-getting-started"></a>
## Getting Started

Teachables are a powerful machine learning models deployed as an API, entirely documented, available to be consumed by any server-side or client-side applications. Teachables are easily integrated in any platform via the TeachableHub REST API or the Python SDK. 

### 1. Setup Serving Keys

Serving Keys are the authorization you use for controlling who can make predictions and to which environment of your Teachable. Serving keys can be issued for a particular and environment and for a specific period of time. Such constraints ensure the better security of your awesome project. It's quite a useful feature for the times when you have some colleague working on the project only for a month, for example. 

You can create New Serving Key from Settings -> Serving Keys -> Add Key, where you'll need to supply a Key Name and Environment for which this key will be valid. Best practices suggest the name for each key to be descriptive, some good examples include:  `production`, `staging`, `rails-backoffice`, `ios-app-production` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/serving-keys/new", type: "primary", size: "medium", title: "Create a new Serving Key" } }}


<a id="how-to-predict-python-sdk"></a>

### 2. Install Python SDK

Requirements: Python 3.4+

To get predictions via the Python SDK, you'll first need to install the SDK with the following command:

```
pip install teachablehub
```


### 3. Start making Predictions

Each model you have already deployed can serve ndarray predictions without any additional setting. Check the example below:

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


# Features Help Section

{{features_help}}


<a id="how-to-predict-advanced-prediction-guide"></a> 

# Advanced Predictions Guide

The Serving process is quite straightforward and seamless. However, TeachbleHub also offers a variety of options for the times you need to fine-tune and order the predictions.  

## Specific Version Predictions

This is useful when you want to test with an older version of the model or when you want to improve the security and to have more control of what will be used in the software when this teachable is integrated. It gives you the option to get predictions from a specific deployment version.

```python
teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    version={{deployment_version}},
    serving_key="your-serving-key-here"
)
```


## Latest Version Predictions

That's the way to go, when you want to use always latest and greatest version. Do not specify any version and your Teachable will always return predictions from the latest model activated in the specific environment. Such approach is recommended and very helpful for automating your deployment pipeline.

```python
teachable = TeachableHubPredictAPI(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    serving_key="your-serving-key-here"
)
```


## Developer Friendly Predictions

For Data Scientists working with ndarrays and with numbers is business as usual. On the other side for the Back-end or the Front-end engineers working with JSON data and object is an everyday job. Not getting lost in translation is essential and using the `Features Validation` feature of TeachableHub is the connection between these two worlds.


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

**This deployment doesn't support Developer Friendly Predictions**. To configure Developer Friendly Predictions, take a look at the `Schema & Features Validation` section of the Deployment docs. Once you add those, your developers will love you and you'll surely become their superhero.🦸

Of course, when on your own you can always work with your Teachable without the `Features Validation`.

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

### `.predict(ndarray_or_features, **kwargs)`

The `teachable.predict` method works both with ndarrays (as well as np.ndarray) and features dict. In order to make it much more useful, we've added support for a couple of additional kwargs that eliminate some boring tasks for you when working with the classification models.

#### Params/kwargs:

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| order     | `string` |  `desc` or `asc` | `desc` | Very useful option for sorting the results when working with multiple number of classes. That works only when you've defined the classes in the Teachable Deployment|
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | Set limitation on the number of classes that will be returned. That works only when you've defined the classes in the Teachable Deployment|
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` | Returns all classes that the model confidence classifies above the given threshold.|


## Errors

The SDK raise the following exceptions:

| Error     |      Reason      |
|-----------|:-------------|
| `MissingTeachableError`     |  Teachable is required for every prediction |
| `NoThAuthMethodError`     |  Missing Serving Key param in the initializer |
| `UnsuccessfulRequestError`     |  General Serving API errors 4xx and 5xx |
| `UnauthorizedError`     |  Wrong Serving Key or configuration |


<a id="how-to-predict-rest-api"></a>

# REST API

> You can play around with your teachables via the [TeachbaleHub's Serving API Swagger UI]({{serving_api_swagger_url}}docs#/predictions/predict__user___teachable__predict__post) or following the examples below.

<br />

## Authentication

The TeachableHub's Serving API uses Serving Keys to authenticate your requests. Authentication to the Serving API is performed via `X-Serving-Key` header. Provide your Serving Key as the value to this header.

All API requests must be made over HTTPS. Calls made over plain HTTP will fail. API requests without authentication will also fail.

{{button: { to: "/{{handler}}/{{teachable}}/settings/serving-keys", type: "primary", size: "normal", title: "Manage your Serving Keys" } }}

<br />

## Query Params:

For each prediction request towards the Rest API, you can pass the following Query Params

| Param | Type | Values | Default | Description |
| :---  | :--- | :--- | :--- | :--- |
| environment     | `string` |  `your-teachable-env` | `production` | Specifies towards which deployment environment the prediction will be made. |
| version     | `int` |  **min**: `0` **max** `2000` | `0` | Specifies towards which deployment version the prediction will be made. |
| order     | `string` |  `desc` or `asc` | `desc` | Very useful option for sorting the results when working with multiple number of classes. That works only when you've defined the classes in the Teachable Deployment|
| limit     |    `int` | **min**: `0` **max** `2000` | `-1`  | Set limitation on the number of classes that will be returned. That works only when you've defined the classes in the Teachable Deployment|
| threshold | `float` | **min**: `0.0` **max** `1.0` | `0.0` | Returns all classes that the model confidence classifies above the given threshold.|

<br />

## Making Prediction Requests

Another great feature that TeachableHub supports is 'Schema Validation'. It guarantees that every single prediction towards your model will hold the correct features, passed in the right order, and will return accurate results each time you make a prediction. Moreover, your teammates will be always informed of what features the model is expecting, their number, and order. How cool is that!

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

> Keep in mind that this response can change based on the deployment configuration. To be aware with all the possible prediction responses please visit the [TeachbaleHub's Serving API Swagger Docs]({{serving_api_swagger_url}}docs#/predictions/predict__user___teachable__predict__post)

```javascript
{{deployment_prediction_result}}
```
<br />

## Errors

The TeachableHub's Serving API uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a prediction failed, etc.). Codes in the 5xx range indicate an error with the TeachableHub's Serving API servers (these are rare).

> Examples of the Errors Responses you can find at the [TeachbaleHub's Serving API Swagger Docs]({{serving_api_swagger_url}}docs#/predictions/predict__user___teachable__predict__post)


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

<br /><br /><br />

# Streamlit Apps

![TeachableHub + Streamlit + Heroku](https://media-blog.sashido.io/content/images/2021/07/thslhe.001.jpeg)

Integrate your Teachables within your Streamlit projects and deploy them on Heroku for free.

## Demo Applications

- [Iris Classifier with sklearn](https://th-iris-demo.herokuapp.com/) ([source code](https://github.com/teachablehub/streamlit-ludwig-ui-demo-app))
- [BBC Text Classifier with Ludwig](https://th-ludwig-demo.herokuapp.com/) ([source code](https://github.com/teachablehub/streamlit-ludwig-ui-demo-app))

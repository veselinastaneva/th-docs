# <a id="how-to-deploy"></a> Deploying Scikit-Learn Models

## <a id="how-to-deploy-getting-started"></a> Getting Started

You can integrate this SDK in your training logic, Jupyter notebook during experimentation, or your CI/CD system during the production environment. This SDK is also applicable for deploying existing models from your model registry or storage.

### 1. Install Python SDK

Тук трябва да напишем, че existing и нови модели се деплойват с питонското сдк. Минимална пайтън версия Python 3.4+

```
pip install teachablehub
```

### 2. Setup Deployment Keys

Тук кратка информация за деплоймънт кийс, че служат за деплой на модели, че може да се ограничават по environment за по-добро секюрити и тнт. Добри примери за deployment keys са `production`, `staging`, `backenders-team`, `ds-team`, `john-dev`, `jane-staging` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/deploy-keys/new", type: "primary", size: "medium", title: "Create a new Deploy Key" } }}


### <a id="how-to-deploy-examples"></a> 3. Deploy a Model

```python
from sklearn import svm
from sklearn import datasets

from teachablehub.deployments.sklearn import TeachableDeployment

clf = svm.SVC(gamma='scale', probability=True)
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)

deployment = TeachableDeployment(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    deploy_key="your-deploy-key-here",
)

deployment.model(clf)
deployment.samples(ndarray=[X[0]])
deployment.deploy(
    summary="Automatic deployment from our CI via sklearn-train-deploy.py",
    activate=True
)
```

> You can find the full example here: [sklearn-train-deploy.py](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy.py)

If you want know more about the deployment process or all of the features please read the [Advanced Deployment Guide](#advanced-deployment-guide) section below.

<br /><br /><br />

## Model Deployment for Data Scientists

We know that Jupyter Notebook is your favorite tool for experimentations and innovations. Having this in mind we would love to share with you a ready to use basic but powerful DeepNote workflow combining your Experimentation and Deployment process in just 3 simple steps.

Designed with simplicity in mind, you'll recieve free-of-charge Auto-Generated Serving API Documentation, Model Validation you'll be protected from deploying models that are not working as expected, better usage examples for everyone who will integrate the Teachable in every software.

### The Notebook Benefits

- Minimal Setup (less than 5 minutes)
- Automated & Scheduled Deployments (Retrain and Deploy every day or week)
- Environment Variables Support (Improved flexibility and security)
- Deployment Verifications
- Auto-Generated Serving API Documentation
- Easy Teachable Cloud Predictions
- and more.

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2Fgetting-started.ipynb", type: "info", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}

<br /><br /><br />

## Advanced Deployment Guide

Here information about the advanced deployment.


### Schema & Features Validation (optional, recommended)

As Data Scientists working with ndarrays and with numbers is business as usual. On the other side for the Back-end or the Front-end engineers working with JSON data and object is everyday job. Configuring this is the connection between these two worlds, plus you'll recieve tons of other benefits for free ... and you'll be your Team's SuperHero!

Deployment schema will enabled the TeachableHub Serving API to accept human readable features and will ensure that they will be validated before they are sent to the model. The schema validation feature will eliminate lots of mistakes and errors when you are working with your Teachable Predict API and will generate better documentation free of charge.

#### Structure

The Deploment Schema contains two parameters `features` and the `ndarray` mapping.

```python
deployment.schema({
    "features": {
        "sepal_length": {"type": "float"},
        "sepal_width": {"type": "float"},
        "petal_length": {"type": "float"},
        "petal_width": {"type": "float"},
    },
    "ndarray": [[
        "sepal_length",
        "sepal_width",
        "petal_length",
        "petal_width",
    ]]
})

```

**How to structure** the `ndarray` in the deployment schema. Structuring the this filed actually is very simple task. As a sklearn user and model creator you know that at the end of the day we should provide ndarray with some numbers there. The logic here is the following, where I should place the `sepal_length` feature from the request into the `ndarray` in the `model.predict` method. In this case `sepal_length` is the `0` item in the array we send to the `model.predict` method. Here is the example:

```python

# What we have in the request (they can be in different order)
{"features": {"sepal_length": 3, "sepal_width": 1, "petal_length": 2 "petal_width": 4} }

#Our ndarray map look like this / We ensure the same order every time.
"ndarray": [["sepal_length", "sepal_width", "petal_length", "petal_width" ]]

# What TeachableHub will do automatically
prediction = model.predict([[3,1,2,4]])

```

### Feature Validation Properties

### `required_all`

Note that **all the features** defined in the Validation Schema are **required by default**.

### `type`

Data type allowed for the key value. Can be one of the following names:

| Type Name | Python 2 Type | Python 3 Type |
| :---  | :--- | :--- |
| `float`     | [float](#) | [float](#) |
| `integer`     | [int](#), [long](#) | [int](#) |
| `number`     | [float](#), [int](#), [long](#), excl. [bool](#) | [float](#), [int](#), excl. [bool](#) |
| `string`     | [basestring()](#) | [str](#) |
| `date`     | [datetime.date](#) | [datetime.date](#) |


### `min, max`

Minimum and maximum value allowed for any object whose class implements comparison operations (`__gt__` & `__lt__`).

```python
deployment.schema({
    "features": {
        ...
        "age":  { "type": "float", "min": -0.1, "max": 0.1 },
        ...
    },
    ...
})
```

### `minlength`, `maxlength`

Minimum and maximum length allowed for sized types like `string`, `list` and the other sized types that implement `__len__`.

```python
deployment.schema({
    "features": {
        ...
        "long_text":  { "type": "string", "minlength": 500},
        ...
    },
    ...
})

```

### `help`

**TeachableHub use this rule to generate Docs section automatically where explain what is this feature about.** This is very helpful when your backend team or 3rd-party integrator consume the API, because they will be aware what data they should provide to the Teachable Predict API. 

```python
deployment.schema({
    "features": {
        ...
        "s1":  {
            "type": "float", 
            "max": 0.1, 
            "min": -0.1,
            "help": "What is this feature about, where we can get it. how to prepare it, how to generate it?",
        },
        ...
    },
    ...
})
```

### Model Classes (optional)

The TeachableHub's Auto-Generated Serving API will use these classes in the predictions results to return human readable predictions outputs. If classes configured for the deployment the Scikit-Learn will use the `predict_proba` method, if not it will use the regular `predict` method.

```python
deployment.classes({"0": "Setosa", "1": "Versicolour", "2": "Virginica" })
```

### Usage Samples (required)

Maintaining these deployment samples, you'll recieve free-of-charge Auto-Generated Serving API Documentation, Model Validation you'll be protected from deploying models that are not working as expected, better usage examples for everyone who will integrate the Teachable in every software. These examples will be used in the `PredictMan` as well. At least one of `ndarray` or `features` is required. if your `Teachable` is using the `Features Validation` feature both are required.

```python
deployment.samples(
    ndarray=[X_train[0]],
    features={"sepal_length": 5.1, "sepal_width": 3.5, "petal_length": 1.4, "petal_width": 0.2 }
)
```

### Deployment Context (optional)

When you need to add useful information that will bring more transparency for the whole team about the training environment, versioning of the dataset, versions of the packages in the environment deploying the model, servers and etc. or Easly tracking of changes, version of the training data or environment where the deployment was made. All of this helps you with the reproducing the models every time.

```
deployment.context({
    "author": "John Doe",
    "github_commit": "9e91a9d16eecf9e44935788ea777549de4377408",
    "dataset_version": "777549de4377408",
    "notebook": "https://deepnote.com/project/TeachableHubExample-Aw22ds202js0/%2Fgetting-started.ipynb",
    "scikit-learn": sklearn_version,
    "python": platform.python_version(),
    "local_hostname": platform.node(),
    "os_info": platform.version()
})
```

### Deployment Deploy (required)

This is the final step of the required deployment steps, you are almost there :) Here are two important things to do:

> `summary` - Like the git commit messages it's always great when you have some details about the deployment that will help you to rollback or refer to it.

> `description` - You can change it from the TeachableHub UI as well. The description can be used as changelog when needed. What are the new stuff of this model deployment. This is probably very helpful for the engineers that are integrating the Serving APIs or maintaining the platforms using this Teachable.

> `activate` - This option will set your new deployed model as the latest version of the environment it's deployed. This is dangerous to do it in the production environment directly but should be entirely okay for the experimentation or staning environments.


```
deployment.deploy(
    summary="What is this deployment about", # required
    description="You can use it as changelog.", # optional
    activate=True # optional, defualt: false
)
```

## CI/CD Automations Helpers

The are couple of useful functions that can help you when you are automating your model deployment in your CI/CD systems.

### `.successful()`

The `successful` method return a boolen result whether or not the deployment is created on the TeachableHub platform. Please keed in mind that the deployment need to be verfied successfuly as well in order to be served by the TeachableHub's Serving API.

```python
if deployment.successful():
    notify_the_team_on_slack(deployment_version=deployment.version())
```

### `.verified(reload=False)`

After every deployment based on your `.samples({...})` explained above, TeachableHub is verifing whether or not the deployment is configured correctly and woking as expected.

The `reload` option is to retrieve the latest updated deployment state from the TeachableHub platform.

```python
for check in range(10):
    if deployment.verified(reload=True):
        deployment.activate()
        new_release_slack_notification(deployment_version=deployment.version())
```


### Retrieving existing Deployment

In this example will give you the deployment v10 of the production environment. So you can apply all the helpers methods on it.

```python
deployment = TeachableDeployment(
    teachable="{{handler}}/{{teachable}}",
    environment="production",
    version="10",
    deploy_key="your-deploy-key-here",
)

```

### `.reload()`

This option is to retrieve the latest updated deployment state from the TeachableHub platform.

```python
deployment.reload()
```

### `.object()`

Using the raw deployment object from the TeachableHub API.

```python
deployment.object()
```

### `.version()`

The version as an integer of the current deployment object.

```python
deployment.version()
```

### `.activate()`

Make the current deployment the latest version for it's environment. Important note, the current deployment should be successfuly verified to be activated.

```python
deployment.activate()
```

### `.rollback(version)`

Revert to this deployment as you latest version of the environment this deployment was deployed.

```python
deployment.rollback(10)
```


### Other Examples

For the full list of features and examples checkout the following links:

- [Deploy an existing model](https://github.com/teachablehub/python-sdk/blob/master/examples/deploy-existing.py)
- [Advanced Deployment](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-advanced.py)
- [Advanced Deployment of Regression model](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-regression-advanced.py)

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2Fgetting-started.ipynb", type: "warning", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}



<br /><br /><br />

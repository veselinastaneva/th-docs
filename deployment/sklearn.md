# <a id="how-to-deploy"></a> How to Deploy

## <a id="how-to-deploy-getting-started"></a> Getting Started

You can integrate this SDK in your training logic, Jupyter notebook during experimentation, or your CI/CD system during the production environment. This SDK is also applicable for deploying existing models from your model registry or storage.

### 1. Install Python SDK

Тук трябва да напишем, че existing и нови модели се деплойват с питонското сдк. Минимална пайтън версия Python 3.4+

```
pip install teachablehub
```

### 2. Setup Deployment Keys

Тук кратка информация за деплоймънт кийс, че служат за деплой на модели, че може да се ограничават по environment за по-добро секюрити и тнт. Добри примери за deployment keys са `production`, `staging`, `backenders-team`, `ds-team`, `john-dev`, `jane-staging` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/deploy-keys", type: "primary", size: "medium", title: "Create a new deploy key" } }}


### <a id="how-to-deploy-examples"></a> 3. Deploy a Model

```python
from teachablehub.deployments.sklearn import TeachableDeployment

# ... training logic here ...

deployment = TeachableDeployment(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    deploy_key="your-deploy-key-here",
)

deployment.model(clf)
deployment.samples(ndarray=[X_train[0]])
deployment.deploy(
    summary="Automatic deployment from our CI via sklearn-deploy.py",
    activate=True
)
```

> You can find the full example here: [sklearn-train-deploy.py](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy.py)

If you want know more about the deployment process or all of the features please read the `Advanced Deploymebt Guide` section below.

<br /><br /><br />

## Model Deployment for Data Scientists

Maintaining these deployment samples, you'll recieve free-of-charge Auto-Generated Serving API Documentation, Model Validation you'll be protected from deploying models that are not working as expected, better usage examples for everyone who will integrate the Teachable in every software.

### Environment Features

- Feature 1
- Feature 2
- Feature 3
- etc.

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2Fgetting-started.ipynb", type: "info", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}

<br /><br /><br />

## Advanced Deployment Guide

Here information about the advanced deployment.

For the full list of features and examples checkout the following links:

- [Deploy an existing model](https://github.com/teachablehub/python-sdk/blob/master/examples/deploy-existing.py)
- [Advanced Deployment](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-advanced.py)
- [Advanced Deployment of Regression model](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-regression-advanced.py)

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2Fgetting-started.ipynb", type: "warning", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}



<br /><br /><br />

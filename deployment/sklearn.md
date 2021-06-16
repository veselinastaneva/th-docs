# <a id="how-to-deploy"></a> How to Deploy

## <a id="how-to-deploy-getting-started"></a> Getting Started

You can integrate this SDK in your training logic, Jupyter notebook during experimentation, or your CI/CD system during the production environment. This SDK is also applicable for deploying existing models from your model registry or storage.

#### Install Python SDK

Тук трябва да напишем, че existing и нови модели се деплойват с питонското сдк. Минимална пайтън версия Python 3.4+

```
pip install teachablehub
```

#### Setup Deployment Keys

Тук кратка информация за деплоймънт кийс, че служат за деплой на модели, че може да се ограничават по environment за по-добро секюрити и тнт. Добри примери за deployment keys са `production`, `staging`, `backenders-team`, `ds-team`, `john-dev`, `jane-staging` etc.

[Create a new deploy key](https://app-th-com.vercel.app/mignev/officedemo/settings/deploy-keys)


## <a id="how-to-deploy-examples"></a> Examples

```python
from teachablehub.deployments.sklearn import TeachableDeployment

# ... training logic here ...

deployment = TeachableDeployment(
    teachable="user/teachable",
    environment="production",
    deploy_key="your-deploy-key-here",
)

deployment.model(clf)
deployment.samples(ndarray=[X_train[0]])
deployment.deploy(
    summary="Automatic deployment from our CI via sklearn-deploy.py",
    activate=True
)
```

For the full list of features and examples checkout the following links:

- [Deploy an existing model](https://github.com/teachablehub/python-sdk/blob/master/examples/deploy-existing.py)
- [Advanced Deployment](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-advanced.py)
- [Advanced Deployment of Regression model](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-regression-advanced.py)


For more details [checkout the Python SDK on GitHub](https://github.com/teachablehub/python-sdk)

<br /><br /><br />

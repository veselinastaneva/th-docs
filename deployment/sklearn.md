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

- Create a new deploy key (евентуално линк към ключовете)


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
deployment.deploy(
    summary="Automatic deployment from our CI via sklearn-deploy.py",
    activate=True
)
```

## <a id="how-to-deploy-SDK"></a> Python SDK Docs

  - Initializer
  - classes
  - schema
  - context
  - model
  - deploy
  - successful
  - activate
  - rollback
  - version
  - errors/exceptions 

[Read the full Python SDK Docs](https://)

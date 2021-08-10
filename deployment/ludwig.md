{{header: { title: "Deploying Ludwig Models" } }}

<a id="how-to-deploy-getting-started"></a>
## Getting Started

You can integrate this SDK in your training logic, Jupyter notebook during experimentation, or your CI/CD system during the production environment. This SDK is also applicable for deploying existing models from your model registry or storage.

### 1. Install Python SDK

Requirements: Python 3.4+

Existing and new models are easily deployed via the python SDK. Use the following command to install the SDK:

```
pip install teachablehub
```

### 2. Setup Deployment Keys

To deploy any model to your Teachable, you'll need a Deployment Key. Deployment keys can be restricted for a particular environment and for a specific period of time. Such constraints ensure the better security of your awesome project. It's quite a useful feature for the times when only a few members of your team are responsible for deployments in production and need to have permission or you when having some colleague working on the project only for a month.  

You can create New Deployment Key from Settings -> Deploy Keys -> Add Key, where you'll need to supply a Key Name and Environment for which this key will be valid. Best practices suggest the name for each key to be descriptive, some good examples include: `production`, `staging`, `backend-team`, `ds-team`, `john-dev`, `jane-staging` etc.

{{button: { to: "/{{handler}}/{{teachable}}/settings/deploy-keys/new", type: "primary", size: "medium", title: "Create a new Deploy Key" } }}

<a id="how-to-deploy-examples"></a>
### 3. Deploy a Model

```python
from teachablehub.deployments.ludwig import TeachableDeployment

# ... training logic here ...

ludwig_model = LudwigModel(ludwig_config)

deployment = TeachableDeployment(
    teachable="{{handler}}/{{teachable}}",
    environment="{{deployment_environment}}",
    deploy_key="your-deploy-key-here",
)

deployment.model(ludwig_model)
deployment.samples(
    features={
      "text": "wilkinson fit to face edinburgh england captain jonny wilkinson"
    }
)

deployment.deploy(
    summary="Automatic deployment from our CI via ludwig-train-deploy.py",
    activate=True
)
```

> You can find the full example here: [ludwig-train-deploy.py](https://github.com/teachablehub/python-sdk/blob/master/examples/ludwig-train-deploy.py)

If you want know more about the deployment process or all of the features please read the [Advanced Deployment Guide](#advanced-deployment-guide) section below.

<br /><br /><br />

<a id="how-to-deploy-jupyter-notebook-environment"></a>
## Model Deployment for Data Scientists

We know that Jupyter Notebook is your favorite tool for experimentations and innovations. Having this in mind, we would love to share with you a ready-to-use basic yet powerful DeepNote workflow combining the Experimentation and Deployment process in just 2 simple steps.

Designed with simplicity in mind, TeachbaleHub provides you with free-of-charge Auto-Generated Serving API Documentation, Model Validation that protects you from deploying models that are not working as expected, and better usage examples for everyone who integrates the Teachable in every software.

### The Notebook Benefits

- Minimal Setup (less than 5 minutes)
- Automated & Scheduled Deployments (Retrain and Deploy every day or week)
- Environment Variables Support (Improved flexibility and security)
- Deployment Verifications
- Auto-Generated Serving API Documentation
- Easy Teachable Cloud Predictions
- and more.

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2F1_getting-started-ludwig.ipynb", type: "info", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}

<br /><br /><br />

<a id="how-to-deploy-advanced-deployment-guide"></a>
## Advanced Deployment Guide

The Deployment process in TeachableHub is quite automated and seamless, but it also offers many additional options that help you and your team to keep a neat workflow and speak the same language.  

### Schema & Features Validation (optional)

TeachableHub has built-in mechanisms for **`Schema and Features Validation` that are automatically received from your Ludwig model `input_features` and `output_features`**. Still, you can further fine-tune those in accordance with your specific case.

For Data Scientists working with ndarrays and with numbers is business as usual. On the other side for the Back-end or the Front-end engineers working with JSON data and object is an everyday job. Not getting lost in translation is essential and configuring this is the connection between these two worlds. You'll surely become your Team's SuperHero right away! 😉

Deployment schema enables the TeachableHub Serving API to accept human-readable features. Furthermore, it ensures those features will be validated before they are sent to the model. The cool thing is that it eliminates any involuntary mistakes and errors while working with your Teachable Predict API and will generate better documentation with no effort and free of charge.

#### Structure

The Deploment Schema contains the `features` parameter, which matches the "input_features" of your model by default.

```python
deployment.schema({
    "features": {
        "text": {"type": "string"},
    }
})

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

### `allowed`

This rule takes a list of allowed values. Validates the target value if the value is in the allowed values. The types `string`, `number`, etc. are supported as well.

```python
deployment.schema({
    "features": {
        ...
        "sex":  { 
            "type": "integer",
            "allowed": [1, 2], 
            "max": 0.1,
            "help": "sex as integer, 1 for Male and 2 for Femail."
        },
        ...
    },
    ...
})
```

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

**TeachableHub uses this rule to automatically generate Docs section that explains what is a specific feature about.** This is very helpful when your backend team or 3rd-party integrator consumes the API because they will be aware of what data they should provide to the Teachable Predict API. So better not skip this one! 😉

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

The TeachableHub's Auto-Generated Serving API will use the Model Classes in the prediction results to return human-readable predictions outputs. Model classes are automatically configured from your model's "output_features".


### Usage Samples (required)

Maintaining these deployment samples, you receive free-of-charge Auto-Generated Serving API Documentation, Model Validation that protects you from deploying models that are not working as expected, and better usage examples for everyone who will integrate the Teachable in their software. These examples are the ones used in the `PredictMan` as well. **At least one of `ndarray` or `features` is required**. If your `Teachable` is using the `Features Validation` feature both are required.

```python
deployment.samples(
    features={"text": "this is very interesting text from the sports section"}
)
```

### Deployment Context (optional)

Deployment Context is quite useful for the time when you need to add some information that **brings more transparency for the whole team** about the training environment, versioning of the dataset, versions of the packages in the model deployment environment, servers, and etc. It's also effective for easily tracking changes, version of the training data, or environments where the deployment was made. All of this helps you with reproducing the models every time.

```python
deployment.context({
    "branch": "main",
    "github_commit": "9e91a9d16eecf9e44935788ea777549de4377408",
    "dataset_path": "/examples_data/ludwig/bbc-text-v23.csv",
    "deploy_script": "/ci/train-deploy-ludwig.py",
    "ludwig": LUDWIG_VERSION,
    "python": platform.python_version(),
    "local_hostname": platform.node(),
    "os_info": platform.version()
})
```

### Deployment Deploy (required)

Congrats, you're almost done! 😊 This is the final stop of the required deployment steps. Here are the three important params to keep in mind:

> `summary` - Just like the git commit messages, 'summary' lets you provide some information about the deployment. It's always great to have the details that will help you refer to this specific deployment or rollback.

> `description` -  The description can be used as a changelog when needed. It should contain valuable clues on what's new stuff in this model deployment. I imagine how this can be very helpful for the engineers that are integrating the Serving APIs or maintaining the platforms, using this Teachable. You can change the 'description' from the TeachableHub UI as well.

> `activate` - This option set to 'true' automatically sets the newly deployed model as the latest version of the environment to which it's deployed. Keep in mind that it might be dangerous to execute in the production environment directly. However, it's entirely okay for experiments or staging environments.


```
deployment.deploy(
    summary="What is this deployment about", # required
    description="You can use it as changelog.", # optional
    activate=True # optional, defualt: false
)
```

<a id="how-to-deploy-ci-cd-automation-helpers"></a>
## CI/CD Automations Helpers

Below you may find a couple of useful functions that can assist in automating your model deployment in your CI/CD systems.

### `.successful()`

The `successful` method returns a boolean result whether or not the deployment is created on the TeachableHub platform. Кeep in mind that the deployment needs to be verified successfully as well in order to be served by the TeachableHub's Serving API. Check the next point for more details on verification.

```python
if deployment.successful():
    notify_the_team_on_slack(deployment_version=deployment.version())
```

### `.verified(reload=False)`

After every deployment based on your `.samples({...})` explained above, TeachableHub is verifying whether or not the deployment is configured correctly and working as expected.

The `reload` option is to retrieve the latest updated deployment state from the TeachableHub platform.

```python
for check in range(10):
    if deployment.verified(reload=True):
        deployment.activate()
        new_release_slack_notification(deployment_version=deployment.version())
```


### Retrieving existing Deployment

This example gives you the deployment v10 of the production environment, so you can apply all the helpers' methods to it.

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

Make the current deployment the latest version for its environment. Important note: the current deployment should be successfuly verified to be activated.

```python
deployment.activate()
```

### `.rollback(version)`

Revert to this deployment as your latest version of the environment this deployment was deployed.

```python
deployment.rollback(10)
```

<a id="how-to-deploy-other-examples"></a>
### Other Examples

For the full list of features and examples checkout the following links:

- [Deploy an existing model](https://github.com/teachablehub/python-sdk/blob/master/examples/deploy-existing.py)
- [Advanced Deployment](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-advanced.py)
- [Advanced Deployment of Regression model](https://github.com/teachablehub/python-sdk/blob/master/examples/sklearn-train-deploy-regression-advanced.py)

{{button: { to: "https://deepnote.com/project/TeachableHub-eXmrWiQKTm6fXJFLzxxCpA/%2F1_getting-started-ludwig.ipynb", type: "info", size: "medium", title: "Explore the Jupyter Notebook Environment on Deepnote"} }}

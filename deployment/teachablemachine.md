{{header: { title: "Deploying Teachable Machine models" } }}

<a id="how-to-deploy-getting-started"></a>
## Getting Started

Deploying a Teachable Machine model on TeachbleHub is pretty straightforward. [Teachable Machine](https://teachablemachine.withgoogle.com/) is a web-based tool that makes creating machine learning models fast, easy, and accessible to everyone. 

<iframe width="900" height="534" src="https://www.youtube.com/embed/T2qQGqZxkD0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


Of course, before you get to deploy your model, the first thing you need to do is to have a trained model. It is as easy as it gets and you can follow the simple tutorial made by Google for how to [Gather data](https://www.youtube.com/watch?v=DFBbSTvtpy4&list=PLJfHZtseuscuTQfodmFnbZ3rBgCWsRT9t&index=1) and [Train your model](https://www.youtube.com/watch?v=CO67EQ0ZWgA&list=PLJfHZtseuscuTQfodmFnbZ3rBgCWsRT9t&index=2). 

### 1. Export your model

Once you have completed training you’ll need to [export your model](https://www.youtube.com/watch?v=n-zeeRLBgd0&list=PLJfHZtseuscuTQfodmFnbZ3rBgCWsRT9t&index=3) as a TensorFlow native model and convert it to **Keras.h5 model**. 

![export-your-model-1](https://media-blog.sashido.io/content/images/2021/09/export-your-model-1.png)
 
### 2. Extract the .zip file
When you download the model it will be in a .zip format. You will need to extract the `keras_model.h5` and `abels.txt` files on your computer.

### 3. Deploy a Model
The deployment, like the rest of the steps, is pretty simple. Go to the **Deployments** tab and press the **“Create new deployment”** button. 

{{button: { to: "/{{handler}}/{{teachable}}/settings/deployment/new", type: "primary", size: "medium", title: "Create New Deployment" } }}

Here you will need to upload the `keras_model.h5` and the `label.txt` files along with a sample URL of your training data. You also need to add a short description of the model to bring more transparency to your team. 

![create-deployment-1](https://media-blog.sashido.io/content/images/2021/09/create-deployment-1.png)

And that’s it! You have successfully deployed a Teachable Machine model!

## More Examples
Here is an awesome list full of creative ideas and examples:

- [Awesome Teachable Machine List](https://github.com/SashiDo/awesome-teachable-machine) 

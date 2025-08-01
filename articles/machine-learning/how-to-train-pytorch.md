---
title: Train deep learning PyTorch models (SDK v2)
titleSuffix: Azure Machine Learning
description: Learn how to run your PyTorch training scripts at enterprise scale using Azure Machine Learning SDK (v2).
services: machine-learning
ms.service: azure-machine-learning
ms.subservice: training
ms.author: scottpolly
author: s-polly
ms.reviewer: balapv
ms.date: 09/17/2024
ms.topic: how-to
ms.custom: sdkv2, update-code1, FY25Q1-Linter
#Customer intent: As a Python PyTorch developer, I need to combine open-source with a cloud platform to train, evaluate, and deploy my deep learning models at scale.
---

# Train PyTorch models at scale with Azure Machine Learning

[!INCLUDE [sdk v2](includes/machine-learning-sdk-v2.md)]

In this article, you learn to train, hyperparameter tune, and deploy a [PyTorch](https://pytorch.org/) model using the Azure Machine Learning Python SDK v2.

You use example scripts to classify chicken and turkey images to build a deep learning neural network (DNN) based on [PyTorch's transfer learning tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html). Transfer learning is a technique that applies knowledge gained from solving one problem to a different but related problem. Transfer learning shortens the training process by requiring less data, time, and compute resources than training from scratch. To learn more about transfer learning, see [Deep learning vs. machine learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning).

Whether you're training a deep learning PyTorch model from the ground-up or you're bringing an existing model into the cloud, use Azure Machine Learning to scale out open-source training jobs using elastic cloud compute resources. You can build, deploy, version, and monitor production-grade models with Azure Machine Learning.

## Prerequisites

- An Azure subscription. If you don't have one already, [create a free account](https://azure.microsoft.com/free/).
- Run the code in this article using either an Azure Machine Learning compute instance or your own Jupyter notebook.
    - Azure Machine Learning compute instance—no downloads or installation necessary:
        - Complete the [Quickstart: Get started with Azure Machine Learning](quickstart-create-resources.md) to create a dedicated notebook server preloaded with the SDK and the sample repository.
        - Under the **Samples** tab in the **Notebooks** section of your workspace, find a completed and expanded notebook by navigating to this directory: *SDK v2/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch*
    - Your Jupyter notebook server:
        - Install the [Azure Machine Learning SDK (v2)](https://aka.ms/sdk-v2-install).
        - Download the training script file [pytorch_train.py](https://github.com/Azure/azureml-examples/blob/main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/src/pytorch_train.py).

You can also find a completed [Jupyter notebook version](https://github.com/Azure/azureml-examples/blob/main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) of this guide on the GitHub samples page.

## Set up the job

This section sets up the job for training by loading the required Python packages, connecting to a workspace, creating a compute resource to run a command job, and creating an environment to run the job.

### Connect to the workspace

First, you need to connect to your [Azure Machine Learning workspace](concept-workspace.md). The workspace is the top-level resource for the service. It provides you with a centralized place to work with all the artifacts you create when you use Azure Machine Learning.

We're using `DefaultAzureCredential` to get access to the workspace. This credential should be capable of handling most Azure SDK authentication scenarios.

If `DefaultAzureCredential` doesn't work for you, see [azure.identity package](/python/api/azure-identity/azure.identity) or [Set up authentication](how-to-setup-authentication.md?tabs=sdk) for more available credentials.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=credential)]

If you prefer to use a browser to sign in and authenticate, you should uncomment the following code and use it instead.

```python
# Handle to the workspace
# from azure.ai.ml import MLClient

# Authentication package
# from azure.identity import InteractiveBrowserCredential
# credential = InteractiveBrowserCredential()
```

Next, get a handle to the workspace by providing your subscription ID, resource group name, and workspace name. To find these parameters:

1. Look for your workspace name in the upper-right corner of the Azure Machine Learning studio toolbar.
2. Select your workspace name to show your resource group and subscription ID.
3. Copy the values for your resource group and subscription ID into the code.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=ml_client)]

The result of running this script is a workspace handle that you can use to manage other resources and jobs.

> [!NOTE]
> Creating `MLClient` doesn't connect the client to the workspace. The client initialization is lazy and waits for the first time it needs to make a call. In this article, this happens during compute creation.

### Create a compute resource to run the job

Azure Machine Learning needs a compute resource to run a job. This resource can be single or multi-node machines with Linux or Windows OS, or a specific compute fabric like Spark.

In the following example script, we provision a Linux [compute cluster](./how-to-create-attach-compute-cluster.md?tabs=python). You can see the [Azure Machine Learning pricing](https://azure.microsoft.com/pricing/details/machine-learning/) page for the full list of VM sizes and prices. Since we need a GPU cluster for this example, let's pick a `STANDARD_NC6` model and create an Azure Machine Learning compute.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=gpu_compute_target)]

### Create a job environment

To run an Azure Machine Learning job, you need an environment. An Azure Machine Learning [environment](concept-environments.md) encapsulates the dependencies (such as software runtime and libraries) needed to run your machine learning training script on your compute resource. This environment is similar to a Python environment on your local machine.

Azure Machine Learning allows you to either use a curated (or ready-made) environment or create a custom environment using a Docker image or a Conda configuration. In this article, you reuse the curated Azure Machine Learning environment `AzureML-acpt-pytorch-2.2-cuda12.1`. Use the latest version of this environment using the `@latest` directive.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=curated_env_name)]

## Configure and submit your training job

In this section, we begin by introducing the data for training. We then cover how to run a training job, using a training script that we've provided. You learn to build the training job by configuring the command for running the training script. Then, you submit the training job to run in Azure Machine Learning.

### Obtain the training data

You can use the dataset in this [zipped file](https://azuremlexamples.blob.core.windows.net/datasets/fowl_data.zip). This dataset consists of about 120 training images each for two classes (turkeys and chickens), with 100 validation images for each class. The images are a subset of the [Open Images v5 Dataset](https://storage.googleapis.com/openimages/web/index.html). The training script *pytorch_train.py* downloads and extracts the dataset.

### Prepare the training script

In the prerequisites section, we provided the training script *pytorch_train.py*. In practice, you should be able to take any custom training script *as is* and run it with Azure Machine Learning without having to modify your code.

The provided training script downloads the data, trains a model, and registers the model.

### Build the training job

Now that you have all the assets required to run your job, it's time to build it using the Azure Machine Learning Python SDK v2. For this example, we create a `command`.

An Azure Machine Learning `command` is a resource that specifies all the details needed to execute your training code in the cloud. These details include the inputs and outputs, type of hardware to use, software to install, and how to run your code. The `command` contains information to execute a single command.

#### Configure the command

You use the general purpose `command` to run the training script and perform your desired tasks. Create a `command` object to specify the configuration details of your training job.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=job)]

- The inputs for this command include the number of epochs, learning rate, momentum, and output directory.
- For the parameter values:
    1. Provide the compute cluster `gpu_compute_target = "gpu-cluster"` that you created for running this command.
    1. Provide the curated environment that you initialized earlier.
    1. If you're not using the completed notebook in the Samples folder, specify the location of the *pytorch_train.py* file.
    1. Configure the command line action itself—in this case, the command is `python pytorch_train.py`. You can access the inputs and outputs in the command via the `${{ ... }}` notation.
    1. Configure metadata such as the display name and experiment name, where an experiment is a container for all the iterations one does on a certain project. All the jobs submitted under the same experiment name would be listed next to each other in Azure Machine Learning studio.

### Submit the job

It's now time to submit the job to run in Azure Machine Learning. This time, you use `create_or_update` on `ml_client.jobs`.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=create_job)]

Once completed, the job registers a model in your workspace (as a result of training) and outputs a link for viewing the job in Azure Machine Learning studio.

> [!WARNING]
> Azure Machine Learning runs training scripts by copying the entire source directory. If you have sensitive data that you don't want to upload, use a [.ignore file](concept-train-machine-learning-model.md#understand-what-happens-when-you-submit-a-training-job) or don't include it in the source directory.

### What happens during job execution

As the job is executed, it goes through the following stages:

- **Preparing**: A docker image is created according to the environment defined. The image is uploaded to the workspace's container registry and cached for later runs. Logs are also streamed to the job history and can be viewed to monitor progress. If a curated environment is specified, the cached image backing that curated environment is used.

- **Scaling**: The cluster attempts to scale up if it requires more nodes to execute the run than are currently available.

- **Running**: All scripts in the script folder *src* are uploaded to the compute target, data stores are mounted or copied, and the script is executed. Outputs from *stdout* and the *./logs* folder are streamed to the job history and can be used to monitor the job.

## Tune model hyperparameters

You trained the model with one set of parameters, let's now see if you can further improve the accuracy of your model. You can tune and optimize your model's hyperparameters using Azure Machine Learning's [`sweep`](/python/api/azure-ai-ml/azure.ai.ml.sweep) capabilities.

To tune the model's hyperparameters, define the parameter space in which to search during training. You do this by replacing some of the parameters passed to the training job with special inputs from the `azure.ml.sweep` package.

Since the training script uses a learning rate schedule to decay the learning rate every several epochs, you can tune the initial learning rate and the momentum parameters.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=job_for_sweep)]

Then, you can configure sweep on the command job, using some sweep-specific parameters, such as the primary metric to watch and the sampling algorithm to use.

In the following code, we use random sampling to try different configuration sets of hyperparameters in an attempt to maximize our primary metric, `best_val_acc`.

We also define an early termination policy, the `BanditPolicy`, to terminate poorly performing runs early.
The `BanditPolicy` terminates any run that doesn't fall within the slack factor of our primary evaluation metric. You apply this policy every epoch (since we report our `best_val_acc` metric every epoch and `evaluation_interval`=1). Notice we delay the first policy evaluation until after the first 10 epochs (`delay_evaluation`=10).

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=sweep_job)]

Now, you can submit this job as before. This time, you're running a sweep job that sweeps over your train job.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=create_sweep_job)]

You can monitor the job by using the studio user interface link that's presented during the job run.

## Find the best model

Once all the runs complete, you can find the run that produced the model with the highest accuracy.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=model)]

## Deploy the model as an online endpoint

You can now deploy your model as an [online endpoint](concept-endpoints.md)—that is, as a web service in the Azure cloud.

To deploy a machine learning service, you typically need:
- The model assets that you want to deploy. These assets include the model's file and metadata that you already registered in your training job.
- Some code to run as a service. The code executes the model on a given input request (an entry script). This entry script receives data submitted to a deployed web service and passes it to the model. After the model processes the data, the script returns the model's response to the client. The script is specific to your model and must understand the data that the model expects and returns. When you use an MLFlow model, Azure Machine Learning automatically creates this script for you.

For more information about deployment, see [Deploy and score a machine learning model with managed online endpoint using Python SDK v2](how-to-deploy-managed-online-endpoint-sdk-v2.md).

### Create a new online endpoint

As a first step to deploying your model, you need to create your online endpoint. The endpoint name must be unique in the entire Azure region. For this article, you create a unique name using a universally unique identifier (UUID).

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=online_endpoint_name)]

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=endpoint)]

After you create the endpoint, you can retrieve it as follows:

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=get_endpoint)]

### Deploy the model to the endpoint

You can now deploy the model with the entry script. An endpoint can have multiple deployments. Using rules, the endpoint can then direct traffic to these deployments.

In the following code, you'll create a single deployment that handles 100% of the incoming traffic. We specified an arbitrary color name *aci-blue* for the deployment. You could also use any other name such as *aci-green* or *aci-red* for the deployment.

The code to deploy the model to the endpoint:

- Deploys the best version of the model that you registered earlier.
- Scores the model, using the *score.py* file.
- Uses the curated environment (that you specified earlier) to perform inferencing.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=blue_deployment)]

> [!NOTE]
> Expect this deployment to take a bit of time to finish.

### Test the deployed model

Now that you deployed the model to the endpoint, you can predict the output of the deployed model, using the `invoke` method on the endpoint.

To test the endpoint, let's use a sample image for prediction. First, let's display the image.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=display_image)]

Create a function to format and resize the image.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=process_image)]

Format the image and convert it to a JSON file.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=test_json)]

You can then invoke the endpoint with this JSON and print the result.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=test_deployment)]

### Clean up resources

If you don't need the endpoint anymore, delete it to stop using resource. Make sure no other deployments are using the endpoint before you delete it.

[!Notebook-python[](~/azureml-examples-main/sdk/python/jobs/single-step/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb?name=delete_endpoint)]

> [!NOTE]
> Expect this cleanup to take a bit of time to finish.

## Related content

In this article, you trained and registered a deep learning neural network using PyTorch on Azure Machine Learning. You also deployed the model to an online endpoint. See these other articles to learn more about Azure Machine Learning.

- [Track run metrics during training](how-to-log-view-metrics.md)
- [Tune hyperparameters](how-to-tune-hyperparameters.md)
- [Reference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

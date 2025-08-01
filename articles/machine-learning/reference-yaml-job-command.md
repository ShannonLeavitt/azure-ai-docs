---
title: 'CLI (v2) command job YAML schema'
titleSuffix: Azure Machine Learning
description: Reference documentation for the CLI (v2) command job YAML schema.
services: machine-learning
ms.service: azure-machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: cliv2, devx-track-python, update-code2
author: s-polly
ms.author: scottpolly
ms.date: 04/14/2025
ms.reviewer: balapv
---

# CLI (v2) command job YAML schema

[!INCLUDE [cli v2](includes/machine-learning-cli-v2.md)]

The source JSON schema can be found at https://azuremlschemas.azureedge.net/latest/commandJob.schema.json.



[!INCLUDE [schema note](includes/machine-learning-preview-old-json-schema-note.md)]

## YAML syntax

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | string | The YAML schema. If you use the Azure Machine Learning VS Code extension to author the YAML file, including `$schema` at the top of your file enables you to invoke schema and resource completions. | | |
| `type` | const | The type of job. | `command` | `command` |
| `name` | string | Name of the job. Must be unique across all jobs in the workspace. If omitted, Azure Machine Learning autogenerates a GUID for the name. | | |
| `display_name` | string | Display name of the job in the studio UI. Can be nonunique within the workspace. If omitted, Azure Machine Learning autogenerates a human-readable adjective-noun identifier for the display name. | | |
| `experiment_name` | string | Experiment name to organize the job under. Each job's run record is organized under the corresponding experiment in the studio's "Experiments" tab. If omitted, Azure Machine Learning defaults it to the name of the working directory where the job was created. | | |
| `description` | string | Description of the job. | | |
| `tags` | object | Dictionary of tags for the job. | | |
| `command` | string | The command to execute. | | |
| `code` | string | Local path to the source code directory to be uploaded and used for the job. | | |
| `environment` | string or object | The environment to use for the job. Can be either a reference to an existing versioned environment in the workspace or an inline environment specification. <br><br> To reference an existing environment, use the `azureml:<environment_name>:<environment_version>` syntax or `azureml:<environment_name>@latest` (to reference the latest version of an environment). <br><br> To define an environment inline, follow the [Environment schema](reference-yaml-environment.md#yaml-syntax). Exclude the `name` and `version` properties as they aren't supported for inline environments.<br><br> When you work with curated environments in the CLI or SDK, curated environment names begin with `AzureML-`. When you use the Azure Machine Learning studio, the curated environment names don't have this prefix. The reason for this difference is that the studio UI displays curated and custom environments on separate tabs, so the prefix isn't necessary. The CLI and SDK don't have this separation, so the prefix is used to differentiate between curated and custom environments. | | |
| `environment_variables` | object | Dictionary of environment variable key-value pairs to set on the process where the command is executed. | | |
| `distribution` | object | The distribution configuration for distributed training scenarios. One of [MpiConfiguration](#mpiconfiguration), [PyTorchConfiguration](#pytorchconfiguration), or [TensorFlowConfiguration](#tensorflowconfiguration). | | |
| `compute` | string | Name of the compute target to execute the job on. Can be either a reference to an existing compute in the workspace (using the `azureml:<compute_name>` syntax) or `local` to designate local execution. **Note:** jobs in pipeline didn't support `local` as `compute` | | `local` |
| `resources.instance_count` | integer | The number of nodes to use for the job. | | `1` |
| `resources.instance_type` | string | The instance type to use for the job. Applicable for jobs running on Azure Arc-enabled Kubernetes compute (where the compute target specified in the `compute` field is of `type: kubernentes`). If omitted, defaults to the default instance type for the Kubernetes cluster. For more information, see [Create and select Kubernetes instance types](how-to-attach-kubernetes-anywhere.md). | | |
| `resources.shm_size` | string | The size of the docker container's shared memory block. Should be in the format of `<number><unit>` where number has to be greater than 0 and the unit can be one of `b` (bytes), `k` (kilobytes), `m` (megabytes), or `g` (gigabytes). | | `2g` |
| `limits.timeout` | integer | The maximum time in seconds the job is allowed to run. When this limit is reached, the system cancels the job. | | |
| `inputs` | object | Dictionary of inputs to the job. The key is a name for the input within the context of the job and the value is the input value. <br><br> Inputs can be referenced in the `command` using the `${{ inputs.<input_name> }}` expression. | | |
| `inputs.<input_name>` | number, integer, boolean, string, or object | One of a literal value (of type number, integer, boolean, or string) or an object containing a [job input data specification](#job-inputs). | | |
| `outputs` | object | Dictionary of output configurations of the job. The key is a name for the output within the context of the job and the value is the output configuration. <br><br> Outputs can be referenced in the `command` using the `${{ outputs.<output_name> }}` expression. | |
| `outputs.<output_name>` | object | You can leave the object empty, in which case by default the output is of type `uri_folder` and Azure Machine Learning generates an output location for the output. Files to the output directory are written via read-write mount. If you want to specify a different mode for the output, provide an object containing the [job output specification](#job-outputs). | |
| `identity` | object | The identity is used for data accessing. It can be [UserIdentityConfiguration](#useridentityconfiguration), [ManagedIdentityConfiguration](#managedidentityconfiguration), or None. If UserIdentityConfiguration, the identity of job submitter is used to access, input data and write result to output folder, otherwise, the managed identity of the compute target is used. | |

### Distribution configurations

#### MpiConfiguration

| Key | Type | Description | Allowed values |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Required.** Distribution type.  | `mpi` |
| `process_count_per_instance` | integer | **Required.** The number of processes per node to launch for the job.  | |

#### PyTorchConfiguration

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Required.** Distribution type.  | `pytorch` | |
| `process_count_per_instance` | integer | The number of processes per node to launch for the job. | |  `1` |

#### TensorFlowConfiguration

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | const | **Required.** Distribution type.  | `tensorflow` |
| `worker_count` | integer | The number of workers to launch for the job. | | Defaults to `resources.instance_count`. |
| `parameter_server_count` | integer | The number of parameter servers to launch for the job. | | `0` |

### Job inputs

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | The type of job input. Specify `uri_file` for input data that points to a single file source, or `uri_folder` for input data that points to a folder source. | `uri_file`, `uri_folder`, `mlflow_model`, `custom_model`| `uri_folder` |
| `path` | string | The path to the data to use as input. Can be specified in a few ways: <br><br> - A local path to the data source file or folder, for example, `path: ./iris.csv`. The data gets uploaded during job submission. <br><br> - A URI of a cloud path to the file or folder to use as the input. Supported URI types are `azureml`, `https`, `wasbs`, `abfss`, `adl`. See [Core yaml syntax](reference-yaml-core-syntax.md) for more information on how to use the `azureml://` URI format. <br><br> - An existing registered Azure Machine Learning data asset to use as the input. To reference a registered data asset, use the `azureml:<data_name>:<data_version>` syntax or `azureml:<data_name>@latest` (to reference the latest version of that data asset), for example, `path: azureml:cifar10-data:1` or `path: azureml:cifar10-data@latest`. | | |
| `mode` | string | Mode of how the data should be delivered to the compute target. <br><br> For read-only mount (`ro_mount`), the data is consumed as a mount path. A folder is mounted as a folder and a file is mounted as a file. Azure Machine Learning resolves the input to the mount path. <br><br> For `download` mode, the data is downloaded to the compute target. Azure Machine Learning resolves the input to the downloaded path. <br><br> If you only want the URL of the storage location of the data artifacts rather than mounting or downloading the data itself, you can use the `direct` mode. This mode passes in the URL of the storage location as the job input. In this case, you're fully responsible for handling credentials to access the storage. <br><br> The `eval_mount` and `eval_download` modes are unique to MLTable, and either mounts the data as a path or downloads the data to the compute target. <br><br> For more information on modes, see [Access data in a job](how-to-read-write-data-v2.md?tabs=cli#modes) | `ro_mount`, `download`, `direct`, `eval_download`, `eval_mount` | `ro_mount` |

### Job outputs

| Key | Type | Description | Allowed values | Default value |
| --- | ---- | ----------- | -------------- | ------------- |
| `type` | string | The type of job output. For the default `uri_folder` type, the output corresponds to a folder. | `uri_folder` , `mlflow_model`, `custom_model`| `uri_folder` |
| `mode` | string | Mode of how output files get delivered to the destination storage. For read-write mount mode (`rw_mount`), the output directory is a mounted directory. For upload mode, the files written get uploaded at the end of the job. | `rw_mount`, `upload` | `rw_mount` |

### Identity configurations

#### UserIdentityConfiguration

| Key | Type | Description | Allowed values |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Required.** Identity type.  | `user_identity` |

#### ManagedIdentityConfiguration

| Key | Type | Description | Allowed values |
| --- | ---- | ----------- | -------------- |
| `type` | const | **Required.** Identity type.  | `managed` or `managed_identity` |

## Remarks

The `az ml job` command can be used for managing Azure Machine Learning jobs.

## Examples

Examples are available in the [examples GitHub repository](https://github.com/Azure/azureml-examples/tree/main/cli/jobs). The following sections show some of the examples.

## YAML: hello world

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world.yml":::

## YAML: display name, experiment name, description, and tags

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-org.yml":::

## YAML: environment variables

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-env-var.yml":::

## YAML: source code

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-code.yml":::

## YAML: literal inputs

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-input.yml":::

## YAML: write to default outputs

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output.yml":::

## YAML: write to named data output

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-world-output-data.yml":::

## YAML: datastore URI file input

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-file.yml":::

## YAML: datastore URI folder input

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-datastore-folder.yml":::

## YAML: URI file input

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-file.yml":::

## YAML: URI folder input

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-iris-folder.yml":::

## YAML: Notebook via papermill

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/basics/hello-notebook.yml":::

## YAML: basic Python model training

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/scikit-learn/iris/job.yml":::

## YAML: basic R model training with local Docker build context

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/r/iris/job.yml":::

## YAML: distributed PyTorch

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/pytorch/cifar-distributed/job.yml":::

## YAML: distributed TensorFlow

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/single-step/tensorflow/mnist-distributed/job.yml":::

## Next steps

- [Install and use the CLI (v2)](how-to-configure-cli.md)

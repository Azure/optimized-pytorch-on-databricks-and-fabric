# PyTorch model training with *Fully Sharded Data Parallel* on Azure Databricks and inference with *BetterTransformer* on Microsoft Fabric

#### This is the repository for the blog post [Training and Inference of LLMs with PyTorch Fully Sharded Data Parallel and Better Transformer](https://techcommunity.microsoft.com/t5/ai-customer-engineering-team/training-and-inference-of-llms-with-pytorch-fully-sharded-data/ba-p/3845995)

#### This repository contains sample code showing how to perform optimized model training with PyTorch [Fully Sharded Data Parallel]( https://pytorch.org/tutorials/intermediate/FSDP_tutorial.html#getting-started-with-fully-sharded-data-parallel-fsdp) training, and how to perform optimized model inference using PyTorch [BetterTransformer]( https://pytorch.org/blog/a-better-transformer-for-fast-transformer-encoder-inference/) through an [integration with Hugging Face Optimum](https://huggingface.co/docs/optimum/bettertransformer/tutorials/convert).

#### Data preparation and model inference are performed on [Microsoft Fabric]( https://learn.microsoft.com/en-us/fabric/get-started/microsoft-fabric-overview). Model training is performed on [Azure Databricks]( https://learn.microsoft.com/en-us/azure/databricks/introduction/).

#### To reproduce the notebooks, you will need to setup the following infrastructure:

1. Microsoft Fabric Workspace and Lakehouse:
- You need access to a Microsoft Fabric environment, which can be done through a [Fabric trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial), or a [paid Fabric subscription](https://learn.microsoft.com/en-us/fabric/enterprise/buy-subscription).
- Then you need access to a Fabric Workspace to upload and run the Notebooks and a Fabric Lakehouse to store data. We recommend that you have a dedicated [Workspace](https://learn.microsoft.com/en-us/fabric/get-started/create-workspaces) and a dedicated [Lakehouse](https://learn.microsoft.com/en-us/fabric/data-engineering/create-lakehouse) to run the notebooks. You can use the default configurations when creating the Workspace and Lakehouse.
- You also need to [install PyPI libraries to your Workspace](https://learn.microsoft.com/en-us/fabric/data-science/python-guide/python-library-management#install-workspace-libraries): *torch* version 2.0.1, *transformers* version 4.29.2, and *optimum* version 1.8.6.

2. [Azure Databricks](https://learn.microsoft.com/en-us/azure/databricks/introduction/):
- You need access to an [Azure Databricks Workspace](https://docs.microsoft.com/en-us/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal#create-an-azure-databricks-workspace). For the Pricing Tier, it should be of type *Premium* to have seamless integration with the Microsoft Fabric Lakehouse.
- [Create a Cluster](https://docs.microsoft.com/en-us/azure/databricks/clusters/create). Your cluster configuration should be based on nodes of type Standard_NC4as_T4_v3. Please make sure you have enough CPU cores of that type, otherwise work with your Azure subscription administrator to request a quota increase. Use the information below when creating your cluster:
- Databricks runtime version should be at least 13.0 ML (GPU, Scala 2.12, Spark 3.4.0)
- worker type should be Standard_NC4as_T4_v3 and number of workers should be at least 2 (the notebooks here were run with 8 worker nodes)
- Driver type should be the same as worker type
- Disable autoscaling
- Install a [cluster-scoped init script](https://docs.microsoft.com/en-us/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts)  in your cluster. The script to be installed is the [env_update.sh](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Azure%20Databricks/env_update.sh).

#### The notebooks provided here are the following and should be run in the following order:
1. [data_preparation.ipynb](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Microsoft%20Fabric/data_preparation.ipynb): it downloads and prepares the datasets needed for model training and inference. This notebook runs on Microsoft Fabric.
2. [model_training_ddp.ipynb](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Azure%20Databricks/model_training_ddp.ipynb): it performs distributed fine tuning on the pre-trained *Hugging Face* model using *PyTorch DDP* and *TorchDistributor* on *Spark*. This notebook runs on Azure Databricks.
3. [model_training_fsdp.ipynb](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Azure%20Databricks/model_training_fsdp.ipynb): it performs distributed fine tuning on the pre-trained *Hugging Face* model using *PyTorch FSDP* and *TorchDistributor* on *Spark*. This notebook runs on Azure Databricks.
4. [model_inference.ipynb](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Microsoft%20Fabric/model_inference.ipynb): it performs distributed model inference using *Pandas UDF* on *Spark*. This notebook runs on Microsoft Fabric.
5. [model_inference_optim.ipynb](https://github.com/Azure/optimized-pytorch-on-databricks-and-fabric/blob/main/Microsoft%20Fabric/model_inference_optim.ipynb): it performs distributed model inference using *Pandas UDF* on *Spark*, and the model is optimized with *BetterTransformer*. This notebook runs on Microsoft Fabric.

#### After running the notebooks, if running on the same configuration as described above, you should see a 3.5X improvement in model training time, on GPU, and a 6X improvement in model inference time, on CPU.

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft 
trademarks or logos is subject to and must follow 
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.

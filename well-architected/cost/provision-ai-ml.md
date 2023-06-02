---
title: Cost for AI + Machine Learning services
description: Learn how to develop cost estimates and strategies for serverless technologies by using Azure Machine Learning, Azure Cognitive Services, or Azure Bot Service.
author: PageWriter-MSFT
ms.author: martinek
ms.date: 04/19/2023
ms.topic: conceptual
ms.custom:
  - article
  - internal-intro
categories:
  - ai-machine-learning
---

# AI + Machine Learning cost estimates

Compute cost is the main cost driver for machine learning workloads. Those resources are needed to run the training model and host the deployment. For information about choosing a compute target, see [What are compute targets in Azure Machine Learning?](/azure/machine-learning/concept-compute-target)

The compute cost depends on cluster size, node type, and the number of nodes. Billing occurs while the cluster nodes are starting, running, or shutting down.

With services such as [Azure Machine Learning](/azure/machine-learning/overview-what-is-azure-machine-learning), you can create fixed-sized clusters or use autoscaling.
> ![Task](./images/i-best-practices.png) If the amount of compute isn't known, start with a zero-node cluster. The cluster scales up when it detects jobs in the queue. There is no charge for a zero-node cluster.

Fix-sized clusters are appropriate for jobs that run at a constant rate and the amount of compute is known and measured beforehand. The time taken to spin up or down a cluster incurs higher cost.
> ![Task](./images/i-best-practices.png) If you don't need retraining frequently, turn off the cluster when not in use.

To lower the cost for experimental or development workloads, choose Spot VMs. We don't recommend Spot VMs for production workloads because Azure might evict them at any time. For more information, see [Use Azure Spot Virtual Machines](/azure/virtual-machines/windows/spot-vms).

For more information about the services that make up a machine learning workload, see [Compare Microsoft machine learning products and technologies](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).

This article provides cost considerations for some technology choices. This list isn't meant to be exhaustive.

## Azure Machine Learning

Training models don't incur the machine learning service surcharge. You're charged for the following factors:

- Compute choices, such as the virtual machine sizes and the region in which they're available, drive costs. If you can commit to one or three years, choosing reserved instances or savings plans can lower cost. For more information, see [Reserved VMs](./optimize-vm.md#reserved-vms) and [Savings plans](./optimize-vm.md#savings-plans).

- As part of provisioning Machine Learning resources, resources are deployed such as [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), [Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/), and [Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). You incur charges according to the pricing of those individual services.

- If you deploy models to a Kubernetes Service cluster, [Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning-service/) adds a surcharge on top of the Kubernetes Service compute cost. This cost can be lowered through autoscaling.

For more information, see [Pricing calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)

### Reference architecture

- [Distributed hyperparameter tuning for machine learning models](/azure/architecture/reference-architectures/ai/training-python-models)
- [Distributed training of deep learning models on Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
- [Batch scoring of Python models on Azure](/azure/architecture/reference-architectures/ai/batch-scoring-python)
- [Batch scoring for deep learning models using Azure Machine Learning pipelines](/azure/architecture/reference-architectures/ai/batch-scoring-deep-learning)
- [Real-time scoring of machine learning models in Python](/azure/architecture/reference-architectures/ai/real-time-scoring-machine-learning-models)
- [MLOps for Python models using Azure Machine Learning](/azure/architecture/reference-architectures/ai/mlops-python)
- [Batch scoring with R models to forecast sales](/azure/architecture/reference-architectures/ai/batch-scoring-r-models)
- [Real-time scoring of R machine learning models](/azure/architecture/reference-architectures/ai/realtime-scoring-r)
- [Batch scoring of Spark models on Azure Databricks](/azure/architecture/reference-architectures/ai/batch-scoring-databricks)
- [Build an enterprise-grade conversational bot](/azure/architecture/reference-architectures/ai/conversational-bot)
- [Build a real-time recommendation API on Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)

## Azure Cognitive Services

The billing depends on the type of service. The charges are based on the number of transactions for each type of operation specific to a service. A certain number of transactions are free. If you need more transactions, choose from the *Standard* instances. For more information, see:

- [Pricing calculator](https://azure.microsoft.com/pricing/calculator/)
- [Azure Cognitive Services pricing](https://azure.microsoft.com/pricing/details/cognitive-services/)

## Azure Bot Service

The Azure Bot Service is a managed service purpose-built for enterprise-grade bot development. Billing is based on the number of messages. A certain number of messages are free. If you need to create custom channels, choose *Premium channels*, which can drive up the cost of the workload.

For a Web App Bot, an [App Service](https://azure.microsoft.com/pricing/details/app-service/) is provisioned to host the bot. Also, an instance of [Azure Monitor](https://azure.microsoft.com/pricing/details/application-insights/) is provisioned. You're charged per the pricing of those individual services.

## Reference architecture for Azure Cognitive Services and Azure Bot Service

- [Enterprise-grade conversational bot](/azure/architecture/reference-architectures/ai/conversational-bot)

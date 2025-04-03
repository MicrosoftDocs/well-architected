---
title: Sustainable design for AI workloads on Azure
description: The article focuses on how to build AI workloads sustainably and aims to provide specific guidance to the reader on various measures that can be taken during the  software development phases.
author: srini1978
ms.author: srrakhun
ms.reviewer: tozimmergren
ms.topic: best-practice
ms.date: 03/24/2025
---

# Sustainable AI Design

Designing, building, and operationalizing [AI workloads](/azure/well-architected/ai/) **sustainably** is crucial as AI workloads might consume significant energy and carbon throughout their lifecycle. To ensure the business value derived from AI doesn't compromise environmental balance, it's essential to adopt sustainable practices.

The AI workload lifecycle consists of three primary steps: Model design, data design, and operations. In this article, we look at the different design aspects that can be considered for factoring in sustainable design principles.

To learn more about the overall AI workload lifecycle, see [MLOps and GenAIOps for AI workloads on Azure](/azure/well-architected/ai/mlops-genaiops).

- **Model design** involves choosing the appropriate model for the business problem that needs to be solved. This choice is dependent on whether we need to solve a prediction scenario or a classification scenario or a content generation scenario.
- **Data design** activities focus on choosing the right storage and data processing infrastructure that will help support the training and operations of the model. Model and data design activities together are considered as part of the training phase.
- **Operations design** phase or the _inference phase_ focuses on deploying the model and running it at scale.

## Model design recommendations

Model design is the first step in the lifecycle of AI workload where the right model is chosen for solving the business problem. A model is a pretrained package that helps in machine learning problems of the world like classification, prediction, and forecasting.

There are multiple decision points during the model design phase, including:

- **Model size**: Opt for Small Language Models (SLMs) over Large Language Models (LLMs) to reduce energy consumption.
  By reducing the size of AI models, developers can decrease the computational resources needed to process them, thus cutting down the overall energy expenditure. This approach not only improves model efficiency but also allows AI to be deployed on resource-constrained devices.
  > ![Tradeoff icon](../_images/trade-off.svg) _Tradeoff_:  SLMs may not be suitable for handling complex tasks, maintaining context and could impact accuracy. Ensure that the right tradeoff is considered based on business needs. A brief comparison is listed [here](https://www.microsoft.com/microsoft-cloud/blog/2024/11/11/explore-ai-models-key-differences-between-small-language-models-and-large-language-models/)

- **Model reusability**: Utilize pretrained models to save energy cycles.
  Using a pretrained model saves energy cycles since we don’t have to plan for data ingestion, data cleanup, training, pretraining, and fine tuning as this is all done and available out of the box for us to use and hence is very much a sustainable alternative.

- **Energy-efficient frameworks**: Choose frameworks like TensorFlow and PyTorch for their energy efficiency. Listed [here](https://www.restack.io/p/energy-efficient-ai-answer-frameworks-cat-ai) are some frameworks.

- **Fine-tuning**: Pretrain models on general datasets before fine-tuning on specific domains to reduce computational cycles.

- **Hosting provider region**: Model training is energy intensive. It's important that the right region is chosen. Select [green regions](https://learn.microsoft.com/en-us/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) powered by renewable energy sources for model training.

## Data design recommendations

The data design phase involves data identification, classification, ingestion, storage, and computation. Each of these process steps requires compute, network, and storage resources. By making conscious decisions around the required infrastructure that needs to be used, you can improve sustainability in data processing.

There are multiple decision points during the data design phase, including:

- **Data processing**: Reuse publicly available datasets and architect data ingestion to pull only relevant data.
  > ![Risk icon](../_images/risk.svg) **Risk**: Ensure that licensing considerations are checked for public datasets and whether they have the right open source approvals.
- **Storage**: Store data in regions powered by renewable energy and consider edge computing.
- **Clean data**: Ensure high-quality data for training to avoid wasted cycles.
- **Move closer to the edge**: You might consider moving data processing, ingestion, computation, and storage to the edge.

## Operational design recommendations

- **Hosting provider region**: Deploy models in [green regions](https://learn.microsoft.com/en-us/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) using renewable energy sources.

- **Retraining frequency**: Retrain-orchestration involves automating the retraining models based on new data.
  This ensures that the model stays up-to-date and maintains its performance over time. Retraining frequently isn't sustainable. Balance model accuracy with energy consumption by setting thresholds for retraining.
  > ![Tradeoff icon](../_images/trade-off.svg) _Tradeoff_: Reducing the frequency of retraining could impact accuracy. Ensure that the right tradeoff is considered based on business needs.

- **Monitoring**: Track model performance to prevent machine learning ops cycles from being wasted due to model decay. When model decay occurs, machine learning ops cycles are wasted since the AI workload isn't predicting correct outcomes, but it continues to run. Monitoring ensures that the correct actions are taken to enable the AI workload to operate sustainably.

Implement these sustainable practices to allow your AI developers and data scientists to significantly reduce the environmental impact of AI workloads while maintaining business value.

## Next steps

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)

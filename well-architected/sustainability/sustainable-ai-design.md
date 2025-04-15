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

Designing, building, and operationalizing [AI workloads](/azure/well-architected/ai/) **sustainably** is crucial as they consume significant energy and carbon throughout their lifetime. To ensure the business value derived from AI doesn't compromise environmental balance, it's essential to adopt sustainable practices.

Designing AI workloads consists of three primary steps: Model design, data design, and operations. In this article, we look at different  aspects that can be considered for factoring in sustainable design principles in these steps.


- **Model design** involves choosing the appropriate model for the business problem that needs to be solved. This choice is dependent on whether we need to solve a prediction scenario or a classification scenario or a content generation scenario.
- **Data design** activities focus on choosing the right storage and data processing infrastructure that will help support the training and operations of the model. Model and data design activities together are considered as part of the training phase.
- **Operations design** phase or the _inference phase_ focuses on deploying the model and running it at scale.

## Model design recommendations

Model design is the first step of AI workload where the right model is chosen for solving the business problem. A model is a pretrained package that helps in machine learning problems of the world like classification, prediction, and forecasting.

There are multiple decision points during the model design phase and we will look at some of these considerations from a sustainability perspective :

- **Model size**: One of the initial considerations is model size. Choose Small Language Models (SLMs) over Large Language Models (LLMs) where applicable to reduce energy consumption.
  By reducing the size of AI models, developers can decrease the computational resources needed to process them, cutting down the overall energy consumption. This approach not only improves model efficiency but also allows AI to be deployed on resource-constrained devices.
  > ![Tradeoff icon](../_images/trade-off.svg) _Tradeoff_:  SLMs may not be suitable for handling complex tasks or for maintaining context, and they might impact accuracy. Ensure that the right tradeoff is considered based on business needs. To learn more about where to use SLM vs LLM, see [key differences between small language models and large language models](https://www.microsoft.com/microsoft-cloud/blog/2024/11/11/explore-ai-models-key-differences-between-small-language-models-and-large-language-models/).

- **Model reusability**: Utilize pretrained models to save energy cycles.
  Using a pretrained model saves energy cycles since we don’t have to plan for data ingestion, data cleanup, training, pretraining, and fine tuning as this is all done and available out of the box for us to use and hence is very much a sustainable alternative.

- **Energy-efficient frameworks**: Choose frameworks like TensorFlow and PyTorch for their energy efficiency. They leverage techniques like quantization and pruning that help in reducing energy consumption. To learn more about quantization or pruning, see [Advances to low-bit quantization enable LLMs on edge devices](https://www.microsoft.com/en-us/research/blog/advances-to-low-bit-quantization-enable-llms-on-edge-devices/)

- **Fine-tuning**: Pretrain models on general datasets before fine-tuning on specific domains to reduce computational cycles. Fine-tuning requires significantly less time compared to training a model from scratch, which translates to lower energy consumption. Also fine-tuned models often achieve high accuracy with fewer iterations, leading to more efficient inference and lower energy usage during deployment.

- **Hosting provider region**: Model training is energy intensive. It's important that the right region is chosen. Select [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) powered by renewable energy sources for model training.

## Data design recommendations

The data design phase involves data identification, classification, ingestion, storage, and computation. Each of these process steps requires compute, network, and storage resources. By making conscious decisions around the required infrastructure that needs to be used, you can improve sustainability in data processing.

There are multiple decision points during the data design phase, including:

- **Data processing**: Reuse publicly available datasets and architect data ingestion to pull only relevant data. For example, by using optimized data ingestion methods, such as batch processing and streamlining data pipelines, the computational resources required for data handling are minimized, leading to lower energy consumption. For more considerations around data processing and storage, see [sustainable storage](/azure/well-architected/sustainability/sustainability-storage)  
  > ![Risk icon](../_images/risk.svg) **Risk**: Ensure that licensing considerations are checked for public datasets and whether they have the right open source approvals.
- **Clean data**: Ensure high-quality data for training to avoid wasted cycles. Clean data reduces the computational power required for [data preparation](/azure/well-architected/ai/grounding-data-design),cleaning and preprocessing, leading to lower energy consumption. Having clean data also minimizes the storage requirements by eliminating redundant and irrelevant data, which in turn reduces the energy needed for data storage.
- **Move closer to the edge**: You might consider moving data processing, ingestion, computation, and storage to the edge. [Edge computing](https://www.microsoft.com/research/articles/improve-edge-device-ai-efficiency/) enables real-time data processing, which reduces both latency and energy required for continuous data communication between devices and central servers. Edge devices can also be optimized to run AI models efficiently using less power compared to traditional cloud-based processing.

## Operational design recommendations

- **Hosting provider region**: Deploy models in [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) using renewable energy sources.

- **Retraining frequency**: Retrain-orchestration involves automating the retraining models based on new data.
  This ensures that the model stays up-to-date and maintains its performance over time. Retraining frequently isn't sustainable.  
    An example where frequent retraining is not required is an image classification or speech processing scenario. Models for recognizing speech patterns in specific languages or dialects can remain effective over extended periods without frequent retraining. Therefore, AI models can maintain their effectiveness over time without the need for constant updates. Balance model accuracy with energy consumption by setting thresholds for retraining.
  > ![Tradeoff icon](../_images/trade-off.svg) _Tradeoff_: Reducing the frequency of retraining could impact accuracy. For example, models that are used in financial markets need to be highly dynamic, and they need to be frequently retrained to adapt to new market conditions and data. Ensure that the right tradeoff is considered based on business needs. As a proactive measure, use automated processes for continuous monitoring, and evaluate and retrain to maintain accuracy and relevance. Additionally, you need to monitor infrastructure and workload metrics to help ensure optimal performance and reliability. For more information, see [GenAIOps monitoring tools](/azure/well-architected/ai/mlops-genaiops#monitoring)

- **Monitoring**: Monitoring strategy is leveraged to track data drift and performance. Track model performance to prevent machine learning ops cycles from being wasted due to [model decay](/azure/well-architected/ai/test#prevent-model-decay). When model decay occurs, machine learning ops cycles are wasted since the AI workload isn't predicting correct outcomes, but it continues to run. Monitoring ensures that the correct actions are taken to enable the AI workload to operate sustainably.

Implement these sustainable practices to allow your AI developers and data scientists to significantly reduce the environmental impact of AI workloads while maintaining business value.  

## Next steps

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)

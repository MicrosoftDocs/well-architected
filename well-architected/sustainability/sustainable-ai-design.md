---
author: srini1978
ms.author: srrakhun
title: Sustainable design for AI workloads on Azure
ms.topic: best-practice
ms.date: 02/19/2025
description: The article focuses on how to build AI workloads sustainably and aims to  provide specific guidances to the reader on various measures that can be taken during the  software development phases.  
---

# Sustainable AI Design

Designing, building, and operationalizing [AI workloads](https://learn.microsoft.com/en-us/azure/well-architected/ai/) **sustainably** is crucial as AI workloads consume significant energy and carbon throughout their lifecycle. To ensure the business value derived from AI doesn't compromise environmental balance, it's essential to adopt sustainable practices.


## [AI workload design lifecycle](https://learn.microsoft.com/azure/well-architected/ai/mlops-genaiops)

The AI workload lifecycle consists of three primary steps: Model design, data design, and operations. In this article, we look at the different design aspects that can be considered for factoring in sustainable design principles. 


## Design recommendations for sustainable AI workloads

As mentioned above, the lifecycle of building an AI workload has three steps : Model design, data design and operations. 

Model design involves chosing the appropriate model for the business problem that needs to be solved. This choice is dependent on whether we need to solve a prediction scenario or a classification scenario or a content generation scenario. 

Data design activities focus on choosing the right storage and data processing infrastructure that will help support  both the training and operations of the model. Model design and Data design activities together considered as part of training phase. 

Operations design phase or the Inference phase focuses on deploying the model and running it as scale. 

### Model design recommendations

Model design is the first step in the lifecycle of AI workload where the right model is chosen for solving the business problem. A model is a pretrained package that helps in machine learning problems of the world like classification, prediction, forecasting, etc. 
There are multiple decisioning points during the model design phase. Let us look at some considerations for model design below: 

* **Size of Model**: Opt for Small Language Models (SLMs) over Large Language Models (LLMs) to reduce energy consumption. By reducing the size of AI models, developers can decrease the computational resources needed to process them, thus cutting down the overall energy expenditure. This approach not only improves model efficiency but also allows AI to be deployed on resource-constrained devices.
* **Reuse Models**: Utilize pretrained models like ChatGPT, BERT, and GPT-3 to save energy cycles. Using a pretrained model saves energy cycles since we don’t have to plan for Data ingestion, Data cleanup, training, pretraining and fine tuning as this is all done and available out of the box for us to use and hence is very much a sustainable alternative.
*	**Energy-Efficient Frameworks**: Choose frameworks like TensorFlow and PyTorch for their energy efficiency.
* **Fine Tuning**: Pretrain models on general datasets before fine-tuning on specific domains to reduce computational cycles.
* **Hosting Provider and Region**: Model training is energy intensive. It's important that the right region is chosen. Select regions powered by renewable energy sources for model training.

### Data design recommendations
Data design phase is one which involves Data identification, classification, ingestion, storage, and computation. Each of these process steps requires compute, network, and storage resources. By making conscious decisions around the amount and type of infrastructure that needs to be stood up, the data processing phase could be made sustainable. Some of the levers that we can use are:
* **Data Processing**: Reuse publicly available datasets and architect data ingestion to pull only relevant data.
 > ![Risk icon](../_images/risk.svg) **Risk**: Ensure that licensing considerations are checked for public datasets and whether they have the right open source approvals.
* **Storage**: Store data in regions powered by renewable energy and consider edge computing.
* **Clean Data**: Ensure high-quality data for training to avoid wasted cycles.
Another consideration would be to move the whole data processing, ingestion, computation, and storage to the edge.

### Operational design recommendations

* **Hosting Provider and Region**:  Deploy models in regions using renewable energy sources.
* **Frequency of Retraining**: Retrain orchestration involves automating the retraining of models based on new data. This ensures that the model stays up-to-date and maintains its performance over time. Retraining frequently isn't sustainable. Balance model accuracy with energy consumption by setting thresholds for retraining.
 > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Reducing the frequency of retraining could impact accuracy. Ensure that the right tradeoff is considered based on business needs .
*	**Monitoring**: Track model performance to prevent machine learning ops cycles from being wasted due to model decay. When model decay occurs, machine learning ops cycles are wasted since the AI workload isn't predicting correct outcomes, but it continues to run. Monitoring and ensuring that corrective actions are taken ensures that the AI workload operates sustainably. 
By implementing these sustainable practices, AI developers and data scientists can significantly reduce the environmental impact of AI workloads while maintaining business value.

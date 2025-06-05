---
title: Sustainable Design for AI Workloads on Azure
description: The article focuses on how to build AI workloads sustainably and provides specific guidance to the reader about various measures that can be taken during the software development phases.
author: srini1978
ms.author: srrakhun
ms.reviewer: tozimmergren
ms.topic: best-practice
ms.date: 03/24/2025
---

# Sustainable AI Design

Designing, building, and operating [AI workloads](/azure/well-architected/ai/) **sustainably** is crucial because of their significant energy consumption and carbon footprint. Adopt sustainable practices aligned with the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars), and carefully balance environmental considerations with your business requirements.

Designing AI workloads consists of three primary steps: Model design, data design, and operations. In this article, we look at different aspects that can be considered for factoring in sustainable design principles in these steps.

- **Model design** involves selecting the appropriate model for the business problem that needs to be solved. This choice depends on whether you need to solve a prediction scenario, a classification scenario, or a content generation scenario.

- **Data design** activities focus on selecting the right storage and data processing infrastructure to support the training and operations of the model. Model and data design activities together are considered part of the training phase.

- **Operations design** phase, or the *inference phase*, focuses on deploying the model and running it at scale.

## Model design recommendations

Model design is the first step of an AI workload where the right model is chosen for solving the business problem. A model is a pretrained package that helps in machine learning problems such as classification, prediction, and forecasting.

There are multiple decision points during the model design phase, and you will look at some of these considerations from a sustainability perspective:

- **Model size:** Choose small language models over large language models where applicable to reduce energy consumption.

  By reducing the size of AI models, developers can decrease the computational resources needed to process them, cutting down the overall energy consumption. This approach improves model efficiency and allows AI to be deployed on resource-constrained devices.

  > ![Tradeoff icon](../_images/trade-off.svg) *Tradeoff:* Small language models might not be suitable for handling complex tasks or for maintaining context, and they might affect accuracy. Ensure that the right tradeoff is considered based on business needs. To learn more about where to use SLM vs LLM, see [key differences between small language models and large language models](https://www.microsoft.com/microsoft-cloud/blog/2024/11/11/explore-ai-models-key-differences-between-small-language-models-and-large-language-models/).

- **Model reusability:** Utilize pretrained models to save energy cycles while ensuring security and compliance requirements are met.
  Using a pretrained model saves energy cycles since you don't have to plan for data ingestion, data cleanup, training, pretraining, and fine-tuning, as this is all done and available out of the box for you to use and is therefore a sustainable alternative. Verify model provenance and perform security scanning for pretrained models.

- **Energy-efficient frameworks:** Choose frameworks like TensorFlow and PyTorch for their energy efficiency. They use techniques like quantization and pruning that help in reducing energy consumption. To learn more about quantization or pruning, see [Advances to low-bit quantization enable LLMs on edge devices](https://www.microsoft.com/research/blog/advances-to-low-bit-quantization-enable-llms-on-edge-devices/)

- **Fine-tuning:** Pretrain models on general datasets before fine-tuning on specific domains to reduce computational cycles. Fine-tuning requires significantly less time compared to training a model from scratch, which translates to lower energy consumption.

   Fine-tuned models often achieve high accuracy with fewer iterations, leading to more efficient inference and lower energy usage during deployment.

- **Training region:** Model training is energy intensive. It's important that the right region is chosen. Select [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) powered by renewable energy sources for model training.

   Beyond selecting low-carbon regions, implement carbon-aware scheduling that shifts training workloads to times and regions with lower carbon intensity. Integrate with [Azure Carbon Optimization](/azure/carbon-optimization/overview) for real-time carbon intensity data.

- **Caching strategies:** Use model caching and result caching to reduce redundant computations while maintaining response times. 

   Model caching involves storing loaded or initialized models in memory, allowing subsequent requests to reuse the already loaded model. Result caching, stores the outputs of previous computations or predictions. When the system encounters identical or similar inputs again, it can quickly retrieve the cached results instead of recalculating them

## Data design recommendations

The data design phase involves data identification, classification, ingestion, storage, and computation. Each of these process steps requires compute, network, and storage resources. By making conscious decisions around the required infrastructure that needs to be used, you can improve sustainability in data processing.

There are multiple decision points during the data design phase, including:

- **Public datasets:** Reuse publicly available datasets and ingest only relevant data. Using existing datasets reduces redundant data collection and processing, lowering energy consumption and carbon emissions.
  
  Implement data filtering strategies to process only the data required for your specific use case, avoiding unnecessary computational overhead. Consider using data sampling techniques for initial model development before scaling to full datasets.

  > ![Risk icon](../_images/risk.svg) **Risk:** Ensure that licensing considerations are checked for public datasets and whether they have the right open source approvals.

- **Optimized ingestion methods:** Implement optimized data ingestion methods, such as batch processing and streamlined pipelines, to minimize computational overhead and enhance sustainability.

   For more information about data processing and storage, see [Sustainable storage](/azure/well-architected/sustainability/sustainability-storage).  

- **Data quality optimization:** Ensure high-quality data for training to avoid wasted cycles. Clean data reduces the computational power required for [data preparation](/azure/well-architected/ai/grounding-data-design), cleaning and preprocessing, leading to lower energy consumption.

   Having clean data also minimizes the storage requirements by eliminating redundant and irrelevant data, which in turn reduces the energy needed for data storage.

- **Edge computing:** Consider moving data processing, ingestion, computation, and storage to the edge while maintaining security and compliance requirements. [Edge computing](https://www.microsoft.com/research/articles/improve-edge-device-ai-efficiency/) enables real-time data processing, which reduces both latency and energy required for continuous data communication between devices and central servers.

  Edge devices can be optimized to run AI models efficiently by using less power compared to traditional cloud-based processing. Implement edge security controls and monitoring for distributed data processing.

- **Storage requirements optimization:** Implement storage optimization approaches to minimize environmental impact while meeting business requirements. Apply automated data lifecycle management with tiered storage and intelligent archiving to reduce long-term storage needs.

   Use advanced compression algorithms and deduplication techniques to minimize storage footprint and associated energy consumption. Balance retention policies with compliance requirements to avoid unnecessary overhead data storage.

- **Regional data strategy:** Select regions for data storage that optimize both cost and performance while minimizing environmental impact. Store training data in the same region where model training occurs to reduce cross-region data transfer costs and latency.

  Consider regions that have lower storage costs and renewable energy availability when you plan your data infrastructure. Evaluate the total cost, including storage, compute, and data transfer fees when you select regions for both data storage and model training.

  > ![Tradeoff icon](../_images/trade-off.svg) *Tradeoff:* Balance data residency and compliance requirements with low-carbon region deployment opportunities. Some organizations might need to store data in specific regions because of regulatory requirements.

- **Disaster recovery and backup:** Design [disaster recovery](/azure/well-architected/reliability/disaster-recovery) and backup strategies that balance data protection with energy efficiency. Implement intelligent tiering, cross-region replication optimization, and [life cycle management](/azure/well-architected/sustainability/sustainability-storage#implement-lifecycle-management) with deduplication to minimize storage energy consumption while maintaining required recovery objectives.

## Operational design recommendations

The operational design phase includes model deployment, inference optimization, monitoring, maintenance, and scaling. By making informed decisions about the operational infrastructure and processes, you can significantly improve the sustainability of your AI inference workloads.

There are multiple operational design decisions that affect sustainability, and we will examine key considerations for maintaining efficient AI workloads while minimizing environmental impact:

- **Carbon-aware deployment:** Deploy models in [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) using renewable energy sources. Implement dynamic region selection based on real-time carbon intensity data and workload requirements.

- **Retraining frequency:** Retrain orchestration involves automating the retraining of models based on new data while balancing accuracy requirements with environmental impact.

  This approach ensures that the model stays up to date and maintains its performance over time. Retraining frequently isn't sustainable. Consider implementing [model decay detection](/azure/well-architected/ai/test#prevent-model-decay) and threshold-based retraining to minimize unnecessary computational cycles.

    An example where frequent retraining isn't required is an image classification or speech processing scenario. Models for recognizing speech patterns in specific languages or dialects can remain effective over extended periods without frequent retraining. Therefore, AI models can maintain their effectiveness over time without the need for constant updates. Balance model accuracy with energy consumption by setting thresholds for retraining.

  > ![Tradeoff icon](../_images/trade-off.svg) *Tradeoff:* Reducing the frequency of retraining could affect accuracy. For example, models that are used in financial markets need to be highly dynamic, and they need to be frequently retrained to adapt to new market conditions and data. Ensure that the right tradeoff is considered based on business needs. As a proactive measure, use automated processes for continuous monitoring, and evaluate and retrain to maintain accuracy and relevance. Additionally, you need to monitor infrastructure and workload metrics to help ensure optimal performance and reliability. For more information, see [GenAIOps monitoring tools](/azure/well-architected/ai/mlops-genaiops#monitoring)

- **Comprehensive monitoring strategy:** A monitoring strategy is used to track data drift, performance, and sustainability metrics. Track model performance to prevent machine learning ops cycles from being wasted because of [model decay](/azure/well-architected/ai/test#prevent-model-decay). When model decay occurs, machine learning operations cycles are wasted since the AI workload isn't predicting correct outcomes, but it continues to run.

   Integrate sustainability metrics into your AI workload monitoring alongside traditional performance, reliability, and security metrics. See [sustainability operational procedures](/azure/well-architected/sustainability/sustainability-operational-procedures) for implementation guidance.

- **Scaling and workload resiliency:** Implement [automatic scaling strategies](/azure/well-architected/performance-efficiency/optimize-code-infrastructure) that balance resource allocation with environmental impact. Configure scaling policies to consider both workload requirements and regional carbon intensity, scaling up during low-carbon periods when possible.

   Design [redundancy and fault tolerance patterns](/azure/well-architected/reliability/redundancy) that minimize energy overhead while maintaining required availability levels. Use carbon-aware load balancing that distributes traffic across regions based on both performance metrics and real-time carbon intensity data.

- **Sustainable machine learning operations practices:** Integrate sustainability metrics into your [maching learning operations workflows](/azure/well-architected/ai/mlops-genaiops) throughout the life cycle. Implement carbon-aware model training schedules that optimize computational workloads during periods of lower carbon intensity.

   Design deployment strategies such as A/B testing and blue/green deployments to validate the sustainability impact of new workload versions, potentially preventing inefficient workloads from reaching full production scale.

   Monitor sustainability metrics during controlled deployments and implement automated rollback mechanisms when sustainability thresholds are exceeded.

Implement these sustainable practices to allow your AI developers and data scientists to significantly reduce the environmental impact of AI workloads while ensuring alignment with all Well-Architected Framework pillars and making appropriate tradeoffs based on your business requirements.

## Next step

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)

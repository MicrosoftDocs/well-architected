---
title: Sustainable Design for AI Workloads on Azure
description: The article provides guidance about building AI workloads sustainably. It outlines specific measures to implement during software development phases.
author: srini1978
ms.author: srrakhun
ms.reviewer: csiemens
ms.topic: best-practice
ms.date: 03/24/2025
---

# Sustainable AI design for workloads on Azure

Designing, building, and operating [AI workloads](/azure/well-architected/ai/) **sustainably** is crucial because of their significant energy consumption and carbon footprint. Adopt sustainable practices that align with the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars), and carefully balance environmental considerations with your business requirements.

The three key phases of AI workload design are model design, data design, and operations:

- The **model design phase** focuses on selecting the appropriate model for the business problem that needs to be solved. This choice depends on whether you need to solve a prediction scenario, a classification scenario, or a content generation scenario.

- The **data design phase** focuses on selecting the right storage and data processing infrastructure to support the training and operations of the model. Model and data design activities are both part of the training phase.

- **The operations design phase**, or the *inference phase*, focuses on deploying the model and running it at scale.

This article describes how to incorporate sustainable design principles into these phases.
## Model design recommendations

Model design is the first phase of an AI workload where you choose the right model to solve your business problem. A model is a pretrained package that helps you solve machine learning problems such as classification, prediction, and forecasting.

There are multiple decision points during the model design phase. Consider the following points from a sustainability perspective:

- **Model size:** Choose small language models over large language models where applicable to reduce energy consumption.

  Reducing the size of AI models lowers the computational resources required for processing. As a result, overall energy consumption decreases. This approach improves model efficiency and allows AI to be deployed on resource-constrained devices.

  > ![Trade-off icon](../_images/trade-off.svg) *Trade-off:* Small language models might not be suitable for handling complex tasks or for maintaining context. They also might affect accuracy. Ensure that the right trade-off is considered based on business needs. For more information, see [Key differences between small language models and large language models](https://www.microsoft.com/microsoft-cloud/blog/2024/11/11/explore-ai-models-key-differences-between-small-language-models-and-large-language-models/).

- **Model reusability:** Use pretrained models to save energy cycles while ensuring security and compliance requirements are met.

  Using a pretrained model conserves energy cycles by eliminating the need to plan for data ingestion, cleanup, training, pretraining, and fine-tuning. Pretrained models can be used immediately and are a more sustainable option. Verify the model's origin and history to ensure that it meets security and ethical standards, and perform security scanning for pretrained models.

- **Energy-efficient frameworks:** Choose frameworks like TensorFlow and PyTorch for their energy efficiency. These frameworks use techniques like quantization and pruning that help reduce energy consumption. For more information, see [Advances to low-bit quantization enable large language models on edge devices](https://www.microsoft.com/research/blog/advances-to-low-bit-quantization-enable-llms-on-edge-devices/).

- **Fine-tuning:** Pretrain models on general datasets before fine-tuning on specific domains to reduce computational cycles. Fine-tuning requires significantly less time compared to training a model from scratch. This approach results in lower energy consumption.

  Fine-tuned models require fewer iterations to achieve high accuracy, which makes inference more efficient and lowers energy consumption during deployment.

- **Training region:** Model training is energy intensive. It's important that you choose the correct region. Select [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) powered by renewable energy sources for model training.

  Also implement carbon-aware scheduling to shift training workloads to times and regions that have lower carbon intensity. Integrate with [Azure carbon optimization](/azure/carbon-optimization/overview) to access real-time carbon intensity data.

- **Caching strategies:** Use model caching and result caching to reduce redundant computations while maintaining response times.

  Model caching includes storing loaded or initialized models in memory. This storage enables subsequent requests to reuse the already loaded model. Result caching stores the outputs of previous computations or predictions. When the system experiences identical or similar inputs again, it can quickly retrieve the cached results instead of recalculating them.

## Data design recommendations

The data design phase includes data identification, classification, ingestion, storage, and computation. Each of these process steps requires compute, network, and storage resources. By making conscious decisions around the required infrastructure, you can improve sustainability in data processing.

Consider the following factors during the data design phase:

- **Public datasets:** Reuse publicly available datasets and ingest only relevant data. Use existing datasets to reduce redundant data collection and processing. This approach lowers energy consumption and carbon emissions.
  
  Implement data filtering strategies to process only the data required for your specific use case. This approach avoids unnecessary computational overhead. Consider using data sampling techniques for initial model development before you scale to full datasets.

  > ![Risk icon](../_images/risk.svg) **Risk:** Check the licensing of public datasets to ensure that they have the right open-source approvals.

- **Optimized ingestion methods:** Implement optimized data ingestion methods, such as batch processing and streamlined pipelines, to minimize computational overhead and enhance sustainability.

  For more information about data processing and storage, see [Sustainable storage](/azure/well-architected/sustainability/sustainability-storage).  

- **Data quality optimization:** Ensure high-quality data for training to avoid wasted cycles. Clean data reduces the computational power required for [data preparation](/azure/well-architected/ai/grounding-data-design) and cleaning and preprocessing. This approach results in lower energy consumption.

  Clean data minimizes storage needs by removing redundant and irrelevant information, which reduces the energy required for data storage.

- **Edge computing:** Consider moving data processing, ingestion, computation, and storage to the edge while maintaining security and compliance requirements. [Edge computing](https://www.microsoft.com/research/articles/improve-edge-device-ai-efficiency/) enables real-time data processing. This processing reduces latency and the energy required for continuous data communication between devices and central servers.

  Edge devices can be optimized to run AI models efficiently by using less power compared to traditional cloud-based processing. Implement edge security controls and monitoring for distributed data processing.

- **Storage optimization:** Implement storage optimization approaches to minimize environmental impact while meeting business requirements. Apply automated data life cycle management with tiered storage and intelligent archiving to reduce long-term storage needs.

  Use advanced compression algorithms and deduplication techniques to minimize storage footprint and associated energy consumption. Balance retention policies with compliance requirements to avoid unnecessary data storage overhead.

- **Regional data strategy:** Select regions for data storage that optimize both cost and performance while minimizing environmental impact. Store training data in the same region where model training occurs to reduce cross-region data transfer costs and latency.

  When you plan your data infrastructure, consider regions that have lower storage costs and renewable energy availability. Evaluate the total cost, including storage, compute, and data transfer fees when you select regions for data storage and model training.

  > ![Trade-off icon](../_images/trade-off.svg) *Trade-off:* Balance data residency and compliance requirements with low-carbon region deployment opportunities. Some organizations might need to store data in specific regions because of regulatory requirements.

- **Disaster recovery and backup:** Design [disaster recovery](/azure/well-architected/reliability/disaster-recovery) and backup strategies that balance data protection with energy efficiency. Implement intelligent tiering, cross-region replication optimization, and [life cycle management](/azure/well-architected/sustainability/sustainability-storage) with deduplication. These strategies minimize storage energy consumption while maintaining required recovery objectives.

## Operational design recommendations

The operational design phase includes model deployment, inference optimization, monitoring, maintenance, and scaling. By making informed decisions about operational infrastructure and processes, you can significantly improve the sustainability of your AI inference workloads.

There are multiple operational design decisions that affect sustainability. Consider the following key factors for maintaining efficient AI workloads while minimizing environmental impact:

- **Carbon-aware deployment:** Deploy models in [low-carbon regions](/azure/well-architected/sustainability/sustainability-application-platform#deploy-to-low-carbon-regions) by using renewable energy sources. Implement dynamic region selection based on real-time carbon intensity data and workload requirements.

- **Retraining frequency:** Retraining orchestration includes automating the retraining models based on new data while balancing accuracy requirements with environmental impact.

  This approach ensures that the model stays up-to-date and maintains its performance over time. Retraining frequently isn't sustainable. Consider implementing [model decay detection](/azure/well-architected/ai/test#prevent-model-decay) and threshold-based retraining to minimize unnecessary computational cycles.

  Sometimes frequent retraining isn't required, such as image classification or speech processing scenarios. Models that recognize speech patterns in specific languages or dialects can remain effective over extended periods without frequent retraining. As a result, AI models can maintain their effectiveness over time without the need for constant updates. Balance model accuracy with energy consumption by setting thresholds for retraining.

  > ![Trade-off icon](../_images/trade-off.svg) *Trade-off:* Reducing the frequency of retraining can affect accuracy. For example, models that are used in financial markets need to be highly dynamic and frequently retrained to adapt to new market conditions and data. Ensure that you consider the right trade-off based on business needs. As a proactive measure, use automated processes for continuous monitoring, and evaluate and retrain models to maintain accuracy and relevance. You also need to monitor infrastructure and workload metrics to help ensure optimal performance and reliability. For more information, see [Generative AI operations monitoring tools](/azure/well-architected/ai/mlops-genaiops#monitoring).

- **Comprehensive monitoring strategy:** A monitoring strategy is used to track data drift, performance, and sustainability metrics. Track model performance to avoid wasting machine learning operations cycles because of model decay. When models decay, they don't predict correct outcomes but continue to run, which wastes machine learning operations cycles.

  Integrate sustainability metrics into your AI workload monitoring alongside traditional performance, reliability, and security metrics. For more information about implementation guidance, see [Sustainability operational procedures](/azure/well-architected/sustainability/sustainability-operational-procedures).

- **Scaling and workload resiliency:** Implement [autoscaling strategies](/azure/well-architected/performance-efficiency/optimize-code-infrastructure) that balance resource allocation with environmental impact. Configure scaling policies to consider both workload requirements and regional carbon intensity. Scale up during low-carbon periods when possible.

  Design [redundancy and fault tolerance patterns](/azure/well-architected/reliability/redundancy) that minimize energy overhead while maintaining required availability levels. Use carbon-aware load balancing that distributes traffic across regions based on performance metrics and real-time carbon intensity data.

- **Sustainable machine learning operations practices:** Integrate sustainability metrics into your [machine learning operations workflows](/azure/well-architected/ai/mlops-genaiops) throughout the life cycle. Implement carbon-aware model training schedules that optimize computational workloads during periods of lower carbon intensity.

  Design deployment strategies such as A/B testing and blue-green deployments to validate the sustainability impact of new workload versions. This approach potentially prevents inefficient workloads from reaching full production scale.

  Monitor sustainability metrics during controlled deployments, and implement automated rollback mechanisms when sustainability thresholds are exceeded.

Implementing these sustainable practices enables AI developers and data scientists to reduce the environmental impact of AI workloads. This approach ensures alignment with Well-Architected Framework pillars while making appropriate trade-offs based on your business requirements.

## Next step

> [!div class="nextstepaction"]
> [Design principles of a sustainable workload](sustainability-design-principles.md)

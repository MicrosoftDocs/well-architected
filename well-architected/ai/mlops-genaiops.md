---
title: MLOps and GenAIOps for AI Workloads on Azure
description: Use MLOps and GenAIOps for AI workloads on Azure. Learn design strategies for operations and get tool recommendations.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: concept-article
ms.service: azure-waf
ms.update-cycle: 180-days  
---

# MLOps and GenAIOps for AI workloads on Azure

AI workload operations are centered on curation of data and consumption of that data. Operations ensure efficiency in achieving and maintaining the quality, reliability, security, ethics, and other standards that you prioritize for the workload.

The workload tasks can be categorized into three main areas: application development, data handling, and AI model management. Each category should adopt industry-proven operational methodologies, like **DevOps, DataOps, MLOps, and GenAIOps**.

DevOps activities cover the entire **application development lifecycle management** through automated continuous integration and continuous deployment (CI/CD) pipelines and monitoring. However, for AI workloads, the data pipeline is one of the core components. DataOps, a specialization of DevOps, focuses on **managing the data lifecycle** by streamlining processes like data extraction, transformation, and loading (ETL/ELT). DataOps practitioners typically measure data flow performance and the efficacy of data cleansing and monitor the pipeline for anomalies. 

AI workloads are inherently nondeterministic. Many AI models are prone to producing different answers for the same inquiry during inference. These workloads need processes that can manage and adapt to the unpredictability of AI outputs. DataOps extends into MLOps, which **operationalizes machine learning workflows** for model training and testing. GenAIOps, a specialized subset of MLOps, targets generative AI solutions. It involves tasks like **model discovery and refining pretrained models** with enriched data. 

Operational activities often overlap, and the different methodologies apply to varying degrees. For instance, in discriminative AI, DataOps plays a major role, while DevOps activities are less prominent. Conversely, in generative AI, operational excellence relies more heavily on DevOps than DataOps.

Regardless, the overall goal is capability delivery with efficient operations throughout the development lifecycle. The expected outcomes are:

- Repeatable processes with consistent results.
- Sustained accuracy of the models over time.
- Effective governance that minimizes risks.
- Change management processes for adapting to model drift.

**Automation and monitoring are your key operational strategies** to achieve those goals. 

You also need to **establish standardized processes for AI components**, for routine, unplanned, and emergency operations, and have proper tracking mechanisms in place. Without those processes, you run the risk of:

- Repeated errors and nonreproducibility in data processing, model hosting, grounding data management, and other tasks.
- Low quality or stale data used for model training and refinement.
- Affecting end user confidence in the system, in the worst case leading to legal, compliance, or security issues.

You must implement established processes with the **right set of tools**. Specialized tools are available for managing AI / machine learning workflows across environments. 

This article focuses on the design strategy for operations and provides tool recommendations.

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
|**Design an efficient workload operations lifecycle**. | Based on the components of your AI workload different operational stages will apply to its lifecycle. It's important that you understand the best practices relevant to your scenario and the tools that are available to you to implement them. Take the the time to learn about and implement the key recommendations for all components of your workload. <br><br>&#9642; [DataOps](#dataops)<br>&#9642; [MLOps](#mlops)<br>&#9642; [GenAIOps](#genaiops)<br>&#9642; [Monitoring](#monitoring)|
|**Automate everything**. | Automation ensures repeatability and efficiency in a workload's lifecycle. While DevOps processes are a key contributor to this, there are additional steps that you need to take to build, test, validate, and deploy your models efficiently. <br><br>&#9642; [Automation](#automation)|
|**Utilize deployment pipelines where possible**. | Deployment pipelines can help you deliver repeatable infrastructure deployments, or integrate code continuously. They're also a great tool for creating and/or validating models before promoting them to production. Implementing deployment pipelines helps you improve reliability as well as the overall user experience of your workload. <br><br>&#9642; [Deployment Pipelines](#deployment-pipelines)|
|**Prevent drift and decay in your models**.  | You must guard against model decay and drift while also having a structured process that helps you accept new model changes in a controlled way. Following the recommendations around model maintenance helps you remain compliant, avoid unexpected user experiences, and provide a more up-to-date service. <br><br>&#9642; [Model Maintenance](#model-maintenance)|

## Workload operations lifecycle

This image illustrates the operational stages for AI models, including data gathering, cleaning data to eliminate inconsistencies or errors, and transforming data into an analyzable format. These stages are relevant for training both discriminative models and grounding data in generative models. However, the specific use case of training generative models isn't depicted in this image. That use case falls outside the scope of this article.

:::image type="content" source="./images/mlops-genaiops.png" alt-text="Diagram showing MLOps and GenAIOps activities." lightbox="./images/mlops-genaiops.png":::

The stages of MLOps and GenAIOps are similar. The main difference is that, in the case of GenAIOps, the focus shifts from training to selecting the right model, prompt engineering, and incorporating domain-specific knowledge through fine-tuning or implementing Retrieval Augmented Generation (RAG). 

Even the stages for deploying and monitoring are fairly similar.

The following sections describe the common operational practices. They cover every stage of the lifecycle, from preproduction to production.

#### DataOps

Data is aggregated from various production data sources and then preprocessed to remove errors and inconsistencies and to handle missing values. Finally, it's transformed and normalized into a suitable format for training or enrichment. Aspects of design are described in the [Training data](./training-data-design.md) and [Grounding data](./grounding-data-design.md) articles.  

The data operations processes during this stage should be efficient because **handling large volumes of data** from multiple sources and complex data pipelines can be challenging. The approaches that you take need to ensure that this phase **produces high quality data**. Monitor this stage to track progress toward the acceptable quality bar.
  
You also need to ensure that data is secure, given that the data is from production. Make sure that lower environments, like Dev/Test, are as secure as production to help prevent any security issues.

> [!NOTE]
> Address poor quality data by investing in extensive data cleaning during the initial phases. Take advantage of the well-known analytics patterns, like medallion, data mesh, and feature stores, to do upstream processing tasks. If the upstream phases are ineffective, you need to improve quality during the downstream phases, which leads to increased workload costs because data preparation occurs during each stage.
>
> For information about data processing tasks, see these articles:
>  - [Data preprocessing for training data](./training-data-design.md#data-preprocessing)
>  - [Data preparation for grounding data](./grounding-data-design.md#data-preparation)

##### Tools

We recommend that you standardize your workload's data orchestration tools. The tools should be able to provide a data pipeline that can group activities and that has built-in automation.

An [Azure Data Factory](/fabric/data-factory/data-factory-overview) pipeline can be an initial choice. It can connect and process many data sources efficiently. You can also consider [Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is), which combines big data and data warehousing and supports data lakes, Apache Spark, and Azure Synapse SQL. It also integrates with Data Factory for ETL.

For preparing training data, [Azure Machine Learning pipelines](/azure/machine-learning/concept-ml-pipelines) provide specialized features that can automate tasks like data collection and processing.

Open-source technologies like Pandas (for data preparation) and Scrapy are popular choices.

#### MLOps

Model training is the process of selecting appropriate algorithms and providing them with preprocessed historical data and observations to enable the model to learn patterns and make predictions.

Training the model (feature engineering) and hyperparameter tuning are iterative processes, and they're expensive. During each iteration, data scientists track results with combinations of data, code, and parameters. **Use repeatable pipelines to track experiments** with minimum manual effort until the right level of accuracy is achieved.

Another operational challenge is provisioning and scaling **specialized compute resources** where experiments are conducted. Also, you should **package and publish models** efficiently.

Teams can start with UI-based development to reduce challenges, and, as they become more confident, transition to a code-based approach.

##### Tools

We recommend that you use tools that can **track machine learning experiments** by capturing details like code versions, environments, parameters, runs, and results. MLflow is one such open-source framework. Consider using Azure Machine Learning workspaces, which are [MLflow-compatible](/azure/machine-learning/concept-mlflow) and provide a streamlined workflow that enables data scientists to manage productivity and reproducibility in their projects. To manage code development with source control tracking, integrate the machine learning pipeline with source control like GitHub, or use file shares.

The hosting compute can also influence your choice of a workflow orchestrator. If your application is hosted on Azure Kubernetes Service (AKS), consider using Kubeflow.

If you're considering Azure Machine Learning, we recommend that you start with the [Azure Well-Architected Framework perspective on Machine Learning](/azure/well-architected/service-guides/azure-machine-learning) to make sure you understand how the product can help you with the Well-Architected Framework quality concerns of the workload.

Part of the benefit of the process is to optimize personal time. Data scientists typically need specific tools and SDKs to effectively conduct exploratory data analysis (EDA) and experimentation from their workstations. Evaluate whether prebuilt options in Azure Machine Learning are suitable. If not, store workstation configuration or maintain approved VM images for this work. One example of an image that you can use as a starting point is the [Data Science Virtual Machine (DSVM)](/azure/machine-learning/data-science-virtual-machine/overview).

In certain cases, the use of VMs might be disallowed by policy. Look for alternatives, like adding Microsoft Dev Box and Azure Virtual Desktop. You can also consider using Docker to bootstrap machines that contain prebuilt images.

However, as this stage matures and you need extended experiments, move to managed compute instances and prefer options that are integrated as part of the workflow. Evaluate whether you can use [Azure Machine Learning compute instances](/azure/machine-learning/concept-compute-instance) for training and inferencing for development and testing purposes. The compute clusters can  handle large datasets and complex models.

Azure Machine Learning provides both code-based solutions through the SDK and low-code options like automated machine learning and the visual designer. The Python SDK offers multiple ways to train models, each with distinct features. Machine Learning also supports advanced optimization and distributed computing technologies like ONNX Runtime Training's ORTModule, DeepSpeed, and LoRA to speed up the training process.

#### GenAIOps

Key activities during this stage begin with **discovering and evaluating existing models** to identify one that's pretrained for the specific use case. This is an iterative process. After you find a suitable model, it might benefit from being refined for domain-specific grounding, which also involves iterative steps and requires a certain level of orchestration. 

Integrating and deploying models requires specialized tools and practices that go beyond traditional MLOps capabilities, including orchestrating models, vector indexes, prompts, and code blocks.

##### Tools

To address discovery tasks, take advantage of model catalogs that include models from various providers. The [model catalog in Azure AI Foundry portal](/azure/ai-studio/how-to/model-catalog-overview) allows you to evaluate from among curated collections and deploy models efficiently. 

[Azure Machine Learning prompt flow](/azure/machine-learning/prompt-flow/overview-what-is-prompt-flow) can help with the development of orchestration code, enabling prototyping, experimenting, iterating, and prompt engineering. These flows can be deployed to Azure Machine Learning managed endpoints. Evaluate whether you can run and deploy the flows with your existing CI/CD pipeline technology.

#### Deployment

During this stage, the model is deployed to a hosting and inferencing platform or the serve layer of AI workloads. The APIs need to be packaged as a scalable container. The container platform can be a managed compute or custom hosting platform. Operational practices should ensure safe deployment and enable rollbacks.

Start with platform as a service (PaaS) and serverless solutions like Azure OpenAI in Foundry Models to simplify adoption and management. Consider using the Azure Machine Learning Serverless API for aggregating endpoint access. Managed compute clusters are a viable option for advanced needs. Self-hosting on AKS is another option. Make sure you right-size your compute and maintain proper isolation from other workloads. You can also consider options like fully hosting your model as infrastructure as a service (IaaS). IaaS provides flexibility but can add operational burden. These options are described in [Application platform](./application-platform.md#online-inferencing).

This stage presents the last chance to catch issues before you move the model to production. Test processes should include validation steps to make sure the model is configured to provide predictions as expected. 

You should integrate the model into the existing production environment by following **progressive exposure processes and using side-by-side deployments**. The canary model is a common way to roll out new models. With this method, the user base is increased gradually. Blue-green deployment is another method.

##### Tools

You can use [Azure Machine Learning pipelines](/azure/machine-learning/concept-ml-pipelines) or [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) to deploy your models for inference. Machine Learning provides several features for simplified operations, including node provisioning, OS updates, autoscaling, monitoring, and isolated virtual networks.

Machine Learning also supports blue-green deployment, which allows a single endpoint to contain multiple deployments.

If you use other hosting platforms, like Azure Container Apps or Azure App Service, you're responsible for the operations, including provisioning and scaling. Use Azure DevOps, GitHub pipelines, or your choice of CI/CD technology in those cases.

#### Monitoring

Monitoring is a key strategy and is applied at all stages. It's an ongoing process and serves as an input to quality gates that ensure AI workloads are rigorously tested to maintain consistency and reliability throughout the development lifecycle. Models must be monitored from both operational and data science perspectives.

We strongly recommend that you have a DataOps inner loop monitoring process that measures proximity to an acceptance quality bar and checks for anomalies. 

For pretrained models, it also important to monitor data drift and performance, with a primary focus on relevancy. Evaluate the inputs (prompts) and the outputs (completions) to ensure they are relevant and accurate. Additionally, be aware of security risks, such as attempts to manipulate the model's behavior through malicious prompts. Make sure there's thorough content moderation that inspects data in both directions and filters out inappropriate content. These considerations are described in the [ResponsibleAI design area](./responsible-ai.md).

After deployment, monitoring operations are necessary to address issues like model decay. Models can become stale because of changes in data or external changes that can cause the model to produce irrelevant results. As a proactive measure, use automated processes for continuous monitoring, and evaluate and retrain to maintain accuracy and relevance. Additionally, you need to monitor infrastructure and workload metrics, as you would with any other workload, to help ensure optimal performance and reliability. For more information, see [Testing for model decay](./test.md#testing-for-model-decay). 


##### Tools

Invest in tools that make it easier to collect metrics from inference endpoints, like Azure Machine Learning Data collector. 

You also need observability of model performance, data drift, and safety and quality for generative AI. 

For more information, see these articles: 

- [Data collection from models in production](/azure/machine-learning/concept-data-collection)

- [Model monitoring with Azure Machine Learning](/azure/machine-learning/concept-model-monitoring)

- [Content filtering in Azure AI Foundry portal](/azure/ai-studio/concepts/content-filtering)

## Automation

AI workloads are complex because the overall lifecycle involves many roles, frequent changes, and interrelated steps. Manual processes can be prone to errors and inconsistencies. Automation in data processing model hosting helps ensure **repeatability and efficiency**. Automation isn't always required, but it's an effective way to manage these complexities. Here are some use cases in which automation can mitigate risks:

- Unlike traditional code deployment, nondeterministic models and solutions in AI / machine learning require iterative experimentation and training. When multiple teams collaborate, automation, as a way to enforce standardized processes, can help maintain consistency, reproducibility, and effective collaboration among data scientists, engineers, and operations teams.

  The model lifecycle involves two main types of training: 

  - Online training incorporates recent data into the model frequently, sometimes daily, to ensure that decisions are based on the latest information. This training is integrated into the workload so that the model is continuously updated as part of the regular process.

  - Offline training trains the model less frequently, allowing a longer gap between updates. The training process is separate from the main workload and is done asynchronously. After the new model is ready, it's then integrated into the system.
 
  **Reliability can be compromised if updates are infrequent**. If an update is missed, it can be postponed without major issues. This concept also applies to grounding data. For example, if you use RAG, you must decide whether you need to use recent data or if slightly older data is sufficient. Both scenarios involve balancing the need for up-to-date information with the practicality of update frequency. You should perform online training via automation because of the frequency and reliability that's required. For offline training, because of the required frequency, you need to justify automation by performing a cost-benefit analysis. Additionally, you can perform offline training by using less expensive resources, like offline hardware.

- Traditional DevOps processes are typically affected by structural changes. However, in AI and machine learning, models are trained on production data. Model decay poses a significant risk and can lead to decreased performance over time if it's not monitored. Automated collection and analysis of performance metrics, alerts, and model retraining are required to maintain model efficacy. Use automation in a way that can help you **detect changes in data and model dependencies** to get a clear understanding of the current state at any given time.

- Models can be trained with two distinct approaches. 

  - **The models is trained in the development environment with full production data** and only the artifact is promoted through environments. This approach can lower computational costs but requires tighter security to handle production data in the lower environments and may not be possible in all organizations. 
  - **The model is trained in each environment**. Code promotion may help with stability because training code is reviewed and tested in the lower environments, but increases the cost of compute. 
  
  There are pros and cons to both approaches. Choosing the right approach depends on your organization's priorities and workload's Software Development Life Cycle (SDLC) practices. Regardless of the method, thorough testing and evaluation of the model before production deployment are essential

- Your automation code should **incorporate data lineage to support auditability** by providing a clear record of data processing stages. This record helps you manage expectations and enables you to demonstrate how decisions were made so you can address any concerns about outcomes.

## Deployment pipelines

In AI / machine learning workloads, model development involves **creating, validating, and promoting models** to model hosting platforms. It's important to have deployment pipelines that streamline complex workflows related to data processing, feature engineering, model training or augmentation, and deployment to production. Given the non-deterministic nature of AI, which makes processes opaque, you need to **incorporate qualitative tests in release pipelines** and monitoring systems.

Although MLOps and GenAIOps might require distinct AI activities and core technologies might differ, the underlying concepts remain similar to those of DevOps. We recommend that you apply best practices from your existing DevOps processes. **Integrate AI activities into your workload's existing pipelines**. 

Typically, AI workloads involve traditional code deployments. You can choose to handle model deployment alongside the code or separately in its own lifecycle. The former approach is preferable. Be prepared to package models and inference endpoints with workload deployment to keep AI operations primarily focused on data preparation, training/fine-tuning, grounding data management, and monitoring.

Reevaluate how the following assets can be tailored to cover the entire MLOps and GenAIOps lifecycle, from preproduction to production:

- Infrastructure-as-code (IaC) tools
- CI/CD pipelines 
- The observability stack for tracking and identifying issues

##### Tools

You can extend Azure Pipelines and GitHub Actions workflows, commonly used for CI/CD, to machine learning models. They help deploy machine learning infrastructure, custom workload components, orchestration code, and models. Combine Azure Machine Learning pipelines with Azure DevOps or GitHub pipelines. For more information, see [Use Azure Pipelines with Azure Machine Learning](/azure/machine-learning/how-to-devops-machine-learning).

Two main factors influence your choice of the right combination of tools: use case and capabilities. For example, Azure Machine Learning pipelines are great for the orchestration that's performed by data scientists. It has a rich feature set that supports reuse, caching, and more. For tooling choices, see [Which Azure pipeline technology should I use?](/azure/machine-learning/concept-ml-pipelines#which-azure-pipeline-technology-should-i-use).

## Model maintenance

The AI/ML landscape is competitive with ongoing innovation. New models emerge frequently, new use cases are discovered, and new data sources become available. As a result, model decay is a common challenge. 

To prevent model performance degradation or drift over time, you need to implement automated processes for continuous monitoring, evaluation, and retraining. For example:

- **Maintain a model catalog**. Automate the process of discovering new models and updating the catalog.

- **Adapt to new use cases**. As new use cases are added to the workload requirements, anticipate the queries and adjust your data-processing logic accordingly.

- **Incorporate new data sources**. If new data sources can potentially enhance the predictive power or relevance of your model, update your data ingestion pipeline to connect to and pull data from those sources.

- **Assess compliance with regulatory requirements**. When you adapt to new capabilities, ensure that changes remain valid within the constraints of organizational or external compliance standards.

Implement a formal process for tracking continuous improvement, and incorporate self-improvement as a subprocess within that cycle.

## Continuous evolution

Regularly review and improve operations and encourage innovation. 

The MLOps maturity model progresses from manual processes to full automation. Start with manual builds and monitoring, and incorporate automated application builds, training environments, and deployment in phases as justified by comprehensive metrics. For more information, see [MLOps maturity model](/azure/architecture/ai-ml/guide/mlops-maturity-model).

The GenAIOps maturity levels move from basic models to structured deployment, progressively using automated optimization techniques. For more information, see [Advance your maturity level for GenAIOps](/azure/machine-learning/prompt-flow/concept-llmops-maturity).

## Next steps

> [!div class="nextstepaction"]
> [Design area: Operations](./operations.md)

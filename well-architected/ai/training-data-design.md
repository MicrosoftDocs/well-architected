---
title: Training Data Design for AI Workloads on Azure
description: Training data considerations for running discriminitive AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: conceptual
---

# Training data design for AI workloads on Azure

When designing data for AI functionality in applications, consider both non-functional requirements (such as operability, cost, and security) and functional requirements related to data ingestion, preparation, and validation. 
 
Data design and application design cannot be decoupled. Application design involves understanding use cases, query patterns, and freshness requirements. For business requirements that drive the need for using features of AI, the application might need output from discriminative models, generative model, or combination of model types. 

AI models require training to produce meaningful results. Model training involves teaching a model to classify or predict new, unseen situations. The training data must be tailored to the specific problem and workload context.

Supervised training involves providing the model with labeled samples, which is useful when the desired outcome is clear. In contrast, unsupervised learning allows the model to identify patterns and relationships within the data without guidance on the expected output. During training, the algorithm type and its parameters are adjusted to control how the model learns. The approach varies depending on the type of model, such as neural networks, decision trees, and others. 

For example, image detection models. These models are typically trained tasks like object detection, facial recognition, or scene understanding. They learn from annotated images to identify specific objects or features. Other common examples include fraud detection algorithms, or price point prediction models. They learn from historical financial data to make informed decisions. 

This article primarily focuses on the preceding use case where models are trained _before_ they can give meaningful input to the application. Included in this article is guidance on data collection, processing, storing, testing, and maintenance. Providing data design for exploratory data science or business intelligence using AI is out of scope. The goal is to support training needs through strategies that's aligned with workload requirements, providing recommendations on the training data pipeline of an AI workload.

For information about data design for AI models that require context _during_ inferencing, see [Grounding data design](./grounding-data-design.md). 

> [!IMPORTANT]
>
>  Expect data design to be an iterative process based on statistical experimentation. To reach an acceptable quality level, adjust training data, its processing, model feature development, and model hyperparameters (when possible). This experimentation loop typically happens both in initial model training and then ongoing refinement efforts to address data and model drift over the lifespan of the feature in the workload.

## Recommendations

Here's the summary of recommendations provided in this article. 

|Recommendation|Description|
|---|---|
|**Select data sources based on workload requirements.** |Factor in available resources and whether the data source can help you reach the acceptable data quality for model training. Cover both positive and negative examples. Combine diverse data types to achieve adequate completeness for analysis and modeling. Consider techniques like Synthetic Minority Oversampling Technique (SMOTE) for data scarcity or imbalance.<br><br>&#9642; [Data ingestion and analysis](#data-ingestion-and-analysis)|
|**Conduct data analysis on the collected data early**. | Perform analysis process, such as Exploratory Data Analysis (EDA), offline, considering costs and security impact. For small datasets without resource constraints, analysis at source can be considered.<br><br>&#9642; [Data collection store](#data-collection-store)|
|**Maintain data segmentation, if business and technical requrements call for it**.  |If using data sources that have distinct security requirements, create separate pipelines for each model. Establish access controls to limit interaction with specific data subsets. <br><br>&#9642; [Data segmentation](#data-segmentation)|
|**Preprocess data making it meaningful against training goals.**| Refine the quality of ingested data by filtering noise, rescoping data, addressing duplicates, and standardizing diverse formats. <br><br>&#9642; [Data preprocessing](#data-preprocessing)|
|**Avoid training on stale data**. |Monitor for data drift and concept drift as part of your inner and outer operational loops to maintain the accuracy and reliability of the models over time. Regularly update training data with new observations. Define conditions triggering model retraining and determine update frequency. <br><br>&#9642; [Data maintenance](#data-maintenance)|
|**Monitor for model drift.**|Changes in input data and |


TODO: Note to Yeliz. This is only a summary of recommendations in this article. This is kept here to gather your opinions and also serve as an input to assessments. 


## Types of data

To build predictive power in models, data needs to be collected, processed, and fed to the model. This process is usually conceptualized as a pipeline broken into stages. Each stage of the pipeline might deal with the same data set but it might serve different purposes. Typically, you'll handle data of these types:

-   _Source data_ is point in time observation data. It can be also be data that can be labeled to serve as a potential input to the data pipeline. 

    This data is usually obtained from production or from an external source. These data sources can be in storage accounts, databases, APIs, or other sources. The data can be in various data format, such as OLTP databases, unstructured documents, or log files. This data serves as potential input to the data pipeline.

- _Training data_ is a subset of source data used for providing samples to the model. The samples are descriptive precalculated data, which helps the model learn patterns and relationships. Without this data, the model cannot generate relevant output.

- _Evaluation data_ is a subset of the source data used to monitor and validate the performance of a machine learning model during training. It's distinct from training and test data and is used to periodically evaluate the model's performance during the training phase and guide hyperparameter tuning. For more information, see [Model evaluation](./application-design.md).
 
- _Testing data_ is used to validate the predictive power of a trained model. This data is sampled from source data that wasn't used for training. It contains observations from production so that the testing process is conclusive. From data design perspective, you need to store it. For information about testing models, see the [Testing](./testing.md) design area.

In some cases, information provided by users during interactions with the application can eventually become source data. In general, it's recommended that user input used this manner is of high quality. Otherwise, continuously handling quality issues downstream can be problematic. Guidance about handling user data isn't in scope for this article.



## Data ingestion and analysis

Training data is collected within a predetermined window that has sufficient representations for training the type of model selected. For example, when training a binary classification model, training data must include representations of what is the case (positive examples) and what is not the case (negative examples). For training data to be meaningful, conduct Exploratory Data Analysis (EDA) early in feature design.

EDA helps analyze source data to identify characteristics, relationships, patterns, and quality issues. EDA can be conducted directly at the source data store or replicated into centralized stores such as a data lake or data warehouse. The outcome of the process is to inform data collection and processing for effective model training.

> [!NOTE]
> While EDA process is a pre-production process, it uses data sourced from production. Apply the same level of control to this process as you would for production.

Here are some considerations for collecting data in preparing for model training. 

#### Data sources

Data can be collected from:

  - **Proprietary data** is created or owned by the organization. It's not intended for public consumption; instead, it serves internal purposes.

  - **Public sources** that's accessible to anyone, including information from websites, research papers, and publicly shared databases. Even if specific to a niche area, such data remains generally available. For example, content from WikiPedia, PubMed, are considered publicly accessible.

Choice of data sources depends on workload requirements, available resources, and the quality of data that's acceptable to train the model. Imbalanced datasets can lead to biased models, so data collection must be designed to get sufficient samples of representative data. You might need to oversample the minority data or under sample the majority data.  In cases of data scarcity or imbalance, consider techniques like [Synthetic Minority Oversampling Technique (SMOTE)](/azure/machine-learning/component-reference/smote) and [Synthetic data generation](/azure/ai-studio/concepts/concept-synthetic-data#synthetic-data-generation).


#### Data collection store

There are two main options for collecting source data: querying the data in situ (origin of data source) or copying the data to localized data stores and then querying that store.

  The choice will depend on workload requirements and the volume of data. If have a relatively small amount of data, the source system might handle your raw queries directly. However, the common practice is to query and analyze from the localized store.
 
  > ![Consider the tradeoff associated with this decision.](../_images/trade-off.svg) **Tradeoff**. While localized data stores may facilitate analysis and the training process, balancing costs, security, and model requirements must be considered.
  >
  > Duplicating data incurs storage and compute costs.
Maintaining a separate copy requires additional resources. Local copies may contain sensitive information. In that case, this data  must be protected with regular security measures.
  >
  > If you're using production data for training data, it must be subject to all the original data classification constraints of that data.
  
Data can be handed to the training process (push mode) or the process itself queries the data source (pull mode). The choice depends on ownership, efficiency, and resource constraints. 

When data is pushed to the workload, it's the responsibility of data source owner to provide fresh data. The workload owner provides a suitable spot in their localized data store where the data lands. This approach is relevant for proprietary data owned by the organization, rather than public sources.

In case of pulling, there are two approaches. The workload writes a query against the data store, retrieves necessary data, and places it in the localized store. Another way is to do real-time query in memory. The decision depends on data volume and available compute resources. For smaller datasets, in-memory retrieval may suffice for model training.

Regardless of pull or push mode, avoid training models on stale data. The frequency of data updates should align with workload requirements.

#### Data segmentation

Workload-specific requirements may require data segmentation. Here are some potential use cases:

- **Security requirements** often drive segmentation decisions. For instance, regulatory constraints may prevent exporting data across geopolitical regions. If your application design allows for separate models, data design would provide for separate data pipelines for each model. 

  However, if a single model is the strategy, segmented data sources will feed into that model. You'll need to train the model on data from both geographies, potentially adding complexity. 
  
  Whether the application is using a single model or multiple models, preserve security measures on each data segment so that it's protected with the same level of rigor as its origin.

- **Data freshness rate** can be a factor for separating data. Data from different sources may refresh at varying time intervals. If the data changes, retraining becomes necessary. Segementation allows for granular control of data lifecycle. Consider separate tables or pipelines for different data segments.

Regardless of the use case, when segmenting data, access controls are key. Data professionals, such as data engineers, data scientists will explore available source data to understand patterns and relationships. Their insights contribute to training models that predict outcomes. Establish access controls to ensure that only authorized users can interact with specific data subsets. Apply least privilege on data that's considered to be relavent. Collaborate with the data owners to set up appropriate permissions.


## Data preprocessing

In a real-world scenario, the source data isn't stored just for AI scenarios. There's an intermediate process that prepares the data for training. During this stage, data is stripped of noise, making it useful for consumption. When dealing with source data, data scientists engage in a process of exploration, experimentation, and decision-making. Their primary goal is to identify and extract parts of the source data that holds predictive power. 

The preprocessing logic depends on the problem, data type, and desired outcomes. Here are some common techniques for preprocessing. This list isn't exhaustive. The actual criteria for your workload will be driven by business requirements. 

- **Quality**. Preprocessing can ensure that the training data is stripped of noise. The goal is to ensure that every row in your training data represents a clear observation or a good example relevant to your use case, while eliminating observations that lack quality or predictive power. For this strategy, preprocessing process should exclude low-quality or non-predictive data to avoid sampling from unreliable sources. For example, when collating product reviews, you might choose to eliminate data that's too short. It's important to discover what data quality will lead to meaningful predictive results. 

- **Rescoping**. Source data fields that are too specific can restrict predictive powers. For example, consider an "Address" field. Broadening the scope from full address (house number and street name) to higher level, such as city, state, or country might be more relevant.

- **Deduplication**. Eliminating redundancy can ensure that your training data remains accurate and representative. In certain cases, the frequency with which an observation is made isn't relevant. For example, when scanning logs, a log entry appearing 1000 times might indicate its frequency, but it doesn't necessarily imply that it's more of an error than a log that occurred only once. This type of redundancy can introduce noise.

- **Sensitive data handling**. Personally identifiable information (PII) should be eliminated unless it's absolutely vital to the model's predictive power in a way that cannot be acheived through anonymization. Training data should be effective without compromising privacy. If it provides value, then be aware of ethical considerations that comes with handling sensitive data. For more information, see [User input, ethics and security considerations](./userinput-ethics-security.md).

- **Standardized transformation**. The preceding techniques are considered a core part of Feature Engineering by domain experts. Broad scope and diverse source data eventually need to merge into feature stores where features are organized (for example, into feature tables) for the explicit purpose of training models. After selecting predictive data for training, transform the data to a standardized format. Standardization also ensures compatibility with training model.

  Morphing images into text representations is a form of transformation. For instance, converting scanned documents or images to machine-readable text.  
  
  To ensure compatibility with models, you might need to adjust  orientations or aspect ratios of imagse to match the model's expectations.

> [!NOTE] 
> Handling large amounts of data with a mix of structured and unstructured data can increase processing time. Workload teams should measure the impact of processing diverse formats. If the window between retraining efforts becomes shorter, then time spent each time in preprocessing becomes more critical. 


## Data retention

After training the model, evaluate whether to delete the data used for training and rebuild for the next training window. 

If data remains relatively unchanged, retraining might not be necessary unless there's a model drift. If the accuracy of prediction decreases, you'll need to retrain the model. You can choose to ingest the data again, preprocess, and build the model. That's preferable if there's significant delta in data from that last training window. If there's large volume of data and it hasn't changed much, preprocessing and rebuilding might not be necessary. In this case, retain data, do in-place updates, and retrain the model. Decide how long you want to retain training data.

In general, delete data from feature stores to reduce clutter and storage costs for features that have poor performance and are no longer relevant to any current or future models. If you're retaining data, expect to manage costs and address security issues, which are typical concerns of data duplication. 

## Lineage tracking

Data lineage refers to tracking the journey of data from its source to its use in model training.  Keeping track of data lineage is essential for explainability. While users may not need detailed information about data origins, internal data governance teams find it crucial. This metadata ensures transparency and accountability, even if it's not directly used by the model. This is useful for debugging purposes. It also helps to determine whether biases were introduced during data preprocessing.

When possible, use platform features for lineage tracking. For example, Azure Machine Learning is integrated in Microsoft Purview, giving you access to features for data discovery, lineage tracking, and governance as part of the MLOps lifecycle. 



## Data maintenance

All models can become stale over time causing model's predictive power or relevance to decay. Several external changes can cause decay, shift in user behavior, market dynamics, or other factors.  Models trained a while ago may no longer be as relevant due to changing circumstances.To predict with better fidelity, recent data is crucial.

- **Adopting newer models**. To ensure relevance, an operational loop is necesssary that continuously evaluates model performance, considers newer models, keeping the data pipeline minimally disruptive. Or, prepare for a bigger change that involves redesigning the data lifecycle and pipeline.

  When choosing a new model, you don't necessarily need to start with new data set. The existing observations used for training may remain valuable even during a model switch. While new models may reveal narrower scenarios, the fundamental process remains similar. Data management approaches like Feature Store and Data Mesh can help streamline the adoption of new machine learning models.

- **Trigger-based or routine operations**. Consider if model retraining is triggered by specific events or conditions. For example, new more relevant data might be available or if relevancy drops below the established baseline, retraining might be triggered. The advantage of this approach is responsiveness and timely updates. 

  Maintenance can also be scheduled at regular and fixed intervals, daily, weekly, and so on. For fail-proof operations, consider both approaches.

- **Data removal**.  Remove data that is no longer in use for training to optimize resource use and minimize the risk of using outdated or irrelevant data for model training.

    The concept of the _right to be forgotten_ refers to an individual's right to have their personal data removed from online platforms or databases. Make sure you have policies in place to remove personal data if it was used for training. 

- **Data retention**. There are situations where you need to rebuild the existing model. For example, for disaster revovery, the model should be regerenated exactly as it was before the catastrophic event. It's recommended that you have a secondary data pipeline that follows the workload requirements implemented by the primary pipeline, such as fights model decay, is reguarly updated through trigger-based or routine operations, and other maintenance tasks. 

> ![Consider the tradeoff of data maintenance.](../_images/trade-off.svg) **Tradeoff**. Data maintenance is expensive, involving data copying, building redundant pipelines, and effort of running routine processes. Keep in mind that regular training may not improve answer quality, it only provides assurance against staleness. Evaluate the importance of data changes as a signal to determine the frequency of updates. 

Make sure data maintenance is done as part of model operations. There should be established processes to handle changes through automation as much as possible and also use the right set of tools. For details, see the [MLOps and LLMOps for AI workloads on Azure](./mlops-llmops.md) design area.





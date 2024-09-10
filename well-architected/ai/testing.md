---
title: Testing and validating AI workloads on Azure
description: AI workload testing operations on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# Testing and validating AI workloads on Azure


- Working with non-deterministic outputs (inherent with LLMs)
- Defining success criteria for AI solutions
- Designing for slowness and asynchronous responses
- Testing acceptance criteria at scale

## Yuexin's (SME & area lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO


Leftovers:

Do you have questions & expected answers pairs created, for testing the quality of the chat system?
	
	For Talk to your SQL:
	Do all tables and columns contain relevant comments and meta information helping the LLM to choos the right column in the SQL statements ?




## Testing 

A subset of source data is used as the training data because the observations can serve as examples that the model learns from. Another subset of observations, distinct from the training data, is testing data that's used to validate model's accuracy and performance. Testing data evaluates how well the trained model generalizes to unseen examples, ideally seen in production. Sometimes, testing data can overlap with the training data when using a cross-validation approach in limited training datasets. 

#### Testing samples

Usually testing data constitutes a smaller percentage than the training data. For example, training data data might 50% of source data. Within the training data, 20% is used as testing data.

Test data should strive for probability distribution. Training and test data should align with even variations of positive and negative examples. If training data has 50% positive and 50% negative examples, test data should also reflect that balance. 

Test cases should validate the sub entities that are part of queries. Those entities should also be tested on positive and negative samples. 

#### Test data collected during ingestion

As part of ingestion from various data sources, include tests to validate that training data matches your expectations. 

Training a machine learning model with incomplete or corrupted data can be counterproductive. It might lead to wasted efforts and result in a model that fails to make meaningful predictions. Your data ingestion and preprocessing pipelines include quality tests as checkpoints. These tests help verify that your data aligns with the expectations set during data analysis and feature engineering.


Here are some example test cases. 

- **Test for completeness**. Test the expected quantity of training data, to verify the completeness of the data set. This test ensures that enough data was provided to train the model. 

- **Test for crtical information**. If training is based on known entities, such as specific records, identifiers, or entities, test the data set to validate that those entities are present. This validation ensures that critical information isn't missing.

- **Test for irrelavent data**.  Ingested data shouldn't contain removing irrelevant or erroneous entries. The data ingestion process should filter out that data. 

- **Test for freshness**. Freshness of the ingested data shouldn't impact the model's predictive power. Validate that the data reflects reasonably current information and isn't outdated from previous runs. For example, if the data is expected to include records from the past week, but there are no such records after importing, it could indicate a failed import or a data freshness issue in the source system.


#### Test for preparedness

Similar to testing the ingestion pipeline, apply quality tests to the processing pipeline before model training and testing. This helps mitigate risks data quality issues, such as stale or corrupted data. Some criteria are given in [Preprocessing criteria](#training-data-1). 


#### Test hyperparameters

Model parameters depend on application-specific design decisions. As part of your application design, choose the model and the parameters based on the workload use cases. The testing process involves an iterative inner loop where training data is compared against test data to validate that the model is training on the inteded data set. Also, the parameters are tuned so that the model is able to predict with an acceptable level of accuracy.

> ![Consider the tradeoff on cost of training.](../_images/trade-off.svg) **Tradeoff**. The inner loop involves computational cost of training the model with the cost of evaluating it through tests. The time required for model training and testing must be factored into this loop. Expect the testing process to execute for a longer period than the training process. Initial testing can be done on a subset of training data to assess whether the model produces reasonable results. That testing set can be gradually scaled up to the full dataset eventually.

#### Bias testing

TODO. Avoiding unfair or discriminatory outcomes.

#### Adversarial testing

TODO avoiding disclosing information it shouldn't



#### Define success metrics

It's recommended that you have a baseline and measure predictive power of the model using well-defined metrics. Here are some common metrics. 

- **Accuracy**. This metric represents the ratio of correctly predicted instances to the total instances in the test dataset. It's a common measure of overall model performance.

- **Precision**. Precision is the ratio of true positive predictions to the sum of true positives and false positives. It's particularly useful when minimizing false positives is important (e.g., in medical diagnoses).

- **Sensitivity**. Sensitivity measures the ratio of true positives to the sum of true positives and false negatives. It’s valuable when avoiding false negatives (missing relevant cases) is critical.

- **Specificity**. Specificity calculates the ratio of true negatives to the sum of true negatives and false positives. It's relevant when optimizing for accurate negative predictions.

TODO: For regression models you're usually concerns about topics like:
 
Mean Absolute Error (MAE): Measures the avg absoulte difference between predicted and actual values. Aligns predictions to reality.
Root Mean Squared Error: "the sq root of the avg squared differences between predicted and actual values." This penalizes significant errors more heavily than smaller ones




## Improvement over time

A generative model's efficiency is influenced by data design. Have a testing processes that evaluates and quantifies results of the end user experience based on the performance of the model, orchestration, index, preprocessing, and source data. Monitor and measure quality metrics. This measurement process is part of the iterative approach. Establish a baseline that's derived from the collected quality metrics and keep adjusting the design until targets are met.

Here are the common metrics.

- **Groundedness**. Refers to the model's alignment with the source data. A grounded model generates answers that's consistent with reality. 

- **Relevancy**. Indicates how pertinent the response is to a given question. A highly grounded answer may lack relevancy if it doesn't address the question directly.

- **Similarity**. Measure the similarity between a source data text and the generated response. Did the model use precise wording? Lack of editorial governance can lower the similarity score.

- **Retrieval**. Indicates the effectiveness of index queries. Evaluate how well the retrieved index data aligns with the question. Irrelevant data from the index search lowers this score. Higher retrieval score indicates lower variability because it's solely reliant on the index queries.

- **Fluency**. Relates to the vocabulary usage. If the model adheres to a style guide and presents content in the appropriate format, it can be fluent even if it lacks grounding or relevancy.

- **Coherence**. Evaluates whether the model's speech flows naturally and coherently. It assesses whether the conversation feels like a genuine exchange.

## Data platform

Data store for storing preprocessed often pulls data from various sources. When choosing the data store, these characteristics are key:

- **Storage format**. To enable hybrid searches between structured, unstructured, and semi-structured data such as images, videos, audio, documents. Choose a data store that can store data in a raw form. Data lakes are appropriate for this. Evaluate if Azure's native offering, Azure Data Lake Storage Gen2, meets your requirements.

  If the expected user queries are standardized to a particular format, for example images, videos, and so on, Blob storage is a competitive option.

- **Performance and ETL**. Evaluating performance in the ETL (Extract, Transform, Load) process is another critical aspect. Consider the number of pipelines, integration sources, and the data volume handled. Real-time updates impact performance, but for batch data, nightly updates suffice.

  Another option is Databricks that supports running big data analytics in an ETL model. For more information, see [Azure Databricks](/azure/databricks/introduction/).



- **Parallel processing**. The level of parallel processing during ETL matters.  
There is a requirement that you need to be able to process certain amount of data in a certain amount of amount of time. Some use cases rely heavily on real-time data updates, while others can tolerate less frequent updates. And the tool that you choose needs to be able to support that, and one of the characteristics there is you know how the parallelism, you know, characteristics of that too.

- Volume of data.

- Support for search indexing.
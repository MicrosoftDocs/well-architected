---
title: Test and Evaluate AI Workloads on Azure
description: Learn about AI workload testing operations and metrics that help you maintain the quality of your workload on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: conceptual
---

# Test and evaluate AI workloads on Azure

The purpose of testing in AI workloads is to help **ensure quality when a change is introduced to the system**. Testing can validate whether the workload meets identified targets and fulfills user expectations. It also prevents quality regressions. This process includes conducting tests across various functional areas and assessing the quality of functionality, load handling, predictability, and other criteria based on workload requirements.

Test results provide critical **data points for decisions** like whether AI components are ready for release and which SKUs or features are appropriate. Additionally, testing can serve as a **notification system for failures** and help detect problems in production through routine or synthetic tests.

The Azure Well-Architected Framework outlines a comprehensive testing methodology. You should **use various types of tests at different stages of the development life cycle and across different system components and flows**. Without these tests, rolled-out changes can degrade system quality. For example, minor code errors might become large system failures. System behavior might become unpredictable or produce biased results because of the nondeterministic nature of AI systems. Also, resource allocation might be inefficient, and real user data or system resources can be exploited because these systems are vulnerable to abuse.

You must **design and develop workload assets with testing in mind**. For instance, when you perform data manipulation and reshape source data for feature engineering, adhere to good coding practices and ensure that you structure the code to support testing. This strategy includes designing the code to facilitate effective unit testing and isolating the tests from the code's functionality and its dependencies. In this example, you must design a system that can perform in a test environment with sufficiently representative test data in terms of volume and likeness.

You must deploy workload components to production safely. Part of any workload's safe deployment practices is strategic testing to help ensure correct behavior before users or data consume the system. Strategic testing is essential during the initial deployment and as the system evolves and undergoes code or infrastructure changes. Test all proposed changes to AI-related code and infrastructure before you deploy the changes to production.

This article focuses on applying that methodology to the AI aspects of the architecture. How to conduct those tests isn't in scope.

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
|**Define success metrics for your testing strategy**. |Like any other type of workload testing, you need to capture and analyze the appropriate metrics for a given test to ensure that your test provides useful insights about your AI workload.<br><br>&#9642; [Define success metrics](#define-success-metrics)|
|**Conduct end-to-end testing of your data ingestion and processing pipelines throughout the development life cycle**. |Tests can validate the data and help you ensure that the data manipulation process works as intended. Incorporate testing early in the design phase to ensure data quality and appropriate technology and sizing choices. Develop unit tests for custom code during development, and conduct real-time production tests to catch problems and validate functionality.<br><br>&#9642; [Test data ingestion](#test-data-ingestion)|
|**Run tests for training jobs to make sure that the scripts are invoked and function as expected**. |Load and performance testing can provide insight into the choice and sizing of compute that's suitable to run the jobs. Unit tests can validate the utility of the code and catch regressions when dependencies are updated.<br><br>&#9642; [Test the training workflow](#test-the-training-workflow)|
|**Evaluate and test the model to avoid duplication in training, evaluation, and testing data**.|To ensure that source data isn't entirely used for training, you must reserve unique data for model evaluation and final testing. These subsets aren't included in the actual training process.<br><br>&#9642; [Evaluate and test the model](#evaluate-and-test-the-model)|
|**Test the inference endpoint**. |Conduct load testing on the endpoint that your inference server hosts and choose GPU SKUs based on those test results. For platform as a service (PaaS)-hosted endpoints, test throughput and the potential failures. These endpoints are reachable, so conduct proper security testing to prevent jailbreaking situations.<br><br>&#9642; [Test the inference endpoint](#test-the-inference-endpoint)|
|**Test the correctness of the index design so that queries yield relevant results**.|Functional and integration testing helps you ensure that data is accurate, and index schema testing checks for backward compatibility. You must test preprocessing steps for quality. Load testing determines suitable SKUs for resources, and security controls protect data confidentiality.<br><br>&#9642; [Test the grounding data](#test-the-grounding-data)|
|**Test the orchestrator to validate its functionality and security**.|Conduct unit, functional, integration, and runtime tests, including load and failure mode testing, to ensure performance and reliability. Security and content safety testing are also crucial to protect the system and data.<br><br>&#9642; [Test the orchestrator](#test-the-orchestrator)|
|**Test for model decay**.|Model decay is an inevitable problem that affects most AI workloads. Testing for data and concept drift can help you catch model decay early and mitigate the problem before it adversely affects your workload.<br><br>&#9642; [Prevent model decay](#prevent-model-decay)|

## Define success metrics

We recommend that you have a baseline and measure the predictive power of the model by using well-defined metrics. Here are some common metrics.

- **Accuracy** represents the ratio of correctly predicted instances to the total instances in the test dataset. It's a common measure of overall model performance.

- **Precision** is the ratio of true positive predictions to the sum of true positives and false positives. It's useful when minimizing false positives is important, like in medical diagnoses, for example.

- **Sensitivity** measures the ratio of true positives to the sum of true positives and false negatives. It's valuable when avoiding false negatives, or missing relevant cases, is critical.

- **Specificity** calculates the ratio of true negatives to the sum of true negatives and false positives. It's relevant when you optimize for accurate negative predictions.

> [!NOTE]
> When you define success metrics for regression models, consider adding the following metrics:
>
> - **Mean Absolute Error (MAE)** measures the average absolute difference between the predicted values and the actual values. Calculate it by taking the mean of the absolute differences between each actual value and its corresponding predicted value. MAE is less sensitive to outliers compared to MSE and RMSE.
>
> - **Mean Squared Error (MSE)** measures the average squared difference between the actual values and the predicted values. Calculate it by taking the mean of the squared differences between each actual value and its corresponding predicted value. MSE penalizes larger errors more heavily than MAE because the errors are squared.
>
> - **Root Mean Squared Error (RMSE)** is the square root of the MSE. It provides a measure of the average absolute error between the actual and predicted values, but in the same units as the original data. RMSE is more sensitive to outliers compared to MAE because it squares the errors before averaging.

## Test data ingestion

Data pipelines, like extract, transform, and load (ETL) processes, move, and manipulate data. Test the ETL portion of the workload to make sure that it ingests data reliably and that the data is high quality and acceptable for analysis and feature engineering. Make sure that data cleansing and processing include tests to confirm that data manipulation functions as intended.

Testing should be integrated throughout the life cycle, including the design, development, and production stages.

### Test to facilitate design choices

When you gather requirements for the workload, a key decision-making step is to choose a specific technology option that's viable for your design.

Based on your ETL requirements and acceptance criteria, conduct exploratory functional tests to select the most suitable product, its SKUs, and features that perform the intended tasks. Proof of concept, proof of technology, and proof of capabilities tests are essential to evaluate whether you're choosing the right technology and whether it's sized appropriately.

For scenarios where you incorporate AI into an existing architecture, test how well the new technology can integrate with the current system.

Initial workload requirements can change. Suppose the business anticipates growth and the system needs to handle double the regular user queries. This expectation requires proper capacity planning. We recommend proactive testing to understand how the system responds to the extra data and to make data-driven adjustments to existing sizing or make new product choices. For capacity testing, we recommend that you combine functional testing with load and performance testing and use synthetics to simulate realistic conditions.

### Test to ensure code quality

When code is included, ensure that it goes through unit testing and isn't released if it fails. For example, it's common to have custom code that runs as part of ingestion. There's also code used for data cleansing and processing. Run unit tests to ensure the code behaves according to its design and that data manipulation functions as expected.

Run tests as part of your continuous integration and continuous delivery pipeline.

### Test in the live system

Functional testing should extend to the live system. If these tests fail, consider triggering alerts to initiate immediate investigations, if necessary. Here are some examples:

- Run scheduled tests to verify that the correct volume of data was collected if data is ingested on a set schedule with an expected amount.

- Run tests that detect data problems, such as missing values or duplicate data, and perform basic data integrity checks. If data contains temporal information that indicates its freshness, these tests can check that information and potentially prevent downstream processes from using stale data.

- Check the availability of external dependencies. For example, a data cleansing job might call another service for extracting tables or preprocessing. Run tests to ensure that they're available because their unavailability could affect the ETL process.

Another way to test the correctness of the ETL system in production is through synthetic testing. Having known test data available in production is highly effective. You can use it to validate end-to-end processing by comparing the known starting state with the expected end state for that data. For example, a document is required to go through document intelligence and contains personal data. Injecting a synthetic document can test that the workload performs the data manipulation job as intended.

Additionally, experiment by releasing different experiences, also known as *A/B testing*, to learn from user interactions before fully committing. A/B testing helps you prevent quality regressions.

### Test data that's collected during ingestion

As part of the ingestion process from various data sources, include tests to validate that training data matches your expectations.

Training a machine learning model with incomplete or corrupted data can be counterproductive. It might lead to wasted efforts and result in a model that fails to make meaningful predictions. Your data ingestion and preprocessing pipelines include quality tests as checkpoints. These tests can help you verify that your data aligns with the expectations that you set during data analysis and feature engineering.

The following list includes some example test cases:

- **Test for completeness.** Test the expected quantity of training data to verify the completeness of the dataset. This test ensures that you provide enough data to train the model.

- **Test for critical information.** If training is based on known entities, such as specific records or identifiers, test the dataset to make sure that those entities are present. This validation ensures that critical information isn't missing.

- **Test for irrelevant data.**  Ingested data shouldn't contain irrelevant or erroneous entries. The data ingestion process should filter out that data.

- **Test for freshness.** Freshness of the ingested data shouldn't affect the model's predictive power. Validate that the data reflects reasonably current information and isn't outdated from previous runs. For example, if you expect the data to include records from the past week, but there are no such records after you import the data, that might indicate a failed import or a data freshness problem in the source system.

### Conduct routine tests

A significant concern with data ingestion is the volume of data and throughput. Continuous evaluation during operations is necessary to prevent performance bottlenecks. This ongoing assessment should be part of your operational processes rather than just a one-time test. The goal is to ensure the workload team doesn't miss their service-level objectives.

Consider a situation where monitoring indicates performance degradation. To mitigate such conditions, reevaluate and optimize the ETL processes. After you make changes, performance tests can help you make sure that the modifications meet the required throughput.

> [!NOTE]
> Testing and monitoring serve different purposes. Conduct tests to evaluate potential changes to the system, typically before you implement any changes. Conduct monitoring continuously to assess the overall health of the system.

## Test the training workflow

Train a model by using custom code, such as PyTorch scripts, which do the actual training work. These scripts run on compute, such as in notebooks or Azure Machine Learning jobs, which also require memory and networking resources. We recommend load testing during the design phase to evaluate compute needs and ensure that the proposed SKUs are suitable. You often need manual testing to determine the best configuration to efficiently run the workload within the time limit.

Write the scripts by using specialized SDKs, which handle most of the tasks. However, because scripts are still code, you should integrate unit testing as part of development. These tests help you ensure that no regressions occur when you update dependencies. If unit testing isn't possible, manual testing is necessary before you deploy new code to prevent quality regressions.

These scripts run as part of a workflow, like Azure Machine Learning studio, which can provide insight as to when and whether the script ran. But we recommend that you run integration tests to make sure that these scripts are invoked reliably.

## Evaluate and test the model

> [!NOTE]
> Model evaluation and testing are often used interchangeably, but they should be considered separate processes that use distinct datasets. Evaluation is an iterative activity that you do during the development phase. It focuses on experimentation to find the best model with the right level of tuning. It includes adjusting hyperparameters, configurations, or features and then evaluating the model based on various metrics. After you identify the best model, conduct tests during deployment.
>
> Testing includes verifying the entire system, including the tuned model and non-AI components, to check that they function correctly, integrate well, and deliver the expected results in accordance with quality standards. Evaluate a model in situ alongside other components of the workload. The process includes sending requests to the model, evaluating its responses, and making a go or no-go decision based on the test data. Although testing is nonnegotiable before production, we recommend that you also conduct tests in production by using real data and synthetic data.

### Use data to evaluate and test

Typically there are three key datasets partitioned from the source data: training, evaluation, and testing.

Use the training dataset, which is usually the largest subset, to train the model. Use another dataset for evaluation to refine the model through an iterative process by assessing different permutations. After you find a satisfactory permutation, test it against the test dataset.

All datasets should contain high-quality data to minimize noise. Your test cases on data ingestion and preprocessing pipelines can serve as quality checkpoints. A lack of samples can also attribute to low-quality data. Use synthetic data to balance and achieve uniformity in the dataset. This approach is useful for training models like fraud detection models, where real fraud instances are rare, which makes it difficult to get sufficient statistical power for reliable predictions.

To avoid bias in predictions, keep all datasets distinct. You shouldn't use training data for evaluation, and you shouldn't use evaluation data for testing. Reserve unique data for model evaluation and final testing.

### Use evaluation metrics

Training a model and selecting the right one for production are interdependent processes. You need to choose a model initially, but it might change after experimentation and evaluation.

Model evaluation follows as an experimentation loop that assesses numerous permutations of models, parameters, and features by using metrics. These metrics provide scientific ratings, which you must iteratively compare across different versions and configurations to determine the best model. For more information, see [Evaluation metrics](/azure/machine-learning/component-reference/evaluate-model#metrics).

A similar approach applies to generative AI models. Have processes that evaluate and quantify results of the user experience based on the performance of the model. For example, groundedness is one of the key metrics that quantifies how well the model aligns with source data. Relevancy is another important metric that indicates how pertinent the response is to the query. For example metrics, see [Evaluation and monitoring metrics for generative AI](/azure/ai-studio/concepts/evaluation-metrics-built-in).

Evaluate different types of models by using various metrics. The importance of each metric can vary depending on the scenario. Prioritize metrics based on the use case. For example, fairness is crucial in responsible AI. Despite good testing, models can still exhibit unfair bias because of biased source data. Results might score high in relevancy but low in fairness. Integrate fairness evaluations into the process to ensure unbiased outcomes.

Generative AI integrates with orchestration code, routing logic, and an index for retrieval-augmented generation (RAG), which complicates evaluation. Although you should assess the models individually by using metrics, it's also important to evaluate other system components.

### Test the model

Fine-tuning is essentially testing because it modifies a pretrained model to change its behavior. It requires starting with a baseline to understand the model's initial performance. After fine-tuning, reevaluate the model's performance to ensure that it meets quality standards. Consider the following common evaluation metrics:

- **Groundedness** refers to the model's alignment with the source data. A grounded model generates answers that are consistent with reality.

- **Relevancy** indicates how pertinent the response is to a given question. A highly grounded answer might lack relevancy if it doesn't address the question directly.

- **Similarity** measures the similarity between a source data text and the generated response. Did the model use precise wording? A lack of editorial governance can lower the similarity score.

- **Retrieval** indicates the effectiveness of index queries. Evaluate how well the retrieved index data aligns with the question. Irrelevant data from the index search lowers this score. Higher retrieval scores indicate lower variability because they rely solely on the index queries.

- **Fluency** relates to the vocabulary usage. If the model adheres to a style guide and presents content in the appropriate format, it can be fluent even if it lacks grounding or relevancy.

- **Coherence** evaluates whether the model's speech flows naturally and coherently. It assesses whether the conversation feels like a genuine exchange.

### Test hyperparameters

Model parameters depend on application-specific design decisions. As part of your application design, choose the model and the parameters based on your workload's use cases. The testing process has an iterative inner loop where training data is compared against test data to validate that the model is training on the intended dataset. Also, the parameters are tuned so that the model can make predictions with an acceptable level of accuracy.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off.** The inner loop includes computational costs of training the model and the cost of evaluating it through tests. You must factor the time that model training and testing requires into this loop. Expect the testing process to take longer than the training process. You can do initial testing on a subset of training data to assess whether the model produces reasonable results. You can gradually scale up that testing set to the full dataset.

## Test the inference endpoint

An inference endpoint is the REST API that allows access to models for making predictions. It's the interface where you send data as part of the request and get a response that contains results from the model. The endpoint is hosted on compute, which can be PaaS like Azure OpenAI Service or a non-Microsoft inferencing server, such as NVIDIA Triton Inference Server, TorchServe, and BentoML. In PaaS scenarios, the service provider handles the testing to a certain extent. However, if you host the endpoint, treat it like any other API and test it thoroughly. 

Although you test the model during training and evaluation, testing the inference endpoint involves ensuring that the mechanisms around that model, such as request processing, response creation, scaling, and coordination across instances, work correctly. Create a comprehensive test plan that covers use cases based on your requirements. This section describes some example test cases and test types to consider.

### Test considerations for inference hosting servers

It's important to understand the load characteristics of the compute and validate performance through load testing. These actions help you choose technologies when you design or optimize the architecture. For example, different inferencing servers have varying performance characteristics. The code consumes CPU cycles and memory as the number of concurrent connections increases. Understand how your code and the compute resources perform under standard and peak load conditions. Azure Load Testing is a good option for load testing and can generate high volume load. Other open-source options, like Apache JMeter, are also popular. Consider invoking these tests directly from your environment. For example, Machine Learning integrates well with Load Testing.

Another key decision is the choice of GPU capabilities. Many models require GPUs for effective inferencing because of their design. Load testing helps you understand the performance limits of a GPU SKU and prevents overprovisioning, which are significant financial considerations.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off.** GPU SKUs are expensive. Although you might make conservative choices in your SKU selection, it's important to continuously check whether GPU resources are underused and rightsize them, when possible. After you make adjustments, test resource usage to maintain the balance between cost efficiency and performance optimization. For cost optimization strategies, see [Recommendations for optimizing component costs](../cost-optimization/optimize-component-costs.md).

For non-PaaS hosting platforms, security is crucial because the API is publicly exposed. It's important to ensure that the endpoint isn't exploited or compromised, which can jeopardize the entire system. Include this endpoint as part of your routine security testing along with other public endpoints. Consider conducting tests, such as penetration testing, on the live system. Another aspect of security is content safety. Your code can call out to specialized APIs that detect harmful content in the request and response payload. Azure AI Content Safety can be called from your testing to facilitate content safety testing.

For key strategies, see [Recommendations for security testing](../security/test.md).

### Test considerations for PaaS inference endpoints

The client should expect errors when it sends requests to the inference endpoint on the PaaS service. Failures can occur because of system overload, unresponsive back ends, and other error conditions. Conduct failure mode analysis on the service and test those potential failures. Failure mode analysis is required for designing and implementing mitigation strategies in the client code. For example, Azure OpenAI APIs throttle requests by returning an HTTP 429 error response code. The client should handle that error by using retry mechanisms and circuit breakers. For more information, see [Recommendations for performing failure mode analysis](../reliability/failure-mode-analysis.md).

Testing PaaS services can help you select service SKUs because you understand the associated costs, like pay-as-you-go or pre-provisioned compute. Use Azure pricing calculators to evaluate workloads, frequency, and token usage to determine the best billing and compute options. Simulate workloads with low-cost SKUs and justify high-end options like provisioned throughput units (PTUs) for Azure OpenAI.

Load testing isn't as relevant for pay-as-you-go compute because, with infinite capacity, you don't encounter problems. Testing validates the limits and quotas. We don't recommend load testing for pay-as-you-go compute because it's a significant financial expense. However, you should load test to verify the throughput, which is measured in tokens per minute or requests per minute. Unlike standard APIs that consider metrics like request size, this approach evaluates workloads based on tokens to determine usage. The key is to understand the number of active users and measure throughput accordingly. For more information, see [Measure your throughput](/azure/ai-services/openai/how-to/latency#how-to-measure-your-throughput).

### Use security controls

Regardless of whether you use an inferencing server or a PaaS option, security is your responsibility. With API endpoints, it's crucial to test for jailbreaking and content safety controls. Make sure that these controls can't be bypassed and are functioning as expected. For instance, sending a known blocked item can help you verify if security controls are in place and working correctly before deployment. Consider running these tests as needed or integrate them into the release process.

It's important to test if the system can inadvertently expose information it shouldn't. For example, the system shouldn't expose personal information in the response payload. Also, test to make sure a client can't access endpoints that are meant for other identities. Conduct security tests to verify that the API, with its authentication and authorization mechanisms, doesn't leak confidential information and maintains proper user segmentation.

## Test the grounding data

Data design influences a generative model's efficiency, and grounding data is the critical component. Grounding data provides more context to boost the relevance of the response. It's indexed before it reaches the model. This index is accessed in real time as the user waits for an answer.

Conduct end-to-end testing and incorporate that process as part of data design. Implement a testing process that evaluates and quantifies the results of the customer's experience based on the model's performance, orchestration, index, preprocessing, and source data. Monitor and measure the quality metrics iteratively. Here are some considerations:

- Thoroughly test data processing by using functional and integration testing. Verify that data is loaded as expected and that all data is present.

- Test the index schema for backward compatibility. You should test any change to a document or field to ensure that the new version can still accommodate previous versions of data.

- Before data is indexed, it undergoes preparation to reduce noise and bias and enable efficient querying. This process includes preprocessing, chunking, and calculating embeddings, and each step saves data to the context or files in the index. An orchestration pipeline, such as skill sets provided by Azure AI Search, conducts these steps. You must test the orchestration code to ensure that no steps are missed and the processed data is high quality. 

   Tests should check for old data, synthetic values, empty tables, data refresh, and processing on the latest version. If test failures occur, you might need to tweak the search query and index. This process includes adjusting filters and other elements discussed previously. You should think of testing as an iterative activity.

- Indexes are complex and query performance can vary based on index structure, which requires load estimation. Proper load testing can help determine the different SKUs for storage, compute, and other resources that are available to support your requirements.

- You must test all security controls. For example, data might be partitioned into separate documents. Each partition has access controls. You must properly test those controls to protect confidentiality.

## Test the orchestrator

A key component of a RAG application is the central orchestrator. This code coordinates various tasks that relate to the initial user question. Orchestrator tasks typically require an understanding of user intent, a connection to the index to look up grounding data, and calling the inference endpoint. If agents need to do tasks, such as calling REST APIs, this code handles those tasks within the context.

You can develop orchestration code in any language or write it from scratch. However, we recommend that you use technologies like prompt flow in [Azure AI Foundry portal](https://ai.azure.com/) or Apache Airflow's Directed Acyclic Graphs (DAGs) to speed up and simplify the development process. Prompt flow provides a design-time experience. Use it to modularize tasks as units and connect inputs and outputs of each unit, ultimately forming the orchestration code, which represents the entire process.

Isolate your orchestration code. **Develop it separately and deploy it as a microservice** with an online endpoint and REST API for access. This approach ensures modularity and ease of deployment.

From a testing perspective, treat this code like any other code and **conduct unit tests**. However, the more important aspect is its functionality, such as its routing logic, which you can validate through functional and integration testing. **Test prompt engineering to ensure that the code can detect user intent and route calls appropriately**. There are several frameworks and libraries for testing, such as Scikit-learn, PyTorch's torch.testing module, FairML for bias and fairness testing, and TensorFlow Model Analysis for model evaluation.

Also, conduct runtime tests, such as failure mode testing. For example, test potential failures that are related to token limitations.

Certain runtime tests can help you make a decision. **Run load tests** to understand how this code behaves under stress and use the results for capacity planning. Because this code is positioned at a crucial point in the architecture where it needs to reach other services, it can help collect telemetry from all those calls. This data can provide insights into how much time is spent on local processing versus network calls and determine the behavior of other components, such as potential latency. Technologies like prompt flow have built-in telemetry capabilities to facilitate this process. Otherwise, incorporate telemetry in your custom code.

> [!NOTE]
>
> Testing this code has cost implications. For example, if you use Azure OpenAI to host your inference endpoint, stress testing is a common practice that can help you determine the system's limits. However, Azure OpenAI charges for every call, which can make extensive stress testing expensive. One way to optimize charges is to use unused PTUs of Azure OpenAI in a test environment. Alternatively, you can simulate the inference endpoint.


Security concerns apply to both the orchestration code and the model. **Include testing for jailbreaking**, where the goal is to break the model's security. Attackers don't interact with the model directly. They interact with the orchestration code first. The orchestration code receives user requests and parses them. If the orchestration code receives a malicious request, it can forward that request to the model and potentially compromise the model.

Content safety is another important aspect. In a chatbot application, orchestration code receives chat text. In the code, **consider calling a content safety service**. Send both the user prompt and the grounding context for analysis and receive an assessment of the risk. [Prompt Shields](/azure/ai-services/content-safety/concepts/jailbreak-detection) is a unified API that analyzes large language model inputs and detects user-prompt attacks and document attacks, which are two common types of adversarial inputs.

**Security control and authentication are crucial** for a RESTful endpoint. You need to manage authentication and ensure thorough testing.

## Prevent model decay

A common problem for all models is some degree of degradation over time. Changes that are internal and external to the workload eventually cause a degradation in the quality of the model and its outputs. Model decay occurs in two ways:

- **Data drift** occurs when the input data changes. New data input makes the trained model out of date. For example, you might have a model that predicts voting patterns of a certain geographical area, like a district. If the district is redrawn and the demographics of the population of that district change, your model needs to be updated to account for the changes.

- **Concept drift** occurs when conditions that are external to the workload and model change in such a way that the model outputs no longer match reality. For example, you might have a sales forecast model for a technology product. If a competitor unexpectedly introduces a more advanced competing product that draws significant attention from the public, you need to update your model based on how consumer trends change.

When possible, use automated testing to detect and evaluate model decay over your model's life cycle. If your model predicts discrete values, you can create tests to evaluate predictions against those values over time and measure the deviation between expected and actual results. Complement this testing with monitoring to detect drift over time by comparing summary statistics and distance metrics.

Another common approach to identify model decay is user feedback. An example of user feedback is a thumbs up or thumbs down response mechanism. Tracking the positive versus negative feedback over time and creating an alert when you meet a negative feedback threshold can help you know when to investigate the quality of the model.

Regardless of the signals that you use to identify model decay, the operations team that's alerted about potential decay needs to engage a data scientist to research the signal and determine whether decay is happening and the root cause.

### Implement tools

Use **[Azure Machine Learning data collector](/azure/machine-learning/concept-data-collection)** to get real-time logging of input and output data from models that are deployed to managed online endpoints or Kubernetes online endpoints. Machine Learning stores the logged inference data in Azure blob storage. You can then use this data for model monitoring, debugging, or auditing, which provides observability into the performance of your deployed models. If you deploy a model outside of Machine Learning or to a Machine Learning batch endpoint, you can't take advantage of data collector and need to operationalize another data collection process.

Use **Machine Learning model monitoring** to implement monitoring. Machine Learning acquires monitoring signals by performing statistical computations on streamed production inference data and reference data. The reference data can be historical training data, validation data, or ground truth data. On the other hand, the production inference data refers to the model's input and output data collected in production.

- See [Machine Learning model monitoring](/azure/machine-learning/concept-model-monitoring) to learn about the monitoring capabilities of Machine Learning and the [metrics](/azure/machine-learning/concept-model-monitoring#monitoring-signals-and-metrics) that it captures and analyzes.
- See the [best practices](/azure/machine-learning/concept-model-monitoring#best-practices-for-model-monitoring) for more recommendations for monitoring.

## Next steps

> [!div class="nextstepaction"]
> [Design area: Responsible AI](./responsible-ai.md)

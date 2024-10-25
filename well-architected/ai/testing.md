---
title: Testing and Evaluation of AI Workloads on Azure
description: Learn about AI workload testing operations on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/25/2024
ms.topic: conceptual
---

# Testing and evaluation of AI workloads on Azure

The purpose of testing in AI workloads is to help maintain quality when a change is introduced to the system. Testing can validate whether identified targets are met, user expectations are fulfilled, and regression in quality is prevented. This process includes conducting tests across various functional areas, assessing the quality of functionality, load handling, predictability, and other criteria based on workload requirements.

Test results provide critical data points for decision making, such as assessing the readiness of AI components for release and selecting the appropriate SKUs or features. Additionally, testing can serve as a notification system for failures and help detect issues in production through routine or synthetic tests.

The Azure Well-Architected Framework outlines a comprehensive testing methodology. You should use various types of tests at different stages of the development life cycle and across different system components and flows. Without these tests, rolled-out changes can degrade system quality. For example, minor code errors might escalate into large system failures. System behavior could become unpredictable or produce biased results because of the nondeterministic nature of AI systems. Also, resource allocation could be inefficient, and real user data or system resources can be exploited, because these systems are vulnerable to abuse.

You must design and develop workload assets with testing in mind. For instance, when you perform data manipulation and reshape source data for feature engineering, adhere to good coding practices and ensure that the code is structured to support testing. This strategy includes designing the code to facilitate effective unit testing and isolating the tests from the code's functionality and its dependencies. In this example, designing a system that can perform in a test environment, with sufficiently representative test data in terms of volume and likeness, is required.

Workload components must be deployed to production safely. Part of any workload's safe deployment practices include strategic testing to help ensure correct behavior before users or data consume the system. Strategic testing is essential not only during the initial deployment but also as the system evolves and undergoes code or infrastructure changes. Test all proposed changes to AI-related code and infrastructure before you deploy the changes to production.

This article focuses on the application of that methodology to the AI aspects of the architecture. How to conduct those tests isn't in scope.  

|Recommendation|Description|
|---|---|
|Define success metrics for your testing strategy. |Like any other type of workload testing, you need to capture and analyze the appropriate metrics for a given test to ensure that your test provides you with useful insights about your AI workload.<br><br>&#9642;[Define success metrics](#define-success-metrics)|
|Conduct end-to-end testing of your data ingestion and processing pipelines throughout the development lifecycle. |Tests can validate that right data and data manipulation process work as intended. Incorporate testing early in the design phase to ensure data quality and appropriate technology and sizing choices, develop unit tests for custom code during development, and conduct real-time production tests to catch issues or validate functionality.<br><br>&#9642; [Tests for data ingestion and processing pipelines](#data-ingestion-testing)|
|Run tests to validate that the training scripts are invoked and function as expected.|Load and performance testing can provide insight into the choice and sizing of compute suitable to run the jobs. Unit tests can validate the utility of the code and catch regressions when dependencies are updated.<br><br>&#9642; [Tests for training jobs](#training-workflow-testing)|
|Avoid duplication in training, evaluation, testing data.|To ensure source data isn't entirely used for training, unique data must be reserved for model evaluation and final testing. These subsets aren't included in the actual training process. <br><br>&#9642; [Model evaluation and testing](#model-evaluation-and-testing)|
|Test the inference endpoint. |Conduct load testing on the endpoint hosted by your inference server and influence your decision making on GPU SKU choices based on test results. For platform as a service (PaaS)-hosted endpoints, test throughput and the potential failures. Given that these are reachable endpoints, conduct proper security testing to prevent jailbreaking situations. <br><br>&#9642; [Inference endpoint testing](#inference-endpoint-testing)|
|Test the correctness of the index design so that queries yield relevant results.|Functional and integration testing ensures data accuracy, while index schema testing checks for backward compatibility. Preprocessing steps must be tested for quality. Load testing determines suitable SKUs for resources, and security controls protect data confidentiality. <br><br>&#9642; [Grounding data testing](#grounding-data-testing)|
|Test the orchestrator to validate its functionality and security.|Conduct unit, functional, integration, and runtime tests, including load and failure mode testing, to ensure performance and reliability. Security and content safety testing are also crucial to protect the system and data. <br><br>&#9642; [Orchestrator testing](#orchestrator-testing)|
|Test for model decay.|Model decay is an inevitable issue that affects most AI workloads. Testing for data and concept drift can help you catch model decay early and mitigate the issue before your workload is adversely affected.<br><br>&#9642; [Model decay](#model-decay)

## Define success metrics

It's recommended that you have a baseline and measure predictive power of the model using well-defined metrics. Here are some common metrics.

- **Accuracy:** This metric represents the ratio of correctly predicted instances to the total instances in the test dataset. It's a common measure of overall model performance.

- **Precision:** This metric is the ratio of true positive predictions to the sum of true positives and false positives. It's particularly useful when minimizing false positives is important (like medical diagnoses, for example).

- **Sensitivity:** This metric measures the ratio of true positives to the sum of true positives and false negatives. It's valuable when avoiding false negatives (missing relevant cases) is critical.

- **Specificity:** This metric calculates the ratio of true negatives to the sum of true negatives and false positives. It's relevant when optimizing for accurate negative predictions.

> [!NOTE]
> When you define success metrics for regression models, consider adding the following metrics:
>
> - **Mean Absolute Error (MAE):** This metric measures the average absolute difference between the predicted values and the actual values. It is calculated by taking the mean of the absolute differences between each actual value and its corresponding predicted value. MAE is less sensitive to outliers compared to MSE and RMSE.
>
> - **Mean Squared Error (MSE):** This metric measures the average squared difference between the actual values and the predicted values. It is calculated by taking the mean of the squared differences between each actual value and its corresponding predicted value. MSE penalizes larger errors more heavily than MAE because the errors are squared.
>
> - **Root Mean Squared Error:** This metric is the square root of the MSE. It provides a measure of the average absolute error between the actual and predicted values, but in the same units as the original data. RMSE is more sensitive to outliers compared to MAE because it squares the errors before averaging.

## Data ingestion testing

Data pipelines, essentially ETL (Extract, Transform, Load) processes, involve data movement and manipulation. Test the ETL portion of the workload to ensure reliable data ingestion and high quality data that's acceptable for analysis and feature engineering. Additionally, make sure that data cleansing and processing include tests to confirm that data manipulation functions as intended.

Testing should be integrated throughout the lifecycle, design, development, and in production.

#### Test to facilitate design choices

When you gather requirements for the workload, a key decision-making step is to choose a specific technology option that's viable for your design.

Based on your ETL requirements and acceptance criteria, conduct exploratory functional tests to select the most suitable product, its SKUs, and features that perform the intended tasks. Proof of concept, proof of technology, and proof of capabilities tests are essential for evaluating whether you're choosing the right technology and if it's sized appropriately.

For scenarios where AI is incorporated into an existing architecture, test how well the new technology can be integrated with the current system.

Initial workload requirements can change. Suppose the business anticipates growth and the system is expected to handle double the regular user queries. This expectation would require proper capacity planning. Proactive testing is recommended to understand how the system responds to the extra data and to make data-driven adjustments to existing sizing or make new product choices. For capacity testing, we recommend combining functional testing with load and performance testing, and using synthetics to simulate realistic conditions.

#### Test to ensure code quality

When code is included, ensure that it goes through unit testing and isn't released if it fails. For example, it's common to have custom code that runs as part of ingestion. There's also code used for data cleansing and processing. Run unit tests to ensure the code behaves according to its design and that data manipulation functions as expected.

Run tests as part of your continuous integration and continuous delivery pipeline.

#### Test in the live system

Functional testing should extend to the live system. If these tests fail, consider triggering alerts to initiate immediate investigations, if necessary. Here are some examples:

- Run scheduled tests to verify that the correct volume of data was collected if data is ingested on a set schedule with an expected amount.

- Run tests that detect data issues, such as checking for missing values or duplicate data, and performing basic data integrity checks. Suppose data contains temporal information used to indicate its freshness. These tests can check against an acceptance time window, potentially preventing the use of stale data in downstream processes.

- Check the availability of external dependencies. For example, a data cleansing job might call another service for extracting tables or preprocessing. Running tests can ensure they're available, as their unavailability could affect the ETL process.

Another way to test the correctness of the ETL system in production is through synthetic testing. Having known test data available in production is highly effective. It allows you to validate end-to-end processing by comparing the known starting state with the expected end state for that data. For example, a document is required to go through document intelligence and contains PII data. Injecting a synthetic document can test that the data manipulation job was performed as intended.

Additionally, conduct experimentation by releasing different experiences (A/B testing) to learn from user interactions before fully committing, ensuring no regression in quality.

#### Test data collected during ingestion

As part of ingestion from various data sources, include tests to validate that training data matches your expectations.

Training a machine learning model with incomplete or corrupted data can be counterproductive. It might lead to wasted efforts and result in a model that fails to make meaningful predictions. Your data ingestion and preprocessing pipelines include quality tests as checkpoints. These tests help verify that your data aligns with the expectations set during data analysis and feature engineering.

The following are some example test cases:

- **Test for completeness.** Test the expected quantity of training data, to verify the completeness of the data set. This test ensures that enough data was provided to train the model.

- **Test for critical information.** If training is based on known entities, such as specific records, identifiers, or entities, test the data set to validate that those entities are present. This validation ensures that critical information isn't missing.

- **Test for irrelevant data.**  Ingested data shouldn't contain irrelevant or erroneous entries. The data ingestion process should filter out that data.

- **Test for freshness.** Freshness of the ingested data shouldn't affect the model's predictive power. Validate that the data reflects reasonably current information and isn't outdated from previous runs. For example, if the data is expected to include records from the past week, but there are no such records after importing, it could indicate a failed import or a data freshness issue in the source system.

#### Conduct routine tests

A significant concern with data ingestion is the volume of data and throughput. Continuous evaluation during operations is necessary to prevent performance bottlenecks. This ongoing assessment should be part of your operational processes rather than just a one-time test. The goal is to ensure the workload team doesn't miss their service-level objectives.

Consider a situation where monitoring indicates performance degradation. To mitigate such conditions, reevaluate and optimize the ETL processes. After you make changes, performance tests can help the modifications meet the required throughput.

> [!NOTE]
> Testing and monitoring serve different purposes. Testing is conducted to evaluate potential changes to the system, typically performed before implementing any changes. Alternatively, monitoring includes continuously assessing the overall health of the system.

## Training workflow testing

A model is trained using custom code, such as PyTorch scripts, which do the actual training work. These scripts run on compute, such as in notebooks or Azure Machine Learning jobs, which also require memory and networking resources. Load testing is recommended during the design phase to evaluate compute needs and ensure the proposed SKUs are suitable. Manual testing is often needed to determine the best configuration for efficient execution within the time budget.

The scripts are written by using specialized SDKs, which handles most of the tasks. However, because this is still code, you should integrate unit testing as part of development. These help ensure no regressions occur when dependencies are updated. If unit testing isn't possible, manual testing is necessary to prevent quality regressions before deploying new code.

These scripts run as part of a workflow, like Azure Machine Learning Studio, which can provide insight as to when and whether the script ran. But it's recommended that you run integration tests to make sure these scripts are invoked reliably.

## Model evaluation and testing

> [!NOTE]
> Model evaluation and testing are often used interchangeably, but they should be considered separate processes using distinct datasets. Evaluation is an iterative, development-time activity focused on experimentation to find the best model with the right level of tuning. It includes adjusting hyperparameters, configurations, or features and then evaluating the model based on various metrics. After the best model is identified, testing is conducted during deployment.
>
> Testing includes verifying the entire system, including the tuned model and non-AI components, to validate if they function correctly, integrate well, and deliver the expected results with quality standards. A model under test is evaluated in-situ alongside other components of the workload. The process includes sending requests to the model, evaluating its responses, and making a go/no-go decision based on the test data. While testing is a non-negotiable process before production, it's recommended that tests are also conducted in production using real data complemented with synthetic data.

#### Data used for evaluation and testing

Typically, there are three key datasets partitioned from the source data: training, evaluation, and testing.

The training dataset, usually the largest subset, is used to train the model. Another dataset is used for evaluation, refining the model through an iterative process by assessing different permutations. After a satisfactory permutation is found, it's tested against the test dataset.

All datasets should contain high-quality data to minimize noise. Your test cases on data ingestion and preprocessing pipelines can serve quality checkpoints. Lack of samples can also attribute to low quality data. Use synthetic data to balance and achieve uniformity in the dataset. This approach is particularly useful for training models like fraud detection, where real fraud instances are rare, making it difficult to get sufficient statistical power for reliable predictions.

To avoid bias in predictions, keep all datasets distinct. Training data shouldn't be used for evaluation, and evaluation data shouldn't be used for testing. Reserve unique data for model evaluation and final testing.

#### Evaluation metrics

Training a model and selecting the right one for production are interdependent processes. You need to choose a model initially but it might change after experimentation and evaluation.

Model evaluation follows as an experimentation loop, assessing numerous permutations of models, parameters, and features using metrics. These metrics provide scientific ratings, which must be iteratively compared across different versions and configurations to determine the best model. For more information, see [Evaluation metrics](/azure/machine-learning/component-reference/evaluate-model#metrics).  

Similar approach applies to generative AI models. Have processes that evaluate and quantify results of the end user experience based on the performance of the model. For example, groundedness one of the key metrics that quantifies how well the model aligns with source data. Relevancy is another important metric that indicates how pertinent the response is to the query. For example metrics, see [Evaluation and monitoring metrics for generative AI](/azure/ai-studio/concepts/evaluation-metrics-built-in).

Different types of models are evaluated using various metrics, and the importance of each metric can vary depending on the scenario. Prioritize metrics based on the use case. For example, fairness is crucial in responsible AI. Despite good testing, models can still exhibit unfair bias due to biased source data. Results might score high in relevancy but low in fairness. Integrate fairness evaluations into the process to ensure unbiased outcomes.

Generative AI integrates with orchestration code, routing logic, and an index for RAG, complicating evaluation. While the model should be assessed individually using metrics, it's also important to evaluate other system components.

#### Model testing

Fine-tuning is essentially testing because it modifies a pretrained model to change its behavior. It requires starting with a baseline to understand the model's initial performance. After fine-tuning, the model's performance is reevaluated to ensure it meets quality standards. Common metrics to evaluate are:

- **Groundedness:** Refers to the model's alignment with the source data. A grounded model generates answers that's consistent with reality.

- **Relevancy:** Indicates how pertinent the response is to a given question. A highly grounded answer might lack relevancy if it doesn't address the question directly.

- **Similarity:** Measures the similarity between a source data text and the generated response. Did the model use precise wording? Lack of editorial governance can lower the similarity score.

- **Retrieval:** Indicates the effectiveness of index queries. Evaluate how well the retrieved index data aligns with the question. Irrelevant data from the index search lowers this score. Higher retrieval score indicates lower variability because it's solely reliant on the index queries.

- **Fluency:** Relates to the vocabulary usage. If the model adheres to a style guide and presents content in the appropriate format, it can be fluent even if it lacks grounding or relevancy.

- **Coherence:** Evaluates whether the model's speech flows naturally and coherently. It assesses whether the conversation feels like a genuine exchange.

#### Test hyperparameters

Model parameters depend on application-specific design decisions. As part of your application design, choose the model and the parameters based on the workload use cases. The testing process has an iterative inner loop where training data is compared against test data to validate that the model is training on the intended data set. Also, the parameters are tuned so that the model is able to predict with an acceptable level of accuracy.

> ![Consider the tradeoff on cost of training.](../_images/trade-off.svg) **Tradeoff**. The inner loop includes computational cost of training the model with the cost of evaluating it through tests. The time required for model training and testing must be factored into this loop. Expect the testing process to execute for a longer period than the training process. Initial testing can be done on a subset of training data to assess whether the model produces reasonable results. That testing set can be gradually scaled up to the full dataset eventually.

## Inference endpoint testing

An inference endpoint is the REST API that allows access to models for making predictions. It's the interface where you send data as part of the request and get response from the model containing results. The endpoint is hosted on compute, which can be PaaS like Azure OpenAI or a third-party inferencing server, such as NVIDIA Triton Inference Service, TorchSever, BentoML. In PaaS scenarios, the service provider handles the testing to certain extent. However, if you host the endpoint, treat it like any other API and test it thoroughly. While the model is tested during training and evaluation, testing the inference endpoint involves ensuring the mechanisms around that model work correctly, such as request processing, response creation, scaling, and coordination across instances. Create a comprehensive test plan that covers use cases based on your requirements. This section describes some example test cases and test types to consider.

#### Test considerations for inference hosting servers

It's important to know the load characteristics of the compute and validate performance through load testing. This helps in choosing technologies when you're designing or optimizing the architecture. For example, different inferencing servers have varying performance characteristics. The code consumes CPU cycles and memory as number of concurrent connections increase. Understand how your code and the compute resources perform under standard and peak load conditions. Azure Load Testing is a good option for load testing and is capable of generating high volume load. Other open source options are also popular, such as Apache JMeter. Consider invoking these tests directly from your environment. For example, Machine Learning is well-integrated with Load Testing.

Another key decision is the choice of GPU capabilities. Many models require GPUs for effective inferencing due to their design. Load testing helps understand performance limits of a GPU SKU and prevents overprovisioning, which are a significant financial consideration.

> ![Consider the tradeoff on cost of GPU capabilities.](../_images/trade-off.svg) **Tradeoff**. GPU SKUs are expensive. While you might make conservative choices in your SKU selection, it's important to continuously detect if GPU resources are underutilized and right size them, when possible. After adjustments, test resource utilization to maintain balance between cost efficiency and performance optimization. For cost optimization strategies, see [Recommendations for optimizing component costs](../cost-optimization/optimize-component-costs.md).

For non-PaaS hosting platforms, security is crucial because this API is publicly exposed. It's important to ensure the endpoint isn't exploited or compromised, which could jeopardize the entire system. Include this endpoint as part of your routine security testing along with other public endpoints. Consider conducting test on the live system, such as penetration testing. Another aspect of security is content safety. Your code can call out to specialized APIs that detect harmful content in the request and response payload. Azure AI Content Safety can be called from your testing to facilitate content safety testing.

For key strategies, see [Recommendations for security testing](../security/test.md).

#### Test considerations for PaaS inference endpoints

The client should expect errors when sending request to the inference endpoint on the PaaS service. Failures can occur due to system overload, unresponsive backends, and other error conditions. Conduct failure mode analysis on the service and test those potential failures. This is requirement for designing and implementing mitigation strategies in the client code. For example, Azure OpenAI APIs throttle requests by returning an HTTP 429 error response code. The client should handle that error by using retry mechanisms and circuit breakers. Some strategies are given in [Recommendations for performing failure mode analysis](../reliability/failure-mode-analysis.md).

Testing PaaS services can help in selecting SKUs of the service by understanding the associated costs, pay-as-you-go, or pre-provisioned compute. Use Azure pricing calculators to evaluate workloads, frequency, and token usage to determine the best billing and compute options. Simulate workloads with low-cost SKUs and justify high-end options like provisioned throughput units (PTU) for Azure OpenAI.

Load testing isn't as relevant for pay-as-you-go because, with infinite capacity, you wouldn't encounter problems. Testing would validate the limits and quotas. This isn't recommended because it would be a significant financial expense. However, you should load testing to verify the throughput, which is measured in tokens per minute or requests per minute. Unlike standard APIs that consider metrics like request size, this approach sizes based on tokens to determine usage. The key is to understand the number of active users and measure throughput accordingly. For more information, see [How to measure your throughput](/azure/ai-services/openai/how-to/latency#how-to-measure-your-throughput).

#### Security controls

Regardless if you're using an inferencing server or a PaaS option, security is your responsibility. With API endpoints, it's crucial to test for jailbreaking and content safety controls to make sure that these controls can't be bypassed and are functioning as expected. For instance, sending a known blocked item can help verify if the security control is in place and working correctly before deployment. Consider running these tests as needed or integrate them into the release process.

It's important to test if the system can inadvertently expose information it shouldn't. For example, personal information in the response payload. Also, test to make sure a client can't access endpoints meant for other identities. Conduct security tests to verify that the API, with its authentication and authorization mechanisms, doesn't leak confidential information and maintains proper user segmentation.

## Grounding data testing

Data design influences a generative model's efficiency, and grounding is the critical component. Grounding data provides more context to boost the relevance of the response. It's indexed before reaching the model. This index is accessed in real-time as the user waits for an answer.

Conduct end-to-end testing and incorporate that process as part of data design. Have a testing processes that evaluate and quantify results of the end user experience based on the performance of the model, orchestration, index, preprocessing, and source data. Monitor and measure the quality metrics iteratively. Here are some common use cases:

- Data processing must be thoroughly tested through functional and integration testing, verifying that data is loaded as expected and that all data is present.

- Index schema must be tested for backward compatibility. Any change to document or field should be tested to ensure the new version can still accommodate previous versions of data.

- Before data is indexed, it undergoes preparation to reduce noise and bias and enable efficient querying. This process includes preprocessing, chunking, and calculating embeddings, with each step saving data to the context or files in the index. An orchestration pipeline, such as skill sets provided by Azure AI Search, conducts these steps. The orchestration code must be tested to ensure no steps are missed and the processed data is of high quality. Tests should check for old data, synthetic values, empty tables, data refresh, and processing on the latest version. If test failures occur, you might need to tweak the search query and index. This process includes adjusting filters and other elements discussed previously. Testing should be considered an iterative activity.

- Indexes are complex and query performance can vary based on index structure, requiring load estimation. Proper load testing can help determine the different SKUs for storage, compute, and other resources are available to support requirements.

- All security controls must be tested. For example, data might be partitioned into separate documents. Each partition with access controls. Those controls must be properly tested to protect confidentiality.

Testing the correctness of index design and search queries is crucial, often done with prompt development. Key metrics are precision (relevance of returned results) and recall (completeness of relevant results). Unit tests can be written for these metrics. Test failures might require changes to the index or search query. Those metrics are also necessary for assessing search performance and ensuring desired data is returned.

## Orchestrator testing

A key component of a Retrieval-Augmented Generation (RAG) application is the central orchestrator. This code coordinates various tasks related to the initial user question. Orchestrator tasks typically require an understanding of user intent, connecting to the index to look up grounding data, and calling the inference endpoint. If tasks need to be done by agents, such as calling REST APIs, this code handles that within the context.

Orchestration code can be developed in any language and written from scratch. However, we recommend using technologies like Prompt Flow in Azure AI Studio or Apache Airflow's DAGs (Directed Acyclic Graphs) to speed up and simplify the development process. Prompt Flow provides a design-time experience that allows you to modularize tasks as units, connecting inputs and outputs of each unit, ultimately forming the orchestration code, which represents the entire process.

Isolate your orchestration code. Develop it separately and deploy it as a microservice with an online endpoint and REST API for access. This approach ensures modularity and ease of deployment.

From a testing perspective, treat this code like any other code and conduct unit tests. However, the bigger aspect is its functionality, such as its routing logic, which can be validated through functional and integration testing. Test prompt engineering to ensure the code can detect user intent and route calls appropriately. There are several frameworks and libraries for testing, such as Scikit-learn, PyTorch's torch.testing module, FairML for bias and fairness testing, and TensorFlow Model Analysis for model evaluation.

Also, conduct runtime tests, such as failure mode testing. For example, test potential failures related to token limitations.

Certain runtime tests can help in decision-making. Run load tests to understand how this code behaves under stress and use the results for capacity planning. Because this code is positioned at a crucial point in the architecture where it needs to reach other services, it can help in collecting telemetry from all those calls. This data can provide insights into how much time is spent on local processing versus network calls and determine the behavior of other components, such as potential latency. Technologies like Prompt Flow have built-in telemetry capabilities to facilitate this process. Otherwise, incorporate telemetry in your custom code.

> [!NOTE]
>
> Testing this code has cost implications. For example, if you use Azure OpenAI to host your inference endpoint, stress testing is a common practice that can help you determine the system's limits. However, OpenAI charges for every call, which can make extensive stress testing expensive. One way to optimize charges is to use unused PTUs of OpenAI in a test environment. Alternatively, simulate the inference endpoint.

Security concerns apply to both the orchestration code and the model. Include testing for jailbreaking, where the goal is to break the model's security. Attackers don't interact with the model directly; they interact with the orchestration code first. The orchestration code receives user requests and parses them. If the orchestration code receives a malicious request, it can be forwarded to the model, potentially compromising it.

Content safety is another important aspect. In a ChatBot application, chat text is sent to the orchestration code. In the code, consider calling a content safety service. Send both the user prompt and the grounding context for analysis and receive an assessment of the risk. [Prompt Shields](/azure/ai-services/content-safety/concepts/jailbreak-detection) is a unified API that analyzes LLM inputs and detects User prompt attacks and document attacks, which are two common types of adversarial inputs.

Security control and authentication are crucial for a RESTful endpoint. You need to manage authentication and ensure thorough testing.

## Model decay

An issue that is common for all models is some degree of degradation over time. Changes that are internal and external to the workload eventually cause a degradation in the quality of the model and its outputs. Model decay occurs in two ways:

- **Data drift:** Data drift occurs when the input data changes. New data input makes the trained model out of date. For example, you might have a model that predicts voting patterns of a certain geographical area, like a district. If the district is redrawn and the demographics of the population of that district change, your model needs to be updated to account for the changes.

- **Concept drift:** Concept drift occurs when conditions external to the workload and model change in such a way that the model outputs no longer match reality. For example, you might have a sales forecast model for a technology product. If a competitor unexpectedly introduces a more advanced competing product that draws significant attention in the public, you need to update your model based on how consumer trends change.

When possible, use automated testing to detect and evaluate model decay over your model's lifecycle. If your model predicts discrete values, you can create tests to evaluate predictions against those values over time and measure the deviation between expected and actual results. Compliment this testing with monitoring to detect drift over time by comparing summary statistics and distance metrics.

Another common approach to identifying model decay is user feedback. An example of user feedback is a thumbs up or thumbs down response mechanism. Tracking the positive versus negative feedback over time and creating an alert when a negative feedback threshold is met can be a good sign to investigate the quality of the model.

Regardless of the signals that you use to identify model decay, the operations team that's alerted about potential decay needs to engage a data scientist to research the signal and determine whether decay is happening and the root cause.

### Tools

- **[Azure Machine Learning Data Collector](/azure/machine-learning/concept-data-collection)**. Data Collector provides real-time logging of input and output data from models that are deployed to managed online endpoints or Kubernetes online endpoints. Machine Learning stores the logged inference data in Azure blob storage. You can then use this data for model monitoring, debugging, or auditing, which provides observability into the performance of your deployed models. If you deploy a model outside of Machine Learning or to a Machine Learning batch endpoint, you can't take advantage of the Data Collector and have need to operationalize another data collection process.

- **Machine Learning model monitoring:** To implement monitoring, Machine Learning acquires monitoring signals by performing statistical computations on streamed production inference data and reference data. The reference data can be historical training data, validation data, or ground truth data. On the other hand, the production inference data refers to the model's input and output data collected in production.

  - Refer to the [model monitoring article](/azure/machine-learning/concept-model-monitoring) to learn about the monitoring capabilities of Machine Learning and the [metrics](/azure/machine-learning/concept-model-monitoring#monitoring-signals-and-metrics) that it captures and analyzes.
  
  - Refer to the [best practices](/azure/machine-learning/concept-model-monitoring#recommended-best-practices-for-model-monitoring) for further recommendations for monitoring.

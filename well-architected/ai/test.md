---
title: Test and Evaluate AI Workloads on Azure
description: Learn about AI workload testing operations and metrics that help you maintain the quality of your workload on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: conceptual
ms.update-cycle: 180-days  
---

# Test and evaluate GenAI workloads on Azur
This article focuses on two distinct aspects: evaluating the models and testing the entire system. Evaluation and testing are often used interchangeably, but they should be considered separate processes that use distinct datasets. 

Evaluation is an iterative activity that you do during the development phase. It focuses on experimentation to find the best model with the right level of tuning. Then, evaluating the model based on various metrics. 

Testing includes verifying the entire system, when a change is introduced, including the tuned model and non-AI components. The goal of validate whether the workload meets identified targets and fulfills user expectations. It also is a nonnegotiable change management strategy that prevents quality regressions. 

Both practices are coupled in actual implementation. The entire process includes sending requests to the model, evaluating its responses, and making a go or no-go decision based on the test data. While the process is nonnegotiable before production, we recommend that you do conduct the process in production by using real data and synthetic data.

//TODO

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

## Use quality metrics for model evaluation

Establish a baseline and measure model quality by using metrics that align with your business goals. 

Have processes that evaluate and quantify results of the user experience against a set of metrics. For instance, **Groundedness** evaluates whether a generative model's response is supported by the provided context, rather than fabricated. Suppose a legal firm develops an AI assistant that cites statutes. Without proper validation, it could draw from outdated or misclassified documents, resulting in serious consequences. A high groundedness score helps ensure that the model's output remains aligned with trusted source material.

Select and prioritize metrics based on your specific use case, monitor them continuously, and use them as decision gates for model tuning and deployment. Avoid relying on a single metric, use a combination to capture different dimensions of quality. For example, even if a model demonstrates strong groundedness, it may still produce biased outputs. Incorporate fairness evaluations for more balanced and responsible outcomes.

For information about metrics, see [Monitoring evaluation metrics descriptions and use cases](/azure/machine-learning/prompt-flow/concept-model-monitoring-generative-ai-evaluation-metrics).

The strategy of baselining also applies to model training, where various combinations of models, parameters, and features are evaluated using well-defined metrics. These metrics provide objective, data-driven scores that you can iteratively compare across versions and configurations to identify the best-performing model. For more information, see [Regression/forecasting metrics](/azure/machine-learning/how-to-understand-automated-ml?view=azureml-api-2#regressionforecasting-metrics).

## Test the grounding data

The quality of grounding data directly impacts a generative model's relevance and reliability. This data is preprocessed, chunked, and indexed before reaching the model, and the index is queried in real time while users wait for a response. Testing should be integrated early in the data design process and continue throughout the system lifecycle.

To ensure a high-quality experience, test the entire data flow—including source documents, preprocessing, orchestration logic, and the index itself. Key testing considerations include:

- **Functional and integration testing.** Validate that all data loads correctly and completely. Ensure the pipeline handles missing, empty, or synthetic data as expected.

- **Index schema compatibility**. Test schema changes to ensure backward compatibility. Any field or document changes must preserve support for older data formats.

- **Preprocessing and orchestration testing**. Grounding data preparation involves preprocessing, chunking, and embedding computation, often orchestrated by tools like Azure AI Search skill sets. Test the orchestration pipeline to ensure all steps execute correctly and the resulting data is accurate and relevant.

- **Data freshness and quality checks**. Include tests for stale data, versioning mismatches, synthetic artifacts, and empty or partial tables. Update queries or index settings as needed to reflect the most current and clean data.

- **Index load testing**. Indexes can behave differently under varying loads. Test query performance against realistic usage scenarios to inform decisions about scaling, compute SKUs, and storage requirements.

- **Security testing**. If documents are partitioned with access controls, rigorously test those controls. Ensure that each user or role only accesses permitted content to maintain confidentiality and compliance.


## Validate the agentic workflows 

As architectures are evolving to use AI, functionality that was once handled by static or deterministic code is now offloaded to autonomous agents. These agents make decisions often with dynamic behavior.

Consider a RAG application where the orchestrator itself is implemented as an agent. Unlike traditional orchestration, agents can invoke tools, interpret prompts, collaborate with other agents, and adapt in real time, making them more flexible, but harder to validate.

This architecture introduces new challenges for testing and evaluation. Because agents operate non-deterministically, traditional static tests are insufficient. Testing strategy should validate the complete flow from user input to final response, including grounding data retrieval, tool invocation, and response generation. For example, 

- Apply unit testing to deterministic components within the agent logic, especially if you're using agent frameworks like Semantic Kernel. These tests validate prompt templates, tool selection logic, data formatting, and decision trees, isolated from runtime variability.

- Verify that agents are calling external tools, APIs, and other agents correctly. Use mock dependencies to validate that data is passed correctly. Simulate tool or agent failures to test reliability in behavior.

- Design scenario-based tests using predefined prompts and expected outputs. Because outputs may vary, evaluate results using automated scoring with another model. Also using human-based review, especially for sensitive or subjective tasks.

- Integrate content safety tools to detect harmful, biased, or inappropriate outputs. Include red teaming exercises to identify unexpected behaviors or jailbreak vulnerabilities. Monitor for fairness, transparency, and compliance with ethical standards.

In addition, conduct regular performance and load testing. Assess the agent's ability to scale under concurrent requests, handle long execution paths, and manage interactions across multiple agents. Continuously monitor for regressions in both logic and performance as the system evolves through iterative releases.

## Test deterministic orchestration code

In some architectures, you might use static, deterministic code, such as a central orchestrator, to coordinate tasks related to a user's request. In a RAG system, for example, the orchestrator interprets user intent, queries the index for grounding data, and calls the model inference endpoint. It may also handle tool calls (like REST APIs) required by agents, depending on the design.

You can build orchestration logic using any general-purpose language, or with frameworks like [Microsoft's Semantic Kernel](/semantic-kernel/overview/) or LangChain.

From a testing perspective, consider Foundry Evaluation SDK, which supports:

- Intent resolution: Does the agent/orchestrator correctly understand the user's request?
- Tool call accuracy: Are the correct tools called, with the right parameters?
- Task adherence: Does the final output align with the assigned task and prior reasoning steps?

There are other open source libraries available such as Scikit-learn, PyTorch's torch.testing module, FairML for bias and fairness testing, and TensorFlow Model Analysis for model evaluation.

Treat this orchestration code like any critical system component, running performance, reliability, and functional tests especially on its routing logic. Security applies equally to both orchestration code and the underlying model:

- Jailbreak testing. Always test for jailbreak attempts. Attackers typically target the orchestration layer first, which parses and forwards requests to the model. If malicious inputs aren't filtered, they can compromise model behavior.

- Content safety: In chat-based applications, run both user prompts and grounding context through a content safety service. 

- Endpoint security: For RESTful interfaces, enforce strong authentication and thoroughly test security controls to prevent unauthorized access.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Testing this code has cost implications. For example, if you use Azure OpenAI to host your inference endpoint, stress testing is a common practice that can help you determine the system's limits. However, Azure OpenAI charges for every call, which can make extensive stress testing expensive. One way to optimize charges is to use unused PTUs of Azure OpenAI in a test environment. Alternatively, you can simulate the inference endpoint.

//TODO

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



## Evaluate and test the model


### Use the right data set

Typically there are three key datasets partitioned from the source data: training, evaluation, and testing.

Use the training dataset, which is usually the largest subset, to train the model. Use another dataset for evaluation to refine the model through an iterative process by assessing different permutations. After you find a satisfactory permutation, test it against the test dataset.

All datasets should contain high-quality data to minimize noise. Your test cases on data ingestion and preprocessing pipelines can serve as quality checkpoints. A lack of samples can also attribute to low-quality data. Use synthetic data to balance and achieve uniformity in the dataset. This approach is useful for training models like fraud detection models, where real fraud instances are rare, which makes it difficult to get sufficient statistical power for reliable predictions.

To avoid bias in predictions, keep all datasets distinct. You shouldn't use training data for evaluation, and you shouldn't use evaluation data for testing. Reserve unique data for model evaluation and final testing.




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

## Test the training workflow

Train a model by using custom code, such as PyTorch scripts, which do the actual training work. These scripts run on compute, such as in notebooks or Azure Machine Learning jobs, which also require memory and networking resources. We recommend load testing during the design phase to evaluate compute needs and ensure that the proposed SKUs are suitable. You often need manual testing to determine the best configuration to efficiently run the workload within the time limit.

Write the scripts by using specialized SDKs, which handle most of the tasks. However, because scripts are still code, you should integrate unit testing as part of development. These tests help you ensure that no regressions occur when you update dependencies. If unit testing isn't possible, manual testing is necessary before you deploy new code to prevent quality regressions.

These scripts run as part of a workflow, like Azure Machine Learning studio, which can provide insight as to when and whether the script ran. But we recommend that you run integration tests to make sure that these scripts are invoked reliably.

## Next steps

> [!div class="nextstepaction"]
> [Design area: Responsible AI](./responsible-ai.md)

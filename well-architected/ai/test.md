---
title: Test and Evaluate AI Workloads on Azure
description: Learn about AI workload testing operations and metrics that help you maintain the quality of your workload on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 08/27/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Test and evaluate AI workloads on Azure
This article focuses on two distinct aspects: evaluating the models and testing the entire system. Evaluation and testing are often used interchangeably, but they should be considered separate processes that use distinct datasets. 

_Evaluation_ is an iterative activity that you do during the development phase. It focuses on experimentation to find the best model with the right level of tuning. Then, evaluate the model based on various metrics.

_Testing_ includes verifying the entire system when a change is introduced, including the tuned model and non-AI components. The goal is to validate whether the workload meets identified targets and fulfills user expectations. It's also a non‑negotiable change management strategy that prevents quality regressions.

Both practices are coupled in actual implementation. The entire process includes sending requests to the model, evaluating its responses, and making a go or no-go decision based on the test data. While the process is non‑negotiable before production, we recommend that you conduct the process in production using a mix of real and synthetic data.

The primary focus here is on solutions built using generative AI, particularly scenarios using foundation models. For guidance applicable to training and fine-tuning models, skip ahead to [Guidance for testing model training and fine-tuning](#guidance-for-testing-model-training-and-fine-tuning).

## Use quality metrics for model evaluation

Establish a baseline and measure model quality by using metrics that align with your business goals. 

Have processes that evaluate and quantify results of the user experience against a set of metrics. For instance, *Groundedness* evaluates whether a generative model's response is supported by the provided context, rather than fabricated. Suppose a legal firm develops an AI assistant that cites statutes. Without proper validation, it could draw from outdated or misclassified documents, resulting in serious consequences. A high groundedness score helps ensure that the model's output remains aligned with trusted source material.

Select and prioritize metrics based on your specific use case, monitor them continuously, and use them as decision gates for model tuning and deployment. Avoid relying on a single metric, use a combination to capture different dimensions of quality. For example, even if a model demonstrates strong groundedness, it may still produce biased outputs. Incorporate fairness evaluations for more balanced and responsible outcomes.

For information about metrics, see [Monitoring evaluation metrics descriptions and use cases](/azure/machine-learning/prompt-flow/concept-model-monitoring-generative-ai-evaluation-metrics).


## Use the right data for evaluation

Refine your model through an iterative process using evaluation data. This dataset, often referred to as the _golden dataset_ consists of trusted input-output pairs, typically created or validated by humans. It serves as the objective benchmark to assess model performance against defined quality metrics.

Ensure the dataset is representative of the domain or task, with diverse, high-quality examples and minimal noise. A limited sample size can lead to poor evaluation quality, so consider generating synthetic data when the sample data lacks sufficient diversity or coverage to improve balance and completeness.


## Validate agentic workflows 

As architectures are evolving to use AI, functionality that was once handled by deterministic code is now offloaded to *AI agents*. These agents make decisions often with dynamic behavior.

Consider an agentic application where the *orchestrator* itself is implemented as an agent. Unlike traditional orchestration, agents can invoke tools, interpret prompts, collaborate with other agents, and adapt in real time, making them more flexible, but harder to validate.

This type of architecture introduces new challenges for testing and evaluation. Because agents operate non-deterministically, traditional static tests are insufficient. Testing strategy should validate the complete flow from user input to final response, including *grounding data* retrieval, tool invocation, and response generation. For example, 

- **Verify that agents are calling external tools, APIs, and other agents correctly.** Use mock dependencies to validate that data is passed correctly. Simulate tool or agent failures to test reliability in behavior.

- **Design scenario-based tests using predefined prompts and expected outputs.** Because outputs may vary, evaluate results using automated scoring with another model. Also use human-based review, especially for sensitive or subjective tasks.

- **Integrate content safety tools to detect harmful, biased, or inappropriate outputs.** Include red teaming exercises to identify unexpected behaviors or jailbreak vulnerabilities. Monitor for fairness, transparency, and compliance with ethical standards.

From a tooling perspective, consider [Azure AI Evaluation SDK](/azure/ai-foundry/how-to/develop/agent-evaluate-sdk), which supports checks like:

- Intent resolution: Does the agent/orchestrator correctly understand the user's request?
- Tool call accuracy: Are the correct tools called, with the right parameters?
- Task adherence: Does the final output align with the assigned task and prior reasoning steps?

In addition, conduct regular performance and load testing. Assess the agent's ability to scale under concurrent requests, handle long execution paths, and manage interactions across multiple agents. Continuously monitor for regressions in both logic and performance as the system evolves through iterative releases.

### Test the security aspects

Secure agentic workflows by controlling access, validating all inputs, and monitoring agent behavior to prevent misuse or unintended actions.

- **Jailbreak testing.** Always test for jailbreak attempts. Attackers typically target the orchestration layer first, which parses and forwards requests to the model. If malicious inputs aren't filtered, they can compromise model behavior.

- **Content safety.** In chat-based applications, run both user prompts and grounding context through a content safety service. 

- **Endpoint security.** For RESTful interfaces, enforce strong authentication and thoroughly test security controls to prevent unauthorized access.

There are other open source libraries available such as Scikit-learn, PyTorch's torch.testing module, FairML for bias and fairness testing, and TensorFlow Model Analysis for model evaluation.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Testing this code has cost implications. For example, if you use Azure OpenAI to host your inference endpoint, stress testing is a common practice that can help you determine the system's limits. However, Azure OpenAI charges for every call, which can make extensive stress testing expensive. One way to optimize charges is to use unused PTUs of Azure OpenAI in a test environment. Alternatively, you can simulate the inference endpoint by using tools like [Dev Proxy](/microsoft-cloud/dev/dev-proxy/how-to/simulate-azure-openai).


### Test the deterministic behavior

In some architectures, you might use deterministic logic to enable orchestration. For example, instead of a non-deterministic agent orchestrator, you can choose to have an orchestrator that uses static code to manage the execution flow, such as interpreting user intent, querying the index for grounding data, and calling the model *inference endpoint*. 

From a testing perspective, treat this code like any critical system component: run performance, reliability, and functional tests, especially on its routing logic. Apply unit testing to deterministic components, especially if you're using agent frameworks like [Microsoft's Semantic Kernel](/semantic-kernel/overview/) or LangChain. These tests validate prompt templates, tool selection logic, data formatting, and decision trees, isolated from runtime variability.


## Test the inference endpoint 

Inference endpoints expose your generative models through REST APIs and must be tested beyond just model accuracy. Whether you're using PaaS platforms or self-hosted servers, test the endpoint like any other endpoint to ensure reliability, scalability, and security.

- **Functional and integration testing.** Validate request handling, response structure, and integration with other components.

- **Performance and load testing.** Simulate realistic conditions to evaluate throughput, latency, and resource usage. For PaaS inference endpoints, focus on token-level metrics (tokens/sec or tokens/min), which are more meaningful than traditional request sizes of REST APIs.

- **Scaling and GPU optimization.** Test under varying load to determine the right GPU SKU or autoscaling configuration. Avoid overprovisioning by monitoring actual GPU usage.

   > :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off.** GPU SKUs are expensive. It's important to continuously check whether GPU resources are underused and rightsize them, when possible. After you make adjustments, test resource usage to maintain the balance between cost efficiency and performance optimization. 

- **Failure handling.** Simulate throttling, like HTTP 429 errors, backend timeouts, and service unavailability. Validate that your client handles retries, backoff, and circuit breaking appropriately.

- **Security and content safety.** For public or self-hosted endpoints, perform penetration tests and validate access controls. Use content moderation tools like Azure AI Content Safety to test and filter unsafe input/output.


## Test the grounding data workflow

The relevance of a generative AI model depends on the quality and integrity of its *grounding data*. Grounding data can be seeded to the model by using data processing pipelines. This data is preprocessed, chunked, and indexed before reaching the model. The model queries the index in real time during user interaction, making indexing performance and accuracy critical to the user experience. Integrate testing early and maintain it throughout the system lifecycle.

Poorly tested data pipelines can lead to inconsistent results and cross-cutting concerns like security breaches. To ensure a high-quality experience, test the entire data flow, including source documents, preprocessing, orchestration logic, and the index itself. Key testing considerations include:

- **Functional and integration testing.** Validate that all data loads correctly and completely. Ensure the pipeline handles missing, empty, or synthetic data as expected.

- **Index schema compatibility.** Test schema changes to ensure backward compatibility. Any field or document changes must preserve support for older data formats.

- **Preprocessing and orchestration testing.** Grounding data preparation involves preprocessing, chunking, and embedding computation, often orchestrated by tools like Azure AI Search skill sets. Test the orchestration pipeline to ensure all steps execute correctly and the resulting data is accurate and relevant.

- **Data freshness and quality checks.** Include tests for stale data, versioning mismatches, synthetic artifacts, and empty or partial tables. Update queries or index settings as needed to reflect the most current and clean data.

- **Index load testing.** Indexes can behave differently under varying loads. Test query performance against realistic usage scenarios to inform decisions about scaling, compute SKUs, and storage requirements.

- **Security testing.** If documents are partitioned with access controls, rigorously test those controls. Ensure that each user or role only accesses permitted content to maintain confidentiality and compliance.


## Guidance for testing model training and fine-tuning

Similar to generative AI models, use various types of tests at different stages of the development life cycle and across different system components and flows. As much as practical, develop workload assets with testing in mind. For instance, when you perform data manipulation and reshape source data for feature engineering, adhere to good coding practices and ensure that you structure the code to support testing.

### Evaluate the model

Apply a baselining strategy during model training to measure and compare model quality. Evaluate the performance of various combinations of models, parameters, and features by using well-defined metrics. These metrics provide objective, data-driven scores that you can iteratively compare across versions and configurations to identify the best-performing model. 

For more information, see [Regression/forecasting metrics](/azure/machine-learning/how-to-understand-automated-ml#regressionforecasting-metrics). 

### Data to evaluate and test

Partition source data into three distinct datasets: training, evaluation, and testing. Use the training dataset to build the model, the evaluation dataset to tune it, and the test dataset to validate final performance. 

Ensure each dataset contains high-quality data to reduce noise. Use test cases in data pipelines to enforce quality, and supplement with synthetic data when real samples are limited, in domains like fraud detection, where real fraud instances are rare and provide limited data for training reliable models.

Keep all datasets separate and non-overlapping to maintain objectivity and prevent bias in predictions. Don't reuse training data for evaluation or evaluation data for testing. 

### Testing the training and fine-tuning workflow

- **Data pipeline technologies.** Combine functional, load, and performance tests using synthetic data to assess scalability and make informed decisions about sizing or product suitability, required SKUs, and system integration.

- **Ingestion workflow.** Test ETL/ELT pipelines end-to-end to make sure that they ingest data reliably and that the data is high quality.  Also, test integration with all connected systems and monitor external dependencies. Use synthetic data to validate end-to-end processing, particularly for complex or high-volume workloads.

   Test scheduled jobs to validate that ingestion tasks complete on time and return expected volumes.

- **Data quality on ingestion.** Verify that data cleansing and processing include tests to confirm that data manipulation functions as intended. Include checks for completeness, freshness, schema consistency, uniqueness, and relevance. Also verify that structured data is ingested without duplicates, missing values, or invalid entries.

- **Feature and label integrity.** Validate that features are correctly calculated and labels accurately assigned, especially when using complex rules. Check for data leakage to prevent future or label-derived information from contaminating training data. Also, verify that data splits are appropriate to avoid biased or overlapping samples because even subtle leakage can harm model performance.

- **Hyperparameter testing.** Hyperparameter testing is an iterative process where model parameters are tuned to meet accuracy goals based on your workload's use case. This involves repeatedly training on selected data and evaluating on test data to validate performance. Start with a smaller dataset to quickly assess model behavior, then scale testing to the full set. Be mindful of the trade-off between model accuracy and the computational cost and time required for repeated training and evaluation.

- **Code quality.** When training models using custom code, like with PyTorch script, run load tests during the design phase to evaluate compute requirements and select appropriate SKUs. Use unit tests to catch regressions during development, and rely on manual testing when automation isn't feasible. Since these scripts run within workflows, add integration tests to verify that scripts are executed reliably within the pipeline.

- **Inference endpoint.** This REST API provides access to the trained machine learning model for making predictions. The model is deployed to an environment with an endpoint that can receive real-time or batch input data, process it, and return predictions. Like any other API, make sure that the inference endpoint undergoes functional, performance, and security testing validate that it returns accurate results, handles expected load, and remains secure against misuse.

- **Live-site testing.** Extend functional testing into the live system. Run scheduled tests to validate data volumes, detect missing or duplicate records, and confirm data freshness. Use synthetic data to safely validate end-to-end transformations and logic under production conditions. Incorporate A/B tests to evaluate new experiences and prevent quality regressions before full deployment. Configure alerting to trigger immediate investigation when tests fail.

Integrate data testing into CI/CD pipelines by automating unit and functional tests, especially during code changes or pipeline updates. Add quality checks before retraining, and use side-by-side deployments to safely test in production. Set up alerting for test failures or ingestion anomalies.

> [!NOTE]
> Testing and monitoring serve different purposes. Conduct tests to evaluate potential changes to the system, typically before you implement any changes. Conduct monitoring continuously to assess the overall health of the system.

### Testing for model decay

All models degrade over time due to internal and external changes in the workload. This decline in model quality, known as model decay, can occur in two ways:

- **Data drift** happens when input data changes, making the model outdated. For example, a model that predicts voting patterns becomes irrelevant because of demographic shifts after redistricting. 

- **Concept drift** occurs when external conditions change, causing the model's predictions to no longer reflect reality. For example, a model that predicts sales trends becomes irrelevant because there's a change in consumer behavior after a competitor launches a new product.

To detect decay, use automated testing to compare predictions against actual outcomes, and monitor for drift using statistical metrics. User feedback, for example using thumbs up/down, is also a valuable signal for identifying issues. When potential decay is detected, the operations team should alert data scientists to investigate and determine root causes and next steps.


### Test tools

Consider these Azure resources:

- [Azure Machine Learning data collector](/azure/machine-learning/concept-data-collection) for real-time logging of input/output data for models deployed to managed or Kubernetes online endpoints.

- [Azure Machine Learning Model Monitoring](/azure/machine-learning/concept-model-monitoring) for automated monitoring by comparing real-time inference data with reference datasets to track drift and other performance metrics. See the [best practices](/azure/machine-learning/concept-model-monitoring#best-practices-for-model-monitoring) for more recommendations.


## Next steps

> [!div class="nextstepaction"]
> [Design area: Responsible AI](./responsible-ai.md)

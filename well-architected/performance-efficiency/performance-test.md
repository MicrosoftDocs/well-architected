---
title: Recommendations for performance testing
description: Learn best practices for performance testing. See how to select tools, configure environments, and take other steps to help workloads meet performance targets.
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for performance testing

**Applies to this Azure Well-Architected Framework Performance Efficiency checklist recommendation:**

| [PE:06](checklist.md) | Test performance. Perform regular testing in an environment that matches the production environment. Compare results against the performance targets and the performance benchmark. |
|---|---|

This guide describes the recommendations for testing. Performance testing helps you evaluate the functionality of a workload in various scenarios. It involves testing the workload's response time, throughput, resource utilization, and stability to help ensure that the workload meets its performance requirements.

Testing helps to prevent performance issues. It also helps ensure that your workload meets its service-level agreements. Without performance testing, a workload can experience performance degradations that are often preventable. Workload performance can drift from performance targets and established baselines.

**Definitions**

| Term | Definition |
| --- | --- |
| Chaos testing | A performance test that aims to test the resilience and stability of a system by deliberately introducing random and unpredictable failures or disruptions. |
| Load test | A performance test that measures system performance under typical and heavy load. |
| Performance baseline | A set of metrics that represent the behavior of a workload under normal conditions as validated by testing. |
| Stress test | A performance test that overloads a system until it breaks. |
| Synthetic test | A performance test that simulates user requests in an application. |

## Key design strategies

Performance testing helps you gather measurable data on a workload. When you run tests early enough, they also help you build workloads to the right specifications. You should conduct performance tests as early as possible in the software development lifecycle. Early testing allows you to catch and fix performance issues earlier in development. You can use a proof of concept (POC) if production code isn't ready.

### Prepare the test

Preparing performance tests refers to setting up and arranging the resources, configurations, and test scenarios that you need to conduct performance testing effectively.

#### Define acceptance criteria

Acceptance criteria specify the performance requirements that a workload needs to meet to be considered acceptable or successful. Define criteria that align with the performance targets.

*Review performance targets.* Performance targets define your desired level of performance for your workload. Review the performance targets that are established for the workload. Performance targets are metrics that can involve response time, throughput, resource utilization, or any other relevant performance indicators. For example, you might have a target for your response time to be under a certain threshold, such as less than 2 seconds.

*Define acceptance criteria.* Translate the performance targets into specific acceptance criteria that you can use to evaluate the performance of your workload. For example, suppose your performance target for response time is 2 seconds or less. Your acceptance criterion could be *The average response time of the workload should be less than 2 seconds*. Use these acceptance criteria to determine whether the workload meets the desired level of performance.

When you define acceptance criteria, it's important to focus on users and their expectations. Acceptance criteria help ensure that the delivered work meets user needs and requirements. Keep in mind the following considerations for incorporating the user perspective into acceptance criteria:

- *User requirements*: Understand the user needs and goals for the workload. Consider how the workload should perform to satisfy these requirements.

- *User experience*: Define acceptance criteria that capture the desired user experience. Include factors such as response time, usability, accessibility, and overall satisfaction.

- *Functional requirements*: Address the specific functionality that the user expects to see in the workload. Define acceptance criteria around these functional requirements to help ensure that they're met.

- *Use cases*: Consider different scenarios or use cases that the user might encounter. Define acceptance criteria based on these use cases to validate the workload's performance in real-world situations.

*Set acceptance thresholds.* Determine the thresholds within the acceptance criteria that indicate whether the workload meets the performance targets. These thresholds define the acceptable range of performance for each metric. For example, suppose the acceptance criterion for response time is less than 2 seconds. You can set the threshold at 2.5 seconds. This level indicates that any response time over 2.5 seconds is considered a performance issue.

*Define passing criteria.* Establish the criteria for determining whether the workload passed or failed the performance test. You might define passing as meeting all the acceptance criteria or achieving a certain percentage of them.

#### Select the test type

To select the right type of performance test, it's important to align the test with your acceptance criteria. The acceptance criteria define the conditions that need to be met for a requirement or bug fix to be considered done. Performance tests should aim to verify whether a workload meets these acceptance criteria and performs as expected under specified conditions. Aligning the performance test type with the acceptance criteria helps ensure that the test focuses on meeting the performance expectations that the criteria define.

- *Understand acceptance criteria*. Review the acceptance criteria for the requirement or bug fix. The criteria outline the specific conditions and functionalities to be met.

- *Identify relevant performance metrics*. Based on the acceptance criteria, determine the performance metrics that are critical to achieving the desired outcomes. For example, if the acceptance criteria focus on response time, prioritizing load testing might be appropriate.

- *Select an appropriate test type*. Evaluate the available test types and choose the one that best aligns with the identified performance metrics and acceptance criteria.

The following table provides a sample of test types and their use cases.

| Test type | Description | Use case |
| --- | --- | --- |
| Load testing | Simulate realistic user loads to measure how your workload performs under expected peak workloads. | Determines load tolerance. |
| Stress testing | Push your workload beyond its normal limits to identify its breaking points and measure its ability to recover. | Determines resilience and robustness. |
| Soak testing (endurance testing) | Run your workload under sustained high loads for an extended period to identify performance degradation, memory leaks, or resource issues. | Evaluates stability and reliability over time. |
| Spike testing | Simulate sudden increases in user load to assess how your workload handles abrupt changes in demand. | Measures the ability to scale and maintain performance during peak periods. |
| Compatibility testing | Test your workload's performance across various platforms, browsers, or devices. | Helps ensure consistent performance across various environments. |

Prioritize your selected test types based on the characteristics and requirements of your workload. Consider factors such as the criticality of performance metrics, user expectations, business priorities, and known issues or vulnerabilities.

#### Select testing tools

Choose appropriate tools based on the type of performance testing that you want to run. Evaluate the testing environment's infrastructure, resources, and constraints. Choose testing tools that support the desired test types and provide the necessary features for monitoring, measurement, analysis, and reporting.

An application performance monitoring (APM) tool provides deep insights into applications and is an essential testing tool. It helps you trace individual transactions and map their paths through various workload services. After testing, you should use the APM tool to analyze and compare testing data against your performance baseline.

Use profiling tools to identify performance bottlenecks in your code. Profiling helps identify areas of the code that consume the most resources and need optimization. It provides insights into the execution time and memory usage of different parts of the code.

The following steps can help you select the appropriate testing tools:

- *Identify testing requirements*. Begin by understanding the specific requirements of your performance testing. Consider various factors:
  - The type of workload
  - Performance metrics to measure, such as response time and throughput
  - The complexity of the workload architecture
  - The testing environment, such as cloud-based, on-premises, or hybrid

- *Research testing tools*. Conduct research to identify performance testing tools that align with your requirements. Consider commercial and open-source tools that are available in the market. Look for tools that support your desired types of performance testing, such as load testing or stress testing, and that provide features for measuring performance metrics.

- *Evaluate tool features*. Assess the features that each testing tool provides. Look for capabilities such as simulation of realistic user behavior and scalability to handle large user loads. Consider support for various protocols and technologies, integration with other testing tools or frameworks, and reporting and analysis capabilities.

- *Consider compatibility and integration*. Determine the compatibility of the testing tools with your existing infrastructure and technologies. Ensure that the tools can be easily integrated into your testing environment and can communicate with the necessary workload for monitoring and analysis.

- *Evaluate cost and licensing*. Assess the cost structure and licensing terms that are associated with the testing tools. Consider factors such as the initial investment, maintenance costs, and support costs. Also consider other licensing requirements that depend on the number of users or virtual users.

- *Conduct a POC*. Select a few tools that appear to be the most suitable based on your evaluation. Conduct a small-scale POC to validate the usability, features, and effectiveness of the tools in your specific testing scenario.

- *Consider support and training*. Evaluate the level of support and training that the tool's vendor or community provides. Determine the availability of documentation, tutorials, and technical support channels to assist with any challenges or issues that might arise during the testing process.

#### Create test scenarios

Creating test scenarios refers to the process of designing specific situations or conditions that are suitable for testing the performance of a workload. Test scenarios are created to emulate realistic user behavior and workload patterns. These scenarios provide a way for performance testers to evaluate how the workload performs under various conditions.

Test scenarios make it possible to replicate various workload patterns, such as concurrent user access, peak load periods, or specific transaction sequences. By testing the workload under different workload patterns, you can identify performance bottlenecks and optimize resource allocation.

- *Define user behavior*. Emulate realistic user behavior and workload patterns by identifying the steps and actions that users perform when they interact with the workload. Consider activities such as signing in, performing searches, submitting forms, or accessing specific features. Break down each scenario into specific steps and actions that represent the user's interaction with the workload. You can include navigating through pages, performing transactions, or interacting with various elements of the workload.

- *Determine data involvement*. Identify the test data required to run the test scenarios. You might include creating or generating realistic data sets that represent various scenarios, user profiles, or data volumes. Ensure that the test data is diverse and covers different use cases to provide a comprehensive performance evaluation.

- *Design test scripts*. Create test scripts that automate the execution of the defined test scenarios. Test scripts typically consist of a sequence of actions, HTTP requests, or interactions with workload APIs or user interfaces. Use performance testing tools or programming languages to write the scripts, considering factors such as parameterization, correlation, and dynamic data handling. Validate the test scripts for correctness and functionality. Debug any issues, such as script errors, missing or incorrect actions, or data-related problems. Test script validation is crucial to help ensure accurate and reliable performance test execution.

- *Configure test variables and parameters*. Configure variables and parameters within test scripts to introduce variability and simulate real-world scenarios. Include parameters such as user credentials, input data, or randomization to mimic different user behaviors and workload responses.

- *Iteratively refine scripts*. Continuously refine and enhance test scripts based on feedback, test results, or changing requirements. Consider optimizing script logic, parameterization, and error handling, or adding extra validation and checkpoints.

#### Configure the test environment

Configuring a test environment refers to the process of setting up the infrastructure, software, and network configurations that you need to create an environment that closely resembles your production environment.

To set up your testing environment in a way that boosts performance efficiency, include the following steps in your configuration process:

- *Mirror your production environment*. Set up your test environment to closely resemble your production environment. Consider factors such as infrastructure configuration, network settings, and software configurations. The goal is to ensure that the performance test results are representative of real-world conditions.

- *Provision sufficient resources*. Allocate adequate resources such as CPU, memory, and disk space to the test environment. Ensure that the available resources can handle the expected workload and provide accurate performance measurements.

- *Replicate network conditions*. Configure the network settings in the test environment to replicate the expected network conditions during the actual workload deployment. You need to include bandwidth, latency, and network protocols.

- *Install and configure dependencies*. Install the software, libraries, databases, and other dependencies that are required for the workload to run correctly. Configure these dependencies to match the expected production environment.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: There are costs associated with maintaining separate test environments, storing data, using tooling, and running tests. Know the cost of performance testing, and find a way to optimize spending.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Production data can contain sensitive information. Without a robust scrubbing and masking strategy, you risk leaking sensitive data when you use production data for testing.

### Perform the tests

Run the performance tests by using the chosen testing tool. Testing involves measuring and recording performance metrics, monitoring health, and capturing any performance issues that arise.

Monitor and collect performance metrics such as response time, throughput, CPU and memory utilization, and other relevant indicators.

Use the defined test scenarios to put the workload under expected loads.  Conduct tests under these varying load conditions. For example, use levels, such as normal, peak, and stress levels, to analyze the behavior of the workload in various scenarios.

### Analyze the results

Analyzing the test results involves examining the collected data and metrics from the performance tests to gain insights into the performance of the workload. The goal is to identify performance issues and use the feedback to adjust priorities in application development. The following actions are key steps for analyzing test results.

*Review performance metrics.* Look at the performance metrics that you collect during performance testing, such as response times, throughput, error rates, CPU and memory utilization, and network latency. Analyze these metrics to understand the overall performance of the workload.

- *Identify bottlenecks*. Evaluate the performance metrics to identify any bottlenecks or areas of inefficient performance. The evaluation can include high response times, resource constraints, database issues, network latency, and scalability limitations. Pinpointing the root causes of these bottlenecks helps you prioritize performance improvements.

- *Correlate metrics*. Assess the relationships and correlations between various performance metrics. For example, analyze how increased load or resource utilization affects response times. Understanding these correlations can provide valuable insights into workload behavior under different conditions. Look for patterns and trends in the performance data over time. Analyze performance under different load levels or during specific periods. Detecting trends can help identify seasonal variations, peak usage times, or recurring performance issues.

*Evaluate acceptance criteria.* Compare the retest results against the predefined acceptance criteria and performance goals. Assess whether the workload meets the desired performance standards. If the workload doesn't meet the acceptance criteria, further investigate and refine the optimizations.

*Iterate and refine the analysis.*  Make other adjustments and improvements as needed. Use the collected data and metrics to diagnose specific performance issues. The diagnosis might involve tracing through the workload components, examining log files, monitoring resource usage, or analyzing error messages. Dig deeper into the data to understand the underlying causes of performance problems.

Based on the analysis of the test results, prioritize identified performance issues and implement necessary improvements. The improvements can involve optimizing code, tuning database queries, improving caching mechanisms, and optimizing network configurations.

### Establish baselines

Baselines provide a reference point for comparing performance results over time. Baselines should be meaningful snapshots of workload performance—you don't need to use every test as a baseline.

Consider the workload objectives, and document performance snapshots that allow you to learn over time and optimize. Use these baseline measurements as a benchmark for future performance tests, and use them to identify any degradation or improvement.

To establish baselines for performance testing and use them as a benchmark for future performance tests, follow these steps:

- *Identify performance metrics*. Determine the specific performance metrics that you want to measure and track. Examples include:
  - Response time, or how quickly the workload responds to requests.
  - Throughput, or the number of requests that are processed per unit of time.
  - Resource utilization, such as CPU, memory, and disk usage.

- *Record meaningful measurements*. Record the performance metrics that you obtain during the test as the baseline measurements. These measurements represent the starting point against which you compare future performance tests.

- *Compare future tests*. In subsequent performance tests, compare the performance metrics against the established baselines and thresholds. The comparison allows you to identify any improvements or degradation in performance.

### Test continuously

Continuous testing involves the ongoing monitoring and refinement of your tests. Continuous testing helps you maintain consistent and acceptable levels of performance. A workload should provide a consistent and acceptable level of performance relative to the baseline. You should tune the workload over time to produce consistent performance that's within the acceptable limits of performance. Here are some key practices:

- *Set degradation limits*. Define numeric thresholds that specify the level of performance degradation that's acceptable over time. By setting these limits, you can monitor performance fluctuations and receive alerts when the performance falls below the defined threshold.

- *Include quality assurance*. Incorporate performance requirements, such as CPU utilization and maximum requests per second, into the quality assurance process. Treat performance requirements with the same level of importance as functional requirements. This process helps ensure that the workload meets the defined performance requirements before you deploy it to production.

- *Automate alerting*. In live environments, rapid detection and response are crucial. Set up automated alerting systems that use the performance baseline as their reference. If there's a significant deviation in performance, the necessary teams are alerted immediately to act.

- *Test changes*. Some performance issues might only manifest in a live setting. Apply thorough testing practices for proposed code and infrastructure changes. Use code instrumentation to gain insights into the application's performance characteristics, such as hot paths, memory allocations, and garbage collection. This testing ensures that any change introduced doesn't degrade performance beyond the acceptable limits.

## Azure facilitation

**Perform the tests**: [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) makes it possible for you to integrate performance testing into your CI/CD pipeline. You can incorporate load testing as a step in your pipeline to validate the performance and scalability of your applications.

[Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) provides a way for you to inject real-world faults into your application so that you can run controlled fault injection experiments. The experiments help you measure, understand, and improve your cloud application and service resilience.

[Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) is a load testing service that generates high-scale load on any application. Load Testing provides capabilities for automating load tests and integrating them into your continuous integration and continuous delivery (CI/CD) workflow. You can define test criteria, such as average response time or error thresholds, and automatically stop load tests based on specific error conditions. Load Testing offers a dashboard that provides live updates and detailed resource metrics of Azure application components during a load test. You can analyze the test results, identify performance bottlenecks, and compare multiple test runs to understand performance regressions over time.

**Analyzing the results**: [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to telemetry from your cloud and on-premises environments. [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Monitor that provides APM features. You can use Application Insights to monitor applications during development and testing and also in production.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Testing takes time and skill to perform and can affect operational efficiency.

## Related links

- [Recommendations for security testing](../security/test.md)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)

## Performance Efficiency checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Performance Efficiency checklist](checklist.md)

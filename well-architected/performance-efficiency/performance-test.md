---
title: Recommendations for testing
description: Learn best practices for testing.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for testing: PE 06

This guide describes the best practices for testing. Performance testing helps you evaluate the functionality of a workload in different scenarios. It involves testing the workload's response time, throughput, resource utilization, and stability to ensure that it meets the performance requirements. Testing helps prevent performance issues and ensures your workload can meet its service level agreements. Without performance testing, a workload experiences performance degradations that are often preventable. Workload performance drifts from performance targets and established baselines.

**Definitions**

|  Term|                                Definition|
|-|-|
| Chaos testing | A performance test that aims to test the resilience and stability of a system by deliberately introducing random and unpredictable failures or disruptions. |
  |Performance baseline|A set of metrics that represent the behavior of a workload under normal conditions as validated by testing.
| Load test|A performance test that measures system performance under typical and heavy load.|
 | Stress test|A performance test that overloads a system until it breaks.|
  |Synthetic test|A performance test that simulates user requests in an application.|

## Key design strategies

Performance testing helps you gather measurable data on a workload. It also helps you build workloads to the right specifications when done early enough. You should conduct performance tests as early in the software development lifecycle as possible. Early testing allows you to catch and fix performance issues earlier in development. You can use a proof-of-concept if production code isn't ready.

### Prepare the test

Preparing performance tests refers to the activities involved in setting up and arranging the necessary resources, configurations, and test scenarios to conduct performance testing effectively.

#### Define acceptance criteria

Acceptance criteria specify the performance requirements that need to be met for the workload to be considered acceptable or successful. Define criteria that align with the performance targets.

*Review performance targets.* Performance targets define the desired level of performance for your workload. Review the performance targets you established for the workload. Performance targets are metrics response time, throughput, resource utilization, or any other relevant performance indicators. For example, you might have a target for response time to be under a certain threshold (less than 2 seconds).

*Define acceptance criteria.* Translate the performance targets into specific acceptance criteria that can be used to evaluate the performance of the workload. For example, if the performance target for response time is to be under 2 seconds, the acceptance criterion could be "The average response time of the workload should be less than 2 seconds." Use these acceptance criteria to determine whether the workload meets the desired level of performance.

When defining acceptance criteria, it's important to focus on the user and their expectations. Acceptance criteria help ensure that the delivered work meets the user's needs and requirements. Here are some considerations for incorporating the user perspective into acceptance criteria:

- *User requirements:* Understand the user needs and goals for the workload. Consider how the workload should perform to satisfy these requirements.
- *User experience:* Define acceptance criteria that capture the desired user experience. Include factors such as response time, usability, accessibility, and overall satisfaction.
- *Functional requirements:* Address the specific functionality that the user expects to see in the workload. Define acceptance criteria around these functional requirements to ensure they're met.
- *Use cases:* Consider different scenarios or use cases that the user might encounter. Define acceptance criteria based on these use cases to validate the workload's performance in real-world situations.

*Set acceptance thresholds.* Determine the thresholds within the acceptance criteria that indicate whether the workload meets the performance targets. These thresholds define the acceptable range of performance for each metric. For example, if the acceptance criterion for response time is less than 2 seconds, the threshold could be set at 2.5 seconds, indicating that any response time over 2.5 seconds would be considered a performance issue.

*Define passing criteria.* Establish the criteria for determining whether the workload has passed or failed the performance test. You could define passing as meeting all the acceptance criteria or achieving a certain percentage of them.

#### Select test type

When selecting the right type of performance test, it's important to align it with the acceptance criteria. The acceptance criteria define the conditions that need to be met for a requirement or bug fix to be considered done. Performance tests should aim to validate whether a workload meets these acceptance criteria and performs as expected under specified conditions. Aligning the performance test type with the acceptance criteria ensures that the test focuses on meeting the performance expectations defined by the criteria.

- *Understand acceptance criteria.* Review the acceptance criteria for the requirement or bug fix. They outline the specific conditions and functionalities to be met.
- *Identify relevant performance metrics.* Based on the acceptance criteria, determine the performance metrics that are critical to achieving the desired outcomes. For example, if the acceptance criteria focus on response time, prioritizing load testing might be appropriate.
- *Select appropriate test type.* Evaluate the available test types (load testing, stress testing, etc.) and choose the one that best aligns with the identified performance metrics and acceptance criteria. Here's a sample of test types and their use cases.

| Test type | Description | Use case |
| --- | --- | --- |
|Load Testing|Simulate realistic user loads to measure how the workload performs under expected peak workloads.|Determines load tolerance.|
|Stress testing|Push the workload beyond its normal limits to identify its breaking points and measure its ability to recover. |Determines resilience and robustness.|
|Soak testing (Endurance Testing)|Run the workload under sustained high loads for an extended period to identify performance degradation, memory leaks, or resource issues. |Evaluates the stability and reliability over time.|
Spike testing|Simulate sudden increases in user load to assess how the workload handles abrupt changes in demand.|Measures the ability to scale and maintain performance during peak periods.|
Compatibility testing|Test the workload's performance across different platforms, browsers, or devices.|Ensure consistent performance across various environments.|

Prioritize the selected test types based on the characteristics and requirements of the workload. Consider factors such as the criticality of performance metrics, user expectations, business priorities, and known issues or vulnerabilities.

#### Select testing tools

Choose appropriate tools based on the type of performance testing you want to execute. Evaluate the testing environment's infrastructure, resources, and constraints. Choose testing tools that support the desired test types and provide the necessary features for monitoring, measurement, analysis, and reporting. An application performance monitoring tool provides deep insights into applications and is an essential testing tool. It helps you trace individual transactions and map their path through different workload services. After testing, you should use the APM tool to analyze and compare testing data against the performance baseline. Use profiling tools to identify performance bottlenecks in the code. Profiling helps identify areas of the code that consume the most resources and need optimization. It provides insights into the execution time and memory usage of different parts of the code. Here are steps to help you select the appropriate testing tools:

- *Identify testing requirements:* Begin by understanding the specific requirements of your performance testing. Consider factors such as the type of workload, performance metrics to measure (response time, throughput, etc.), the complexity of the workload architecture, and the testing environment (cloud-based, on-premises, or hybrid).
- *Research testing tools:* Conduct research to identify performance testing tools that align with your requirements. Consider both commercial and open-source tools available in the market. Look for tools that support the desired types of performance testing (load testing, stress testing, etc.) and provide features for measuring performance metrics.
- *Evaluate tool features:* Assess the features provided by each testing tool. Look for capabilities such as simulation of realistic user behavior and scalability to handle large user loads. You want to consider support for various protocols and technologies, integration with other testing tools or frameworks, and reporting and analysis capabilities.
- *Consider compatibility and integration:* Determine the compatibility of the testing tools with your existing infrastructure and technologies. Ensure that the tools can be easily integrated into your testing environment and can communicate with the necessary workload for monitoring and analysis.
- *Evaluate cost and licensing:* Assess the cost structure and licensing terms associated with the testing tools. Consider factors such as the initial investment, maintenance,- and support costs, and any other licensing requirements based on the number of users or virtual users.
- *Conduct Proof of Concept (POC):* Select a few tools that appear to be the most suitable based on your evaluation. Conduct a small-scale proof of concept to validate the usability, features, and effectiveness of the tools in your specific testing scenario.
- *Consider support and training:* Evaluate the level of support and training provided by the tool's vendor or community. Determine the availability of documentation, tutorials, and technical support channels to assist with any challenges or issues that might arise during the testing process.

#### Create test scenarios

Creating test scenarios refers to the process of designing specific situations or conditions to test the performance of a workload. Test scenarios are created to emulate realistic user behavior and workload patterns, allowing performance testers to evaluate how the workload performs under different conditions. Test scenarios allow you to replicate various workload patterns, such as concurrent user access, peak load periods, or specific transaction sequences. By testing the workload under different workload patterns, you can identify performance bottlenecks and optimize resource allocation.

- *Define user behavior:* Emulate realistic user behavior and workload patterns by identifying the steps and actions that users perform when interacting with the workload. Consider activities such as logging in, performing searches, submitting forms, or accessing specific features. Break down each scenario into specific steps and actions that represent the user's interaction with the workload. You can include navigating through pages, performing transactions, or interacting with different elements of the workload.
- *Determine data involvement:* Identify the test data required to execute the test scenarios. You might include creating or generating realistic data sets that represent various scenarios, user profiles, or data volumes. Ensure that the test data is diverse and covers different use cases to provide a comprehensive performance evaluation.
- *Design test scripts:* Create test scripts that automate the execution of the defined test scenarios. Test scripts typically consist of a sequence of actions, HTTP requests, or interactions with the workload APIs or user interfaces. Use performance testing tools or programming languages to write the scripts, considering factors such as parameterization, correlation, and dynamic data handling. Validate the test scripts for correctness and functionality. Debug any issues, such as script errors, missing or incorrect actions, or data-related problems. Test script validation is crucial to ensure accurate and reliable performance test execution.
- *Configure test variables and parameters:* Configure variables and parameters within the test scripts to introduce variability and simulate real-world scenarios. Include parameters such as user credentials, input data, or randomization to mimic different user behaviors and workload responses.
- *Iteratively refine:* Continuously refine and enhance the test scripts based on feedback, test results, or changing requirements. You should consider optimizing script logic, parameterization, error handling, or adding extra validation and checkpoints.

#### Configure the test environment

Configuring a test environment refers to the process of setting up the necessary infrastructure, software, and network configurations to create an environment that closely resembles the production environment. It's important for performance efficiency for the following reasons:

- *Mirror production environment.* Set up your test environment to closely resemble the production environment. Consider factors such as infrastructure configuration, network settings, and software configurations. The goal is to ensure the performance test results are more representative of real-world conditions.
- *Provision sufficient resources.* Allocate adequate resources such as CPU, memory, and disk space to the test environment. Ensure that the available resources can handle the expected workload and provide accurate performance measurements.
- *Replicate network conditions.* Configure the network settings in the test environment to replicate the expected network conditions during the actual workload deployment. You need to include bandwidth, latency, and network protocols.
- *Install and configure dependencies.* Install the necessary software, libraries, databases, or other dependencies required for the workload to run correctly. Configure these dependencies to match the expected production environment.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Maintaining separate test environments, storing data, using tooling, and running tests costs money. Know the cost of performance testing and find way to optimize spending.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Production data can contain sensitive information and using it for testing can create risk of leaking sensitive data without a robust scrubbing and masking strategy.

### Perform the test

Run the performance tests using the chosen testing tool. Monitor and collect performance metrics such as response time, throughput, CPU and memory utilization, and other relevant indicators. Execute the defined test scenarios to put the workload under expected loads. Testing involves measuring and recording performance metrics, monitoring health, and capturing any performance issues that arise. Conduct tests under varying load conditions, such as normal, peak, and stress levels, to analyze the behavior of the workload in different scenarios.

### Analyze the results

Analyzing the test results involves examining the collected data and metrics from the performance tests to gain insights into the performance of the workload. The goal is to identify any performance issues and use the feedback to update the priorities in application development. Here are the key steps involved in analyzing the test results:

**Review performance metrics.** Look at the performance metrics collected during the performance testing, such as response times, throughput, error rates, CPU and memory utilization, and network latency. Analyze these metrics to understand the overall performance of the workload.

- *Identify bottlenecks:* Evaluate the performance metrics to identify any bottlenecks or areas of inefficient performance. The evaluation could include high response times, resource constraints, database issues, network latency, or scalability limitations. Pinpointing the root causes of these bottlenecks helps in prioritizing performance improvements.
- *Correlate metrics:* Assess the relationships and correlations between different performance metrics. For example, analyze how increased load or resource utilization affects response times. Understanding these correlations can provide valuable insights into workload behavior under different conditions. Look for patterns and trends in the performance data over time. Analyze performance under different load levels or during specific periods. Detecting trends can help identify seasonal variations, peak usage times, or recurring performance issues.

**Evaluate acceptance criteria.** Compare the retest results against the predefined acceptance criteria and performance goals. Assess whether the workload now meets the desired performance standards. If the workload doesn't meet the acceptance criteria, further investigate and refine the optimizations.

**Iterate and refine.**  Make other adjustments and improvements as needed. Use the collected data and metrics to diagnose specific performance issues. The diagnosis might involve tracing through the workload components, examining log files, monitoring resource usage, or analyzing error messages. Dig deeper into the data to understand the underlying causes of performance problems.
Based on the analysis of the test results, prioritize the identified performance issues and implement the necessary improvements. The improvements could involve optimizing code, tuning database queries, improving caching mechanisms, or optimizing network configurations.

### Establish baselines

Establishing baselines provides a reference point to compare performance results over time. Baselines should be meaningful snapshots of workload performance, meaning you don't need to use every test as a baseline. Consider the workload objectives and document performance snapshots that allow you to learn over time and optimize. Use these baseline measurements as a benchmark for future performance tests, allowing you to identify any degradation or improvement. To establish baselines for performance testing and use them as a benchmark for future performance tests, follow these steps:

- *Identify performance metrics:* Determine the specific performance metrics that you want to measure and track. Examples include response time (how quickly the workload responds to requests), throughput (the number of requests processed per unit of time), and resource utilization (CPU, memory, disk usage).
- *Record meaningful measurements:* Record the performance metrics obtained during the test as the baseline measurements. These measurements represent the starting point against which you compare future performance tests.
- *Compare future tests:* In subsequent performance tests, compare the performance metrics against the established baselines and thresholds. The comparison allows you to identify any improvements or degradation in performance.

### Test continuously

Continuous testing involves ongoing monitoring and refinement of the tests to maintain consistent and acceptable levels of performance. A workload should provide a consistent and acceptable level of performance relative to the baseline. You should tune the workload over time to produce consistent performance that's within the acceptable limits of performance. Here are some key practices:

**Set degradation limits.** Define numeric thresholds that specify the acceptable level of performance degradation over time. By setting these limits, you can monitor performance fluctuations and receive alerts when the performance falls below the defined threshold.

**Include quality assurance.** Incorporate performance requirements, such as CPU utilization and maximum requests per second, into the quality assurance process. Treat performance requirements with the same level of importance as functional requirements. This process ensures the workload meets the defined performance requirements before deploying it to production.

**Automate alerting.** Implement automated alerting mechanisms that are based on the performance baseline. Utilize the performance baseline as a reference point to identify any significant performance fluctuations that might occur. Automated alerting enables quick detection and response to performance issues.

**Test changes.** Apply thorough testing practices for proposed code and infrastructure changes. Use code instrumentation to gain insights into the application's performance characteristics, such as hot paths, memory allocations, and garbage collection. Ensure that all changes undergo testing to prevent any degradation of performance beyond the defined threshold.

## Azure facilitation

**Chaos testing.** [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) lets you inject real-world faults into your application to run controlled fault injection experiments. The experiments help you measure, understand, and improve your cloud application and service resilience.

**Automated load testing:** [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) is a load testing service that generates high-scale load on any application. Azure Load Testing provides the capability to automate load tests and integrate them into your CI/CD workflow. You can define test criteria, such as average response time or error thresholds, and automatically stop load tests based on specific error conditions. Azure Load Testing offers a dashboard that provides live updates and detailed resource metrics of Azure application components during a load test. You can analyze the test results, identify performance bottlenecks, and compare multiple test runs to understand performance regressions over time.

**Testing changes:** Azure Pipelines allows you to integrate performance testing into your continuous integration and delivery (CI/CD) pipeline. You can incorporate load testing as a step in your pipeline to validate the performance and scalability of your applications

**Monitoring.** [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to telemetry from your cloud and on-premises environments. [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor and provides application performance monitoring (APM) features. It allows you to monitor applications from development, through test, and into production.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Testing takes time and skill to execute and can affect operational efficiency.

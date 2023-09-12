---
title: Recommendations for testing
description: Learn best practices for testing.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for testing: PE 06

This guide describes the best practices for testing. Performance testing helps you evaluate the functionality of a workload in different scenarios. It involves testing the system's response time, throughput, resource utilization, and stability to ensure that it meets the performance requirements. Testing helps prevent performance issues and ensures your workload can meet its service level agreements. Without performance testing, a workload experiences performance degradations that are often preventable. Workload performance drifts from performance targets and established baselines.

**Definitions**

|  Term|                                Definition|
|-|-|
  |Performance baseline|A set of metrics that represent the behavior of a workload under normal conditions as validated by testing.
| Load test|A performance test that measures system performance under typical and heavy load.|
 | Stress test|A performance test that overloads a system until it breaks.|
  |Synthetic test|A performance test that simulates user requests in an application.|
| Chaos testing | A performance test that aims to test the resilience and stability of a system by deliberately introducing random and unpredictable failures or disruptions. |

## Key strategies

Performance testing is important for performance efficiency because it helps identify potential bottlenecks, assess system capacity, and optimize resource allocation. By conducting workload performance testing, organizations can determine the system's ability to handle the expected workload, identify areas for improvement, and ensure that the system can perform optimally under various scenarios.

### Establish performance baselines

Performance baselines are measurements. They provide a numeric foundation for understanding the performance of a workload. Establishing performance baselines is an important aspect of performance testing. It involves measuring and recording the performance of a system or application under normal or expected conditions. By establishing performance baselines, you can determine the expected performance metrics and use them as a reference point for future performance testing. Knowing your performance baseline makes it easier to maintain your performance targets. Performance baselines give us something to compare proposed changes against and production performance against.

**Establish a performance baseline.** Test and monitor your workload to gather the performance metrics you need to establish your baseline. You should use platform tooling and an application performance monitoring tool to capture and store the performance metrics you need to establish your baseline and as a visualization of current performance against the established baseline.

**Set degradation tolerance.** Performance baselines help you understand the effects of workload changes. They let you know if a workload is performing better or worse over time. Most workloads can tolerate performing better than the baseline. You need to determine your degradation tolerance. It states how far under the performance baseline you're willing to accept. You should reject change below that threshold. The goal is to draw a line where you need to protect users and product above your updates.

### Understand test types

**Load test.** Load testing provides insights into how and when your application needs to scale to meet your SLO or SLA with your customers, both internal and external. Load testing can also be useful for determining latency across distributed applications and microservices. You should consider using different load testing techniques to execute load tests. You should use different load models for special events, daily fluctuations, variable load, and internal user changes. Understand the limits of the services you consume so that you aren\'t blocked if you need to exceed them. For a list of the most common Azure limits, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits). The [ResourceLimits](https://github.com/mspnp/samples/tree/master/OperationalExcellence/ResourceLimits) sample shows how to query the limits and quotas for commonly used resources.

**Stress test.** A stress test shows you the maximum load a workload can handle. From this maximum, determine a threshold for scaling out. For example, you can configure autoscale to scale out at 80% CPU capacity. You should use stress tests to determine the scaling threshold.

**Synthetic testing.** Synthetic testing simulates user interactions with a workload in production. You simulate requests to test the performance of the workload, typically in the most critical or common scenarios. You develop scripts or scenarios that mirror critical user interactions. You should use synthetic tests to proactively identify performance issues in production workloads. Any static test data that lives in production (mock customer record, mock inventory, etc) to support this process should be well-known and clearly marked. Make sure that all data generated and work performed from synthetic transactions are not inadvertently performing actual business processing and are excluded in business metrics.

### Run tests

**Categorize workload.** Categorizing a workload can help improve the precision of testing. You can use categories such as, high user frequency, critical, risky, data-intensive, complex architecture. You should tailor the performance tests based on the category.

**Test early.** Performance testing helps you gather measurable data on a workload. It also helps you build workloads to the right specifications when done early enough. You should conduct performance tests as early in the software development lifecycle as possible. Early testing allows you to catch and fix performance issues earlier in development. You can use a proof-of-concept if production code isn't ready.

**Consider end users.** End users experience the full stack of your workload. They interact with the workload from different client resources, locations, and mobility options. You need to measure and test from the end user perspective. For example, measuring requests per second or server processing time doesn't account for the end user experience. Rather, you should deploy resources close to your user base or use a content delivery network to improve response times for end users.

**Mirror the production environment in pre-production tests.** The goal of performance testing is replicate workload performance as it would behave in production.Pre-production testing should use a production-like environment to measure proposed changes. When you can't use actual dependencies during testing, you should mock those dependencies. Mocking dependencies accurately can be difficult. Always strive for a production-like test and factor in false performance characteristics from proxies that do not reflect production behavior.

One way to replicate production is to use production data in testing.  The testing data should resemble the shape, quantity, and rate of change of production data. As part of the software development lifecycle, you should copy production data to a test environment (mirroring). You should periodically synchronize the data to ensure the data is similar to production in terms of diversity, breadth, and quantity. Production data can contain sensitive information. You must scrub or mask all sensitive data as part of your copy process.

**Create stable tests.** Performance tests need to be consistent. It's unhelpful to compare tests that use different test protocols and methods. Use the same starting and injected data in addition to consistent methods and measurements.

- *Test per flow.* Conducting tests on each workload flow makes it easier to find performance issues.
- *Test scaling:* Scaling can affect the performance of an application. You should load test a workload at various levels of [scaling](/azure/azure-monitor/platform/autoscale-best-practices).

**Use an application performance monitoring tool.** An APM tool provides deep insights into applications and is an essential testing tool. It helps you trace individual transactions and map their path through different workload services. After testing, you should use the APM tool to analyze and compare testing data against the performance baseline.

### Analyze test results

Every test should result in a numeric measurement. You need to compare test results against performance targets and the performance baseline. The performance baseline might be the last deployment or even an earlier point in time.

**Associate test results with workload components.** Associating test results with the individual components involved in the test flows helps identify what components are potentially involved in any performance issues identified. It's helpful to design tests that target individual components. When you get undesirable test results, you should be able to break the performance data into layers. For example, you might have tests that target a web application firewall, request routing, web servers, and calls to a database. You need to define workload or flow components and collect performance data on each of them.

**Create a remediation plan.** The only way to know how a workload will run in production is to deploy it to production. This uncertainty creates some risk. To minimize the risk of production issues, you should create a remediation plan. The remediation plan should identify how you will respond to performance issues in production. To create your remediation plan, you should use the results of pre-production testing. These tests might help draft a remediation plan around known issues.

### Maintain workload

A workload should provide a consistent and acceptable level of performance relative to the baseline. You should tune the workload over time to produce consistent performance that's within the acceptable limits of performance.

**Set performance degradation limits**: The performance of a workload can degrade over time. You need define a numeric threshold the degradation you can tolerate. Consider setting a percentage threshold against baseline. The numeric threshold allows you alert on performance drift.

**Include performance requirements in quality assurance process.** Performance requirements, such as CPU utilization and max requests per second, should be a part of the quality assurance process. Performance requirements should be as important as functional requirements. A workload should meet performance requirements before deploying to production.

**Automate alerting.** Automated alerting allows you to respond to performance issues faster. You should use the performance baseline as the basis for alerting. It should alert you of any performance fluctuations below the degradation limit.

**Use performance-centric tooling**. Performance-centric tooling is critical for measuring and interpreting performance data. Independent software vendors make performance tooling. There's also open-source and built-in tools to use in many cases. These tools often help monitor performance of everything from the operating system up through code. Some tools are designed for active use during development. Other tools are designed to gather telemetry from production environments. These tools help generate insights into workload performance. You should have relevant tooling available, and you should learn how to effectively use them. You should use available tooling over custom solutions.

**Test code and infrastructure changes** Code instrumentation allows you to find and fix performance issues faster. It adds necessary visibility into your application, including hot paths, significant memory allocations, and late-generation garbage collection. As part of the software development lifecycle, you should test all proposed code and infrastructure changes. You should block any change that degrades the workload below the degradation limit.

## Azure facilitation

-   [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) is a load testing service that generates high-scale load on any application.

-   [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-overview) lets you inject real-world faults into your application to run controlled fault injection experiments. The experiments help you measure, understand, and improve your cloud application and service resilience. 

-   [Azure Monitor](/azure/azure-monitor/overview) is a comprehensive monitoring solution for collecting, analyzing, and responding to telemetry from your cloud and on-premises environments.

-   [Application Insights](/azure/azure-monitor/app/app-insights-overview) is an extension of Azure Monitor and provides application performance monitoring (APM) features. It allows you to monitor applications from development, through test, and into production.

## Tradeoff

-   Maintaining separate test environments and running tests costs money. It costs money to store the data. Some tooling costs money to use.

-   Production data can contain sensitive information and using it for testing can create risk of leaking sensitive data without a robust scrubbing and masking strategy.

-   Testing takes time and skill to execute. Performance testing can affect operational efficiency.

## Example

*\[Optional. Include a reference architecture that demonstrates the best practice.\]*

## Related links

---
title: Tradeoffs for performance efficiency
description: See pros and cons of optimizing for performance, and tradeoffs between performance and operational excellence, reliability, cost optimization, and security.
author: martinekuan
ms.author: martinek
ms.date: 05/04/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-pillar-efficiency
products:
  - azure-cost-management
  - azure-security-center
  - azure-monitor
categories:
  - management-and-governance
subject:
  - security
  - monitoring
ms.custom:
  - What scalability tradeoffs are you making?
  - article
  - seo-aac-fy21q3
keywords:
  - performance efficiency
  - cost optimization
  - performance optimization
  - operational excellence
---

# Tradeoffs for performance efficiency

As you design a workload, consider tradeoffs between performance efficiency and other aspects of the design, such as cost optimization, operational excellence, reliability, and security.

## Performance efficiency vs. cost optimization

Cost can increase as a result of increasing performance. When you optimize for performance, consider the following factors to manage costs:

- Avoid estimating workload costs at consistently high utilization. Smooth out the peaks to get a consistent flow of compute and data. Consumption-based pricing is more expensive than the equivalent provisioned pricing. Ideally, use manual scaling and autoscaling to find the right balance. Scaling up is usually more expensive than scaling out.

- Costs increase directly with number of regions. Locating resources in cheaper regions doesn't negate the cost of network ingress and egress, or degraded application performance because of increased latency.

- Every render cycle of a payload consumes both compute and memory. You can use caching to reduce load on servers, and save with precanned storage and bandwidth costs. The savings can be dramatic, especially for static content services.

  While caching can reduce cost, there are some performance tradeoffs. For example, Azure Traffic Manager pricing is based on the number of Domain Name Service (DNS) queries that reach the service. You can reduce that number through caching, and configure how often the cache is refreshed. But relying on a cache that isn't frequently updated causes longer user failover times if an endpoint is unavailable.

- Using dedicated resources to batch process long running jobs increases the cost. You can lower costs by provisioning [Azure Spot Virtual Machines](https://azure.microsoft.com/products/virtual-machines/spot), but be prepared for the job to be interrupted every time Azure evicts the virtual machine (VM).

For other cost considerations, see the [Cost optimization](../cost/index.yml) pillar.

## Performance efficiency vs. operational excellence

As you determine how to design your workload to meet the demands placed on it by users in an efficient manner, consider operations. Operations processes keep an application running in production. To achieve operational excellence with these processes, use the following guidelines:

- Ensure that deployments remain reliable and predictable.
- Automate deployments to reduce the chance of human error.
- Make the deployment process fast and routine, so it doesn't slow down the release of new features or bug fixes.
- Be able to quickly rollback or roll forward if an update has problems.

For other operational considerations, see the [Operational excellence](../devops/overview.md) pillar.

### Automate performance testing

[Automated performance testing](/azure/architecture/checklist/dev-ops#testing) is an operational process that can help to identify performance issues early. A serious performance issue can impact a deployment as severely as a bug in the code. Automated functional tests can prevent application bugs, but they might not detect performance problems. Define acceptable performance goals for metrics such as latency, load times, and resource usage. Include automated performance tests in your release pipeline to make sure the application meets those goals.

### Do fast builds

Another operational excellence process is making sure that your product is in a deployable state through a [fast build process](../devops/release-engineering-performance.md#build-times). Builds provide crucial information about the status of your application.

The following practices promote faster builds:

- Select the right size of VMs.
- Ensure that the build server is located near the source and target locations to considerably reduce build duration.
- Scale out build servers.
- Optimize the build.

For more information, see [Performance considerations for your deployment infrastructure](../devops/release-engineering-performance.md#build-times).

### Monitor performance

As you consider performance improvements, monitoring should be done to verify that your application is running correctly. Monitoring should include the application, platform, and networking. For more information, see [Monitor operations of cloud applications](../devops/checklist.md).

## Performance efficiency vs. reliability

Acknowledge up front that failures happen. Instead of trying to completely prevent failures, the goal is to minimize the effects of a single failing component.

Reliable applications are *resilient* and *highly available* (HA). Resiliency allows systems to recover gracefully from failures and continue to function with minimal downtime and data loss before full recovery. HA systems run as designed in a healthy state with no significant downtime. Maintaining reliability lets you maintain performance efficiency.

For reliability, consider the following guidelines:

- Use the [circuit breaker](/azure/architecture/patterns/circuit-breaker) pattern to provide stability and minimize performance impact while the system recovers from a failure.

- Segregate read and write interfaces by using the [Command query responsibility segregation (CQRS) pattern](/azure/architecture/patterns/cqrs) to achieve the scale and performance needed for your solution.

- Try to achieve higher availability by adopting an *eventual consistency* model. For more information about selecting the correct data store, see [Use the best data store for your data](/azure/architecture/guide/design-principles/use-the-best-data-store).

- If your application requires more storage accounts than are currently available in your subscription, create a new subscription with more storage accounts. For more information, see [Scalability and performance targets](/azure/storage/common/scalability-targets-standard-account).

- Avoid scaling up and down. Instead, select a tier and instance size that meet your performance requirements under typical load, and then scale out the instances to handle changes in traffic volume. Scaling up or down might trigger an application restart.

- Don't use the same storage account for logs and application data. Create a separate storage account for logs to help prevent logging from reducing application performance.

- Monitor performance. Use a performance monitoring service such as [Application Insights](/azure/azure-monitor/app/app-insights-overview) or [New Relic](https://newrelic.com/) to monitor application performance and behavior under load. Performance monitoring gives you real-time insight into the application, and helps you diagnose issues and perform root-cause failure analysis.

For resiliency, availability, and reliability considerations, see the [Reliability](../resiliency/principles.md) pillar.

## Performance efficiency vs. security

If performance is so poor that the data is unusable, you can consider the data inaccessible. You need to do whatever you can do from a security perspective to make sure that your services have optimal uptime and performance.

A popular and effective method for enhancing availability and performance is *load balancing*. Load balancing is a method of distributing network traffic across servers that are part of a service. Load balancing improves performance because the processor, network, and memory overhead for serving requests are distributed across all the load-balanced servers. Employ load balancing whenever you can, as appropriate for your services. For information about load balancing scenarios, see [Optimize uptime and performance](/azure/security/fundamentals/network-best-practices#optimize-uptime-and-performance).

Consider how the following security measures impact performance:

- To optimize performance and maximize availability, application code should first try to [get OAuth access tokens silently from a cache](/azure/active-directory/develop/msal-net-acquire-token-silently) before attempting to acquire a token from the identity provider. OAuth is a technological standard that allows you to securely share information between services without exposing your password.

- Make sure to integrate critical security alerts and logs into security information and event management (SIEM) systems without introducing a high volume of low-value data, which can increase costs and false positives and decrease performance. For more information, see [Security logs and alerts using Azure services](../security/monitor-logs-alerts.md).

- Use [Azure Active Directory Connect (Azure AD Connect)](/azure/active-directory/hybrid/connect/whatis-azure-ad-connect) to synchronize your on-premises directory with your cloud directory. Various factors affect the performance of Azure AD Connect. Ensure Azure AD Connect has enough capacity to keep underperforming systems from impeding security and productivity.

  Large or complex organizations that provision more than 100,000 objects should follow the recommendations to optimize their Azure AD Connect implementation. For more information, see [What is hybrid identity with Azure Active Directory?](/azure/active-directory/hybrid/whatis-hybrid-identity)

- To gain access to real-time performance information at the packet level, use [packet capture](/azure/network-watcher/network-watcher-alert-triggered-packet-capture) to set alerts.

For other security considerations, see the [Security](../security/overview.md) pillar.

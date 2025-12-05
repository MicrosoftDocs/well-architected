---
title: Architecture Best Practices for Azure Functions
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations for Azure Functions.
author: PageWriter-MSFT
ms.author: mohamad
ms.date: 11/21/2025
ms.topic: concept-article
ms. service: waf
ms. subservice: waf-service-guide
products:
  - azure-functions
azure.category:
  - compute
---

# Architecture best practices for Azure Functions

Azure Functions is a serverless compute service that you can use to run event-driven code without having to manage the infrastructure directly. Azure Functions is a function as a service offering that abstracts the underlying infrastructure. This abstraction allows you to focus on your code. Azure Functions automatically scales based on demand and charges you only for the resources consumed during operation.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Azure Functions as the compute solution for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resource:

- Function apps

> [!NOTE]
> This service guide builds on guidance in the [Azure App Service (Web Apps)](./app-service-web-apps.md) service guide. Azure Functions is a serverless compute service. Functions are deployed on App Service plans, which provide the underlying compute infrastructure for your workload. You can choose from several hosting plans.
>
> The Consumption plan is fully managed and doesn't support access to the associated App Service instance. The Dedicated and Premium hosting plans give you the ability to configure and manage the App Service instance. Refer to the App Service service guide for recommendations and considerations when you choose the Dedicated or Premium hosting plan.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the specific features and configurations of Azure Functions. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Understand the triggers:** Ensure that you fully understand how Azure Functions is triggered. Triggers include HTTP requests, timers, queues, and more. Choose triggers that match the reliability requirements of your application.
>
>   For example, if your function processes messages from a queue, ensure that the queue itself is resilient and that messages can be reprocessed if a failure occurs.
>
> - **Implement retries and durable patterns:** Use built-in retry policies for transient failures. For more complex workflows, consider using the Durable Functions extension of Azure Functions. It provides state management and coordination across multiple function operations.
>
>   Durable Functions is particularly useful for scenarios that require reliability across long-running workflows. It supports automatic retries and durable task management.
>
> - **Ensure proper exception handling:** Implement robust exception handling to help ensure that functions fail gracefully. Log errors and consider implementing alerting mechanisms for critical failures.
>
> - **Plan for scalability:** Azure Functions automatically scales out based on demand. Ensure that your application is designed to handle spikes by testing its performance under load.
>
>   Consider using Application Insights to monitor performance and track how your functions scale in real time.
>
> - **Design for idempotency:** Ensure that your functions can be safely retried without causing unintended side effects. Idempotency is crucial for functions that interact with external systems or modify data.
>
> - **Use Durable Functions for long-running operations:** For operations that require orchestration or long-running processes, use Durable Functions to maintain state and help ensure reliability across multiple steps.
>
> - **Monitor the health of your solution:** Integrate your Azure Functions solution into your overall workload health monitoring and alerting system.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Configure **automatic retries** for transient errors. Learn more about [Azure Functions error handling and retries](/azure/azure-functions/functions-bindings-error-pages). | Improves reliability by automatically retrying failed runs, which reduces the likelihood of data loss or interruption. |
| Use **Durable Functions** for orchestrating complex workflows and long-running processes. [Learn more about Durable Functions](/azure/azure-functions/durable/durable-functions-overview). | Provides reliable operation of long-running workflows, with built-in state management and automatic retries.  |
| Implement **monitoring** and **centralized logging** by using Application Insights. [Set up Application Insights](/azure/azure-functions/configure-monitoring). | Enhances monitoring and troubleshooting by providing detailed insights into function operations and dependencies. |
|**Scale out automatically** based on the Functions hosting plan, trigger type, and demand. [Learn more about scaling](/azure/azure-functions/functions-scale). | Helps ensure that your application can handle increases in traffic without manual intervention, which improves reliability and performance. |
|**Use Availability Zones for enhanced resilience.** The [Flex Consumption plan](/azure/azure-functions/flex-consumption-plan) supports deployment across Availability Zones for zone-redundant resilience. | Provides protection against datacenter-level failures through automatic failover across availability zones, enhancing overall application reliability. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around Azure Functions.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use managed identities:** Enable [managed identities](/azure/app-service/overview-managed-identity) for your function apps to securely access other Azure services without needing to manage credentials.
>
> - **Secure your function app with Microsoft Entra ID:** Restrict access to your functions by configuring Azure Functions to require [Microsoft Entra ID authentication](/azure/app-service/configure-authentication-provider-aad).
>
> - **Apply network security controls:** Understand the available [Azure Functions networking options](/azure/azure-functions/functions-networking-options) for your hosting model to help secure the Azure Functions networking configuration. Secure the networking to meet your requirements.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| **Enable managed identities** for secure access to Azure resources. [Enable managed identities](/azure/app-service/overview-managed-identity) | Simplifies credential management by eliminating the need to store and rotate secrets. This approach enhances security. |
| Use **Azure Key Vault** for secrets management and regular rotation. [Integrate Key Vault with Azure Functions](/azure/app-service/app-service-key-vault-references). | Helps protect sensitive information, such as API keys and connection strings, by storing it securely and automating secret rotation when it isn't feasible to use a managed identity. |
|[**Integrate with a virtual network** and use **private endpoints**](/azure/azure-functions/functions-create-vnet). | Secures function apps by restricting access to your internal network and preventing exposure to the public internet. </br></br> Virtual network integration and private endpoints aren't available in the Consumption hosting plan. |
|**Use network-restricted Key Vault and App Configuration access.** The [Flex Consumption plan](/azure/azure-functions/flex-consumption-plan) supports network-restricted references to Key Vault and App Configuration services. | Enhances security by enabling secure access to configuration and secrets through private network connectivity, reducing exposure to public endpoints. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [**Cost Optimization design principles**](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Functions and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Choose the right pricing plan:** Azure Functions has multiple pricing plans, including the Consumption plan, Premium plan, and Dedicated (App Service) plan. Choose a [pricing plan](/azure/azure-functions/functions-scale) that aligns with your workload and cost considerations.
>
>   The Consumption plan is ideal for unpredictable workloads that have infrequent operations because you only pay for the operation time and resources consumed. When your app is idle, you aren't charged.
>
> - **Optimize operation time:** Reduce operation time by optimizing function code. Minimize the use of external dependencies and optimize code logic to decrease the duration of each operation.
>
> - **Monitor and analyze costs:** Regularly monitor function app usage and costs by using [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) and set up alerts to detect cost anomalies.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use the **Consumption plan** for workloads with unpredictable traffic. [Understand the Consumption plan](/azure/azure-functions/functions-scale). | Reduces costs by charging only for the resources used when functions are performed, which avoids costs associated with idle resources. |
|**Consider the Flex Consumption plan** for workloads requiring both cost efficiency and advanced features. See, [Flex Consumption](/azure/azure-functions/flex-consumption-plan). | Combines serverless cost benefits with enhanced capabilities like Availability Zones and VNet integration, providing cost optimization with improved reliability and security features. |
| Reserve capacity for **[Elastic Premium plans](/azure/azure-functions/functions-premium-plan) or [Dedicated App Service plans](/azure/azure-functions/dedicated-plan)** if your workload is predictable. | Lowers costs through discounted pricing for predictable workloads with steady usage patterns. |
| Regularly **monitor costs** and set up alerts for anomalies. [Monitor costs with alerts](/azure/cost-management-billing/cost-management-billing-overview#monitor-costs-with-alerts). | Helps identify cost spikes early, which enables proactive management and optimization. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [**Operational Excellence design principles**](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Functions.

> [!div class="checklist"]
>
> - **Automate deployments:** Use continuous integration and continuous delivery (CI/CD) pipelines to automate the deployment of your function apps. Integrate with Azure DevOps or GitHub Actions for seamless deployment workflows.
>
> - **Implement health monitoring:** Use Azure Monitor and Application Insights to track the health and performance of your functions. Set up alerts for critical metrics and use custom dashboards for real-time insights. For more information, see [Monitor Azure Functions](/azure/azure-functions/monitor-functions).
>
> - **Deploy your solution safely:** Understand the [deployment models](/azure/azure-functions/functions-deployment-technologies) available for Azure Functions and adopt the model that best meets your safe deployment practices.
>
> - **Plan for disaster recovery (DR):** [Implement DR strategies](/azure/reliability/reliability-functions) by using cross-region DR and availability zones for critical functions.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Automate deployments with **CI/CD pipelines** by using Azure DevOps or GitHub Actions. [Set up CI/CD](/azure/azure-functions/functions-continuous-deployment). | Improves deployment consistency, reduces manual errors, and accelerates time-to-market for new features. |
| Use [**deployment slots**](/azure/azure-functions/functions-deployment-slots) for staging changes before the production release. | Reduces the risk of introducing errors into production and enables safe rollbacks if problems are detected. |
| Implement **centralized monitoring** using Application Insights and Azure Monitor by using available [metrics](/azure/azure-functions/monitor-functions-reference?tabs=consumption-plan). | Enhances visibility into function performance, which helps identify and resolve problems quickly. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [**Performance Efficiency design principles**](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Functions.

> [!div class="checklist"]
>
> - **Optimize cold starts:** Minimize the effects of cold starts by using Premium Flex plans that provide prewarmed instances or ensure that your functions remain warm by using strategies such as the Azure Functions warmup trigger.
>
> - **Optimize function code:** Write efficient code to reduce operation time and resource consumption. Avoid long-running operations and optimize external service calls.
>
> - **Enable autoscaling:** Leverage the autoscaling capabilities of Azure Functions to automatically scale out based on demand. Ensure that your scaling rules are well-defined and tested.
>
> - **Monitor performance metrics:** Use Application Insights to [monitor key performance metrics](/azure/azure-functions/monitor-functions), such as operation time, CPU, and memory usage. Set up alerts for performance degradation.

### Configuration recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use the **Elastic Premium plan with a prewarmed instance** to minimize cold-start latency. [Understand cold starts](/azure/azure-functions/functions-scale#cold-start-behavior). | Reduces the latency associated with cold starts, which improves response times for time-sensitive applications. |
| **Optimize function code** to reduce operation time. Understand the [best practices for Azure Functions](/azure/azure-functions/performance-reliability). | Enhances performance by reducing the time and resources required for each function to run. |
| **Enable autoscaling** to automatically adjust capacity based on demand. [Configure autoscaling](/azure/azure-functions/functions-scale). | Ensures that your function apps can handle varying loads without manual intervention. This approach helps maintain performance under pressure. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Functions and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- All function apps have managed identities enabled.
- Function apps are using only private endpoints for network security.
- Diagnostic logging is enabled for all function apps.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/governance/policy/samples/built-in-policies#network) and other policies that might affect the security of the network layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Web applications architecture design](/azure/architecture/web-apps).

## Next steps

Consider the following resources to further explore the recommendations highlighted in this document:

- [Serverless event processing](/azure/architecture/reference-architectures/serverless/event-processing)
- [Getting started with Azure Functions](/azure/azure-functions/functions-get-started)

<!-- Updated: August 17, 2025 for Azure Update 498149 -->
<!-- Updated: November 21, 2025 for Azure Update 512379 -->

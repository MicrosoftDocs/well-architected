---
title: Azure Well-Architected Framework perspective on Azure Functions
description: Explore Azure Well-Architected Framework design considerations and configuration recommendations for Azure Functions.
author: PageWriter-MSFT
ms.author: mohamad
ms.date: 04/25/2024
ms.topic: conceptual
ms. service: waf
ms. subservice: waf-service-guide
products:
  - azure-functions
azure.category:
  - compute
---


# Azure Well-Architected Framework perspective on Azure Functions

Azure Functions is a serverless compute service that allows you to run event-driven code without having to manage infrastructure explicitly. As a Functions as a Service" (FaaS) offering, Azure Functions abstracts the underlying infrastructure, allowing you to focus on your code. This service automatically scales based on demand and charges you only for the resources consumed during execution.

This document assumes that as an architect, you have reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Azure Functions as the compute solution for your workload. The guidance provided here aligns with the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section contains a *design checklist* that highlights architectural areas of concern with specific design strategies related to Azure Functions.
>
> The *recommendations* offer technology capabilities to help implement these strategies. This list is not exhaustive but includes key recommendations to help you build or optimize your workloads with Azure Functions.
>
> Foundational architecture showcasing the key recommendations: [Serverless Functions Architecture Design](/azure/architecture/serverless-quest/serverless-overview)

##### Technology scope

This review focuses on Azure Functions and their associated resources, including:

- Function apps

>[!NOTE]\
>This service guide builds upon guidance found in the [App Service](./app-service-web-apps.md) service guide. Azure Functions are deployed on App Service Plans and you can configure the underlying App Service for your Functions app if you use Premium or Dedicated hosting plans.

## Reliability

The Reliability pillar ensures continuous functionality by **building resilience and enabling fast recovery from failures**.

The [**Reliability design principles**](../reliability/principles.md)   provide high-level design strategies that apply to individual components, system flows, and the overall system.

##### Design checklist

Start your design strategy based on the [Reliability design principles](../reliability/principles.md). Evaluate its relevance to your business requirements while considering the specific features and configurations of Azure Functions.

> [!div class="checklist"]
>
> - **Understand the triggers**: Ensure that you fully understand how Azure Functions are triggered. Triggers include HTTP requests, timers, queues, and more. Choose triggers that match the reliability requirements of your application.
>
>   For example, if your function processes messages from a queue, ensure that the queue itself is resilient and that messages can be reprocessed if a failure occurs.
>
> - **Implement retries and durable patterns**: Use built-in retry policies for transient failures. For more complex workflows, consider using Durable Functions, which offer state management and coordination across multiple function executions.
>
>   Durable Functions are particularly useful for scenarios that require reliability across long-running workflows. They support automatic retries and durable task management.
>
> - **Ensure proper exception handling**: Implement robust exception handling to ensure that functions fail gracefully. Log errors and consider implementing alerting mechanisms for critical failures.
>
> - **Plan for scalability**: Azure Functions automatically scale out based on demand. Ensure that your application is designed to handle spikes by testing its performance under load.
>
>   Consider using Application Insights to monitor performance and track how your functions scale in real-time.
>
> - **Design for idempotency**: Ensure that your functions can be safely retried without causing unintended side effects. Idempotency is crucial for functions that interact with external systems or modify data.
>
> - **Use Durable Functions for long-running operations**: For operations that require orchestration or long-running processes, use Durable Functions to maintain state and ensure reliability across multiple steps.
>
> - **Monitor the health of your solution**: Integrate your Azure Functions solution into your overall workload health monitoring and alerting system.

##### Recommendations 

| Recommendation | Benefit |
|----------------|---------|
| **(Function App)** Configure **automatic retries** for transient errors. Learn more about [Azure Functions error handling and retries](/azure/azure-functions/functions-bindings-error-pages) | Improves reliability by automatically retrying failed executions, reducing the likelihood of data loss or interruption. |
| **(Function App)** Use **Durable Functions** for orchestrating complex workflows and long-running processes. [Learn more about Durable Functions](/azure/azure-functions/durable/durable-functions-overview). | Provides reliable execution of long-running workflows, with built-in state management and automatic retries. |
| **(Function App)** Implement **Monitoring** and **centralized logging** using Application Insights. [Set up Application Insights](/azure/azure-functions/configure-monitoring). | Enhances monitoring and troubleshooting by providing detailed insights into function executions and dependencies. |
| **(Function App)** **Scale out automatically** based on the Function Hosting Plan, trigger type and demand. [Learn about scaling](/azure/azure-functions/functions-scale). | Ensures that your application can handle increases in traffic without manual intervention, improving reliability and performance. |


## Security

The Security pillar is centered on ensuring the confidentiality, integrity, and availability of your data and services.

The [**Security design principles**](../security/principles.md) offer a high-level strategy for securing your Azure Functions, including best practices for authentication, authorization, and data protection.

##### Design checklist

Start your design strategy with the [**design review checklist for Security**](../security/checklist.md) and identify potential vulnerabilities. Extend your strategy by incorporating additional security measures as necessary.

> [!div class="checklist"]
>
> - **Use managed identities**: Enable managed identities for your function apps to securely access other Azure services without needing to manage credentials. [Learn about managed identities](/azure/app-service/overview-managed-identity). 
>
> - **Secure your function app with Microsoft Entra ID**: Restrict access to your functions by configuring Azure Functions to require Microsoft Entra ID authentication. [Set up Microsoft Entra authentication](/azure/app-service/configure-authentication-provider-aad).
>
> - **Apply network security controls**:  Understand the [available options]( /azure/azure-functions/functions-networking-options) for your hosting model to secure the Functions networking configuration. Secure the networking to meet your requirements.
>
##### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Enable **managed identities** for secure access to Azure resources. [Enable managed identities](/azure/app-service/overview-managed-identity) | Simplifies credential management by eliminating the need to store and rotate secrets, enhancing security. |
| Use **Azure Key Vault** for secrets management and regular rotation. [Integrate Key Vault with Azure Functions](/azure/app-service/app-service-key-vault-references). | Protects sensitive information, such as API keys and connection strings, by storing them securely and automating secret rotation when using a managed identity is not feasible. |
| **Integrate with a virtual network** and use **private endpoints**.  | Secures function apps by restricting access to your internal network and preventing exposure to the public internet. </br></br> Virtual network integration and private endpoints are not available in the Consumption hosting plan. |

## Cost Optimization

The Cost Optimization pillar focuses on identifying ways to reduce costs while maintaining necessary performance levels.

The [**Cost Optimization design principles**](../cost-optimization/principles.md) provide strategies for balancing cost and performance, ensuring that your Azure Functions deployment is efficient and cost-effective.

##### Design checklist

Begin your cost optimization strategy with the [design review checklist for Cost Optimization](../cost-optimization/checklist.md), and adjust your design to align with budgetary requirements.

> [!div class="checklist"]
>
> - **Choose the right pricing plan**: Azure Functions offers multiple pricing plans, including the Consumption plan, Premium plan, and Dedicated (App Service) plan. Choose a plan that aligns with your workload and cost considerations. [Compare pricing plans](/azure/azure-functions/functions-scale).
>
>   The Consumption plan is ideal for unpredictable workloads with infrequent executions, as you only pay for the execution time and resources consumed. When your app is idle, you aren't charged.
>
> - **Optimize execution time**: Reduce execution time by optimizing function code. Minimize the use of external dependencies and optimize code logic to decrease the duration of each execution.
>
> - **Monitor and analyze costs**: Regularly monitor function app usage and costs using Azure Cost Management and set up alerts detect cost anomalies.[Learn about cost management and optimization](/azure/cost-management-billing/cost-management-billing-overview).
>

##### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use the **Consumption plan** for workloads with unpredictable traffic. [Understand the Consumption plan](/azure/azure-functions/functions-scale). | Reduces costs by charging only for the resources used during function execution, avoiding costs associated with idle resources. |
| **(Function App)** Reserve capacity for **[Elastic Premium](/azure/azure-functions/functions-premium-plan) plans or [Dedicated App Service Plans](/azure/azure-functions/dedicated-plan)** if your workload is predictable. | Lowers costs through discounted pricing for predictable workloads with steady execution patterns. |
| Regularly **monitor costs** and set up alerts for anomalies. [Set up cost alerts](/azure/cost-management-billing/cost-management-billing-overview). | Helps identify cost spikes early, allowing for proactive management and optimization. |

## Operational Excellence

Operational Excellence focuses on processes and procedures for deployment, observability, and management of your Azure Functions applications.

The [**Operational Excellence design principles**](../operational-excellence/principles.md) provide strategies to ensure that your function apps are deployed, managed, and monitored efficiently.

##### Design checklist

Start your design strategy based on the [design principles for Operational Excellence](../operational-excellence/principles.md) offer strategies to maintain the operational quality of Azure Functions.

> [!div class="checklist"]
>
> - **Automate deployments**: Use CI/CD pipelines to automate the deployment of your function apps. Integrate with Azure DevOps or GitHub Actions for seamless deployment workflows. 
>
> - **Implement health monitoring**: Use Azure Monitor and Application Insights to track the health and performance of your functions. Set up alerts for critical metrics and use custom dashboards for real-time insights. [Monitor Azure Function](/azure/azure-functions/monitor-functions).
>
> - **Deploy your solution safely**: Understand the [deployment models](/azure-functions/functions-deployment-technologies) available for Azure Functions and adopt the model that best meets your safe deployment practices.
>
> - **Plan for disaster recovery**: Implement disaster recovery strategies by using cross-region disaster recovery and availability zone for critical functions. [Plan for DR](/azure/reliability/reliability-functions).

##### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Automate deployments with **CI/CD pipelines** using Azure DevOps or GitHub Actions. [Set up CI/CD](/azure/azure-functions/functions-continuous-deployment) | Improves deployment consistency, reduces manual errors, and accelerates time-to-market for new features. |
| Use **deployment slots** for staging changes before production release. [Deploy using slots](/azure/azure-functions/functions-deployment-slots). | Reduces the risk of introducing errors into production and enables safe rollbacks if issues are detected. |
| Implement **centralized monitoring** using Application Insights and Azure Monitor using available [metrics](/azure/azure-functions/monitor-functions-reference?tabs=consumption-plan). | Enhances visibility into function performance, helping identify and resolve issues quickly. |

## Performance Efficiency

Performance Efficiency ensures **optimal user experience even under increased load** by managing capacity effectively.

The [**Performance Efficiency design principles**](../performance-efficiency/principles.md) provide strategies to help you design function apps that meet performance requirements, even as demand increases.

##### Design checklist

Start your performance strategy with the [Design principles of Performance Efficiency](../performance-efficiency/principles.md), and design your function apps to scale and perform optimally.

> [!div class="checklist"]
>
> - **Optimize cold start**: Minimize the impact of cold starts by using Premium Flex plans with pre-warmed instance or ensuring that your functions remain warm with strategies such as Azure Functions warmup trigger. 
>
> - **Optimize function code**: Write efficient code to reduce execution time and resource consumption. Avoid long-running operations and optimize external service calls.
>
> - **Enable autoscaling**: Leverage the autoscaling capabilities of Azure Functions to automatically scale out based on demand. Ensure that your scaling rules are well-defined and tested. 
>
> - **Monitor performance metrics**: Use Application Insights to monitor key performance metrics, such as execution time, CPU and memory usage. Set up alerts for performance degradation. [Monitor performance](/azure/azure-functions/monitor-functions).

##### Recommendations

| Recommendation | Benefit |
|----------------|---------|
| Use the **Elastic Premium plan with a Pre-warmed instance** to minimize cold start latency. [Understand cold start](/azure/azure-functions/functions-scale). | Reduces the latency associated with cold starts, improving response times for time-sensitive applications. |
| **Optimize function code** to reduce execution time. [Best practices for Azure Functions](/azure/azure-functions/performance-reliability). | Enhances performance by reducing the time and resources required for each function execution. |
| **Enable autoscaling** to automatically adjust capacity based on demand. [Configure autoscaling](/azure/azure-functions/functions-scale). | Ensures that your function apps can handle varying loads without manual intervention, maintaining performance under pressure. |

## Azure policies

Azure provides a comprehensive set of built-in policies to audit and enforce configurations for Azure Functions and their dependencies. You can use Azure Policy to ensure that your function apps adhere to organizational standards for security, cost optimization, and performance.

For example, you can enforce policies that require:

- Managed identities to be enabled for all function apps.
- Function apps to use only private endpoints for network security.
- Diagnostic logging to be enabled for all function apps.

Review the [Azure Policy built-in definitions](/azure/governance/policy/samples/built-in-policies) to find policies that align with your organizational requirements.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/advisor-overview) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. The following recommendations can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your function apps:

- [Reliability](/azure/advisor/advisor-reference-reliability-recommendations)
- [Security](/azure/defender-for-cloud/plan-multicloud-security-get-started)
- [Cost Optimization](/azure/advisor/advisor-reference-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

Consider the following resources to further explore the recommendations highlighted in this document:

- Reference architectures:
  - [Serverless web application architecture](/azure/architecture/web-apps/serverless/architectures/web-app)
  - [Serverless event processing](/azure/architecture/reference-architectures/serverless/event-processing)
  

---
title: Azure Well-Architected Framework review for Azure Container Apps (ACA)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Container Apps (ACA).
author: igorjovovic
ms.author: igorjovovic
ms.topic: conceptual
ms.date: 01/16/2024
ms.product: azure-container-apps
azure.category:
  - containers
---

# Well-Architected Framework perspective on Azure Container Apps

This article provides architectural best practices for Azure Container Apps (ACA). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency


We assume you've reviewed the [Compute Decision Tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose Container Apps as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.

Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure Container Apps and its dependencies. Instead, they list the key recommendations mapped to the design perspectives.

Foundational architecture that demonstrates the key recommendations: [Baseline highly available, zone-redundant web application architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

TODO:

## Reliability

In the cloud, failures can happen. Instead of trying to prevent failures entirely, the goal is to architect for resiliency, ensuring that your application can continue to function and recover quickly. Reliability focuses on ensuring your application can recover from failures and continue to operate effectively. This section outlines best practices and design considerations to enhance the reliability of Azure Container Apps, ensuring high availability and fault tolerance in your deployments.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

In the **design checklist** and **list of recommendations** below, callouts are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Use Availability Zones**: Deploy production and critical workloads across multiple availability zones to ensure resilience and minimize the impact of zone failures.  
> - **Implement Horizontal Auto-Scaling:** Configure auto-scaling using scale rules based on HTTP requests, TCP connections, or custom metrics (e.g., CPU, memory, Azure Service Bus, Azure Event Hubs, Apache Kafka, Redis) to dynamically manage loads and maintain high availability during peak usage times.
> - **Configure Health Probes**: Set up startup, readiness, and liveness probes for all services to monitor and maintain application health.  
> - **Enable Monitoring and Alerts**: Activate monitoring tools and set up alerts to detect and respond to reliability-impacting events promptly (e.g., Azure Monitor, OpenTelemetry).  
> - **Choose Appropriate SKU Configurations**: Select an environment SKU that aligns with the resource and performance requirements of your container apps.

#### Recommendations

| **Recommendation** | **Benefit** |
|--------|----|
| **Store your Azure Container Apps environment configurations as IaC in source control and set up pipelines to redeploy to another region.** | Ensures critical data/configs can be quickly restored and redeployed in another region, enhancing disaster recovery and minimizing downtime. |
| **Resource Quotas: Define resource quotas and limits.** | Prevents resource contention and avoids performance degradation. Use monitoring over time to observe resource usage and tune quotas/limits accordingly. |
| **Deployment Strategies: Use revisions for blue-green or canary deployments.** | Minimizes downtime and reduces risk during releases by enabling safe rollouts and quick rollbacks. Labels can help manage revisions for UAT or previews. |
| **Use volume mounts for stateful apps and Azure Zone-Redundant Storage (ZRS).** | Ensures data persistence across restarts/failures. ZRS protects data against zonal outages. |
| **Enable Container Apps zone redundancy.** | Replicas automatically distribute across zones, and traffic shifts to healthy zones if one fails. |
| **Implement resilient recovery with cross-region deployment.** | Multi-region deployments with Azure Front Door or Traffic Manager ensure business continuity. If a region goes down, traffic is redirected to a secondary region. |
| **Implement liveness and readiness probes.** | Automatically handles unhealthy containers by restarting them and only routing traffic to healthy instances. |
| **Leverage built-in observability features.** | Built-in tools (log streaming, console, metrics, alerts) enable proactive monitoring and efficient debugging. |
| **Implement Service Discovery Resiliency Policies.** | Configuring retries, timeouts, and circuit breakers boosts reliability and prevents cascading failures. |

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

### Policy definitions
TODO:

- **Ensure Volume Mounts**: Container Apps environments should be configured with volume mounts to ensure data persistence and integrity.  
- **Enable Diagnostics Settings**: Enable logging by category group for Container Apps environments (`microsoft.app/managedenvironments`) to Storage.  

## Security

Security is a critical aspect of any cloud architecture, and ensuring the protection of your applications and data is paramount. This section provides best practices and recommendations for securing Azure Container Apps. By implementing robust security measures, you can safeguard your applications against threats, ensure compliance with regulatory requirements, and maintain the integrity and confidentiality of your data. Security encompasses a range of practices, including authentication, network isolation, encryption, and identity management, all designed to create a secure and resilient application environment.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

#### Design checklist

> [!div class="checklist"]
> - **Use Managed Identities with Microsoft Entra ID:** Ensure container apps use managed identities for secure, credential-free access to Azure resources.  
> - **Deploy private container apps environments:** Use private environments and internal ingress mode for isolation from the public internet.  
> - **Use Azure Key Vault:** Securely store sensitive configuration values and secrets in Azure Key Vault to protect them from unauthorized access.  
> - **Secure CI/CD pipelines:** Implement container-aware scanning in your CI/CD pipelines to detect vulnerabilities and ensure the integrity of your container images. Refer to the Container Secure Supply Chain documentation for details on this topic.  
> - **Enable mTLS (Mutual TLS):** Use mutual TLS to authenticate and encrypt traffic between services, enhancing security by ensuring both parties are verified.
> - **Control egress traffic:** Integrate your container apps environment into a custom virtual network with user-defined routes to secure outbound traffic.  
> - **Enforce HTTPS:** Configure Envoy proxy to redirect all HTTP traffic to HTTPS (default is allowInsecure: false).  
> - **Enable logging options:** Capture detailed logs for monitoring and auditing.  
> - **Use lean base images:** Reduce the attack surface by using minimal base images (e.g., Alpine, Ubuntu Chiseled).  
> - **Secure Container Images:** Consider enabling Microsoft Defender for Containers to scan images in ACR for vulnerabilities.

### Recommendations

Explore the following table of recommendations to optimize your Azure Container Apps configuration for security.



### Azure Policy definitions


## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Gateway and its environment.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Kubernetes Service** from the available products. You can test different configuration and payment plans in the calculator.



#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]

#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient AKS workloads, in [Sustainable software engineering principles in Azure Kubernetes Service (AKS)](/azure/aks/concepts-sustainable-software-engineering).

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management.**

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]


#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported AKS versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.



#### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]



#### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



## Azure policies

Azure provides an extensive set of built-in policies related to App Service and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

[Add your content.]

- [policy-description]
- [policy-description]
  ...
- [policy-description]


## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Application Gateway.

- [Reliability](/azure/advisor/advisor-reference-reliability-recommendations#application-gateway)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#make-sure-you-have-enough-instances-in-your-application-gateway-to-support-your-traffic)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#networking)


## Related resources

[Add your content.]



## Next step -or- Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)



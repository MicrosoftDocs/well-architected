---
title: Architecture Best Practices for Azure API Management
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure API Management.
author: dlepow
ms.author: danlep
ms.topic: conceptual
ms.date: 03/21/2025
ms.subservice: waf-service-guide
products:
 - azure-api-management
azure.category:
 - integration
---

# Architecture best practices for Azure API Management

Azure API Management is a hybrid, multicloud management platform for APIs across all environments. As a platform-as-a-service, API Management supports the complete API lifecycle.  

This article assumes that as an architect, you've reviewed the [integration services decision tree](/azure/logic-apps/azure-integration-services-choose-capabilities) and chosen Azure API Management as the integration service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the Well-Architected Framework pillars. 

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural
areas of concern along with design strategies localized to the technology
scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for API Management and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [Azure API Management Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/api-management/landing-zone-accelerator#design-guidelines).


**Technology scope**

**The scope of this guide is the API Management service, primarily the gateway component (data plane)** which is used to proxy API requests from client applications to backend service hosted in various locations. However, the architecture of the system or solution includes the API Management control plane, components such as the client apps accessing the gateway and the backend APIs that the gateway routes requests to, and supporting Azure services for networking, monitoring, identity management, and other capabilities. 

> [!NOTE]
> Not all recommendations apply to all [service tiers](/azure/api-management/api-management-features) of API Management. Many recommendations in this guide focus on the Premium tier of API Management, which is the most feature-rich tier.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles)
provide a high-level design strategy applied for individual components,
system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the
[design review checklist for Reliability](../reliability/checklist.md). Determine
its relevance to your business requirements while keeping in mind the
tiers and features of API Management and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Evaluate gateway capabilities for reliability and redundancy**: Decide on the API Management [tier and features](/azure/api-management/api-management-features) that are needed to meet the workload's reliability requirements. Evaluate gateway redundancy features including availability zones, multiple gateway units, multiple regions, and workspaces, all of which are supported in the Premium tier. The Developer tier, which is not backed by SLA, should not be considered for production workloads Also consider tradeoffs of adopting features such as external caching that can introduce potential points of failure and/or performance bottlenecks. 
>
> - **Review observability capabilities** Understand the service's [observability capabilities](/azure/api-management/observability) - including Azure Monitor logs and metrics, Application Insights, built-in analytics, and built-in diagnostics - and how you to use them to meet your workload's reliability requirements. For example, consider using [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-overview) to notify you of potential issues with the API Management gateway or its dependencies.  
>
> - **Review scaling strategies**: Define criteria for [scaling out](/azure/api-management/upgrade-and-scale) the gateway by adding units to maintain service reliability. Consider whether to scale based on requests and/or exceptions. Consider whether other components of the solution (for example, network address spaces and backends) need to scale.  
> 
> - **Design to handle critical workloads**: Decide when to use dedicated gateways (for example, a dedicated [workspace gateway](/azure/api-management/workspaces-overview) or a separate API Management instance) for critical APIs to ensure high availability and performance, or other critical considerations such as data sovereignty. Shared gateways can be used for less critical APIs to optimize resource usage. If multiple API Management instances are needed for mission-critical workloads, customers should plan their own solutions to keep the environments synchronized. 
> 
> - **SLO alignment**: Factor in the gateway's SLA scope when setting your workload's SLOs. Use monitoring tools such as alerting on [gateway metrics](/azure/api-management/monitor-api-management-reference#metrics) (Capacity in classic tiers, CPU and memory % of gateway metrics in v2 tiers) , diagnostics, and Application Insights to ensure the gateway meets required objectives. The service provides its own SLA and customer is responsible for SLAs on other components such as API backends. 
> 
> - **Decide on backend fault tolerance**: Plan for unexpected backend failures and test client experiences in such scenarios. Evaluate gateway [policies](/azure/api-management/api-management-policies) for quotas, rate limits, retry policies, backend circuit breakers and load balancing, and exception handling to improve system resiliency. However, customer should decide on the resiliency of the backends because the APIM gateway can't be used for all backend fault tolerance. 
>
> - **Define testing strategies**: Plan to use a testing solution such as Azure Load Testing to reflect actual production workloads. Don't rely on published throughput or other estimates which might not apply to your project.  
>
> - **Plan for disaster recovery**: Review options for backing up and restoring the gateway infrastructure and APIs. While built-in [backup and restore capabilities](/azure/api-management/api-management-howto-disaster-recovery-backup-restore) may be useful in some cases, customer-managed options such as ApiOps tooling and infrastructure-as-code solutions may be sufficient. Develop strategies for maintaining other system data such as user subscriptions. Active/passive or active/active configurations can also be considered, although there are tradeoffs for costs and complexity. 

<!-- For example, consider whether the API Management services could be sensitive to changes from Azure-side updates and plan canary environments to detect changes before mission-critical systems are affected. --> 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
|**In multiregion configurations, configure automated scaling in all regions**. API Management supports [autoscaling with Azure Monitor](/azure/api-management/api-management-howto-autoscale) in the primary region. Implement custom function or logic app to handle scaling in secondary regions.  | Ensure that the API Management gateway can scale to meet demand in all regions without manual intervention.  |
| **Configure at least one replica (additional [availability zone](/azure/reliability/migrate-api-mgt) or [capacity unit](/azure/api-management/upgrade-and-scale)) per gateway** | Accommodate burst gateway demand instead of requiring immediate scale-out. Required for 99.99% SLA in Premium tier.  |
| **Implement federated API management with [workspaces](/azure/api-management/workspaces-overview)**.  | Segregate API runtime between API teams, improving reliability, resiliency, and security.  |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

* The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design in protecting the API Management gateway.
*  The [security baseline for API Management](/security/benchmark/azure/baselines/api-management-security-baseline) applies guidance from the [Microsoft cloud security benchmark version 1.0](/security/benchmark/azure/overview) to API Management. 

> [!NOTE]
> The checklist and recommendations in this section are focused on securing the API Management gateway. For more recommendations to mitigate API security threats with Azure API Management, see [Mitigate OWASP API security top 10 in Azure API Management](/azure/api-management/mitigate-owasp-api-threats).


### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - **Develop a change management plan**:  Identify the individuals and RBAC roles that are required to manage the service platform and CI/CD pipelines. Ensure that only authorized individuals have access to manage the service.  
>
> - **Evaluate data sensitivity**: If sensitive data could pass in API requests to and responses from the API Management gateway, develop strategies to protect it consistently throughout its lifecycle, and pay attention to data protection requirements in different regions. Evaluate service features such as [multiple regions](/azure/api-management/api-management-howto-deploy-multi-region)  to isolate certain data, and whether caching strategy is aligned.  
>
> - **Develop segmentation strategies**: If needed in your organization, segregate roles and networks (and possibly gateways) using [workspaces](/azure/api-management/workspaces-overview) so that different teams have appropriate access and control to APIs they manage.  
>
> - **Understand network controls**: Identify requirements and options for isolating or filtering inbound and/or outbound gateway traffic using [virtual networks](/azure/api-management/virtual-network-concepts). Can access to the gateway be restricted through Private Link, or is public access to the gateway required? Does architecture need to include a web application firewall such as Application Gateway or Azure Front Door to accomplish the required network isolation and to filter network traffic?  
>
> - **Understand capabilities for authentication and authorization**: Evaluate use of identity providers such as [Microsoft Entra ID](/azure/api-management/api-management-howto-protect-backend-with-aad) (with built-in groups) or [Azure AD B2C](/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c) to screen gateway requests and for OAuth authorization to backend APIs.  
> 
> - **Encrypt network traffic** - Identify the most secure TLS [protocol versions and ciphers](/azure/api-management/api-management-howto-manage-protocols-ciphers) that your workloads will support; don't require insecure TLS versions.  
>
> - **Evaluate service’s attack surface**: Can certain API Management components be disabled, limited, or removed (such as direct management API, public access to developer portal)?
>
> - **Identify secrets required by workloads**: Gateway operation may require certificates, API  keys, or other secret values. Review requirements and capabilities of Azure Key Vault to store secrets/certificates, or built-in API Management capabilities such as [named values](/azure/api-management/api-management-howto-properties) and [managed certificates](/azure/api-management/configure-custom-domain#domain-certificate-options). 


### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| **Manage and rotate secrets and certificates in Key Vault**. [Store secrets](/azure/api-management/api-management-howto-properties) and certificates in Key Vault and use managed identity with strict role-based access control for access  | Restrict access, manipulation, and exposure of secrets. Ensure that secrets are rotated regularly and automatically, and aren't hard-coded in code or configuration files.  |
| **Configure [custom domains](/azure/api-management/configure-custom-domain)** for the gateway endpoint(s). Combine with highest supported version(s) of TLS for end-to-end encryption of traffic.  | Encrypt data in transit to and from the gateway. Verify client and server identities.  |
| **Restrict or block traffic to the gateway.** Implement [rate-limiting](/azure/api-management/api-management-policies#rate-limiting-and-quotas) and [content validation](/azure/api-management/api-management-policies#content-validation) policies and tools such as [Azure DDoS Protection](/azure/api-management/protect-with-ddos-protection) and [Microsoft Defender for APIs](/azure/api-management/protect-with-defender-for-apis)<br/><br/>Limit traffic to the gateway using Application Gateway, Azure Front Door, network security group rules, and/or client IP filtering| Limit effectiveness of brute-force attacks and malicious content in API requests or responses. Detect and block bot or other unwanted traffic to the gateway. |
| **Do not use [API tracing](/azure/api-management/api-management-howto-api-inspector#enable-tracing-for-an-api) in production**. | Prevent sensitive data from being exposed in request traces.  |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure API Management and its environment.


### Design checklist

> [!div class="checklist"]
>
> - **Understand API Management’s cost model**: Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/), along with the organization’s account benefits and the organization’s criteria for SLA and scalability, to develop accurate cost estimates of using an API Management service tier. Decide whether a charge-back model is needed and how it will be calculated (using metrics, tags, tokens, and so on).  
  
  The service cost model is mainly influenced by the service tier, number of units, and number of gateways. Evaluate extra costs of maintaining reserve unit or an active-passive DR configuration if that is implemented. 
    
  If implementing [workspaces](/azure/api-management/workspaces-overview), evaluate the costs of implementing separate versus shared workspace gateways to manage distinct API flow needs of different API teams or stakeholders. 
>
> - **Estimate scaling costs**: Develop scaling criteria to maintain high utilization and reliability of resources (see [Reliability](#reliability) pillar). Evaluate baseline costs in a pre-production environment and perform testing to model costs of scaling.
>
> - **Evaluate service configurations and policies** such as [rate-limit](/azure/api-management/rate-limit-policy) and [limit-concurrency](/azure/api-management/limit-concurrency-policy) to manage request loads. Understand opportunity costs of insufficient capacity. 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| **Use Developer tier in lower environments**| Use a cost-effective tier for nonproduction uses, proof-of-concept deployments, and initial cost modeling.  |
| **Scale-in when demand decreases**. Configure [autoscale rules](/azure/api-management/api-management-howto-autoscale) or other automated processes to reduce units when gateway capacity drops below defined thresholds. | Reduce unnecessary costs in production. |


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles)
provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to API Management.


### Design checklist

> [!div class="checklist"]
>
> - **Review key knowledge and skills needed to operate the service**: Areas include API lifecycle, DevOps processes, networking and connectivity, monitoring and observability, and software development (for policy configuration, unit testing, and CI/CD pipelines).  
>
> - **Understand documentation needs**: Organization should commit to to documenting processes for service-level and API-level configuration and maintenance and, if workspaces are adopted, the different levels of access for platform team and API teams.  
>
> - **Evaluate standard tooling** to support operation of service: For example, implement [ApiOps](/azure/api-management/devops-api-development-templates) (DevOps and CI/CD to publish APIs) methods for API management and infrastructure-as-code tooling for service-level configuration changes. Design artifacts for reuse from development environments up to production. Consider use of linter such as Spectral in API approval pipelines.  
>
> - **Decide on key operational metrics and logs**: Review the [metrics](/azure/api-management/monitor-api-management-reference#metrics) (such as Capacity, CPU % and memory % of gateway, number of requests), logs, and [observability tools](/azure/api-management/observability) (Azure Monitor, Application Insights, or other tools) that will be used in production. What strategies or tools are needed to process a potentially large amount of observational data in production? What queries will provide the necessary insights?  
>
> - **Plan regular testing of production workloads** with Azure Load Testing.
>
> - **Identify operational tasks beyond CI/CD or infrastructure as code processes** that require automation: Plan automation in areas such as API Management policy source management, Azure policies, certain developer portal configurations.  


### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [**Log to Event Hubs**](/azure/api-management/api-management-howto-log-event-hubs) when processing large amounts of data  | Customize and reduce data that is logged. Enable near real-time analysis of API usage.  |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for API Management.

### Design checklist

> [!div class="checklist"]
>
> - **Define performance targets**: Key metrics to define performance of the API Management gateway are Capacity (or CPU % or Memory % of Gateway) for utilization of gateway infrastructure, request duration, and request throughput. In multi-region deployments, define performance targets per region. Customer needs to define appropriate scaling thresholds based on traffic patterns, API workloads, and scaling times. 
>
> - **Collect performance data**: Review capabilities of built-in analytics, Azure Monitor metrics, Azure Monitor logs, Application Insights and Event Hubs to collect and analyze performance at different levels of granularity. 
>
> - **Review how to identify live performance issues**: Indicators include Azure service availability, HTTP response errors, and errors raised in **Diagnose and solve problems** blade in the portal. Troubleshoot performance and availability issues using Application Insights, Kusto queries, recommendations from API Management Diagnostics in portal, and remediations surfaced in Defender for Cloud.
>
> - **Test performance**: Test performance under production conditions using Azure Load Testing.
>
> - **Evaluate adjacent services that may improve performance**: Caching policies and/or an external cache may improve performance of certain API operations. Azure Front Door or Application Gateway may be used for TLS offloading
>

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [**Define a sampling percentage for Application Insights**](/azure/api-management/api-management-howto-app-insights#performance-implications-and-log-sampling) | Satisfy observability data needs without affecting overall performance. |
| ** Use [Diagnose and solve problems**](/azure/api-management/diagnose-solve-problems) in the Azure portal.| Quickly Identify and troubleshoot gateway performance and service availability issues in production. |
| **Use the [Network status blade](/azure/api-management/api-management-using-with-vnet?tabs=stv2#verify-network-status)** in the portal to troubleshoot network connectivity. | For instances in classic tiers that are deployed in a virtual network, validate service connectivity to dependencies such as Azure Storage. |


## Azure policies

Azure provides an extensive set of built-in policies related to API Management and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Proper network controls are in place for the API Management gateway, such as deployment in a virtual network.
- The gateway is configured for zone redundancy.
- The service configuration endpoints aren't publicly accessible, and the Direct Management API is disabled.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/api-management/policy-reference) and other policies that might affect the security of the API Management gateway. 

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. 

For more information, see [Azure Advisor](/azure/advisor/).


## Tradeoffs
You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: Security and Cost Optimization

- Network isolation features such as virtual network injection can increase costs because of their availability only in higher cost tiers of API Management. However, they can also improve security and reliability of the API Management gateway.

:::image type="icon" source="../_images/trade-off.svg"::: Reliability and Cost Optimization

- Use of availability zones, multiple regions, and workspaces can increase costs because of their availability only in the Premium tier of API Management. However, they can also improve reliability and performance of the API Management gateway.

## Related content

[Add links.]



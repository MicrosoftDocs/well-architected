---
title: Architecture Best Practices for Azure API Management
description:Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure API Management.
author: dlepow
ms.author: danlep
ms.topic: concept-article
ms.date: 03/21/2025
ms.subservice: waf-service-guide
products:
 - azure-api-management
azure.category:
 - integration

---

<!--

This template provides the basic structure of a Well-Architected Framework (WAF)
service guide for an Azure offering article.

For more information, see the
[pattern instructions](waf-review-for-product-template-instructions.md).

Remove all comments in this template before you sign off
or merge to the main branch.

For metadata values to set, including products and Azure categories,
see [Taxonomies for Learn]
(https://review.learn.microsoft.com/help/platform/metadata-taxonomies?branch=main).

-->

# Architecture best practices for Azure API Management

Azure API Management is a hybrid, multicloud management platform for APIs across all environments. As a platform-as-a-service, API Management supports the complete API lifecycle.  

This article assumes that as an architect, you've reviewed the [integration services decision tree](/azure/logic-apps/azure-integration-services-choose) and chosen Azure API Management as the integration service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the Well-Architected Framework pillars. 

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural
areas of concern along with design strategies localized to the technology
scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for [Azure offering] and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations:
> [Azure API Management Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/api-management/landing-zone-accelerator#design-guidelines).

**Technology scope**

**The scope of this guide is the API Management service, primarily the gateway component (data plane)** which is used to proxy API requests from client applications to backend service hosted in various locations. However, the architecture of the system or solution includes the API Management control plane, components such as the client apps accessing the gateway and the backend APIs that the gateway routes requests to, and supporting Azure services for networking, monitoring, identity management, and other capabilities. 

## Reliability

The purpose of the Reliability pillar is to provide continued
functionality by **building enough resilience and the ability
to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles)
provide a high-level design strategy applied for individual components,
system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the
[design review checklist for Reliability](../reliability/checklist.md). Determine
its relevance to your business requirements while keeping in mind the
tiers and features of API Management and its dependencies. Extend the  rategy to include more approaches
as needed.

> [!div class="checklist"]
>
> - **Evaluate gateway capabilities for reliability and redundancy**: Decide on the API Management [tier and features](/azure/api-management/api-management-features) that are needed to meet the workload's reliability requirements. Evaluate how you will achieve gateway redundancy using features such as availability zones, multiple gateway units, multiple regions, and workspaces. The Premium tier supports all API Management gateway redundancy features.  

    Review the API Management features that benefit the workload to maintain simplicity and avoid potential points of failure and/or performance bottlenecks. Consider needs for features such as external caching that add complexity. 
>
> - **Review observability capabilities** Understand the service's [observability capabilities](/azure/api-management/observability) including Azure Monitor logs and metrics, Application Insights, built-in analytics, diagnostics, 
 monitoring strategy.  
>
> - **Review scaling strategies**: Define criteria for [scaling out](/azure/api-management/upgrade-and-scale) the gateway by adding units to maintain service reliability. In multiregion configurations, plan for autoscaling by Azure Monitor in the primary region and manual scaling in secondary regions (or scaling using custom tooling). Consider whether to scale based on requests and/or exceptions. Consider whether other components of the solution (for example, network address spaces and backends) need to scale.  
>
<!-->
> - **Understand needs for endpoint protection and network configuration** - Evaluate services such as Application Gateway, Azure Front Door, and Private Link to protect the gateway endpoint (see the Security pillar).  
--> 
> - **Design to handle critical workloads**: Decide when to use dedicated gateways (for example, a dedicated [workspace gateway](/azure/api-management/workspaces-oveview) or a separate API Management instance) for critical APIs to ensure high availability and performance, or other critical considerations such as data sovereignty. Shared gateways can be used for less critical APIs to optimize resource usage. If multiple API Management instances are needed for mission-critical workloads, customers should plan their own solutions to keep the environments synchronized. 
> 
> - **SLO alignment**: Factor in the gateway's SLA scope when setting your workload's SLOs. Use monitoring tools such as alerting on [gateway metrics](/azure/api-management/monitor-api-management-reference#metrics) (Capacity in classic tiers, CPU and memory % of gateway metrics in v2 tiers) , diagnostics, and Application Insights to ensure the gateway meets required objectives. The service provides its own SLA and customer is responsible for SLAs on other components such as API backends. 
> 
> - **Decide on backend fault tolerance**: Plan for unexpected backend failures and test client experiences in such scenarios. Evaluate gateway [policies](/azure/api-management/api-management-policies) for quotas, rate limits, retry policies, backend circuit breakers and load balancing, and exception handling to improve system resiliency. However, customer should decide on the resiliency of the backends because the APIM gateway can't be used for all backend fault tolerance. 
>
> - **Define testing strategies**: Plan to use a testing solution such as Azure Load Testing to reflect actual production workloads. Don't rely on published throughput or other estimates for capacity which might not be applicable for your project.  
>
> - **Plan for BC/DR**: Review options for backing up and restoring the gateway infrastructure and APIs. While built-in [backup/restore capabilities]() may be useful in some cases, customer-managed options such as ApiOps tooling and infrastructure-as-code solutions may be sufficient. Active/passive configuration can also be considered although there are tradeoffs for costs and complexity. Develop strategies for maintaining other data such as user subscriptions.
> - **Plan for disaster recovery**: Review options for backing up and restoring the gateway infrastructure and APIs. While built-in [backup and restore capabilities](/api-management-howto-disaster-recovery-backup-restore) may be useful in some cases, customer-managed options such as ApiOps tooling and infrastructure-as-code solutions may be sufficient. Develop strategies for maintaining other data such as user subscriptions.

    Active/passive or active/active configurations can also be considered, although there are tradeoffs for costs and complexity. For example, consider whether the API Management services could be sensitive to changes from Azure-side updates and plan canary environments to detect changes before mission-critical systems are affected. 


 

<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of recommendations
for optimizing the configuration of your Azure offering. The recommendations
should relate to the pillar and show how to materialize the vision of the
preceding design checklist items.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |

<!-- Required: Security H2

Include a standardized description of the pillar.

-->

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

* The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design in protecting the API Management gateway.
*  The [security baseline for API Management](/security/benchmark/azure/baselines/api-management-security-baseline) applies guidance from the [Microsoft cloud security benchmark version 1.0](/security/benchmark/azure/overview) to API Management. 

> [!NOTE]
> The checklist and recommendations in this section are focused on securing the API Management gateway. See more considerations for mitigating API security threats with Azure API Management: [Mitigate OWASP API security top 10 in Azure API Management](/azure/api-management/mitigate-owasp-api-threats).


### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - **Develop a change management plan**:  Identify the individuals and RBAC roles that are required to manage the service platform and CI/CD pipelines. Ensure that only authorized individuals have access to manage the service itself.  
>
> - **Evaluate data sensitivity**: If sensitive data could pass in API requests to and responses from the API Management gateway, develop strategies to protect it consistently throughout its lifecycle, and pay attention to data protection requirements in different regions. Evaluate service features such as [multiple regions](/azure/api-management/api-management-howto-deploy-multi-region)  to isolate certain data, and whether caching strategy is aligned.  
>
> - **Develop segmentation strategies**: If needed in your organization, segregate roles and networks (and possibly gateways) using [workspaces](/azure/api-management/workspaces-oveview) so that different teams have appropriate access and control to APIs they manage.  
>
> - **Understand capabilities for authentication and authorization**: Evaluate use of identity providers such as [Microsoft Entra ID](/azure/api-management/api-management-howto-protect-backend-with-aad) (with built-in groups) or [Azure AD B2C](/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c) to screen gateway requests and for OAuth authorization to backend APIs.  
>
> - **Understand network controls**: Identify requirements and options for isolating inbound and/or outbound gateway traffic using [virtual networks](/azure/api-management/virtual-network-concepts). Is public access to the gateway required? Does architecture need to include a web application firewall such as Application Gateway or Azure Front Door to accomplish the required network isolation and to filter network traffic?  
> 
> - **Encrypt network traffic** - Identify the most secure TLS [protocol versions and ciphers](/api-management-howto-manage-protocols-ciphers) that your workloads will support; don't require insecure TLS versions.  
>
> - **Evaluate service’s attack surface**: Can certain API Management components be disabled, limited, or removed (such as direct management API, public access to developer portal).  
>
> - **Identify secrets**: Do your workloads require certificates, API  keys, or other secret values? Review requirements and capabilities of Azure Key Vault to store secrets/certificates, or built-in API Management capabilities such as [named values](/azure/api-management/api-management-howto-properties) and [managed certificates](). 


### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| **Manage and rotate secrets and certificates in Key Vault**. [Store secrets](azure/api-management/api-management-howto-properties) and certificates in Key Vault and use managed identity with strict role-based access control for access  | * Restrict access, manipulation, and exposure of secrets.<br/><br/>* Ensure that secrets are rotated regularly and automatically. <br/><br/>* Ensure that secrets are not hard-coded in code or configuration files.  |
| **Configure [custom domains](/azure/api-management/configure-custom-domain)** for gateway endpoint. Combine with highest supported version(s) of TLS for end-to-end encryption of traffic.  | * Encrypt data in transit to and from the gateway<br/><br/>* Verify client and server identities  |
| **Restrict or block traffic to the gateway.** Implement [rate-limiting](/azure/api-management/api-management-policies#rate-limiting-and-quotas) and [content validation](/azure/api-management/api-management-policies#content-validation) policies and tools such as [Azure DDoS Protection](/azure/api-management/protect-with-ddos-protection) and [Microsoft Defender for APIs](/azure/api-management/protect-with-defender-for-apis)<br/><br/>* Limit traffic to the gateway using Application Gateway, Azure Front Door, network security group rules, and/or client IP filtering| * Limit effectiveness of brute-force attacks and malicious content in API requests or responses<br/><br/>* Detect and block bot or other unwanted traffic to the gateway  |
| **Do not use tracing in production**. | * Prevent sensitive data from being exposed in request traces  |


## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure API Management and its environment.



### Design checklist

> [!div class="checklist"]
>
> - **Understand API Management’s cost model**: Use [Azure pricing calculator](https://azure.microsoft.com/en-us/pricing/calculator/), along with the organization’s account benefits and the organization’s criteria for SLA and scalability, to develop accurate cost estimates of using an API Management service tier. Decide whether a chargeback model is needed and how it will be calculated (metrics, tags, tokens, and so on).  
  * The service cost model is mainly influenced by the service tier, number of units, and number of gateways. Evaluate extra costs of maintaining reserve unit or an active-passive DR configuration if that is implemented. 
  * If implementing [workspaces](/azure/api-management/workspaces-overview), evaluate the costs of implementing separate versus shared workspace gateways to manage distinct API flow needs of different API teams or stakeholders. 
> - **Estimate scaling costs**: Develop scaling criteria and autoscale rules to maintain high utilization and reliability of resources (see [Reliability](#reliability) pillar). Evaluate baseline costs in a pre-production environment and perform testing to model costs of scaling.
> - **Evaluate service configurations and policies** such as [rate-limit](/azure/api-management/rate-limit-policy) and [limit-concurrency](/azure/api-management/limit-concurrency-policy) to manage request loads. Understand opportunity costs of insufficient capacity. 


<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of recommendations
for optimizing the configuration of your Azure offering. The recommendations 
should relate to the pillar and show how to materialize the vision of the
preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles)
provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

Start your design strategy based on the [design review checklist for Operational Excellence](.. operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to API Management.


### Design checklist

> [!div class="checklist"]
>
> - **Review the knowledge and skills needed to operate the service**: Areas include API lifecycle, DevOps processes, networking and connectivity, monitoring and observability, and software development (for policy configuration, unit testing, and CI/CD pipelines).  
>
> -**Understand documentation needs**: Organization should commit to to documenting processes for service-level configuration and maintenance and, if workspaces are adopted, the different levels of access for platform team and API teams.  
>
> - **Evaluate standard tooling** to support operation of service: For example, implement [ApiOps processes](/azure/api-management/devops-api-development-templates) (DevOps and CI/CD to publish APIs) and infrastructure-as-code tooling for service-level configuration changes. Design artifacts for reuse from development environments up to production. Consider use of linter such as Spectral in API approval pipelines.  
>
> - **Decide on key operational metrics and logs**: Review the key [metrics](/azure/api-management/monitor-api-management-reference#metrics) (such as Capacity, CPU % and memory % of gateway, requests), logs, and [observability tools](/azure/api-management/observability) (Azure Monitor, Application Insights, or other tools) that will be used in production. What strategies or tools are needed to process a potentially large amount of observational data in production? What queries will provide the necessary insights?  

> - **Plan regular testing of production workloads** with Azure Load Testing.
>
> - **Identify operational tasks that require automation beyond CI/CD or infrastructure as code processes**: Plan automation in areas such as API Management policy source management, Azure policies, certain developer portal configurations .  


 

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [**Log to Event Hubs**](/api-management-howto-log-event-hubs) when processing large amounts of data  | * Customize and reduce data that is logged<br/><br/>* Enable near real-time analysis of API usage  |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles)
provide a high-level design strategy for achieving those capacity goals against the expected usage.

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for API Management.

-->

### Design checklist

> [!div class="checklist"]
>
> - [design-consideration]
> - [design-consideration]
>   ...
> - [design-consideration]


<!-- Required: Recommendations H3

In the second H3 of the pillar section, present a table of
recommendations for optimizing the configuration of your Azure
offering. The recommendations should relate to the pillar and
show how to materialize the vision of the preceding design principles.

-->

### Recommendations

| Recommendation | Benefit |
| ----- | ----- |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| [configuration-recommendation] | [problem-mitigated-by-recommendation] |
| ... | ... |



Azure provides an extensive set of built-in policies related to
API Management and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- [TBD]

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/api-management/policy-reference) and other policies that might affect the security of the API Management gateway. 


## Azure policies

[Add your content.]

- [policy-description]
- [policy-description]
  ...
- [policy-description]



## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. 

For more information, see [Azure Advisor](/azure/advisor/).

## Related resources

[Add your content.]

##  Related content

> [!div class="nextstepaction"]
> [Next sequential article title](link.md)

-or-

- [Related article title](link.md)
- [Related article title](link.md)
- [Related article title](link.md)


---
title: Architecture Best Practices for Azure API Management
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure API Management.
author: dlepow
ms.author: danlep
ms.topic: conceptual
ms.date: 04/01/2025
ms.subservice: waf-service-guide
products:
 - azure-api-management
azure.category:
 - integration
---

# Architecture best practices for Azure API Management

Azure API Management is a management platform and gateway for APIs across all environments, including hybrid and multicloud. As a platform-as-a-service, API Management helps support your workload's API lifecycle.

This article assumes that as an architect, you've reviewed the [integration services decision tree](/azure/logic-apps/azure-integration-services-choose-capabilities) and chosen Azure API Management as the integration service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the Well-Architected Framework pillars.

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for API Management or the application platform servers. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [Azure API Management landing zone architecture](/azure/architecture/example-scenario/integration/app-gateway-internal-api-management-function).

**Technology scope**

**The scope of this guide is the API Management service, primarily the gateway component (data plane)** which is used to proxy API requests from client applications to backend APIs hosted in application platforms or locations. However, the architecture of the workload must consider the API Management control plane, components such as the client apps accessing the gateway and the backend APIs that the gateway routes requests to, and supporting Azure services for networking, monitoring, identity management, and other capabilities.

This guide does not cover [Azure API Center](/azure/api-center/overview). This guide touches on API-level topics as it relates to their relationship to Azure API Management, not as a well-architected perspective on API design considerations.

> [!NOTE]
> Not all recommendations apply to all [service tiers](/azure/api-management/api-management-features) of API Management. Many recommendations in this guide focus on the Standard v2 and classic Premium tiers of API Management, which are the recommended production tiers for most enterprise workloads.

> [!TIP]
>
> The Premium v2 tier with enterprise capabilities is currently in preview. To decide if your design should depend on early access capabilities or on current generally available capabilities, estimate your design and implementation timelines relative to information available on the availability of Premium v2 and available migration paths.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and features of API Management and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Evaluate gateway capabilities for reliability and redundancy**: Decide on the API Management [tier and features](/azure/api-management/api-management-features) that are needed to meet the workload's reliability requirements, per environment.
>
>    Evaluate gateway redundancy features including availability zones, multiple gateway units, multiple regions, and workspaces, all of which are supported in the Premium tier. The Developer tier, which is not backed by SLA, should not be considered for production workloads. Also consider tradeoffs of adopting features such as external caching that can introduce potential points of failure and/or performance bottlenecks.
>
> - **Review observability capabilities**: Understand the service's [observability capabilities](/azure/api-management/observability), including Azure Monitor logs and metrics, Application Insights, built-in analytics, and built-in diagnostics. You'll use them to monitor your workload's reliability signals.
>
>   For example, consider using [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-overview) to notify you of potential issues with the API Management gateway or its dependencies.
>
> - **Review scaling strategies**: Define criteria for [scaling out](/azure/api-management/upgrade-and-scale) the gateway by adding units to maintain service reliability. Consider whether to scale based on requests and/or exceptions. Consider the relationship between the scaling of the gateway component relative to other components of the solution such as network address space and the scale capabilities of the backends.
>
> - **Isolate critical workloads**: Decide when to bulkhead your workload by using dedicated gateways (for example, a dedicated [workspace gateway](/azure/api-management/workspaces-overview) or a separate API Management instance) for critical APIs to ensure high availability and performance, or other critical considerations such as data sovereignty. Gateways sharing APIs can be used for less critical APIs to optimize resource usage. If multiple API Management instances are needed for mission-critical workloads, you will need to plan on how to keep the environments synchronized as part of your safe deployment practices.
>
> - **SLO alignment**: Factor in the gateway's SLA scope when setting your workload's SLOs. The service provides its own SLA for you to consider, but you'll need to factor in the anticipated reliability of other workload components such as the API backends.
>
> - **Address backend faults**: Plan for both expected and unexpected backend faults. Test client experiences in such scenarios. Evaluate gateway [policies](/azure/api-management/api-management-policies) for quotas, rate limits, retry policies, backend circuit breakers and load balancing, and exception handling to improve resiliency and client experience as documented in your API specifications.
>
> - **Define testing strategies**: Plan to use a testing solution such as Azure Load Testing to reflect actual production workloads. Don't rely on published throughput or other estimates which might not apply to your workload.  
>
> - **Plan for disaster recovery**: Review options for backing up and restoring the gateway infrastructure and APIs. While built-in [backup and restore capabilities](/azure/api-management/api-management-howto-disaster-recovery-backup-restore) might be useful in some cases, customer-managed options such as APIOps tooling and infrastructure-as-code solutions can also be considered. Develop strategies for maintaining other system data such as user subscriptions. Active/passive or active/active configurations can also be considered, although there are tradeoffs for costs and complexity.

<!-- For example, consider whether the API Management services could be sensitive to changes from Azure-side updates and plan canary environments to detect changes before mission-critical systems are affected. -->

### Recommendations

These reliability recommendations can apply to either the service itself or for the traffic flowing through APIs and their policies. That distinction is made by using *(Service)* or *(API)* designators respectively on the recommendations.

| Recommendation | Benefit |
| :------------- | :------ |
| *(Service)* Enable [zone redundancy](/azure/api-management/high-availability#availability-zones) and have a minimum of three units deployed.<br/><br/>In this configuration, under normal operating conditions all scale units in all configured zones are active and serve gateway traffic.<br/><br/>In any active-active scenario, such as this, plan to support scaling out in remaining active zones to handle traffic that was normally being processed by units in the faulted zone. | Provides resiliency during a data center outage within a region. During a complete data center loss, API traffic will continue to flow through the remaining units deployed in other zones. |
| *(Service)* Enable [automatic scale-out](/azure/api-management/api-management-howto-autoscale) based on traffic demands.<br/><br/> In multiregion deployments, you might need to implement a custom function or logic app to handle scaling in secondary regions which do not support automatic scale-out. | Ensures you have enough resources available to meet demand from your API clients. Without enough capacity to handle new requests, clients would experience failures when contacting your API. |
| *(Service)* Use a [multi-region topology](/azure/api-management/api-management-howto-deploy-multi-region) to support resiliency against a complete regional failure.<br/><br/>Implementing this involves coordination with other components in your workload and understanding their planned failover characteristics. In any active-active scenario, plan to support scaling out in remaining active regions to handle traffic that was normally being processed by the now-inactive region.<br/><br/>Ensure your multi-region topology aligns with any compliance requirements for data in transit or data in cache residency. | Provides resiliency during a complete regional outage. During a complete regional outage, API traffic can continue to flow through the units deployed in other regions. |
| *(Service)* Isolate unrelated APIs with [workspaces](/azure/api-management/workspaces-overview) or additional API Management instances. | Separating APIs across different compute instances ensures that the blast radius of a failure due misconfiguration or outages has only a localized impact. |
| *(API)* Thoroughly test policy expressions and logic. Combine with [resilient error handling techniques](/azure/api-management/api-management-error-handling-policies) in API management policies. | Improves client experience by not introducing new sources of failures at the gateway. Error handling can provide graceful degradation capabilities or can also be used to apply safe transient fault handling to hide an intermittent issue from the client. |
| *(Service & API)* [Collect reliability metrics](/azure/api-management/monitor-api-management-reference).<br/><br/>Common API reliability metrics include:<br/><br/>- Rate limits and quotas violations<br/>- Error rate based on HTTP status codes<br/>- Request rate deviations from baseline<br/>- Health checks, including dependency health. | With health signals like this available, you are able to detect deviations from expected behavior and past baselines. You use that data to trace back to root causes such as changed user behavior, interference from routine operations, an unexpected impact from a new feature, or an unplanned fault in the workload. |
| *(Service & API)* Use [built-in backup and restore](/azure/api-management/api-management-howto-disaster-recovery-backup-restore) capabilities built into API Management as part of your disaster recovery playbook. Supplement those features with your infrastructure as code artifacts and APIOps processes for a robust solution that can handle many scenarios, including recovery coordination with dependencies and backends. | Business continuity is maintained beyond what your resiliency approach addresses; you'll be able to recover the API gateway and the defined APIs themselves after a total system loss. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

- The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design in protecting the API Management gateway.
- The [security baseline for API Management](/security/benchmark/azure/baselines/api-management-security-baseline) applies guidance from the [Microsoft cloud security benchmark version 1.0](/security/benchmark/azure/overview).

> [!NOTE]
> The checklist and recommendations in this section are focused on securing the API Management gateway resource. Securing the APIs themselves is only lightly addressed.
>
> For more recommendations to mitigate API traffic security threats with Azure API Management, see [Mitigate OWASP API security top 10 in Azure API Management](/azure/api-management/mitigate-owasp-api-threats).

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Protect the deployment pipeline**:  Identify the individuals and RBAC roles that are required to manage the service platform, CI/CD pipelines, and the individual APIs. Ensure that only authorized individuals have access to manage the service and its APIs.
>
> - **Evaluate data sensitivity**: If sensitive data will pass in API requests to and responses from the API Management gateway, develop strategies to protect it consistently throughout its lifecycle, and pay attention to data protection requirements in different regions. Evaluate service features such as [multiple regions](/azure/api-management/api-management-howto-deploy-multi-region)  to isolate certain data, and whether caching strategy is aligned.  
>
> - **Develop segmentation strategies on shared gateways**: If your API Management instance is going to host APIs from multiple workload teams, segregate roles and networks (and possibly gateways) using [workspaces](/azure/api-management/workspaces-overview) so that different teams have appropriate access and control to APIs they manage and cannot access or control other APIs.
>
> - **Understand network controls**: Identify requirements and options for isolating or filtering inbound and/or outbound gateway traffic using [virtual networks](/azure/api-management/virtual-network-concepts). Can access to the gateway be restricted through Private Link, or is public access to the gateway required? Does architecture need to include a web application firewall such as Azure Web Application Firewall in Azure Application Gateway or Azure Front Door to accomplish the required network isolation and to filter network traffic?  
>
> - **Understand capabilities for API authentication and authorization**: Evaluate use of identity providers such as [Microsoft Entra ID](/azure/api-management/api-management-howto-protect-backend-with-aad) (with built-in groups) or [Microsoft Entra External ID](/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c) to screen gateway requests and for OAuth authorization to backend APIs.
>
> - **Encrypt network traffic** - Identify the most secure TLS [protocol versions and ciphers](/azure/api-management/api-management-howto-manage-protocols-ciphers) that your workloads will support; don't require insecure TLS versions.
>
> - **Perform threat modeling on API Management and reduce its surface area**: Can certain API Management components be disabled, limited, or removed such as, the direct management API or public access to developer portal?
>
> - **Identify secrets required by workloads**: Gateway operation may require certificates, API  keys, or other secret values. Review requirements and capabilities of Azure Key Vault to store secrets/certificates, or built-in API Management capabilities such as [named values](/azure/api-management/api-management-howto-properties) and [managed certificates](/azure/api-management/configure-custom-domain#domain-certificate-options).

### Recommendations

These security recommendations can apply to either the service itself or for the traffic flowing through APIs and their policies. That distinction is made by using *(Service)* or *(API)* designators respectively on the recommendations.

| Recommendation | Benefit |
| :------------- | :------ |
| *(Service)* Disable the [Direct management API Management REST API](/azure/api-management/breaking-changes/direct-management-api-retirement-march-2025), which is deprecated. Azure Resource Manager should be your control plane. | Reduces the surface area of the service by removing a control plane access point. |
| *(Service)* Limit exposure of the gateway based exclusively on where legitimate clients connect from.<br/><br/>- Use [virtual network injection](/azure/api-management/inject-vnet-v2) with no public IP if all clients are able to route through a virtual network. Use a [network security group](/azure/api-management/inject-vnet-v2#network-security-group) to further restrict traffic to just the expected client origin IPs.<br/>- Use [virtual network integration](/azure/api-management/integrate-vnet-outbound) combined with Azure Application Gateway or Azure Front Door if any traffic must come from the internet. Configure the [network security group](/azure/api-management/integrate-vnet-outbound#network-security-group) to only accept traffic from the single point of entry. | Protects the confidentiality of the network traffic by limiting exposure to just the source IP ranges that should contain legitimate clients. Restrictions block access from sources that should never originate legitimate client communication. Limiting exposure to sources of legitimate traffic helps protect confidentiality, integrity, and availability of the service. |
| *(Service)* Disable [developer portal](/azure/api-management/developer-portal-overview) if not being used. If the portal is being used, [disable the sign-up experience](/azure/api-management/developer-portal-faq#how-do-i-disable-sign-up-in-the-developer-portal), [disable anonymous access](/azure/api-management/secure-developer-portal-access), and restrict access to it from only trusted network locations. | Reduces the surface area of the service and the chance for misconfiguration or neglect. |
| *(Service)* Explicitly set the narrowest supported [TLS versions, protocols, and ciphers](/azure/api-management/api-management-howto-manage-protocols-ciphers#how-to-manage-tls-protocols-cipher-suites) for your clients and your backends. | Reducing the versions and supported ciphers to just those supported by your clients and your backends will ensure your connections are prioritizing the highest grade connections for confidentiality possible. |
| *(Service)* Store [custom certificates in Key Vault](/azure/api-management/configure-custom-domain?tabs=key-vault#domain-certificate-options). | Key Vault offers certificate management functionality that helps routine rotation. Key Vault also audits access to the certificate. |
| *(Service)* For API Management instances that host APIs for multiple teams or for segmented workloads, design access control isolation strategy, such as by using [workspaces](/azure/api-management/workspaces-overview) or a rigorous [APIOps process](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops).<br/><br/>Teams should only have access to influence the APIs they own, and shouldn't be aware of other APIs that might be hosted in the same instance. | Reduces the horizontal movement by attackers from one compromised API set into other unrelated APIs. |
| *(API)* [Store secrets](/azure/api-management/api-management-howto-properties) in Key Vault and expose them to policies through named values. Don't use Key Vault to store nonsecrets, use named value properties directly for those values. | Encourages secret rotation through a single management plane in Azure Key Vault, like you do with certificates, which will result in API Management being updated. Likewise, using named values provides a consistent experience for policy configuration for both secrets and nonsecrets. All secret access is also audited in Key Vault to provide an access history. Only storing secrets in Key Vault minimizes the dependency on Key Vault and does not turn Key Vault into an application configuration service. |
| *(API)* Use different user-assigned managed identities for different APIs using the [authentication-managed-identity](/azure/api-management/authentication-managed-identity-policy) policy. | This allows each API to have an independent identity which supports segmentation goals through least privilege access per API and provides better auditability in the backends. |
| *(API)*  Where possible, require clients to [authenticate with OAuth 2.0 flows](/azure/api-management/authentication-authorization-overview#oauth-20-authorization-scenarios-in-api-management) instead of exclusively using preshared keys (including [subscription keys](/azure/api-management/api-management-subscriptions)) or [client certificates](/azure/api-management/api-management-howto-mutual-certificates-for-clients). | Preshared keys don't support strong client identification for auditing purposes, they can be a challenge to rotate, and puts a burden to protect the secret by all of your clients. |
| *(API)* Suppress HTTP headers from API responses using the [set-header](/azure/api-management/set-header-policy) policy that might expose implementation details. | Increases cost to attackers by withholding implementation detail information from them. |
| *(API)* Don't use [API tracing](/azure/api-management/api-management-howto-api-inspector#enable-tracing-for-an-api) in production. | Prevent sensitive data from being exposed in request traces. |
| *(API)* Use [Defender for APIs](/azure/defender-for-cloud/defender-for-apis-introduction). | This capability of Microsoft Defender for Cloud provides API security insights, recommendations, and threat detection. |
| *(API)* Protect backend resources by delegating key security checks in API policy, for example: [validate-jwt](/azure/api-management/validate-jwt-policy), [ip-filter](/azure/api-management/ip-filter-policy), [validate-headers](/azure/api-management/validate-headers-policy), [validate-content](/azure/api-management/validate-content-policy). | Offloading security checks at the gateway allows the gateway to reduce the amount of nonlegitimate traffic that reaches your backend services, which helps protect the integrity and availability of those resources. |
| *(API)* Apply [security development lifecycle (SDL)](/compliance/assurance/assurance-microsoft-security-development-lifecycle) practices to API policy changes like you'd apply proposed changes to application code in your workload. | Policies execute with a highly privileged view of the API traffic, in that those policies are effectively creating an adversary-in-the-middle potential in your API. A backend that reliably protects confidentially, integrity, and availability can be thwarted by a compromised API gateway. |
| *(Service & API)* Use [managed identity](/azure/api-management/api-management-howto-use-managed-service-identity) for service and API dependencies. | Connections to Key Vault (certificates and named values), Event Hubs, and other dependencies can be established without maintaining pre-shared secrets. |
| *(Service & API)* Connect to dependencies such as Key Vault, Event Hubs, and backends over private network connections where possible. | Protects the confidentiality of traffic by not exposing the traffic beyond your private network. |
| *(Service & API)* Client traffic for internet exposed APIs should first traverse a web application firewall before reaching API Management, such as using [Azure Web Application Firewall](/azure/web-application-firewall/overview). Likewise, protect public endpoints with [Azure DDoS Protection](/azure/ddos-protection/ddos-protection-overview). | Performing security checks using a web application firewall will reduce the amount of nonlegitimate traffic that reaches your gateway and therefore your backend services. Reducing this traffic helps protect the integrity and availability of those resources. |
| *(Service & API)* Evaluate and enable all of the [Azure Policy Regulatory Compliance controls](/azure/api-management/security-controls-policy) that are relevant to your workload. | Having security policies in place ensures that your API Management instance aligns with your desired posture and stays aligned as the workload evolves. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure API Management and its environment.

### Design checklist

> [!div class="checklist"]
>
> - **Understand API Management’s cost model**: Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/), along with the organization’s account benefits and the organization’s criteria for SLA and scalability, to develop accurate cost estimates of using an API Management service tier. Decide whether a charge-back model is needed and how it will be calculated (using metrics, tags, tokens, and so on).  
>
>   The service cost model is mainly influenced by the service tier, number of units, and number of gateways. Evaluate extra costs of maintaining reserve unit or an active-passive disaster recovery configuration.
>
>   If implementing [workspaces](/azure/api-management/workspaces-overview), evaluate the costs of implementing separate versus shared workspace gateways to manage distinct API flow needs of different API teams or stakeholders.
>
> - **Estimate scaling costs**: Develop scaling criteria to maintain high utilization of the gateway resources. Evaluate baseline costs in a pre-production environment and perform testing to model costs of scaling out based on anticipated workload traffic.
>
>   Design a mitigation strategy to prevent abuse of your gateways which might cause expensive scaling beyond predicated usage.
>
> - **Evaluate service configurations and policies** such as [rate-limit](/azure/api-management/rate-limit-policy) and [limit-concurrency](/azure/api-management/limit-concurrency-policy) to manage request loads. Understand opportunity costs of insufficient capacity.
>
> - **Optimize logic placement** by evaluating if backend servers can more cost effectively handle the processing logic or if the gateway should incur the resource usage. While a gateway is an ideal place to handle cross cutting concerns, an optimization investigation on resource-heavy request processing tasks could result in a decision to avoid the don't repeat yourself (DRY) principal to help save operating costs by moving that logic to the backends.

### Recommendations

These cost optimization recommendations can apply to either the service itself or for the traffic flowing through APIs and their policies. That distinction is made by using *(Service)* or *(API)* designators respectively on the recommendations.

| Recommendation | Benefit |
| :------------- | :------ |
| *(Service)* Use the [least expensive tier](https://azure.microsoft.com/pricing/details/api-management/#pricing) that supports your workload's requirements. For example, use a Standard tier instead of a Premium tier if your workload wouldn't see a return on investment from the added functionality. | Avoids purchasing unused or underutilized capabilities. |
| *(Service)* Use [nonproduction tiers](/azure/api-management/v2-service-tiers-overview) or transient infrastructure in lower environments, such as development environments, proof-of-concept deployments, and initial cost modeling activities. | This choice saves on resource costs for environments that can still be useful when not fully mirroring production's exact configuration or uptime requirements. |
| *(Service)* Scale-in when demand decreases. Configure [autoscale rules](/azure/api-management/api-management-howto-autoscale) or other automated processes to reduce units when gateway capacity drops below defined thresholds. | Reduce unnecessary costs by matching capacity to demand. |
| *(Service)* Calculate any cost advantages of a federated model for API management using [workspaces](/azure/api-management/workspaces-overview) to serve APIs while also providing team isolation. | Reduces the deployment and management surface, with the intent to get economies of scale out of your time and resource purchases. |
| *(Service)* [Decommission workspaces](/rest/api/apimanagement/workspace-api/delete) as soon as they're no longer in use. | Avoids spend on unused resources. |
| *(Service)* Use the [built-in cache](/azure/api-management/api-management-howto-cache) if your workload's cached data fits within the constraints of the built-in cache in your tier. | Avoids the costs involved in purchasing and maintaining an external Redis-compatible cache. |
| *(Service)* Block abusive traffic before it reaches the gateway by using network controls, [DDoS protection](/azure/ddos-protection/ddos-protection-overview), and [web application firewalls](/azure/web-application-firewall/overview). Some tiers of API Management charge by HTTP request operations served by the gateway, so undesirable traffic (such as from bots) can cause additional requests that you're obligated to pay for.<br/><br/>You need to evaluate the cost of the blocking mechanism vs the estimated cost of HTTP operation reduction to evaluate if this will have a return on investment (ROI). | Reduces charges that are due to excessive malicious or nuisance HTTP operations against your gateway. |
| *(API)* Optimize the [policy expressions and processing](/azure/api-management/api-management-policy-expressions) and code to avoid excessive compute resource usage, such as processor, networking, or memory. | This avoids unnecessary deployment of addition units to provide capacity for unoptimized policy implementations and code. |
| *(API)* Evaluate cost of logic placement between your gateway, your backend, or your public entry point (such as Azure Front Door). The same processing can often happen at any of those layers, with tradeoffs for each, but some may offer cost savings because of unused capacity at that layer. For example, caching logic that was implemented in the backend might be able to be implemented at the gateway level cheaper by using the built-in cache and avoiding additional network and compute utilization on the backend services. | Capabilities running from the cost-effective location can reduce pressure on expensive resources, delegating them to resources that have spare capacity or are cheaper compute units. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to API Management.

### Design checklist

> [!div class="checklist"]
>
> - **Review key knowledge and skills needed to operate the service**: Areas include API lifecycle, DevOps processes, networking and connectivity, monitoring and observability, and software development (for policy configuration, unit testing, and CI/CD pipelines).
>
> - **Understand documentation needs**: Organization should commit to to documenting processes for service-level and API-level configuration, lifecycle operations, and the different access patterns for API teams.
> - **Evaluate standard tooling** to support operation of service: For example, implement [APIOps](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops) (GitOps and CI/CD to publish APIs) methods for API management and infrastructure-as-code tooling for service-level configuration changes. Design artifacts for reuse from development environments up to production. Consider use of linter such as [Spectral](https://stoplight.io/open-source/spectral) in API approval pipelines.
>
> - **Decide on key operational metrics and logs**: Review the [metrics](/azure/api-management/monitor-api-management-reference#metrics) (such as Capacity, CPU % and memory % of gateway, number of requests), logs, and [observability tools](/azure/api-management/observability) (Azure Monitor, Application Insights, or other tools) that will be used in production. What strategies or tools are needed to process a potentially large amount of observational data in production? What queries will provide the necessary insights to both the gateway operator and those interested in specific hosted APIs?  
>
> - **Plan regular testing of production workloads** with Azure Load Testing.
>
> - **Identify operational tasks beyond CI/CD or infrastructure as code processes** that require automation: Plan automation in areas such as API Management policy source management, Azure policies, certain developer portal configurations.

### Recommendations

These operational excellence recommendations can apply to either the service itself or for the traffic flowing through APIs and their policies. That distinction is made by using *(Service)* or *(API)* designators respectively on the recommendations.

| Recommendation | Benefit |
| :------------- | :------ |
| *(Service)* Configure [Azure diagnostics resource logs for API Management](/azure/api-management/monitor-api-management-reference#resource-logs). | You capture platform-generated logs for routine, ad-hoc, or emergency usage, including security auditing. |
| *(Service)* Use Event Grid for automation based on meaningful [events raised by your API Management instance](/azure/event-grid/event-schema-api-management), such as `APICreated`. | Enables you to build automation or notification tasks around key lifecycle events that happen in your API Management instance. |
| *(Service)* Avoid using a [self-hosted gateway](/azure/api-management/self-hosted-gateway-overview) if a Microsoft-hosted gateway unit works in your scenario. | Enables you to build automation or notification tasks around key lifecycle events that happen in your API Management instance. |
| *(Service)* Apply all of the [built-in Azure Policy policies](/azure/api-management/policy-reference) that help you govern your instance and constrain it to align with workload requirements, including security requirements. For example, use *deny* policies to prevent APIs from being exposed over HTTP or to prevent the enabling of the direct management REST API. Use *audit* policies if deny policies aren't available, or create custom deny policies if important to your workload. | Having policies in place ensures that your API Management instance aligns with your design and stays aligned as the workload evolves. |
| *(Service)* Familiarize yourself with the [Diagnose and solve problems](/azure/api-management/diagnose-solve-problems) capability in the Azure portal.<br/><br/>Use the [Network status blade](/azure/api-management/api-management-using-with-vnet#verify-network-status) in the portal to troubleshoot network connectivity.| These tools can help your site reliability engineering individuals identify and troubleshoot gateway performance, service availability, and network connectivity issues in all environments. |
| *(API)* Use [Event Hubs](/azure/api-management/api-management-howto-log-event-hubs) for log or event streams from API invocations that you might need to react on in near real-time or perform windowed operations against in a short timeframe. | Logging to Azure Monitor within your APIs is subject to [log data ingestion delay](/azure/azure-monitor/logs/data-ingestion-time), but logging to event hubs makes log entries available in near real-time. |
| *(API)* Support the usage of [API tracing](/azure/api-management/api-management-howto-api-inspector#enable-tracing-for-an-api) in development to help developers understand their policy execution. | This capability optimizes developer productivity by providing introspection on the execution of policies within an API. Without this capability, developers need to introduce hacks in policy execution to gain their needed insight. |
| *(API)* Design your APIs' versioning approach to align with API Management's [versioning and revision capabilities](/azure/api-management/api-management-versions) and factor this approach into your API deployment operations. | Using an API versioning strategy that is supported out of the box by API Management allows you to use those built-in capabilities instead of building custom solutions. |
| *(Service & API)* Define a consistent and sustainable operational process for adding, modifying, and deleting APIs. Decide if this experience will be managed manually through the portal or implemented through an [APIOps process](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops). Prefer using an infrastructure-as-code (IaC) based approach over a portal based approach.<br/><br/>API Management's representation in the Resource Manager API consists of many [child resources](/azure/templates/microsoft.apimanagement/service/apis), so it's important to build a [layered approach](/azure/well-architected/operational-excellence/infrastructure-as-code-design#use-a-layered-approach-for-iac-pipelines) to IaC-based management of this collection of resource. | API specifications and their gateway implementations have change control processes that integrate with the rest of the workload's change control processes. This avoids handling changes to the backend APIs in a way that is substantially different than how they're exposed to the API clients through the gateway. |

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
> - **Review how to identify live performance issues**: Indicators include Azure service availability, HTTP response errors, and errors raised in **Diagnose and solve problems** blade in the portal. Troubleshoot performance and availability issues using Application Insights, Kusto queries, and recommendations from API Management Diagnostics in the Azure portal.
>
> - **Test performance**: Test performance under production conditions using Azure Load Testing.
>
> - **Evaluate adjacent services that may improve performance**: Caching policies and/or an external cache may improve performance of certain API operations. Azure Front Door or Application Gateway may be used for TLS offloading
>
> - **Review the documented limits and constraints**: [Azure API Management has limits and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-api-management-limits) like all services. Review the documented constraints and compare them against your workload's requirements to see if you'll need to design a solution that avoids the constraints.

### Recommendations

| Recommendation | Benefit |
| :------------- | :------ |
| *(Service)* Dynamically scale to match demand. Configure [autoscale rules](/azure/api-management/api-management-howto-autoscale) or other automated processes to adjust gateway units to match a target usage capacity. | Allows the system to be elastic based on concurrent usage without starving the current deployed units or over-allocating capacity. |
| *(API)* Minimize expensive processing tasks, such as generating large buffered payload sizes, using the [validate-content policy](/azure/api-management/validate-content-policy) on large request bodies, or maintaining a high number of active WebSockets. | Not stressing the scale units allows them to process more requests concurrently before needing to scale out. |
| *(Service & API)* [Collect performance metrics](/azure/api-management/monitor-api-management-reference).<br/><br/>Common API performance metrics include:<br/><br/>- Request processing time for the gateway itself and for the full operation<br/>- Gateway unit resource utilization metrics<br/>- Throughput measurements like requests per second (RPS) or megabits per second (Mbps)<br/>- Cache hit ratio | With this data available, you are able to measure the actual performance against your workload's targets. |
| *(Service & API)* Define a [sampling percentage for Application Insights](/azure/api-management/api-management-howto-app-insights#performance-implications-and-log-sampling) that provides enough visibility without impacting performance. | Satisfy observability data needs without affecting overall performance. |
| *(Service & API)* Evaluate performance impact of logic placement between your gateway, your backend, or your public entry point (such as Azure Front Door). The same processing tasks can often happen at any of those layers, with performance tradeoffs and limitations in optimization opportunities for each. If too much latency is being introduced by a task, such as an API management API policy, evaluate if that task can run elsewhere in a more optimized way. | Tasks that add latency to your APIs are now running on compute where that latency can be optimized to meet your workload requirements. |

## Azure policies

Azure provides many built-in policies related to API Management and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- The gateway is configured for zone redundancy.
- Proper network controls are in place for the API Management gateway, such as deployment in a virtual network.
- The service configuration endpoints aren't publicly accessible.
- The direct Management REST API is disabled.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/api-management/policy-reference) and other policies that might affect the security of the API Management gateway.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Following the design guides in this article helps reduce notices surfaced in Advisor. Advisor might surface other recommendations in your production system as well, such as:

- Failure to require long JWT key sizes in the validate-jwt policy.
- You used a legacy Resource Manager API version to deploy the resource.
- API key tokens are reaching their expiry date.
- Failure in a certificate rotation operation.

For more information, see [Azure Advisor](/azure/advisor/advisor-reference-operational-excellence-recommendations#api-management).

## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: High availability through redundancy and isolation

Recommendations such *use at least three units* to avoid zonal outages comes as a financial cost that might not be tenable for your workload. Furthermore, recommendations such as *use a multi-region architecture* then requires at least six units (three per region) which adds more cost. The multi-region topology also comes with operational process impact such as coordinating safe deployment practices across them, bespoke scaling solutions, failover coordination with backends, and more.

Isolating workloads across workspaces or across API Management instances increases operational complexities as you're effectively managing a multitenant system with compute isolation.

:::image type="icon" source="../_images/trade-off.svg"::: Scale to match demand

If you're automatically scaling out to match demand from all of your well-behaving clients, you've already accounted for that in your cost models. However, that same capability also allows the service to scale to handle excessive nuisance (bot) and malicious traffic.

Mitigating undesired traffic always has a cost somewhere. If you externalize the mitigation through a web application firewall and DDoS protection, those come at a cost. If you mitigate by allowing your service to scale to absorb the traffic, in hopes your legitimate clients aren't starved, then you're paying for those added units. Setting upper limits on scale can cap spending, but excessive abusive use of your API will eventually cause reliability problems for legitimate clients.

:::image type="icon" source="../_images/trade-off.svg"::: Federated or distributed

A fundamental decision you make with API Management is to either colocate disparate workloads in a single API Management instance, or to instead isolate workloads across multiple API Management instances in a fully autonomous topology.

API Management has capabilities with workspaces to effectively operate as a multitenant, colocation platform for multiple API teams. The pricing models for the tiers reflect this, and service costs can be amortized across all of the gateways' tenants. As with any colocation platform, outages and misconfiguration can cause widespread impact across unrelated workloads sharing that infrastructure.

A fully distributed model where every workload team manages their own instances has duplicative cost implications and routine operations redundancy, but comes with built-in blast radius mitigation for reliability, security, and performance incidents.

## Next steps

The following articles demonstrate some of the recommendations discussed in this article.

- [Use API Management as a gateway for Azure OpenAI](/azure/architecture/ai-ml/guide/azure-openai-gateway-guide) in workloads.
- [APIOps documentation](https://azure.github.io/apiops/) and [Automated API deployments using APIOps](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops)
- [Azure API Management architecture in an application landing zone](/azure/architecture/example-scenario/integration/app-gateway-internal-api-management-function) and [Azure landing zone platform considerations](/azure/cloud-adoption-framework/scenarios/app-platform/api-management/landing-zone-accelerator) for API Management deployments.

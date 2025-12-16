---
title: Architecture Best Practices for Azure API Management
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure API Management.
author: dlepow
ms.author: danlep
ms.topic: concept-article
ms.date: 12/11/2025
ms.subservice: waf-service-guide
products:
 - azure-api-management
azure.category:
 - integration
---

# Architecture best practices for Azure API Management

Azure API Management is a management platform and gateway for APIs across all environments, including hybrid and multicloud. As a platform as a service (PaaS) solution, API Management helps support your workload's API lifecycle.

This article assumes that as an architect, you reviewed the [integration services decision tree](/azure/logic-apps/azure-integration-services-choose-capabilities) and chose API Management as the integration service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resource:

- Azure API Management

**The scope of this guide is the API Management service, primarily the gateway component (data plane)**, which proxies API requests from client applications to back-end APIs hosted on various platforms or cross-premises locations. The workload's architecture should account for the API Management control plane and related components such as client applications that access the gateway and the back-end APIs that process routed requests. It also integrates supporting Azure services, including networking, monitoring, identity management, and other capabilities.

This guide doesn't cover [Azure API Center](/azure/api-center/overview). It only addresses API-level topics as they relate to API Management instead of providing a well-architected perspective on API design considerations.

> [!NOTE]
>
> Not all recommendations apply to all [service tiers](/azure/api-management/api-management-features) of API Management. Many recommendations in this guide focus on the Standard v2, classic Premium, and Premium v2 tiers of API Management, which are the recommended production tiers for most enterprise workloads.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and features of API Management and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Evaluate gateway capabilities for reliability and redundancy:** Determine the API Management [tier and features](/azure/api-management/api-management-features) that are needed to meet the workload's reliability requirements for each environment.
>
>   Evaluate gateway redundancy features, including availability zones, multiple gateway units, multiple regions, and workspaces. All these features are supported in the Premium tier. The Premium v2 tier currently supports all these features except multiple regions. The Developer tier, which doesn't include a service-level agreement (SLA), isn't suitable for production workloads. Consider the tradeoffs of adopting features such as external caching that can introduce potential points of failure and performance bottlenecks.
>
> - **Review observability capabilities:** Consider the service's [observability capabilities](/azure/api-management/observability), including Azure Monitor logs and metrics, Application Insights, built-in analytics, and built-in diagnostics. Use these capabilities to monitor the reliability signals of your workload.
>
>   For example, consider using [Azure Monitor alerts](/azure/azure-monitor/alerts/alerts-overview) to notify you of potential problems with the API Management gateway or its dependencies.
>
> - **Review scaling strategies:** Define criteria for [scaling out](/azure/api-management/upgrade-and-scale) the gateway by adding units to maintain service reliability. Consider whether to scale based on requests, exceptions, or both. Evaluate the impact of scaling the gateway component on other components of the workload. For example, its effect on network address space and the scaling capabilities of the back ends.
>
> - **Isolate critical workloads:** Determine if compute isolation is needed to fulfill workload requirements, like data sovereignty or performance optimization, in your APIs. Use dedicated gateways for critical APIs and shared gateways for less critical APIs.
>
>   Choose an isolation approach, like using a [dedicated workspace gateway](/azure/api-management/workspaces-overview) or a separate API Management instance. For multiple instances, keep the environments synchronized as part of your safe deployment practices.
>
> - **Service-level objective (SLO) alignment:** Factor in the gateway's SLA scope when you set your workload's SLOs. The service provides its own SLA, but you also need to account for the anticipated reliability of other workload components, such as the API back ends.
>
> - **Address back-end faults:** Plan for both expected and unexpected back-end faults. Test client experiences in these scenarios. Evaluate gateway [policies](/azure/api-management/api-management-policies) to improve resiliency and enhance the client experience. Focus on quotas, rate limits, retry policies, back-end circuit breakers, load balancing, and exception handling as documented in your API specifications.
>
> - **Define testing strategies:** Use a testing solution such as [Azure Load Testing](/azure/load-testing/concept-azure-load-testing-vnet-injection) from within your network to reflect actual production workloads. Don't rely on published throughput or other estimates which might not apply to your workload.
>
> - **Plan for disaster recovery (DR):** Review options for backing up and restoring the gateway infrastructure and APIs. Built-in [backup and restore capabilities](/azure/api-management/api-management-howto-disaster-recovery-backup-restore) might be useful in some scenarios. But customer-managed options such as [APIOps](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops) tooling and infrastructure as code (IaC) solutions can also be considered. Develop strategies for maintaining other system data, including user subscriptions.

### Configuration recommendations

These reliability recommendations apply either to the service itself or to the traffic that flows through APIs and their policies. The *(Service)* or *(API)* designators indicate whether a recommendation targets the service or the APIs.

| Recommendation | Benefit |
| :------------- | :------ |
| (Service) Enable [zone redundancy](/azure/api-management/high-availability#availability-zones) (available in the Premium and Premium v2 tiers) and deploy at least two units. <br/><br/> Under normal operating conditions, all scale units in all configured zones are active and serve gateway traffic. <br/><br/> In any active-active scenario, plan to support scaling out in the remaining active zones to handle traffic that units originally processed in the faulted zone.  | Resiliency is ensured during a datacenter outage within a region. During a complete datacenter loss, API traffic continues to flow through the remaining units deployed in other zones. |
| (Service) Enable [automatic scale-out](/azure/api-management/api-management-howto-autoscale) based on traffic demands. <br/><br/> In multiregion deployments, secondary regions don't have automatic scale-out or scale-in capabilities. You need to implement a custom function or Logic App that activates in response to Azure Monitor metric alerts to manage unit adjustments based on demand. | Sufficient resources are guaranteed to meet demand from API clients, which prevents failures because of insufficient capacity. |
| (Service) Use a [multiregion topology](/azure/api-management/api-management-howto-deploy-multi-region) to support resiliency against a complete regional failure. <br/><br/> This approach requires you to coordinate with other components in your workload and to understand their planned failover characteristics. In any active-active scenario, plan to support scaling out in remaining active regions to handle traffic that the now-inactive region originally processed. <br/><br/> Ensure that your multiregion topology aligns with any compliance requirements for data in transit or data in cache residency. | Robust resiliency is provided during a complete regional outage, which helps ensure uninterrupted API traffic through units deployed in other regions. |
| (Service) Isolate unrelated APIs with [workspaces](/azure/api-management/workspaces-overview) or additional API Management instances. | The impact of failures caused by misconfiguration or outages is minimized by separating APIs across different compute instances. |
| (API) Thoroughly test policy expressions and logic and pair them with [resilient error handling techniques](/azure/api-management/api-management-error-handling-policies) in API management policies. | Client experience is enhanced by preventing new sources of failures at the gateway and providing graceful degradation or safe transient fault handling. |
| (Service & API) [Collect reliability metrics](/azure/api-management/monitor-api-management-reference). <br/><br/> Typical API reliability metrics include: <br/><br/> - Rate limits and quotas violations. <br/> - Error rate based on HTTP status codes. <br/> - Request rate deviations from baseline. <br/> - Health checks, including dependency health. | Deviations from expected behavior and past baselines are identified. This data can be used to trace root causes such as changed user behavior, interference from routine operations, unexpected impacts from new features, or unplanned faults in the workload. |
| (Service & API) Use [built-in backup and restore](/azure/api-management/api-management-howto-disaster-recovery-backup-restore) capabilities built into API Management as part of your DR playbook. Supplement those features with your IaC artifacts and APIOps processes for a robust solution that can handle various scenarios, including recovery coordination with dependencies and back ends. | Business continuity is ensured by allowing recovery of the API gateway and restoration of defined APIs after a complete system loss. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of API Management.

> [!NOTE]
> The checklist and recommendations in this section focus on securing the API Management gateway resource. Securing the APIs themselves is only briefly addressed. For more information, see [Mitigate OWASP API security top 10 in API Management](/azure/api-management/mitigate-owasp-api-threats).

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish a security baseline:** Review the [security baseline for API Management](/security/benchmark/azure/baselines/api-management-security-baseline) and incorporate applicable measures in your baseline.
>
> - **Protect the deployment pipeline:** Identify the individuals and role-based access control roles that are required to manage the service platform, continuous integration and continuous deployment (CI/CD) pipelines, and the individual APIs. Ensure that only authorized individuals have access to manage the service and its APIs.
>
> - **Evaluate data sensitivity:** If sensitive data flows through API requests and responses in the API Management gateway, ensure its protection throughout its entire lifecycle. Account for varying data protection requirements across different regions. Evaluate service features such as [multiple regions](/azure/api-management/api-management-howto-deploy-multi-region) to isolate specific data. Also, confirm whether your caching strategy aligns with these isolation requirements.
>
> - **Develop segmentation strategies on shared gateways:** If your API Management instance hosts APIs for multiple workload teams, use [workspaces](/azure/api-management/workspaces-overview) to segregate roles, networks, and possibly gateways. This approach ensures that each team has appropriate access and control over the APIs that they manage while restricting access to the APIs of other teams.
>
> - **Consider network controls:** Identify requirements and options for isolating or filtering inbound and outbound gateway traffic by using [virtual networks](/azure/api-management/virtual-network-concepts). Determine whether access to the gateway can be restricted through Azure Private Link or if public access to the gateway is necessary. Assess whether the architecture should include a web application firewall, such as Azure Web Application Firewall in Azure Application Gateway or Azure Front Door, to achieve the required network isolation and filter network traffic.
>
> - **Consider capabilities for API authentication and authorization:** Evaluate the use of identity providers like [Microsoft Entra ID](/azure/api-management/api-management-howto-protect-backend-with-aad), which includes built-in groups, or [Microsoft Entra External ID](/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c) to screen gateway requests and enable OAuth authorization for back-end APIs.
>
> - **Encrypt network traffic:** Identify the most secure Transport Layer Security (TLS) [protocol versions and ciphers](/azure/api-management/api-management-howto-manage-protocols-ciphers) that your workloads can support. Don't require insecure TLS versions. Use TLS 1.3 when supported by your clients.
>
> - **Perform threat modeling on API Management and reduce its surface area:** Determine whether specific API Management components, such as the direct management API or public access to the developer portal, can be disabled, restricted, or removed.
>
> - **Identify secrets that workloads require:** Gateway operation might require certificates, API keys, or other secret values. Review the requirements and capabilities of Azure Key Vault to store secrets and certificates. Or review the built-in API Management capabilities such as [named values](/azure/api-management/api-management-howto-properties) and [managed certificates](/azure/api-management/configure-custom-domain#domain-certificate-options).

### Configuration recommendations

These security recommendations can apply either to the service itself or to the traffic that flows through APIs and their policies. The *(Service)* or *(API)* designators indicate whether a recommendation targets the service or the APIs.

| Recommendation | Benefit |
| :------------- | :------ |
| (Service) Disable the deprecated [direct management API](/azure/api-management/breaking-changes/direct-management-api-retirement-march-2025). Use Azure Resource Manager as your control plane. | The surface area of the service is reduced by removing a control plane access point. |
| (Service) Limit exposure of the gateway based exclusively on where legitimate clients connect from. <br/><br/> - Use [virtual network injection](/azure/api-management/inject-vnet-v2) without a public IP address when all clients can route traffic through a virtual network. Use a [network security group (NSG)](/azure/api-management/inject-vnet-v2#network-security-group) to further restrict traffic to only the expected client origin IP addresses. <br/><br/> - Use [virtual network integration](/azure/api-management/integrate-vnet-outbound) with Application Gateway or Azure Front Door if any traffic must come from the internet. Configure the [NSG](/azure/api-management/integrate-vnet-outbound#network-security-group) to only accept traffic from the single point of entry. | Confidentiality of network traffic is protected by restricting exposure to the source IP address ranges expected to contain legitimate clients. These restrictions block access from sources that shouldn't initiate legitimate client communication. Limiting exposure to legitimate traffic sources enhances the confidentiality, integrity, and availability of the service. |
| (Service) Disable [developer portal](/azure/api-management/developer-portal-overview) if it's not in use. If the portal is in use, [disable the sign-up experience](/azure/api-management/developer-portal-faq#how-do-i-disable-sign-up-in-the-developer-portal), [disable anonymous access](/azure/api-management/secure-developer-portal-access), and restrict access to only trusted network locations. | The surface area of the service and the chance for misconfiguration or neglect are reduced. |
| (Service) Explicitly set the narrowest supported [TLS versions, protocols, and ciphers](/azure/api-management/api-management-howto-manage-protocols-ciphers#how-to-manage-tls-protocols-cipher-suites) for your clients and your back ends. | Versions and supported ciphers are reduced to only those options that clients and back ends support. This approach helps ensure that connections prioritize the highest grade connections possible for confidentiality. |
| (Service) Store [custom certificates in Key Vault](/azure/api-management/configure-custom-domain?tabs=key-vault#domain-certificate-options). | Certificate management functionality is provided by using Key Vault, which supports routine rotation and audits access to certificates. |
| (Service) Back ends should only accept traffic from the API gateways and should block all other traffic. | Malicious traffic is prevented from bypassing the security and other crosscutting concerns offloaded to the gateway. |
| (Service) For API Management instances that host APIs for multiple teams or segmented workloads, design a robust access control isolation strategy. Use [workspaces](/azure/api-management/workspaces-overview) or implement a rigorous [APIOps process](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops) to achieve this strategy. <br/><br/> Teams must only have access to the APIs that they own. They shouldn't have access to other APIs that might be hosted in the same instance. | Lateral movement by attackers from one compromised API set into other unrelated APIs is reduced. |
| (API) [Store secrets](/azure/api-management/api-management-howto-properties) in Key Vault and expose them to policies through named values. Don't use Key Vault to store nonsecrets. Use named value properties directly for those values. | Secret rotation is encouraged through a single management plane in Key Vault, similar to the approach used for certificates. This process ensures that API Management is updated accordingly. Named values also ensure a consistent experience for policy configuration for both secrets and nonsecrets. All secret access is also audited in Key Vault to provide an access history. Only storing secrets in Key Vault minimizes the dependency on Key Vault and doesn't turn Key Vault into an application configuration service. |
| (API) Use different user-assigned managed identities for different APIs by using the [authentication-managed-identity](/azure/api-management/authentication-managed-identity-policy) policy. | Each API is enabled to have an independent identity, which supports segmentation goals through least privilege access for each API. It also provides better auditability in the back-end systems. |
| (API) When possible, require clients to [authenticate with OAuth 2.0 flows](/azure/api-management/authentication-authorization-overview#oauth-20-authorization-scenarios-in-api-management) instead of only using preshared keys, including [subscription keys](/azure/api-management/api-management-subscriptions) or [client certificates](/azure/api-management/api-management-howto-mutual-certificates-for-clients). | Client identification for auditing purposes is improved, key rotation is eliminated, and the burden on clients to protect secrets is reduced compared to using preshared keys.|
| (API) Suppress HTTP headers from API responses by using the [set-header](/azure/api-management/set-header-policy) policy that might expose implementation details. | The cost to attackers is increased by withholding implementation information. |
| (API) Don't use [API tracing](/azure/api-management/api-management-howto-api-inspector#enable-tracing-for-an-api) in production. | Sensitive data is prevented from being exposed in request traces. |
| (API) Use [Defender for APIs](/azure/defender-for-cloud/defender-for-apis-introduction). | API security insights, recommendations, and threat detection are provided. |
| (API) Protect back-end resources by delegating key security checks in API policy, such as [validate-jwt](/azure/api-management/validate-jwt-policy), [ip-filter](/azure/api-management/ip-filter-policy), [validate-headers](/azure/api-management/validate-headers-policy), [validate-content](/azure/api-management/validate-content-policy). | The amount of nonlegitimate traffic that reaches back-end services is reduced by performing security checks at the gateway. This offloading approach helps protect the integrity and availability of those resources. |
| (API) Apply [security development lifecycle (SDL)](/compliance/assurance/assurance-microsoft-security-development-lifecycle) practices to API policy changes the same way that you apply proposed changes to application code in your workload. Policies execute with a highly privileged view of the API traffic. | Circumventing back-end protections for confidentiality, integrity, and availability by a compromised API gateway is prevented. |
| (Service & API) Use [managed identities](/azure/api-management/api-management-howto-use-managed-service-identity) for service and API dependencies. | Connections to Key Vault, Azure Event Hubs, and other dependencies, such as certificates and named values, are established without relying on preshared secrets. |
| (Service & API) Connect to dependencies such as Key Vault, Event Hubs, and back ends over private network connections when possible. | Confidentiality of traffic is protected by not exposing the traffic beyond the private network. |
| (Service & API) Client traffic that targets internet-exposed APIs should first pass through a web application firewall, such as [Web Application Firewall](/azure/web-application-firewall/overview), before it reaches API Management. Also, protect public endpoints by using [Azure DDoS Protection](/azure/ddos-protection/ddos-protection-overview). | The amount of nonlegitimate traffic that reaches the gateway and back-end services is reduced by conducting security checks with a web application firewall. Reducing this traffic helps protect the integrity and availability of those resources. |
| (Service & API) Evaluate and enable all of the [Azure Policy Regulatory Compliance controls](/azure/api-management/security-controls-policy) that are relevant to your workload. | The API Management instance aligns with the desired posture and remains aligned as the workload evolves by having security policies in place. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to API Management and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload aligns with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.


> [!div class="checklist"]
>
> - **Consider the API Management cost model:** Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) with your organization's account benefits and criteria for SLA and scalability to develop accurate cost estimates of using an API Management service tier. Determine whether a charge-back model is necessary and determine how to calculate it based on metrics, tags, and tokens.
>
>   The service cost model is mainly influenced by the service tier, number of units, and number of gateways. Evaluate the extra costs associated with maintaining a reserve unit or implementing an active-passive DR configuration.
>
>   If you implement [workspaces](/azure/api-management/workspaces-overview), evaluate the costs of using separate versus shared workspace gateways to address the distinct API flow requirements of various API teams or stakeholders.
>
> - **Estimate scaling costs:** Develop scaling criteria to maintain high usage of the gateway resources. Evaluate baseline costs in a pre-production environment and perform testing to model costs of scaling out based on anticipated workload traffic.
>
>   Design a mitigation strategy to prevent misuse of your gateways, which might cause expensive scaling beyond predicted usage.
>
> - **Evaluate service configurations and policies:** Capabilities such as [rate-limit](/azure/api-management/rate-limit-policy) and [limit-concurrency](/azure/api-management/limit-concurrency-policy) can be used as cost optimization techniques to manage request loads.
>
> - **Optimize logic placement:** Assess whether back-end servers are more cost-effective for processing logic or if the gateway should handle the resource usage. The gateway is a strong component for implementing cross-cutting concerns, but it might perform excessive functions in certain scenarios. Identify resource-heavy request processing tasks that the gateway performs, and determine whether moving that logic to back-end servers can reduce costs.

### Configuration recommendations

These cost optimization recommendations apply either to the service itself or to the traffic that flows through APIs and their policies. The *(Service)* or *(API)* designators indicate whether a recommendation targets the service or the APIs.

| Recommendation | Benefit |
| :------------- | :------ |
| (Service) Use the [least expensive tier](https://azure.microsoft.com/pricing/details/api-management/#pricing) that supports your workload's requirements. For example, choose a Standard tier instead of a Premium tier if your workload won't benefit from the added functionality in a way that justifies the return on investment (ROI). | Purchasing unused or underused capabilities is avoided. |
| (Service) Use [nonproduction tiers](/azure/api-management/v2-service-tiers-overview) or transient infrastructure in lower environments, such as development environments, proof-of-concept deployments, and initial cost modeling activities. | Resource costs are saved for environments that can remain useful without fully mirroring the production's exact configuration or uptime requirements. |
| (Service) Scale in when demand decreases. Configure [autoscale rules](/azure/api-management/api-management-howto-autoscale) or other automated processes to reduce units when gateway capacity drops below defined thresholds. | Unnecessary costs are reduced by matching capacity to demand. |
| (Service) Calculate any cost advantages of a federated model for API management by using [workspaces](/azure/api-management/workspaces-overview) to serve APIs while also providing team isolation. | The deployment and management surface is reduced. This approach aims to achieve economies of scale from time and resource purchases. |
| (Service) [Decommission workspaces](/rest/api/apimanagement/workspace-api/delete) when they're no longer in use. | Spending on unused resources is avoided. |
| (Service) Use the [built-in cache](/azure/api-management/api-management-howto-cache) if your workload's cached data fits within the constraints of the built-in cache in your tier. | Costs involved in purchasing and maintaining an external Redis-compatible cache are avoided. |
| (Service) Block malicious traffic before it reaches the gateway by using network controls, [DDoS protection](/azure/ddos-protection/ddos-protection-overview), and [web application firewalls](/azure/web-application-firewall/overview). Specific tiers of API Management incur charges based on the number of HTTP request operations that the gateway processes. As a result, undesirable traffic, such as bot-generated requests, can increase costs. <br/><br/> Evaluate the cost of the blocking mechanism versus the estimated cost of HTTP operation reduction to evaluate whether this approach has a ROI. | Charges incurred because of excessive malicious or nuisance HTTP operations against the gateway are reduced. |
| (API) Optimize the [policy expressions and processing](/azure/api-management/api-management-policy-expressions) and code to avoid excessive compute resource usage, such as processor, networking, or memory. | Unnecessary deployment of more units to provide capacity for unoptimized policy implementations and code is avoided. |
| (API) Evaluate the cost of logic placement between your gateway, your back end, or your public entry point, such as Azure Front Door. The same processing can often occur at any of these layers, each with its own tradeoffs. However, some layers might provide cost savings because of unused capacity available at that level. For instance, caching logic originally implemented in the back end might be more cost-effectively implemented at the gateway level by using the built-in cache. This approach reduces extra network and compute overhead on back-end services. | Load on high-cost resources is minimized by placing capabilities in the most cost-effective layer. This approach shifts workloads to layers that have spare capacity or lower-cost compute options.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to API Management.

> [!div class="checklist"]
>
> - **Review key knowledge and skills needed to operate the service:** Areas include API lifecycle, DevOps processes, networking and connectivity, monitoring and observability, and software development. This approach encompasses policy configuration, unit testing, and the creation of CI/CD pipelines.
>
> - **Consider documentation needs:** Commit to documenting processes for service-level and API-level configuration, lifecycle operations, and the different access patterns for API teams.
>
> - **Evaluate standard tooling** to support service operations. For example, adopt [APIOps](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops) methods, such as GitOps and CI/CD, to publish APIs and manage API configurations. Use IaC tooling for service-level configuration changes. Design reusable artifacts that can seamlessly transition from development environments to production. Consider integrating a linter like [Spectral](https://stoplight.io/open-source/spectral), either self-managed or as integrated into an Azure service like [Azure API Center](/azure/api-center/enable-managed-api-analysis-linting), into API approval pipelines.
>
> - **Determine key operational metrics and logs:** Review the [metrics](/azure/api-management/monitor-api-management-reference#metrics) for production. These metrics include gateway capacity, CPU usage, memory usage, and the number of requests. Review logs and [observability tools](/azure/api-management/observability), such as Azure Monitor and Application Insights. Determine the strategies and tools needed to effectively manage large volumes of observational data in production environments. Determine queries that deliver actionable insights for both the gateway operator and stakeholders that monitor specific hosted APIs.
>
> - **Plan regular testing of production workloads** by using load testing.
>
> - **Identify operational tasks beyond CI/CD or IaC processes** that require automation. Plan automation in areas such as API Management policy source management, Azure policies, and specific developer portal configurations.

### Configuration recommendations

These operational excellence recommendations can apply to either the service itself or to the traffic that flows through APIs and their policies. The *(Service)* or *(API)* designators indicate whether a recommendation targets the service or the APIs.

| Recommendation | Benefit |
| :------------- | :------ |
| (Service) Configure [Azure diagnostics resource logs for API Management](/azure/api-management/monitor-api-management-reference#resource-logs). | Platform-generated logs are available to query for routine operations, on-demand needs, or urgent scenarios, such as security audits. |
| (Service) Use Azure Event Grid for automation based on meaningful [events that your API Management instance raises](/azure/event-grid/event-schema-api-management), such as `APICreated`. | Automation or notification tasks can be built around key lifecycle events that occur in the API Management instance. |
| (Service) Avoid using a [self-hosted gateway](/azure/api-management/self-hosted-gateway-overview) if a Microsoft-hosted gateway unit works in your scenario. | Automation or notification tasks can be built around key lifecycle events that occur in the API Management instance. |
| (Service) Apply all of the [built-in Azure Policy policies](/azure/api-management/policy-reference) that help you govern your instance and constrain it to align with workload requirements, including security requirements. For example, use *deny* policies to prevent APIs from being exposed over HTTP or to prevent the enabling of the direct management REST API. Use *audit* policies if deny policies aren't available, or create custom deny policies if it's important to your workload. | Your API Management instance aligns with the design and remains aligned as the workload evolves. |
| (Service) Familiarize yourself with the [Diagnose and solve problems](/azure/api-management/diagnose-solve-problems) capability in the Azure portal. <br/><br/> Use the [Network status blade](/azure/api-management/api-management-using-with-vnet#verify-network-status) in the portal to troubleshoot network connectivity.| Site reliability engineering individuals can identify and troubleshoot gateway performance, service availability, and network connectivity problems across all environments. |
| (API) Use [Event Hubs](/azure/api-management/api-management-howto-log-event-hubs) to handle log or event streams from API invocations that require near real-time reactions or short-timeframe windowed operations. | Near real-time availability of log entries is provided. The [log data ingestion delay](/azure/azure-monitor/logs/data-ingestion-time) that occurs when logging to Azure Monitor within APIs is avoided. |
| (API) Support the usage of [API tracing](/azure/api-management/api-management-howto-api-inspector#enable-tracing-for-an-api) in development to help developers understand their policy implementation. | Developer productivity is optimized by providing insight into the implementation of policies within an API. Without this capability, developers need to introduce hacks in policy implementation to gain insight. |
| (API) Always define back ends by using the [backends feature](/azure/api-management/backends) of API Management. Reference these back ends by ID in policy by using [set-backend-service](/azure/api-management/set-backend-service-policy). Load balanced back ends should be defined as a [backend pool](/azure/api-management/backends#load-balanced-pool). | Back-end connection changes apply to all APIs that use the back end without requiring updates to individual policy code. The risk of misconfigurations or overlooked API policy changes is reduced, and scenarios where multiple APIs share the same back end are suited through this layer of configuration abstraction. |
| (API) Design the versioning approach of your APIs to align with API Management's [versioning and revision capabilities](/azure/api-management/api-management-versions). Factor this approach into your API deployment operations. | An API versioning strategy supported out of the box by API Management is used to take advantage of built-in capabilities instead of building custom solutions. |
| (Service & API) Define a consistent and sustainable operational process for adding, modifying, and deleting APIs. Determine whether to manage this experience manually through the portal or implement it through an [APIOps process](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops). Prefer using an IaC-based approach instead of a portal based approach. <br/><br/> API Management's representation in the Resource Manager API consists of many [child resources](/azure/templates/microsoft.apimanagement/service/apis), so it's important to adopt a [layered approach](/azure/well-architected/operational-excellence/infrastructure-as-code-design#use-a-layered-approach-for-iac-pipelines) for IaC-based management of this resource collection. | API specifications and their gateway implementations are integrated into the workload's change control processes. This approach prevents handling changes to back-end APIs differently from how they're exposed to API clients through the gateway.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for API Management.

> [!div class="checklist"]
>
> - **Define performance targets:** Key metrics to evaluate the performance of the API Management gateway include capacity metrics, such as CPU and memory usage percentages for gateway infrastructure, request duration, and request throughput. In multiregion deployments, define performance targets for each region. The customer needs to define appropriate scaling thresholds based on traffic patterns, API workloads, and scaling times.
>
> - **Collect performance data:** Review capabilities of built-in analytics, Azure Monitor metrics, Azure Monitor logs, Application Insights, and Event Hubs to collect and analyze performance at different levels of granularity.
>
> - **Review how to identify live performance problems:** Indicators for live performance problems include Azure service availability, HTTP response errors, and errors raised in the **Diagnose and solve problems** section in the portal. Troubleshoot performance and availability problems by using Application Insights, Kusto queries, and recommendations from API Management Diagnostics in the Azure portal.
>
> - **Test performance:** Test performance under production conditions by using load testing.
>
> - **Evaluate adjacent services that might improve performance:** Caching policies or an external cache might improve the performance of specific API operations. Azure Front Door and Application Gateway are common choices for TLS offloading.
>
> - **Review the documented limits and constraints:** [API Management has limits and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-api-management-limits). Review the documented constraints and compare them against your workload's requirements to see if you need to design a solution that avoids these constraints.

### Configuration recommendations

These performance efficiency recommendations can apply either to the service itself or to the traffic that flows through APIs and their policies. The *(Service)* or *(API)* designators indicate whether a recommendation targets the service or the APIs.

| Recommendation | Benefit |
|:------------- |:------ |
| (Service) Dynamically scale to match demand. Configure [autoscale rules](/azure/api-management/api-management-howto-autoscale) or other automated processes to adjust gateway units to match a target usage capacity. | Elasticity is achieved based on concurrent usage, which prevents resource depletion of currently deployed units or over-allocation of capacity. |
| (API) Minimize expensive processing tasks, such as generating large buffered payload sizes, by using the [validate-content policy](/azure/api-management/validate-content-policy) on large request bodies or by maintaining a high number of active WebSockets. | The load on scale units is reduced, which enables them to process more requests concurrently before needing to scale out. |
| (Service & API) [Collect performance metrics](/azure/api-management/monitor-api-management-reference). <br/><br/> Common API performance metrics include: <br/><br/> - Request processing time for the gateway itself and for the full operation. <br/> - Gateway unit resource usage metrics. <br/> - Throughput measurements like requests per second or megabits per second. <br/> - Cache hit ratio. | Actual performance is measured against the targets of the workload.|
| (Service & API) Define a [sampling percentage for Application Insights](/azure/api-management/api-management-howto-app-insights#performance-implications-and-log-sampling) that provides sufficient visibility without affecting performance. | Observability data needs are satisfied without affecting overall performance. |
| (Service & API) Evaluate the performance impact of logic placement between your gateway, your back end, or your public entry point, such as Azure Front Door. The same processing tasks can often occur at any of these layers, with performance tradeoffs and limitations in optimization opportunities for each. If a task, such as an API management API policy, introduces too much latency, consider whether that task can run somewhere else in a more optimized way. | Tasks that add latency to APIs run on compute optimized to meet workload requirements. |

## Azure policies

Azure provides an extensive set of built-in policies related to API Management and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- The gateway is configured for zone redundancy.
- Proper network controls are in place for the API Management gateway, such as deployment in a virtual network.
- The service configuration endpoints aren't publicly accessible.
- The direct Management REST API is disabled.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/api-management/policy-reference) and other policies that might affect the security of the API Management gateway.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Tradeoffs

You might need to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **High availability through redundancy and isolation**

- **High availability.** Redundancy affects costs. For example, provisioning at least three units to avoid zonal outages might not be financially feasible for your workload. Costs increase further with a multiregion architecture, which requires at least six units, or three units per region. A multiregion setup also adds operational costs for coordinating safe deployments, reliable scaling, and failover coordination with back ends.

- **Isolation.** Isolating workloads across workspaces or API Management instances adds operational complexity because it includes managing a multitenant system that has compute isolation.

:::image type="icon" source="../_images/trade-off.svg"::: **Scale to match demand**

- When you automatically scale out to meet the demand from well-behaved clients, those costs are already factored into your cost models. However, this scaling capability also allows the service to scale to handle traffic from nuisance and malicious traffic.

- Mitigating undesired traffic incurs costs. Adding services like a web application firewall and DDoS protection increases expenses. Scaling your service to handle traffic increases costs because of added units. Setting upper scaling limits can cap spending but might result in reliability problems for legitimate users if malicious or harmful traffic overwhelms your API.

:::image type="icon" source="../_images/trade-off.svg"::: **Federated versus distributed approach**

A fundamental decision in API Management is whether to colocate disparate workloads within a single API Management instance or to isolate workloads across multiple instances in a fully autonomous topology.

API Management workspaces enable efficient operation as a multitenant colocation platform for multiple API teams. The tiered pricing models are designed to allow service costs to be shared across all tenants that use the gateways. However, like any colocation platform, outages or misconfigurations can result in widespread effects on unrelated workloads that share the same infrastructure.

A fully distributed model, where each workload team manages its own instances, introduces duplicative costs and redundancy in routine operations. However, it provides inherent blast radius mitigation for reliability, security, and performance-related incidents.

## Example architecture

Foundational architecture that demonstrates the key recommendations: [API Management landing zone architecture](/azure/architecture/example-scenario/integration/app-gateway-internal-api-management-function).

## Next steps

API Management is often combined with the following services. Be sure to review their service guides or product documentation if your workload includes these services.

- [Application Gateway](./azure-application-gateway.md)
- [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)
- [Azure Front Door](./azure-front-door.md)
- [Key Vault](/azure/key-vault/general/basic-concepts)
- [Azure Virtual Network](./virtual-network.md)
- [Web Application Firewall](/azure/web-application-firewall/overview)

The following articles demonstrate some of the recommendations discussed in this article.

- [Access Azure OpenAI and other language models through a gateway](/azure/architecture/ai-ml/guide/azure-openai-gateway-guide)
- [Automated API deployments using APIOps](/azure/architecture/example-scenario/devops/automated-api-deployments-apiops)
- [API Management architecture in an application landing zone](/azure/architecture/example-scenario/integration/app-gateway-internal-api-management-function) and [Azure landing zone platform considerations](/azure/cloud-adoption-framework/scenarios/app-platform/api-management/landing-zone-accelerator) for API Management deployments

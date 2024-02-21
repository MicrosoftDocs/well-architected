---
title: Azure Well-Architected Framework perspective on Azure Front Door
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Front Door.
author: duongau
ms.author: prwilk
ms.date: 02/12/2024
ms.topic: conceptual
products:
  - azure-front-door
categories:
  - networking
---

# Azure Well-Architected Framework perspective on Azure Front Door

Azure Front Door is a global load balancer and content delivery network that routes HTTP and HTTPS traffic. Azure Front Door delivers and distributes traffic that's closest to the application users. 

This article assumes that as an architect you've reviewed the [load balancing options](/azure/architecture/guide/technology-choices/load-balancing-overview) and chosen Azure Front Door as the load balancer for your workload. It also assumes that your application is deployed to multiple regions in an active-active or active-passive model. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern and design strategies that are localized to the technology scope. 
>
> This article also includes *recommendations* on the technology capabilities that help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Front Door and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations:
> [Mission-critical baseline architecture with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure Front Door

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components,system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the tiers and Azure Content Delivery Network capabilities. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Estimate the traffic pattern and volume**. The number of requests from the client to the Azure Front Door edge might influence your tier choice. If you need to support a high volume of requests, consider the Azure Front Door Premium tier because performance ultimately impacts availability. However, there's a cost tradeoff. These tiers are described in [Performance Efficiency](#performance-efficiency).
>
> - **Choose your deployment strategy**. The fundamental deployment approaches are *active-active* and *active-passive*. Active-active deployment means that multiple environments or stamps that run the workload serve traffic. Active-passive deployment means that only the primary region handles all traffic, but it fails over to the secondary region when necessary. In a multiregion deployment, stamps run in different regions for higher availability with a global load balancer, like Azure Front Door, that distributes traffic. Therefore, it's important to configure the load balancer for the appropriate deployment approach. 
> 
>   Azure Front Door supports several routing methods, which you can configure to distribute traffic in an active-active or active-passive model.
>
>   The preceding models have many variations. For example, you can deploy the active-passive model with a warm spare. In this case, the secondary hosted service deploys with the minimum possible compute and data sizing and runs without load. Upon failover, the compute and data resources scale to handle the load from the primary region. For more information, see [Key design strategies for multiregion design](/azure/well-architected/reliability/highly-available-multi-region-design#key-design-strategies).
>
>   Some applications need the user connections to stay on the same origin server during the user session. From a reliability perspective, we don't recommend keeping user connections on the same origin server. Avoid session affinity as much as possible.
>
> - **Use the same host name on Azure Front Door and origin servers**. To ensure that cookies or redirect URLs work properly, preserve the original HTTP host name when you use a reverse proxy, like a load balancer, in front of a web application. 
>
> - **Implement the health endpoint monitoring pattern**. Your application should expose health endpoints, which aggregate the state of the critical services and dependencies that your application needs to serve requests. Azure Front Door health probes use the endpoint to detect origin servers' health. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).
>
> - **Take advantage of the built-in content delivery network functionality in Azure Front Door**. The content delivery network feature of Azure Front Door has hundreds of edge locations and can help withstand distributed denial of service (DDoS) attacks. These capabilities help improve reliability.
>
> - **Consider a redundant traffic management option**. Azure Front Door is a globally distributed service that runs as a singleton in an environment. Azure Front Door is a potential single point of failure in the system. If the service fails, then clients can't access your application during the downtime.
>   
>   Redundant implementations can be complex and costly. Consider them only for mission-critical workloads that have a very low tolerance to outage. Carefully consider the [tradeoffs](/azure/architecture/guide/networking/global-web-applications/overview#tradeoffs). 
>   - If you absolutely need redundant routing, see [Global routing redundancy](/azure/architecture/guide/networking/global-web-applications/overview).
>   - If you need redundancy only to serve cached content, see [Global content delivery](/azure/architecture/guide/networking/global-web-applications/mission-critical-content-delivery).

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Choose a [routing method](/azure/frontdoor/routing-methods) that supports your deployment strategy. <br><br> The weighted method, which distributes traffic based on the configured weight coefficient, supports active-active models. <br><br> A priority-based value that configures the primary region to receive all traffic and send traffic to the secondary region as a backup supports active-passive models. <br><br> Combine the preceding methods with latency so that the origin with the lowest latency receives traffic. |You can select the best origin resource by using a series of decision steps and your design. The selected origin serves traffic within the allowable latency range in the specified ratio of weights.|
|Support redundancy by having [multiple origins in one or more back-end pools](/azure/frontdoor/quickstart-create-front-door). <br><br>Always have redundant instances of your application and make sure each instance exposes an endpoint or origin. You can place those origins in one or more back-end pools.|Multiple origins support redundancy by distributing traffic across multiple instances of the application. If one instance is unavailable, then other back-end origins can still receive traffic.|
|[Set up health probes on the origin](/azure/frontdoor/health-probes). <br><br>Configure Azure Front Door to conduct health checks to determine if the back-end instance is available and ready to continue receiving requests.| Enabled health probes are part of the health monitoring pattern implementation. Health probes make sure that Azure Front Door only routes traffic to instances that are healthy enough to handle requests. <br> For more information, see [Best practices on health probes](/azure/frontdoor/health-probes).|
|Set a timeout on forwarding requests to the back end. <br><br> Adjust the timeout setting according to your endpoints' needs. If you don't, Azure Front Door might close the connection before the origin sends the response. <br> You can also lower the default timeout for Azure Front Door if all of your origins have a shorter timeout. <br> For more information, see [Troubleshooting unresponsive requests](/azure/frontdoor/troubleshoot-issues#troubleshooting-steps).|Timeouts help prevent performance issues and availability issues by terminating requests that take longer than expected to complete.|
|Use the same host name on Azure Front Door and your origin. <br><br>Azure Front Door can rewrite the host header of incoming requests, which is useful when you have multiple custom domain names that route to one origin. However, rewriting the host header might cause issues with request cookies and URL redirection.|Set the same host name to prevent malfunction with session affinity, authentication, and authorization. For more information, see [Preserve the original HTTP host name](/azure/architecture/best-practices/host-name-preservation) between a reverse proxy and its back-end web application.|
|Decide if your application requires [session affinity](/azure/frontdoor/routing-methods#23session-affinity). If you have high reliability requirements, we recommend that you disable session affinity. |With session affinity, user connections stay on the same origin during the user session. If that origin becomes unavailable, the user experience might be disrupted.|
|Take advantage of the [rate-limiting rules](/azure/web-application-firewall/afds/waf-front-door-rate-limit) that are included with a web application firewall (WAF).|Limit requests to prevent clients from sending too much traffic to your application. Rate limiting can help you avoid problems like a retry storm.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design in restricting traffic coming through Azure Front Door.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md). Identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline for [Azure Front Door](/security/benchmark/azure/baselines/azure-front-door-security-baseline).**
>
> - **Protect the back-end servers**. The front end acts as the single point of ingress to the application. 
>
>   Azure Front Door uses Azure Private Link to access an application's origin. Private Link creates segmentation so that the back ends don't need to expose public IP addresses and endpoints. For more information, see [Secure your origin with Private Link in Azure Front Door Premium](/azure/frontdoor/private-link).
>
>   Configure your back-end services to accept only requests with the same host name that Azure Front Door uses externally.
>
> - **Allow only authorized access to the control plane**. Use Azure Front Door [role-based access control (RBAC)](/azure/role-based-access-control/overview) to restrict access to only the identities that need it.
>
> - **Block common threats at the edge**. WAF is integrated with Azure Front Door. Enable WAF rules on the front ends to protect applications from common exploits and vulnerabilities at the network edge, closer to the attack source.
>   Consider geo-filtering to restrict access to your web application by countries or regions. 
>   
>   For more information, see [Azure Web Application Firewall on Azure Front Door](/azure/web-application-firewall/afds/afds-overview).
> 
> - **Protect Azure Front Door against unexpected traffic**. [Azure Front Door uses the basic plan of Azure DDoS protection](/azure/frontdoor/front-door-ddos) to protect application endpoints from DDoS attacks. If you need to expose other public IP addresses from your application, consider adding the DDoS Protection standard plan for those addresses for advanced protection and detection capabilities.
>
>   There are also WAF rule sets that detect bot traffic or unexpectedly large volumes of traffic that could potentially be malicious. 
>
> - **Protect data in transit**. Enable end-to-end Transport Layer Security (TLS), HTTP to HTTPS redirection, and managed TLS certificates when applicable. For more information, see [TLS best practices for Azure Front Door](/azure/frontdoor/best-practices#23tls-best-practices).
>
> - **Monitor anomalous activity**. Regularly review the logs to check for attacks and false positives. Send [WAF logs from Azure Front Door](/azure/web-application-firewall/afds/waf-front-door-monitor) to your organization's centralized security information and event management (SIEM), such as Microsoft Sentinel, to detect threat patterns and incorporate preventative measures in the workload design. 

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Enable WAF rule sets that detect and block potentially malicious traffic. This feature is available on the Premium tier. We recommend these rule sets: <br> - [Default](/azure/web-application-firewall/afds/afds-overview#azure-managed-rule-sets)<br>- [Bot protection](/azure/web-application-firewall/afds/afds-overview#bot-protection-rule-set)<br>- [IP restriction](/azure/web-application-firewall/afds/waf-front-door-configure-ip-restriction)<br>- [Geo-filtering](/azure/web-application-firewall/afds/waf-front-door-tutorial-geo-filtering)<br>- [Rate limiting](/azure/web-application-firewall/afds/waf-front-door-rate-limit)|Default rule sets are updated frequently based on OWASP top-10 attack types and information from Microsoft Threat Intelligence. <br> The specialized rule sets detect certain use cases. For example, bot rules classify bots as good, bad, or unknown based on the client IP addresses. They also block bad bots and known IP addresses and restrict traffic based on geographical location of the callers. <br><br> By using a combination of rule sets, you can detect and block attacks with various intents.|
| [Create exclusions](/azure/web-application-firewall/afds/waf-front-door-exclusion-configure) for managed rule sets. <br><br> Test a WAF policy in detection mode for a few weeks and adjust any false positives before you deploy it. | Reduce false positives and allow legitimate requests for your application. |
|Send the [host header](/azure/frontdoor/origin?pivots=front-door-standard-premium#origin-host-header) to the back end. |The back-end services should be aware of the host name so that they can create rules to accept traffic only from that host.
| Enable end-to-end TLS, HTTP to HTTPS redirection, and managed TLS certificates when applicable. <br><br> Review the [TLS best practices for Azure Front Door](/azure/frontdoor/best-practices#23tls-best-practices). <br><br> Use TLS version 1.2 as the minimum allowed version with ciphers that are relevant for your application. <br><br> Azure Front Door managed certificates should be your default choice for ease of operations. However, if you want to manage the lifecycle of the certificates, use your own certificates in [Azure Front Door custom domain](/azure/frontdoor/standard-premium/how-to-configure-https-custom-domain) endpoints and store them in Key Vault.|TLS ensures that data exchanges between the browser, Azure Front Door, and the back-end origins are encrypted to prevent tampering. <br><br> Key Vault offers managed certificate support and simple certificate renewal and rotation.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Front Door and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Review Azure Front Door tiers and pricing**. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator) to estimate the realistic costs for each tier. [Compare the features](/azure/frontdoor/front-door-cdn-comparison) and suitability of each tier for your scenario. For instance, only the Premium tier supports connecting to your origin via Private Link.
>
>   The Standard SKU is more cost-effective and suitable for moderate traffic scenarios. In the Premium SKU, you pay a higher unit rate, but you gain access to security benefits and advanced features like managed rules in WAF and Private Link. Consider the tradeoffs on [reliability](#reliability) and [security](#security) based on your business requirements. 
>
> - **Consider bandwidth costs**. The bandwidth costs of Azure Front Door depend on the tier that you choose and the type of data transfer. Azure Front Door provides built-in reports for billable metrics. To assess your costs related to bandwidth and where you can focus your optimization efforts, see [Azure Front Door reports](/azure/frontdoor/standard-premium/how-to-reports).
>
> - **Optimize incoming requests**. Azure Front Door bills the incoming requests. You can set restrictions in your design configuration. 
>    
>   Reduce the number of requests by using design patterns like [Backend for Frontends](/azure/architecture/patterns/backends-for-frontends) and [Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation). These patterns can improve the efficiency of your operations. 
>
>   WAF rules restrict incoming traffic, which can optimize costs. For example, use rate limiting to prevent abnormally high levels of traffic, or use geo-filtering to allow access only from specific regions or countries.
>
> - **Use resources efficiently**. Azure Front Door uses a routing method that helps with resource optimization. Unless the workload is extremely latency sensitive, distribute traffic evenly across all environments to effectively use deployed resources. 
>
>   Azure Front Door endpoints can serve many files. One way to reduce bandwidth costs is to use compression. 
>
>   Use caching in Azure Front Door for content that doesn't change often. Because content is served from a cache, you save on bandwidth costs that are incurred when the request is forwarded to the back ends.
>
> - **Consider using a shared instance that's provided by the organization**. Costs incurred from centralized services are shared between the workloads. However, consider the tradeoff with [reliability](#reliability). For mission-critical applications that have high availability requirements, we recommend an autonomous instance. 
>
> - **Pay attention to the amount of data logged**. Costs related to both bandwidth and storage can accrue if certain requests aren't necessary or if logging data is retained for a long period of time.

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| [Use caching](/azure/frontdoor/front-door-caching) for endpoints that support it. | Caching optimizes data transfer costs because it reduces the number of calls from your Azure Front Door instance to the origin.|
| [Consider enabling file compression](/azure/frontdoor/standard-premium/how-to-compression). <br>For this configuration, the application must support compression and caching must be enabled.| Compression reduces bandwidth consumption and improves performance.|
|Disable health checks in single back-end pools.<br>If you have only one origin configured in your Azure Front Door origin group, these calls are unnecessary.|You can save on bandwidth costs by disabling requests that aren't required to make routing decisions.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the
operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Front Door.

> [!div class="checklist"]
>
> - **Use infrastructure as code (IaC) technologies**. Use IaC technologies like [Bicep and Azure Resource Manager templates](/azure/frontdoor/front-door-quickstart-template-samples) to provision the Azure Front Door instance. These declarative approaches provide consistency and straightforward maintenance. For example, by using IaC technologies, you can easily adopt new ruleset versions.
>   Always use the latest API version.
>
> - **Simplify configurations**. Use Azure Front Door to easily manage configurations. For example, suppose your architecture supports microservices. Azure Front Door supports [redirection capabilities](/azure/frontdoor/front-door-url-redirect), so you can use path-based redirection to target individual services. 
>   Another use case is the configuration of wildcard domains. 
>
> - **Handle progressive exposure by using [Azure Front Door routing methods](/azure/frontdoor/routing-methods)**. For a [weighted load balancing approach](/azure/frontdoor/routing-methods#weighted-traffic-routing-method) you can use a canary deployment to send a specific percentage of traffic to a back end. This approach helps you test new features and releases in a controlled environment before you roll them out.
>
> - **Collect and analyze Azure Front Door operational data as part of your workload monitoring**. Capture relevant Azure Front Door logs and metrics with Azure Monitor Logs. This data helps you troubleshoot, understand user behaviors, and optimize operations. 
>
> - **Offload certificate management to Azure**. Ease the operational burden associated with certification rotation and renewals.


### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| [Use HTTP to HTTPS redirection](/azure/frontdoor/front-door-url-redirect#redirection-protocol) to support forward compatibility.|When redirection is enabled, Azure Front Door automatically redirects clients that are using older protocol to use HTTPS for a secure experience.|
|[Capture logs and metrics](/azure/frontdoor/front-door-diagnostics). <br><br> Include resource activity logs, access logs, health probe logs, and WAF logs. <br><br>[Set up alerts](/azure/frontdoor/standard-premium/how-to-monitor-metrics#configure-alerts-in-the-azure-portal).|Monitoring ingress flow is a crucial part of monitoring an application. You want to track requests and make performance and security improvements. You need data to debug your Azure Front Door configuration. <br><br> With alerts in place, you can get instant notifications of any critical operational issues.|
|Review the [built-in analytics reports](/azure/frontdoor/standard-premium/how-to-reports).|A holistic view of your Azure Front Door profile helps drive improvements based on traffic and security reports through WAF metrics.|
| [Use managed TLS certificates](/azure/frontdoor/domain#azure-front-door-managed-tls-certificates) when possible. | Azure Front Door can issue and manage certificates for you. This feature eliminates the need for certificate renewals and minimizes the risk of an outage due to an invalid or expired TLS certificate.|
|[Use wildcard TLS certificates](/azure/frontdoor/front-door-wildcard-domain).|  You don't need to modify the configuration to add or specify each subdomain separately. |


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Front Door.

> [!div class="checklist"]
>
> - **Plan capacity by analyzing your expected traffic patterns**. Conduct thorough testing to understand how your application performs under different loads. Consider factors like simultaneous transactions, request rates, and data transfer. 
>
>   Base your SKU choices on that planning. The Standard SKU is more cost-effective and suitable for moderate traffic scenarios. If you anticipate higher loads, we recommend the Premium SKU.
>
> - **Analyze performance data by regularly reviewing [Azure Front Door reports](/azure/frontdoor/standard-premium/how-to-reports)**. These reports provide insights into various metrics that serve as performance indicators at the technology level. 
>
>   Use Azure Front Door reports to set realistic performance targets for your workload. Consider factors like response times, throughput, and error rates. Align the targets with your business requirements and user expectations.
>
> - **Optimize data transfers.**
>
>   - Use caching to reduce latency in serving static content, such as images, stylesheets, and JavaScript files, or content that doesn't change frequently.
>
>     Optimize your application for caching. Use cache expiration headers in the application that control how long the content should be cached by clients and proxies. Longer cache validity means less frequent requests to the origin server, which results in reduced traffic and lower latency.
>   - Reduce the size of files that are transmitted over the network. Smaller files lead to faster load times and improved user experience.
>   - Minimize the number of back-end requests in the application. 
>
>     For example, a web page displays user profiles, recent orders, balances, and other related information. Instead of making separate requests for each set of information, use design patterns to structure your application so that multiple requests are aggregated into a single request. 
>
>      By aggregating requests, you send less data between the front end and the back end and establish fewer connections between the client and the back end, which reduces overhead. Also, Azure Front Door handles fewer requests, which prevents overload.
> 
> - **Optimize the use of health probes**. Get health information from health probes only when the state of the origins change. Strike a balance between monitoring accuracy and minimizing unnecessary traffic.
>
>   Health probes are typically used to assess the health of multiple origins within a group. If you have only one origin configured in your Azure Front Door origin group, disable health probes to reduce unnecessary traffic on your origin server. Because there's only one instance, the health probe status won't impact routing.
>
> - **Review the origin routing method**. Azure Front Door provides various routing methods, including latency-based, priority-based, weighted, and session affinity-based routing, to the origin. These methods significantly affect your application's performance. To learn more about the best traffic routing option for your scenario, see [Traffic routing methods to origin](/azure/frontdoor/routing-methods).
>
> - **Review the location of origin servers**. Your origin servers' location impacts the responsiveness of your application. Origin servers should be closer to the users. Azure Front Door ensures that users from a specific location access the nearest Azure Front Door entry point. The performance benefits include faster user experience, better use of latency-based routing by Azure Front Door, and minimized data transfer time by using caching, which stores content closer to users.
>
>     For more information, see [Traffic by location report](/azure/frontdoor/standard-premium/how-to-reports#traffic-by-location-report). 

### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| [Enable caching](/azure/frontdoor/front-door-caching). <br><br> You can optimize [query strings for caching](/azure/frontdoor/front-door-caching). For purely static content, ignore query strings to maximize your use of the cache. <br><br> If your application uses query strings, consider including them in the cache key. Including the query strings in the cache key allows Azure Front Door to serve cached responses or other responses, based on your configuration.| Azure Front Door offers a robust content delivery network solution that caches content at the edge of the network. Caching reduces the load on the back-end servers and reduces data movement across the network, which helps offload bandwidth usage.|
| [Use file compression](/azure/frontdoor/standard-premium/how-to-compression) when you're accessing downloadable content.| Compression in Azure Front Door helps deliver content in the optimal format, has a smaller payload, and delivers content to the users faster.|
|When you configure health probes in Azure Front Door, consider using `HEAD` requests instead of `GET` requests. <br> The health probe reads only the status code, not the content. |`HEAD` requests let you query a state change without fetching its entire content.|
|Evaluate whether you should enable [session affinity](/azure/frontdoor/routing-methods#23session-affinity) when requests from the same user should be directed to the same back-end server. <br><br> From a reliability perspective, we don't recommend this approach. If you use this option, the application should gracefully recover without disrupting user sessions. <br><br> There's also a tradeoff on load balancing because it restricts the flexibility of distributing traffic across multiple back ends evenly.| Optimize performance and maintain continuity for user sessions, especially when applications rely on maintaining state information locally. |


## Azure policies

Azure provides an extensive set of built-in policies related to Azure Front Door and its dependencies. Some of the preceding recommendations can be audited through Azure Policies. For example, you can check whether:

- You need the Premium tier to support managed WAF rules and Private Link in Azure Front Door profiles. 
- You need to use the minimum TLS version, which is version 1.2. 
- You need secure, private connectivity between Azure Front Door Premium and Azure PaaS services. 
- You need to enable resource logs. WAF should have request body inspection enabled. 
- You need to use policies to enforce the WAF rule set. For example, you should enable bot protection and turn on rate-limiting rules.

For comprehensive governance, review the [built-in definitions for Azure Content Delivery Network](/azure/governance/policy/samples/built-in-policies#cdn) and other Azure Front Door policies that are listed in [Azure Policy built-in policy definitions](/azure/governance/policy/samples/built-in-policies).


## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Azure Front Door.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps


Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- Use the following reference architectures as examples of how you can apply this article's guidance to a workload:
   - [Mission-critical baseline with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture)
- Build implementation expertise by using the following product documentation:
   - [Azure Front Door and Azure content Delivery Network](/azure/frontdoor/)
   - [Best practices for Azure Front Door](/azure/frontdoor/best-practices)
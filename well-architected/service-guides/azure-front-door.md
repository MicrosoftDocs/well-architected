---
title: Azure Well-Architected Framework - Azure Front Door
description: Design considerations and recommendations about Azure Front Door.
author: duongau
ms.author: prwilk
ms.date: 01/28/2024
ms.topic: conceptual
products:
  - azure-front-door
categories:
  - networking
---
# Azure Well-Architected Framework perspective on Azure Front Door

Azure Front Door is a global load balancer suitable for HTTP(S) traffic and a Content Delivery Network (CDN) with capabilities to deliver and distribute traffic closest to the application users. 

This article assumes that as an architect, you've reviewed the [**load balancing options**](/azure/architecture/guide/technology-choices/load-balancing-overview) and chosen Azure Front Door as your load balancer. Most likely your application is deployed to multiple regions in an active-active or active-passive model. 

> [!IMPORTANT]
>
> **How to use this guide**
>
> The guidance in this article provides architectural recommendations that are mapped to the principles of the [**Azure Well-Architected Framework pillars**](../pillars.md).
>
> Each section has a _design checklist_ that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are _recommendations_ on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Front Door. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demostrates the key recommendations: [Mission-critical baseline architecture with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture).

##### Technology scope

This review focuses on the interrelated decisions for these Azure resources.  

- Azure Front Door
- Azure CDN Microsoft


## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the tiers and CDN capabilities. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Estimate the traffic pattern and volume**. The number of requests from client to Azure Front Door edge might influence your tier choice. If you need to support high volume of requests, consider the Azure Front Door Premium tier because performance ultimately might impact availability. However, there's a cost tradeoff. These tiers are described in [Performance Efficiency](#performance-efficiency).
>
> - **Consider a redundant traffic management option**.  Front Door is a globally distributed service that runs as a singleton in the environment. **Front Door is a potential single point of point in the system**, this means if the service fails, clients won't be able to access your application during the downtime. 
>   
>   Redundant implementations of this nature can be complex and costly. It should only be considered for mission-critical workloads that have very low tolerance to outage. Carefully consider the [tradeoffs](/azure/architecture/guide/networking/global-web-applications/overview#tradeoffs). 
>   - If you absolutely need redundant routing, see [**Global routing redundancy**](/azure/architecture/guide/networking/global-web-applications/overview).
>   - If need redundancy only to serve cached content, see [**Global content delivery**](/azure/architecture/guide/networking/global-web-applications/mission-critical-content-delivery).
> 
> - **Decide your deployment strategy**. The fundamental approaches are: _active-active_, where mutiple environments (or stamps) running the workload serve traffic; _active-passive_ where only the primary handles all traffic but fails over to the secondary, when necessary. In multiregion deployment, stamps run in different regions for higher availability with a global load balancer, such as Front Door, distributing traffic. Therefore, it's important for the the load balancer to know the chosen approach. 
> 
>   **Azure Front Door supports various routing methods** you can configure to distribute traffic in an active-active or active-passive model.
>
>   The preceding models have many variations. For example, you can deploy with active-active with warm spare in which the secondary is deployed with the minimum possible compute and data sizing and runs without load. Upon failover, the compute and data resources are scaled to handle the load from the primary region. For more information, see [**RE:04: Key design strategies for multi-region design**](/azure/well-architected/reliability/highly-available-multi-region-design#key-design-strategies).
>
> - **Evaluate the resiliency features**. Addition to the routine methods, Azure Front Door provides capabilities that implement retry logic for resilience against transient failures. If, however, those failures are permanent, set a timeout value. 
>   
>   Some applications need the user connections to stay on the same origin server during the user session. This isn't recommended from a reliability perspective. Avoid session affinity as much as possible.
>
> - **Use the same host name on Front Door and origin servers**. To ensure that cookies or redirect URLs work properly,preserve the original HTTP host name when using a reverse proxy, such as a load balancer, in front of a web application. 
>
> - **Implement the health endpoint monitoring pattern**. Your **application should expose health endpoints**, which  aggregate the state of the critical services and dependencies needed to serve requests. Front Door health probes can then use the endpoint to detect health of the origin servers. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).
>
> - **Don't centralize Front Door within an organization**. Central instances are shared among workloads. That centralized approach might not factor the reliability concerns specific to your use case.

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Choose a [**routing method**](/azure/frontdoor/routing-methods) that supports your deployment strategy. <br><br> Active-active can be supported through the  weighted method that distributes traffic based on the configured weight coefficient. <br><br> Active-passive is supported through a priority-based value that configures primary to receive all traffic and send traffic to secondary as a backup. <br><br> Combine the preceding methods with latency so that the origin with the lowest latency receives traffic. |You'll be able to select the best origin resource through a series of decision steps and your design. The selected origin will serve traffic within the allowable latency range in the specified ratio of weights.|
|**Use [multiple backend pools](/azure/frontdoor/quickstart-create-front-door)**. <br><br>A backend pool has endpoint for all application instances deployed across multiple zones or regions.|Multiple backend pools support redundancy by distributing traffic across multiple instances. If one instance is unavailable, other backend origins can still receive traffic.|
|[**Set up health probes on the origin**](/azure/frontdoor/health-probes). <br><br>Configure Front Door to conduct health checks to determine if the backend instance is available and ready to continue to receive request.| Enabling health probes is part of  implementing the health monitoring pattern that makes sure that Front Door only routes traffic to instances that are healthy enough to handle requests. <br> For best practices, see [Best practices on health probes](/azure/frontdoor/health-probes)|
|**Set a timeout** on forwarding request to the backend. <br><br> Adjust the timeout setting according to your endpoints' needs. If you don't, Front Door might close the connection before the origin sends the response. Alternatively, you can lower Front Door's default timeout if all your origins have a shorter timeout. <br> For more information, see [Troubleshooting unresponsive requests](/azure/frontdoor/troubleshoot-issues#troubleshooting-steps).|Timeouts help avoid performance issues and availability issues by terminating requests that take longer than expected to complete.|
|**Use the same host name on Front Door and your origin**. <br><br>Azure Front Door can rewrite the host header of incoming requests, which is useful when you have multiple custom domain names that route to one origin. However, rewriting the host header might cause issues with request cookies and URL redirection.|Setting the same host name can prevent malfunction with session affinity, authentication and authorization might malfunction. For more information, see [Preserve the original HTTP host name](/azure/architecture/best-practices/host-name-preservation) between a reverse proxy and its back-end web application.|
|Evaluate if [**session affinity**](/azure/frontdoor/routing-methods#23session-affinity) is required. If you have high reliability requirements, disabling session affinity is recommended. |With session affinity, user connections stay on the same origin during the user session. If that origin becomes unavailable, the user experience might be disrupted.|
|Take advantage of the [Rate limiting rules](/azure/web-application-firewall/afds/waf-front-door-rate-limit) included with Web application firewall (WAF).|You can limit the requests to prevent clients from sending too much traffic to your application. Rate limiting can help you avoid problems like a retry storm.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design in restricting traffic coming through Azure Front Door.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline** for [Azure Front Door](/security/benchmark/azure/baselines/azure-front-door-security-baseline).
>
> - **Protect the backend servers**. Front end acts as the single point of ingress to the application. 
>
>   Use Front Door's capability to use Azure Private Link for accessing application origin. Private Links create segmentation and avoid the need for the backends to expose public IPs/endpoints. For more information, see [Secure your Origin with Private Link in Azure Front Door Premium](/azure/frontdoor/private-link).
>
>   The backend services accept traffic only from Front Door's host name if  Front Door is within the trust boundary of the workload.
>
> - **Only allow authorized access**. Use Front Door's [role-based access control (RBAC)](/azure/role-based-access-control/overview) to restrict access to only those identities that need it.
>
> - **Block common threats at edge**.  Web application firewall (WAF) is integrated with Front Door. Enable WAF rules on the frontends to protect applications from common exploits and vulnerabilities at the network edge closer to the attack source.
>   Consider geo-filtering to restrict access to your web application by countries or regions. 
>   
>   Refer to [Azure Web Application Firewall on Azure Front Door](/azure/web-application-firewall/afds/afds-overview).
> 
> - **Protect Front Door against unexpected traffic.** [Front Door uses Azure DDoS protection Basic](/azure/frontdoor/front-door-ddos) to protect application endpoints against DDoS attacks. Consider adding Azure DDoS protection Standard for advanced protection and detection capabilities.
>
>   There are also WAF rule sets that detect bot traffic or unexpectedly large volume of traffic that could potentially be malicious. 
>
> - **Protect data in transit**. Enable end-to-end TLS, HTTP to HTTPS redirection, and managed TLS certificates (when applicable). Review the [TLS best practices for Front Door](/azure/frontdoor/best-practices#23tls-best-practices).
>
> - **Monitor anamolous activity**. Regularly review the logs to check for attacks and false positives. Send [WAF logs from Front Door](/azure/web-application-firewall/afds/waf-front-door-monitor) to the organization's centralized SIEM, such as Microsoft Sentinel to detect threat patterns and preventitive measures that can be incorporated in the workload design. 

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|**Enable WAF rulesets** that detect and block potentially malicious traffic. These rulesets are recommended: <br> - [Default](/azure/web-application-firewall/afds/afds-overview#azure-managed-rule-sets)<br>- [Bot protection](/azure/web-application-firewall/afds/afds-overview#bot-protection-rule-set)<br>- [IP restriction](/azure/web-application-firewall/afds/waf-front-door-configure-ip-restriction)<br>- [Geo-filtering](/azure/web-application-firewall/afds/waf-front-door-tutorial-geo-filtering)<br>- [Rate limiting](/azure/web-application-firewall/afds/waf-front-door-rate-limit)|Default rule sets are updated frequently based on OWASP top-10 attack types and information from Microsoft Threat Intelligence. <br> The specialized rulesets can detect certain use cases. For example, bot rules can classify bots as good, bad, or unknown based on the client IP addresses and also block bad bots, known IP addresses, restrict traffic based on geographical location of the callers, and others. <br><br> By using a combination of rule sets, you'll be able to detect and block attacks with different intents.|
| **[Create exclusions](/azure/web-application-firewall/afds/waf-front-door-exclusion-configure) through managed rules**. <br><br> Test a WAF policy in Detection mode for a few weeks before deploying it and adjust any false positives.| You'll be able to reduce false positives and allow legitimate requests for your application. |
|**Send the [host header](/azure/frontdoor/origin?pivots=front-door-standard-premium#origin-host-header) to the backend**. |The backend services should be aware of the host name so that they can create rules to accept traffic only from that host name.
| **Enable end-to-end TLS, HTTP to HTTPS redirection, and managed TLS certificates** (when applicable). <br><br> Review the [TLS best practices for Front Door](/azure/frontdoor/best-practices#23tls-best-practices). <br><br> Use TLS v1.2 as the minimum allowed version with ciphers that are relevant for your application. <br><br>  Use your own certificates in [Front Door custom domain](/azure/frontdoor/standard-premium/how-to-configure-https-custom-domain) endpoints and store them in Azure Key Vault.|TLS ensures that data exchange between browser, Front Door, and backend origins is encrypted to prevent tampering. <br><br> Key Vault offers managed certificate support and simpler certificate renewal and rotation.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design.

##### Design checklist

Start your design strategy based on the [**design review checklist for Cost Optimization**](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Review Front Door tiers and pricing**. Azure Front Door offers three tiers: Classic, Standard, and Premium. Use the [pricing calculator](https://azure.microsoft.com/pricing/calculator) to estimate realistic costs. [Compare the features](/azure/frontdoor/front-door-cdn-comparison) and suitability of each tier for your scenario. For instance, only the Premium tier supports Private Link to the origin.
>
> - **Consider bandwidth costs**. The bandwidth costs of Azure Front Door depend on the tier you choose and the type of data transfer. It povides built-in reports for billable metrics. Refer to [Azure Front Door reports](/azure/frontdoor/standard-premium/how-to-reports) to assess your costs related to bandwidth and where you can focus your optimization efforts.
>
> - **Optimize on incoming requests**. Azure Front Door bills the incoming requests. You can set restrictions through design choices. 
>    
>   You can lower the number of requests by using design patterns, such as [Backend for Frontends](/azure/architecture/patterns/backends-for-frontends) and [Gateway Aggregation](/azure/architecture/patterns/gateway-aggregation). These patterns can improve the efficiency of your operations. 
>
>   Web application firewall (WAF) rules restrict incoming traffic, which can optimize costs. For example, you can use rate limiting to prevent abnormally high levels of traffic. You can also use geo-filtering to allow access from specific regions or countries.
>
> - **Use resources efficiently**. Front Door's routing method can help with resource optimization. To most effectively use deployed resources, distribute  traffic evenly across all environments, unless the workload is extremely latency sensitive. 
>
>   Front Door endpoints can serve many files. Using compression is a way to reduce bandwith costs. 

##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| [**Use caching**](/azure/frontdoor/front-door-caching?pivots=front-door-standard-premium) for your endpoints that support it. | Caching optimizes data transfer costs because it reduces the number of calls from your Azure Front Door instance to the origin.|
| [**Consider enabling file compression**](/azure/frontdoor/standard-premium/how-to-compression). <br>For this configuration, the application must support compression and caching must be enabled, which is required for file compression.| Compression reduces bandwidth consumption and improves performance.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [**design review checklist for Operational Excellence**](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Front Door.

> [!div class="checklist"]
>
> - **Use Infrastructure as Code(IaC)** technologies such as [Bicep, ARM templates](/azure/frontdoor/front-door-quickstart-template-samples), to provision the Front Door instance. Those declarative approaches provide consistency and easier maintenance. For example, adopt new ruleset versions easily.
>   Always use the latest API version.
>
> - **Simplify configurations**. Take advantage of Front Door capabilites to make configurations easy to manage. For example, use Front Door supports [redirection capabilities](/azure/frontdoor/front-door-url-redirect). Suppose your architecture supports microservices, you can use path-based redirection to target individual services. 
>
>   Another use case is configuration of wildcard domains. 
>
> - **Handle progressive exposure** by using [Front Door routing methods](/azure/frontdoor/routing-methods). 
>
>   A [weighted load balancing approach](/azure/frontdoor/routing-methods#weighted-traffic-routing-method) enables canary deployments in which a certain percentage of traffic is sent to a specific backend. This approach can help you test new features and releases in a controlled environment before rolling them out to your entire user base.
>
> - **Collect and analyze Front Door operational data** as part of your workload monitoring. Capture relevant Front Door logs and metrics with Azure Monitor and Azure Log Analytics. This data helps you troubleshoot, understand user behaviors, and optimize operations. 
>
> - **Offload certificate management to Azure** to ease operation burden associated with certification rotation and renewals.


##### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
| **[Use HTTP to HTTPS redirection](/azure/frontdoor/front-door-url-redirect#redirection-protocol)** to support forward compability.|Clients using older protocol will be automatically redirected by Front Door to use HTTPS for a secure experience.|
|**[Capture logs and metrics](/azure/frontdoor/front-door-diagnostics)**. <br><br> Include resource activity logs, access logs, health probe logs, and WAF logs. <br><br>[Set up alerts](/azure/frontdoor/standard-premium/how-to-monitor-metrics#configure-alerts-in-the-azure-portal).|Monitoring ingress flow is a crucial part of monitoring the your application. You want to track requests and make performance and security improvements. This data is also needed to debug your Front Door configuration. <br><br> With alerts in place, you can get instant notifications of any critical operational issues.|
|**Review the [built-in analytics reports](/azure/frontdoor/standard-premium/how-to-reports)**.|You'll have a holistic view of your Azure Front Door profile that includes traffic and security reports through web application firewall (WAF) metrics.|
| **[Use managed TLS certificates](/azure/frontdoor/domain#azure-front-door-managed-tls-certificates)**, when possible. | With Azure Front Door, you can let it issue and manage certificates for you. This feature eliminates the need for certificate renewals and minimizes the risk of an outage due to an invalid or expired TLS certificate.|
|**[Use wildcard TLS certificates](/azure/frontdoor/front-door-wildcard-domain)**.|  You don't need to modify the configuration to add or specify each subdomain separately. |


## Performance Efficiency


## Azure policies
Azure Front Door profiles should use Premium tier that supports managed WAF rules and private link. 

Both Azure Front Door Standard and Premium should be running minimum TLS version of 1.2. There should be secure private connectivity between Azure Front Door Premium and Azure Storage Blob, or Azure App Service. 

Azure Front Door Standard or Premium (Plus WAF) should have resource logs enabled. Azure Web application firewall (WAF) on Azure Front Door should have request body inspection enabled and be enabled for Azure Front Door entry-points. 

Bot Protection should be enabled for Azure Front Door. Enable Rate Limit rule to protect against DDoS attacks on Azure Front Door. WAF should use the specified mode for Azure Front Door Service.

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Virtual Machines.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps


Consider these articles as resources that demonstrate the  recommendations highlighted in this article.

- Use these reference architectures as examples of how these recommendations can be applied to a workload.
  - Reference architecture: [Mission-critical baseline with network controls](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-network-architecture).

- Build implementation expertise using product documentation: 
  - [Azure Front Door and CDN documentation](/azure/frontdoor/)

- Azure Front Door best practices: [Best practices for Front Door](/azure/frontdoor/best-practices).

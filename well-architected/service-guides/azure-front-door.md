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

Start your design strategy based on the [**design review checklist for Reliability**](../reliability/checklist.md) and determine its relevance to your business requirements while keeping in mind the tiers and CDN capabilities. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Estimate the traffic pattern and volume**. The number of requests from client to Azure Front Door edge might influence your tier choice. If you need to support high volume of requests, consider the Azure Front Door Premium tier because performance ultimately might impact availability. However, there's a cost tradeoff. These tiers are described in [Performance Efficiency](#performance-efficiency).
>
> - **Consider a redundant traffic management option**.  Front Door is a globally distributed service that runs as a singleton in the environment. **Front Door is a potential single point of point in the system**, this means if the service fails, clients won't be able to access your application during the downtime. 
>   
>   Redundant implementations of this nature can be complex and costly. It should only be considered for mission-critical workloads that have very low tolerance to outage. Carefully consider the [tradeoffs](/azure/architecture/guide/networking/global-web-applications/overview#tradeoffs). 
>   - If you absolutely need redundant routing, see [**Global routing redundancy**](hazure/architecture/guide/networking/global-web-applications/overview).
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

#### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|Choose a [**routing method**](/azure/frontdoor/routing-methods) that supports your deployment strategy. <br><br> Active-active can be supported through the  weighted method that distributes traffic based on the configured weight coefficient. <br><br> Active-passive is supported through a priority-based value that configures primary to receive all traffic and send traffic to secondary as a backup. <br><br> Combine the preceding methods with latency so that the origin with the lowest latency receives traffic. |You'll be able to select the best origin resource through a series of decision steps and your design. The selected origin will serve traffic within the allowable latency range in the specified ratio of weights.|
|**Use [multiple backend pools](/azure/frontdoor/quickstart-create-front-door)**. <br><br>A backend pool has endpoint for all application instances deployed across multiple zones or regions.|Multiple backend pools support redundancy by distributing traffic across multiple instances. If one instance is unavailable, other backend origins can still receive traffic.|
|[**Set up health probes on the origin**](/azure/frontdoor/health-probes). <br><br>Configure Front Door to conduct health checks to determine if the backend instance is available and ready to continue to receive request.| Enabling health probes is part of  implementing the health monitoring pattern that makes sure that Front Door only routes traffic to instances that are healthy enough to handle requests. <br> For best practices, see [Best practices on health probes](/azure/frontdoor/health-probes)|
|**Set a timeout** on forwarding request to the backend. <br><br> Adjust the timeout setting according to your endpoints' needs. If you don't, Front Door might close the connection before the origin sends the response. Alternatively, you can lower Front Door's default timeout if all your origins have a shorter timeout. <br> For more information, see [Troubleshooting unresponsive requests](/azure/frontdoor/troubleshoot-issues#troubleshooting-steps).|Timeouts help avoid performance issues and availability issues by terminating requests that take longer than expected to complete.|
|**Use the same host name on Front Door and your origin**. <br><br>Azure Front Door can rewrite the host header of incoming requests, which is useful when you have multiple custom domain names that route to one origin. However, rewriting the host header might cause issues with request cookies and URL redirection.|Setting the same host name can prevent malfunction with session affinity, authentication and authorization might malfunction. For more information, see [Preserve the original HTTP host name](/azure/architecture/best-practices/host-name-preservation) between a reverse proxy and its back-end web application.|
|Evaluate if [**session affinity**](/azure/frontdoor/routing-methods#23session-affinity) is required. If you have high reliability requirements, disabling session affinity is recommended. |With session affinity, user connections stay on the same origin during the user session. If that origin becomes unavailable, the user experience might disrupted.|
|Take advantage of the [Rate limiting rules](/azure/web-application-firewall/afds/waf-front-door-rate-limit) include with Web Application Firewall (WAF).|You can limit the requests to prevent clients from sending too much traffic to your application. Rate limiting can help you avoid problems like a retry storm.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around Azure Virtual Machines.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review the security baseline** for [Azure Front Door](/security/benchmark/azure/baselines/azure-front-door-security-baseline).
>
> - **Protect the backend servers by removing direct internet access**. Use Front Door's capability to use Azure Private Link to access application origin. Private Links create segmentation and avoid the need for the backends to expose public IPs/endpoints. For more information, see [Secure your Origin with Private Link in Azure Front Door Premium](/azure/frontdoor/private-link).
>
> - **Only allow authorized access**. Use Front Door's [role-based access control (RBAC)](/azure/role-based-access-control/overview) to restrict access to only those identities that need it.
>
> - **Block common threats at edge**.  Web Application Firewall (WAF) is integrated with Front Door. Enable WAF rules on the frontends to protect applications from common exploits and vulnerabilities at the network edge closer to the attack source.
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

#### Recommendations

|Recommendation|Benefit|
|------------------------------|-----------|
|**Enable WAF rulesets** that detect and block potentially malicious traffic. These rulesets are recommended: <br><br> - [Default](/azure/web-application-firewall/afds/afds-overview#azure-managed-rule-sets)<br>- [Bot protection](/azure/web-application-firewall/afds/afds-overview#bot-protection-rule-set)<br>- [IP restriction](/azure/web-application-firewall/afds/waf-front-door-configure-ip-restriction)<br>- [Geo-filtering](/azure/web-application-firewall/afds/waf-front-door-tutorial-geo-filtering)<br>- [Rate limiting](/azure/web-application-firewall/afds/waf-front-door-rate-limit)|Default rule sets are updated frequently based on OWASP top-10 attack types and information from Microsoft Threat Intelligence. <br> The specialized rulesets can detect certain use cases. For example bot rules can classify bots as good, bad, or unknown based on the client IP addresses and also block blocks bad bots, block known IP addresses of the callers, restrict traffic based on geographical location of the callers, and others. <br> <br> By using a combination of rule sets, you'll be able to detect and block attacks with different intents.|
| **[Create exclusions](/azure/web-application-firewall/afds/waf-front-door-exclusion-configure) through managed rules**. <br><br> Test a WAF policy in Detection mode for a few weeks before deploying it and adjust any false positives.| You'll be able to reduce false positives and allow legitimate requests for your application. |
| **Enable end-to-end TLS, HTTP to HTTPS redirection, and managed TLS certificates** (when applicable). <br><br> Review the [TLS best practices for Front Door](/azure/frontdoor/best-practices#23tls-best-practices). <br><br> Use TLS v1.2 as the minimum allowed version with ciphers that are relevant for your application. <br><br>  Use your own certificates in [Front Door custom domain](/azure/frontdoor/standard-premium/how-to-configure-https-custom-domain) endpoints and store them in Azure Key Vault.|TLS ensures that data exchange between browser, Front Door, and backend origins is encrypted to prevent tampering. <br><br> Key Vault offers managed certificate support and simpler certificate renewal and rotation.|

## Cost Optimization

## Operational Excellence

## Performance Efficiency


## Azure policies


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
  - Reference architecture: [Mission-critical baseline](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-intro).

- Build implementation expertise using product documentation: 
  - [Azure Front Door and CDN documentation](/azure/frontdoor/)



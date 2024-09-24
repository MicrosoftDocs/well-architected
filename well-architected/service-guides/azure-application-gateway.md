---
title: Well-Architected Framework perspective on Azure Application Gateway v2
description: Provides architectural best practices for the Azure Application Gateway v2 family of SKUs.
author: greg-lindsay
ms.author: greglin
ms.topic: conceptual
ms.date: 09/30/2024
ms.service: azure-waf
ms.subservice: waf-service-guide
products: azure-application-gateway
azure.category:
  - networking
---

# Well-Architected Framework perspective on Azure Application Gateway v2

This article provides architectural best practices for the Azure Application Gateway v2 family of SKUs. The guidance is based on the five pillars of architectural excellence.

This article assumes that as an architect, you've reviewed the [networking options](/azure/architecture/networking/) and chose Application Gateway as the web traffic load balancer for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
>**How to use this guide**
>
>Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
>Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Application Gateway and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments. 
>
>Foundational architecture that demonstrates the key recommendations:
>[Zero Trust network for web applications with Azure Firewall and Application Gateway](/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Application Gateway

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

#### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of Application Gateway and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use Azure Application Gateway v2**. In new deployments, use Azure Application Gateway v2 unless there is a compelling reason to use Azure Application Gateway v1.
>
> - **Build redundancy in your design**. Build redundancy in the Azure Application Gateway deployment by spreading instances across availability zones to improve fault tolerance. Traffic is routed to other zones if one zone fails. For more information, see  [Recommendations for using availability zones and regions](/azure/well-architected/reliability/regions-availability-zones).
> - **Plan for rule updates**. Plan enough time for rule updates and other configuration changes before accessing Application Gateway or making further changes. For example, removing servers from backend pool might take some time because they have to drain existing connections.
> - **Implement the health endpoint monitoring pattern**. Your application should expose health endpoints, which aggregate the state of the critical services and dependencies that your application needs to serve requests. Azure Application Gateway health probes use the endpoint to detect the health of servers in the backend pool. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).
> - **Evaluate the impact of the interval and threshold settings on health probes**. The health probe sends requests to the configured endpoint at a set interval. Also, there's a threshold of failed requests that will be tolerated before the backend is marked unhealthy. These numbers present a trade-off.
>   - Setting a higher interval puts a higher load on your service. Each Application Gateway instance sends its own health probes, so 100 instances every 30 seconds means 100 requests per 30 seconds.
>   - Setting a lower interval leaves more time before an outage is detected.
>   - Setting a low unhealthy threshold might mean that short, transient failures might take down a backend.
>   - Setting a high threshold it can take longer to take a backend out of rotation.
> - **Verify downstream dependencies through health endpoints**. Suppose each backend has its own dependencies to ensure failures are isolated. For example, an application hosted behind Application Gateway might have multiple backends, each connected to a different database (replica). When such a dependency fails, the application might be working but won't return valid results. For that reason, the health endpoint should ideally validate all dependencies. 
>   Keep in mind that if each call to the health endpoint has a direct dependency call, that database would receive 100 queries every 30 seconds instead of 1. To avoid this, the health endpoint should cache the state of the dependencies for a short period of time.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Deploy the instances in a [zone-aware configuration](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).<br><br>Check regional support for zone redundancy because not all regions offer this feature.|Your workload can withstand failures in a single zone when multiple instances are spread across zones. Traffic automatically shifts to healthy instances in other zones and maintains application reliability if one zone is unavailable.|
| Use [Application Gateway health probes](/azure/application-gateway/application-gateway-probe-overview) to detect backend unavailability | This will ensure that traffic is not routed to backends that are unable to handle the traffic. Azure Application Gateway monitors the health of all the servers in its backend pool and automatically stops sending traffic to any server it considers unhealthy  |
| Configure [rate-limiting rules](/azure/web-application-firewall/ag/rate-limiting-configure) available web application firewall (WAF) to limit clients from sending too much traffic to your application.| Rate limiting can help you avoid problems like a retry storm.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Gateway.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - **Review the security baseline for [Azure Application Gateway](/security/benchmark/azure/baselines/azure-web-application-firewall-security-baseline).**
>
> - **Block common threats at the edge**. WAF is integrated with Azure Application Gateway. Enable WAF rules on the front ends to protect applications from common exploits and vulnerabilities at the network edge, closer to the attack source. For more information, see [Azure Web Application Firewall on Azure Application Gateway](/azure/web-application-firewall/afds/afds-overview).
>
>     Be aware of Application Gateway capacity changes when enabling WAF. When WAF is enabled, every request must be buffered by the Application Gateway until it fully arrives, checks if the request matches with any rule violation in its core rule set, and then forwards the packet to the backend instances. When there are large file uploads (30MB+ in size), it can result in a significant latency. Because Application Gateway capacity requirements are different with WAF, we do not recommend enabling WAF on Application Gateway without proper testing and validation.
>
>     When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door. Certain scenarios can force you to implement rules specifically on Application Gateway. For example, if ModSec CRS 2.2.9, CRS 3.0 or CRS 3.1 rules are required, these rules can be only implemented on Application Gateway. Conversely, rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway.
> - **Allow only authorized access to the control plane**. Use Azure Application Gateway [role-based access control (RBAC)](/azure/role-based-access-control/overview) to restrict access to only the identities that need it.
> - **Protect data in transit**. Enable end-to-end Transport Layer Security (TLS), TLS termination, and End-to-end TLS encryption. When re-encrypting backend traffic, ensure the backend server certificate contains both the root and intermediate Certificate Authorities (CAs).
>
>     A TLS certificate of the backend server must be issued by a well-known CA. If the certificate was not issued by a trusted CA, the Application Gateway checks if the certificate was issued by a trusted CA, and so on, until a trusted CA certificate is found. Only then a secure connection is established. Otherwise, Application Gateway marks the backend as unhealthy.
> - **Protect application secrets**. Use Azure Key Vault to store TLS certificates for increased security and easier certificate renewal and rotation process.
> - **Reduce the attack surface and harden configuration**: Remove default configurations that you don't need and harden your Azure Application Gateway configuration to tighten security controls. Use an appropriate DNS server for backend pool resources. Comply with all NSG restrictions for Application Gateway.
>
>     Use an appropriate DNS server for backend pool resources. When the backend pool contains a resolvable FQDN, the DNS resolution is based on a private DNS zone or custom DNS server (if configured on the VNet), or it uses the default Azure-provided DNS.
> - **Monitor anomalous activity**. Regularly review the logs to check for attacks and false positives. Send [WAF logs from Azure Application Gateway](/azure/web-application-firewall/ag/application-gateway-waf-metrics) to your organization's centralized security information and event management (SIEM), such as Microsoft Sentinel, to detect threat patterns and incorporate preventative measures in the workload design.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Set up a [TLS policy](/azure/application-gateway/application-gateway-ssl-policy-overview#predefined-tls-policy) for enhanced security. Ensure you're always using the latest TLS policy version available.|  The TLS policy includes control of the TLS protocol version as well as the cipher suites and the order in which ciphers are used during a TLS handshake. Using the latest TLS policy enforces the use of TLS 1.2 and stronger ciphers. |
| Use AppGateway for [TLS termination](/azure/application-gateway/ssl-overview) | There are advantages of using Application Gateway for TLS termination:<br><br>- Performance improves because requests going to different backends don't have to re-authenticate to each backend.<br>- Intelligent routing by accessing the request content.<br>- Easier certificate management because the certificate only needs to be installed on Application Gateway. |
|Use [Application Gateway integrated with Key Vault](/azure/application-gateway/key-vault-certs) to store TLS certificates.| This provides stronger security, easier separation of roles and responsibilities, support for managed certificates, and an easier certificate renewal and rotation process.|
|Comply with all [Network security groups](/azure/application-gateway/configuration-infrastructure#network-security-groups) restrictions for Application Gateway|NSGs are supported on Application Gateway subnet, but there are some restrictions. For instance, some communication with certain port ranges is prohibited. Make sure you understand the implications of those restrictions.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Gateway and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - Familiarize yourself with Application Gateway pricing
> - Review underutilized resources
> - Stop Application Gateway instances that are not in use
> - Have a scale-in and scale-out policy
> - Review consumption metrics across different parameters 

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Familiarize yourself with Application Gateway pricing | For information about Application Gateway pricing, see [Understanding Pricing for Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) and Web Application Firewall. You can also leverage the [Pricing calculator](https://azure.microsoft.com/pricing/calculator/).<br><br>Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.  |
| Review underutilized resources | Identify and delete Application Gateway instances with empty backend pools to avoid unnecessary costs. |
| Stop Application Gateway instances when not in use | You aren't billed when Application Gateway is in the stopped state. Continuously running Application Gateway instances can incur extraneous costs. Evaluate usage patterns and stop instances when you don't need them. For example, usage after business hours in Dev/Test environments is expected to be low.<br><br>See these articles for information about how to stop and start instances.<br>- [Stop-AzApplicationGateway](/powershell/module/az.network/stop-azapplicationgateway?view=azps-6.0.0&viewFallbackFrom=azps-5.2.0&preserve-view=true)<br>- [Start-AzApplicationGateway](/powershell/module/az.network/start-azapplicationgateway?view=azps-5.2.0&preserve-view=true) |
| Have a scale-in and scale-out policy | A scale-out policy ensures that there will be enough instances to handle incoming traffic and spikes. Also, have a scale-in policy that makes sure the number of instances are reduced when demand drops. Consider the choice of instance size. The size can significantly impact the cost. Some considerations are described in the Estimate the Application Gateway instance count.<br><br>For more information, see [What is Azure Application Gateway v2?](/azure/application-gateway/overview-v2#pricing.) |
| Review consumption metrics across different parameters | You're billed based on metered instances of Application Gateway based on the metrics tracked by Azure. Evaluate the various metrics and capacity units and determine the cost drivers. For more information, see [Microsoft Cost Management and Billing](https://azure.microsoft.com/services/cost-management/#overview).<br><br> The following metrics are key for Application Gateway. This information can be used to validate that the provisioned instance count matches the amount of incoming traffic.<br><br>- Estimated Billed Capacity Units<br>- Fixed Billable Capacity Units<br>- Current Capacity Units<br><br>For more information, see [Application Gateway metrics](/azure/application-gateway/application-gateway-metrics#application-gateway-metrics).<br><br>Make sure you account for bandwidth costs.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Gateway.

> [!div class="checklist"]
> - Monitor capacity metrics
> - Enable diagnostics on Application Gateway and Web Application Firewall (WAF)
> - Use Azure Monitor Network Insights
> - Match timeout settings with the backend application
> - Monitor Key Vault configuration issues using Azure Advisor
> - Configure and monitor SNAT port limitations 
> - Consider SNAT port limitations in your design
> - Refrain from using UDRs on the Application Gateway subnet

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Monitor capacity metrics | Use these metrics as indicators of utilization of the provisioned Application Gateway capacity. We strongly recommend setting up alerts on capacity. For details, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support). |
| Troubleshoot using metrics | There are other metrics that can indicate issues either at Application Gateway or the backend. We recommend evaluating the following alerts:<br><br>- Unhealthy Host Count<br>- Response Status (dimension 4xx and 5xx)<br>- Backend Response Status (dimension 4xx and 5xx)<br>- Backend Last Byte Response Time<br>- Application Gateway Total Time<br><br>For more information, see [Metrics for Application Gateway](/azure/application-gateway/application-gateway-metrics). |
| Enable diagnostics on Application Gateway and Web Application Firewall (WAF) | Diagnostic logs allow you to view firewall logs, performance logs, and access logs. Use these logs to manage and troubleshoot issues with Application Gateway instances. For more information, see [Back-end health and diagnostic logs for Application Gateway](/azure/application-gateway/application-gateway-diagnostics).|
| Use Azure Monitor Network Insights | Azure Monitor Network Insights provides a comprehensive view of health and metrics for network resources, including Application Gateway. For additional details and supported capabilities for Application Gateway, see [Azure Monitor Network insights](/azure/azure-monitor/insights/network-insights-overview). |
| Match timeout settings with the backend application | Ensure you have configured the IdleTimeout settings to match the listener and traffic characteristics of the backend application. The default value is set to four minutes and can be configured to a maximum of 30. For more information, see [Load Balancer TCP Reset and Idle Timeout](/azure/load-balancer/load-balancer-tcp-reset).<br><br>For workload considerations, see [Monitoring application health for reliability](/azure/architecture/framework/resiliency/monitoring). |
|Monitor Key Vault configuration issues using Azure Advisor|Application Gateway checks for the renewed certificate version in the linked Key Vault at every 4-hour interval. If it is inaccessible due to any incorrect Key Vault configuration, it logs that error and pushes a corresponding Advisor recommendation. You must configure the Advisor alerts to stay updated and fix such issues immediately to avoid any Control or Data plane related problems. For more information, see [Investigating and resolving key vault errors](/azure/application-gateway/key-vault-certs#investigating-and-resolving-key-vault-errors). To set an alert for this specific case, use the Recommendation Type as **Resolve Azure Key Vault issue for your Application Gateway**.|
|Consider SNAT port limitations in your design|SNAT port limitations are important for backend connections on the Application Gateway. There are separate factors that affect how Application Gateway reaches the SNAT port limit. For example, if the backend is a public IP address, it will require its own SNAT port. In order to avoid SNAT port limitations, you can increase the number of instances per Application Gateway, scale out the backends to have more IP addresses, or move your backends into the same virtual network and use private IP addresses for the backends.<br><br>Requests per second (RPS) on the Application Gateway will be affected if the SNAT port limit is reached. For example, if an Application Gateway reaches the SNAT port limit, then it won't be able to open a new connection to the backend, and the request will fail.|
|Refrain from using UDRs on the Application gateway subnet|Using User Defined Routes (UDR) on the Application Gateway subnet can cause some issues. [Health status in the back-end](/azure/application-gateway/application-gateway-diagnostics#back-end-health) might be unknown. Application Gateway logs and metrics might not get generated. We recommend that you don't use UDRs on the Application Gateway subnet so that you can view the back-end health, logs, and metrics. If your organizations require to use UDR in the Application Gateway subnet, please ensure you review the supported scenarios. For more information, see [Supported user-defined routes](/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes).|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

> [!div class="checklist"]
> - Estimate the Application Gateway instance count
> - Define the maximum instance count
> - Define the minimum instance count
> - Define Application Gateway subnet size
> - Take advantage of Application Gateway V2 features for autoscaling and performance benefits

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Estimate the Application Gateway instance count | Application Gateway v2 scales out based on many aspects, such as CPU, network throughput, current connections, and more. To determine the approximate instance count, factor in these metrics:<br><br>Current compute units — Indicates CPU utilization. 1 Application Gateway instance is approximately 10 compute units.<br>Throughput — Application Gateway instance can serve ~500 Mbps of throughput. This data depends on the type of payload.<br><br>Consider this equation when calculating instance counts.<br>![Approximate instance count](./_images/autoscale-instance.svg)<br><br>After you've estimated the instance count, compare that value to the maximum instance count. This will indicate how close you are to the maximum available capacity.
| Define the minimum instance count | For Application Gateway v2 SKU, autoscaling takes some time (approximately six to seven minutes) before the additional set of instances is ready to serve traffic. During that time, if there are short spikes in traffic, expect transient latency or loss of traffic.<br><br>We recommend that you set your minimum instance count to an optimal level. After you estimate the average instance count and determine your Application Gateway autoscaling trends, define the minimum instance count based on your application patterns. For information, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support).<br><br>Check the Current Compute Units for the past one month. This metric represents the gateway's CPU utilization. To define the minimum instance count, divide the peak usage by 10. For example, if your average Current Compute Units in the past month is 50, set the minimum instance count to five. |
| Define the maximum instance count | We recommend 125 as the maximum autoscale instance count. Make sure the subnet that has the Application Gateway has sufficient available IP addresses to support the scale-up set of instances.<br><br>Setting the maximum instance count to 125 has no cost implications because you're billed only for the consumed capacity. |
| Define Application Gateway subnet size | Application Gateway needs a dedicated subnet within a virtual network. The subnet can have multiple instances of the deployed Application Gateway resource. You can also deploy other Application Gateway resources in that subnet, v1 or v2 SKU.<br><br>Here are some considerations for defining the subnet size:<br><br>- Application Gateway uses one private IP address per instance and another private IP address if a private front-end IP is configured.<br>- Azure reserves five IP addresses in each subnet for internal use.<br>- Application Gateway (Standard or WAF SKU) can support up to 32 instances. Taking 32 instance IP addresses + 1 private front-end IP + 5 Azure reserved, a minimum subnet size of /26 is recommended. Because the Standard_v2 or WAF_v2 SKU can support up to 125 instances, using the same calculation, a subnet size of /24 is recommended.<br>- If you want to deploy additional Application Gateway resources in the same subnet, consider the additional IP addresses that will be required for their maximum instance count for both, Standard and Standard v2. |
| Take advantage of features for autoscaling and performance benefits | The v2 SKU offers autoscaling to ensure that your Application Gateway can scale up as traffic increases. When compared to v1 SKU, v2 has capabilities that enhance the performance of the workload. For example, better TLS offload performance, quicker deployment and update times, zone redundancy, and more. For more information about autoscaling features, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).<br><br>If you are running v1 SKU Application gateway, consider migrating to the Application gateway v2 SKU. For more information, see [Migrate Azure Application Gateway and Web Application Firewall from v1 to v2](/azure/application-gateway/migrate-v1-v2). |

## Azure policies

Azure provides an extensive set of built-in policies related to App Service and its dependencies. A set of Azure policies can audit some of the preceding recommendations. For example, you can check whether:

- [Web Application Firewall (WAF) should be enabled for Application Gateway](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66). Deploy Azure Web Application Firewall (WAF) in front of public facing web applications for additional inspection of incoming traffic. Web Application Firewall (WAF) provides centralized protection of your web applications from common exploits and vulnerabilities such as SQL injections, Cross-Site Scripting, local and remote file executions. You can also restrict access to your web applications by countries/regions, IP address ranges, and other http(s) parameters via custom rules.

- [Web Application Firewall (WAF) should use the specified mode for Application Gateway](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12430be1-6cc8-4527-a9a8-e3d38f250096). Mandates the use of 'Detection' or 'Prevention' mode to be active on all Web Application Firewall policies for Application Gateway.
- [Azure DDoS Protection should be enabled](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd). DDoS protection should be enabled for all virtual networks with a subnet that is part of an application gateway with a public IP.

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/app-service/policy-reference) and other policies that might affect networking.
 
## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Application Gateway.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations#ensure-application-gateway-fault-tolerance)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations#[offering-or-infrastructure-area-anchor])
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations#[offering-or-infrastructure-area-anchor])
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#[offering-or-infrastructure-area-anchor])
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#[offering-or-infrastructure-area-anchor])

## Next steps

- [Using API gateways in microservices](/azure/architecture/microservices/design/gateway)
- [Firewall and Application Gateway for virtual networks](/azure/architecture/example-scenario/gateway/firewall-application-gateway)
- [Protect APIs with Application Gateway and API Management](/azure/architecture/reference-architectures/apis/protect-apis)
- [IaaS: Web application with relational database](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)
- [Securely managed web applications](/azure/architecture/example-scenario/apps/fully-managed-secure-apps)
- [Zero-trust network for web applications with Azure Firewall and Application Gateway](/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall)
- [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](/azure/application-gateway/quick-create-portal)

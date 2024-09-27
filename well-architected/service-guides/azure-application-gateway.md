---
title: Well-Architected Framework perspective on Azure Application Gateway v2
description: Learn about architectural best practices for the Azure Application Gateway v2 family of SKUs.
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

Azure Application Gateway v2 is a web traffic load balancer that operates at the application layer. Application Gateway manages traffic to your web applications based on the attributes of an HTTP request. Use Application Gateway for scenarios that have advanced routing capabilities and require enhanced security and scalability. 

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
>[Baseline highly available, zone-redundant web application architecture](/azure/architecture/web-apps/app-service/architectures/baseline-zone-redundant).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:  

- Application Gateway v2
- Web Application Firewall (WAF) on Application Gateway

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures.**

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

#### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of Application Gateway and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Use Application Gateway v2** in new deployments unless your workload specifically requires Application Gateway v1.
>
> - **Build redundancy in your design.** Spread Application Gateway instances across availability zones to improve fault tolerance and build redundancy. Traffic routes to other zones if one zone fails. For more information, see [Recommendations for using availability zones and regions](/azure/well-architected/reliability/regions-availability-zones).
> - **Plan for rule updates.** Plan extra time for rule updates and other configuration changes before you access Application Gateway or make further changes. For example, you might need extra time to remove servers from a back-end pool because they have to drain existing connections.
> - **Implement the Health Endpoint Monitoring pattern.** Your application should expose health endpoints, which aggregate the state of the critical services and dependencies that your application needs to serve requests. Application Gateway health probes use the endpoint to detect the health of servers in the back-end pool. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).
> - **Evaluate the impact of interval and threshold settings on health probes.** The health probe sends requests to the configured endpoint at a set interval. The back end tolerates a limited number of failed requests before it's marked as unhealthy. These settings can conflict, which presents a tradeoff.
>   - A higher interval puts a higher load on your service. Each Application Gateway instance sends its own health probes, so 100 instances every 30 seconds equals 100 requests every 30 seconds.
>
>   - A lower interval increases the amount of time before the health probe detects an outage.
>   - A low, unhealthy threshold increases the chance of short, transient failures shutting down a back end.
>   - A high threshold increases the amount of time that it takes a back end to go out of rotation.
> - **Verify downstream dependencies through health endpoints.** Suppose each back end has its own dependencies to ensure that failures are isolated. For example, an application that you host behind Application Gateway might have multiple back ends, and each back end connects to a different database, or replica. When such a dependency fails, the application might work but doesn't return valid results. For that reason, the health endpoint should ideally validate all dependencies.
>   Keep in mind that if each call to the health endpoint has a direct dependency call, that database receives 100 queries every 30 seconds instead of 1 query. To avoid excessive queries, the health endpoint should cache the state of the dependencies for a short period of time.
> - **Consider Application Gateway limitations and known issues that may affect reliability**. Review the [Application Gateway FAQ](/azure/application-gateway/application-gateway-faq) documenting  important information about by-design behavior, fixes under construction, platform limitations, and possible workarounds or mitigation strategies. Avoid using UDRs in the Application Gateway dedicated subnet.  
> - **Consider SNAT port limitations in your design**. SNAT port limitations are important for backend connections on the Application Gateway. There are separate factors that affect how Application Gateway reaches the SNAT port limit. For example, if the backend is a public IP address, it will require its own SNAT port. In order to avoid SNAT port limitations, you can increase the number of instances per Application Gateway, scale out the backends to have more IP addresses, or move your backends into the same virtual network and use private IP addresses for the backends.
>
>     Requests per second (RPS) on the Application Gateway will be affected if the SNAT port limit is reached. For example, if an Application Gateway reaches the SNAT port limit, then it won't be able to open a new connection to the backend, and the request will fail.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Deploy Application Gateway instances in a [zone-aware configuration](/azure/application-gateway/application-gateway-autoscaling-zone-redundant). <br><br> Check regional support for zone redundancy because not all regions offer this feature. | Your workload can withstand failures in a single zone when you spread multiple instances across zones. If you have an unavailable zone, traffic automatically shifts to healthy instances in other zones, which maintains application reliability. |
| Use [Application Gateway health probes](/azure/application-gateway/application-gateway-probe-overview) to detect back-end unavailability. | Health probes ensure that traffic only routes to back ends that can handle the traffic. Application Gateway monitors the health of all the servers in its back-end pool and automatically stops sending traffic to any server that it considers unhealthy.  |
| Configure [rate-limiting rules](/azure/web-application-firewall/ag/rate-limiting-configure) for Azure Web Application Firewall (WAF) so that clients can't send too much traffic to your application. | Use rate limiting to avoid problems like retry storms. |
| Refrain from using UDRs on the Application gateway to enable proper functionality of the [Backend health report](/azure/application-gateway/application-gateway-backend-health) and the correct generation of Application Gateway logs and metrics.<br><br>If your organizations require to use UDR in the Application Gateway subnet, please review the supported scenarios. For more information, see [Supported user-defined routes](/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes).|Using User Defined Routes (UDR) on the Application Gateway subnet can cause some issues.  We recommend that you don't use UDRs on the Application Gateway subnet so that you can view the back-end health, logs, and metrics.|
| Configure the IdleTimeout settings to match the listener and traffic characteristics of the backend application. The default value is set to four minutes and can be configured to a maximum of 30.<br><br>For more information, see [Load Balancer TCP Reset and Idle Timeout](/azure/load-balancer/load-balancer-tcp-reset).| Setting the IdleTimeout to match the backend will ensure the connection between Application Gateway and the client stays open in the case the back-end takes over four minutes to respond to the request, otherwise the connection is closed, and the client never sees the backend response.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Application Gateway.

### Design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - **Review the security baseline for [Application Gateway](/security/benchmark/azure/baselines/azure-web-application-firewall-security-baseline).**
>
> - **Block common threats at the edge.** WAF integrates with Application Gateway. Enable WAF rules on the front ends to protect applications from common exploits and vulnerabilities at the network edge, which is close to the attack source. For more information, see [WAF on Application Gateway](/azure/web-application-firewall/afds/afds-overview).
>
>     Understand how WAF affects Application Gateway capacity changes. When you enable WAF, Application Gateway:
>   - Buffers every request until it fully arrives.
>   - Checks if the request matches with any rule violation in its core rule set.
>   - Forwards the packet to the back-end instances.
>
>   Large file uploads that are 30 MB or more can introduce significant latency. Application Gateway capacity requirements change when you enable WAF, so we recommend that you properly test and validate this method first.
>
>     When you use Azure Front Door and Application Gateway to protect HTTP or HTTPS applications, use WAF policies in Azure Front Door and lock down Application Gateway to receive traffic only from Azure Front Door. Certain scenarios can force you to implement rules specifically on Application Gateway. For example, if you require ModSec CRS 2.2.9, CRS 3.0, or CRS 3.1 rules, you can only implement these rules on Application Gateway. Conversely, Azure Front Door supports rate-limiting and geo-filtering. Application Gateway doesn't support these features.
> - **Allow only authorized access to the control plane.** Use Application Gateway [role-based access control (RBAC)](/azure/role-based-access-control/overview) to restrict access to only the identities that need it.
> - **Protect data in transit.** Enable end-to-end Transport Layer Security (TLS), TLS termination, and end-to-end TLS encryption. When you re-encrypt back-end traffic, ensure that the back-end server certificate contains both the root and intermediate certificate authorities (CAs).
>
>     Use a well-known CA to issue a TLS certificate of the back-end server. If you don't use a trusted CA to issue the certificate, Application Gateway checks until it finds a trusted CA certificate. It establishes a secure connection only when it finds a trusted CA. Otherwise, Application Gateway marks the back end as unhealthy.
> - **Protect application secrets.** Use Azure Key Vault to store TLS certificates for increased security and an easier certificate renewal and rotation process.
> - **Reduce the attack surface and harden the configuration.** Remove default configurations that you don't need, and harden your Application Gateway configuration to tighten security controls. Comply with all network security group (NSG) restrictions for Application Gateway.
>
>     Use an appropriate Domain Name System (DNS) server for back-end pool resources. When the back-end pool contains a resolvable fully qualified domain name (FQDN), the DNS resolution is based on a private DNS zone or custom DNS server (if configured on the virtual network), or it uses the default Azure-provided DNS.
> - **Monitor anomalous activity.** Regularly review logs to check for attacks and false positives. Send [WAF logs from Application Gateway](/azure/web-application-firewall/ag/application-gateway-waf-metrics) to your organization's centralized security information and event management (SIEM), such as Microsoft Sentinel, to detect threat patterns and incorporate preventative measures in the workload design.

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Set up a [TLS policy](/azure/application-gateway/application-gateway-ssl-policy-overview#predefined-tls-policy) for enhanced security. Ensure that you use the latest TLS policy version.|  The TLS policy includes control of the TLS protocol version and the cipher suites and also the order in which a TLS handshake uses ciphers. Use the latest TLS policy to enforce the use of TLS 1.2 and stronger ciphers. |
| Use Application Gateway for [TLS termination](/azure/application-gateway/ssl-overview). | Application Gateway for TLS termination provides the following advantages:<br><br>- Performance improves because requests that go to different back ends don't have to reauthenticate to each back end.<br>- Intelligent routing by accessing the request content.<br>- Easy certificate management because you only need to install the certificate on Application Gateway. |
|Use [Application Gateway integrated with Key Vault](/azure/application-gateway/key-vault-certs) to store TLS certificates.| This approach provides stronger security, easier separation of roles and responsibilities, support for managed certificates, and an easier certificate renewal and rotation process.|
|Comply with all [NSG](/azure/application-gateway/configuration-infrastructure#network-security-groups) restrictions for Application Gateway. |The Application Gateway subnet supports NSGs, but there are some restrictions. For instance, some communication with certain port ranges is prohibited. Make sure you understand the implications of those restrictions.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Application Gateway and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - **Familiarize yourself with [Application Gateway and WAF pricing](https://azure.microsoft.com/pricing/details/application-gateway/).** Choose appropriately sized options to meet your workload capacity demand and deliver expected performance without wasting resources. You can also use the [pricing calculator](https://azure.microsoft.com/pricing/calculator/).
>
> - **Remove unused Application Gateway instances, and optimize underused instances.** Identify and delete Application Gateway instances that have empty back-end pools to avoid unnecessary costs. Stop Application Gateway instances when they're not in use.
> - **Optimize the scaling cost of your Application Gateway instance.** To optimize your scaling strategy and reduce your wokload's demands, see [Recommendations for optimizing scaling cost](/azure/well-architected/cost-optimization/optimize-scaling-costs).
>
>     To scale the service in or out based on application traffic requirements, use [autoscaling in Application Gateway v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).
> - **Monitor Application Gateway consumption metrics** and understand their cost impact. Azure charges for metered instances of Application Gateway based on tracked metrics. Evaluate the various metrics and capacity units, and determine the cost drivers. For more information, see [Microsoft Cost Management and Billing](https://azure.microsoft.com/services/cost-management/#overview).

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Stop Application Gateway instances when they're not in use. For mroe information, see:<br><br>- [Stop-AzApplicationGateway](/powershell/module/az.network/stop-azapplicationgateway)<br>- [Start-AzApplicationGateway](/powershell/module/az.network/start-azapplicationgateway) | A stopped Application Gateway instance doesn't incur costs. Application Gateway instances that continuously run can incur unnecessary costs. Evaluate usage patterns, and stop instances when you don't need them. For example, expect low usage after business hours in dev/test environments.|
| Monitor key cost driver [Application Gateway metrics](/azure/application-gateway/application-gateway-metrics#application-gateway-metrics) like: <br><br>- Estimated billed capacity units. <br> - Fixed billable capacity units. <br>- Current capacity units. <br><br> Make sure you account for bandwidth costs. | Use these metrics to validate whether the provisioned instance count matches the amount of incoming traffic and ensure you are fully utilizing the allocated resources.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management.**

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Application Gateway.

> [!div class="checklist"]
> - **Enable diagnostics on Application Gateway and Web Application Firewall (WAF)**. Collect logs and metrics to enable monitoring the health of the workload, identify trends in the workload performance and reliability, and troublshoot issues. Follow the guidance in [Recommendations for designing and creating a monitoring system](well-architected/operational-excellence/observability) to design your overall monitoring approach.
>
>    [needs edits]
>
>    - Use capacity metrics and set alerts on capacity metrics as indicators of utilization of the provisioned Application Gateway capacity
>    - Set alerts on metrics that can indicate issues either at Application Gateway or the backend
>    - Use logs to manage and troubleshoot issues with Application Gateway instances
> - **Use Azure Monitor Network Insights**. [Azure Monitor Network insights](/azure/azure-monitor/insights/network-insights-overview) provides a comprehensive view of health and metrics for network resources, including Application Gateway. This centralized monitoring helps you quickly identify and resolve issues, optimize performance, and ensure the reliability of your applications.
> - **Monitor Application Gateway recommendations in Azure Advisor** and configure alerts to notify your team when new critical recommendations are available for your Azure Application Gateway instance. 
>
>     [needs edits]
>    - An Azure advisor recommendation alert can be based on the recommendation's properties: Category, Impact Level, and Recommendation type

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Configure alerts to notify your team when capacity metrics, like CPU usage and Compute Unit utilization, cross recommended thresholds.<br><br>Follow the guidance in [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support#alerts-for-application-gateway-v2-sku-standard_v2waf_v2) to set a comprehensive set of alerts based on capacity metrics.| Setting alerts when metrics cross thresholds indicating that your usage is increasing give you enough time to implement the necessary changes to your workload to avoid degradation or outages.|
| Configure alerts to notify your team on metrics that indicate issues either at Application Gateway or the backend. We recommend evaluating the following alerts:<br><br>- Unhealthy Host Count<br>- Response Status (dimension 4xx and 5xx)<br>- Backend Response Status (dimension 4xx and 5xx)<br>- Backend Last Byte Response Time<br>- Application Gateway Total Time<br><br>For more information, see [Metrics for Application Gateway](/azure/application-gateway/application-gateway-metrics).| These alerts will help ensure your team can respond to issues in a timely manner and facilitate troubleshooting.|
| Enable [diagnostics logs](/azure/application-gateway/application-gateway-diagnostics) on Application Gateway and Web Application Firewall (WAF) to collect firewall logs, performance logs, and access logs.| These logs are useful for detecting and investigating anomalies. Use them logs troubleshoot issues with Application Gateway instances and your workload.|
|Monitor [Key Vault configuration issues using Azure Advisor](/azure/application-gateway/key-vault-certs#investigating-and-resolving-key-vault-errors) and set an alert to notify your team when a recommendation of type **Resolve Azure Key Vault issue for your Application Gateway** is generated. | The Azure Advisor alerts enables you to stay updated and fix such issues immediately to avoid any Control or Data plane related problems.<br><br>Application Gateway checks for the renewed certificate version in the linked Key Vault at every 4-hour interval. If it is inaccessible due to any incorrect Key Vault configuration, it logs that error and pushes a corresponding Advisor recommendation.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

> [!div class="checklist"]
> - **Estimate capacity requirements for Azure Application Gateway to support your workload requirements**. Take advantage of the autoscaling functionality in Azure Application Gateway v2. Set appropiate values for the minimum and maximum number of instances and ensure to appropiately size the dedicated subnet required by Application Gateway. Apply the guidance from the [Recommendations for capacity planning](/azure/well-architected/performance-efficiency/capacity-planning) guide to cover the various aspects and strategies to plan capacity.
>
>    Application Gateway v2 scales out based on many aspects, such as CPU, network throughput, current connections, and more. To determine the approximate instance count, factor in these metrics:
>    Current compute units — Indicates CPU utilization. 1 Application Gateway instance is approximately 10 compute units.
>    Throughput — Application Gateway instance can serve ~500 Mbps of throughput. This data depends on the type of payload.
>
>    Consider this equation when calculating instance counts.
>    ![Approximate instance count](./_images/autoscale-instance.svg)
>
> - **Take advantage of features for autoscaling and performance benefits** The v2 SKU offers autoscaling to ensure that your Application Gateway can scale up as traffic increases. When compared to v1 SKU, v2 has capabilities that enhance the performance of the workload. For example, better TLS offload performance, quicker deployment and update times, zone redundancy, and more. For more information about autoscaling features, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant)
>
>    If you are running v1 SKU Application gateway, consider migrating to the Application gateway v2 SKU. For more information, see [Migrate Application Gateway and Web Application Firewall from v1 to v2](/azure/application-gateway/migrate-v1-v2).

### Recommendations

| Recommendation | Benefit |
|--------|----|
| Set the [minimum instance count to an optimal level](/azure/application-gateway/high-traffic-support#set-your-minimum-instance-count-based-on-your-average-compute-unit-usage) based on you estimated instance count, actual Application Gateway autoscaling trends, and your application patterns.<br><br>Check the Current Compute Units for the past one month. This metric represents the gateway's CPU utilization. To define the minimum instance count, divide the peak usage by 10. For example, if your average Current Compute Units in the past month is 50, set the minimum instance count to five.| For Application Gateway v2 SKU, autoscaling takes some time (approximately six to seven minutes) before the additional set of instances is ready to serve traffic. During that time, if there are short spikes in traffic, expect transient latency or loss of traffic.|
| Set the [maximum autoscale instance count](/azure/application-gateway/high-traffic-support#set-maximum-instance-count-to-the-maximum-possible-125) to the maximum possible (125). Make sure the subnet that has the Application Gateway has sufficient available IP addresses to support the scale-up set of instances.| Setting the maximum instance count to 125 allows the Application Gateway to scale out as needed to handle the possible increase in traffic to your applications. This has no cost implications because you're billed only for the consumed capacity.|
| Appropiately size Application Gateway dedicated subnet. A /24 subnet is highly recommended for an Application Gateway v2 SKU deployment.<br>If you want to deploy additional Application Gateway resources in the same subnet, consider the additional IP addresses that will be required for their maximum instance count<br><br> For additional considerations for sizing the subnet, see [Application Gateway infrastructure configuration](/azure/application-gateway/configuration-infrastructure#size-of-the-subnet)  | A /24 subnet would provide supports for all IP addresses needed by your Application Gateway v2 deployment<br><br>- Application Gateway uses one private IP address per instance and another private IP address if a private front-end IP is configured.<br>- Azure reserves five IP addresses in each subnet for internal use.<br>- The Standard_v2 or WAF_v2 SKU can support up to 125 instances.|

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
- [Quickstart: Direct web traffic with Application Gateway - Azure portal](/azure/application-gateway/quick-create-portal)

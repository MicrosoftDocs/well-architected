---
title: Microsoft Azure Well-Architected Framework review - Azure Application Gateway v2
description: Provides architectural best practices for the Azure Application Gateway v2 family of SKUs.
author: greg-lindsay
ms.author: greglin
ms.topic: conceptual
ms.date: 01/27/2023
products: azure-application-gateway
---

# Azure Well-Architected Framework review - Azure Application Gateway v2

This article provides architectural best practices for the Azure Application Gateway v2 family of SKUs. The guidance is based on the five pillars of architectural excellence:

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

We assume that you have a working knowledge of Azure Application Gateway and are well-versed with v2 SKU features. For more information, see [Azure Application Gateway features](/azure/application-gateway/features).

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Protect APIs with Application Gateway and API Management](/azure/architecture/reference-architectures/apis/protect-apis) and [IaaS: Web application with relational database](/azure/architecture/high-availability/ref-arch-iaas-web-and-db).

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

### Design checklist

As you make design choices for Application Gateway, review the [Reliability design principles](../resiliency/principles.md).

> [!div class="checklist"]
> - Deploy the instances in a [zone-aware configuration](/azure/application-gateway/application-gateway-autoscaling-zone-redundant), where available.
> - Use Application Gateway with Web Application Firewall (WAF) within a virtual network to protect inbound `HTTP/S` traffic from the Internet.
> - In new deployments, use Azure Application Gateway v2 unless there is a compelling reason to use Azure Application Gateway v1.
> - Plan for rule updates
> - Use health probes to detect backend unavailability
> - Review the impact of the interval and threshold settings on health probes
> - Verify downstream dependencies through health endpoints

### Recommendations

Explore the following table of recommendations to optimize your Application Gateway configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
| Plan for rule updates | Plan enough time for updates before accessing Application Gateway or making further changes. For example, removing servers from backend pool might take some time because they have to drain existing connections. |
| Use health probes to detect backend unavailability | If Application Gateway is used to load balance incoming traffic over multiple backend instances, we recommend the use of health probes. These will ensure that traffic is not routed to backends that are unable to handle the traffic. |
| Review the impact of the interval and threshold settings on health probes | The health probe sends requests to the configured endpoint at a set interval. Also, there's a threshold of failed requests that will be tolerated before the backend is marked unhealthy. These numbers present a trade-off.<br><br>- Setting a higher interval puts a higher load on your service. Each Application Gateway instance sends its own health probes, so 100 instances every 30 seconds means 100 requests per 30 seconds.<br>- Setting a lower interval leaves more time before an outage is detected.<br>- Setting a low unhealthy threshold might mean that short, transient failures might take down a backend.<br> - Setting a high threshold it can take longer to take a backend out of rotation. |
| Verify downstream dependencies through health endpoints | Suppose each backend has its own dependencies to ensure failures are isolated. For example, an application hosted behind Application Gateway might have multiple backends, each connected to a different database (replica). When such a dependency fails, the application might be working but won't return valid results. For that reason, the health endpoint should ideally validate all dependencies. Keep in mind that if each call to the health endpoint has a direct dependency call, that database would receive 100 queries every 30 seconds instead of 1. To avoid this, the health endpoint should cache the state of the dependencies for a short period of time. |
|When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.|Certain scenarios can force you to implement rules specifically on Application Gateway. For example, if ModSec CRS 2.2.9, CRS 3.0 or CRS 3.1 rules are required, these rules can be only implemented on Application Gateway. Conversely, rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. Application Gateway provides features to employ both the principle of least privilege and defense-in-defense. We recommend you review the [Security design principles](../security/principles.md).

### Design checklist

> [!div class="checklist"]
> - Set up a TLS policy for enhanced security
> - Use AppGateway for TLS termination
> - Use Azure Key Vault to store TLS certificates
> - When re-encrypting backend traffic, ensure the backend server certificate contains both the root and intermediate Certificate Authorities (CAs)
> - Use an appropriate DNS server for backend pool resources
> - Comply with all NSG restrictions for Application Gateway
> - Refrain from using UDRs on the Application Gateway subnet
> - Be aware of Application Gateway capacity changes when enabling WAF

### Recommendations

Explore the following table of recommendations to optimize your Application Gateway configuration for Security.

| Recommendation | Benefit |
|--------|----|
| Set up a TLS policy for enhanced security |Set up a [TLS policy](/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) for extra security. Ensure you're using the latest TLS policy version (AppGwSslPolicy20170401S). This enforces TLS 1.2 and stronger ciphers. |
| Use AppGateway for TLS termination | There are advantages of using Application Gateway for TLS termination:<br><br>- Performance improves because requests going to different backends to have to re-authenticate to each backend.<br>- Better utilization of backend servers because they don't have to perform TLS processing<br>- Intelligent routing by accessing the request content.<br>- Easier certificate management because the certificate only needs to be installed on Application Gateway. |
|Use Azure Key Vault to store TLS certificates|[Application Gateway is integrated with Key Vault](/azure/application-gateway/key-vault-certs). This provides stronger security, easier separation of roles and responsibilities, support for managed certificates, and an easier certificate renewal and rotation process.|
| When re-encrypting backend traffic, ensure the backend server certificate contains both the root and intermediate Certificate Authorities (CAs) |  A TLS certificate of the backend server must be issued by a well-known CA. If the certificate was not issued by a trusted CA, the Application Gateway checks if the certificate of the issuing CA was issued by a trusted CA, and so on until either a trusted CA is found. Only then a secure connection is established. Otherwise, Application Gateway marks the backend as unhealthy. |
|Use an appropriate DNS server for backend pool resources| When the backend pool contains a resolvable FQDN, the DNS resolution is based on a private DNS zone or custom DNS server (if configured on the VNet), or it uses the default Azure-provided DNS. |
|Comply with all NSG restrictions for Application Gateway|NSGs are supported on Application Gateway subnet, but there are some restrictions. For instance, some communication with certain port ranges is prohibited. Make sure you understand the implications of those restrictions. For details, see [Network security groups](/azure/application-gateway/configuration-infrastructure#network-security-groups).|
|Refrain from using UDRs on the Application gateway subnet|Using User Defined Routes (UDR) on the Application Gateway subnet can cause some issues. [Health status in the back-end](/azure/application-gateway/application-gateway-diagnostics#back-end-health) might be unknown. Application Gateway logs and metrics might not get generated. We recommend that you don't use UDRs on the Application Gateway subnet so that you can view the back-end health, logs, and metrics. If your organizations require to use UDR in the Application Gateway subnet, please ensure you review the supported scenarios. For more information, see [Supported user-defined routes](/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes).|
|Be aware of Application Gateway capacity changes when enabling WAF|When WAF is enabled, every request must be buffered by the Application Gateway until it fully arrives and check if the request matches with any rule violation in its core rule set and then forward the packet to the backend instances. For large file uploads (30MB+ in size), this can result in a significant latency. Because Application Gateway capacity requirements are different with WAF, we do not recommend enabling WAF on Application Gateway without proper testing and validation.|

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- [Web Application Firewall (WAF) should be enabled for Application Gateway](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66). Deploy Azure Web Application Firewall (WAF) in front of public facing web applications for additional inspection of incoming traffic. Web Application Firewall (WAF) provides centralized protection of your web applications from common exploits and vulnerabilities such as SQL injections, Cross-Site Scripting, local and remote file executions. You can also restrict access to your web applications by countries/regions, IP address ranges, and other http(s) parameters via custom rules.
- [Web Application Firewall (WAF) should use the specified mode for Application Gateway](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12430be1-6cc8-4527-a9a8-e3d38f250096). Mandates the use of 'Detection' or 'Prevention' mode to be active on all Web Application Firewall policies for Application Gateway.
- [Azure DDoS Protection should be enabled](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd). DDoS protection should be enabled for all virtual networks with a subnet that is part of an application gateway with a public IP.

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](../cost-optimization/principles.md).

### Design checklist

> [!div class="checklist"]
> - Familiarize yourself with Application Gateway pricing
> - Review underutilized resources
> - Stop Application Gateway instances that are not in use
> - Have a scale-in and scale-out policy
> - Review consumption metrics across different parameters 

### Recommendations

Explore the following table of recommendations to optimize your Application Gateway configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| Familiarize yourself with Application Gateway pricing | For information about Application Gateway pricing, see [Understanding Pricing for Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) and Web Application Firewall. You can also leverage the [Pricing calculator](https://azure.microsoft.com/pricing/calculator/).<br><br>Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.  |
| Review underutilized resources | Identify and delete Application Gateway instances with empty backend pools to avoid unnecessary costs. |
| Stop Application Gateway instances when not in use | You aren't billed when Application Gateway is in the stopped state. Continuously running Application Gateway instances can incur extraneous costs. Evaluate usage patterns and stop instances when you don't need them. For example, usage after business hours in Dev/Test environments is expected to be low.<br><br>See these articles for information about how to stop and start instances.<br>- [Stop-AzApplicationGateway](/powershell/module/az.network/stop-azapplicationgateway?view=azps-6.0.0&viewFallbackFrom=azps-5.2.0&preserve-view=true)<br>- [Start-AzApplicationGateway](/powershell/module/az.network/start-azapplicationgateway?view=azps-5.2.0&preserve-view=true) |
| Have a scale-in and scale-out policy | A scale-out policy ensures that there will be enough instances to handle incoming traffic and spikes. Also, have a scale-in policy that makes sure the number of instances are reduced when demand drops. Consider the choice of instance size. The size can significantly impact the cost. Some considerations are described in the Estimate the Application Gateway instance count.<br><br>For more information, see [What is Azure Application Gateway v2?](/azure/application-gateway/overview-v2#pricing.) |
| Review consumption metrics across different parameters | You're billed based on metered instances of Application Gateway based on the metrics tracked by Azure. Evaluate the various metrics and capacity units and determine the cost drivers. For more information, see [Microsoft Cost Management and Billing](https://azure.microsoft.com/services/cost-management/#overview).<br><br> The following metrics are key for Application Gateway. This information can be used to validate that the provisioned instance count matches the amount of incoming traffic.<br><br>- Estimated Billed Capacity Units<br>- Fixed Billable Capacity Units<br>- Current Capacity Units<br><br>For more information, see [Application Gateway metrics](/azure/application-gateway/application-gateway-metrics#application-gateway-metrics).<br><br>Make sure you account for bandwidth costs.|

For more suggestions, see [Principles of the cost optimization pillar](../cost-optimization/checklist.md).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial for ensuring operational excellence of your Application Gateway and the web applications or backends behind the gateway. You can not only measure performance statistics but also use metrics to troubleshoot and remediate issues quickly. We recommend you review the [Operational Excellence design principles](../operational-excellence/principles.md).

### Design checklist

> [!div class="checklist"]
> - Monitor capacity metrics
> - Enable diagnostics on Application Gateway and Web Application Firewall (WAF)
> - Use Azure Monitor Network Insights
> - Match timeout settings with the backend application
> - Monitor Key Vault configuration issues using Azure Advisor
> - Configure and monitor SNAT port limitations 
> - Consider SNAT port limitations in your design

### Recommendations

Explore the following table of recommendations to optimize your Application Gateway configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
| Monitor capacity metrics | Use these metrics as indicators of utilization of the provisioned Application Gateway capacity. We strongly recommend setting up alerts on capacity. For details, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support). |
| Troubleshoot using metrics | There are other metrics that can indicate issues either at Application Gateway or the backend. We recommend evaluating the following alerts:<br><br>- Unhealthy Host Count<br>- Response Status (dimension 4xx and 5xx)<br>- Backend Response Status (dimension 4xx and 5xx)<br>- Backend Last Byte Response Time<br>- Application Gateway Total Time<br><br>For more information, see [Metrics for Application Gateway](/azure/application-gateway/application-gateway-metrics). |
| Enable diagnostics on Application Gateway and Web Application Firewall (WAF) | Diagnostic logs allow you to view firewall logs, performance logs, and access logs. Use these logs to manage and troubleshoot issues with Application Gateway instances. For more information, see [Back-end health and diagnostic logs for Application Gateway](/azure/application-gateway/application-gateway-diagnostics).|
| Use Azure Monitor Network Insights | Azure Monitor Network Insights provides a comprehensive view of health and metrics for network resources, including Application Gateway. For additional details and supported capabilities for Application Gateway, see [Azure Monitor Network insights](/azure/azure-monitor/insights/network-insights-overview). |
| Match timeout settings with the backend application | Ensure you have configured the IdleTimeout settings to match the listener and traffic characteristics of the backend application. The default value is set to four minutes and can be configured to a maximum of 30. For more information, see [Load Balancer TCP Reset and Idle Timeout](/azure/load-balancer/load-balancer-tcp-reset).<br><br>For workload considerations, see [Monitoring application health for reliability](/azure/architecture/framework/resiliency/monitoring). |
|Monitor Key Vault configuration issues using Azure Advisor|Application Gateway checks for the renewed certificate version in the linked Key Vault at every 4-hour interval. If it is inaccessible due to any incorrect Key Vault configuration, it logs that error and pushes a corresponding Advisor recommendation. You must configure the Advisor alerts to stay updated and fix such issues immediately to avoid any Control or Data plane related problems. For more information, see [Investigating and resolving key vault errors](/azure/application-gateway/key-vault-certs#investigating-and-resolving-key-vault-errors). To set an alert for this specific case, use the Recommendation Type as **Resolve Azure Key Vault issue for your Application Gateway**.|
|Consider SNAT port limitations in your design|SNAT port limitations are important for backend connections on the Application Gateway. There are separate factors that affect how Application Gateway reaches the SNAT port limit. For example, if the backend is a public IP address, it will require its own SNAT port. In order to avoid SNAT port limitations, you can increase the number of instances per Application Gateway, scale out the backends to have more IP addresses, or move your backends into the same virtual network and use private IP addresses for the backends.<br><br>Requests per second (RPS) on the Application Gateway will be affected if the SNAT port limit is reached. For example, if an Application Gateway reaches the SNAT port limit, then it won't be able to open a new connection to the backend, and the request will fail.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../scalability/principles.md).

### Design checklist

> [!div class="checklist"]
> - Estimate the Application Gateway instance count
> - Define the maximum instance count
> - Define the minimum instance count
> - Define Application Gateway subnet size
> - Take advantage of Application Gateway V2 features for autoscaling and performance benefits

### Recommendations

Explore the following table of recommendations to optimize your Application Gateway configuration for Performance efficiency.

| Recommendation | Benefit |
|--------|----|
| Estimate the Application Gateway instance count | Application Gateway v2 scales out based on many aspects, such as CPU, network throughput, current connections, and more. To determine the approximate instance count, factor in these metrics:<br><br>Current compute units — Indicates CPU utilization. 1 Application Gateway instance is approximately 10 compute units.<br>Throughput — Application Gateway instance can serve ~500 Mbps of throughput. This data depends on the type of payload.<br><br>Consider this equation when calculating instance counts.<br>![Approximate instance count](../_images/autoscale-instance.svg)<br><br>After you've estimated the instance count, compare that value to the maximum instance count. This will indicate how close you are to the maximum available capacity.
| Define the minimum instance count | For Application Gateway v2 SKU, autoscaling takes some time (approximately six to seven minutes) before the additional set of instances is ready to serve traffic. During that time, if there are short spikes in traffic, expect transient latency or loss of traffic.<br><br>We recommend that you set your minimum instance count to an optimal level. After you estimate the average instance count and determine your Application Gateway autoscaling trends, define the minimum instance count based on your application patterns. For information, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support).<br><br>Check the Current Compute Units for the past one month. This metric represents the gateway's CPU utilization. To define the minimum instance count, divide the peak usage by 10. For example, if your average Current Compute Units in the past month is 50, set the minimum instance count to five. |
| Define the maximum instance count | We recommend 125 as the maximum autoscale instance count. Make sure the subnet that has the Application Gateway has sufficient available IP addresses to support the scale-up set of instances.<br><br>Setting the maximum instance count to 125 has no cost implications because you're billed only for the consumed capacity. |
| Define Application Gateway subnet size | Application Gateway needs a dedicated subnet within a virtual network. The subnet can have multiple instances of the deployed Application Gateway resource. You can also deploy other Application Gateway resources in that subnet, v1 or v2 SKU.<br><br>Here are some considerations for defining the subnet size:<br><br>- Application Gateway uses one private IP address per instance and another private IP address if a private front-end IP is configured.<br>- Azure reserves five IP addresses in each subnet for internal use.<br>- Application Gateway (Standard or WAF SKU) can support up to 32 instances. Taking 32 instance IP addresses + 1 private front-end IP + 5 Azure reserved, a minimum subnet size of /26 is recommended. Because the Standard_v2 or WAF_v2 SKU can support up to 125 instances, using the same calculation, a subnet size of /24 is recommended.<br>- If you want to deploy additional Application Gateway resources in the same subnet, consider the additional IP addresses that will be required for their maximum instance count for both, Standard and Standard v2. |
| Take advantage of features for autoscaling and performance benefits | The v2 SKU offers autoscaling to ensure that your Application Gateway can scale up as traffic increases. When compared to v1 SKU, v2 has capabilities that enhance the performance of the workload. For example, better TLS offload performance, quicker deployment and update times, zone redundancy, and more. For more information about autoscaling features, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).<br><br>If you are running v1 SKU Application gateway, consider migrating to the Application gateway v2 SKU. For more information, see [Migrate Azure Application Gateway and Web Application Firewall from v1 to v2](/azure/application-gateway/migrate-v1-v2). |

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost-effectiveness, performance, and operational excellence of your Application Gateway.

### Reliability

- [Ensure application gateway fault tolerance](/azure/advisor/advisor-high-availability-recommendations#ensure-application-gateway-fault-tolerance)
- [Do not override hostname to ensure website integrity](/azure/advisor/advisor-high-availability-recommendations#do-not-override-hostname-to-ensure-website-integrity)

## Additional resources

### Azure Architecture Center guidance

- [Using API gateways in microservices](/azure/architecture/microservices/design/gateway)
- [Firewall and Application Gateway for virtual networks](/azure/architecture/example-scenario/gateway/firewall-application-gateway)
- [Protect APIs with Application Gateway and API Management](/azure/architecture/reference-architectures/apis/protect-apis)
- [IaaS: Web application with relational database](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)
- [Securely managed web applications](/azure/architecture/example-scenario/apps/fully-managed-secure-apps)
- [Zero-trust network for web applications with Azure Firewall and Application Gateway](/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall)

## Next steps

- Deploy an Application Gateway to see how it works: [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](/azure/application-gateway/quick-create-portal)

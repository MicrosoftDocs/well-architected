---
title: Microsoft Azure Well-Architected Framework review - Azure Application Gateway v2
description: Provides architectural best practices for the Azure Application Gateway v2 family of SKUs.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 02/25/2022
---

# Azure Well-Architected Framework review - Azure Application Gateway v2

This article provides architectural best practices for the Azure Application Gateway v2 family of SKUs. The guidance is based on the five pillars of architecture excellence: 

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure Application Gateway and are well versed with v2 SKU features. For more information, see [Azure Application Gateway features](/azure/application-gateway/features).

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with !!!!! xxxxxx !!!!!!.

<!-- 5. Pillar H2s ----------------------------------------------------

    Required

    The H2 headings in this template should be followed as-is. The order in which they are presented is a best practice. Each heading sets expectations for the content that follows. 

    TO DO: Add your H2 sections.
-->

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for Application Gateway, review the [Reliability design principles](../../../resiliency/principles.md).

> [!div class="checklist"]
> - Deploy the instances in a [zone-aware configuration](/azure/application-gateway/application-gateway-autoscaling-zone-redundant), where available.
> - Use Application Gateway with Web Application Firewall (WAF) within an application virtual network to protect inbound `HTTP/S` traffic from the Internet.
> - In new deployments, use Application Gateway v2 unless there is a compelling reason to use v1.
> - Deploy at least two AppGateway v2 instances to increase availability.

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your Application Gateway configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
| Plan for rule updates | Plan enough time for updates before accessing Application Gateway or making further changes. For example, removing servers from backend pool might take some time because they have to drain existing connections. |
| Use health probes to detect backend unavailability | If Application Gateway is used to load balance incoming traffic over multiple backend instances, we recommend the use of health probes. These will ensure that traffic is not routed to backends that are unable to handle the traffic. |
| Review the impact of the interval and threshold settings on health probes | The health probe sends requests to the configured endpoint at a set interval. Also, there's a threshold of failed requests that will be tolerated before the backend is marked unhealthy. These numbers present a trade-off.<br><br>- Setting a higher interval puts a higher load on your service. Each Application Gateway instance sends its own health probes, so 100 instances every 30 seconds means 100 requests per 30 seconds.<br>- Setting a lower interval leaves more time before an outage is detected.<br>- Setting a low unhealthy threshold may mean that short, transient failures may take down a backend.<br> - Setting a high threshold it can take longer to take a backend out of rotation. |
| Verify downstream dependencies through health endpoints | Suppose each backend has its own dependencies to ensure failures are isolated. For example, an application hosted behind Application Gateway may have multiple backends, each connected to a different database (replica). When such a dependency fails, the application may be working but won't return valid results. For that reason, the health endpoint should ideally validate all dependencies. Keep in mind that if each call to the health endpoint has a direct dependency call, that database would receive 100 queries every 30 seconds instead of 1. To avoid this, the health endpoint should cache the state of the dependencies for a short period of time. |
|When using Azure Front Door and Application Gateway to protect `HTTP/S` applications, use WAF policies in Front Door and lock down Application Gateway to receive traffic only from Azure Front Door.|Certain scenarios can force you to implement rules specifically on Application Gateway. For example, if ModSec CRS 2.2.9, CRS 3.0 or CRS 3.1 rules are required, these rules can be only implemented on Application Gateway. Conversely, rate-limiting and geo-filtering are available only on Azure Front Door, not on AppGateway.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Security

<!-- 5B. ----------------------------------------------------

    Follow the Security H2 heading with a sentence about how the section contributes to the framework. 
-->

Security is one of the most important aspects of any architecture. Application Gateway provides features to employ both the principle of least privilege and defense-in-defense. We recommend you review the [Security design principles](../../../security/security-principles.md).

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

> [!div class="checklist"]
> - When re-encrypting backend traffic, ensure the backend server certificate contains both the root and intermediate Certificate Authorities (CAs). A TLS certificate of the backend server must be issued by a well-known CA. If the certificate was not issued by a trusted CA, the Application Gateway checks if the certificate of the issuing CA was issued by a trusted CA, and so on until either a trusted CA is found. Only then a secure connection is established. Otherwise, Application Gateway marks the backend as unhealthy.
> - When the backend pool contains a resolvable FQDN, the DNS resolution is based on a private DNS zone or custom DNS server (if configured on the VNet), or it uses the default Azure-provided DNS.
> - NSGs are supported on Application Gateway, but there are some restrictions. For instance, some communication with certain port ranges is prohibited. Make sure you understand the implications of those restrictions. For details, see [Network security groups](/azure/application-gateway/configuration-infrastructure#network-security-groups).
> - Using User Defined Routes (UDR) on the Application Gateway subnet cause some issues. [Health status in the back-end](/azure/application-gateway/application-gateway-diagnostics#back-end-health) might be unknown. Application Gateway logs and metrics might not get generated. We recommend that you don't use UDRs on the Application Gateway subnet so that you can view the back-end health, logs, and metrics. If your organizations require to use UDR in the Application Gateway subnet, please ensure you review the supported scenarios. For more information, see [Supported user-defined routes](/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes).
> - When WAF is enabled, every request must be buffered by the Application Gateway until it fully arrives and check if the request matches with any rule violation in its core rule set and then forward the packet to the backend instances. For large file uploads (30MB+ in size), this can result in a significant latency. Because Application Gateway capacity requirements are different with WAF, we do not recommend enabling WAF on Application Gateway without proper testing and validation.

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your Application Gateway configuration for Security.

| Recommendation | Benefit |
|--------|----|
| Set up a TLS policy for enhanced security |Set up a [TLS policy](/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) for extra security. Ensure you're using the latest TLS policy version (AppGwSslPolicy20170401S). This enforces TLS 1.2 and stronger ciphers. |
| Use AppGateway for TLS termination | There are advantages of using Application Gateway for TLS termination:<br><br>- Performance improves because requests going to different backends to have to re-authenticate to each backend.<br>- Better utilization of backend servers because they don't have to perform TLS processing<br>- Intelligent routing by accessing the request content.<br>- Easier certificate management because the certificate only needs to be installed on Application Gateway. |
|Use Azure Key Vault to store TLS certificates|[Application Gateway is integrated with Key Vault](/azure/application-gateway/key-vault-certs). This provides stronger security, easier separation of roles and responsibilities, support for managed certificates, and an easier certificate renewal and rotation process.|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

<!-- 5C. ----------------------------------------------------

    Follow the Cost optimization H2 heading with a sentence about how the section contributes to the framework. 
-->

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](../../../cost/principles.md).

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

> [!div class="checklist"]
> - Familiarize yourself with Application Gateway pricing to help you identify the right deployment configuration for your environment. Ensure that the options are adequately sized to meet the capacity demand and deliver expected performance without wasting resources.<br><br>For information about Application Gateway pricing, see Understanding Pricing for Azure Application Gateway and Web Application Firewall.<br><br>Use these resources to estimate cost based on units of consumption.<br>- [Azure Application Gateway pricing](https://azure.microsoft.com/pricing/details/application-gateway/)<br>- [Pricing calculator](https://azure.microsoft.com/pricing/calculator/)


### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| Review underutilized resources | Identify and delete Application Gateway instances with empty backend pools to avoid unnecessary costs. |
| Stop Application Gateway instances when not in use | You aren't billed when Application Gateway is in the stopped state. Continuously running Application Gateway instances can incur extraneous costs. Evaluate usage patterns and stop instances when you don't need them. For example, usage after business hours in Dev/Test environments is expected to be low.<br><br>See these articles for information about how to stop and start instances.<br>- [Stop-AzApplicationGateway](/powershell/module/az.network/stop-azapplicationgateway?view=azps-6.0.0&viewFallbackFrom=azps-5.2.0&preserve-view=true)<br>- [Start-AzApplicationGateway](/powershell/module/az.network/start-azapplicationgateway?view=azps-5.2.0&preserve-view=true) |
| Have a scale-in and scale-out policy | A scale-out policy ensures that there will be enough instances to handle incoming traffic and spikes. Also, have a scale-in policy that makes sure the number of instances are reduced when demand drops. Consider the choice of instance size. The size can significantly impact the cost. Some considerations are described in the Estimate the Application Gateway instance count.<br><br>For more information, see [What is Azure Application Gateway v2?](/azure/application-gateway/overview-v2#pricing.) |
| Review consumption metrics across different parameters | You're billed based on metered instances of Application Gateway based on the metrics tracked by Azure. Evaluate the various metrics and capacity units and determine the cost drivers. For more information, see [Azure Cost Management and Billing](https://azure.microsoft.com/services/cost-management/#overview).<br><br> The following metrics are key for Application Gateway. This information can be used to validate that the provisioned instance count matches the amount of incoming traffic.<br><br>- Estimated Billed Capacity Units<br>- Fixed Billable Capacity Units<br>- Current Capacity Units<br><br>For more information, see [Application Gateway metrics](/azure/application-gateway/application-gateway-metrics#application-gateway-metrics).<br><br>Make sure you account for bandwidth costs. For details, see [Traffic across billing zones and regions](/azure/architecture/framework/cost/design-regions#traffic-across-billing-zones-and-regions).|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Operational excellence

<!-- 5D. ----------------------------------------------------

    Follow the Operational excellence H2 heading with a sentence about how the section contributes to the framework. 
-->

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](../../../devops/principles.md).

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

> [!div class="checklist"]
> - SNAT port limitations<br>SNAT port limitations are important for backend connections on the Application Gateway. There are separate factors that affect how Application Gateway reaches the SNAT port limit. For example, if the backend is a public IP address, it will require its own SNAT port. In order to avoid SNAT port limitations, you can increase the number of instances per Application Gateway, scale out the backends to have more IP addresses, or move your backends into the same virtual network and use private IP addresses for the backends.
> - Requests per second (RPS)<br>Requests per second (RPS) on the Application Gateway will be affected if the SNAT port limit is reached. For example, if an Application Gateway reaches the SNAT port limit, then it won't be able to open a new connection to the backend, and the request will fail.
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your Application Gateway configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
| Monitor capacity metrics | Use these metrics as indicators of utilization of the provisioned Application Gateway capacity. We strongly recommend setting up alerts on capacity. For details, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support). |
| Troubleshoot using metrics | There are other metrics that can indicate issues either at Application Gateway or the backend. We recommend evaluating the following alerts:<br><br>- Unhealthy Host Count<br>- Response Status (dimension 4xx and 5xx)<br>- Backend Response Status (dimension 4xx and 5xx)<br>- Backend Last Byte Response Time<br>- Application Gateway Total Time<br><br>For more information, see [Metrics for Application Gateway](/azure/application-gateway/application-gateway-metrics). |
| Enable diagnostics on Application Gateway and Web Application Firewall (WAF) | Diagnostic logs allow you to view firewall logs, performance logs, and access logs. Use these logs to manage and troubleshoot issues with Application Gateway instances. |
| Use Azure Monitor Network Insights | Azure Monitor Network Insights provides a comprehensive view of health and metrics for network resources, including Application Gateway. For additional details and supported capabilities for Application Gateway, see [Azure Monitor Network insights](/azure/azure-monitor/insights/network-insights-overview). |
| Match timeout settings with the backend application | Ensure you have configured the IdleTimeout settings to match the listener and traffic characteristics of the backend application. The default value is set to four minutes and can be configured to a maximum of 30. For more information, see [Load Balancer TCP Reset and Idle Timeout](/azure/load-balancer/load-balancer-tcp-reset).<br><br>For workload considerations, see [Application Monitoring](/azure/architecture/framework/devops/monitoring#application-monitoring). |
|Monitor Key Vault configuration issues using Azure Advisor|Application Gateway checks for the renewed certificate version in the linked Key Vault at every 4-hour interval. If it is inaccessible due to any incorrectly modified Key Vault configurations, it logs that error and pushes a corresponding Advisor recommendation. You must configure the Advisor alert to stay updated and fix such issues immediately to avoid any Control or Data plane related problems. To set an alert for this specific case, use the Recommendation Type as **Resolve Azure Key Vault issue for your Application Gateway**.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Performance efficiency

<!-- 5E. ----------------------------------------------------

    Follow the Performance efficiency H2 heading with a sentence about how the section contributes to the framework. 
-->

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](../../../scalability/principles.md).

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

> [!div class="checklist"]
> - Estimate the Application Gateway instance count<br>Application Gateway v2 scales out based on many aspects, such as CPU, memory, network utilization, and more. To determine the approximate instance count, factor in these metrics:<br><br>Current compute units — Indicates CPU utilization. 1 Application Gateway instance is approximately 10 compute units.<br>Throughput — Application Gateway instance can serve 60-75 Mbps of throughput. This data depends on the type of payload.<br><br>Consider this equation when calculating instance counts.<br>![Approximate instance count](../../../_images/autoscale-instance.svg)<br><br>After you've estimated the instance count, compare that value to the maximum instance count. This will indicate how close you are to the maximum available capacity.
> - Define the minimum instance count<br>For Application Gateway v2 SKU, autoscaling takes some time (approximately six to seven minutes) before the additional set of instances is ready to serve traffic. During that time, if there are short spikes in traffic, expect transient latency or loss of traffic.<br><br>We recommend that you set your minimum instance count to an optimal level. After you estimate the average instance count and determine your Application Gateway autoscaling trends, define the minimum instance count based on your application patterns. For information, see [Application Gateway high traffic support](/azure/application-gateway/high-traffic-support).<br><br>Check the Current Compute Units for the past one month. This metric represents the gateway's CPU utilization. To define the minimum instance count, divide the peak usage by 10. For example, if your average Current Compute Units in the past month is 50, set the minimum instance count to five.
> - Define the maximum instance count<br>We recommend 125 as the maximum autoscale instance count. Make sure the subnet that has the Application Gateway has sufficient available IP addresses to support the scale-up set of instances.<br><br>Setting the maximum instance count to 125 has no cost implications because you're billed only for the consumed capacity.
> - Define Application Gateway subnet size<br>Application Gateway needs a dedicated subnet within a virtual network. The subnet can have multiple instances of the deployed Application Gateway resource. You can also deploy other Application Gateway resources in that subnet, v1 or v2 SKU.<br><br>Here are some considerations for defining the subnet size:<br><br>- Application Gateway uses one private IP address per instance and another private IP address if a private front-end IP is configured.<br>- Azure reserves five IP addresses in each subnet for internal use.<br>- Application Gateway (Standard or WAF SKU) can support up to 32 instances. Taking 32 instance IP addresses + 1 private front-end IP + 5 Azure reserved, a minimum subnet size of /26 is recommended. Because the Standard_v2 or WAF_v2 SKU can support up to 125 instances, using the same calculation, a subnet size of /24 is recommended.<br>- If you want to deploy additional Application Gateway resources in the same subnet, consider the additional IP addresses that will be required for their maximum instance count for both, Standard and Standard v2.

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| Take advantage features for autoscaling and performance benefits | The v2 SKU offers autoscaling to ensure that your Application Gateway can scale up as traffic increases. When compared to v1 SKU, v2 has capabilities that enhance the performance of the workload. For example, better TLS offload performance, quicker deployment and update times, zone redundancy, and more. For more information about autoscaling features, see [Scaling Application Gateway v2 and WAF v2](/azure/application-gateway/application-gateway-autoscaling-zone-redundant).<br><br>If you are running v1 SKU gateways, consider migrating to the v2 SKU. For more information, see [Migrate Azure Application Gateway and Web Application Firewall from v1 to v2](/azure/application-gateway/migrate-v1-v2). |

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Azure Advisor recommendations

<!-- 6. Azure Advisor recommendations -----------------------------------------

    Required    

    Follow the Azure Advisor recommendations H2 heading with a sentence about how recommendations might help improve the configuration of the resource. 

-->
[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Application Gateway.

### Reliability

- [Ensure application gateway fault tolerance](/azure/advisor/advisor-high-availability-recommendations#ensure-application-gateway-fault-tolerance)
- [Do not override hostname to ensure website integrity](/azure/advisor/advisor-high-availability-recommendations#do-not-override-hostname-to-ensure-website-integrity)

## Additional resources

<!-- 7. Additional resources ----------------------------------------------------

    Optional

    The resources should be separated in sections. The H3 heading should indicate the purpose of the resource. 

-->

- \[\<link text>]\(\<link>)
- \[\<link-text>]\(\<link>)

## Next steps

<!-- 8. Next steps ------------------------------------------------------------

    Required

    Add a context sentence for the following links: 

    Best practices:
        - Provide at least one next step and no more than three.
        - Include some context, so the customer can determine why they would select the link.
        - Do not use a "More information" or "See also" section.
        - Do provide a link to relevant quickstarts in the product documentation.
        - Do provide a link to a Learn module that covers resource provisioning.

-->

- Deploy an Application Gateway to see how it works: [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](/azure/application-gateway/quick-create-portal)

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->
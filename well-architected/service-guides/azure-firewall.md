---
title: Azure Well-Architected Framework review - Azure Firewall
description: This article provides architectural best practices for Azure Firewall.
author: vhorne
ms.author: victorh
ms.reviewer: tozimmergren
ms.custom: engagement-fy23
ms.topic: conceptual
ms.date: 09/30/2022
products: azure-firewall
---

# Azure Well-Architected Framework review - Azure Firewall

This article provides architectural best practices for Azure Firewall. The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you have working knowledge of Azure Firewall and are well versed with its features. For more information, see [Azure Firewall Standard features](/azure/firewall/features).

## Prerequisites

- Understanding the Azure Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. Review your workload by using the [Well-Architected Framework review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. Start with [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app) and [Implement a secure hybrid network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?tabs=portal).

## Reliability

To learn how Azure Firewall supports a reliable workload, see the following articles:

- [Introduction to Azure Firewall](/training/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with availability zones](/azure/firewall/deploy-template)

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../resiliency/principles.md) for reliability.

> [!div class="checklist"]
> - Deploy by using a secured virtual hub.
> - Use a global Azure Firewall policy.
> - Determine if you want to use third-party security as a service (SECaaS) providers.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for reliability.

| Recommendation | Benefit |
|--------|----|
|Use Azure Firewall Manager with Azure Virtual WAN to deploy and manage instances of Azure Firewall across Virtual WAN hubs or in hub virtual networks.|Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection.|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all instances of Azure Firewall.|Allow for granular policies to meet the requirements of specific regions. Delegate incremental firewall policies to local security teams through role-based access control (RBAC).|
|Configure supported third-party software as a service (SaaS) security providers within Firewall Manager if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, third-party SECaaS offerings to protect internet access for your users.|
|Deploy Azure Firewall across multiple availability zones for a higher service-level agreement (SLA).|Azure Firewall provides different SLAs when it's deployed in a single availability zone and when it's deployed in multizones. For more information, see [SLA for Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_1). For information about all Azure SLAs, see [SLA summary for Azure services](https://azure.microsoft.com/support/legal/sla/summary).|
|In multi-region environments, deploy an instance of Azure Firewall per region.|For workloads designed to be resistant to failures and fault tolerant, remember to consider that instances of Azure Firewall and Azure Virtual Network are regional resources.|
|Closely monitor Azure Firewall metrics to ensure this component of your solution is healthy.|Closely monitor metrics, especially SNAT port utilization, firewall health state, and throughput.|

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. [Azure Firewall](/azure/firewall/) is an intelligent firewall security service that provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../security/security-principles.md) for security.

> [!div class="checklist"]
> - Use a global Azure Firewall policy.
> - Use threat intelligence.
> - Use a DNS proxy.
> - Direct network traffic through Azure Firewall.
> - Validate spoke networks.
> - Determine if you want to use third-party SECaaS providers.
> - Use just-in-time (JIT) systems.
> - Protect your hub virtual network with a DDoS protection plan.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for security.

| Recommendation | Benefit |
|--------|----|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all instances of Azure Firewall.|Allow for granular policies to meet the requirements of specific regions. Delegate incremental firewall policies to local security teams through RBAC.|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services, including Microsoft Defender for Cloud.
|Enable Domain Name System (DNS) proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate if unnecessary peering exists between the hub virtual network where Azure Firewall is deployed, and other spoke virtual networks.|Helps to guarantee that undesired traffic isn't being sent to the Azure firewall or the hub network where the Azure Firewall is deployed.|
|Use security partner providers for third-party SECaaS offerings.|Security partner providers help filter internet traffic through a virtual private network or a branch to the internet.|
|Use JIT systems to control access to virtual machines (VMs) from the internet.|You can use Microsoft Defender for Cloud JIT to control access for clients that connect from the internet by using Azure Firewall.|
|Configure Azure Firewall in the forced tunneling mode to route all internet-bound traffic to a designated next hop instead of going directly to the internet.|Azure Firewall must have direct internet connectivity. If your **AzureFirewallSubnet** learns a default route to your on-premises network via the Border Gateway Protocol, you must configure Azure Firewall in the forced tunneling mode. Using the forced tunneling feature, you'll need another /26 address space for the Azure Firewall Management subnet. You're required to name it **AzureFirewallManagementSubnet**.<br><br>If this is an existing Azure Firewall instance that can't be reconfigured in the forced tunneling mode, create a UDR with a 0.0.0.0/0 route. Set the **NextHopType** value as **Internet**. Associate it with **AzureFirewallSubnet** to maintain internet connectivity.|
|Set the public IP address to **None** to deploy a fully private data plane when you configure Azure Firewall in the forced tunneling mode.|When you deploy a new Azure Firewall instance, if you enable the forced tunneling mode, you can set the public IP address to **None** to deploy a fully private data plane. However, the management plane still requires a public IP for management purposes only. The internal traffic from virtual and on-premises networks won't use that public IP. For more about forced tunneling, see [Azure Firewall forced tunneling](/azure/firewall/forced-tunneling).|
|Use fully qualified domain name (FQDN) filtering in network rules.|You can use FQDNs based on DNS resolution in Azure Firewall and firewall policies. This capability allows you to filter outbound traffic with any TCP/UDP protocol (including NTP, SSH, RDP, and more). You must enable the DNS Proxy option to use FQDNs in your network rules. To learn how it works, see [Azure Firewall FQDN filtering in network rules](/azure/firewall/fqdn-filtering-network-rules#how-it-works).|
|Use Azure Firewall Manager to create and associate a DDoS protection plan with your hub virtual network.|A DDoS protection plan provides enhanced mitigation features to defend your firewall from DDoS attacks. Azure Firewall Manager is an integrated tool to create your firewall infrastructure and DDoS protection plans. For more information, see [Configure an Azure DDoS Protection Plan using Azure Firewall Manager](/azure/firewall-manager/configure-ddos).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

[All internet traffic should be routed via your Azure Firewall](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next-generation firewall.

All built-in policy definitions related to Azure networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../cost-optimization/principles.md) for cost optimization.

> [!div class="checklist"]
> - Determine which firewall SKUs to deploy.
> - Determine if some resources don't need 100% allocation.
> - Determine where you can optimize firewall use across workloads.
> - Monitor firewall usage to determine cost-effectiveness.
> - Review Firewall Manager capabilities to determine potential operational efficiency.
> - Determine the number of public IP addresses required.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for cost optimization.

| Recommendation | Benefit |
|--------|----|
|Deploy the Standard and Premium SKUs where appropriate.| The Standard option is usually enough for east-west traffic. Premium has the necessary extra features for north-south traffic, the forced tunneling feature, and many other features. For more information, see [Azure Firewall Premium Preview features](/azure/firewall/premium-features). Deploy mixed scenarios using the Standard and Premium options according to your needs.|
|Stop Azure Firewall deployments that don't need to run for 24 hours.|You might have development environments that are used only during business hours. For more information, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
|Share the same instance of Azure Firewall across multiple workloads and Azure Virtual Network.|You can use a central instance of Azure Firewall in the hub virtual network and share the same firewall across many spoke virtual networks that are connected to the same hub from the same region.<br><br>Ensure there's no unexpected cross-region traffic as part of the hub-spoke topology.|
|Review underutilized Azure Firewall instances. Identify and delete unused Azure Firewall deployments. | To identify unused Azure Firewall deployments, start by analyzing the monitoring metrics and UDRs associated with subnets pointing to the firewall's private IP. Combine that information with other validations, such as if your instance of Azure Firewall has any rules (classic) for NAT, Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, and with internal documentation about your environment and deployments. <br><br> You can detect deployments that are cost-effective over time. <br><br> For more information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
|Use Azure Firewall Manager and its policies to reduce operational costs, increase efficiency, and reduce management overhead.|Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.<br><br>For more information, see [Pricing - Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
|Delete unused public IP addresses and use IP Groups to reduce your management overhead.|Validate whether all the associated public IP addresses are in use. If they aren't in use, disassociate and delete them. Use IP Groups to reduce your management overhead. Evaluate SNAT port utilization before removing any IP addresses.<br><br>You'll only use the number of public IPs your firewall needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).

For more suggestions, see [Principles of the Cost optimization pillar](../../cost-optimization/overview.md).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. You can measure performance statistics and metrics to troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../devops/principles.md) for operational excellence.

> [!div class="checklist"]
> - Use logs for monitoring.
> - Use tags when possible to allow traffic through the firewall.
> - Use workbooks.
> - Use the Azure Firewall connector in Microsoft Sentinel.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for operational excellence.

| Recommendation | Benefit |
|--------|----|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall by using firewall logs or workbooks. You can also use activity logs for auditing operations on Azure Firewall resources.|
|Use FQDN tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Use workbooks in Azure Log Analytics.|Workbooks help visualize firewall logs.|
|Enable Azure Firewall connector in Microsoft Sentinel.|You can use Microsoft Sentinel to create detections and logic apps for Azure Firewall.|
|Migrate Azure Firewall rules to Azure Firewall Manager policies for existing deployments.|For existing deployments, migrate Azure Firewall rules to Azure Firewall Manager policies. Use Azure Firewall Manager to centrally manage your firewalls and policies.|
|Monitoring capacity metrics are indicators of the utilization of provisioned Azure Firewall capacity.|Set alerts as needed to get notifications after reaching a threshold for any metric.<br>For information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics).|
|Monitor other Azure Firewall logs and metrics for troubleshooting and set alerts.|Azure Firewall exposes a few other logs and metrics for troubleshooting that are suitable indicators of issues. Evaluate alerts based on the following list.<br><ul><li>Application Rule log: Each new connection that matches one of your configured application rules results in a log for the accepted/denied connection.</li><li>Network Rule log: Each new connection that matches one of your configured network rules results in a log for the accepted/denied connection.</li><li>DNS Proxy log: This log tracks DNS messages to a DNS server configured using a DNS proxy.</li></ul>|
|Use diagnostics logs and policy analytics.|Diagnostic logs allow you to view Azure Firewall logs, performance logs, and access logs. You can use these logs in Azure to manage and troubleshoot your Azure Firewall instance.<br><br>Policy analytics for Azure Firewall Manager allows you to start seeing rules and flows that match the rules and hit count for those rules. You can have full traffic visibility by watching what rule is in use and the traffic being matched.|

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to efficiently meet the demands placed on it by users.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../scalability/principles.md) for performance efficiency.

> [!div class="checklist"]
> - Determine your SNAT port requirements and if you should deploy a NAT gateway.
> - Plan load tests to test auto-scale performance in your environment.
> - Plan network rule requirements and opportunities to summarize IP ranges.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for performance efficiency.

| Recommendation | Benefit |
|--------|----|
| If you'll need more than 512,000 SNAT ports, deploy a NAT gateway with Azure Firewall. | With a NAT gateway, you can scale up to more than 1 million ports. For more information, see [Scale SNAT ports with Azure NAT gateway](/azure/firewall/integrate-with-nat-gateway).|
|Create initial traffic that isn't part of your load tests 20 minutes before the test. Use diagnostics settings to capture scale-up and scale-down events. You can use the [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) service to generate the initial traffic.|Allows the Azure Firewall instance to scale up its instances to the maximum. |
|Use IP Groups to summarize IP address ranges.|You can use IP Groups to summarize IP ranges, so you don't exceed [the limit of unique source/destination network rules](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits). For each rule, Azure multiplies ports by IP addresses. So, if you have one rule with four IP address ranges and five ports, you'll consume 20 network rules. The IP Group is treated as a single address for the purpose of creating network rules.
|Configure an Azure Firewall subnet (AzureFirewallSubnet) with a /26 address space.|Azure Firewall is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the instance of Azure Firewall. Azure Firewall provisions more capacity as it scales.<br>A /26 address space for its subnets ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall doesn't need a subnet bigger than /26. The Azure Firewall subnet name must be **AzureFirewallSubnet**.|

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost-effectiveness, performance, and operational excellence of your instance of Azure Firewall.

- [Create Azure Service Health alerts to be notified when Azure problems affect you](/azure/advisor/advisor-high-availability-recommendations#create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you)
- [Ensure you have access to Azure cloud experts when you need it](/azure/advisor/advisor-operational-excellence-recommendations#ensure-you-have-access-to-azure-cloud-experts-when-you-need-it)
- [Enable Traffic Analytics to view insights into traffic patterns across Azure resources](/azure/advisor/advisor-reference-operational-excellence-recommendations#enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources)
- [Update your outbound connectivity protocol to Service Tags for Azure Site Recovery](/azure/advisor/advisor-reference-reliability-recommendations#update-your-outbound-connectivity-protocol-to-service-tags-for-azure-site-recovery)
- [Follow just enough administration (least privilege principle)](/security/benchmark/azure/baselines/advisor-security-baseline?toc=/azure/advisor/toc.json#pa-7-follow-just-enough-administration-least-privilege-principle)
- [Protect your network resources with Microsoft Defender for Cloud](/azure/defender-for-cloud/protect-network-resources)

## Additional resources

- [Azure Firewall documentation](/azure/firewall)
- [Azure Firewall service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits)
- [Azure security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline)

### Azure Architecture Center guidance

- [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls)
- [Use Azure Firewall to help protect an Azure Kubernetes Service (AKS) cluster](/azure/architecture/example-scenario/aks-firewall/aks-firewall)
- [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli)
- [Implement a secure hybrid network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)
- [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app)

## Next step

Deploy an instance of Azure Firewall to see how it works:
- [Tutorial: Deploy and configure Azure Firewall and policy by using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)

---
title: Azure Well-Architected Framework review - Azure Firewall
description: This article provides architectural best practices for Azure Firewall.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 05/17/2022
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

- [Introduction to Azure Firewall](/learn/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with availability zones](/azure/firewall/deploy-template)

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../resiliency/principles.md) for Reliability.

> [!div class="checklist"]
> - Deploy by using a secured virtual hub.
> - Use a global Azure Firewall policy.
> - Determine if you want to use third-party security as a service (SECaaS) providers.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for reliability.

| Recommendation | Benefit |
|--------|----|
|Use Azure Firewall Manager with Azure Virtual WAN to deploy and manage instances of Azure Firewall across Virtual WAN hubs or in hub virtual networks.|Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection.|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental firewall policies to local security teams through role-based access control (RBAC).|
|Configure supported third-party software as a service (SaaS) security providers within Firewall Manager, if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, third-party SECaaS offerings to protect internet access for your users.|
|Deploy Azure Firewall across multiple availability zones for a higher service-level agreement (SLA).|Azure Firewall provides different SLAs when it's deployed in a single availability zone and when it's deployed in multizones. For more information, see [SLA for Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_1). For information about all Azure SLAs, see [SLA summary for Azure services](https://azure.microsoft.com/support/legal/sla/summary).|
|In multi-region environments, deploy an instance of Azure Firewall per region.|For workloads designed to be resistant to failures and to be fault tolerant, remember to take into consideration that instances of Azure Firewall and Azure Virtual Network are regional resources.|
|Closely monitor Azure Firewall metrics to ensure this component of your solution is healthy.|Closely monitor metrics, especially SNAT port utilization, firewall health state, and throughput.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. [Azure Firewall](/azure/firewall/) is an intelligent firewall security service that provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../security/security-principles.md) for Security.

> [!div class="checklist"]
> - Use a global Azure Firewall policy.
> - Use threat intelligence.
> - Use a DNS proxy.
> - Direct network traffic through Azure Firewall.
> - Validate spoke networks.
> - Determine if you want to use third-party SECaaS providers.
> - Use just-in-time (JIT) systems.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for security.

| Recommendation | Benefit |
|--------|----|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all instances of Azure Firewall.|Allow for granular policies to meet requirements of specific regions. Delegate incremental firewall policies to local security teams through RBAC.|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.
|Enable Domain Name System (DNS) proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate if unnecessary peering exists between the virtual network where Azure Firewall is deployed and other virtual networks.|Helps to guarantee that undesired traffic is sent to the firewall or hub network.|
|Use security partner providers for third-party SECaaS offerings.|Security partner providers help to filter internet traffic through a virtual private network to the internet or a branch to the internet.|
|Use JIT systems to control access to virtual machines (VMs) from the internet.|You can use Security Center JIT to control access for clients that connect from the internet by using Azure Firewall.|
|Configure Azure Firewall in the forced tunneling mode to route all internet-bound traffic to a designated next hop instead of going directly to the internet.|Azure Firewall must have direct internet connectivity. If your **AzureFirewallSubnet** learns a default route to your on-premises network via the Border Gateway Protocol, you must configure Azure Firewall in the forced tunneling mode. When you use the forced tunneling feature, you'll need another /26 address space for the Azure Firewall Management subnet. You're required to name it **AzureFirewallManagementSubnet**.<br><br>If this is an existing Azure Firewall instance, which can't be reconfigured in the forced tunneling mode, create a UDR with a 0.0.0.0/0 route. Set the **NextHopType** value as **Internet**. Associate it with **AzureFirewallSubnet** to maintain internet connectivity.|
|Set the public IP address to **None** to deploy a fully private data plane when you configure Azure Firewall in the forced tunneling mode.|When you deploy a new Azure Firewall instance, if you enable the forced tunneling mode, you can set the public IP address to **None** to deploy a fully private data plane. However, the management plane still requires a public IP for management purposes only. The internal traffic from virtual networks and on-premises won't use that public IP. For more about forced tunneling, see [Azure Firewall forced tunneling](/azure/firewall/forced-tunneling).|
|Use fully qualified domain name (FQDN) filtering in network rules.|You can use FQDNs in network rules, based on DNS resolution in Azure Firewall and firewall policies. This capability allows you to filter outbound traffic with any TCP/UDP protocol (including NTP, SSH, RDP, and more). You must enable the DNS Proxy option to use FQDNs in your network rules. To learn how it works, see [Azure Firewall FQDN filtering in network rules](/azure/firewall/fqdn-filtering-network-rules#how-it-works).|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

[All internet traffic should be routed via your deployed Azure Firewall](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next-generation firewall.

All built-in policy definitions related to Azure networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../cost/principles.md) for Cost optimization.

> [!div class="checklist"]
> - Determine which firewall SKUs to deploy.
> - Determine if some resources don't need 100% allocation.
> - Determine where you can optimize firewall use across workloads.
> - Monitor firewall usage to determine cost effectiveness.
> - Review Firewall Manager capabilities to determine potential operational efficiency.
> - Determine the number of public IP addresses required.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for cost optimization.

| Recommendation | Benefit |
|--------|----|
|Deploy the Standard and Premium SKUs where appropriate.| The Standard option is usually enough for east-west traffic. Premium comes with the necessary extra features for north-south traffic, the forced tunneling feature, and many other features. For more information, see [Azure Firewall Premium Preview features](/azure/firewall/premium-features). Deploy mixed scenarios by using the Standard and Premium options, according to your needs.|
|Stop Azure Firewall deployments that don't need to run for 24 hours.|You might have development environments that are used only during business hours. For more information, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
|Share the same instance of Azure Firewall across multiple workloads and Azure Virtual Network.|You can use a central instance of Azure Firewall in the hub virtual network and share the same firewall across many spoke virtual networks that are connected to the same hub from the same region.<br><br>Ensure there's no unexpected cross-region traffic as part of the hub-spoke topology.|
|Review underutilized Azure Firewall instances. Identify and delete Azure Firewall deployments not in use. | To identify Azure Firewall deployments not in use, start by analyzing the monitoring metrics and UDRs that are associated with subnets pointing to the firewall's private IP. Combine that information with other validations, such as if your instance of Azure Firewall has any rules (Classic) for NAT, or Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, and with internal documentation about your environment and deployments. <br><br> You can detect deployments that are cost effective over time. <br><br> For more information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
|Use Azure Firewall Manager and its policies to reduce your operational costs, increase your efficiency, and reduce your management overhead.|Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.<br><br>For more information, see [Pricing - Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
|Delete unused public IP addresses and use IP Groups to reduce your management overhead.|Validate whether all the associated public IP addresses are in use. If they aren't in use, disassociate and delete them. Use IP Groups to reduce your management overhead. Evaluate SNAT ports utilization before you remove any IP addresses.<br><br>You'll only use the number of public IPs that your firewall actually needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).

For more suggestions, see [Principles of the Cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. You can measure performance statistics and also use metrics to troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../devops/principles.md) for Operational excellence.

> [!div class="checklist"]
> - Use logs for monitoring.
> - Use tags when possible to allow traffic through the firewall.
> - Use workbooks.
> - Use the Azure Firewall connector in Microsoft Sentinel.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for operational excellence.

| Recommendation | Benefit |
|--------|----|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall by using firewall logs or workbooks. You can also use activity logs to audit operations on Azure Firewall resources.|
|Use FQDN tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Use workbooks in Azure Log Analytics.|Workbooks help you visualize firewall logs.|
|Enable Azure Firewall connector in Microsoft Sentinel.|You can use Microsoft Sentinel to create detections and logic apps for Azure Firewall.|
|Migrate Azure Firewall rules to Azure Firewall Manager policies for existing deployments.|For existing deployments, migrate Azure Firewall rules to Azure Firewall Manager policies. Use Azure Firewall Manager to centrally manage your firewalls and policies.|
|Monitoring capacity metrics are indicators of utilization of provisioned Azure Firewall capacity.|Alerts can be set as needed to get notifications after a threshold is reached for any metric.<br>For information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics).|
|Monitor other Azure Firewall logs and metrics for troubleshooting and set alerts.|Azure Firewall exposes a few other logs and metrics for troubleshooting that can be used as indicators of issues. Evaluate alerts based on the following list.<br><ul><li>Application Rule log: Each new connection that matches one of your configured application rules results in a log for the accepted/denied connection.</li><li>Network Rule log:	Each new connection that matches one of your configured network rules results in a log for the accepted/denied connection.</li><li>DNS Proxy log: This log tracks DNS messages to a DNS server that's configured by using a DNS proxy.</li></ul>|
|Use diagnostics logs and policy analytics.|Diagnostic logs allow you to view Azure Firewall logs, performance logs, and access logs. You can use these logs in Azure to manage and troubleshoot your Azure Firewall instance.<br><br>Policy analytics for Azure Firewall Manager allow you to start seeing rules and flows that match the rules and hit count for those rules. By watching what rule is in use and the traffic that's being matched, you can have full visibility of the traffic.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../scalability/principles.md) for Performance efficiency.

> [!div class="checklist"]
> - Determine your SNAT port requirements and if you should deploy a NAT gateway.
> - Plan load tests to test auto-scale performance in your environment.
> - Plan network rule requirements and opportunities to summarize IP ranges.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for performance efficiency.

| Recommendation | Benefit |
|--------|----|
| If you'll need more than 512,000 SNAT ports, deploy a NAT gateway with Azure Firewall. | With a NAT gateway, you can scale up to more than 1 million ports. For more information, see [Scale SNAT ports with Azure NAT gateway](/azure/firewall/integrate-with-nat-gateway).|
|Create initial traffic that isn't part of your load tests 20 minutes prior to the test. Use diagnostics settings to capture scale-up and scale-down events.|Allows the Azure Firewall instance to scale up its instances to the maximum. |
|Use IP Groups to summarize IP address ranges.|You can use IP Groups to summarize IP ranges so you don't exceed 10,000 network rules. For each rule, Azure multiplies ports by IP addresses. So, if you have 1 rule with 4 IP address ranges and 5 ports, you'll actually consume 20 network rules.
|Configure an Azure Firewall subnet (AzureFirewallSubnet) with a /26 address space.|An Azure Firewall is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the instance of Azure Firewall. Azure Firewall provisions more capacity as it scales.<br>A /26 address space for its subnets ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall doesn't need a subnet bigger than /26. The Azure Firewall subnet name must be **AzureFirewallSubnet**.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your instance of Azure Firewall.

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

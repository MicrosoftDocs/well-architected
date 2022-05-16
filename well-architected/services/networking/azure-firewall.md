---
title: Microsoft Azure Well-Architected Framework review - Azure Firewall
description: Provides architectural best practices for the Azure Firewall.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 05/09/2022
---

# Azure Well-Architected Framework review - Azure Firewall

This article provides architectural best practices for Azure Firewall. The guidance is based on the five pillars of architecture excellence:

- [Reliability](#reliability)
- [Security](#security)
- [Cost optimization](#cost-optimization)
- [Operational excellence](#operational-excellence)
- [Performance efficiency](#performance-efficiency)

We assume that you have working knowledge of Azure Firewall and are well versed with its features. For more information, see [Azure Firewall Standard features](/azure/firewall/features).

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app) and [Implement a secure hybrid network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?tabs=portal).

## Reliability

To learn how Azure Firewall supports a reliable workload, see the following articles:

- [Introduction to Azure Firewall](/learn/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with Availability Zones](/azure/firewall/deploy-template)

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../resiliency/principles.md) for Reliability.

> [!div class="checklist"]
> - Deploy using a secured virtual hub
> - Use a global Azure Firewall policy
> - Determine if you want to use use third-party security as a service (SECaaS) providers.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|Use Firewall Manager with Azure Virtual Wide Area Networks (WAN) to deploy and manage Azure Firewalls across Azure Virtual WAN Hubs, or in Hub VNets.|Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection.|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security Teams through role-based access control (RBAC).|
|Configure supported third-party SaaS security providers within the Firewall Manager, if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, third-party security as a service (SECaaS) offerings to protect Internet access for your users.|
|Deploy Azure Firewall across multiple availability zones for a higher SLA|Azure Firewall provides different SLAs when it is deployed in a single Availability Zone and for when it is deployed in multi-zones. For more information, see [SLA for Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_1). For information about all Azure SLAs, see the [Azure service level agreements page](https://azure.microsoft.com/support/legal/sla/summary).|
|In multi-region environments, deploy an instance of Azure Firewall per region|For workloads designed to be resistant to failures and to be fault-tolerant, remember to take into consideration that Azure Firewalls and Virtual Networks are regional resources.|
|Closely monitor Azure Firewall metrics to ensure this component of your solution is healthy|Closely monitor metrics, especially SNAT port utilization, firewall health state, and throughput.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. An intelligent firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../security/security-principles.md) for Security.

> [!div class="checklist"]
> - Use a global Azure Firewall policy
> - Use threat intelligence
> - Use DNS proxy
> - Direct network traffic through Azure Firewall
> - Validate spoke networks
> - Determine if you want to use use third-party security as a service (SECaaS) providers.
> - Use just-in-time systems (JIT)

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Security.

| Recommendation | Benefit |
|--------|----|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security teams through role-based access control (RBAC).|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.
|Enable Domain Name System (DNS) Proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use Corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate if unnecessary peering exist between the VNet where Azure Firewall is deployed and other VNets.|Helps to guarantee that undesired traffic is sent to the Firewall or Hub Network.|
|Leverage Security Partner Providers for third-party Security as a Service (SECaaS) Offerings.|Security partner providers help to filter internet traffic through Virtual Private Network (VNET) to internet or Branch to internet.|
|Use just-in-time systems (JIT) to control access to virtual machines (VMs) from the internet.|You can use Azure Security Center JIT to control access for clients connecting from the internet using Azure Firewall.|
|Configure Azure Firewall in the forced tunneling mode to route all Internet-bound traffic to a designated next hop instead of going directly to the Internet|Azure Firewall must have direct Internet connectivity. If your **AzureFirewallSubnet** learns a default route to your on-premises network via BGP, then you must configure Azure Firewall in the forced tunneling mode. When using the forced tunneling feature, you will need an additional /26 address space for the Azure Firewall Management subnet, and you must name it **AzureFirewallManagementSubnet** (this is also a requirement).<br><br>If this is an existing Azure Firewall instance, which cannot be reconfigured in the forced tunneling mode, then we recommended that you create a UDR with a 0.0.0.0/0 route, with the **NextHopType** value set as **Internet**. Associate it with the **AzureFirewallSubnet** to maintain internet connectivity.|
|Set the Public IP Address to **None** to deploy a fully private data plane when configuring Azure Firewall in the forced tunneling mode|When deploying a new Azure Firewall instance, if you enable the forced tunneling mode, you can set the Public IP Address to **None** to deploy a fully private data plane. However, the management plane still requires a public IP, for management purposes only. The internal traffic from Virtual Networks, and/or on-premises, will not use that public IP. See more about forced tunneling at [Azure Firewall forced tunneling](/azure/firewall/forced-tunneling).|
|Use FQDN filtering in network rules.|You can use FQDNs in network rules, based on DNS resolution in Azure Firewall and Firewall policy. This capability allows you to filter outbound traffic with any TCP/UDP protocol (including NTP, SSH, RDP, and more). You must enable the DNS proxy to use FQDNs in your network rules. See how it works at [Azure Firewall FQDN filtering in network rules](/azure/firewall/fqdn-filtering-network-rules#how-it-works).|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- [All Internet traffic should be routed via your deployed Azure Firewall](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next generation firewall.

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

### Design checklist

As you make design choices for Azure Firewall, review the  [design principles](../../cost/principles.md) for Cost optimization.

> [!div class="checklist"]
> - Determine which firewall SKUs to deploy
> - Determine if some resources don't need 100% allocation
> - Determine where you can optimize firewall use across workloads
> - Monitor firewall usage to determine cost effectiveness
> - Review Firewall Manager capabilities to determine potential operational efficiency
> - Determine the number of Public IP addresses required

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| Deploy the Basic and Premium SKUs where appropriate.| The Standard option is usually enough for east-west traffic, where Premium comes with the necessary additional features for north-south traffic, as well as the forced tunneling feature and many other features. For more information, see [Azure Firewall Premium Preview features](/azure/firewall/premium-features). Deploy mixed scenarios using the Standard and Premium options, according to your needs.|
|Stop Azure Firewall deployments that do not need to run for 24 hours.|You may have development environments that are used only during business hours. For more details, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
|Share the same Azure Firewall across multiple workloads and Azure Virtual Networks.|You can use a central Azure Firewall in the hub virtual network, and share the same Firewall across many spoke virtual networks that are connected to the same hub from the same region.<br>Ensure that there is no unexpected cross-region traffic as part of the hub-spoke topology.|
|Review underutilized Azure Firewall instances, and identify and delete Azure Firewall deployments not in use. | To identify Azure Firewall deployments not in use, start analyzing the Monitoring Metrics and User Defined Routes (UDRs) that are associated with subnets pointing to the Firewall's private IP. Then, combine that with additional validations, such as if the Azure Firewall has any Rules (Classic) for NAT, or Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, as well as with internal documentation about your environment and deployments. <br><br> You can detect  deployments that are cost effective over time. <br><br> For more details about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
|Use Azure Firewall Manager and its policies to reduce your operational costs, increasing your efficiency, and reduce your management overhead.|Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.<br><br>For more information, see [Pricing - Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
|Delete unused public IP addresses and use IP Groups to reduce your management overhead.|Validate whether all the associated Public IP addresses are in use. If they are not in use, disassociate and delete them. Use IP Groups to reduce your management overhead. Evaluate SNAT ports utilization before you remove any IP Addresses.<br><br>You will only use the number of Public IPs that your firewall actually needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [Operational excellence design principles](../../devops/principles.md).

> [!div class="checklist"]
> - Use logs for monitoring
> - Use tags when possible allow traffic through the firewall
> - Use Workbooks
> - Use Azure Firewall connector in Azure Sentinel

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall using firewall logs or workbooks. You can also use activity logs to audit operations on Azure Firewall resources.|
|Use Fully Qualified Domain Name (FQDN) tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Use Workbooks in Azure Log Analytics|Helps you visualize firewall logs.|
|Enable Azure Firewall connector in Azure Sentinel.|You can use Azure Sentinel to create detections and logic apps for Azure Firewall.|
|Migrate Azure Firewall rules to Azure Firewall Manager policies for existing deployments|For existing deployments, migrate Azure Firewall rules to Azure Firewall Manager policies, and use Azure Firewall Manager to centrally manage your firewalls and policies.|
|Monitoring capacity metrics as indicators of utilization of provisioned Azure Firewall capacity.|Alerts can be set as needed by the customers, to get notifications once a threshold has been reached for any metric.<br>See the details about monitoring logs and metrics at [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and a list of capacity metrics to monitor at [](/azure/architecture/networking/guide/well-architected-framework-azure-firewall#monitoring-capacity-metrics)|
|Monitor other Azure Firewall logs and metrics for troubleshooting and set alerts.|Azure Firewall exposes a few other logs and metrics for troubleshooting that can be used as indicators of issues. We recommend evaluating alerts, as per the list below.<br><ul><li>Application rule log: Each new connection that matches one of your configured application rules will result in a log for the accepted/denied connection.</li><li>Network rule log:	Each new connection that matches one of your configured network rules will result in a log for the accepted/denied connection.</li><li>DNS Proxy log: This log tracks DNS messages to a DNS server that is configured using a DNS proxy.</li></ul>|
|Leverage diagnostics logs and policy analytics|Diagnostic logs allow you to view Azure Firewall logs, performance logs, and access logs. You can use these logs in Azure to manage and troubleshoot your Azure Firewall instance.<br><br>Policy analytics for Azure Firewall Manager allows you to start seeing rules and flows that match the rules and hit count for those rules. By watching what rule is in use and the traffic that's being matched, you can have full visibility of the traffic.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner.

### Design checklist

As you make design choices for Azure Firewall, review the [Performance efficiency principles](../../scalability/principles.md).

> [!div class="checklist"]
> - Determine your SNAT port requirements and if you should deploy a NAT gateway
> - Plan load tests to test auto scale performance in your environment
> - Plan network rule requirements and opportunities to summarize IP ranges.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for Performance efficiency.

| Recommendation | Benefit |
|--------|----|
| If you'll need more than 512k SNAT ports, deploy a NAT Gateway with Azure Firewall. | With a NAT Gateway, you can scale up to +1M ports. For more information, see [Scale SNAT ports with Azure NAT Gateway](/azure/firewall/integrate-with-nat-gateway)|
|Create initial traffic that is not part of your load tests 20 minutes prior to the test. Use diagnostics settings to capture scale-up and scale-down events.|Allows the Azure Firewall instance to scale up its instances to the maximum. |
|Use IP Groups to summarize IP address ranges.|You can use IP Groups to summarize IP ranges so you do not exceed 10k network rules. For each rule, Azure multiplies ports x IP addresses. So if you have one rule with four IP address ranges and five ports, you will actually consume 20 network rules.
|Configure Azure Firewall subnet (AzureFirewallSubnet) with a /26 address space|An Azure Firewall is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the Azure Firewall. Azure Firewall will provision more capacity as it scales.<br>A /26 address space for its subnets ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall does not need a subnet bigger than /26, and the Azure Firewall subnet name must be AzureFirewallSubnet|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Firewall.

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

## Next steps

- Deploy an Azure Firewall to see how it works: [Tutorial: Deploy and configure Azure Firewall and policy using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)

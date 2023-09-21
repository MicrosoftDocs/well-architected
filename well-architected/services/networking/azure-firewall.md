---
title: Azure Well-Architected Framework review - Azure Firewall
description: This article provides architectural best practices for Azure Firewall.
author: igorpag
ms.author: igorpag
ms.reviewer: tozimmergren
ms.custom: engagement-fy23
ms.topic: conceptual
ms.date: 09/15/2023
products: azure-firewall
---

# Azure Well-Architected Framework review - Azure Firewall

This article provides architectural best practices for Azure Firewall. The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you have working knowledge of Azure Firewall and are well versed with its features. For more information, see [Azure Firewall Overview](https://learn.microsoft.com/azure/firewall/overview).

## Prerequisites

- Understanding the Azure Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. Review your workload by using the [Well-Architected Framework review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. Start with [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app) and [Implement a secure hybrid network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?tabs=portal).

## Reliability

To learn how Azure Firewall supports workloads reliably, see the following articles:

- [Introduction to Azure Firewall](/training/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with availability zones](/azure/firewall/deploy-template)
- [Configure Azure Firewall in a Virtual WAN hub](https://learn.microsoft.com/azure/virtual-wan/howto-firewall)

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../resiliency/principles.md) for reliability.

> [!div class="checklist"]

> - Deploy Azure Firewall in hub virtual networks or as part of Azure Virtual WAN hubs.
> - Leverage Availability Zones resiliency.  
> - Create Azure Firewall Policy structure.
> - Review the Known Issue list.
> - Monitor Azure Firewall health state.

> [!NOTE]
> There are differences in the availability of network services between the traditional Hub & Spoke model and Virtual WAN managed secured hubs. For example, in a Virtual WAN Hub the Azure Firewall Public IP cannot be taken from a Public IP Prefix and cannot have DDoS Protection enabled. Selection of one or the other model must consider requirements across all five pillars of the Well-Architected Framework.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for reliability.

| Recommendation | Benefit |
|--------|----|
|Use [Azure Firewall Manager](https://learn.microsoft.com/azure/firewall-manager/vhubs-and-vnets) with traditional Hub & Spokes or Azure Virtual WAN network topologies to deploy and manage instances of Azure Firewall. |Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection. <br><br> For more information on network topologies, see the [Azure Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity) documentation. |
|Create Azure Firewall [Policies](https://learn.microsoft.com/azure/firewall-manager/policy-overview) to govern the security posture across global network environments. Assign policies to all instances of Azure Firewall.| Azure Firewall Policies can be arranged in an [hierarchical structure](https://learn.microsoft.com/azure/firewall-manager/rule-hierarchy) to overlay a central base policy. Allow for granular policies to meet the requirements of specific regions. Delegate incremental firewall policies to local security teams through role-based access control (RBAC). Some settings are specific per instance, for example DNAT Rules and DNS configuration, then multiple specialized policies may be required. |
|Migrate Azure Firewall [Classic Rules](https://learn.microsoft.com/azure/firewall-manager/policy-overview#classic-rules-and-policies) to Azure Firewall Manager Policies for existing deployments.|For existing deployments, migrate Azure Firewall rules to Azure Firewall Manager policies. Use Azure Firewall Manager to centrally manage your firewalls and policies. <br><br> For more information, see [Migrate to Azure Firewall Premium](https://learn.microsoft.com/azure/firewall/premium-migrate). |
| Review the list of Azure Firewall *Known Issues*. | Azure Firewall Product Group maintains an updated list of known-issues at [this location](https://learn.microsoft.com/azure/firewall/overview#known-issues). This list contains important information related to by-design behavior, fixes under construction, platform limitations, along with possible workarounds or mitigation. |
| Ensure your Azure Firewall Policy adhere to Azure Firewall limits and recommendations. | There are limits on the policy structure, including numbers of Rules and Rule Collection Groups, total policy size, source/target destinations. Be sure to compose your policy and stay behind the [documented thresholds](https://learn.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits). |
|Deploy Azure Firewall across multiple availability zones for higher service-level agreement (SLA).| Azure Firewall provides different SLAs when it's deployed in a single availability zone and when it's deployed in [multiple zones](https://learn.microsoft.com/azure/reliability/availability-zones-overview#availability-zones). For more information, see [SLA for Azure Firewall](https://www.azure.cn/support/sla/azure-firewall/). For information about all Azure SLAs, see [SLA summary for Azure services](https://azure.microsoft.com/support/legal/sla/summary).|
|In multi-region environments, deploy an Azure Firewall instance per region.| For traditional Hub & Spokes architectures, multi-region details are explained in [this article](https://learn.microsoft.com/azure/firewall/firewall-multi-hub-spoke). For secured virtual hubs (Azure Virtual WAN), [Routing Intent and Policies](https://learn.microsoft.com/azure/virtual-wan/how-to-routing-policies) must be configured to secure inter-hub and branch-to-branch communications. For workloads designed to be resistant to failures and fault tolerant, remember to consider that instances of Azure Firewall and Azure Virtual Network as regional resources. |
| Monitor Azure Firewall *Metrics* and *Resource Health* state. | Closely monitor [key metrics](https://learn.microsoft.com/azure/firewall/logs-and-metrics#metrics) indicator of Azure Firewall health state such as *Throughput*, *Firewall health state*, *SNAT port utilization* and *AZFW Latency Probe* metrics. Additionally, Azure Firewall now integrates with [Azure Resource Health](https://learn.microsoft.com/azure/service-health/resource-health-overview). With the Azure Firewall Resource Health check, you can now view the health status of your Azure Firewall and address service problems that may affect your Azure Firewall resource. |

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. [Azure Firewall](/azure/firewall/) is an intelligent firewall security service that provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../security/security-principles.md) for security.

> [!div class="checklist"]

> - Determine if you need *Forced Tunneling*.
> - Create rules for Policies based on least privilege criteria.
> - Leverage *Threat Intelligence*.
> - Enable Azure Firewall *DNS proxy*.
> - Direct network traffic through Azure Firewall.
> - Determine if you want to use third-party security as a service (SECaaS) providers.
> - Protect your Azure Firewall public IP addresses with *DDoS*.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for security.

| Recommendation | Benefit |
|--------|----|
| If required to route all internet-bound traffic to a designated next hop instead of going directly to the internet, configure Azure Firewall in [forced tunneling mode](https://learn.microsoft.com/azure/firewall/forced-tunneling) (*does not apply to Azure Virtual WAN*). |Azure Firewall must have direct internet connectivity. If your **AzureFirewallSubnet** learns a default route to your on-premises network via the Border Gateway Protocol, you must configure Azure Firewall in the forced tunneling mode. Using the forced tunneling feature, you'll need another /26 address space for the Azure Firewall Management subnet. You're required to name it **AzureFirewallManagementSubnet**.<br><br>If this is an existing Azure Firewall instance that can't be reconfigured in the forced tunneling mode, create a UDR with a 0.0.0.0/0 route. Set the **NextHopType** value as **Internet**. Associate it with **AzureFirewallSubnet** to maintain internet connectivity.|
| Set the public IP address to **None** to deploy a fully private data plane when you configure Azure Firewall in the forced tunneling mode (*does not apply to Azure Virtual WAN*).|When you deploy a new Azure Firewall instance, if you enable the forced tunneling mode, you can set the public IP address to **None** to deploy a fully private data plane. However, the management plane still requires a public IP for management purposes only. The internal traffic from virtual and on-premises networks won't use that public IP. For more about forced tunneling, see [Azure Firewall forced tunneling](/azure/firewall/forced-tunneling).|
| Create rules for Firewall Policies based on least privilege access criteria. | Azure Firewall Policies can be arranged in an [hierarchical structure](https://learn.microsoft.com/azure/firewall-manager/rule-hierarchy) to overlay a central base policy. Allow for granular policies to meet the requirements of specific regions. Each policy can contains different sets of DNAT, Network and Application rules with specific priority, action and processing order. Create your rules based on [least privilege access Zero Trust principle]( https://learn.microsoft.com/security/zero-trust/zero-trust-overview#guiding-principles-of-zero-trust) . How rules are processed is explained in [this article](https://learn.microsoft.com/azure/firewall/rule-processing#rule-processing-using-firewall-policy). |
| Enable [Threat Intelligence](https://learn.microsoft.com/azure/firewall/threat-intel) on Azure Firewall in *Alert and deny* mode.| You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services, including Microsoft Defender for Cloud.
| Enable [IDPS](https://learn.microsoft.com/azure/firewall/premium-features#idps) in  *Alert* or *Alert and deny* mode. | IDPS is one of the most powerful Azure Firewall (Premium) security feature and should be enabled. Based on security and application requirements, and considering the performance impact (see the Cost section below), *Alert* or *Alert and deny* modes can be selected. |
| Enable [Azure Firewall (DNS) proxy](https://learn.microsoft.com/azure/firewall/dns-details) configuration.| Enable this feature point clients in the VNets to Azure Firewall as a DNS server.It will protect internal DNS infrastructure that will not directly accessed and exposed. Azure Firewall must be also configured to use custom DNS that will be used to forward DNS queries.|
| Configure user-defined routes (UDR) to force traffic through Azure Firewall. | In a traditional Hub & Spokes architecture, configure UDRs to force traffic through Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity. In Azure Virtual WAN, instead, configure [Routing Intent and Policies](https://learn.microsoft.com/azure/virtual-wan/how-to-routing-policies) to redirect private and/or Internet traffic through the Azure Firewall instance integrated into the hub. |
| If not possible to apply UDR, and only web traffic redirection is required, consider using Azure Firewall as an [Explicit Proxy](https://learn.microsoft.com/azure/firewall/explicit-proxy) | With explicit proxy feature enabled on the outbound path, you can configure a proxy setting on the sending web application (such as a web browser) with Azure Firewall configured as the proxy. As a result, web traffic will reach the firewall's private IP address and therefore egresses directly from the firewall without using a UDR. This feature also facilitate the usage of multiple firewalls without modifying existing network routes. |
| Configure supported third-party software as a service (SaaS) security providers within Firewall Manager if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, [third-party SECaaS offerings](https://learn.microsoft.com/azure/firewall-manager/trusted-security-partners) to protect internet access for your users. This scenario does require Azure Virtual WAN with a S2S VPN Gateway in the Hub, as it uses an IPSec tunnel to connect to the provider's infrastructure. SECaaS providers may charge additional license fees and limit throughput on IPSec connections. Alternative solutions such ZScaler Cloud Connector exist and may be more suitable.|
| Use fully qualified domain name (FQDN) filtering in network rules.|You can use FQDNs based on DNS resolution in Azure Firewall and firewall policies. This capability allows you to filter outbound traffic with any TCP/UDP protocol (including NTP, SSH, RDP, and more). You must enable the DNS Proxy option to use FQDNs in your network rules. To learn how it works, see [Azure Firewall FQDN filtering in network rules](/azure/firewall/fqdn-filtering-network-rules#how-it-works).|
| Use *Service Tags* in Network Rules to enable selective access to specific Microsoft services. | A service tag represents a group of IP address prefixes to help minimize complexity for security rule creation. Using [Service Tags](https://learn.microsoft.com/azure/firewall/service-tags) in Network Rules, it is possible to enable outbound access to specific services in Azure, Dynamics and Office 365 without opening wide ranges of IP addresses. Azure will maintain automatically the mapping between these tags and underlying IP addresses used by each service. The list of Service Tags available to Azure Firewall are listed here: [Az Firewall Service Tags](https://learn.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).|  
  | Use *FQDN Tags* in Application Rules to enable selective access to specific Microsoft services. | An [FQDN tag](https://learn.microsoft.com/azure/firewall/fqdn-tags) represents a group of fully qualified domain names (FQDNs) associated with well known Microsoft services. You can use an FQDN tag in application rules to allow the required outbound network traffic through your firewall for some [specific Azure services, Office 365, Windows 365 and Intune](https://learn.microsoft.com/azure/firewall/fqdn-tags#current-fqdn-tag). |
| Use Azure Firewall Manager to create and associate a DDoS protection plan with your hub virtual network (*does not apply to Azure Virtual WAN*).|A DDoS protection plan provides enhanced mitigation features to defend your firewall from DDoS attacks. Azure Firewall Manager is an integrated tool to create your firewall infrastructure and DDoS protection plans. For more information, see [Configure an Azure DDoS Protection Plan using Azure Firewall Manager](/azure/firewall-manager/configure-ddos). |
| Use an Enterprise PKI to generate certificates for TLS Inspection. | With Azure Firewall Premium, if [TLS Inspection](https://learn.microsoft.com/azure/firewall/premium-features#tls-inspection) feature is used, it is recommended to leverage an [internal Enterprise Certification Authority (CA)](https://learn.microsoft.com/azure/firewall/premium-certificates) for production environment. Self-signed certificates should be used for [testing/PoC purposes](https://techcommunity.microsoft.com/t5/azure-network-security-blog/building-a-poc-for-tls-inspection-in-azure-firewall/ba-p/3676723) only. |
| Zero-trust web applications with Azure Firewall and Application Gateway | If for your security requirements a strategy is required for implementing zero-trust security for web apps for inspection and encryption, it is recommended to follow this [guide](https://learn.microsoft.com/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall). In this document, how to integrate together Azure Firewall and Application Gateway will be explained, in both traditional Hub & Spoke and Virtual WAN scenarios.     |

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- [All Internet traffic should be routed via your deployed Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Azure Security Center has identified that some of your subnets aren't protected with a next generation firewall. Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next generation firewall.

- [Azure firewall policy should enable TLS inspection within application rules](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa58ac66d-92cb-409c-94b8-8e48d7a96596). Enabling TLS inspection is recommended for all application rules to detect, alert, and mitigate malicious activity in HTTPS. To learn more about TLS inspection with Azure Firewall, visit [https://aka.ms/fw-tlsinspect](https://aka.ms/fw-tlsinspect).

- [Azure Firewall Premium should configure a valid intermediate certificate to enable TLS inspection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F711c24bb-7f18-4578-b192-81a6161e1f17). Configure a valid intermediate certificate and enable Azure Firewall Premium TLS inspection to detect, alert, and mitigate malicious activity in HTTPS. To learn more about TLS inspection with Azure Firewall, visit [https://aka.ms/fw-tlsinspect](https://aka.ms/fw-tlsinspect).

- [Bypass list of Intrusion Detection and Prevention System (IDPS) should be empty in Firewall Policy Premium](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff516dc7a-4543-4d40-aad6-98f76a706b50). Intrusion Detection and Prevention System (IDPS) Bypass List allows you to not filter traffic to any of the IP addresses, ranges, and subnets specified in the bypass list. However, enabling IDPS is recommanded for all traffic flows to better identify known threats. To learn more about the Intrusion Detection and Prevention System (IDPS) signatures with Azure Firewall Premium, visit [https://aka.ms/fw-idps-signature](https://aka.ms/fw-idps-signature).

- [Firewall Policy Premium should enable all IDPS signature rules to monitor all inbound and outbound traffic flows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F610b6183-5f00-4d68-86d2-4ab4cb3a67a5). Enabling all Intrusion Detection and Prevention System (IDPS) signature rules is recommanded to better identify known threats in the traffic flows. To learn more about the Intrusion Detection and Prevention System (IDPS) signatures with Azure Firewall Premium, visit [https://aka.ms/fw-idps](https://aka.ms/fw-idps).

- [Firewall Policy Premium should enable the Intrusion Detection and Prevention System (IDPS)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6484db87-a62d-4327-9f07-80a2cbdf333a). Enabling the Intrusion Detection and Prevention System (IDPS) allows you to monitor your network for malicious activity, log information about this activity, report it, and optionally attempt to block it. To learn more about the Intrusion Detection and Prevention System (IDPS) with Azure Firewall Premium, visit [https://aka.ms/fw-idps](https://aka.ms/fw-idps).

- [Subscription should configure the Azure Firewall Premium to provide additional layer of protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2c2d0a6-e183-4fc8-bd8f-363c65d3bbbf). Azure Firewall Premium provides advanced threat protection that meets the needs of highly sensitive and regulated environments. Deploy Azure Firewall Premium to your subscription and make sure all the service traffic are protected by Azure Firewall Premium. To learn more about Azure Firewall Premium, visit [https://aka.ms/fw-premium](https://aka.ms/fw-premium).

All built-in policy definitions related to Azure networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../cost/principles.md) for cost optimization.

> [!div class="checklist"]

> - Select the best Azure Firewall *SKU* to deploy.
> - Determine if some instances don't need permanent 24x7 allocation.
> - Determine where you can optimize firewall use across workloads.
> - Monitor and optimize firewall instances usage to determine cost-effectiveness.
> - Review and optimize the number of public IP addresses required.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for cost optimization.

| Recommendation | Benefit |
|--------|----|
| Deploy the proper Azure Firewall SKU.| Azure Firewall can be deployed in three different SKUs: [Basic](https://learn.microsoft.com/azure/firewall/basic-features), [Standard](https://learn.microsoft.com/azure/firewall/features) and [Premium](https://learn.microsoft.com/azure/firewall/premium-features). Azure Firewall Premium is recommended to secure highly sensitive applications (such as payment processing). Azure Firewall Standard is recommended for customers looking for Layer 3–Layer 7 firewall and needs autoscaling to handle peak traffic periods of up to 30 Gbps. Azure Firewall Basic is recommended for SMB customers with throughput needs of 250 Mbps. If required, downgrade or upgrade is possible between Standard and Premium as documented [here](https://learn.microsoft.com/azure/firewall/easy-upgrade).  <br><br> For more information, see [Choose the right Azure Firewall SKU to meet your needs](https://learn.microsoft.com/azure/firewall/choose-firewall-sku). |
| Stop Azure Firewall deployments that don't need to run for 24 hours.|You might have development or testing environments that are used only during business hours. For more information, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
| Share the same instance of Azure Firewall across multiple workloads and Azure Virtual Networks.|You can use a central instance of Azure Firewall in the hub virtual network or Virtual WAN secure hub and share the same firewall across many spoke virtual networks that are connected to the same hub from the same region. Ensure there's no unexpected cross-region traffic as part of the hub-spoke topology.|
| Regularly review traffic processed by Azure Firewall and look for originating workload optimizations | [Top Flows](https://learn.microsoft.com/azure/firewall/enable-top-ten-and-flow-trace#top-flows) log (known in the industry as *Fat Flows*), shows the top connections that are contributing to the highest throughput through the firewall. It is recommended to regularly review traffic processed by the Azure Firewall and search for possible optimizations to reduce the amount of traffic traversing the firewall.    |
| Review under-utilized Azure Firewall instances. Identify and delete unused Azure Firewall deployments. | To identify unused Azure Firewall deployments, start by analyzing the monitoring metrics and UDRs associated with subnets pointing to the firewall's private IP. Combine that information with other validations, such as if your instance of Azure Firewall has any rules (classic) for NAT, Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, and with internal documentation about your environment and deployments. You can detect deployments that are cost-effective over time. <br><br> For more information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
| Use Azure Firewall Manager and its Policies to reduce operational costs, increase efficiency, and reduce management overhead.| Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.<br><br>For more information, see [Pricing - Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
| Delete unused public IP addresses. | Validate whether all the associated public IP addresses are in use. If they aren't in use, disassociate and delete them. Evaluate SNAT port utilization before removing any IP addresses.<br><br>You'll only use the number of public IPs your firewall needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
| Review logging requirements. | Azure Firewall has the ability to comprehensively log metadata of all traffic it sees, to Log Analytics Workspaces, Storage or third party solutions through Event Hub. However, all logging solutions incur costs for data processing and storage. At very large volumes these costs can be significant, a cost effective approach and alternative to Log Analytics described [here](https://techcommunity.microsoft.com/t5/azure-data-explorer-blog/scale-your-azure-firewall-monitoring-with-azure-data-explorer/ba-p/3611826) should be considered and cost estimated . Consider whether it is required to log traffic metadata for all logging categories and modify in Diagnostic Settings if needed. |

For more suggestions, see [Principles of the Cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. You can measure performance statistics and metrics to troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../devops/principles.md) for operational excellence.

> [!div class="checklist"]

> - Maintain inventory and backup of Azure Firewall configuration and policies.
> - Leverage diagnostic logs for firewall monitoring and troubleshooting.
> - Leverage Azure Firewall monitoring workbook.
> - Regularly review your policy insights and analytics.
> - Integrate Azure Firewall with Microsoft Defender for Cloud and Microsoft Sentinel.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for operational excellence.

| Recommendation | Benefit |
|--------|----|
| Maintain regular backups of Azure Policy artifacts. | If Infrastructure-as-Code (IaC) approach is used to maintain Azure Firewall and all dependencies then backup and versioning of Azure Firewall Policies should be already in place. If not, a [companion mechanism](https://techcommunity.microsoft.com/t5/azure-network-security-blog/backup-azure-firewall-and-azure-firewall-policy-with-logic-apps/ba-p/3613928) based on external Logic App can be deployed to automate. |
|Enable Diagnostic Logs for Azure Firewall.| [Diagnostic Logs](https://learn.microsoft.com/azure/firewall/logs-and-metrics#diagnostic-logs) is a key component for many monitoring tools and strategies for Azure Firewall and should be enabled. You can monitor Azure Firewall by using firewall logs or workbooks. You can also use activity logs for auditing operations on Azure Firewall resources.|
| Use Structured Firewall Logs format. | [Structured Firewall Logs](https://learn.microsoft.com/azure/firewall/firewall-structured-logs) are a type of log data that are organized in a specific new format. They use a predefined schema to structure log data in a way that makes it easy to search, filter, and analyze. The latest monitoring tools are based on this type of logs hence it is often a pre-requisite. Use the previous [Diagnostic Logs format](https://learn.microsoft.com/azure/firewall/logs-and-metrics#diagnostic-logs) only if there is an existing tooling pre-requisite on that. Do not enable both logging formats at the same time.  |
|Use the built-in Firewall Monitoring Workbook.| Azure Firewall portal experience now includes a new workbook under the *Monitoring* section UI, a separate installation is no more required. With the [Azure Firewall Workbook](https://techcommunity.microsoft.com/t5/azure-network-security-blog/azure-firewall-new-monitoring-and-logging-updates/ba-p/3897897), you can extract valuable insights from Azure Firewall events, delve into your application and network rules, and examine statistics regarding firewall activities across URLs, ports, and addresses. |
|Monitor key metrics and create alerts for indicators of the utilization of provisioned Azure Firewall capacity.| Alerts should be created to monitor at least *Throughput*, *Firewall health state*, *SNAT port utilization* and *AZFW Latency Probe* metrics.<br>For information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics).|
| Configure Azure Firewall integration with Microsoft Defender for Cloud and Microsoft Sentinel. | If these tools are available in the environment, it is recommended to leverage integration with [Microsoft Defender for Cloud](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/azure-network-security-using-microsoft-defender-for-cloud/ba-p/2228222) and [Microsoft Sentinel](https://www.microsoft.com/security/blog/2021/06/08/optimize-security-with-azure-firewall-solution-for-azure-sentinel/) solutions. With Microsoft Defender for Cloud integration, you can visualize the all-up status of network infrastructure and network security in one place, including Azure Network Security across all VNets and Virtual Hubs spread across different regions in Azure. Integration with Microsoft Sentinel provides threat detection and prevention capabilities. |
| Regularly review *Policy Analytics* dashboard to identify potential issues. | [Policy Analytics](https://learn.microsoft.com/azure/firewall/policy-analytics) is a new feature that provides insights into the impact of your Azure Firewall policies. It helps you identify potential issues (hitting policy limits, low utilization rules, redundant rules, rules too generic, IP Groups usage recommendation) in your policies and provides recommendations to improve your security posture and rule processing performance. |
| Become familiar with KQL (Kusto Query Language) queries to allow quick analysis and troubleshooting using Azure Firewall logs. | [Sample queries](https://learn.microsoft.com/azure/firewall/firewall-structured-logs#structured-log-queries) are provided for Azure Firewall. Those will enable you to quickly identify what's happening inside your firewall and check to see which rule was triggered, or which rule is allowing/blocking a request. |
|


Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).  

## Performance efficiency

Performance efficiency is the ability of your workload to scale to efficiently meet the demands placed on it by users.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../scalability/principles.md) for performance efficiency.

> [!div class="checklist"]

> - Regularly review and optimize firewall rules.
> - Review policy requirements and opportunities to summarize IP ranges and URLs list.
> - Assess your SNAT port requirements.
> - Plan load tests to test auto-scale performance in your environment.
> - Do not enable diagnostic tools if not required.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for performance efficiency.

| Recommendation | Benefit |
|--------|----|
| Use *Policy Analytics* dashboard to identify potential optimizations for Firewall Policies. | [Policy Analytics](https://learn.microsoft.com/azure/firewall/policy-analytics) is a new feature that provides insights into the impact of your Azure Firewall policies. It helps you identify potential issues (hitting policy limits, low utilization rules, redundant rules, rules too generic, IP Groups usage recommendation) in your policies and provides recommendations to improve your security posture and rule processing performance. |
| For Firewall Policies with large [rule sets](https://learn.microsoft.com/azure/firewall/policy-rule-sets), place the most frequently used rules early in the group to optimize latency. | Rules are processed based on rule type, inheritance, Rule Collection Group priority and Rule Collection priority. Highest priority Rule Collection Groups are processed first. Inside a rule collection group, Rule Collections with highest priority are processed first. Placing most used rules higher in rule set will optimize processing latency. How rules are processed and evaluated is explained in [this article](https://learn.microsoft.com/azure/firewall/rule-processing#rule-processing-using-firewall-policy).
| Use *IP Groups* to summarize IP address ranges.|You can use IP Groups to summarize IP ranges, so you don't exceed [the limit of unique source/destination network rules](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits). For each rule, Azure multiplies ports by IP addresses. So, if you have one rule with four IP address ranges and five ports, you'll consume 20 network rules. The IP Group is treated as a single address for the purpose of creating network rules.
| Consider *Web Categories* to allow or deny outbound access in bulk. | Instead of explicitly building and maintaining a long list of public Internet sites, consider the usage of Azure Firewall [Web Categories](https://learn.microsoft.com/azure/firewall/web-categories). This feature will dynamically categorize web content and will permit the creation of compact Application Rules. |
| Evaluate the performance impact of *IDPS* in *Alert and deny* mode. | If Azure Firewall is required to operate in [IDPS](https://learn.microsoft.com/azure/firewall/premium-features#idps) mode *Alert and deny*, carefully consider the performance impact as documented in [this page](https://learn.microsoft.com/azure/firewall/firewall-performance).|
| Assess potential SNAT port exhaustion problem. | Azure Firewall currently supports 2496 ports per Public IP address per backend Virtual Machine Scale Set instance. By default, there are two Virtual Machine Scale Set instances. So, there are 4992 ports per flow destination IP, destination port and protocol (TCP or UDP). The firewall scales up to a maximum of 20 instances. You can work around the limits by configuring Azure Firewall deployments with a minimum of five public IP addresses for deployments susceptible to SNAT exhaustion.  |
| Properly warm-up Azure Firewall before any performance test. | Create initial traffic that isn't part of your load tests 20 minutes before the test. Use diagnostics settings to capture scale-up and scale-down events. You can use the [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) service to generate the initial traffic. Allows the Azure Firewall instance to scale up its instances to the maximum. |
| Configure an Azure Firewall subnet (AzureFirewallSubnet) with a /26 address space.|Azure Firewall is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the instance of Azure Firewall. Azure Firewall provisions more capacity as it scales.<br>A /26 address space for its subnets ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall doesn't need a subnet bigger than /26. The Azure Firewall subnet name must be **AzureFirewallSubnet**.|
| Do not enable advanced logging if not required | Azure Firewall provides some advanced logging capabilities that can be expensive to maintain always active. Instead, they should be used for troubleshooting purposes only, and limited in duration, then disabled when no more necessary. For example, [Top Flows](https://learn.microsoft.com/azure/azure-monitor/reference/tables/azfwfatflow) and [Flow Trace](https://learn.microsoft.com/azure/azure-monitor/reference/tables/azfwflowtrace) logs are expensive can cause excessive CPU and storage usage on the Azure Firewall infrastructure. |

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. There is no Azure Firewall specific Advisor recommendation yet. Some general recommendations can be applied to help improving the reliability, security, cost-effectiveness, performance, and operational excellence.

- [Create Azure Service Health alerts to be notified when Azure problems affect you](/azure/advisor/advisor-high-availability-recommendations#create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you)
- [Ensure you have access to Azure cloud experts when you need it](/azure/advisor/advisor-operational-excellence-recommendations#ensure-you-have-access-to-azure-cloud-experts-when-you-need-it)
- [Enable Traffic Analytics to view insights into traffic patterns across Azure resources](/azure/advisor/advisor-reference-operational-excellence-recommendations#enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources)
- [Follow just enough administration (least privilege principle)](/security/benchmark/azure/baselines/advisor-security-baseline?toc=/azure/advisor/toc.json#pa-7-follow-just-enough-administration-least-privilege-principle)
- [Protect your network resources with Microsoft Defender for Cloud](/azure/defender-for-cloud/protect-network-resources)

## Additional resources

- [Azure Firewall documentation](/azure/firewall)
- [What is Azure Firewall Manager?](https://learn.microsoft.com/azure/firewall-manager/overview)
- [Azure Firewall service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits)
- [Azure security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline)

### Azure Architecture Center guidance

- [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls)
- [Use Azure Firewall to help protect an Azure Kubernetes Service (AKS) cluster](/azure/architecture/example-scenario/aks-firewall/aks-firewall)
- [Use Azure Firewall to protect Azure Virtual Desktop (AVD) deployments](https://learn.microsoft.com/azure/firewall/protect-azure-virtual-desktop)
- [Use Azure Firewall to protect Office 365](https://learn.microsoft.com/azure/firewall/protect-office-365)
- [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli)
- [Zero-trust network for web applications with Azure Firewall and Application Gateway](https://learn.microsoft.com/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall)
- [Implement a secure hybrid network](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)
- [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app)

## Next step

Deploy an instance of Azure Firewall to see how it works:

- [Deploy and configure Azure Firewall and policy by using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)
- [Configure Azure Firewall in a Virtual WAN hub](https://learn.microsoft.com/azure/virtual-wan/howto-firewall)

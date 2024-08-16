---
title: Azure Well-Architected Framework perspective on Azure Firewall
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Firewall.
author: igorpag
ms.author: igorpag
ms.reviewer: tozimmergren
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-service-guide
ms.date: 07/20/2024
products: azure-firewall
azure.category:
  - networking
---

# Azure Well-Architected Framework perspective on Azure Firewall

Azure Firewall is a cloud-native and intelligent network firewall security service that provides the best of breed threat protection for your cloud workloads that run in Azure. It's a fully stateful, managed firewall service with built-in high availability and unrestricted cloud scalability. Azure Firewall provides both east-west and north-south traffic inspection.

This article assumes that as an architect, you've reviewed the [virtual network security options](/azure/architecture/example-scenario/gateway/firewall-application-gateway) and chosen Azure Firewall as the network security service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Firewall and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations:
> [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app).

### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:  

- Azure Firewall
- Azure Firewall Manager

## Reliability

The purpose of the Reliability pillar is to provide continued
functionality by **building enough resilience and the ability
to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles)
provide a high-level design strategy applied for individual components,
system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the
[design review checklist for Reliability](../reliability/checklist.md). Determine
its relevance to your business requirements while keeping in mind the
policies and the type of architecture that you use. Extend the strategy to include more approaches
as needed.

> [!div class="checklist"]
> 
> - **Review the list of Azure Firewall *known issues*.** Azure Firewall Product Group maintains an updated list of [known issues](/azure/firewall/overview#known-issues). This list contains important information related to by-design behavior, fixes under construction, platform limitations, and possible workarounds or mitigation strategies.
>
>  - **Ensure your Azure Firewall Policy adheres to Azure Firewall limits and recommendations.** There are limits on the policy structure, including the number of rules and rule collection groups, total policy size, source destinations, and target destinations. Be sure to compose your policy and stay below the [documented thresholds](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits).
> - **Deploy Azure Firewall across multiple availability zones** for higher service-level agreement (SLA). Azure Firewall provides different SLAs when you deploy it in a single availability zone compared to [multiple zones](/azure/reliability/availability-zones-overview#availability-zones). For more information, see [SLA for Azure Firewall](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services).
> - **Deploy an Azure Firewall instance per region** in multi-region environments. For traditional hub-and-spoke architectures, see [multi-region considerations](/azure/firewall/firewall-multi-hub-spoke). For secured virtual hubs (Azure Virtual WAN), configure [routing intent and policies](/azure/virtual-wan/how-to-routing-policies) to secure inter-hub and branch-to-branch communications. For failure-resistant and fault-tolerant workloads, consider instances of Azure Firewall and Azure Virtual Network as regional resources.
> - **Monitor Azure Firewall Metrics and Resource Health state.** Closely monitor [key metrics](/azure/firewall/logs-and-metrics#metrics) that indicate the Azure Firewall health state, such as *Throughput*, *Firewall health state*, *SNAT port utilization*, and *AZFW Latency Probe* metrics. Azure Firewall also integrates with [Azure Resource Health](/azure/service-health/resource-health-overview). Use the Azure Firewall Resource Health check to view the health status of Azure Firewall and address service problems that might affect your Azure Firewall resource.
> - **Deploy Azure Firewall in hub virtual networks or as part of Virtual WAN hubs.** 

> [!NOTE]
> There are differences in the availability of network services between the traditional hub-and-spoke model and the Virtual WAN-managed secured hubs model. For example, in a Virtual WAN hub, the Azure Firewall public IP can't be taken from a public IP prefix and can't have Azure DDoS Protection enabled. When you choose your model, consider your requirements across all five pillars of the Well-Architected Framework.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for reliability.

| Recommendation | Benefit |
|--------|----|
|Deploy Azure Firewall across [multiple availability zones](/azure/firewall/deploy-availability-zone-powershell) for higher service-level agreement (SLA).| When you deploy across multiple availability zones, Azure Firewall can operate even if a zone experiences an outage.|
| Monitor [Azure Firewall metrics in a Log Analytics workspace](/azure/firewall/metrics#configure-metrics-to-a-log-analytics-workspace). <br><br>Monitor [Azure Firewall health with Azure Service Health](/azure/service-health/resource-health-alert-arm-template-guide). | Monitor resource metrics and service health to detect problems early and help prevent failures. |

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Firewall.

### Design checklist

Start your design strategy based on the
[design review checklist for Security](../security/checklist.md). Identify
vulnerabilities and controls to improve the security posture. Extend the
strategy to include more approaches as needed.

> [!div class="checklist"]
> - **Review the Zero Trust configuration guide** for Azure Firewall and Application Gateway. 
> - **Create Azure Firewall [policies](/azure/firewall-manager/policy-overview)** to govern the security posture across global network environments. Assign policies to all instances of Azure Firewall.
>
> - **Create rules for Firewall policies** based on least-privilege access criteria. Use Fully Qualified Domain Name (FQDN) filtering in network rules. Use *service tags* in network rules to enable selective access to specific Microsoft services. Use *FQDN tags* in application rules to enable selective access to specific Microsoft services.
> - **Determine if you need forced tunneling.** If required to route all internet-bound traffic to a designated next hop instead of going directly to the internet, configure Azure Firewall in [forced tunneling mode](/azure/firewall/forced-tunneling) (*does not apply to Virtual WAN*). Set the public IP address to None to deploy a fully private data plane when you configure Azure Firewall in the forced tunneling mode (*does not apply to Virtual WAN*).
> - **Enable Threat Intelligence** on Azure Firewall in Alert and deny mode.
> - **Enable [IDPS](/azure/firewall/premium-features#idps)** in  *Alert* or *Alert and deny* mode.
> - **Enable [Azure Firewall (DNS) proxy](/azure/firewall/dns-details) configuration.**
> - **Configure user-defined routes (UDRs)** to force traffic through Azure Firewall.
> - **Restrict usage of [public IP addresses](/azure/virtual-network/ip-services/associate-public-ip-address-vm)** directly tied to virtual machines.
> - If not possible to apply UDR, and only web traffic redirection is required, consider using Azure Firewall as an [explicit proxy](/azure/firewall/explicit-proxy).
> - **Configure supported third-party software as a service (SaaS) security providers** within Firewall Manager if you want to use these solutions to protect outbound connections.
> - **Use Azure Firewall Manager to create and associate a DDoS protection plan** with your hub virtual network (*does not apply to Virtual WAN*).
> - **Use an Enterprise PKI** to generate certificates for TLS Inspection.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for security.

| Recommendation | Benefit |
|--------|----|
|Create Azure Firewall [policies](/azure/firewall-manager/policy-overview) to govern the security posture across global network environments. Assign policies to all instances of Azure Firewall.| Azure Firewall Policies can be arranged in an [hierarchical structure](/azure/firewall-manager/rule-hierarchy) to overlay a central base policy. Allow for granular policies to meet the requirements of specific regions. Delegate incremental firewall policies to local security teams through role-based access control (RBAC). Some settings are specific per instance, for example DNAT Rules and DNS configuration, then multiple specialized policies might be required. |
| If required to route all internet-bound traffic to a designated next hop instead of going directly to the internet, configure Azure Firewall in [forced tunneling mode](/azure/firewall/forced-tunneling) (*does not apply to Virtual WAN*). |Force tunneling enables you avoid exposing your Azure resources directly to the internet, reducing the attack surface and minimizes the risk of external threats. You can route all internet-bound traffic through an on-premises firewall or a network virtual appliance (NVA) to enforce corporate policies and compliance requirements more effectively.|
| Enable [Threat Intelligence](/azure/firewall/threat-intel) on Azure Firewall in *Alert and deny* mode.| This feature leverages Microsoft’s extensive threat intelligence feed to automatically alert and block traffic from known malicious IP addresses, domains, and URLs. By doing so, it provides real-time protection against emerging threats, reducing the risk of cyberattacks. |
| Enable [IDPS](/azure/firewall/premium-features#idps) in  *Alert* or *Alert and deny* mode. | Enabling Intrusion Detection and Prevention System (IDPS) filtering in Azure Firewall provides real-time monitoring and analysis of network traffic to detect and prevent malicious activities. By leveraging signature-based detection, it can swiftly identify known threats and block them before they cause harm.  |
| Enable [Azure Firewall (DNS) proxy](/azure/firewall/dns-details) configuration.| Enabling this feature points clients in the VNets to Azure Firewall as a DNS server. It will protect internal DNS infrastructure that will not be directly accessed and exposed. |
| Configure supported third-party software as a service (SaaS) security providers within Firewall Manager if you want to use these solutions to protect outbound connections.|Enabling third-party security providers in Azure Firewall allows you to leverage best-in-breed security-as-a-service (SECaaS) offerings, providing advanced protection for your internet traffic. These providers offer specialized, user-aware filtering and comprehensive threat detection capabilities that enhance your overall security posture. |
| Use Fully Qualified Domain Name (FQDN) filtering in network rules.| Using Fully Qualified Domain Names (FQDNs) in Azure Firewall network rules significantly simplifies management by allowing administrators to manage domain names instead of multiple IP addresses. This dynamic resolution ensures that firewall rules automatically update when domain IPs change. |
| Use *Service Tags* in Network Rules to enable selective access to specific Microsoft services. | Using service tags in Azure Firewall network rules simplifies security management by allowing you to define access controls based on service names rather than specific IP addresses. Microsoft manages and updates these tags automatically as IP addresses change, this ensures that your firewall rules remain accurate and effective without manual intervention. |  
| Use *FQDN Tags* in Application Rules to enable selective access to specific Microsoft services. |  Using Fully Qualified Domain Names (FQDNs) in Azure Firewall application rules significantly simplifies management by allowing administrators to manage domain names instead of multiple IP addresses. This dynamic resolution ensures that firewall rules automatically update when domain IPs change. |
| Use Azure Firewall Manager to create and associate a DDoS protection plan with your hub virtual network (*does not apply to Virtual WAN*).|Configuring an Azure DDoS Protection Plan using Azure Firewall Manager streamlines the management of your network security by allowing you to centrally manage DDoS protection alongside your firewall policies, simplifying the deployment and monitoring processes. |
| Review the Zero Trust configuration guide for Azure Firewall and Application Gateway. | If your security requirements necessitate implementing a Zero-Trust approach for web applications (inspection and encryption), it is recommended to follow this [guide](/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall). In this document, how to integrate together Azure Firewall and Application Gateway will be explained, in both traditional Hub & Spoke and Virtual WAN scenarios. |


Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Firewall and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - Select the Azure Firewall *SKU* to deploy.
> - Determine if some instances don't need permanent 24x7 allocation.
> - Determine where you can optimize firewall use across workloads.
> - Monitor and optimize firewall instances usage to determine cost-effectiveness.
> - Review and optimize the number of public IP addresses required and Policies used.
> - Review logging requirements, estimate cost and control over time.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for cost optimization.

| Recommendation | Benefit |
|--------|----|
| Deploy the proper Azure Firewall SKU.| Azure Firewall can be deployed in three different SKUs: [Basic](/azure/firewall/basic-features), [Standard](/azure/firewall/features) and [Premium](/azure/firewall/premium-features). Azure Firewall Premium is recommended to secure highly sensitive applications (such as payment processing). Azure Firewall Standard is recommended for customers looking for Layer 3–Layer 7 firewall and needs autoscaling to handle peak traffic periods of up to 30 Gbps. Azure Firewall Basic is recommended for SMB customers with throughput needs of 250 Mbps. If required, downgrade or upgrade is possible between Standard and Premium as documented [here](/azure/firewall/easy-upgrade).  <br><br> For more information, see [Choose the right Azure Firewall SKU to meet your needs](/azure/firewall/choose-firewall-sku). |
| Stop Azure Firewall deployments that don't need to run for 24x7.|You might have development or testing environments that are used only during business hours. For more information, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
| Share the same instance of Azure Firewall across multiple workloads and Azure Virtual Networks.|You can use a central instance of Azure Firewall in the hub virtual network or Virtual WAN secure hub and share the same firewall across many spoke virtual networks that are connected to the same hub from the same region. Ensure there's no unexpected cross-region traffic as part of the hub-spoke topology.|
| Regularly review traffic processed by Azure Firewall and look for originating workload optimizations | [Top Flows](/azure/firewall/enable-top-ten-and-flow-trace#top-flows) log (known in the industry as *Fat Flows*), shows the top connections that are contributing to the highest throughput through the firewall. It is recommended to regularly review traffic processed by the Azure Firewall and search for possible optimizations to reduce the amount of traffic traversing the firewall.    |
| Review under-utilized Azure Firewall instances. Identify and delete unused Azure Firewall deployments. | To identify unused Azure Firewall deployments, start by analyzing the monitoring metrics and UDRs associated with subnets pointing to the firewall's private IP. Combine that information with other validations, such as if your instance of Azure Firewall has any rules (classic) for NAT, Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, and with internal documentation about your environment and deployments. You can detect deployments that are cost-effective over time. <br><br> For more information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
| Use Azure Firewall Manager and its Policies to reduce operational costs, increase efficiency, and reduce management overhead.| Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.<br><br>For more information, see [Pricing - Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
| Delete unused public IP addresses. | Validate whether all the associated public IP addresses are in use. If they aren't in use, disassociate and delete them. Evaluate SNAT port utilization before removing any IP addresses.<br><br>You'll only use the number of public IPs your firewall needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
| Review logging requirements. | Azure Firewall has the ability to comprehensively log metadata of all traffic it sees, to Log Analytics Workspaces, Storage or third party solutions through Event Hubs. However, all logging solutions incur costs for data processing and storage. At very large volumes these costs can be significant, a cost effective approach and [alternative to Log Analytics](https://techcommunity.microsoft.com/t5/azure-data-explorer-blog/scale-your-azure-firewall-monitoring-with-azure-data-explorer/ba-p/3611826) should be considered and cost estimated. Consider whether it is required to log traffic metadata for all logging categories and modify in Diagnostic Settings if needed. |

For more suggestions, see [Design review checklist for Cost Optimization](../cost-optimization/checklist.md). 

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Firewall.

> [!div class="checklist"]
> - **Use [Azure Firewall Manager](/azure/firewall-manager/vhubs-and-vnets) with traditional hub-and-spoke topologies or Virtual WAN network topologies** to deploy and manage instances of Azure Firewall. Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection. For more information about network topologies, see [Azure Cloud Adoption Framework](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity) documentation.
>
> - **Migrate Azure Firewall [classic rules](/azure/firewall-manager/policy-overview#classic-rules-and-policies)** to Azure Firewall Manager policies for existing deployments.
> - Maintain inventory and backup of Azure Firewall configuration and Policies.
> - Leverage diagnostic logs for firewall monitoring and troubleshooting.
> - Leverage Azure Firewall Monitoring workbook.
> - Regularly review your Policy insights and analytics.
> - Integrate Azure Firewall with Microsoft Defender for Cloud and Microsoft Sentinel.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for operational excellence.

| Recommendation | Benefit |
|--------|----|
|Migrate Azure Firewall [classic rules](/azure/firewall-manager/policy-overview#classic-rules-and-policies) to Azure Firewall Manager policies for existing deployments.|For existing deployments, migrate Azure Firewall rules to Azure Firewall Manager policies. Use Azure Firewall Manager to centrally manage your firewalls and policies. <br><br> For more information, see [Migrate to Azure Firewall Premium](/azure/firewall/premium-migrate). |
| Do not use Azure Firewall for intra-VNet traffic control. | Azure Firewall should be used to control traffic across VNets, between VNets and on-premises networks, outbound traffic to the Internet and incoming non-HTTP/s traffic. For intra-VNet traffic control, it is recommended to use [Network Security Groups](/azure/virtual-network/network-security-groups-overview). |
| Maintain regular backups of Azure Policy artifacts. | If Infrastructure-as-Code (IaC) approach is used to maintain Azure Firewall and all dependencies then backup and versioning of Azure Firewall Policies should be already in place. If not, a [companion mechanism](https://techcommunity.microsoft.com/t5/azure-network-security-blog/backup-azure-firewall-and-azure-firewall-policy-with-logic-apps/ba-p/3613928) based on external Logic App can be deployed to automate and provide an effective solution. |
|Enable *Diagnostic Logs* for Azure Firewall.| [Diagnostic Logs](/azure/firewall/logs-and-metrics#diagnostic-logs) is a key component for many monitoring tools and strategies for Azure Firewall and should be enabled. You can monitor Azure Firewall by using firewall logs or workbooks. You can also use activity logs for auditing operations on Azure Firewall resources.|
| Use *Structured Firewall Logs* format. | [Structured Firewall Logs](/azure/firewall/firewall-structured-logs) are a type of log data that are organized in a specific new format. They use a predefined schema to structure log data in a way that makes it easy to search, filter, and analyze. The latest monitoring tools are based on this type of logs hence it is often a pre-requisite. Use the previous [Diagnostic Logs format](/azure/firewall/logs-and-metrics#diagnostic-logs) only if there is an existing tool with a pre-requisite on that. Do not enable both logging formats at the same time. |
| Use the built-in *Azure Firewall Monitoring Workbook*.| Azure Firewall portal experience now includes a new workbook under the *Monitoring* section UI, a separate installation is no more required. With the [Azure Firewall Workbook](https://techcommunity.microsoft.com/t5/azure-network-security-blog/azure-firewall-new-monitoring-and-logging-updates/ba-p/3897897), you can extract valuable insights from Azure Firewall events, delve into your application and network rules, and examine statistics regarding firewall activities across URLs, ports, and addresses. |
| Monitor key metrics and create alerts for indicators of the utilization of Azure Firewall capacity.| Alerts should be created to monitor at least *Throughput*, *Firewall health state*, *SNAT port utilization* and *AZFW Latency Probe* metrics.<br>For information about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics).|
| Configure Azure Firewall integration with *Microsoft Defender for Cloud* and *Microsoft Sentinel*. | If these tools are available in the environment, it is recommended to leverage integration with [Microsoft Defender for Cloud](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/azure-network-security-using-microsoft-defender-for-cloud/ba-p/2228222) and [Microsoft Sentinel](https://azuremarketplace.microsoft.com/marketplace/apps/sentinel4azurefirewall.sentinel4azurefirewall) solutions. With Microsoft Defender for Cloud integration, you can visualize the all-up status of network infrastructure and network security in one place, including Azure Network Security across all VNets and Virtual Hubs spread across different regions in Azure. Integration with Microsoft Sentinel provides threat detection and prevention capabilities. |
| Regularly review *Policy Analytics* dashboard to identify potential issues. | [Policy Analytics](/azure/firewall/policy-analytics) is a new feature that provides insights into the impact of your Azure Firewall policies. It helps you identify potential issues (hitting policy limits, low utilization rules, redundant rules, rules too generic, IP Groups usage recommendation) in your policies and provides recommendations to improve your security posture and rule processing performance. |
| Become familiar with *KQL* (*Kusto Query Language*) queries to allow quick analysis and troubleshooting using Azure Firewall logs. | [Sample queries](/azure/firewall/firewall-structured-logs#structured-log-queries) are provided for Azure Firewall. Those will enable you to quickly identify what's happening inside your firewall and check to see which rule was triggered, or which rule is allowing/blocking a request. |
|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Firewall.

> [!div class="checklist"]
> - Regularly review and optimize firewall rules.
> - Review policy requirements and opportunities to summarize IP ranges and URLs list.
> - Assess your SNAT port requirements.
> - Plan load tests to test auto-scale performance in your environment.
> - Do not enable diagnostic tools and logging if not required.

### Recommendations

Explore the following table of recommendations to optimize your Azure Firewall configuration for performance efficiency.

| Recommendation | Benefit |
|--------|----|
| Use *Policy Analytics* dashboard to identify potential optimizations for Firewall Policies. | [Policy Analytics](/azure/firewall/policy-analytics) is a new feature that provides insights into the impact of your Azure Firewall policies. It helps you identify potential issues (hitting policy limits, low utilization rules, redundant rules, rules too generic, IP Groups usage recommendation) in your policies and provides recommendations to improve your security posture and rule processing performance. |
| For Firewall Policies with large [rule sets](/azure/firewall/policy-rule-sets), place the most frequently used rules early in the group to optimize latency. | Rules are processed based on rule type, inheritance, Rule Collection Group priority and Rule Collection priority. Highest priority Rule Collection Groups are processed first. Inside a rule collection group, Rule Collections with highest priority are processed first. Placing most used rules higher in rule set will optimize processing latency. How rules are processed and evaluated is explained in [this article](/azure/firewall/rule-processing#rule-processing-using-firewall-policy).
| Use *IP Groups* to summarize IP address ranges.|You can use IP Groups to summarize IP ranges, so you don't exceed [the limit of unique source/destination network rules](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits). For each rule, Azure multiplies ports by IP addresses. So, if you have one rule with four IP address ranges and five ports, you'll consume 20 network rules. The IP Group is treated as a single address for the purpose of creating network rules.
| Consider *Web Categories* to allow or deny outbound access in bulk. | Instead of explicitly building and maintaining a long list of public Internet sites, consider the usage of Azure Firewall [Web Categories](/azure/firewall/web-categories). This feature will dynamically categorize web content and will permit the creation of compact Application Rules. |
| Evaluate the performance impact of *IDPS* in *Alert and deny* mode. | If Azure Firewall is required to operate in [IDPS](/azure/firewall/premium-features#idps) mode *Alert and deny*, carefully consider the performance impact as documented in [this page](/azure/firewall/firewall-performance).|
| Assess potential [SNAT port exhaustion problem](/azure/firewall/overview#known-issues). | Azure Firewall currently supports 2496 ports per Public IP address per backend Virtual Machine Scale Set instance. By default, there are two Virtual Machine Scale Set instances. So, there are 4992 ports per flow destination IP, destination port and protocol (TCP or UDP). The firewall scales up to a maximum of 20 instances. You can work around the limits by configuring Azure Firewall deployments with a minimum of five public IP addresses for deployments susceptible to SNAT exhaustion.  |
| Properly warm up Azure Firewall before any performance test. | Create initial traffic that isn't part of your load tests 20 minutes before the test. Use diagnostics settings to capture scale-up and scale-down events. You can use the [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) service to generate the initial traffic. Allows the Azure Firewall instance to scale up its instances to the maximum. |
| Configure an Azure Firewall subnet (AzureFirewallSubnet) with a /26 address space.|Azure Firewall is a dedicated deployment in your virtual network. Within your virtual network, a dedicated subnet is required for the instance of Azure Firewall. Azure Firewall provisions more capacity as it scales.<br>A /26 address space for its subnets ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall doesn't need a subnet bigger than /26. The Azure Firewall subnet name must be **AzureFirewallSubnet**.|
| Do not enable advanced logging if not required | Azure Firewall provides some advanced logging capabilities that can be expensive to maintain always active. Instead, they should be used for troubleshooting purposes only, and limited in duration, then disabled when no more necessary. For example, [Top flows and Flow trace](/azure/firewall/enable-top-ten-and-flow-trace) logs are expensive can cause excessive CPU and storage usage on the Azure Firewall infrastructure. |

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Firewall and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- [Network interfaces should not have public IPs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a86a26-fd1f-447c-b59d-e51f44264114). This policy denies the network interfaces which are configured with any public IP. Public IP addresses allow internet resources to communicate inbound to Azure resources, and Azure resources to communicate outbound to the internet.

- [All Internet traffic should be routed via your deployed Azure Firewall](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Azure Security Center has identified that some of your subnets aren't protected with a next generation firewall. Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next generation firewall.

- [Azure firewall policy should enable TLS inspection within application rules](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa58ac66d-92cb-409c-94b8-8e48d7a96596). Enabling TLS inspection is recommended for all application rules to detect, alert, and mitigate malicious activity in HTTPS. To learn more about TLS inspection with Azure Firewall, visit [https://aka.ms/fw-tlsinspect](https://aka.ms/fw-tlsinspect).

- [Azure Firewall Premium should configure a valid intermediate certificate to enable TLS inspection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F711c24bb-7f18-4578-b192-81a6161e1f17). Configure a valid intermediate certificate and enable Azure Firewall Premium TLS inspection to detect, alert, and mitigate malicious activity in HTTPS. To learn more about TLS inspection with Azure Firewall, visit [https://aka.ms/fw-tlsinspect](https://aka.ms/fw-tlsinspect).

- [Bypass list of Intrusion Detection and Prevention System (IDPS) should be empty in Firewall Policy Premium](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff516dc7a-4543-4d40-aad6-98f76a706b50). Intrusion Detection and Prevention System (IDPS) Bypass List allows you to not filter traffic to any of the IP addresses, ranges, and subnets specified in the bypass list. However, enabling IDPS is recommended for all traffic flows to better identify known threats. To learn more about the Intrusion Detection and Prevention System (IDPS) signatures with Azure Firewall Premium, visit [https://aka.ms/fw-idps-signature](https://aka.ms/fw-idps-signature).

- [Firewall Policy Premium should enable all IDPS signature rules to monitor all inbound and outbound traffic flows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F610b6183-5f00-4d68-86d2-4ab4cb3a67a5). Enabling all Intrusion Detection and Prevention System (IDPS) signature rules is recommended to better identify known threats in the traffic flows. To learn more about the Intrusion Detection and Prevention System (IDPS) signatures with Azure Firewall Premium, visit [https://aka.ms/fw-idps](https://aka.ms/fw-idps).

- [Firewall Policy Premium should enable the Intrusion Detection and Prevention System (IDPS)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6484db87-a62d-4327-9f07-80a2cbdf333a). Enabling the Intrusion Detection and Prevention System (IDPS) allows you to monitor your network for malicious activity, log information about this activity, report it, and optionally attempt to block it. To learn more about the Intrusion Detection and Prevention System (IDPS) with Azure Firewall Premium, visit [https://aka.ms/fw-idps](https://aka.ms/fw-idps).

- [Subscription should configure the Azure Firewall Premium to provide additional layer of protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2c2d0a6-e183-4fc8-bd8f-363c65d3bbbf). Azure Firewall Premium provides advanced threat protection that meets the needs of highly sensitive and regulated environments. Deploy Azure Firewall Premium to your subscription and make sure all the service traffic are protected by Azure Firewall Premium. To learn more about Azure Firewall Premium, visit [https://aka.ms/fw-premium](https://aka.ms/fw-premium).

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Firewall](/azure/governance/policy/samples/built-in-policies#network) and other policies that might impact the security of the network.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Azure Firewall.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Related resources

- [Azure Firewall service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits)
- [Azure security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline)


Deploy an instance of Azure Firewall to see how it works:

- [Deploy and configure Azure Firewall and policy by using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)
- [Configure Azure Firewall in a Virtual WAN hub](/azure/virtual-wan/howto-firewall)

## Next steps

Consider the following articles as resources that demonstrate the
recommendations highlighted in this article.

- Use the following reference architectures as examples of how you can
  apply this article's guidance to a workload:
  - [Network-hardened web application with private connectivity to PaaS datastores](/azure/architecture/example-scenario/security/hardened-web-app)
  - [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls)
  - [Use Azure Firewall to help protect an Azure Kubernetes Service (AKS) cluster](/azure/architecture/example-scenario/aks-firewall/aks-firewall)

- Build implementation expertise by using the following product documentation:
  - [Azure Firewall documentation](/azure/firewall)
  - [What is Azure Firewall Manager?](/azure/firewall-manager/overview)

<!--additional links from the original article
- [Create Azure Service Health alerts to be notified when Azure problems affect you](/azure/advisor/advisor-high-availability-recommendations#create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you)
- [Ensure you have access to Azure cloud experts when you need it](/azure/advisor/advisor-operational-excellence-recommendations#ensure-you-have-access-to-azure-cloud-experts-when-you-need-it)
- [Enable Traffic Analytics to view insights into traffic patterns across Azure resources](/azure/advisor/advisor-reference-operational-excellence-recommendations#enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources)
- [Follow just enough administration (least privilege principle)](/security/benchmark/azure/baselines/advisor-security-baseline?toc=/azure/advisor/toc.json#pa-7-follow-just-enough-administration-least-privilege-principle)
- [Protect your network resources with Microsoft Defender for Cloud](/azure/defender-for-cloud/protect-network-resources)
-->
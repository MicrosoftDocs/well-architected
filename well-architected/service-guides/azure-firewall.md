---
title: Architecture Best Practices for Azure Firewall
description: Learn about Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure Firewall.
author: lnyswonger
ms.author: lnyswonger
ms.reviewer: lnyswonger
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
ms.date: 11/21/2025
products: azure-firewall
azure.category:
  - networking
---

# Architecture best practices for Azure Firewall

Azure Firewall is a cloud-native and intelligent network firewall security service that provides best-of-breed threat protection for your cloud workloads that run in Azure. It's a fully stateful, managed firewall service that has built-in high availability and unrestricted cloud scalability. Azure Firewall provides both east-west and north-south traffic inspection.

This article assumes that as an architect, you've reviewed the [virtual network security options](/azure/architecture/example-scenario/gateway/firewall-application-gateway) and chosen Azure Firewall as the network security service for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

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

### Workload design checklist

Start your design strategy based on the
[design review checklist for Reliability](../reliability/checklist.md). Determine
its relevance to your business requirements while keeping in mind the
policies and the type of architecture that you use. Extend the strategy to include more approaches
as needed.

> [!div class="checklist"]
> 
> - **Review the list of Azure Firewall *known issues*.** Azure Firewall products maintain an updated list of [known issues](/azure/firewall/firewall-known-issues#azure-firewall-standard). This list contains important information about by-design behavior, fixes under construction, platform limitations, and possible workarounds or mitigation strategies.
>
>  - **Ensure that your Azure Firewall policy adheres to Azure Firewall limits and recommendations.** The policy structure has limits, including the number of rules and rule collection groups, total policy size, source destinations, and target destinations. Be sure to compose your policy and stay below the [documented thresholds](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits).
> - **Deploy Azure Firewall across multiple availability zones** for a higher service-level agreement (SLA). Azure Firewall provides different SLAs depending on whether you deploy the service in a single availability zone or [multiple zones](/azure/reliability/availability-zones-overview#availability-zones). For more information, see [SLAs for online services](https://www.microsoft.com/licensing/docs/view/Service-Level-Agreements-SLA-for-Online-Services). For more information about reliability for Azure Firewall, see [Reliability in Azure Firewall](/azure/reliability/reliability-firewall).
> - **Deploy an Azure Firewall instance in each region** in multi-region environments. For traditional hub-and-spoke architectures, see [Multi-region considerations](/azure/firewall/firewall-multi-hub-spoke). For secured Azure Virtual WAN hubs, configure [routing intent and policies](/azure/virtual-wan/how-to-routing-policies) to secure inter-hub and branch-to-branch communications. For failure-resistant and fault-tolerant workloads, consider instances of Azure Firewall and Azure Virtual Network as regional resources.
> - **Monitor Azure Firewall metrics and the resource health state.** Azure Firewall integrates with [Azure Resource Health](/azure/service-health/resource-health-overview). Use the Resource Health check to view the health status of Azure Firewall and address service problems that might affect your Azure Firewall resource.
> - **Deploy Azure Firewall in hub virtual networks or as part of Virtual WAN hubs.** 

> [!NOTE]
> The availability of network services differs between the traditional hub-and-spoke model and the Virtual WAN-managed secured hubs model. For example, in a Virtual WAN hub, the Azure Firewall public IP can't come from a public IP prefix and can't have Azure DDoS Protection enabled. When you choose your model, consider your requirements across all five pillars of the Well-Architected Framework.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|Deploy Azure Firewall across [multiple availability zones](/azure/firewall/deploy-availability-zone-powershell).| Deploy Azure Firewall across multiple availability zones to maintain a specific level of resiliency. If one zone experiences an outage, another zone continues to serve traffic.|
|[Monitor Azure Firewall metrics](/azure/firewall/metrics#configure-metrics-to-a-log-analytics-workspace) in a Log Analytics workspace. Closely monitor metrics that indicate the Azure Firewall health state, such as *throughput*, *Firewall health state*, *SNAT port utilization*, and *AZFW latency probe* metrics. <br><br> Use Azure Service Health to [monitor Azure Firewall health](/azure/service-health/resource-health-alert-arm-template-guide). | Monitor resource metrics and service health so you can detect when a service state degrades and take proactive measures to prevent failures.|
| Enable [Resource Health](/azure/service-health/resource-health-overview) monitoring to proactively monitor Azure Firewall health state and receive notifications about potential degradations. | Provides early warning of potential issues and recommended mitigation actions, improving operational responsiveness and reducing downtime. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [**Security design principles**](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Firewall.

### Workload design checklist

Start your design strategy based on the
[design review checklist for Security](../security/checklist.md). Identify
vulnerabilities and controls to improve the security posture. Extend the
strategy to include more approaches as needed.

> [!div class="checklist"]
>- **Send all internet traffic from your workload through a firewall or a network virtual appliance (NVA)** to detect and block threats. Configure user-defined routes (UDRs) to force traffic through Azure Firewall. For web traffic, consider using Azure Firewall as an explicit proxy.
>
>   Configure supported partner software as a service (SaaS) security providers within Firewall Manager if you want to use these providers to protect outbound connections.
>
>   Restrict the usage of [public IP addresses](/azure/virtual-network/ip-services/associate-public-ip-address-vm) that are directly tied to virtual machines so that traffic can't bypass the firewall. The Azure Cloud Adoption Framework model assigns a specific Azure policy to the [CORP management group](https://github.com/Azure/Enterprise-Scale/wiki/ALZ-Policies#corp).
>
>   Follow the Zero Trust configuration guide for Azure Firewall and Application Gateway if your security needs require that you implement a Zero Trust approach for web applications, such as adding inspection and encryption. Follow this [guide](/azure/architecture/example-scenario/gateway/application-gateway-before-azure-firewall) to integrate Azure Firewall and Application Gateway for both traditional hub-and-spoke and Virtual WAN scenarios.
>
>   For more information, see [Apply firewalls at the edge](../security/networking.md#apply-firewalls-at-the-edge).
>
>- **Establish network perimeters** as part of your workload [segmentation strategy](../security/segmentation.md#enhance-with-networking-as-a-perimeter) to control the blast radius, obfuscate workload resources, and block unexpected, prohibited, and unsafe access. Create rules for Azure Firewall policies based on the [least-privilege access](/security/zero-trust/zero-trust-overview#guiding-principles-of-zero-trust) criteria.
>
>   Set the public IP address to *None* to deploy a fully private data plane when you configure Azure Firewall in forced tunneling mode. This approach doesn't apply to Virtual WAN.
>
>   Use fully qualified domain names (FQDN) and service tags when you define network rules to simplify management.
>
>- **Use detection mechanisms** to diligently monitor for threats and signs of abuse. Take advantage of platform-provided detection mechanisms and measures. Enable the intrusion detection and prevention system (IDPS). Associate an Azure DDoS Protection plan with your hub virtual network. Where possible, explore capabilities that enhance threat analysis with AI-powered security capabilities.
>
>   For more information, see [Detect abuse](../security/monitor-threats.md#detect-abuse).


### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Configure Azure Firewall in [forced tunneling mode](/azure/firewall/forced-tunneling) if you need to route all internet-bound traffic to a designated next hop instead of directly to the internet. This recommendation doesn't apply to Virtual WAN. <BR><BR> Azure Firewall must have direct internet connectivity. If your **AzureFirewallSubnet** learns a default route to your on-premises network via the Border Gateway Protocol, you must configure Azure Firewall in forced tunneling mode. You can use the forced tunneling feature to add another /26 address space for the Azure Firewall Management subnet. Name the subnet **AzureFirewallManagementSubnet**. If you have an existing Azure Firewall instance that you can't reconfigure in forced tunneling mode, create a UDR with a 0.0.0.0/0 route. Set the **NextHopType** value as **Internet**. To maintain internet connectivity, associate the UDR with **AzureFirewallSubnet**. <br><br> Set the public IP address to **None** to deploy a fully private data plane when you configure Azure Firewall in forced tunneling mode. But the management plane still requires a public IP for management purposes only. The internal traffic from virtual and on-premises networks doesn't use that public IP. | Use forced tunneling so you don't expose your Azure resources directly to the internet. This approach reduces the attack surface and minimizes the risk of external threats. To enforce corporate policies and compliance requirements more effectively, route all internet-bound traffic through an on-premises firewall or an NVA. |
| Create rules for Firewall policies in an [hierarchical structure](/azure/firewall-manager/rule-hierarchy) to overlay a central base policy. For more information, see [Use Azure Firewall policies to process rules](/azure/firewall/rule-processing#rule-processing-using-firewall-policy). <BR><BR> Create your rules based on the [least-privilege access Zero Trust principle]( /security/zero-trust/zero-trust-overview#guiding-principles-of-zero-trust)| Organize rules in a hierarchical structure so that granular policies can meet the requirements of specific regions. Each policy can contain different sets of Destination Network Address Translation (DNAT), network, and application rules that have specific priorities, actions, and processing orders. |
| Configure supported [security partner providers](/azure/firewall-manager/trusted-security-partners) within Firewall Manager to protect outbound connections. <BR><BR> This scenario requires Virtual WAN with a S2S VPN gateway in the hub because it uses an IPsec tunnel to connect to the provider's infrastructure. Managed security service providers might charge extra license fees and limit throughput on IPsec connections. You can also use alternative solutions, such as Zscaler Cloud Connector. | Enable security partner providers in Azure Firewall to take advantage of best-in-breed cloud security offerings, which provide advanced protection for your internet traffic. These providers offer specialized, user-aware filtering and comprehensive threat-detection capabilities that enhance your overall security posture. |
| Enable [Azure Firewall DNS proxy](/azure/firewall/dns-details) configuration. <BR><BR> Also configure Azure Firewall to use custom DNS for forwarding DNS queries. | Enable this feature to point clients in the virtual networks to Azure Firewall as a DNS server. This feature protects internal DNS infrastructure that's not directly accessed and exposed. |
| Configure UDRs to force traffic through Azure Firewall in a traditional hub-and-spoke architecture for *spoke-to-spoke*, *spoke-to-internet*, and *spoke-to-hybrid* connectivity. <BR><BR> In Virtual WAN, configure [routing intent and policies](/azure/virtual-wan/how-to-routing-policies) to redirect private traffic or internet traffic through the Azure Firewall instance that's integrated into the hub. <BR><BR> If you can't apply a UDR, and you only require web traffic redirection, use Azure Firewall as an [explicit proxy](/azure/firewall/explicit-proxy) on the outbound path. You can configure a proxy setting on the sending application, such as a web browser, when you configure Azure Firewall as a proxy.|Send traffic through the firewall to inspect traffic and help identify and block malicious traffic. <BR><BR> Use Azure Firewall as an explicit proxy for outbound traffic so that web traffic reaches the firewall's private IP address and therefore egresses directly from the firewall without using a UDR. This feature also facilitates the use of multiple firewalls without modifying existing network routes. |
| Use [FQDN filtering in network rules](/azure/firewall/fqdn-filtering-network-rules#how-it-works). You must enable the Azure Firewall DNS proxy configuration to use FQDNs in your network rules. | Use FQDNs in Azure Firewall network rules so that administrators can manage domain names instead of multiple IP addresses, which simplifies management. This dynamic resolution ensures that firewall rules automatically update when domain IPs change. |
| Use [Azure Firewall service tags](/azure/firewall/service-tags) in place of specific IP addresses to provide selective access to specific services in [Azure](/azure/virtual-network/service-tags-overview#available-service-tags), Microsoft Dynamics 365, and Microsoft 365. | Use service tags in network rules so you can define access controls based on service names rather than specific IP addresses, which simplifies security management. Microsoft manages and updates these tags automatically when IP addresses change. This method ensures that your firewall rules remain accurate and effective without manual intervention.|
| Use [FQDN tags](/azure/firewall/fqdn-tags) in application rules to provide selective access to specific Microsoft services. <BR><BR> You can use an FQDN tag in application rules to allow the required outbound network traffic through your firewall for [specific Azure services, such as Microsoft 365, Windows 365, and Microsoft Intune](/azure/firewall/fqdn-tags#current-fqdn-tags). | Use FQDN tags in Azure Firewall application rules to represent a group of FQDNs that are associated with well-known Microsoft services. This method simplifies the management of network security rules. |
| Enable [threat intelligence](/azure/firewall/threat-intel) on Azure Firewall in *Alert and deny* mode. | Use threat intelligence to provide real-time protection against emerging threats, which reduces the risk of cyberattacks. This feature uses the Microsoft threat intelligence feed to automatically alert and block traffic from known malicious IP addresses, domains, and URLs. |
| Enable the [IDPS](/azure/firewall/premium-features#idps) in *Alert* or *Alert and deny* mode. Consider the [performance impact](#cost-optimization) of this feature. |Enable IDPS filtering in Azure Firewall provides real-time monitoring and analysis of network traffic to detect and prevent malicious activities. This feature uses signature-based detection to swiftly identify known threats and block them before they cause harm. <br><br> For more information, see [Detect abuse](../security/monitor-threats.md#detect-abuse). |
| Use an [internal enterprise certification authority (CA)](/azure/firewall/premium-certificates) to generate certificates when you use [TLS inspection](/azure/firewall/premium-features#tls-inspection) with Azure Firewall Premium. Use self-signed certificates only for [testing and proof of concept (PoC) purposes](https://techcommunity.microsoft.com/t5/azure-network-security-blog/building-a-poc-for-tls-inspection-in-azure-firewall/ba-p/3676723). | Enable TLS inspection so that Azure Firewall Premium terminates and inspects TLS connections to detect, alert, and mitigate malicious activity in HTTPS. |
| Use Firewall Manager to create and associate an [Azure DDoS Protection plan](/azure/firewall-manager/configure-ddos) with your hub virtual network. This approach doesn't apply to Virtual WAN. | Configure an Azure DDoS Protection plan so that you can centrally manage DDoS protection alongside your firewall policies. This approach streamlines how you manage your network security and simplifies how you deploy and monitor processes. |
|Use [integration in Security Copilot](/azure/firewall/firewall-copilot) to do investigations of the malicious traffic. |This integration helps analysts perform detailed investigations of the malicious traffic intercepted by the IDPS feature.|
| Configure [explicit proxy](/azure/firewall/explicit-proxy) for outbound traffic when UDRs can't be applied. Configure proxy settings on sending applications (such as web browsers) to direct traffic through Azure Firewall's private IP address without requiring user-defined routes. | Use explicit proxy to simplify traffic routing in scenarios where traditional UDR-based routing isn't feasible, while maintaining security inspection capabilities. |
| Enable [DNS flow trace logs](/azure/firewall/monitor-firewall-reference#dns-flow-trace-logs) for end-to-end visibility into DNS traffic and name resolution. <br><br> These logs can be useful for verifying traffic forwarding paths for custom DNS configurations, or debugging DNS resolution issues that prevent applications from connecting to critical services. | Provides detailed DNS flow visibility for troubleshooting, auditing, and analyzing DNS behavior. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Firewall and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - **Select an Azure Firewall SKU to deploy.** Choose from three Azure Firewall SKUs: [Basic](/azure/firewall/basic-features), [Standard](/azure/firewall/features), and [Premium](/azure/firewall/premium-features). Use Azure Firewall Premium to secure highly sensitive applications, such as payment processing. Use Azure Firewall Standard if your workload needs a Layer 3 to Layer 7 firewall and needs autoscaling to handle peak traffic periods of up to 30 Gbps. Use Azure Firewall Basic if you use SMB and require up to 250 Mbps of throughput. You can [downgrade or upgrade between Standard and Premium SKUs](/azure/firewall/easy-upgrade). For more information, see [Choose the right Azure Firewall SKU](/azure/firewall/choose-firewall-sku).
>
> - **Remove unused firewall deployments and optimize underused deployments.** Stop Azure Firewall deployments that don't need to continuously run. Identify and delete unused Azure Firewall deployments. To reduce operational costs, monitor and optimize firewall instances usage, Azure Firewall Manager policies configuration, and the number of public IP addresses and policies that you use.
> 
> - **Share the same instance of Azure Firewall.** You can use a central instance of Azure Firewall in the hub virtual network or Virtual WAN secure hub and share the same Azure Firewall instance across spoke virtual networks that connect to the same hub from the same region. Ensure that you don't have unexpected cross-region traffic in a hub-and-spoke topology.
> - **Optimize traffic through the firewall.** Regularly review traffic that Azure Firewall processes. Find opportunities to reduce the amount of traffic that traverses the firewall. 
> - **Decrease the amount of log data that you store.** Azure Firewall can use Azure Event Hubs to comprehensively log the traffic's metadata and send it to Log Analytics workspaces, Azure Storage, or non-Microsoft solutions. All logging solutions incur costs to process data and provide storage. Large amounts of data can incur significant costs. Consider a cost-effective approach and [alternative to Log Analytics](https://techcommunity.microsoft.com/t5/azure-data-explorer-blog/scale-your-azure-firewall-monitoring-with-azure-data-explorer/ba-p/3611826), and estimate the cost. Consider whether you need to log traffic metadata for all logging categories.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Stop Azure Firewall deployments that don't need to continuously run. You might have development or testing environments that you only use during business hours. For more information, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall#example-4-deallocate-and-allocate-the-firewall). | Shut down these deployments during off-peak hours or when idle to reduce unnecessary expenses but maintain security and performance during critical times. |
| Regularly review traffic that Azure Firewall processes, and find originating workload optimizations. The [top flows log](/azure/firewall/enable-top-ten-and-flow-trace#top-flows), also known as the *fat flows log*, shows the top connections that contribute to the highest throughput through the firewall. | Optimize workloads that generate the most traffic through the firewall to reduce the volume of traffic, which decreases the load on the firewall and minimizes data-processing and bandwidth costs.    |
| Identify and delete unused Azure Firewall deployments. Analyze [monitoring metrics](/azure/firewall/metrics#firewall-metrics) and UDRs that are associated with subnets that point to the firewall's private IP. Also consider other validations and internal documentation about your environment and deployments. For example, analyze any classic NAT, network, and application rules for Azure Firewall. And consider your settings. For example, you might configure the DNS proxy setting to **Disabled**. <br><br> For more information, see [Monitor Azure Firewall](/azure/firewall/monitor-firewall). | Use this approach to detect cost-effective deployments over time and eliminate unused resources, which prevents unnecessary costs.|
| Review your Firewall Manager policies, associations, and inheritance carefully to optimize cost. Policies are billed based on firewall associations. A policy with zero or one firewall association is free. A policy with multiple firewall associations is billed at a fixed rate.<br><br> For more information, see [Firewall Manager pricing](https://azure.microsoft.com/pricing/details/firewall-manager).| Properly use Firewall Manager and its policies to reduce operational costs, increase efficiency, and reduce management overhead. |
| Review all the public IP addresses in your configuration, and disassociate and delete the ones that you don't use. Evaluate source network address translation (SNAT) port usage before you remove any IP addresses. <br><br> For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port usage](/azure/firewall/metrics#firewall-metrics).| Delete unused IP addresses to reduce costs.|
| Reduce cost through [ingestion-time transformation](/azure/azure-monitor/data-collection/data-collection-transformations#workspace-transformation-dcr) in Log Analytics for selective firewall log processing. <br><br> To optimize costs while maintaining security visibility, configure advanced filtering and data transformation before logs are stored in the tables prefixed with `AZFW`. | Significant cost reduction in log analytics expenses for high-traffic environments while preserving relevant security events for analysis. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Firewall.

> [!div class="checklist"]
> - **Use [Firewall Manager](/azure/firewall-manager/vhubs-and-vnets) with traditional hub-and-spoke topologies or Virtual WAN network topologies** to deploy and manage instances of Azure Firewall. Use native security services for traffic governance and protection to create hub-and-spoke and transitive architectures. For more information, see [Network topology and connectivity](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity).
>
>    Migrate Azure Firewall [classic rules](/azure/firewall-manager/policy-overview#classic-rules-and-policies) to Firewall Manager policies for existing deployments. Use Firewall Manager to centrally manage your firewalls and policies. For more information, see [Migrate to Azure Firewall Premium](/azure/firewall/premium-migrate).
>
> - **Maintain regular backups of Azure Policy artifacts.** If you use an infrastructure-as-code approach to maintain Azure Firewall and all dependencies, you should have backup and versioning of Azure Firewall policies in place. If you don't, you can deploy a [companion mechanism](https://techcommunity.microsoft.com/t5/azure-network-security-blog/backup-azure-firewall-and-azure-firewall-policy-with-logic-apps/ba-p/3613928) that's based on an external logic app to provide an effective automated solution.
>
> - **Monitor Azure Firewall logs and metrics.** Take advantage of diagnostic logs for firewall monitoring and troubleshooting and activity logs for auditing operations.
>
> - **Analyze monitoring data to assess the overall health of the system.** Use the built-in Azure Firewall monitoring workbook, familiarize yourself with Kusto Query Language (KQL) queries, and use the policy analytics dashboard to identify potential problems.
>
> - **Define alerts for key events** so that operators can quickly respond to them.
>
> - **Take advantage of platform-provided detection mechanisms in Azure to detect abuse.** Integrate Azure Firewall with [Microsoft Defender for Cloud](https://techcommunity.microsoft.com/t5/microsoft-defender-for-cloud/azure-network-security-using-microsoft-defender-for-cloud/ba-p/2228222) and [Microsoft Sentinel](https://azuremarketplace.microsoft.com/marketplace/apps/sentinel4azurefirewall.sentinel4azurefirewall) if possible. Integrate with Defender for Cloud so you can visualize the status of network infrastructure and network security in one place, including Azure network security across all virtual networks and virtual hubs in different regions in Azure. Integrate with Microsoft Sentinel to provide threat-detection and prevention capabilities.
>
> - **Configure customer-controlled maintenance** to align system updates with business requirements. Schedule maintenance during planned downtime windows to minimize disruption to critical operations, especially for applications that require persistent connections.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|Enable [diagnostic logs](/azure/firewall/monitor-firewall#legacy-azure-diagnostics-logs) for Azure Firewall. Use firewall logs or workbooks to monitor Azure Firewall. You can also use activity logs to audit operations on Azure Firewall resources. <br><br> Use the [structured firewall logs](/azure/firewall/monitor-firewall#structured-azure-firewall-logs) format. Only use the previous [diagnostic logs format](/azure/firewall/logs-and-metrics#diagnostic-logs) if you have an existing tool that requires it. Don't enable both logging formats at the same time. |Enable diagnostic logs to optimize your monitoring tools and strategies for Azure Firewall. <br><br> Use structured firewall logs to structure log data so that it's easy to search, filter, and analyze. The latest monitoring tools are based on this type of log, so it's often a prerequisite. |
| Use the built-in [Azure Firewall workbook](https://techcommunity.microsoft.com/t5/azure-network-security-blog/azure-firewall-new-monitoring-and-logging-updates/ba-p/3897897). | Use the Azure Firewall workbook to extract valuable insights from Azure Firewall events, analyze your application and network rules, and examine statistics about firewall activities across URLs, ports, and addresses. |
|[Monitor Azure Firewall logs and metrics](/azure/firewall/monitor-firewall), and create alerts for Azure Firewall capacity. Create alerts to monitor *Throughput*, *Firewall health state*, *SNAT port utilization*, and *AZFW latency probe* metrics.|Set up alerts for key events to notify operators before potential problems arise, help prevent disruptions, and initiate quick capacity adjustments.|
| Regularly review the [policy analytics dashboard](/azure/firewall/policy-analytics) to identify potential problems. | Use policy analytics to analyze the impact of your Azure Firewall policies. Identify potential problems in your policies, such as meeting policy limits, improper rules, and improper IP groups usage. Get recommendations to improve your security posture and rule-processing performance. |
| Understand KQL queries so you can use Azure Firewall logs to quickly analyze and troubleshoot problems. Azure Firewall provides [sample queries](/azure/firewall/firewall-structured-logs#structured-log-queries).  | Use KQL queries to quickly identify events inside your firewall and check to see which rule is triggered or which rule allows or blocks a request. |
| Configure [customer-controlled maintenance](/azure/firewall/customer-controlled-maintenance) with minimum 5-hour windows and daily recurrence. <br><br> Plan maintenance schedules around business requirements and low-traffic periods. Consider geographic distribution of maintenance windows for multi-region deployments. | Predictable maintenance reduces unexpected downtime while ensuring firewall infrastructure receives necessary security updates. Aligns infrastructure maintenance with business availability requirements. |
| Use [FQDNs in DNAT rules](/azure/firewall/destination-nat-rules) to route inbound traffic to backend infrastructure instead of static IP addresses. | Reduces operational overhead when backend infrastructure changes IP addresses due to scaling operations, deployments, or infrastructure updates. |
| Configure [DNAT on private IP addresses](/azure/firewall/destination-nat-rules) for complex hybrid and enterprise networking scenarios. <br><br> Enable connectivity between networks with overlapping IP address ranges through port translation capabilities. | This is especially useful in scenarios with overlapping IP ranges, common in mergers, partner networks, or multi-tenant environments. |
| Implement [change tracking](/azure/firewall/monitor-firewall#change-tracking-preview) (preview) to monitor configuration changes made to Azure Firewall Rule Collection Groups. | Provides detailed visibility into configuration changes, enhancing security posture and simplifying troubleshooting by tracking who made what changes and when. |
| Configure [packet capture capabilities](/azure/firewall/packet-capture) for capturing specific traffic flows with filtering based on protocol, flags, and custom filters. <br><br> Use packet capture to identify blocked connections, and validate firewall rule behavior with detailed packet-level information for troubleshooting connectivity issues. | Enables network troubleshooting and traffic analysis by providing detailed packet-level visibility into Azure Firewall traffic flows.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Firewall.

> [!div class="checklist"]
> - **Optimize your Azure Firewall configuration** in accordance with the Well-Architected Framework [recommendations](/azure/well-architected/performance-efficiency/optimize-code-infrastructure#optimize-infrastructure-performance) to optimize code and infrastructure and ensure peak operation. To maintain an efficient and secure network, regularly review and optimize firewall rules. This practice helps ensure that your firewall configurations remain effective and up to date with the latest security threats.
>
>   Assess policy requirements, and find opportunities to summarize IP ranges and URL lists. Use web categories to allow or deny outbound access in bulk to streamline management and enhance security. Evaluate the performance impact of IDPS in *Alert and deny* mode because this configuration can affect network latency and throughput. Configure public IP addresses to support your SNAT port requirements. Follow these practices to create a robust and scalable network security infrastructure.
> - **Don't use Azure Firewall for intra-virtual network traffic control.** Use Azure Firewall to control the following types of traffic:
>   - Traffic across virtual networks
>   - Traffic between virtual networks and on-premises networks
>   - Outbound traffic to the internet
>   - Incoming non-HTTP or non-HTTPS traffic
>
>   For intra-virtual network traffic control, use [network security groups](/azure/virtual-network/network-security-groups-overview).
>
> - **Warm up Azure Firewall properly before performance tests.** Create initial traffic that isn't part of your load tests 20 minutes before your tests. Use diagnostics settings to capture scale-up and scale-down events. You can use the [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) service to generate the initial traffic so you can scale up Azure Firewall to the maximum number of instances.
> - **Configure an Azure Firewall subnet with a /26 address space.** You need a dedicated subnet for Azure Firewall. Azure Firewall provisions more capacity as it scales.
>    A /26 address space ensures that the firewall has enough IP addresses available to accommodate the scaling. Azure Firewall doesn't require a subnet that's larger than /26. Name the Azure Firewall subnet **AzureFirewallSubnet**.
> - **Don't enable advanced logging if you don't need it.** Azure Firewall provides some advanced logging capabilities that can incur significant costs to keep active. Instead, you can use these capabilities for troubleshooting purposes only and for limited amounts of time. Disable capabilities when you don't need them. For example, [top flows and flow trace logs](/azure/firewall/monitor-firewall-reference#top-flows) are expensive and can cause excessive CPU and storage usage on the Azure Firewall infrastructure.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
| Use the [policy analytics](/azure/firewall/policy-analytics) dashboard to identify ways to optimize Azure Firewall policies. | Use policy analytics to identify potential problems in your policies, such as meeting policy limits, improper rules, and improper IP groups usage. Get recommendations to improve your security posture and rule-processing performance. |
| Place frequently used rules early in a group to optimize latency for Azure Firewall policies that have large [rule sets](/azure/firewall/policy-rule-sets). <br><br> For more information, see [Use Azure Firewall policies to process rules](/azure/firewall/rule-processing#rule-processing-using-firewall-policy). |Place frequently used rules high in a rule set to optimize processing latency. Azure Firewall processes rules based on the rule type, inheritance, rule collection group priority, and rule collection priority. Azure Firewall processes high-priority rule collection groups first. Inside a rule collection group, Azure Firewall processes rule collections that have the highest priority first. |
| Use IP groups to summarize IP address ranges and avoid exceeding [the limit of unique source or unique destination network rules](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits). Azure Firewall treats the IP group as a single address when you create network rules.| This approach effectively increases the number of IP addresses that you can cover without exceeding the limit. For each rule, Azure multiplies ports by IP addresses. So, if one rule has four IP address ranges and five ports, you consume 20 network rules.|
| Use Azure Firewall [web categories](/azure/firewall/web-categories) to allow or deny outbound access in bulk, instead of explicitly building and maintaining a long list of public internet sites. | This feature dynamically categorizes web content and permits the creation of compact application rules, which reduces operational overhead. |
| Evaluate the performance impact of [IDPS](/azure/firewall/premium-features#idps) in *Alert and deny* mode. For more information, see [Azure Firewall performance](/azure/firewall/firewall-performance). | Enable IDPS in *Alert and deny* mode to detect and prevent malicious network activity. This feature might introduce a performance penalty. Understand the effect on your workload so you can plan accordingly. |
| Configure Azure Firewall deployments with a minimum of five public IP addresses for deployments that are susceptible to [SNAT port exhaustion](/azure/firewall/firewall-known-issues#azure-firewall-standard).| Azure Firewall supports 2,496 ports for each public IP address that each back-end Azure Virtual Machine Scale Sets instance uses. This configuration increases the available SNAT ports by five times. <br><br> By default, Azure Firewall deploys two Virtual Machine Scale Sets instances that support 4,992 ports for each flow destination IP, destination port, and TCP or UDP protocol. The firewall scales up to a maximum of 20 instances. |
| Configure [prescaling capabilities](/azure/firewall/prescaling) and monitor the Observed Capacity metric for capacity planning and visibility into activity. <br><br> Use prescaling to allocate capacity units for anticipated traffic increases, in advance. | Enables proactive capacity management based on metrics and allows you to set alerts for reliable scaling operations during expected traffic increases. <br>  There's added benefit that you pay only for provisioned capacity units with flexibility to adjust as needs evolve. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Firewall and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- [Network interfaces shouldn't have public IPs](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a86a26-fd1f-447c-b59d-e51f44264114). This policy denies network interfaces that are configured with a public IP. Public IP addresses allow internet resources to communicate inbound to Azure resources, and Azure resources can communicate outbound to the internet.

- [All internet traffic should be routed via your deployed Azure Firewall instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Azure Security Center identifies that some of your subnets aren't protected with a next generation firewall. Protect your subnets from potential threats. Use Azure Firewall or a supported next generation firewall to restrict access to your subnets.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Firewall](/azure/governance/policy/samples/built-in-policies#network) and other policies that might affect the security of the network.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Hub-spoke network topology in Azure](/azure/architecture/networking/architecture/hub-spoke).


## Next steps

See the following resources that demonstrate the
recommendations in this article.

- Use the following reference architectures as examples of how to
  apply this article's guidance to a workload:
  - [Hub-spoke network topology in Azure](/azure/architecture/networking/architecture/hub-spoke)
  - [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls)
  - [Use Azure Firewall to help protect an Azure Kubernetes Service (AKS) cluster](/azure/architecture/example-scenario/aks-firewall/aks-firewall)

- Use the following resources to improve your implementation expertise:
  - [Azure Firewall documentation](/azure/firewall)
  - [What is Firewall Manager?](/azure/firewall-manager/overview)

- See other resources:
   - [Azure Firewall service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits)
   - [Azure security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline)
   - [Use the Azure portal to deploy and configure Azure Firewall and policy](/azure/firewall/tutorial-firewall-deploy-portal-policy)
   - [Configure Azure Firewall in a Virtual WAN hub](/azure/virtual-wan/howto-firewall)

<!--additional links from the original article
- [Create Azure Service Health alerts to be notified when Azure problems affect you](/azure/advisor/advisor-high-availability-recommendations#create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you)
- [Ensure you have access to Azure cloud experts when you need it](/azure/advisor/advisor-operational-excellence-recommendations#ensure-you-have-access-to-azure-cloud-experts-when-you-need-it)
- [Enable Traffic Analytics to view insights into traffic patterns across Azure resources](/azure/advisor/advisor-reference-operational-excellence-recommendations#enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources)
- [Follow just enough administration (least privilege principle)](/security/benchmark/azure/baselines/advisor-security-baseline?toc=/azure/advisor/toc.json#pa-7-follow-just-enough-administration-least-privilege-principle)
- [Protect your network resources with Microsoft Defender for Cloud](/azure/defender-for-cloud/protect-network-resources)
-->

<!-- Updated: August 17, 2025 for Azure Update 497160, 497428, 493296, 498568, public-preview-azure-firewall-integration-in-microsoft-copilot-for-security -->
<!-- Updated: November 21, 2025 for Azure Update 515452, 516002, 526720, 528969 -->

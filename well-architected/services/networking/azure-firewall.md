---
title: Microsoft Azure Well-Architected Framework review - Azure Firewall
description: Provides architectural best practices for the Azure Firewall.
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 04/29/2022
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

Azure Firewall ia an intelligent firewall security service that provides threat protection for your cloud workloads running in Azure.

Features include:

- Stateful Firewall as a service
- Built-in high availability
- Unrestricted cloud scalability
- East-west and north-south traffic inspection

To learn how Azure Firewall supports a reliable workload, see the following articles:

- [Introduction to Azure Firewall](/learn/modules/introduction-azure-firewall/)
- [Quickstart: Deploy Azure Firewall with Availability Zones](/azure/firewall/deploy-template)

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../resiliency/principles.md) for Reliability.

<!--
The following are the Reliability design principles:
 
Design for business requirements
Design for failure
Observe application health
Drive automation
Design for self-healing
Design for scale-out
-->

> [!div class="checklist"]
> - Deploy using a secured virtual hub
> - Use a global Azure Firewall policy
> - Determine if you want to use use third-party security as a service (SECaaS) providers.

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|Use Firewall Manager with Azure Virtual Wide Area Networks (WAN) to deploy and manage Azure Firewalls across Azure Virtual WAN Hubs, or in Hub VNets.|Easily create hub-and-spoke and transitive architectures with native security services for traffic governance and protection.|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security Teams through role-based access control (RBAC).|
|Configure supported third-party SaaS security providers within the Firewall Manager, if you want to use these solutions to protect outbound connections.|You can use your familiar, best-in-breed, third-party security as a service (SECaaS) offerings to protect Internet access for your users.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Security

Security is one of the most important aspects of any architecture. An intelligent firewall security service, [Azure Firewall](/azure/firewall/) provides threat protection for your cloud workloads running in Azure.

### Design checklist

As you make design choices for Azure Firewall, review the [design principles](../../security/security-principles.md) for Security.

<!--
The following are the Security design principles:
 
- Plan resources and how to harden them
- Automate and use least privilege
- Classify and encrypt data
- Monitor system security, plan incident response
- Identify and protect endpoints
- Protect against code-level vulnerabilities
- Model and test against potential threats
-->

> [!div class="checklist"]
> - Use a global Azure Firewall policy
> - Use threat intelligence
> - Use DNS proxy
> - Direct network traffic through Azure Firewall
> - Validate spoke networks
> - Determine if you want to use use third-party security as a service (SECaaS) providers.
> - Use just-in-time systems (JIT)

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Security.

| Recommendation | Benefit |
|--------|----|
|Create a global Azure Firewall policy to govern the security posture across global network environments. Assign the policy to all Azure Firewalls.|Allow for granular policies to meet requirements of specific regions. Delegate incremental Firewall Policies to local security teams through role-based access control (RBAC).|
|Enable threat intelligence on Azure Firewall.|You can enable threat intelligence-based filtering for your firewall to alert and deny traffic from or to unknown IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence Feed. Intelligent Security Graph powers Microsoft threat intelligence and is used by multiple services including Azure Security Center.
|Enable Domain Name System (DNS) Proxy and point the infrastructure DNS to Azure Firewall.|By default, Azure Firewall uses Azure DNS. Custom DNS allows you to configure Azure Firewall to use Corporate DNS to resolve external and internal names.|
|Configure the user-defined routes (UDR) to force traffic to Azure Firewall.|Configure UDRs to force traffic to Azure Firewall for `SpoketoSpoke`, `SpoketoInternet`, and `SpoketoHybrid` connectivity.|
|Validate any overlapping or missing peering in spoke networks.|Check if there's any overlapping or missing peering in spoke networks.|
|Leverage Security Partner Providers for third-party Security as a Service (SECaaS) Offerings.|Security partner providers help to filter internet traffic through Virtual Private Network (VNET) to internet or Branch to internet.|
|Use just-in-time systems (JIT) to control access to virtual machines (VMs) from the internet.|You can use Azure Security Center JIT to control access for clients connecting from the internet using Azure Firewall.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

### Policy definitions

- [All Internet traffic should be routed via your deployed Azure Firewall](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c). Azure Security Center has identified that some of your subnets aren't protected with a next generation firewall. Protect your subnets from potential threats by restricting access to them with Azure Firewall or a supported next generation firewall.

All built-in policy definitions related to Azure Networking are listed in [Built-in policies - Network](/azure/governance/policy/samples/built-in-policies#network).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies.

### Design checklist

As you make design choices for Azure Firewall, review the  [design principles](../../cost/principles.md) for Cost optimization.

<!--
The following are the Cost design principles:
 
- *Choose the correct resources*
- Set up budgets and maintain cost constraints
- *Dynamically allocate and de-allocate resources*
- *Optimize workloads, aim for scalable costs*
- *Continuously monitor and optimize cost management*
-->

> [!div class="checklist"]
> - Determine which firewall SKUs to deploy
> - Determine if some resources don't need 100% allocation
> - Determine where you can optimize firewall use across workloads
> - Monitor firewall usage to determine cost effectiveness
> - Review Firewall Manager capabilities to determine potential operational efficiency
> - Determine the number of Public IP addresses required

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Cost optimization.

| Recommendation | Benefit |
|--------|----|
| Deploy the Basic and Premium SKUs where appropriate.| The Standard option is usually enough for east-west traffic, where Premium comes with the necessary additional features for north-south traffic, as well as the forced tunneling feature and many other features. For more information, see [Azure Firewall Premium Preview features](/azure/firewall/premium-features). Deploy mixed scenarios using the Standard and Premium options, according to your needs.|
|Stop Azure Firewall deployments that do not need to run for 24 hours.|You may have development environments that are used only during business hours. For more details, see [Deallocate and allocate Azure Firewall](/powershell/module/az.network/set-azfirewall?#4--deallocate-and-allocate-the-firewall).|
|Share the same Azure Firewall across multiple workloads and Azure Virtual Networks. Ensure that there is no unexpected cross-region traffic as part of the hub-spoke topology.|You can use a central Azure Firewall in the hub virtual network, and share the same Firewall across many spoke virtual networks that are connected to the same hub from the same region.|
|Review underutilized Azure Firewall instances, and identify and delete Azure Firewall deployments not in use. To identify Azure Firewall deployments not in use, start analyzing the Monitoring Metrics and User Defined Routes (UDRs) that are associated with subnets pointing to the Firewall's private IP. Then, combine that with additional validations, such as if the Azure Firewall has any Rules (Classic) for NAT, or Network and Application, or even if the DNS Proxy setting is configured to **Disabled**, as well as with internal documentation about your environment and deployments.| You can detect  deployments that are cost effective over time. <br><br> For more details about monitoring logs and metrics, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).|
|Use Azure Firewall Manager and its policies. Review your Firewall Manager policies, associations, and inheritance carefully. Policies are billed based on firewall associations. A policy with zero or one firewall association is free of charge. A policy with multiple firewall associations is billed at a fixed rate.|You can reduce your operational costs, increasing your efficiency, and reduce your management overhead.  For more information, see [Pricing - Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager).|
|Validate whether all the associated Public IP addresses are in use. If they are not in use, disassociate and delete them. Use IP Groups to reduce your management overhead. Evaluate SNAT ports utilization before you remove any IP Addresses.| You will only use the number of Public IPs that your firewall actually needs. For more information, see [Monitor Azure Firewall logs and metrics](/azure/firewall/firewall-diagnostics) and [SNAT port utilization](/azure/firewall/logs-and-metrics#metrics).

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly.

### Design checklist

As you make design choices for Azure Firewall, review the [Operational excellence design principles](../../devops/principles).

<!--
The following are the Operational excellence design principles:

- Optimize build and release processes
- Understand operational health
- Rehearse recovery and practice failure
- Embrace continuous operational improvement
- Use loosely coupled architecture

-->

> [!div class="checklist"]
> - Use logs for monitoring
> - Use tags when possible allow traffic through the firewall
> - Use Workbooks
> - Use Azure Firewall connector in Azure Sentinel

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Operational excellence.

| Recommendation | Benefit |
|--------|----|
|Turn on logs for Azure Firewall.|You can monitor Azure Firewall using firewall logs or workbooks. You can also use activity logs to audit operations on Azure Firewall resources.|
|Use Fully Qualified Domain Name (FQDN) tags on Azure Firewall.|FQDN tags make it easy to allow known Azure service network traffic through your firewall. For example, say you want to allow Windows Update network traffic through your firewall. You create an application rule and use the Windows Update tag. Now network traffic from Windows Update can flow through your firewall.|
|Use Workbooks in Azure Log Analytics|Helps you visualize firewall logs.|
|Enable Azure Firewall connector in Azure Sentinel.|You can use Azure Sentinel to create detections and logic apps for Azure Firewall.|

Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner.

### Design checklist

As you make design choices for Azure Firewall, review the [Performance efficiency principles](../../scalability/principles.md).

<!--
The following are the Performance efficiency design principles:

- Understand the challenges of distributed architectures
- Run performance testing in the scope of development
- Establish performance baselines
- *Run load and stress tests*
- Identify bottlenecks
- Have a data-driven approach
- Troubleshoot performance issues
- Identify improvement opportunities with resolution planning
- *Invest in capacity planning*
-->

> [!div class="checklist"]
> - Determine your SNAT port requirements and if you should deploy a NAT gateway
> - Plan load tests to test auto scale performance in your environment
> - Plan network rule requirements and opportunities to summarize IP ranges.

### Recommendations

<!--
In table format, present the top recommendations that demonstrate how to achieve the points described in the design checklist.
-->

Explore the following table of recommendations to optimize your Azure Firewall configuration for Performance efficiency.

| Recommendation | Benefit |
|--------|----|
| If you'll need more than 512k SNAT ports, deploy a NAT Gateway with Azure Firewall. | With a NAT Gateway, you can scale up to +1M ports. For more information, see [Scale SNAT ports with Azure NAT Gateway](azure/firewall/integrate-with-nat-gateway.md)|
|Create initial traffic that is not part of your load tests 20 minutes prior to the test. Use diagnostics settings to capture scale-up and scale-down events.|Allows the Azure Firewall instance to scale up its instances to the maximum. |
|Use IP Groups to summarize IP address ranges.|You can use IP Groups to summarize IP ranges so you do not exceed 10k network rules. For each rule, Azure multiplies ports x IP addresses. So if you have one rule with four IP address ranges and five ports, you will actually consume 20 network rules.


Azure Advisor helps you ensure and improve continuity of your business-critical applications. Review the [Azure Advisor recommendations](#azure-advisor-recommendations).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your Azure Firewall.

### Reliability

- XXX

## Additional resources

- [Azure Firewall documentation](/azure/firewall/)
- [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls/)
- [Azure security baseline for Azure Firewall](/security/benchmark/azure/baselines/firewall-security-baseline)
- [Azure Firewall service limits, quotas, and constraints](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-firewall-limits)
- [Use Azure Firewall to help protect an Azure Kubernetes Service (AKS) cluster](/azure/architecture/example-scenario/aks-firewall/aks-firewall)
- [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?tabs=cli)

### Azure Architecture Center guidance

- [Azure Firewall architecture overview](/azure/architecture/example-scenario/firewalls)

## Next steps

- Deploy an Azure Firewall to see how it works: [Tutorial: Deploy and configure Azure Firewall and policy using the Azure portal](/azure/firewall/tutorial-firewall-deploy-portal-policy)

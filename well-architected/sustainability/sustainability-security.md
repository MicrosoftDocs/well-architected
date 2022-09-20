---
title: Security considerations for sustainable workloads on Azure
description: This design area explores security design considerations for sustainable workloads on Azure.
author: Zimmergren
ms.author: tozimmergren
ms.topic: conceptual
ms.date: 07/27/2022
ms.service: architecture-center
ms.subservice: well-architected
categories: 
  - networking
products: Azure
ms.custom:
  - sustainability
---

# Security considerations for sustainable workloads on Azure

Designing sustainable workloads on Azure must encompass security, which is a foundational principle through all phases of a project. Learn about considerations and recommendations leading to a more sustainable security posture.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected sustainable workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a sustainable workload?](sustainability-get-started.md#what-is-a-sustainable-workload)

## Security monitoring

Use cloud native security monitoring solutions to optimize for sustainability.

### Use cloud native log collection methods where applicable

Traditionally, log collection methods for ingestion to a Security Information and Event Management (SIEM) solution required the use of an intermediary resource to collect, parse, filter and transmit logs onward to the central collection system. Using this design can carry an overhead with more infrastructure and associated financial and carbon-related costs.

_Green Software Foundation alignment: [Carbon efficiency](sustainability-design-principles.md#carbon-efficiency)_

**Recommendation:**

- Using cloud native [service-to-service connectors](/azure/sentinel/connect-data-sources#service-to-service-integration-for-data-connectors) simplify the integration between the services and the SIEM, and removes the overhead of extra infrastructure.
- It's possible to ingest log data from existing compute resources using previously deployed agents such as the Azure Monitor Analytics agent. Review how to [migrate to Azure Monitor agent from Log Analytics agent](/azure/azure-monitor/agents/azure-monitor-agent-migration).
- Consider this tradeoff: Deploying more monitoring agents will increase the overhead in processing as it needs more compute resources. Carefully design and plan for how much information is needed to cover the security requirements of the solution and find a suitable level of information to store and keep.
  - A possible solution to reduce unnecessary data collection is to rely on the [Azure Monitor Data Collection Rules (DCR)](/azure/azure-monitor/essentials/data-collection-rule-overview).

### Avoid transferring large unfiltered data sets from one cloud service provider to another

Conventional SIEM solutions required all log data to be ingested and stored in a centralized location. In a multicloud environment, this solution can lead to a large amount of data being transferred out fo a cloud service provide and into another, causing increased burden on the network and storage infrastructure.

_Green Software Foundation alignment: [Carbon efficiency](sustainability-design-principles.md#carbon-efficiency)_

**Recommendation:**

- Cloud native security services can perform localized analysis on relevant security data source. This analysis allows the bulk of log data to remain within the source cloud service provider environment. Cloud native SIEM solutions can be [connected via an API or connector](/azure/sentinel/connect-aws) to these security services to transmit only the relevant security incident or event data. This solution can greatly reduce the amount of data transferred while maintaining a high level of security information to respond to an incident.

In time, using the described approach helps reduce data egress and storage costs, which inherently help reduce emissions.

### Filter or exclude log sources before transmission or ingestion into a SIEM

Consider the complexity and cost of storing all logs from all possible sources. For instance, applications, servers, diagnostics and platform activity.

_Green Software Foundation alignment: [Carbon efficiency](sustainability-design-principles.md#carbon-efficiency)_

**Recommendation:**

- When designing a log collection strategy for cloud native SIEM solutions, consider the use cases based on the [Microsoft Sentinel analytics rules](/azure/sentinel/detect-threats-built-in) required for your environment and match up the required log sources to support those rules.
- This option can help remove the unnecessary transmission and storage of log data, reducing the carbon emissions on the  environment.

### Archive log data to long-term storage

Many customers have a requirement to store log data for an extended period due to regulatory compliance reasons. In these cases, storing log data in the primary storage location of the SIEM system is a costly solution.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Log data can be [moved out to a cheaper long-term storage option](https://techcommunity.microsoft.com/t5/microsoft-sentinel-blog/move-your-microsoft-sentinel-logs-to-long-term-storage-with-ease/ba-p/1407153) which respects the retention policies of the customer, but lowers the cost by utilizing separate storage locations.

## Network architecture

Increase the efficiency and avoid unnecessary traffic by following good practices for network security architectures.

### Use cloud native network security controls to eliminate unnecessary network traffic

When you use a centralized routing- and firewall design, all network traffic is sent to the hub for inspection, filtering, and onward routing. While this approach centralizes policy enforcement, it can create an overhead on the network of unnecessary traffic from the source resources.

_Green Software Foundation alignment: [Hardware efficiency](sustainability-design-principles.md#hardware-efficiency), [Carbon efficiency](sustainability-design-principles.md#carbon-efficiency)_

**Recommendation:**

- Use [Network security groups](/azure/virtual-network/network-security-groups-overview) and [Application security groups](/azure/virtual-network/application-security-groups) to help filter traffic at the source, and to remove the unnecessary data transmission. Using these capabilities can help reduce the burden on the cloud infrastructure, with lower bandwidth requirements and less infrastructure to own and manage.

### Minimize routing from endpoints to the destination

In many customer environments, especially in hybrid deployments, all end user device network traffic is routed through on-premises systems before being allowed to reach the internet. Usually, this happens due to the requirement to inspect all internet traffic. Often, this requires higher capacity network security appliances within the on-premises environment, or more appliances within the cloud environment.

_Green Software Foundation alignment: [Carbon efficiency](sustainability-design-principles.md#carbon-efficiency)_

**Recommendation:**

- Minimize routing from endpoints to the destination.
  - Where possible, end user devices should be optimized to [split out known traffic directly to cloud services](/microsoft-365/enterprise/microsoft-365-vpn-implement-split-tunnel) while continuing to route and inspect traffic for all other destinations. Bringing these capabilities and policies closer to the end user device prevents unnecessary network traffic and its associated overhead.

### Use network security tools with auto-scaling capabilities

Based on network traffic, there will be times when demand of the security appliance will be high, and other times where it will be lower. Many network security appliances are deployed to a scale to cope with the highest expected demand, leading to inefficiencies. Additionally, reconfiguration of these tools often requires a reboot leading to unacceptable downtime and management overhead.

_Green Software Foundation alignment: [Hardware efficiency](sustainability-design-principles.md#hardware-efficiency)_

**Recommendation:**

- Making use of auto-scaling allows the rightsizing of the backend resources to meet demand without manual intervention.
- This approach will vastly reduce the time to react to network traffic changes, resulting in a reduced waste of unnecessary resources, and increases your sustainability effect.
- Learn more about relevant services by reading [how to enable a Web Application Firewall (WAF) on an Application Gateway](/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal), and [deploy and configure Azure Firewall Premium](/azure/firewall/premium-deploy).

### Evaluate whether to use TLS termination

Terminating and re-establishing TLS is CPU consumption that might be unnecessary in certain architectures.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- Consider if you can terminate TLS at your border gateway and continue with non-TLS to your workload load balancer and onwards to your workload.
- Review the information on [TLS termination](/azure/application-gateway/ssl-overview#tls-termination) to better understand the performance and utilization impact it offers.
- Consider the tradeoff: A balanced level of security can offer a more sustainable and energy efficient workload while a higher level of security may increase the requirements on compute resources.

### Use DDoS protection

Distributed Denial of Service (DDoS) attacks aim to disrupt operational systems by overwhelming them, creating a significant impact on the resources in the cloud. Successful attacks flood network and compute resources, leading to an unnecessary spike in usage and cost.

_Green Software Foundation alignment: [Energy efficiency](sustainability-design-principles.md#energy-efficiency)_

**Recommendation:**

- DDoS protection seeks to [mitigate attacks at an abstracted layer](/azure/ddos-protection/types-of-attacks), so the attack is mitigated before reaching any customer operated services.
  - Mitigating any malicious usage of compute and network services will ultimately help reduce unnecessary carbon emissions.

## Endpoint security

It's imperative that we secure our workloads and solutions in the cloud. Understanding how we can optimize our mitigation tactics all the way down to the client devices can have a positive outcome for reducing emissions.

### Integrate Microsoft Defender for Endpoint

Many attacks on cloud infrastructure seek to misuse deployed resources for the attacker's direct gain. Two such misuse cases are botnets and crypto mining.

Both of these cases involve taking control of customer-operated compute resources and use them to either create new cryptocurrency coins, or as a network of resources from which to launch a secondary action like a DDoS attack, or mass e-mail spam campaigns.

_Green Software Foundation alignment: [Hardware efficiency](sustainability-design-principles.md#hardware-efficiency)_

**Recommendations:**

- Integrate [Microsoft Defender for Endpoint](/azure/defender-for-cloud/integration-defender-for-endpoint) with Defender for Cloud to identify and shut down crypto mining and botnets.
  - The EDR capabilities provide advanced attack detections and are able to take response actions to remediate those threats. The unnecessary resource usage created by these common attacks can quickly be discovered and remediated, often without the intervention of a security analyst.

## Reporting

Getting the right information and insights at the right time is important for producing reports around emissions from your security appliances.

### Tag security resources

It can be a challenge to quickly find and report on all security appliances in your tenant. Identifying the security resources can help when designing a strategy for a more sustainable operating model for your business.

_Green Software Foundation alignment: [Measuring sustainability](sustainability-design-principles.md#measuring-sustainability)_

**Recommendation:**

- Tag security resources to record emissions impact of security resources.

## Next step

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Design principles](sustainability-design-principles.md)

---
title: Networking and connectivity considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop networking and connectivity concepts. See techniques for improving the security, operations, and scalability of networks.
author: swathibhat1
ms.author: swathibhat
ms.date: 10/07/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-azure-virtual-desktop
---

# Networking and connectivity

Networking is a foundational element of Azure Virtual Desktop (AVD) workloads, impacting performance, security, and user experience. This article provides networking guidelines and recommendations aligned with the [Azure Well-Architected Framework](/azure/well-architected/).

## Traffic Optimization

Azure Virtual Desktop (AVD) requires real-time, low-latency network connectivity to deliver a seamless user experience. High latency or jitter can result in lag, screen tearing, or slow input response. Optimizing your network helps ensure efficient bandwidth usage, reduces costs, and improves performance—especially in bandwidth-constrained environments. 

You should validate that latency and bandwidth meet requirements in development, testing, and proof-of-concept environments. Always consider the actual user experience, including network conditions, end-user devices, and session host configuration.

> [!NOTE]
> Latency is only one aspect of remote connectivity. Network throughput and user workload also affect the end-user experience. To estimate bandwidth requirements, see [Remote Desktop Protocol (RDP) bandwidth requirements](/azure/virtual-desktop/rdp-bandwidth).

### Recommendations

| Recommendation | Benefit |
|---|---|
| Deploy session hosts in the Azure region closest to your end-users | Reduces latency and improves responsiveness |
| Implement [RDP Shortpath](/azure/virtual-desktop/rdp-shortpath) | Enables direct UDP connections for lower latency and better performance |
| For Point-to-Site (P2S) VPN connections, use a split tunnel based on User Datagram Protocol (UDP) | Improves performance and reduces unnecessary traffic through the VPN |
| In managed networks, implement Quality of Service (QoS) policies for RDP traffic that's sensitive to network delays | Prioritizes critical AVD traffic and minimizes delays |
| [Test network latency](/azure/virtual-network/virtual-network-test-latency) between session hosts and on-premises | Identifies and addresses connectivity bottlenecks |
| Consider using Virtual Machine (VM) SKUs that offer [accelerated networking](/azure/virtual-network/accelerated-networking-overview) capabilities | Improves throughput and reduces network latency |
| Use a route table to allow Azure Virtual Desktop traffic to bypass any forced tunneling rules that you use to route traffic to a firewall or network virtual appliance (NVA). Otherwise, forced tunneling can affect the performance and reliability of your clients' connectivity. Consider using [service tags](/azure/virtual-network/service-tags-overview).| Maintains optimal connectivity and reduces risk of bottlenecks |
| [Enable the Azure Network Watcher connection monitor](/azure/network-watcher/connection-monitor-overview). <br><br> Use the connection monitor during testing, which can generate synthetic traffic | You're able to collect metrics that indicate loss and latency across networks. Also, you can trace the entire traffic path, which is important for detecting network bottlenecks |


<!---- Private Subnets Outbound Connectivity Service Tags/Internet -->

## Network Security 

Network security is essential for protecting Azure Virtual Desktop (AVD) workloads from threats and ensuring compliance. As cloud environments become more complex, traditional network controls based on a trusted-intranet approach are no longer sufficient. Modern security requires layered controls and continuous monitoring.

For general information about protecting assets by placing controls on network traffic, see [Recommendations for networking and connectivity](/azure/well-architected/security/networking).

> [!NOTE]
> Ensure both session hosts and end-user devices can access all [required URLs for Azure Virtual Desktop](/azure/virtual-desktop/safe-url-list) to maintain connectivity and service functionality.

### Recommendations  

| Recommendation | Benefit |
|---|---|
| Use an [Azure Firewall](/azure/firewall/protect-azure-virtual-desktop) or third-party Networking Virtual Appliance (NVA) for east-west and north-south protection and filtering. <br><br> If you're not using Azure Firewall or a third-party NVA and have north-south traffic, consider [outbound access](/azure/virtual-network/ip-services/default-outbound-access) methods available | Protects against threats, controls traffic flow, and ensures compliance |
| To segment/isolate your subnets and workloads by controlling their traffic flow, implement [Network Security Groups](/azure/virtual-network/network-security-groups-overview) (NSG) | Enhances security by isolating workloads and limiting lateral movement |
| To reduce administrative overhead when scaling your network access and controlling traffic flow for your session hosts, use [Application Security Groups](/azure/virtual-network/application-security-groups) (ASG) to group session hosts and define network security policies in your NSGs | ASGs allows you to reuse your security policy at scale without needing to manually maintain explicit IP addresses |
| Where possible, utilize [service tags](/azure/virtual-network/service-tags-overview) rather than specific IP addresses. | Reduces administrative overhead, simplifies rule management, and lowers risk of misconfiguration |
| Implement [Private Link](/azure/virtual-desktop/private-link-overview) to securely connect to remote Azure Virtual Desktop and related services privately. <br><br> When you use this service with Azure Virtual Desktop, you can disable the public endpoints for Azure Virtual Desktop control plane components and use private endpoints to avoid using public IP addresses | Retains traffic on the Microsoft network, improves privacy, and reduces exposure to the public internet |
| For VMs not protected by a Firewall, consider [Just-in-time](/azure/defender-for-cloud/just-in-time-access-overview) access if any direct admin access is needed | Minimizes exposure windows and reduces risk of unauthorized access |
| Configure your network rules in Network Security Groups or NVAs on the [least-privilege access Zero Trust principle](/security/zero-trust/zero-trust-overview#guiding-principles-of-zero-trust) | Minimizes attack surface and limits lateral movement in your network |

### Tradeoffs

:::image type="icon" source="../_images/trade-off.svg"::: **Private Link**

Enabling Private Link and private endpoints enhances security and privacy by keeping traffic on the Microsoft network and off the public internet. However, this configuration introduces extra costs, including charges for each private endpoint. Session hosts also require connectivity to name resolution services that can [resolve private endpoint IP addresses](/azure/private-link/private-endpoint-dns), causing potential Domain Name System (DNS) management overhead.

> [!TIP]
> Use [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) to track and optimize network spend related to Private Link and other networking features.

Carefully evaluate your security requirements and budget to determine if the benefits of Private Link justify the extra expense for your Azure Virtual Desktop deployment.

## Network Segmentation  

Effective network segmentation is critical for supporting Azure Virtual Desktop (AVD) at scale. When designing an AVD environment, prioritize flexibility and future growth by allocating dedicated virtual networks and subnets for each landing zone. This approach helps prevent IP address conflicts, simplifies management, and ensures that the network can accommodate other resources and evolving workloads over time. By planning for expansion and isolating workloads appropriately, you maintain both operational efficiency and security as your AVD deployment grows.

> [!NOTE]
> Refactoring IP address and subnet schemes to accommodate growth can be labor-intensive. Avoid creating a large number of small subnets unless necessary, as it adds management overhead with limited security value. Overlapping IP address ranges are a common pitfall—assign IP management to network architects and review regularly.

### Recommendations

> [!TIP]
> Be aware of networking limits, for more details see [Azure networking limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-networking-limits).

| Recommendation | Benefit |
|---|---|
| Size virtual networks and subnets according to your scaling strategy. Choose fewer, larger virtual networks to accommodate projected growth without operational burden | Ensures the network scales efficiently and avoids address-space limitations |
| Ensure that there isn't overlapping address space with other virtual networks that you need to communicate with, and plan the address space in advance | Prevents conflicts and supports a smooth, scalable network architecture |
| Regularly review and update network segmentation as workloads evolve | Maintains operational efficiency and security |
| Segregate subnets, where appropriate, by either: host pools, user personas and/or workloads. | Allows for the segregation of session hosts that requires similar access and thus reducing any complex NVA or NSG rules |

### Tradeoffs

:::image type="icon" source="../_images/trade-off.svg"::: **Large Subnets**

Large subnets provide ample address space, allowing workloads to scale out seamlessly and safeguarding against unexpected spikes in demand. However, this can lead to inefficient use of IP addresses and, over time, potential IP address exhaustion. Larger subnets may also increase operational costs and complexity. From an operational excellence perspective, balance subnet size to support growth while minimizing waste and management overhead.

## Multi-Region Connectivity 

High availability and optimal user experience for Azure Virtual Desktop (AVD) often require deploying resources across multiple Azure regions. When designing a multi-region AVD environment, consider both platform service proximity and end-user latency to ensure reliable and performant access.

### Recommendations

| Recommendation | Benefit |
|---|---|
| Use [Round-trip Latency Figures](/azure/networking/azure-network-latency#round-trip-latency-figures) to estimate connection round-trip times (RTTs) between Azure regions and inform your region selection process | Ensures optimal user experience |
| Take workload types into account when estimating bandwidth requirements and monitor real-user connections. See [Remote Desktop Protocol (RDP) bandwidth requirements](/azure/virtual-desktop/rdp-bandwidth) | Supports scaling and maintains performance |
| Replicate platform and shared services to each region whenever possible | Reduces latency to identity, name resolution, hybrid connectivity, and storage services |
| Minimize cross-region traffic unless required for business continuity or global user base | Reduces unnecessary expenditure from data transfer between virtual networks |
| Your session hosts should be placed in the same region as your host pool; not mixed | For more information, see [Business Continuity and Disaster Recovery (BCDR) for Azure Virtual Desktop](/azure/architecture/example-scenario/azure-virtual-desktop/azure-virtual-desktop-multi-region-bcdr) |

<!--  Don't mix session hosts in single host pool. Session host update is not supported for session hosts with multiple regions An existing pooled host pool with a session host configuration with session hosts that are all in the same Azure region and resource group. Personal host pools aren't supported. https://learn.microsoft.com/en-us/azure/virtual-desktop/session-host-update-configure?tabs=portal#prerequisites --> 

<!--  Worth mentioning about costs incurred between virtual networks especially considering if you're replicating services across regions  -->

## Hybrid Networking

Hybrid networking enables organizations to maintain access to on-premises resources such as shared or platform services, data, or applications while using the scalability and flexibility of Azure Virtual Desktop (AVD). This approach is essential for scenarios where workloads span both cloud and on-premises environments, or where regulatory or operational requirements necessitate hybrid connectivity.

> [!NOTE]
> Hybrid networking isn't Azure Virtual Desktop specific, for guidance on this topic refer to [Connect an on-premises network to Azure](/azure/architecture/reference-architectures/hybrid-networking/). You can also review the best practices and recommendations in the Cloud Adoption Framework [Network topology and connectivity](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity) article.


### Tradeoffs

:::image type="icon" source="../_images/trade-off.svg"::: **Hybrid Connectivity Costs**

Hybrid networking introduces additional costs for ExpressRoute circuits, VPN gateways, and data transfer between Azure and on-premises environments. Monitor usage by using [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) to track and optimize hybrid network spend.

## Next steps

Explore best practices for monitoring your infrastructure and workloads. Proactive monitoring helps you detect issues early, optimize performance, and ensure security and compliance.

> [!div class="nextstepaction"]
> [Monitoring](./monitoring.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)

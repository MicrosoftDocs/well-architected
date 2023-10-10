---
title: Networking and connectivity considerations for Azure Virtual Desktop workloads
description: Understand Azure Virtual Desktop networking concepts. See techniques for improving the security, operations, and scalability of networks.
author: swathibhat1
ms.author: swathibhat
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Networking and connectivity considerations for Azure Virtual Desktop workloads

This article discusses the networking design area of a Microsoft Azure Virtual Desktop workload. It's critical to design and implement Azure networking capabilities for your Azure Virtual Desktop landing zone. As a foundation, this article uses several Azure Well-Architected Framework [enterprise-scale landing zone architectural principles and recommendations](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity). By building on this guidance, this article shows you how to manage network topology and connectivity at scale.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Client latency

*Impact: Performance Efficiency*

The latency between end users and session hosts is a key aspect that affects the Azure Virtual Desktop user experience. You can use the [Azure Virtual Desktop Experience Estimator](https://azure.microsoft.com/products/virtual-desktop/assessment) tool to help estimate connection round-trip times (RTTs). Specifically, this tool estimates RTTs from user locations through the Azure Virtual Desktop service to each Azure region in which you deploy virtual machines (VMs).

To assess the quality of your end-user experience:

- Test end-to-end latencies in development, testing, and proof-of-concept environments. This test should take into account the actual experience of your users. It should consider factors like network conditions, end-user devices and the configuration of the deployed VMs.
- Keep in mind that latency is only one aspect of connectivity with remote protocols. Bandwidth and user workload also affect your end-user experience.

##### Assessment question

How have you designed your overall network infrastructure for Azure Virtual Desktop to help ensure reliable and high-speed internet connectivity for geographically distributed clients while maintaining an acceptable latency threshold and prioritizing Azure Virtual Desktop traffic over other network traffic?

##### Recommendations

- Use [Azure Virtual Desktop Experience Estimator](https://azure.microsoft.com/products/virtual-desktop/assessment) to gather estimated latency values.
- Test latencies from your Azure virtual networks to your on-premises systems.

##### Assessment question

Do any of your clients use a virtual private network (VPN) to connect to your virtual network?

##### Recommendations

- For clients who use a point-to-site (P2S) VPN connection, use a split tunnel that's based on User Datagram Protocol (UDP).
- For on-site clients who use a VPN or Azure ExpressRoute, use Remote Desktop Protocol (RDP) Shortpath with a managed network.

## On-premises connectivity (hybrid networking)

*Impact: Performance Efficiency, Operational Excellence*

Some organizations use hybrid models that include on-premises and cloud resources. In many hybrid cases, end-user workflows that run on Azure Virtual Desktop need to reach on-premises resources such as shared or platform services, data, or applications.

When you implement hybrid networking, review best practices and recommendations in the Cloud Adoption Framework [Network topology and connectivity](/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity) article.

It's important to align with the Azure Virtual Desktop scaling model that's described in [Integrate an Azure Virtual Desktop workload with Azure landing zones](./landing-zone-integration.md). To follow this model:

- Assess the latency and bandwidth requirements of Azure Virtual Desktop workflows that connect to on-premises systems. This information is crucial when you design your hybrid networking architecture.
- Ensure that there are no overlapping IP addresses between your Azure Virtual Desktop subnets and your on-premises networks. We recommend that you assign the task of IP addressing to the network architects who are the owners of your connectivity subscription.
- Give every Azure Virtual Desktop landing zone its own virtual network and subnet configuration.
- Size subnets appropriately by considering potential growth when you determine the amount of IP address space that's needed.
- Use smart IP classless inter-domain routing (CIDR) notation to avoid wasting IP address space.

##### Assessment question

If you use hybrid networking, are you aware of the connectivity options that are available and their impact on performance?

##### Recommendations

- Review best practices for connecting Azure virtual networks to on-premises systems.
- Test latencies from your Azure virtual networks to your on-premises systems.
- Ensure that no overlapping IP addresses are used in your Azure Virtual Desktop landing zone.
- Give every Azure Virtual Desktop landing zone its own virtual network and subnet configuration.
- Consider potential growth when you size Azure Virtual Desktop subnets.

## Multi-region connectivity

*Impact: Performance Efficiency, Cost Optimization*

For your Azure Virtual Desktop multi-region deployment to offer the best possible experience to your end users, your design needs to take the following factors into consideration:

- Platform services, such as identity, name resolution, hybrid connectivity, and storage services. Connectivity from Azure Virtual Desktop session hosts to these services is key for the service to be functional. As a result, the ideal design aims to lower the latency from Azure Virtual Desktop landing zone subnets to these services. You can accomplish this goal by replicating services to each region or making them available via the connection with the lowest possible latency.
- End-user latency. When you select locations to use for an Azure Virtual Desktop multi-region deployment, it's important to account for the latency that users experience when they connect to the service. We recommend that you gather latency data from your end-user population by using the [Azure Virtual Desktop Experience Estimator](https://azure.microsoft.com/products/virtual-desktop/assessment/) when you select Azure regions to deploy your session hosts to.

Also consider the following factors:

- Application dependencies across regions.
- VM SKU availability.
- Networking costs that are associated with internet egress, cross region traffic, and hybrid (on-premises) traffic that your application or workload dependencies require.
- The extra load that the [FSLogix cloud cache](/fslogix/concepts-fslogix-cloud-cache) feature places on networking. This factor is only relevant if you use this feature to replicate user profile data between different regions. Also consider the cost of the increased network traffic and storage that this feature uses.

If possible, use VM SKUs that offer accelerated networking. In workloads that use high-bandwidth, accelerated networking can lower CPU utilization and latency.

Your network's available bandwidth dramatically impacts the quality of your remote sessions. As a result, it's a good practice to assess networking bandwidth requirements for users to ensure that enough bandwidth is available for on-premises dependencies.

##### Assessment question

When you use Azure Virtual Desktop in multiple regions, are you aware of networking factors that impact the architecture?

##### Recommendations

- Whenever your internal policies allow, replicate platform and shared services to each region.
- If possible, use VM SKUs that offer accelerated networking.
- Include end-user latency estimations in your region selection process.
- When you estimate bandwidth requirements, take workload types into account, and monitor real-user connections.

## Network security

*Impact: Security, Cost Optimization, Operational Excellence*

Traditionally, network security has been the linchpin of enterprise security efforts. But cloud computing has increased the requirement for network perimeters to be more porous, and many attackers have mastered the art of attacks on identity system elements. The following points provide an overview of the minimum firewall requirements for deploying Azure Virtual Desktop. This section also provides recommendations for connecting to a firewall and reaching the apps that require this service.

- Traditional network controls that are based on a trusted-intranet approach don't effectively provide security assurances for cloud applications.
- Integrating logs from network devices and raw network traffic provides visibility into potential security threats.
- Most organizations end up adding more resources to networks than initially planned. As a result, IP address and subnet schemes need to be refactored to accommodate the extra resources. This process is labor-intensive. There's limited security value in creating a large number of small subnets and then trying to map network access controls, such as security groups, to each of them.

For general information about protecting assets by placing controls on network traffic, see [Network security](/azure/well-architected/security/design-network).

##### Assessment question

What measures have you implemented to improve network security?

##### Recommendations

- Understand the configurations that are needed to use Azure Firewall in your deployment. For more information, see [Use Azure Firewall to protect Azure Virtual Desktop deployments](/azure/firewall/protect-azure-virtual-desktop).
- Create network security groups and application security groups to segment your Azure Virtual Desktop traffic. This practice helps you isolate your subnets by controlling their traffic flows.
- Use [service tags](/azure/virtual-network/service-tags-overview) instead of specific IP addresses for Azure services. Because addresses change, this approach minimizes the complexity of frequently updating network security rules.
- Familiarize yourself with the [required URLs for Azure Virtual Desktop](/azure/virtual-desktop/safe-url-list).
- If you use forced tunneling to route traffic to a firewall or network virtual appliance (NVA), use a route table to allow Azure Virtual Desktop traffic to bypass the forced tunneling rule. Otherwise, forced tunneling can affect the performance and reliability of your clients' connectivity.
- Use private endpoints to help protect platform as a service (PaaS) solutions like Azure Files and Azure Key Vault. But consider the cost of using private endpoints.
- Adjust the configuration options for Azure Private Link. When you use this service with Azure Virtual Desktop, you can disable the public endpoints for Azure Virtual Desktop control plane components and use private endpoints to avoid using public IP addresses.
- If you use Active Directory Domain Services (AD DS), implement strict firewall policies. Base those policies on the traffic that's required across your domain.
- Consider using Azure Firewall or NVA web filtering to help protect your end users' access to the internet from Azure Virtual Desktop session hosts.

## Private endpoints (Private Link)

*Impact: Security*

By default, connections to Azure Virtual Desktop resources are established through a publicly accessible endpoint. In some scenarios, the traffic needs to use private connections. These scenarios can use Private Link to privately connect to remote Azure Virtual Desktop resources. For more information, see [Azure Private Link with Azure Virtual Desktop](/azure/virtual-desktop/private-link-overview). When you create a [private endpoint](/azure/private-link/private-endpoint-overview), traffic between your virtual network and the service remains on the Microsoft network. Your service isn't exposed to the public internet.

You can use Azure Virtual Desktop private endpoints to support the following scenarios:

- Your clients, or end users, and your session host VMs both use private routes.
- Your clients, or end users, use public routes while your session host VMs use private routes.

Azure Virtual Desktop session hosts have the same name resolution requirements as any other infrastructure as a service (IaaS) workloads. As a result, the session hosts require connectivity to name resolution services that are configured to resolve private endpoint IP addresses. Consequently, when you use private endpoints, you need to configure specific DNS settings. For detailed information, see [Azure private endpoint DNS configuration](/azure/private-link/private-endpoint-dns).

Private Link is also available for other Azure services that work in conjunction with Azure Virtual Desktop, such as Azure Files and Key Vault. We recommend that you also implement private endpoints for these services to keep the traffic private.

##### Assessment question

Are you familiar with Azure Virtual Desktop name resolution requirements?

##### Recommendations

- Understand how Private Link works with Azure Virtual Desktop. For more information, see [Azure Private Link with Azure Virtual Desktop](/azure/virtual-desktop/private-link-overview).
- Understand the DNS configurations that are needed for Azure private endpoints. For more information, see [Azure private endpoint DNS configuration](/azure/private-link/private-endpoint-dns).

## RDP Shortpath

*Impact: Performance Efficiency, Cost Optimization*

RDP Shortpath is a feature of Azure Virtual Desktop that's available for managed and unmanaged networks.

- For managed networks, RDP Shortpath establishes a direct connection between a remote desktop client and a session host. The transport is based on UDP. By removing extra relay points, RDP Shortpath reduces the round-trip time, which improves the user experience in latency-sensitive applications and input methods. To support RDP Shortpath, an Azure Virtual Desktop client needs a direct line of sight to the session host. The client also needs to install the Windows Desktop client and run Windows 11, Windows 10, or Windows 7.
- For unmanaged networks, two connection types are possible:
  - Direct connectivity is established between the client and session host. Simple traversal underneath network address translation (STUN) and interactive connectivity establishment (ICE) are used to establish the connection. This configuration enhances transport reliability for Azure Virtual Desktop. For more information, see [How RDP Shortpath works](/azure/virtual-desktop/rdp-shortpath?tabs=public-networks#how-rdp-shortpath-works).
  - An indirect UDP connection is established. It overcomes network address translation (NAT) limitations by using the Traversal Using Relay NAT (TURN) protocol with a relay between the client and session host.

With transport that's based on Transmission Control Protocol (TCP), the outbound traffic from a VM to an RDP client flows through an Azure Virtual Desktop gateway. With RDP Shortpath, the outbound traffic flows directly between the session host and the RDP client over the internet. This configuration helps eliminate a hop and improve latency and the end-user experience.

##### Assessment question

Do you use RDP Shortpath to establish a UDP-based connection between your clients and session hosts?

##### Recommendations

- To help improve latency and your end-user experience, use RDP Shortpath.
- Be aware of the availability of RDP Shortpath connection models.
- Be aware of RDP Shortpath charges.

## Next steps

Now that you've examined networking in Azure Virtual Desktop, investigate best practices for monitoring your infrastructure and workload.

> [!div class="nextstepaction"]
> [Monitoring](./monitoring.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
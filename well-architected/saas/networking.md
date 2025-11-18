---
title: Networking for SaaS Workloads on Azure
description: Learn about the networking considerations, including deployment strategies, topology, security, and connectivity, for SaaS workloads on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/05/2025
ms.update-cycle: 1095-days
ms.topic: concept-article
ms.collection: learn-startups
---

# Networking for SaaS workloads on Azure

Your network provides the backbone for how customers access your software as a service (SaaS) application, and it enables communication between your solution's components. The way you design your network has a direct effect on your solution's security, operations, cost, performance, and reliability. A structured approach to your networking strategy becomes even more important as your cloud environment grows.

## Decide on a network deployment strategy and topology

SaaS solutions have unique networking requirements. As you onboard more customers and their usage increases, networking requirements change. Handling growth can be challenging because of limited resources, like IP address ranges. Your network design affects security and customer isolation. Plan your network strategy to help manage growth, improve security, and reduce operational complexity.

### Design considerations

- **Plan your network deployment strategy based on your tenancy model.** Decide whether you want to share network resources among customers, dedicate resources to a single customer, or a combination of those options. This choice affects your application's functionality, security, and customer isolation.

    It's common to share networking resources, like virtual networks and Azure Front Door profiles, among multiple customers. This approach reduces costs and operational overhead. It also simplifies connectivity. You can easily connect a customer's resources with shared resources, such as shared storage accounts or a control plane.

    However, dedicated networking resources for each customer might be necessary to establish high security and compliance. For example, to support a high degree of network segmentation between customers, you can use virtual networks as the boundary. Dedicated resources might be necessary when the number of network resources across all customers exceeds the capacity of a single shared network.

    Plan for the number of network resources that each customer needs by considering immediate and future requirements. Customer requirements and Azure resource limits might force specific outcomes. Different resources might require different deployment strategies, such as using separate networks for virtual network peering with customer-owned Azure virtual networks.

    For more information about sharing resources in a SaaS solution, see [Resource organization for SaaS workloads](./resource-organization.md).

- **Understand network topologies.** Network topologies typically fall into three categories:
  - **Flat network**: A single, isolated network that has subnets for segmentation. Use a flat-network topology when you have a single multitenant application with a simple network layout. Flat networks can reach resource limits and require more networks as you scale, which increases overhead and costs. If you plan to host multiple applications or use dedicated deployment stamps within the same virtual network, you might need a complex network layout.
  - **Hub and spoke**: A centralized hub network that peers to isolated spoke networks. Use a hub-and-spoke topology for high scalability and customer isolation because each customer or application has its own spoke and only communicates with the hub. You can quickly deploy more spokes as needed so that all spokes can use resources in the hub. *Transitive*, or spoke-to-spoke, communication through the hub is disabled by default, which helps maintain customer isolation in SaaS solutions.
  - **No network**:  Use a no-network topology for Azure platform as a service (PaaS) solutions where you can host complex workloads without deploying virtual networks. For example, Azure App Service allows for direct integration with other PaaS services over the Azure backbone network. Although this approach simplifies management, it restricts flexibility in deploying security controls and the ability to optimize performance. This approach can work well for cloud-native applications. As your solution evolves, expect to transition to a hub-and-spoke topology over time.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complexity and security**. Starting without a defined network boundary can reduce the operational burden of managing network components like security groups, IP address space, and firewalls. However, a network perimeter is essential for most workloads. In the absence of network security controls, rely on strong identity and access management to protect your workload from malicious traffic.

- **Understand how multi-region architectures affect network topologies.** In a multi-region architecture that uses virtual networks, most networking resources are deployed in each region separately because firewalls, virtual network gateways, and network security groups can't be shared between regions.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Decide which network components are shared and which components are dedicated to the customer. <br><br> Share resources that are charged per instance, like Azure Firewall, Azure Bastion, and Azure Front Door. | Balance support between your security and isolation requirements while reducing your cost and operational burden. |
| Start with a flat topology or no-network approach. <br><br> Always review your security requirements first because these approaches offer limited isolation and traffic controls. | You can reduce the complexity and cost of your solution by using simpler network topologies. |
| Consider hub-and-spoke topologies for complex needs or when you deploy dedicated virtual networks for each customer. Use the hub to host shared network resources across customer networks. | You can scale more easily and improve your cost efficiency by sharing resources through your hub network. |

## Design a highly secure network perimeter

Your network perimeter establishes the security boundary between your application and other networks, including the internet. By documenting your network perimeter, you can distinguish between the following types of traffic flows:

- Ingress traffic, which arrives into the network from an external source.
- Internal traffic, which goes between components within your network.
- Egress traffic, which leaves the network.

Each flow involves different risks and controls. For example, you need multiple security controls to inspect and process ingress traffic.

> [!IMPORTANT]
> As a general best practice, always follow a Zero Trust approach. Make sure all traffic is controlled and inspected, including internal traffic.
>
> Your customers might also have specific compliance requirements that influence your architecture. For example, if they need [SOC 2 compliance](/azure/governance/policy/samples/soc-2#security-measures-against-threats-outside-system-boundaries), they must implement various network controls, including a firewall, web application firewall (WAF), and network security groups, to fulfill the security requirements. Even if you don't need to comply immediately, consider these extensibility factors when you design your architecture.
>
> For more information, see [SE:06 Recommendations for networking and connectivity](/azure/well-architected/security/networking).

### Design considerations

- **Protect and manage ingress traffic.** Inspect this traffic for incoming threats.

    Firewalls enable you to block malicious IP addresses and complete advanced analyses to protect against intrusion attempts. However, firewalls can be costly. Assess your security requirements to determine whether a firewall is required.

    Web applications are vulnerable to common attacks, like SQL injection, cross-site scripting, and other OWASP top 10 vulnerabilities. The Azure web application firewall feature helps protect against those attacks and integrates with Azure Application Gateway and Azure Front Door. Review the tiers for these services to understand which WAF capabilities are in which products.

    Distributed denial-of-service (DDoS) attacks are a risk for internet-facing applications. Azure provides a basic level of protection at no cost. [Azure DDoS Protection](/azure/ddos-protection/ddos-protection-overview#key-features) provides advanced protection by learning your traffic patterns and adjusting protections accordingly, but these features come at a cost. If you use Azure Front Door, take advantage of the built-in [DDoS capabilities](/azure/frontdoor/front-door-ddos).
  
    Beyond security, you can also manipulate ingress traffic to improve your application's performance by using caching and load balancing.

    Consider using a reverse proxy service like Azure Front Door for global HTTP and HTTPS traffic management. Alternatively, use Application Gateway or other Azure services for inbound traffic control. For more information, see [Load-balancing options](/azure/architecture/guide/technology-choices/load-balancing-overview).

- **Protect internal traffic.** Ensure that traffic between your application and its components is secure to help prevent malicious access. Protect these resources and improve performance by using internal traffic instead of routing over the internet. Azure Private Link is commonly used to connect to Azure resources through an internal IP address within your network. For some resource types, service endpoints can be a more cost-effective alternative.
  
   If you enable public internet connectivity for your resources, understand how to restrict traffic by using IP addresses and application identities, such as managed identities.

- **Protect egress traffic.** In some solutions, inspect outbound traffic to prevent data exfiltration, especially for regulatory compliance and enterprise customers. Use firewalls to manage and review egress traffic by blocking connections to unauthorized locations.

- **Plan how you scale outbound connectivity and SNAT.** Source network address translation (SNAT) port exhaustion can affect multitenant applications. These applications often need distinct network connections for each tenant, and sharing resources between customers increases the risk of SNAT exhaustion as your customer base grows.
  
   You can mitigate SNAT exhaustion by using Azure NAT Gateway, firewalls like Azure Firewall, or a combination of the two approaches.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Maintain a catalog of the network endpoints that are exposed to the internet. Capture details such as the IP address (if it's static), hostname, ports, protocols that the endpoints use, and the justification for connections. <br><br>Document how you plan to protect each endpoint. | This list forms the basis of your perimeter definition so that you can make explicit decisions about how to manage traffic through your solution. |
| Understand Azure service capabilities to limit access and enhance protection. <br><br> For example, you need more controls to expose storage account endpoints to customers. These controls include shared access signatures, storage account firewalls, and separate storage accounts for internal and external use. | You can select controls that meet your security, cost, and performance needs. |
| For HTTP and HTTPS-based applications, use a reverse proxy, like Azure Front Door or Application Gateway. | Reverse proxies provide a broad range of capabilities for performance improvements, resiliency, and security. They also help reduce operational complexity. |
| Inspect ingress traffic by using a WAF. <br><br> Avoid exposing web-based resources such as App Service or Azure Kubernetes Service (AKS) directly to the internet. | You can more effectively protect your web applications against common threats and reduce the overall exposure of your solution. |
| Protect your application against DDoS attacks. <br><br> Use Azure Front Door or DDoS Protection depending on the protocols that your public endpoints use. | Protect your solution from a common type of attack. |
| If your application requires egress connectivity at scale, use NAT Gateway or a firewall to provide extra SNAT ports. | You can support higher levels of scale. |

## Cross-network connectivity

For some scenarios, you might need to connect to resources that are outside of Azure. These resources include data within a customer's private network or assets on a different cloud provider in a multicloud setup. These needs can complicate your network design because they require various approaches to implement cross-network connectivity based on your specific requirements.

### Design considerations

- **Identify the endpoints that the application needs to connect to.** The application might need to communicate with other services, such as storage services and databases. Document their owner, location, and connectivity type. You can then choose the appropriate method to connect to these endpoints. The following table describes common approaches.

    | Resource location | Owner | Connectivity options to consider |
    |---|---|---|
    | Azure | Customer | <ul><li>Private endpoint (across Microsoft Entra tenants)</li><li>Virtual network peering (across Microsoft Entra tenants)</li><li>Service endpoint (across Microsoft Entra tenants)</li></ul> |
    | Other cloud provider | ISV or customer | <ul><li>Site-to-site VPN</li><li>Azure ExpressRoute</li><li>Internet</li></ul> |
    | On-premises |  ISV or customer | <ul><li>Site-to-site VPN</li><li>ExpressRoute</li><li>Internet</li></ul> |

    - **Private Link and private endpoints** provide secure connectivity to various Azure resources, including internal load balancers for virtual machines. They enable private access to your SaaS solution for customers, but they come with cost considerations.

      > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and cost.** Private Link helps ensure that your traffic remains within your private network. We recommend Private Link for network connectivity across Microsoft Entra tenants. However, each private endpoint incurs costs, which can add up depending on your security needs. Service endpoints can be a cost-effective alternative. They keep traffic on the Microsoft backbone network while providing a level of private connectivity.
 
    - **Service endpoints** route traffic to PaaS resources via the Microsoft backbone network, which secures service-to-service communication. Service endpoints can be cost-effective for high-bandwidth applications, but you need to configure and maintain access control lists for security. Support for service endpoints across Microsoft Entra tenants varies by Azure service. Check the product documentation for each service that you use.

    - **Virtual network peering** connects two virtual networks and allows resources in one network to access IP addresses in the other. It facilitates connectivity to private resources in an Azure virtual network. You can manage access by using network security groups, but enforcing isolation can be challenging. Therefore, it's important to plan your network topology based on specific customer needs.

    - **Virtual private networks (VPNs)** create a secure tunnel through the internet between two networks, including across cloud providers and on-premises locations. Site-to-site VPNs use network appliances in each network for configuration. They offer a low-cost connectivity option, but they require setup and don't guarantee predictable throughput.

    - **ExpressRoute** provides a dedicated, high-performance, private connection between Azure and other cloud providers or on-premises networks. It ensures predictable performance and bypasses internet traffic, but it comes with higher costs and requires more complex configuration.

- **Plan based on the destination.** You might need to connect to resources in different Microsoft Entra tenants, especially if the target resource is in a customer's Azure subscription. Consider using private endpoints, a site-to-site VPN, or peered virtual networks. For more information, see [Create a virtual network peering between different subscriptions](/azure/virtual-network/create-peering-different-subscriptions).

    To connect to resources that another cloud provider hosts, it's common to use public internet connectivity, a site-to-site VPN, or ExpressRoute. For more information, see [Connectivity to other cloud providers](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-other-providers).

- **Understand the effects of connectivity on your network topology.** An Azure virtual network can have only one virtual network gateway, which can connect to multiple locations via a site-to-site VPN or ExpressRoute. But, there are limits on the number of connections that you can make through a gateway, and isolating customer traffic can be challenging. For multiple connections to different locations, plan your network topology accordingly, possibly by deploying a separate virtual network for each customer.

- **Understand the implications for IP address planning.** Some connectivity approaches automatically provide network address translation (NAT), which helps you avoid the problems that overlapping IP addresses cause. However, virtual network peering and ExpressRoute don't perform NAT. When you use these methods, plan your network resources and IP address allocations carefully to avoid overlapping IP address ranges and ensure future growth. IP address planning can be complex, especially when you connect to external sources like customers, so consider potential conflicts with their IP address ranges.

- **Understand network egress billing.** Azure typically bills for outbound network traffic when it leaves the Microsoft network or moves between Azure regions. When designing multi-region or multicloud solutions, it's important to understand the cost implications. Architectural choices, such as using Azure Front Door or ExpressRoute, can affect how you're billed for network traffic.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Choose private networking approaches for connecting across networks to prioritize security. <br><br> Only consider routing over the internet after you evaluate the associated security and performance implications. | Private traffic traverses a secured network path, which helps reduce many types of security risks. |
| When you connect to resources that customers' Azure environments host, use Private Link, service endpoints, or virtual network peerings. | You can keep traffic on the Microsoft network, which helps reduce costs and operational complexity compared to other approaches. |
| When you connect across cloud providers or to on-premises networks, use site-to-site VPNs or ExpressRoute. | These technologies provide secure connections between providers.  |

## Deploy to your customers' environments

Your business model might require you to host the application or its components within a customer's Azure environment. The customer manages their own Azure subscription and directly pays the cost of resources required to run the application. As the solution provider, you're responsible for managing the solution, including the initial deployment, applying configuration, and deploying updates to the application.

In such situations, customers often bring their own network and deploy your application into a network space that they define. Azure Managed Applications provide capabilities to facilitate this process. For more information, see [Use an existing virtual network with Azure Managed Applications](/azure/azure-resource-manager/managed-applications/existing-vnet-integration).

### Design considerations

- **Prepare for different IP address ranges and conflicts.** When customers deploy and manage virtual networks, they're responsible for handling network conflicts and scaling. However, you should anticipate different customer usage scenarios. Plan for deployments in environments with minimal IP address space by using IP addresses efficiently. Avoid hard coding IP address ranges to prevent overlaps with customer ranges.

    Alternatively, deploy a dedicated virtual network for your solution. You might use Private Link or virtual network peering to enable customers to connect to the resources. For more information, see [Cross-network connectivity](#cross-network-connectivity). If you have defined ingress and egress points, evaluate NAT as an approach to eliminate problems that IP address overlaps cause.

- **Provide network access for management purposes.** Review the resources that you deploy into customer environments and plan how to access them to monitor, manage, or reconfigure them. When you deploy resources with private IP addresses into a customer-owned environment, ensure that you have a network path to reach them from your own network. Consider how you facilitate both application and resource changes, such as pushing a new version of the application or updating an Azure resource configuration.

    In some solutions, you can use capabilities that Azure Managed Applications provide, such as [just-in-time access](/azure/azure-resource-manager/managed-applications/request-just-in-time-access) and [deployment of updates to applications](/azure/azure-resource-manager/managed-applications/update-managed-resources). If you need more control, you can host an endpoint within the customer's network that your control plane can connect to. This approach gives you access to your resources. This method requires more Azure resources and development to meet security, operational, and performance requirements. For an example of how to implement this approach, see [Azure Managed Applications updating sample](https://github.com/Azure-Samples/ama-update-sample).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Use Azure Managed Applications to deploy and manage customer-deployed resources. | Azure Managed Applications provide a range of capabilities that enable you to deploy and manage resources within a customer's Azure subscription. |
| Minimize the number of IP addresses that you consume within the customer's virtual network space. | Customers often have restricted IP address availability. By minimizing your footprint and decoupling your scaling from their IP address usage, you can broaden the number of customers who can use your solution and enable higher levels of growth. |
| Plan how to gain network access to manage resources in customer environments so that you can do monitoring, resource configuration changes, and application updates. | You can directly configure the resources that you manage. |
| Decide whether you want to deploy a dedicated virtual network or integrate with a customer's existing virtual network. | By deciding which virtual network to use ahead of time, you can make sure that you can meet your customers' requirements for isolation, security, and integration with their other systems. |
| Disable public access on Azure resources by default. Choose private ingress where possible. | You reduce the scope of network resources that you and your customers need to protect. |

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information related to network design:

- [Architectural approaches for networking in multitenant solutions](/azure/architecture/guide/multitenant/approaches/networking)
- [Tenancy models for a multitenant solution](/azure/architecture/guide/multitenant/considerations/tenancy-models)
- [Hub-and-spoke network topology](/azure/architecture/networking/architecture/hub-spoke)
- [Azure NAT Gateway considerations for multitenancy](/azure/architecture/guide/multitenant/service/nat-gateway)
- [Architectural approaches for tenant integration and data access](/azure/architecture/guide/multitenant/approaches/integration)

## Next step

Learn about the data platform considerations for data integrity and performance for SaaS workloads on Azure.

> [!div class="nextstepaction"]
> [Design area: Data for SaaS workloads on Azure](./data.md)

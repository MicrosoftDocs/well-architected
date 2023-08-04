---
title: Networking considerations for Azure VMware Solution
description: Understand Azure VMware Solution networking concepts. See techniques for improving the security, operations, and scalability of networks.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Networking considerations for Azure VMware Solution

This article discusses the networking design area of an Azure VMware Solution workload. Well-architected networks are critical for enabling connectivity, optimizing response times, distributing traffic, and helping to ensure continuous availability of workloads in Azure VMware Solution.

## Distribute loads for high availability

*Impact: Reliability, Performance efficiency*

To achieve scalability and optimize performance, you should distribute traffic across destinations within your Azure VMware Solution infrastructure. When you load balance Azure VMware Solution traffic, you can distribute it through various algorithms, such as algorithms that consider performance and weight. Distributing incoming traffic improves the scale and reliability of your workload applications. If your application spans multiple software-defined datacenters (SDDCs), an Azure load balancer can distribute traffic across all your environments.

##### Recommendations

- Use a load balancer such as VMware NSX Advanced Load Balancer for even distribution of traffic. Support internal-facing and external-facing application gateways. Use the load balancer for routing, application delivery, and TLS termination.
- For workloads that extend into Azure, use Azure Application Gateway. This load balancer distributes traffic to enhance application performance. Application Gateway also offers intrusion detection and prevention system (IDPS) and Azure Web Application Firewall capabilities. Azure Load Balancer can distribute traffic across zones to provide high availability and fault tolerance for workloads that span multiple Azure availability zones.

## Minimize distance in global distribution

*Impact: Reliability, Performance efficiency, Cost optimization*

For applications with a global presence, it's important to minimize the distance between instances and users. You can achieve this objective by routing traffic to the nearest Azure VMware Solution SDDC. If you use a traffic manager, you can also potentially lower outbound data transfer costs. Specifically, you can send users to the nearest Azure VMware Solution deployment or edge location. Reducing the distance that data travels helps you avoid long data transfers.

##### Recommendations

- For applications that span multiple regions, consider deploying a Domain Name System–based global traffic load balancing solution such as Azure Traffic Manager.
- Create traffic-routing profiles. Configure various routing methods such as priority-based routing, weighted round-robin, performance-based routing, or geographic-based routing.

## Deliver content

*Impact: Performance, Cost optimization*

High-traffic applications require optimal retrieval of content. Optimization techniques such as compression and HTTP accelerators can improve the retrieval performance of assets within your Azure VMware Solution environment. You can use compression techniques on files before you transmit them. This practice can reduce costs by reducing the amount of data that you transmit. A content delivery network caches frequently accessed content. As a result, using a content delivery network with Azure VMware Solution can help you optimize retrieval and distribution. Content delivery networks can also help you save on costs in other ways. Because these networks cache date at edge locations that are close to users, they reduce the distance that data travels.

##### Recommendations

- Use Azure Content Delivery Network to improve responsiveness and reduce latency for users who access your applications and websites.
- Use compression to minimize the payload of static assets.
- Use caching solutions like Redis or Azure Cache for Redis to cache frequently accessed data.

## Use a firewall for internet-facing workloads

*Impact: Security*

Front-facing workloads in Azure VMware Solution are mapped to a public IP address. As a result, they can be exposed to the internet, and they accept incoming connections from external sources. This association with your virtual machine (VM) or load balancer poses risks to your workloads.

##### Recommendations

- For internet-facing applications, use a firewall such as Azure Firewall to inspect Azure VMware Solution traffic to the internet and Azure.
- Make sure that your firewall has rules and access control lists to restrict and filter inbound traffic.

## Secure traffic between internal workloads

*Impact: Security*

The network is a critical perimeter in the Azure VMware Solution environment. Networks help control access, protect data, and mitigate threats. Robust network security measures help ensure the availability and resiliency of Azure VMware Solution workloads. For example, implementing network isolation through segmentation and the use of virtual LANs can help prevent unauthorized access between the components of your Azure VMware Solution environment. You can use network security groups to isolate and protect traffic within your workload virtual networks.

##### Recommendations

- Use network security groups to restrict traffic to VMs and other application components. Also use these groups to restrict traffic in virtual networks, in subnets, and from Azure VMware Solution.  
- Create segments and virtual LANs for your Azure VMware Solution workloads.
- Create firewall rules within NSX-T.

## Design IP addressing schemes for growth

*Impact: Security, Operational excellence*

You can use an intentional subnet security strategy to design Azure VMware Solution and cloud virtual networks for growth. This design involves strategically organizing IP address allocation. You also need to use an IP addressing tool and enforce allocation.

Besides a /22 RFC-1918 address range, workload segments have separate, nonconflicting classless inter-domain routing (CIDR) ranges. Plan to have enough IP addresses for VMs, public IP addresses, and load balancers.

##### Recommendations

- Ensure your IP address range is large enough to accommodate all current and future Azure VMware Solution workloads.
- Use a spreadsheet or IP address management (IPAM) tool to efficiently organize available IP addresses, track IP address usage, and help avoid IP address conflicts.
- Plan for potential increases in devices, segments, or subnets. Use an IP addressing scheme that can handle demand increases.
- Use Dynamic Host Configuration Protocol (DHCP) for dynamic IP address assignment.

## Next steps

Now that you've examined networking in Azure VMware Solution, investigate best practices for monitoring your infrastructure and application.

> [!div class="nextstepaction"]
> [Monitoring](./vmware-monitoring.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./vmware-assessment.md)

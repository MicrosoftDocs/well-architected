---
title: Recommendations for networking and connectivity
description: Learn about network security controls that can filter, block, and detect adversaries who cross network boundaries at various depths of your architecture.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for networking and connectivity

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:05](checklist.md)|Isolate, filter, and control network traffic across both ingress and egress flows. Apply defense in depth principles by using localized network controls at all available network boundaries across both east-west and north-south traffic.|
|---|---|

This guide describes the recommendations for network design. The focus is on **security controls that can filter, block, and detect adversaries crossing network boundaries** at various depths of your architecture.

You can strengthen your identity controls by implementing network-based access control measures. Along with identity-based access control, network security is a high priority for protecting assets. Proper network security controls can provide a defense-in-depth element that can help detect and contain threats, and prevent attackers from gaining entry into your workload.

**Definitions**

| Term |Definition |
|---------|---------|
|East-west traffic | Network traffic that moves within a trusted boundary. |
|Egress flow | Outbound workload traffic. |
|Hostile network | A network that isn't deployed as part of your workload. A hostile network is considered a threat vector. |
|Ingress flow | Inbound workload traffic. |
|Network filtering | A mechanism that allows or blocks network traffic based on specified rules. |
|Network segmentation or isolation | A strategy that divides a network into small, isolated segments, with security controls applied at the boundaries. This technique helps protect resources from hostile networks, such as the internet. |
|Network transformation | A mechanism that mutates network packets to obscure them. |
|North-south traffic | Network traffic that moves from a trusted boundary to external networks that are potentially hostile, and vice versa. |

## Key design strategies

Network security uses **obscurity to protect workload assets from hostile networks**. Resources that are behind a network boundary are hidden until the boundary controls mark the traffic as safe to move forward. Network security design is built on three main strategies:

- **Segment**. This technique **isolates traffic on separate networks by adding boundaries**. For example, traffic to and from an application tier passes a boundary to communicate with other tiers, which have different security requirements. Layers of segmentation actualize the defense-in-depth approach.

  The foremost security boundary is the **networking edge between your application and public networks**. It's important to clearly define this perimeter so that you establish a boundary for isolating hostile networks. The controls on this edge must be highly effective, because this boundary is your first line of defense.

  Virtual networks provide a logical boundary. By design, a virtual network can't communicate with another virtual network unless the boundary has been intentionally broken through peering. Your architecture should take advantage of this strong, platform-provided security measure.

  You can also use other logical boundaries, such as carved-out **subnets within a virtual network**. A benefit of subnets is that you can use them to group together resources that are within an isolation boundary and have similar security assurances. You can then configure controls on the boundary to filter traffic.

- **Filter**. This strategy helps ensure that **traffic that enters a boundary is expected, allowed, and safe**. From a Zero-Trust perspective, filtering explicitly verifies all available data points at the network level. You can place rules on the boundary to check for specific conditions.

  For example, at the header level, the rules can verify that the traffic originates from an expected location or has an expected volume. But these checks aren't sufficient. Even if the traffic exhibits expected characteristics, the payload might not be safe. Validation checks might reveal an SQL injection attack.

- **Transform**. **Mutate packets at the boundary as a security measure.** For example, you can remove HTTP headers to eliminate the risk of exposure. Or you can turn off Transport Layer Security (TLS) at one point and reestablish it at another hop with a certificate that's managed more rigorously.

### Classify the traffic flows

The first step in classifying flows is to study a schematic of your workload architecture. From the schematic, **determine the intent and characteristics of the flow** with respect to the functional utility and operational aspects of your workload. Use the following questions to help classify the flow:

- If the workload needs to communicate with external networks, what should the required level of proximity to those networks be?

- What are the network characteristics of the flow, such as the expected protocol and the source and shape of the packets? Are there any compliance requirements at the networking level?

There are many ways to classify traffic flows. The following sections discuss commonly used criteria.

##### Visibility from external networks

- **Public**. A workload is public facing if its application and other components are reachable from the public internet. The application is exposed through one or more public IP addresses and public Domain Name System (DNS) servers.

- **Private**. A workload is private if it can only be accessed through a private network such as a virtual private network (VPN). It's exposed only through one or more private IP addresses and potentially through a private DNS server.

  In a private network, there's no line of sight from the public internet to the workload. For the gateway, you can use a load balancer or firewall. These options can provide security assurances.

Even with public workloads, **strive to keep as much of the workload private as possible**. This approach forces packets to cross through a private boundary when they arrive from a public network. A gateway in that path can function as a transition point by acting as a reverse proxy.

#### Traffic direction

- **Ingress**. Ingress is inbound traffic that flows toward a workload or its components. To help secure ingress, apply the preceding set of key strategies. Determine what the traffic source is and whether it's expected, allowed, and safe. Attackers who scan public cloud provider IP address ranges can successfully penetrate your defenses if you don't check ingress or implement basic network security measures.

- **Egress**. Egress is outbound traffic that flows away from a workload or its components. To check egress, determine where the traffic is headed and whether the destination is expected, allowed, and safe. The destination might be malicious or associated with data exfiltration risks.

:::image type="content" source="images/networking/internet-network-flow-public-cloud.png" alt-text="Diagram that shows the flow of network traffic flow between Azure deployments and the internet." border="false" lightbox="images/networking/internet-network-flow-public-cloud.png":::

You can also **determine your level of exposure by considering your workload's proximity to the public internet**. For example, the application platform typically serves public IP addresses. The workload component is the face of the solution.

#### Scope of influence

- **North-south**. Traffic that flows between a workload network and external networks is north-south traffic. This traffic crosses the edge of your network. External networks can be the public internet, a corporate network, or any other network that's outside your scope of control.

  Ingress and egress can both be north-south traffic.

  As an example, consider the egress flow of a hub-spoke network topology. You can define the networking edge of your workload so that the hub is an external network. In that case, outbound traffic from the virtual network of the spoke is north-south traffic. But if you consider the hub network within your sphere of control, north-south traffic is extended to the firewall in the hub, because the next hop is the internet, which is potentially hostile.

- **East-west**. Traffic that flows within a workload network is east-west traffic. This type of traffic results when components in your workload communicate with each other. An example is traffic between the tiers of an *n*-tier application. In microservices, service-to-service communication is east-west traffic.

To provide defense in depth, maintain **end-to-end control of security affordances that are included in each hop or that you use when packets cross internal segments**. Different risk levels require different risk remediation methods.

:::image type="content" source="images/networking/network-defense-depth-private-cloud.png" alt-text="Diagram that shows network defense in depth for a private cloud." border="false" lightbox="images/networking/network-defense-depth-private-cloud.png":::

The preceding diagram illustrates network defense in depth in the private cloud. In this diagram, the border between the public and private IP address spaces is significantly farther from the workload than in the public cloud diagram. Multiple layers separate the Azure deployments from the public IP address space.

> [!NOTE]
> Identity is always the primary perimeter. Access management must be applied to networking flows. Use managed identities when you use Azure role-based access control (RBAC) between components of your network.

After you classify flows, perform a segmentation exercise to identify firewall injection points on the communication paths of your network segments. When you **design your network defense in depth across all segments and all traffic types, assume a breach at all points**. Use a combination of various localized network controls at all available boundaries. For more information, see [Segmentation strategies](segmentation.md).

### Apply firewalls at the edge

Internet edge traffic is north-south traffic and includes ingress and egress. To detect or block threats, an edge strategy must mitigate as many attacks as possible to and from the internet.

For egress, **send all internet-bound traffic through a single firewall** that provides enhanced oversight, governance, and control of traffic. For ingress, force all traffic from the internet to go through a network virtual appliance (NVA) or a web application firewall.

- Firewalls are usually singletons that are deployed per region in an organization. As a result, they're shared among workloads and owned by a central team. Make sure that any NVAs that you use are configured to support the needs of your workload.

- We recommend that you use Azure native controls as much as possible.

  In addition to native controls, you can also consider partner NVAs that provide advanced or specialized features. Partner firewall and web application firewall vendor products are available in Azure Marketplace.

  The decision to use native features as opposed to partner solutions should be based on your organization's experience and requirements.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Partner capabilities often provide advanced features that can protect against sophisticated, but typically uncommon, attacks. The configuration of partner solutions can be complex and fragile, because these solutions don't integrate with the cloud's fabric controllers. From a cost perspective, native control is preferred because it's cheaper than partner solutions.

Any technological options that you consider should provide security controls and monitoring for both ingress and egress flows. To see options that are available for Azure, see the [Edge security](#edge-security) section in this article.

### Design virtual network and subnet security

The primary objective of a private cloud is to obscure resources from the public internet. There are several ways of achieving this goal:

- **Move to private IP address spaces**, which you can accomplish by using virtual networks. Minimize network line of sight even within your own private networks.

- **Minimize the number of public DNS entries that you use** to expose less of your workload.

- **Add ingress and egress network flow control**. Don't allow traffic that's not trusted.

##### Segmentation strategy

To minimize network visibility, **segment your network and start with least-privilege network controls**. If a segment isn't routable, it can't be accessed. Broaden the scope to include only segments that need to communicate with each other through network access.

You can segment virtual networks by creating subnets. The criteria for division should be intentional. When you collocate services inside a subnet, make sure that those services can see each other.

You can base your segmentation on many factors. For example, you can place different application tiers in dedicated segments. Another approach is to plan your subnets based on common roles and functions that use well-known protocols.

For more information, see [Segmentation strategies](segmentation.md).

##### Subnet firewalls

It's important to inspect each subnet's inbound and outbound traffic. Use the three main strategies discussed earlier in this article, in [Key design strategies](#key-design-strategies). Check whether the flow is expected, allowed, and safe. To verify this information, **define firewall rules that are based on the protocol, source, and destination** of the traffic.

On Azure, you set firewall rules in network security groups. For more information, see the [Network security groups](#network-security-groups) section in this article.

For an example of a subnet design, see [Azure Virtual Network subnets](/azure/virtual-network/quick-create-portal).

### Use controls at the component level

After you minimize the visibility of your network, map out your Azure resources from a network perspective and evaluate the flows. The following types of flows are possible:

- **Planned traffic**, or intentional communication between services according to your architecture design. For example, you have planned traffic when your architecture recommends that Azure Functions pulls messages from Azure Service Bus.

- **Management traffic**, or communication that happens as part of the service's functionality. This traffic isn't part of your design, and you have no control over it. An example of managed traffic is the communication between the Azure services in your architecture and the Azure management plane.

Distinguishing between planned and management traffic helps you build localized, or service-level, controls. Have a good understanding of the source and destination at each hop. Especially understand how your data plane is exposed.

As a starting point, determine whether each service is exposed to the internet. If it is, plan how to restrict access. If it isn't, place it in a virtual network.

##### Service firewalls

If you expect a service to be exposed to the internet, **take advantage of the service-level firewall that's available for most Azure resources**. When you use this firewall, you can set rules based on access patterns. For more information, see the [Azure service firewalls](#azure-service-firewalls) section in this article.

> [!NOTE]
> When your component isn't a service, use a host-based firewall in addition to network-level firewalls. A virtual machine (VM) is an example of a component that's not a service.

#### Connectivity to platform as a service (PaaS) services

Consider using **private endpoints to help secure access to PaaS services**. A private endpoint is assigned a private IP address from your virtual network. The endpoint allows other resources in the network to communicate with the PaaS service over the private IP address.

Communication with a PaaS service is achieved by using the service's public IP address and DNS record. That communication occurs over the internet. You can make that communication private.

A tunnel from the PaaS service into one of your subnets creates a private channel. All communication takes place from the component's private IP address to a private endpoint in that subnet, which then communicates with the PaaS service.

In this example, the image on the left shows the flow for publicly exposed endpoints. On the right, that flow is secured by using private endpoints.

:::image type="content" source="images/networking/private-endpoints.png" alt-text="Diagram that shows how a private endpoint helps protect a database from internet users." border="false" lightbox="images/networking/private-endpoints.png":::

For more information, see the [Private endpoints](#private-endpoints) section in this article.

> [!NOTE]
> We recommend that you use private endpoints in conjunction with service firewalls. A service firewall blocks incoming internet traffic and then exposes the service privately to internal users who use the private endpoint.

Another advantage of using private endpoints is that you don't need to open the ports on the firewall for outbound traffic. **Private endpoints lock down all outbound traffic on the port for the public internet.** Connectivity is limited to resources within the network.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Azure Private Link is a paid service that has meters for inbound and outbound data that's processed. You're also charged for private endpoints.

### Protect against distributed denial of service (DDoS) attacks

A DDoS attack attempts to exhaust an application's resources to make the application unavailable to legitimate users. DDoS attacks can target any endpoint that's publicly reachable through the internet.

A DDoS attack is usually a massive, widespread, geographically dispersed abuse of your system's resources that makes it hard to pinpoint and block the source.

For Azure support to help protect against these attacks, see the [Azure DDoS Protection](#azure-ddos-protection) section in this article.

## Azure facilitation

You can use the following Azure services to add defense-in-depth capabilities to your network.

### Azure Virtual Network

[Virtual Network](/azure/virtual-network/virtual-networks-overview) helps your Azure resources securely communicate with each other, the internet, and on-premises networks.

By default, all resources in a virtual network can engage in outbound communication with the internet. But inbound communication is restricted by default.

Virtual Network offers features for filtering traffic. You can restrict access at the virtual-network level by using a user-defined route (UDR) and a firewall component. At the subnet level, you can filter traffic by using network security groups.

### Edge security

By default, ingress and egress both flow over public IP addresses. Depending on the service or topology, either you set these addresses or Azure assigns them. Other ingress and egress possibilities include passing traffic through a load balancer or network address translation (NAT) gateway. But these services are intended for traffic distribution and not necessarily for security.

The following technology choices are recommended:

- [Azure Firewall](/azure/firewall/overview). You can use Azure Firewall at the network edge and in popular network topologies, such as hub-spoke networks and virtual WANs. You typically **deploy Azure Firewall as an egress firewall** that acts as the final security gate before traffic goes to the internet. Azure Firewall can route traffic that uses non-HTTP and non-HTTPS protocols, such as Remote Desktop Protocol (RDP), Secure Shell Protocol (SSH), and File Transfer Protocol (FTP). The feature set of Azure Firewall includes:
  - Destination network address translation (DNAT), or port forwarding.
  - Intrusion detection and prevention system (IDPS) signature detection.
  - Strong layer 3, layer 4, and fully qualified domain name (FQDN) network rules.

  > [!NOTE]
  > Most organizations have a forced tunneling policy that forces traffic to flow through an NVA.

  If you don't use a virtual WAN topology, **you must deploy a UDR** with a `NextHopType` of `Internet` to your NVA's private IP address. UDRs are applied at the subnet level. By default, subnet-to-subnet traffic doesn't flow through the NVA.

  You can also use Azure Firewall simultaneously for ingress. It can route HTTP and HTTPS traffic. In higher-tiered SKUs, Azure Firewall offers TLS termination so that you can implement payload-level inspections.

  The following practices are recommended:

  - **Enable diagnostics settings** in Azure Firewall to collect traffic flow logs, IDPS logs, and DNS request logs.

  - Be as specific as possible in rules.

  - Where it's practical, avoid FQDN service tags. But when you use them, use the regional variant, which allows communication with all endpoints of the service.

  - Use IP groups to define sources that must share the same rules over the life of the IP group. IP groups should reflect your segmentation strategy.

  - Override the infrastructure FQDN allow rule only if your workload requires absolute egress control. Overriding this rule comes with a reliability tradeoff, because Azure platform requirements change on services.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Azure Firewall can impact your performance. Rule order, quantity, TLS inspection, and other factors can cause significant latency.
  >
  > There can also be an impact on the reliability of your workload. It might experience source network address translation (SNAT) port exhaustion. To help overcome this problem, add public IP addresses as needed.

  > :::image type="icon" source="../_images/risk.svg"::: **Risk**: For egress traffic, Azure assigns a public IP address. This assignment can have a downstream impact on your external security gate.

- [Azure Web Application Firewall](/azure/web-application-firewall/overview). This service supports inbound filtering and only targets HTTP and HTTPS traffic.

  It offers basic security for common attacks, such as threats that the Open Worldwide Application Security Project (OWASP) identifies in the OWASP Top 10 document. Azure Web Application Firewall also provides other security features that are focused on layer 7, such as rate limiting, SQL-injection rules, and cross-site scripting.

  With Azure Web Application Firewall, TLS termination is required, because most checks are based on payloads.

  You can integrate Azure Web Application Firewall with routers, such as Azure Application Gateway or Azure Front Door. Azure Web Application Firewall implementations for those kinds of routers can vary.

Azure Firewall and Azure Web Application Firewall aren't mutually exclusive choices. For your edge security solution, various options are available. For examples, see [Firewall and Application Gateway for virtual networks](/azure/architecture/example-scenario/gateway/firewall-application-gateway).

### Network security groups

A [network security group](/azure/virtual-network/manage-network-security-group) is a layer 3 and layer 4 firewall that you apply at the subnet or network interface card (NIC) level. Network security groups aren't created or applied by default.

**Network security group rules act as a firewall** to stop traffic that flows in and out at the perimeter of a subnet. A network security group has a default rule set that's overly permissive. For example, the default rules don't set a firewall from the egress perspective. For ingress, no inbound internet traffic is allowed.

To create rules, start with the default rule set:

- For **inbound** traffic, or ingress:
  - Virtual network traffic from direct, peered, and VPN gateway sources is allowed.
  - Azure Load Balancer health probes are allowed.
  - All other traffic is blocked.
- For **outbound** traffic, or egress:
  - Virtual network traffic to direct, peered, and VPN gateway destinations is allowed.
  - Traffic to the internet is allowed.
  - All other traffic is blocked.

Then consider the following five factors:

- Protocol
- Source IP address
- Source port
- Destination IP address
- Destination port

The lack of support for FQDN limits network security group functionality. You need to provide specific IP address ranges for your workload, and they're hard to maintain.

But for Azure services, you can use [service tags](/azure/virtual-network/service-tags-overview#available-service-tags) to summarize source and destination IP address ranges. A security benefit of service tags is that they're **opaque to the user, and the responsibility is offloaded to Azure**. You can also assign an application security group as a destination type to route traffic to. This type of named group contains resources that have similar inbound or outbound access needs.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Service tag ranges are very broad so that they accommodate the widest possible range of customers. Updates to service tags lag behind changes in the service.

:::image type="content" source="images/networking/virtual-network-default-isolation-peering.png" alt-text="Diagram that shows virtual network default isolation with peering." border="false" lightbox="images/networking/virtual-network-default-isolation-peering.png":::

In the preceding image, network security groups are applied at the NIC. Internet traffic and subnet-to-subnet traffic are denied. The network security groups are applied with the `VirtualNetwork` tag. So in this case, the subnets of peered networks have a direct line of sight. The broad definition of the `VirtualNetwork` tag can have a significant security impact.

When you use service tags, use regional versions when possible, such as `Storage.WestUS` instead of `Storage`. By taking this approach, you limit the scope to all endpoints in a particular region.

Some tags are exclusively for **inbound** or **outbound** traffic. Others are for *both* types. **Inbound** tags usually allow traffic from all hosting workloads, such as `AzureFrontDoor.Backend`, or from Azure to support service runtimes, such as `LogicAppsManagement`. Similarly, **outbound** tags allow traffic to all hosting workloads or from Azure to support service runtimes.

Scope the rules as much as possible. In the following example, the rule is set to specific values. Any other type of traffic is denied.

|Information | Example |
|---------|---------|
|Protocol | Transmission Control Protocol (TCP), UDP |
|Source IP address | Allow ingress to the subnet from \<source-IP-address-range\>:4575/UDP |
|Source port | Allow ingress to the subnet from \<service-tag\>:443/TCP |
|Destination IP address | Allow egress from the subnet to \<destination-IP-address-range\>:443/TCP |
|Destination port | Allow egress from the subnet to \<service-tag\>:443/TCP |

To summarize:

- **Be precise when you create rules**. Only allow traffic that's necessary for your application to function. Deny everything else. This approach limits the network line of sight to network flows that are needed to support the operation of the workload. **Supporting more network flows than necessary leads to unnecessary attack vectors and extends the surface area.**

  Restricting traffic doesn't imply that allowed flows are beyond the scope of an attack. Because network security groups work at layers 3 and 4 on the Open Systems Interconnection (OSI) stack, they only contain shape and direction information. For example, if your workload needs to allow DNS traffic to the internet, you would use a network security group of `Internet:53:UDP`. In this case, an attacker might be able to exfiltrate data through UDP on port 53 to some other service.

- Understand that network security groups can differ slightly from one another. It's easy to overlook the intent of the differences. **To have granular filtering, it's safer to create extra network security groups.** Set up at least one network security group.

  - Adding a network security group unlocks many diagnostics tools, such as flow logs and network traffic analytics.

  - Use Azure Policy to help control traffic in subnets that don't have network security groups.

- If a subnet supports network security groups, add a group, even if it's minimally impactful.

### Azure service firewalls

Most Azure services offer a service-level firewall. This feature inspects ingress traffic to the service from specified classless inter-domain routing (CIDR) ranges. These firewalls offer benefits:

- They provide a **basic level of security**.
- There's a **tolerable performance impact**.
- Most services offer these firewalls at **no extra cost**.
- The firewalls emit logs through Azure diagnostics, which can be useful for analyzing access patterns.

But there are also security concerns associated with these firewalls, and there are limitations associated with providing parameters. For example, if you use Microsoft-hosted build agents, you have to open the IP address range for all Microsoft-hosted build agents. The range is then open to your build agent, other tenants, and adversaries who might abuse your service.

If you have access patterns for the service, which can be configured as service firewall rule sets, you should enable the service. You can use Azure Policy to enable it. Make sure you don't enable the trusted Azure services option if it isn't enabled by default. Doing so brings in all dependent services that are in the scope of the rules.

For more information, see the product documentation of individual Azure services.

### Private endpoints

[Private Link](/azure/private-link/private-link-overview) provides a way for you to give a PaaS instance a private IP address. The service is then unreachable over the internet. [Private endpoints](/azure/private-link/private-endpoint-overview) aren't supported for all SKUs.

Keep the following recommendations in mind when you use private endpoints:

- Configure services that are bound to virtual networks to **contact PaaS services through private endpoints**, even if those PaaS services also need to offer public access.

- Promote the use of **network security groups for private endpoints to restrict access** to private endpoint IP addresses.

- Always **use service firewalls when you use private endpoints**.

- When possible, if you have a service that's only accessible via private endpoints, remove the DNS configuration for its public endpoint.

- Consider runtime **line-of-sight concerns** when you implement private endpoints. But also consider **DevOps and monitoring concerns**.

- Use Azure Policy to **enforce resource configuration**.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Service SKUs with private endpoints are expensive. Private endpoints can complicate operations because of network obscurity. You need to add self-hosted agents, jump boxes, a VPN, and other components to your architecture.

DNS management can be complex in common network topologies. You might have to introduce DNS forwarders and other components.

### Virtual network injection

You can use the **virtual network injection process** to deploy some Azure services into your network. Examples of such services include Azure App Service, Functions, Azure API Management, and Azure Spring Apps. This process **isolates the application** from the internet, systems in private networks, and other Azure services. Inbound and outbound traffic from the application is allowed or denied based on network rules.

### Azure Bastion

You can use [Azure Bastion](/azure/bastion/bastion-overview) to connect to a VM by using your browser and the Azure portal. Azure Bastion **enhances the security of RDP and SSH connections**. A typical use case includes connecting to a jump box in the same virtual network or a peered virtual network. Using Azure Bastion removes the need for the VM to have a public IP address.

### Azure DDoS Protection

Every property in Azure is protected by Azure DDoS infrastructure protection at no extra cost and with no added configuration. The level of protection is basic, but the protection has high thresholds. It also doesn't provide telemetry or alerting, and it's workload-agnostic.

Higher-tiered SKUs of DDoS Protection are available but aren't free. The scale and capacity of the globally deployed Azure network offers protection against common network-layer attacks. Technologies like always-on traffic monitoring and real-time mitigation provide this capability.

For more information, see [Azure DDoS Protection overview](/azure/ddos-protection/ddos-protection-overview).

## Example

This example architecture combines the network controls that are described in this article. The example doesn't show the complete architecture. Instead, it focuses on ingress controls on the private cloud.

:::image type="content" source="images/networking/controlled-ingress.png" alt-text="Diagram that shows controlled ingress, including Application Gateway, a network security group, Azure Bastion, and Azure DDoS Protection." border="false" lightbox="images/networking/controlled-ingress.png":::

### Highlights

**Application Gateway is a web traffic load balancer** that you can use to manage traffic to your web applications. You deploy Application Gateway in a dedicated subnet that has network security group controls and web application firewall controls in place.

Communication with all PaaS services is conducted through **private endpoints**. All endpoints are placed in a dedicated subnet. DDoS Protection helps protect all public IP addresses that are configured for a basic or higher level of firewall protection.

**Management traffic is restricted through Azure Bastion**, which helps provide secure and seamless RDP and SSH connectivity to your VMs directly from the Azure portal over TLS. Build agents are placed in the virtual network so that they have a network view to workload resources such as compute resources, container registries, and databases. This approach helps provide a secure and isolated environment for your build agents, which boosts protection for your code and artifacts.

:::image type="content" source="images/networking/controlled-egress.png" alt-text="Diagram that shows controlled egress for a network security group and Azure Firewall." border="false" lightbox="images/networking/controlled-egress.png":::

### Highlights

Network security groups at the subnet level of the compute resources restrict egress traffic. Forced tunneling is used to route all traffic through Azure Firewall. This approach helps provide a secure and isolated environment for your compute resources, which boosts protection for your data and applications.

## Related links

- [Recommendations for designing segmentation strategies](segmentation.md)
- [Azure Virtual Network subnets](/azure/virtual-network/quick-create-portal)
- [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
- [Azure Firewall](/azure/firewall/overview)
- [Azure Web Application Firewall](/azure/web-application-firewall/overview)
- [Firewall and Application Gateway for virtual networks](/azure/architecture/example-scenario/gateway/firewall-application-gateway)
- [Network security groups](/azure/virtual-network/manage-network-security-group)
- [Service tags](/azure/virtual-network/service-tags-overview#available-service-tags)
- [Azure Private Link](/azure/private-link/private-link-overview)
- [Private endpoints](/azure/private-link/private-endpoint-overview)
- [Azure Bastion](/azure/bastion/bastion-overview)
- [Azure DDoS Protection overview](/azure/ddos-protection/ddos-protection-overview)

## Security checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

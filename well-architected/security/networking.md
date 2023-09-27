---
title: Recommendations for networking and connectivity
description: Learn about network design with a focus on security controls that can filter, block, and detect adversaries crossing network boundaries at various depths of your architecture.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for networking and connectivity

**Applies to: SE 11**

This guide provides recommendations on network design. The focus is on security controls that can filter, block, and detect adversaries crossing network boundaries at various depths of your architecture. Identity should always be your first line of defense. Along with identity-based access control, network-based access control is still a high priority for protecting assets. Proper network security controls can provide a defense-in-depth element that can help detect, contain, and prevent attackers from gaining entry into your workload.

**Definitions**

| Term |Definition |
|---------|---------|
|Hostile network | A network that isn't deployed as part of your workload. A hostile network is considered a threat vector. |
|Network segmentation | A strategy that divides a network into small, isolated segments, with security controls applied at the boundaries. This technique helps protect resources from hostile networks, such as the internet. |
|Network filtering | A mechanism that allows or blocks network traffic based on specified rules. |
|Network transformation | A mechanism that changes or mutates network packets for the purpose of obscuring them. |
|Ingress flow | Inbound workload traffic. |
|Egress flow | Outbound workload traffic. |
|North-south traffic | Network traffic that moves from a trusted boundary to external networks that are potentially hostile, and vice versa. |
|East-west traffic | Network traffic that moves within a trusted boundary. |

## Key design strategies

Network security uses obscurity to protect workload assets from hostile networks. Resources that are behind a network boundary are hidden until the boundary controls mark the traffic as safe to move forward. Network security design is built on three main strategies:

- **Segment**. This technique isolates traffic on separate networks by adding boundaries. For example, traffic to and from an application tier passes a boundary to communicate with other tiers, which have different security requirements. Layers of segmentation actualize the defense-in-depth approach.

  The foremost security boundary is the networking edge between your application and public networks. It's important to clearly define this perimeter so that you establish a boundary for isolating hostile networks. The controls on this edge must be highly effective, because this boundary is your first line of defense. Virtual networks provide a logical boundary. By design, a virtual network cannot communicate with another virtual network unless the boundary has been intentionally broken through peering. Your architecture should take advantage of this strong, platform-provided security measure.

  You can also use other logical boundaries, such as carved-out subnets within a virtual network. A benefit of subnets is that you can group resources that have similar security assurances together within an isolation boundary. You can then configure controls on the boundary to filter traffic.

- **Filter**. This strategy ensures that traffic that enters a boundary is expected, allowed, and safe. From a Zero-Trust perspective, filtering explicitly verifies all available data points at the network level. You can place rules on the boundary to check for specific conditions.

  For example, at the header level, the rules can verify that the traffic originates from an expected location or has an expected volume. But these checks aren't sufficient. Even if the traffic exhibits expected characteristics, the payload might not be safe. Validation checks might reveal an SQL injection attack.

- **Transform**. Mutate packets at the boundary as a security measure. For example, you can remove HTTP headers to eliminate the risk of exposure. Or you can turn off Transport Layer Security (TLS) at one point and reestablish it at another hop with a certificate that's managed more rigorously.

### Classify the traffic flows

The first step in classifying flows is to study an schematic of your workload architecture. From the schematic, determine the intent and characteristics of the flow with respect to the functional utility and operational aspects of your workload. Use the following questions to help classify the flow:

- If the workload needs to communicate with external networks, what should the required level of proximity to those networks be?

- What are the network characteristics of the flow, such as the expected protocol and the source and shape of the packets? Are there any compliance requirements at the networking level?

There are many ways to classify traffic flows. The following sections discuss commonly used criteria.

#### Visibility from external networks

- **Public**. A workload is public facing if its application and other components are reachable from the public internet. The application is exposed through one or more public IP addresses and public Domain Name System (DNS) servers.

- **Private**. A workload is private if it can only be accessed through a private network such as a virtual private network (VPN). It's exposed only through one or more private IP addresses and potentially through a private DNS server.

  In a private network, there's no line of sight from the public internet to the workload. For the gateway, you can use a load balancer or firewall. These options can provide security assurances.

Even with public workloads, strive to keep as much of the workload private as possible. Then packets have to cross through a private boundary when they arrive from a public network. A gateway in that path can function as a transition point by acting as a reverse proxy.

#### Traffic direction

- **Ingress**. Inbound traffic flows toward the workload or its components. For secure ingress, apply the preceding set of key strategies. Determine what the source is and whether it's expected, allowed, and safe. Attackers who scan public cloud provider IP address ranges can successfully penetrate your defenses if you don't check ingress or implement basic network security measures.

- **Egress**. Outbound traffic flows away from the workload or its components. To check egress, determine where the traffic is headed and whether the destination is expected, allowed, and safe. The destination might be malicious or associated with data exfiltration risks.

:::image type="content" source="images/networking/internet-network-flow-public-cloud.png" alt-text="Diagram of internet network flow for Azure deployments." border="false" lightbox="images/networking/internet-network-flow-public-cloud.png":::

You can also determine your level of exposure by considering your workload's proximity to the public internet. For example, the application platform typically serves public IP addresses. The workload component is the face of the solution.

#### Scope of influence

- **North-south**. Traffic that flows between a workload network and external networks is north-south traffic. This traffic crosses the edge of your network. External networks can be the public internet, a corporate network, or any other network that's outside your scope of control.

  Ingress and egress flows can both be north-south traffic.

  As an example, consider the egress flow of a hub-spoke network topology. You can define the networking edge of your workload so that the hub is an external network. In that case, outbound traffic from the virtual network of the spoke is north-south traffic. But if you consider the hub network within your sphere of control, north-south traffic is extended to the firewall in the hub, because the next hop is the internet, which is potentially hostile.

- **East-west**. Traffic that flows within a workload network is east-west traffic. This type of traffic results when components in your workload communicate with each other. An example is traffic between the tiers of an n-tier application. In microservices, service-to-service communication is east-west traffic.

  To provide defense in depth, maintain end-to-end control of security affordances that are included in each hop or that you use when packets cross internal segments. Different risk levels require different risk remediation methods.

  :::image type="content" source="images/networking/network-defense-depth-private-cloud.png" alt-text="Diagram that shows network defense in depth for a private cloud." border="false" lightbox="images/networking/network-defense-depth-private-cloud.png":::

  The preceding diagram illustrates network defense in depth in the private cloud. In this diagram, the border between the public and private IP address spaces is significantly farther from the workload than in the public cloud diagram. The border with the public IP space is shifted to the right in this diagram. Multiple layers separate the Azure deployments from the public IP space.

> [!NOTE]
> Identity is always the primary perimeter. Access management must be applied to networking flows. Use managed identities when you use Azure role-based access control (RBAC)between components of your network.

After you classify flows, perform a segmentation exercise to identify firewall injection points on the communication paths of your network segments. When you design your network defense in depth across all segments and all traffic types, assume a breach at all points. Use a combination of various localized network controls at all available boundaries. For more information, see [Segmentation strategies](segmentation.md).

### Apply firewalls at the edge

Internet edge traffic is north-south traffic and includes ingress and egress. To detect or block threats, an edge strategy must mitigate as many attacks as possible to and from the internet.

For egress, send all internet bound traffic through a single firewall that provides enhanced oversight, governance, and control of traffic. For ingress, force all traffic from the internet to go through a network virtual appliance (NVA) or a web application firewall.

- Firewalls are usually singletons that are deployed per region in an organization. As a result, they're shared among workloads and owned by a central team. Make sure that any NVAs that you use are configured to support the needs of your workload.

- We recommend that you use Azure native controls as much as possible.

  In addition to native controls, you can also consider partner NVAs that provide advanced or specialized features. Partner firewall and web application firewall vendor products are available in Azure Marketplace.

  The decision to use native features as opposed to partner solutions should be based on your organization's experience and requirements.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Partner capabilities often provide advanced features that can protect against sophisticated, but typically uncommon, attacks. The configuration of partner solutions can be complex and fragile, because these solutions don't integrate with the cloud's fabric controllers. From a cost perspective, native control is preferred because it's cheaper than partner solutions.

Any technological options that you consider should provide security controls and monitoring for both ingress and egress flows. To see options that are available for Azure, see the [Edge security section](#edge-security) in this article.

### Design virtual network and subnet security

The primary objective of a private cloud is to obscure resources from the public internet. There are several ways of achieving that goal:

- Move to private IP spaces, which you can accomplish by using virtual networks. Minimize network line of sight even within your own private networks.

- Minimize the number of public DNS entries so that you expose less of your workload.

- Add ingress and egress network flow control. Don't allow traffic that's not trusted.

#### Segmentation strategy

To minimize network visibility, segment your network and start with least-privilege network controls. If a segment isn't routable, it can't be accessed. Broaden the scope to only include segments that need to communicate with each other through network access.

You can segment virtual networks by creating subnets. The criteria for division should be intentional. Make sure that services that are collocated inside a subnet can see each other.

You can base your segmentation on many factors. For example, you can place different application tiers in their dedicated segments. Another approach is to plan your subnets based on common roles and functions that use well-known protocols.

For more information, see [Segmentation strategies](segmentation.md).

#### Subnet firewalls

It's important to inspect each subnet's inbound and outbound traffic. Use the three main strategies discussed earlier in this article, in [Key design strategies](#key-design-strategies). Check whether the flow is expected, allowed, and safe. To verify this information and subsequently set firewall rules, you need information about the protocol, source, and destination of the traffic. On Azure, you set firewall rules in network security groups. For more information, see [Network security groups](#network-security-groups), later in this article.

For an example of a subnet design, see [Azure Virtual Network subnets](<!-- add link -->).

### Use controls at the component level

After you minimize the visibility of your network, map out your Azure resources from a network perspective and evaluate the flows. The following types of flows are possible:

- **Planned traffic**, or intentional communication between services according to your architecture design. For example, you have planned traffic when your architecture recommends that Azure Functions pulls messages from Azure Service Bus.

- **Management traffic**, or communication that happens as part of the service's functionality. This traffic isn't part of your design, and you have no control over it. An example of managed traffic is the communication between the Azure services in your architecture and the Azure management plane.

Distinguishing between planned and management traffic helps you build localized, or service-level, controls. Have a good understanding of the source and destination at each hop. Understand, especially, how the data plane is exposed.

As a starting point, determine whether each service is exposed to the internet. If it is, plan how to restrict access. If it isn't, place it in a virtual network.

#### Service firewalls

If you expect a service to be exposed to the internet, take advantage of the service-level firewall that's available for most Azure resources. You can set rules based on access patterns. For more information, see [Azure service firewalls](#azure-service-firewalls), later in this article.

> [!NOTE]
> When your component isn't a service, use a host-based firewall in addition to network-level firewalls. A virtual machine (VM) is an example of a component that's not a service.

#### Connectivity to platform as a service (PaaS) services

Consider securing access to PaaS services by using private endpoints. A private endpoint is assigned a private IP address from the virtual network. The endpoint allows other resources in the network to communicate with the PaaS service over the private IP address.

Communication with a PaaS service is achieved by using the service's public IP address and DNS record. That communication is over the internet. You can make that communication private.

A tunnel from the PaaS service into one of your subnets creates a private channel. All communication takes place from the component's private IP address to a private endpoint in that subnet, which then communicates with the PaaS service.

:::image type="content" source="images/networking/private-endpoints.png" alt-text="Diagram that shows private endpoints." border="false" lightbox="images/networking/private-endpoints.png":::

For more information, see [Private endpoints](#private-endpoints), later in this article.

> [!NOTE]
> We recommend that you use private endpoints in conjunction with service firewalls. A service firewall blocks incoming internet traffic and then exposes the service privately to internal users who use the private endpoint.

Another advantage of using private endpoints is that you don't need to open the ports on the firewall for outbound traffic. Private endpoints lock down all outbound traffic on the port for the public internet. Connectivity is limited to resources within the network.

### Protect against distributed denial of service (DDoS) attacks

A DDoS attack attempts to exhaust an application's resources to make the application unavailable to legitimate users. DDoS attacks can target any endpoint that's publicly reachable through the internet.

A DDoS attack is usually a massive, widespread, geographically dispersed abuse of your system's resources that makes it hard to pinpoint and block the source.

For Azure support to protect against these attacks, see [Azure DDoS protection](#azure-ddos-protection), later in this article.

## Azure facilitation

#### Azure Virtual Network

[Virtual Network](/azure/virtual-network/virtual-networks-overview) makes it possible for Azure resources to securely communicate with each other, the internet, and on-premises networks.

All resources in a virtual network can communicate outbound to the internet by default. But inbound communication is restricted by default.

Virtual Network offers features for filtering traffic. You can restrict access at the virtual network level through a user-defined routing and firewall component. At the subnet level, you can filter traffic by using network security groups.

#### Edge security

By default, ingress and egress both flow over public IP addresses. Those addresses are either set by you or assigned by Azure, depending on the service or topology. Other ingress and egress possibilities include passing traffic through a load balancer or network address translation (NAT) gateway. But these services are intended for traffic distribution and not necessarily for security.

The following technology choices are recommended:

- [Azure Firewall](/azure/firewall/overview). You can use Azure Firewall at the network edge and in popular network topologies, such as hub-spoke networks and Azure Virtual WAN. You typically deploy Azure Firewall as an egress firewall that acts as the final security gate before traffic goes to the internet. Azure Firewall can route traffic that uses non-HTTP and non-HTTPS protocols, such as Remote Desktop Protocol (RDP), Secure Shell Protocol (SSH), and File Transfer Protocol (FTP). The feature set of Azure Firewall includes:
  - Destination network address translation (DNAT), or port forwarding.
  - Intrusion detection and prevention system (IDPS) signature detection.
  - Strong layer 3, layer 4, and fully qualified domain name (FQDN) network rules.

  > [!NOTE]
  > Most organizations have a forced tunneling policy that forces traffic to flow through an NVA.

  If you don't use a virtual WAN topology, you must deploy a user-defined route (UDR) with a `NextHopType` of `Internet` to your NVA's private IP address. UDRs are applied at the subnet level. By default, subnet-to-subnet traffic doesn't flow through the NVA.

  You can also use Azure Firewall simultaneously for ingress. It can route HTTP and HTTPS traffic. In higher SKUs, Azure Firewall offers Transport Layer Security (TLS) termination so that you can implement payload-level inspections.

  Here are some recommended practices:

  - Enable diagnostics settings in Azure Firewall to get traffic flow logs, IDPS logs, and DNS request logs.

  - Be as specific as possible in rules.

  - Where it's practical, avoid FQDN service tags. But when you use them, use the regional variant, which allows communication with all endpoints of the service.

  - Use IP groups to define sources that must share the same rules over the life of the IP group. In this way, IP groups should reflect your segmentation strategy.

  - Override the infrastructure FQDN **allow** rule only if your workload requires absolute egress control. Overriding this rule comes with a reliability tradeoff, because Azure platform requirements change on services.

  > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Azure Firewall can impact your performance. Rule order, quantity, TLS inspection, and other factors can cause significant latency.
  >
  > There can also be an impact on the reliability of your workload. It might experience source network address translation (SNAT) port exhaustion. Add public IP addresses as needed to help overcome this problem.

  > ![Risk icon](../_images/risk.svg) **Risk**: For egress traffic, Azure assigns a public IP address. This assignment can have a downstream impact on your external security gate.

- [Azure Web Application Firewall](/azure/web-application-firewall/overview) supports inbound filtering and only targets HTTP and HTTPS traffic.

  It offers basic security for common attacks, such as threats that the Open Worldwide Application Security Project (OWASP) identifies in the OWASP Top 10 document. Azure Web Application Firewall also provides other security features that are focused on layer 7, such as rate limiting, SQL-injection rules, and cross-site scripting.

  With Azure Web Application Firewall, TLS termination is required, because most checks are payload based.

  You can integrate Azure Web Application Firewall with routers, such as Azure Application Gateway or Azure Front Door. Be aware that Azure Web Application Firewall implementations for those kinds of routers can vary.

You don't have to select either Azure Firewall or Azure Web Application Firewall as your edge security solution. For various possibilities, see [Firewall and Application Gateway for virtual networks](/azure/architecture/example-scenario/gateway/firewall-application-gateway).

#### Network security groups

A [network security group](/azure/virtual-network/manage-network-security-group) is a layer 3 and layer 4 firewall that you apply at the subnet or network interface card (NIC) level.

Network security group rules act as a firewall to stop traffic that flows in and out at the perimeter of a subnet. Network security groups aren't created or applied by default. Each network security group has the following default rule set, which is overly permissive:

- **Inbound** traffic, or ingress:
  - Virtual network traffic from direct, peered, and VPN gateway sources is allowed.
  - Azure Load Balancer health probes are allowed.
  - All other traffic is blocked.
- **Outbound** traffic, or egress:
  - Virtual network traffic to direct, peered, and VPN gateway destinations is allowed.
  - Traffic to the internet is allowed.
  - All other traffic is blocked.

For example, the default rule doesn't set a firewall from the egress perspective. For ingress, no inbound internet traffic is allowed.

To create rules, start with the default network security group, and consider the following five factors:

- Protocol
- Source IP address
- Source port
- Destination IP address
- Destination port

The lack of support for FQDN limits network security group functionality. You need to provide specific IP address ranges for your workload, and they're hard to maintain.

But for Azure services, you can use [service tags](/azure/virtual-network/service-tags-overview#available-service-tags) to summarize source and destination IP address ranges. A security benefit of service tags is that they're opaque to the user, and the responsibility is offloaded to Azure. You can also assign an application security group as a destination type to route traffic to. This type of named group contains resources that have similar inbound or outbound access needs.

> ![Risk icon](../_images/risk.svg) **Risk**: Service tag ranges are very broad so that they accommodate the widest possible range of customers. Updates to service tags lag behind changes in the service.

:::image type="content" source="images/networking/virtual-network-default-isolation-peering.png" alt-text="Diagram that shows virtual network default isolation with peering." border="false" lightbox="images/networking/virtual-network-default-isolation-peering.png":::

In the preceding image, network security groups are applied at the NIC. Internet traffic and subnet-to-subnet traffic are denied. The network security groups are applied with the `VirtualNetwork` tag. So in this case, the subnets of peered networks have a direct line of sight. The broad definition of the `VirtualNetwork` tag can have a significant security impact.

- When you use service tags, use regional versions when possible, such as `Storage.WestUS` instead of `Storage`. By taking this approach, you limit the scope to all endpoints in a particular region.

  Some tags are exclusively for **inbound** or **outbound** traffic. Others are for *both* types. **Inbound** tags usually allow traffic from all hosting workloads, such as `AzureFrontDoor.Backend`, or from Azure to support service runtimes, such as `LogicAppsManagement`. Similarly, **outbound** tags allow traffic to all hosting workloads or from Azure to support service runtimes.

Scope the rules as much as possible. In the following example, the rule is set to specific values. Any other type of traffic is denied.

|Information | Example  |
|---------|---------|
|Protocol | TCP, UDP |
|Source IP address | Allow ingress to the subnet from \<source IP range\>:4575/UDP |
|Source port | Allow ingress to the subnet from \<service tag\>:443/TCP |
|Destination IP address | Allow egress from the subnet to \<destination IP range\>:443/TCP |
|Destination port | Allow egress from the subnet to \<service tag\>:443/TCP |

To summarize:

- Be precise. Only allow what your application needs to function. Deny everything else. This will minimize network line of sight to just those network flows that are necessary to support the operation of the workload. Supporting more network flows than necessary leads to unnecessary attack vectors and extends the surface area.

    This doesn't imply that the allowed flows are beyond the scope of an attack*.* Because network security groups work at layer 3 and 4 on the OSI stack, they only contain shape and direction information. For example, if your workload needed DNS traffic to the internet, that would be an network security group of Internet:53:UDP, an attacker might be able to exfiltrate data through UDP on port 53 to some other service.

- Network security groups might differ slightly from one another. It's easy to skip the intent of the differences. It's always safer to create more network security groups to have granular filtering. At least have one network security group.

    - Adding an network security group unlocks many diagnostics tools: Flow Logs and Network Traffic Analytics.

    - Use Azure Policy to enforce subnets that don't have network security groups.

-   If a subnet supports network security groups, add one. Even if it's minimally meaningful.

#### Azure service firewalls

Most Azure services offer service-level firewall. This feature inspects ingress traffic to the service by the specified CIDR ranges. There are benefits. They provide a basic level of security. There's a tolerable performance impact and are offered without additional costs for most services. Service firewalls emit logs well through Azure diagnostics, which can be useful to analyze access patterns.

However, there are security concerns and there are limitations in providing parameters. For example, if you're applying to Microsoft-hosted build agents, you have to open the IP range for all Microsoft hosted build agents, regardless of whether it\'s your build agent, another tenant, or an adversary trying to abuse your service.

If you do have access patterns to the service, which can be configured as service firewall rulesets, you should enable it. You can enforce enablement through Azure policy. Make sure you don't enable trusted Azure services option if it isn't enabled by default. Doing so will bring in all dependent services in scope of the rules.

For more information, check product documentation for Azure services.

#### Private endpoints

[Azure Private Link](/azure/private-link/private-link-overview) provides the capability of giving a PaaS instance a private IP address. This causes the service to be unreachable over the internet. [Private endpoints](/azure/private-link/private-endpoint-overview) aren't supported for all SKUs.

Here are some recommendations:

-   Virtual Network bound services should contact PaaS services through private endpoints, even if those PaaS services also need to offer public access.

-   Promote the use of the Network Security Group feature for private endpoints to restrict access to the private endpoint IPs.

-   Always use service firewalls with Private Endpoint enablement.

-   When possible, remove public DNS from Private Endpoint-only services.

-   Consider not just runtime line-of-sight concerns when implementing private endpoints, but also DevOps and monitoring concerns.

-   Use Azure Policy to enforce resource configuration.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Service SKUs with private endpoints are more expensive. It can complicate operations because of network obscurity. You will have to add self-hosted agents, jump boxes, VPN, and other components to your architecture.

DNS management can be complex in common network topologies. You may have to introduce DNS forwarders and other components.

#### VNET Injection

Some Azure services such as App Services, Functions, API Management, Spring Apps are deployed into the network through the VNET injection process. This process isolates the application from the internet, systems in private networks, and other Azure services. Inbound and outbound traffic from the application is allowed or denied based on network rules.

#### Azure Bastion

You can use [Azure Bastion](/azure/bastion/bastion-overview) to connect to a VM by using your browser and the Azure portal. Bastion provides secure connectivity over remote desktop protocol (RDP) or secure shell (SSH). Typical use case includes connecting to a jump box in the same virtual network or a peered virtual network. Using Bastion removes the need for the VM to have a public IP address.

#### Azure DDoS protection 

Every property in Azure is protected by Azure DDoS infrastructure protection at no additional cost and no added configuration. This SKU provides basic protection. However, that protection has extremely high thresholds, doesn't provide telemetry or alerting, and is workload-agnostic.

The higher SKUs aren't free. The scale and capacity of the globally deployed Azure network provides defense against common network-layer attacks through always-on traffic monitoring and real-time mitigation.

For more information, see [Azure DDoS Protection Overview](/azure/ddos-protection/ddos-protection-overview).

## Example

This example architecture combines the network controls described in this article. The example doesn't show the complete architecture and only focuses on ingress controls on private cloud.

:::image type="content" source="images/networking/controlled-ingress.png" alt-text="Diagram that shows controlled ingress, including App Gateway, network security group, Bastion, and Azure DDoS." border="false" lightbox="images/networking/controlled-ingress.png":::

#### Highlights

Azure Application Gateway is a web traffic load balancer that enables you to manage traffic to your web applications. It's placed in a dedicated subnet with network security group and WAF controls in place.

Communication with all PaaS services is done through private endpoints. All endpoints are placed in a dedicated subnet. Azure DDoS is protecting all public IPs with some level of firewall applied to them.

Management traffic is restricted through Azure Bastion, which provides secure and seamless RDP/SSH connectivity to your VMs directly from the Azure portal over TLS. Build agents are placed in the virtual network so they have a network view to workload resources such as the compute, container registry, and database. This approach provides a secure and isolated environment for your build agents, which can help protect your code and artifacts.

:::image type="content" source="images/networking/controlled-egress.png" alt-text="Diagram that shows controlled egress for  network security group and Azure Firewall." border="false" lightbox="images/networking/controlled-egress.png":::

#### Highlights

At the subnet level, egress traffic is restricted by network security group at the subnet level of the compute. All traffic is force tunneled through Azure Firewall. This approach provides a secure and isolated environment for your compute resources, which can help protect your data and applications.

## Related links

- [Recommendations for designing segmentation strategies](segmentation.md)
- [Azure virtual network](/azure/virtual-network/virtual-networks-overview)
- [Azure Firewall](/azure/firewall/overview)
- [Firewall, App Gateway for virtual networks - Azure Example Scenarios](/azure/architecture/example-scenario/gateway/firewall-application-gateway)
- [Network security group](/azure/virtual-network/manage-network-security-group)
- [Azure Private Link](/azure/private-link/private-link-overview)
- [Azure Bastion](/azure/bastion/bastion-overview)
- [Azure DDoS Protection Overview](/azure/ddos-protection/ddos-protection-overview)

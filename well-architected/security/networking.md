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

This guide provides recommendations on network design with a focus on security controls that can filter, block, and detect adversaries crossing network boundaries at various depths of your architecture. Identity should always be your first line of defense. Along with identity-based access control, network-based access control is still a high priority for protecting assets. Proper network security controls can provide a defense in depth element that can help detect, contain, and prevent attackers from gaining entry into your workload.

**Definitions** 


|Terms   |Definition   |
|---------|---------|
|Hostile network     |  All networks that aren't deployed as part of the workload. They must be considered a threat vector.       |
|Network segmentation     |  Strategy where network security used to be at the isolation boundaries to protect the resources from hostile networks, such as the internet.       |
|Network filtering      | Mechanism to allow or block network traffic based on certain rules.        |
|Network transformation     |  Mechanism to change or mutate network packets for the purposes of obscurity.       |
|Ingress flow     |  Inbound traffic to the workload.       |
|Egress flow        |  Outbound traffic from the workload.       |
|North-south traffic     | Network traffic that moves from trusted boundary to external and potentially hostile networks, and vice versa.        |
|East-west traffic     | Network traffic moving within a trusted boundary.        |

## Key design strategies

Network security protects workload assets from hostile networks through obscurity. Resources behind a network boundary are hidden until the boundary controls mark the traffic as safe to move forward. Network security design is built on three main strategies.

-   **Segment**. This isolates different traffic on separate networks by adding boundaries. For example, traffic to and from an application tier passes a boundary to communicate with other tiers, which have different security requirements. Layers of segmentation actualize the defense in depth approach.

    The foremost security boundary is the networking edge between your application and public networks. It's important to have a crisp definition of this perimeter so that you can place a definitive boundary to isolate hostile networks. The controls on the edge must be highly effective because this is the first line of defense. Virtual networks are a technical boundary. A virtual network inherently cannot communicate with another virtual network unless the boundary has been intentionally broken through peering. Your architecture must take advantage of this strong platform-provided security measure.
    
    There are also logical boundaries, such as carved out subnets within a virtual network. The benefit is that you can group resources with similar security assurances in an isolation boundary with the ability to filter out traffic through configured controls.
    
-   **Filter**. Makes sure that traffic entering a boundary is expected, allowed, and safe. From a Zero-Trust perspective, this explicitly verifies all available data points at the network level. You can place rules on the boundary to check for specific conditions.

    For example, at the header level, the rules can verify if the traffic originates from an expected location or is an expected volume. Even if it's expected traffic, do we allow it? However, that's not enough. The payload might not be safe as per the validation checks, such as SQL injection.

-   **Transform**. Mutate the packet at the boundary as a security measure. For example, you can remove HTTP headers for the risk of exposure. Or, you can choose to drop TLS at a point and reestablish at another hop with a certificate that's managed more rigorously.

### Classify the traffic flows

Given an initial schematic of the workload architecture, determine the intent and characteristics of the flow with respect to the functional utility and the operational aspects of the workload.

-   Does this workload need communication from external networks and what should be the required level of proximity from those networks?

-   What are the network characteristics of the flow, such as the expected protocol, source, and shape of the packets? Are there any compliance requirements at the networking level?

While there are many ways to classify traffic flows, here are the commonly used criteria:

#### Visibility from external networks

-   **Public**. The workload is public facing if its application and other components are reachable from the public internet. The application is exposed with one or more public IP addresses and public DNS.

-   **Private**. The workload is private if it can only be accessed through a private network such as VPN. It's exposed by one or more private IPs and potentially private DNS.

    In a private network, there's no line of sight from public internet to the workload. Technology choices for the gateway can be a load balancer or firewall. Those options can provide security assurances.

Even with public workloads, strive to keep much of the workload private as possible. Packets cross from public to private boundary. A gateway in that path acts as the transition point by acting as a reverse proxy.

#### Traffic direction 

-   **Ingress**. Inbound traffic toward the workload or its components. For secure ingress, apply the preceding set of key strategies. What is the source and is it expected, allowed, and safe? Attackers scanning public cloud provider IP ranges can successfully penetrate defenses, if ingress isn't checked and doesn't have the basic network security measures.

-   **Egress**. Outbound traffic from the workload or its components. Where is this traffic headed? Is that expected, allowed, and safe? The destination might be potentially malicious and cause data exfiltration risks.

:::image type="content" source="images/networking/internet-network-flow-public-cloud.png" alt-text="Diagram of internet network flow for Azure deployments." border="false" lightbox="images/networking/internet-network-flow-public-cloud.png":::

The level of exposure can also be determined by the workload's proximity to the public IP. For example, The application platform typically serves the public IP/DNS. The workload component is the face of the solution.

#### Scope of influence 

-   **North-south**. Traffic flow between the workload network and external networks. It's traffic that crosses the edge. External networks can be the public internet, corporate network, or any other network that's outside your scope of control.

    Both ingress and egress flows can be north-south traffic.
    
    As an example, consider the egress flow of a hub-spoke network topology. Depending on your definition of networking edge for the workload, the hub can be an external network. In that case, outbound traffic from the virtual network of the spoke is north-south traffic. However, if you consider the hub network within your sphere of control, north-south traffic could be extended to the firewall in the hub because the next hop is the internet, which is potentially hostile.
    
-   **East-west**. Traffic flows within the workload network. This represents various components of your workload communicating with each other.

    For example, traffic between the tiers of an n-tier application. Or in microservices, service to service communication would be considered as east-west traffic.
    
    To provide defense in depth, have end-to-end control of security affordances included in each hop or when packets cross internal segments. Those represent different risk levels, and different risk remediations.
    
    :::image type="content" source="images/networking/network-defense-depth-private-cloud.png" alt-text="Diagram that shows network defense in depth for private cloud." border="false" lightbox="images/networking/network-defense-depth-private-cloud.png":::
    
    Comparing this image to the public cloud image, notice that the public IP has shifted significantly away from the workload. There are now added layers. DNS has shifted right and there's a transition from public IP space to private IP space at this layer.

> [!NOTE] 
> Identity is always the primary perimeter. Access management must be applied to networking flows. Use managed identities when using Azure role-base access controls between the components of the network.

After classification, do a segmentation exercise to identify the firewall injection points on communication paths of the network segments. When you design your network defense in depth across all segments and all traffic types, assume breach at all points. Use a combination of various localized network controls at all available boundaries. For information, see [Segmentation strategies](segmentation.md).

### Apply firewalls at the edge

Internet edge traffic is north-south (both ingress and egress). An edge strategy must mitigate as many attacks to and from the internet as it's reasonable to detect or block threats.

For egress, send all internet bound traffic through a single firewall that provides more oversight, governance, and control of traffic. For ingress, force all traffic from the internet to go through an NVA and, or web application firewall (WAF).

-   Firewalls are usually singletons (deployed per region) in an organization. This means they're shared among workloads and owned by a central team. Work with that to make sure the NVA is configured to support the needs of your workload.

-   It's recommended that you use Azure native controls as much as possible.

    Partner NVAs that provide advanced or specialized features can be considered in addition to native controls. Partner firewall and WAF vendor products are available in Azure Marketplace.
    
    The decision to use native features as opposed to partner solutions should be based on your organization's experience and requirements.
    
    > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Partner capabilities often provide advanced features that can protect against sophisticated (but typically uncommon) attacks. Configuration of partner solutions can be complex and more fragile because they do not integrate with cloud's fabric controllers. From a cost perspective, native control is cheaper than partner solutions.

The technological options should provide security controls and monitoring for both ingress and egress flows. To see options available for Azure, see the [Edge security section](#edge-security) in this article.

### Design virtual network and subnet security 

The primary objective of private cloud is to obscure resources from the public internet. There are several ways of achieving that goal:

-   Move to private IP spaces, which is done through virtual networks. Minimize network line of sight even within your own private networks.

-   Minimize public DNS entries so that less of the workload is exposed.

-   Add ingress and egress network flow control. Don't allow traffic that's not trusted.

#### Segmentation strategy

To minimize network visibility, segment your network and start with least privilege network controls. If a segment isn't routable, then it cannot be accessed. Broaden the scope to only include segments that need to communicate with each other through network access.

Segmentation in virtual networks is done through subnetting. The criteria for division should be intentional. Make sure that the services that are co-located inside a subnet should be able to see each other.

You can segment based on many factors. For example, you can place different application tiers in their dedicated segments. Another approach is to plan your subnets based on common roles and functions that use well known protocols.

For more information, see [Segmentation strategies](segmentation.md).

#### Subnet firewalls

You want to inspect inbound and outbound traffic from the subnet. Again, use the three main strategies. Is the flow expected, allowed, and safe. To verify that set of information and subsequently set firewall rules, you need to know information about the protocol, source, and destination. On Azure, firewall rules are set on Network Security Groups. For more information, see the [Network security groups section](#network-security-groups) in this article.

For an example of a subnet design, see [Azure Virtual Network subnets](<!-- add link -->).

### Use controls at the component level

Next, map out your Azure resources from a network perspective and evaluate the flows:

-   **Planned traffic**. Intentional communication between services as per architecture design. For example, the architecture recommends that Azure Functions pulls messages from Service Bus.

-   **Management traffic**. Communication that happens as part of the service's functionality. This traffic isn't part of your design, and you have no control over it. For example, the Azure services in your architecture communicating with the Azure management plane.

Keeping that distinction will help you build localized or service-level controls. Have a good understanding of the source and destination at each hop; especially, how the data plane is exposed.

As a starting point, determine if the service is going to be exposed to the internet. If it is, how do you restrict access. If it isn't then you can place it in a virtual network.

#### Service firewalls

If the service is expected to be exposed to the internet, take advantage of the service-level firewall available for most Azure resources. You can set rules based on the access patterns. For more information, see the [Azure service firewalls section](#azure-service-firewalls) in this article.

> [!NOTE] 
> When your component isn\'t a service, such as a virtual machine, use host-based firewall in addition to the network-level firewalls.

#### Connectivity to PaaS services

Consider securing access to PaaS services by using private endpoints. That endpoint is assigned a private IP address from the virtual network and allows other resources in the network to communicate with the PaaS service over the private IP address.

Communication with a PaaS service is done by using the service's public IP and DNS record. That communication is over the internet. You can make that communication private.

A tunnel from the PaaS service into one of your subnets will create a private channel. All communication takes place from the component's private IP to a private endpoint in that subnet, which then talks to the PaaS service.

:::image type="content" source="images/networking/private-endpoints.png" alt-text="Diagram that shows private endpoints." border="false" lightbox="images/networking/private-endpoints.png":::

For more information, the [Private endpoints section](#private-endpoints) in this article.

> [!NOTE] 
> Using private endpoints in conjunction with service firewalls is recommended. Service firewall will block incoming internet traffic, then expose the service privately to internal users using private endpoint.

Another advantage for using private endpoints is that you don\'t need to open the ports on the firewall for outbound traffic. Private endpoints locks down all outbound traffic on the port for public internet, and keeps connectivity to only resources within the network.

### Protect against distributed denial of service

A Distributed Denial of Service (DDoS) attack attempts to exhaust an application\'s resources, making the application unavailable to legitimate users. DDoS attacks can be targeted at any endpoint that is publicly reachable through the internet.

DDoS is usually a mass, widespread, geo-graphically dispersed abuse of your system's resources that makes it hard to pinpoint and block the source.

For Azure's support to protect against these attacks, see the [Azure DDoS protection section](#azure-ddos-protection) in this article.

## Azure facilitation

#### Virtual Network

[Azure virtual network](/azure/virtual-network/virtual-networks-overview) enables Azure resources to securely communicate with each other, the internet, and on-premises networks.

All resources in a virtual network can communicate outbound to the internet, by default. However, inbound communication is restricted, by default.

It offers features that filter traffic by restricting access at the VNet level through the user-defined routing and Firewall component Also, at a subnet level by using network security groups.

#### Edge security 

By default, both ingress and egress flow over public IP addresses. Those addresses are either set by you or assigned by Azure, depending on the service or topology. Other ways include passing traffic through a load balancer or NAT gateway. Those services are intended for traffic distribution and not necessarily for security.

Recommended technology choices:

-   [Azure Firewall](/azure/firewall/overview) at the network edge and is used in popular network topologies, such as hub-spoke and VWan. Typically, Azure Firewalls is deployed as an egress firewall acting as the final security gate before traffic goes to the internet. It can route non-HTTP(s) protocols, such as RDP, SSH, FTP. It has a rich feature set that combines DNAT (port forwarding), IDPS signature detection, and strong layer 3/4 and FQDN network rules.

    > [!NOTE] 
    > Most organizations have a force tunneling policy. This means traffic must be forced through an NVA.
    
    If not using VWan topology, you must deploy a user-defined route (UDR) with NextHopType of internet to your NVA's private IP address. UDRs are applied at the subnet level. By default, subnet to subnet traffic doesn't route through the NVA.
    
    Azure Firewall can also be simultaneously used for ingress and can route HTTP(s) traffic. In higher SKUs, it offers TLS termination to do payload-level inspection.
    
    Here are some recommended practices:
    
    -   Enable Diagnostics Settings to get traffic flow logs, IDPS logs, and DNS request logs.
    
    -   Be as specific as possible in rules.
    
    -   Avoid FQDN Service Tags where practical. However, when you do use them, use the "regional" variant. It allows communication with all endpoints of that service.
    
    -	Use IP Groups to define sources that must share the same rules over the life of that IP Group. In this way, IP Groups should reflect your segmentation strategy.
      
    -	Only override the Infrastructure FQDN **allow** rule if absolute egress control is required in your workload; doing so will have a reliability tradeoff as Azure platform requirements change on services.

    
    > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Azure Firewall can impact your performance. There might be significant latency from rule order, quantity, TLS inspection, and others.
    >
    > There can also be an impact on the reliability of the workload. It might experience SNAT port exhaustion. Add Public IPs as necessary.
    
    > ![Risk icon](../_images/risk.svg) **Risk**: For egress traffic, Azure assigns a public IP. This can have a downstream security impact on external security gate).
    
-   [Azure Web Application Firewall (WAF)](/azure/web-application-firewall/overview) supports inbound filtering and only targets HTTP(s) traffic.

    It offers basic security that's a good start for common attacks, such as the OWASP Top 10. There are other security features focused on layer 7, such as Rate Limiting, SQL-injection rules, cross-site scripting, and others.
    
    With WAF, TLS termination is required, because most checks are payload based.
    
    You can use integrated WAF with routers, such as Azure Application Gateway or Front Door. Be aware that WAF implementations for those kinds of routers can vary.
    
Firewall and WAF aren't an either, or choice. See various patterns here: [Firewall, App Gateway for virtual networks - Azure Example Scenarios](/azure/architecture/example-scenario/gateway/firewall-application-gateway).

#### Network security groups 

A [network security group](/azure/virtual-network/manage-network-security-group) (NSG) is a layer 3/4 firewall applied at the subnet or network interface card (NIC) level.

NSG rules act as firewall to stop traffic flowing in and out at the perimeter of the subnet. NSGs aren't created or applied by default. An NSG has a default ruleset that's overly permissive. For example, the default rule doesn't set a firewall for egress perspective and no inbound internet for ingress.

Start with the default NSG, and create rules based on a 5-Tuple of information: Protocol, Source IP address, Source port, Destination IP address, Destination port.

-   **Inbound** (ingress): VirtualNetwork (direct, peered, and VPN gateway sources), LoadBalancer (Health Probes), all else blocked

-   **Outbound** (egress): VirtualNetwork (direct, peered, and VPN gateway destinations), internet, all else blocked

The lack of support for FQDN limits NSG functionality. You need to provide specific IP ranges for the workload, and they're hard to maintain.

However, for Azure services, source and Destination IP ranges can be summarized, with [Service Tags](/azure/virtual-network/service-tags-overview#available-service-tags). From a security perspective, the benefit is that Service Tags are opaque to the user and the responsibility is offloaded to Azure. You can also assign application security group (ASG) as a destination type to route traffic using a named group for resources that have similar inbound or outbound access needs.

> ![Risk icon](../_images/risk.svg) **Risk**: Service Tag ranges are very broad to accommodate the widest range of customers. Updates to Service Tags lag behind changes in the service.

:::image type="content" source="images/networking/virtual-network-default-isolation-peering.png" alt-text="Diagram that shows virtual network default isolation with peering." border="false" lightbox="images/networking/virtual-network-default-isolation-peering.png":::

In this image, NSGs are applied at the NIC. Internet traffic is denied and so is subnet to subnet traffic. The NSGs are applied with the VirtualNetwork tag. So, in this case, peered networks' subnets have direct line of sight. The broad definition of the VirtualNetwork tag can have a significant security impact.

-   When using Service Tags, use regional versions when possible, such as Storage.WestUS vs. Storage. You will limit scope to all endpoints in a particular region.

    Some tags are for **Inbound** or **Outbound** exclusively, others are for *both*. **Inbound** is usually allows traffic from all hosting workloads, such as AzureFrontDoor.Backend or from Azure to support service runtime, such as LogicAppsManagement. Similarly, **Outbound** allows traffic to all hosting workloads or Azure to support service runtime.
    
Scope the rules as much as possible. In the example below, the rule is set to these values. Any other type of traffic is denied.


|Information  |Example  |
|---------|---------|
|Protocol     | TCP, UDP        |
|Source IP address     |  Allow ingress to subnet from \<source IP range\>:4575/UDP       |
|Source port     | Allow ingress to subnet from \<service tag\>:443/TCP        |
|Destination IP address     |  Allow subnet to egress to \<destination IP range\>:443/TCP       |
|Destination port     | Allow subnet to egress to \<service tag\>:443/TCP        |

To summarize,

-   Be precise. Only allow what your application needs to function. Deny everything else. This will minimize network line of sight to just those network flows that are necessary to support the operation of the workload. Supporting more network flows than necessary leads to unnecessary attack vectors and extends the surface area.

    This doesn't imply that the allowed flows are beyond the scope of an attack*.* Because NSGs work at layer 3 and 4 on the OSI stack, they only contain shape and direction information. For example, if your workload needed DNS traffic to the internet, that would be an NSG of Internet:53:UDP, an attacker might be able to exfiltrate data through UDP on port 53 to some other service.

-   NSGs might differ slightly from one another. It's easy to skip the intent of the differences. It's always safer to create more network security groups to have granular filtering. At least have one NSG.

    - Adding an NSG unlocks many diagnostics tools: Flow Logs and Network Traffic Analytics.

    - Use Azure Policy to enforce subnets that don't have NSGs.

-   If a subnet supports NSGs, add one. Even if it's minimally meaningful.

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

:::image type="content" source="images/networking/controlled-ingress.png" alt-text="Diagram that shows controlled ingress, including App Gateway, NSG, Bastion, and Azure DDoS." border="false" lightbox="images/networking/controlled-ingress.png":::

#### Highlights

Azure Application Gateway is a web traffic load balancer that enables you to manage traffic to your web applications. It's placed in a dedicated subnet with NSG and WAF controls in place.

Communication with all PaaS services is done through private endpoints. All endpoints are placed in a dedicated subnet. Azure DDoS is protecting all public IPs with some level of firewall applied to them.

Management traffic is restricted through Azure Bastion, which provides secure and seamless RDP/SSH connectivity to your virtual machines directly from the Azure portal over TLS. Build agents are placed in the virtual network so they have a network view to workload resources such as the compute, container registry, and database. This approach provides a secure and isolated environment for your build agents, which can help protect your code and artifacts.

:::image type="content" source="images/networking/controlled-egress.png" alt-text="Diagram that shows controlled egress for  NSG and Azure Firewall." border="false" lightbox="images/networking/controlled-egress.png":::

#### Highlights

At the subnet level, egress traffic is restricted by NSG at the subnet level of the compute. All traffic is force tunneled through Azure Firewall. This approach provides a secure and isolated environment for your compute resources, which can help protect your data and applications.

## Related links

- [Recommendations for designing segmentation strategies](segmentation.md)
- [Azure virtual network](/azure/virtual-network/virtual-networks-overview)
- [Azure Firewall](/azure/firewall/overview)
- [Firewall, App Gateway for virtual networks - Azure Example Scenarios](/azure/architecture/example-scenario/gateway/firewall-application-gateway)
- [Network security group](/azure/virtual-network/manage-network-security-group)
- [Azure Private Link](/azure/private-link/private-link-overview)
- [Azure Bastion](/azure/bastion/bastion-overview)
- [Azure DDoS Protection Overview](/azure/ddos-protection/ddos-protection-overview)

## Next steps

We recommend that you review the Security checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)
---
title: Azure Well-Architected Framework perspective on Virtual Network
description: See Azure Well-Architected Framework design considerations and configuration recommendations that are relevant for Azure virtual network and its supported features.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 02/20/2024
ms.topic: conceptual
products:
  - azure-virtual-network
azure.category:
  - networking
---

# Azure Well-Architected Framework perspective on Virtual Network

Virtual Network is a fundamental building block for establishing a private network on Azure. It allows communication between with Azure resources, internet connectivity, and integration with on-premises systems. Additionally, it includes built-in filtering capabilities to make sure that only expected, allowed, and safe traffic reaches the components within the network boundaries.

This article assumes that as an architect you've reviewed [SE:06 Recommendations for networking and connectivity](/azure/architecture/guide/technology-choices/compute-decision-tree) and understand the key strategies. This article builds on those strategies and balances the approaches of all the [Azure Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope. 
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Virtual Network and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments. 
>
> Foundational architecture that demonstrates the key recommendations: [Hub-spoke network topology in Azure](/azure/architecture/networking/architecture/hub-spoke).

##### Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- Virtual networks, its subnets, and network interface cards (NICs)
- Private endpoints
- Network Security Groups (NSGs)
- IP addresses, allocations
- Route tables
- Network managers

There are other services associated with Virtual Network, such as the load balancers. Those services are covered in their respective guides. 

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

The [**Reliability design principles**](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

##### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of Virtual Network and its dependencies. Extend the strategy to include more approaches as needed.


> [!div class="checklist"]
>
> - **Set your reliability targets**. Azure VNets and most of its subservices don't have Microsoft-backed SLA guarantees.  However, specific services like load balancers, Network Interface Cards (NICs), and Public IPs do have SLAs. It's recommended that you have a good understanding of the coverage provided around the published percentile by Azure. Keep in mind that your central IT services organization typically owns the VNet and central services. Your objective calculation should account for that dependency.  
>
> - **Mitigate points of failure**. Do failure mode analysis (FMA) and identify single points of failure in network connections. Here are some examples: 
>
>   |Failure|Mitigation|
>   |---|---|
>   | Public IP failure in a single availability zone. | Deploy IP resources across zones or use secondary IPs with a load balancer.|
>   | NVA failure in a single zone. | Deploy a secondary NVA in another zone and use a load balancer to direct traffic to the NVA. |
>   | Latency in workloads spread across regions or zones, lowering throughput and causing timeouts.|Co-locate resources in one region or zone. Redesign the architecture to use reliability patterns, such as deployment stamps with load balancers, so that each stamp can handle the load and work together with nearby resources.|
>   | Single-region workload failure with cold failover site.| Pre-configure network settings in the failover region, ensuring no IP address overlap.|
>   | Single-region application failure in a VNet that communicates with database via Private Link, using cold failover site.| Replicate connections in the secondary region and peer VNets for communication.|
>
> - **Overprovision IP address spaces**. To ensure reliable scaling, a common strategy is to overprovision capacity to prevent IP exhaustion. However, this approach involves a tradeoff between reliability and operational efficiency. Subnets should only use a portion of the VNet's address space. The goal should be to have just enough extra address space in your VNet and subnets to balance reliability with operational efficiency.
>
> - **Be aware of the networking limits**. Azure imposes limits on the number of resources you can deploy. While most Azure networking limits are set to their maximum values, some can be increased. For more details, see [Networking limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
>
> - **Create network diagrams with focus on user flows**. These diagrams are valuable for visualizing network segmentation, identifying potential points of failure, and pinpointing key transitions like internet ingress and egress points. They also serve as important tools for audits and incident response.
>
>   Highlight high priority traffic flows between the user and workload resources. For instance, prioritizing ExpressRoute for enterprise network flows or securing user requests in a DMZ design can provide insight into capacity planning for firewalls and other services.
> 
> - **Add redundancy**. Consider deploying NAT gateways and VNets across multiple regions if necessary, ensuring public IPs and other availability zones aware services have zone redundancy enabled, and making shared resources like firewalls also region redundant. 
>
>   For more infiormation, see [Business Continuity](/azure/virtual-network/virtual-network-disaster-recovery-guidance).
>
> - **Avoid complexity**. Pay attention to VNets, subnets, IPs, routes, ASGs, and tags. Simpler configurations decrease the likelihood of misconfigurations and error, which can contribute to reliability issues and add to operational and maintenance costs. Some examples of simplification include: 
>   - Using private DNS whenever possible and minimizing the number of DNS zones. 
>   - Simplifying routing configurations. Consider routing all traffic through the firewall, if used in the architecture.
>
> - **Test the resiliency of the network**. Utilize Azure Chaos Studio to simulate network connectivity disruptions, ensuring workload redundancy and assessing the impact range of potential failures.
>
> - **Monitor network traffic for reliability impact .** Monitoring traffic flow is a crucial operation for reliability. For example, you want to identify top talkers in your network to see if they can cause any disruption. Azure provides Flow Logging capabilities, which are described in [Operational Excellence](#operational-excellence).

##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|Size VNets and subnets as per your scaling strategy. Opt for fewer, larger VNets accommodate redundancy as a mitigation strategy for failures. <br><br> Ensure there aren't overlapping address space with other VNets you need to communicate with, and plan the address space in advance.  <br><br> See, [**Create, change, or delete a virtual network**](/azure/virtual-network/manage-virtual-network).| With a bit of overprovisioning, you can ensure that the network can scale efficiently without running into address space limitations. <br>Planning the address space in advance helps avoid conflicts and ensures a smooth, scalable network architecture. |
|Use the [**standard Public IP SKU**](/azure/virtual-network/ip-services/public-ip-addresses#sku) for better reliability support through availability zones. <br> By default, public IPs are deployed across multiple zones unless restricted to one zone.|Ensures that communication within a public IP remains operational during zonal failures.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design around hosting on App Service.

##### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Establish a security baseline**. Review the [security baseline for Virtual Network](/security/benchmark/azure/baselines/virtual-network-security-baseline) and incorporate applicable measures in your baseline. 
>
> - **Keep your network perimeter up to date**. Security settings, such as Network Security Groups (NSGs), Application Security Groups (ASGs), and even IP ranges must be updated regularly. Outdated rules may not align with current network architecture or traffic patterns. This security gap can be leaving your network exposed to potential attacks by lowering restrictions on ingress and egress traffic. 
>
> - **Use segmentation to enhance security**. Use NSGs as L4 firewalls at the subnet level. Route all external traffic through an network virtual appliance, such as a firewall, by using UDRs for monitoring and management. Use fully qualified domain names (FQDN) to filter internet access.
>
>    Secure PaaS service connectivity with private endpoints while blocking outbound connections.   
>
> - **Apply the principle of least privilege**. Configure role-based-access-controls (RBAC) with a no-access mindset for network-related roles. Make sure that users are only able to modify settings as required by their job function.
>
> - **Limit public IP addresses**. Use shared public IPs from services like Azure Front Door for better security and initial request checks. Managing a dedicated public IP requires you to oversee its security, including port management and request validation. Where possible, use private connectivity.


##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|[**Use the VNet encryption**](/azure/virtual-network/virtual-network-encryption-overview).| By enforcing encrypted traffic, you can protect data in transit between Virtual Machines and Virtual Machines Scale Sets within the same virtual network. It also encrypts traffic between regionally and globally peered virtual networks |
|[**Enable Virtual Network Verifier**](/azure/virtual-network-manager/concept-virtual-network-verifier) in the Azure Virtual Network Manager. <br><br> Use this feature in your preproduction environment to test connectivity between resources. This feature isn't recommended in production. |You want to make sure that the Azure resources within the network are reachable and not blocked by policies.|
|Enable [**Azure DDoS Network Protection**](/azure/ddos-protection/manage-ddos-protection) for the VNet. Alternately, you can protect individual public IP addresses through [**Azure DDoS IP Protection**](/azure/ddos-protection/manage-ddos-ip-protection-portal). <br><br>  [Review the security features](/azure/ddos-protection/ddos-protection-sku-comparison) offered in those tiers and choose one that fits your requirements. For example, with Network Protection, you get support by the Rapid Response Team when you are under attack, where as with IP Protection, you don't. |You're able to safeguard against DDoS attacks.|
|Safeguard segments within a VNet using [**Network Security Groups (NSG)**](/azure/virtual-network/network-security-groups-overview). <br><br> Where possible, use Application Security Groups (ASG) to define the rules. |Traffic entering and leaving the network can be filtered based on IP and port ranges. <br> ASGs simplifies management by abstracting the underlying IP ranges. |
|Use [**private endpoints**](/azure/private-link/private-endpoint-overview) to access Azure services over a private IP address within the VNet. <br><br> Another way to implement private networking is through [**service endpoints**](/azure/virtual-network/virtual-network-service-endpoints-overview). These endpoints routes traffic to a service over the Azure network backbone. Prefer private endpoint over service endpoints, if available for the service.| Private endpoints eliminate the need for public IP addresses, reducing the attack surface.|


## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.  

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to your networking environment.

##### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Optimize the "fat flows"**. These flows refer to large data transfers between endpoints. VNet peering is recommended for efficiently moving data between VNets. Even though peering has inbound and outbound costs, this approach can be cost-effective because it reduces bandwidth consumption and network performance issues. Avoid routing through a hub to minimize inefficiencies and costs.
>
>   To optimize data transfer between regions, it's important to consider both the frequency and the method of transfer. For instance, when dealing with backups, the location where you save your backups can significantly impact costs. Storing backup data in a different region incurs bandwidth. To mitigate these costs, ensure that data is compressed before transferring it across regions. Additionally, adjusting the frequency of data transfers can further optimize costs and efficiency. 
>
> - **Include networking components in your cost model**. Account for the hidden costs when creating or adjusting your budget. For example, in multi-region architectures, there's extra cost of data transfer between regions. 
>
>   Azure cost reports may not include the expenses associated with third-party network virtual appliances (NVAs), which have separate licensing costs. They may also have different billing models for fixed price and consumption-based options. Make sure you include these aspects in your budget considerations.  
>
>   Certain networking resources can be expensive, such as Azure Firewall and ExpressRoute. Organizations usually provision these resources in centralized hub model and teams can be cross-charged for the incurred cost. Don't forget to account for that charge in your cost model.
>
> - **Don't pay for unused capabilities**. Regularly review component costs and remove legacy features or default configurations. Limit the number of public IPs to save costs. This also enhances security by reducing the attack surface, creating a win-win situation.
> 
> - **Optimize private endpoints.** Analyze if you can reuse a Private Link to a resource from other VNets. When you use Private Endpoint across a regional virtual network peering, you're not charged peering fees for traffic to and from Private Endpoint. You only pay for the Private Link access itself, not for the traffic between VNets.  Review [Azure Private Link in a hub-and-spoke network](/azure/architecture/networking/guide/private-link-hub-spoke-network) to learn about scenarios for accessing private endpoints across vnets.
>
> - **Align the network traffic inspection functions with the priority and security requirements of the flow**. If there's large bandwidth requirements, consider routing traffic to lower-cost paths. While ExpressRoute is suitable for large traffic, it can be expensive. Consider alternatives like public endpoints for cost savings, however, be aware of the tradeoff with security. Use network peering for VNet-to-VNet traffic, bypassing the firewall, to avoid unnecessary inspection.
>
>   Intentionally allow only necessary traffic between components and block unexpected traffic. If traffic is expected and the flow is aligned with your security requirements, those checkpoints could be omitted. For example, evaluate if you need to route traffic through a firewall if the remote resource is within the trust boundary.
>
>   Even within a VNet, evaluate the number of subnets and their associated network security groups (NSGs). The more NSGs you have, the higher the operational costs for managing the rulesets. Where possible, use application security groups (ASGs) to streamline management and reduce costs.
>  
> - **Optimize code costs**. When developing your application, choose more efficient protocols and apply data compression to optimize performance. For example, in a web app, configuring components to compress data can enhance efficiency. These optimizations also have an impact on performance.
>
> - **Take advantage of resources in the centralized VNet**. Using centralized resources reduces duplication and overhead. Additionally, offloading responsibilities to existing teams can further help in cost optimization and allow for delegation of expertise for specific functions.


##### Recommendations

| Recommendation|Benefit|
|-----------|-------- |
|[**Use VNet peering**](/azure/virtual-network/virtual-network-peering-overview) to make network flow more efficient by bypassing controls. <br><br> Avoid excessive peering.|Transferring  data directly between peered VNets, bypassing the firewal reduces bandwidth consumption and network performance issues.<br><br> Avoid putting all your resources in a single VNet. Even though you may be incurring peering cost, it's not practical to put all resources in a single VNet just to save costs. It can hinder growth. The VNet can eventually reach a point where new resources don't fit anymore.|
|[**Minimize public IP address resources**](/azure/virtual-network/ip-services/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address), if you don't need them. <br><br> Before deletion, make sure IP address isn't linked with any IP configuration or virtual machine network interface. |Maintaining unnecessary public IPs can increase costs due to resource charges and operational overhead.|


## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals towards the operational requirements of the workload.

##### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to your networking environment.

> [!div class="checklist"]
>
> - **Skill up on Azure networking constructs**.  When onboarding to Azure, networking teams often assume that their existing knowledge will suffice, but many aspects differ in Azure. Make sure the team understands the fundamental Azure networking concepts, DNS complexities, routing, and security capabilities. Build a taxonomy of networking services so that the team can share knowledge and have a common understanding.
>
> - **Formalize your network design, strive for simplicity**. Document the design and any changes, including configuration details like route tables, network security groups (NSGs), and firewall rules. Include the governance policies that are in place, such as blocking ports. Having clear documentation will make collaboration with other teams and stakeholders effective.
>
>   Simplified networks are easier to monitor, troubleshoot, and maintain. For example, if your topology is hub-spoke, minimize direct peerings between spokes to reduce operational burden and strengthen security. Always document the design, providing justifications for each design decision.
>
>   Reduce complexity by using aliases instead of direct IP ranges. This will lower operational burden. 
>
> - **Use design patterns that optimize network traffic**. To optimize network use and configuration, implement known design patterns that minimize or optimize network traffic. Additionally, validate the network configuration during builds using security scanners to ensure everything is set up correctly.
>
> - **Do consistent network deployments**. Use Infrastructure as Code (IaC) for all components, including network peerings and private endpoints. Recognize that core networking components are likely to change less frequently than other components. Implement a layered deployment approach for your stack so that each layer can be independently deployed. Also, avoid combining IaC with scripting to prevent complexity.
>
> - **Monitor the networking stack**. Continuously monitor the traffic patterns to identify anomalies, issues such as connection drops before they lead to cascading failures. Where possible set alerts to get notified about these disruptions.
>
>   Similar to other components in the architecture, capture all relevant metrics and logs from various networking components, such as the virtual network, subnets, network security groups, firewalls, load balancers, and others. Aggregate, visualize, and analyze them in your dashboards, creating alerts on important events.
>
> - **Include networking in your failure mitigation strategy**. VNets and subnets are deployed initially and typically remain unchanged, making rollbacks difficult. However, you can optimize your recovery by following a few strategies: 
>  
>   Duplicate networking infrastructure in advance, especially for hybrid setups. Ensure separate routes in different regions are ready to communicate with each other beforehand. Replicate and maintain consistent network security groups (NSGs) and Azure Firewall rules across both primary and disaster recovery (DR) sites. This process can be time-consuming and requires approval, but doing it in advance helps prevent issues and failures. Make sure you test the networking stack in the DR site.
>
>   Avoid overlapping IP address ranges between your production and DR networks. By maintaining distinct IP ranges, you can simplify network management and expedite the transition during a failover event. 
>
>   Consider the tradeoffs between cost and reliability. For more information, see [Tradeoffs](#tradeoffs).
>
> - **Offload network operations to central teams**. Centralize management and governance of networking infrastructure, where possible. For example, in a hub-spoke topology, services like Azure Firewall, ExpressRoute, DNS, and others that are intended for shared use are placed in the hub network. That networking stack should be centrally managed taking the burden off the workload team. 
>
>   Even in the spoke network, offload the administration of the VNet to the central team. Minimize network operations to what is pertinent to the workload, such as management of NSGs. 
>
>   Keep the central teams informed of any necessary changes in the workload that might impact configuration of the shared resources. 
>
>  - **Prefer service names  over IP addresses**. When defining routes, use service names or aliases instead of specific IP addresses. This approach ensures reliability because IP addresses can change but the configuration doesn't need to. Also, it helps overcome limits on the number of routes or rules you can set by using more generic names.
>
> - **Right-size your VNet and subnets**. Opt for fewer, larger VNets to reduce management overhead but avoid making subnets excessively large. Managing subnets and their NSGs can add to operational burden. For environments with limited private IP addresses (RFC 1918) availability, consider using IPv6.

| Recommendation|Benefit|
|-----------|-------- |
| Deploy [**Azure Network Manager**](/azure/virtual-network-manager/overview). |Instead of configuring each VNet individually, Azure Network Manager centrally manages connectivity based on rules. This makes networking operations more streamlined.|
| Use networking monitoring tools. <br><br> - Regularly use [VNet Flow Logs](/azure/network-watcher/vnet-flow-logs-overview) and [Traffic analytics](/azure/network-watcher/traffic-analytics) to verify to identify changes in demand and patterns. <br> - Use [Connection monitor](/azure/network-watcher/connection-monitor-overview) to analyze and identify issues like connection drops before they impact applications.|You're be able to understand how data flows through your network, identify bottlenecks, identify unusual or unauthorized access attempts. |
|When defining routes, use [**service tags**](/azure/virtual-network/service-tags-overview) instead of specific IP addresses. <br><br> Similarly, use Application Security Groups (ASG) when defining traffic rules for NSGs.| This approach ensures reliability because IP addresses can change but the configuration doesn't need to. Also, it helps overcome limits on the number of routes or rules you can set by using more generic names.|


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on the key performance indicators.

> [!div class="checklist"]
>
> - **Define performance targets**. To define performance targets, rely on monitoring metrics, particularly for latency and bandwidth. Use connection monitor data, such as latency and the number of hops, to set targets and thresholds for acceptable performance. Application Insights offers a detailed view of the time workload requests spend in the network, helping to refine these targets. 
>
> - **Right-size your subnets**. When allocating subnets, it's important to strike a balance between size and scalability. You want subnets to be large enough to accommodate projected growth without operational burden.
>
>   To manage capacity effectively, a common strategy is to overprovision capacity due to uncertainty, but the goal should be to optimize over time. Continuously analyze data, so your network can handle the load but you don't pay extra for unused resources. 
>
> - **Conduct performance testing**. Use combination of synthetic and production data to test latency and bandwidth to check how those aspects might affect workload performance. For example, detecting resources that consume more bandwidth than expected and cause noisy neighbor issues. Or, traffic that's making multiple hops, can be the cause of the high latency.
>
>   It's recommended that you test in production or capture and replay production data as test data. This approach ensures tests are reflective of actual usage, which will lead to setting realistic performance targets.
>
> - **Monitor traffic across regions*. It's important to consider that workload resources might be located in different regions. Communication across regions can add significant latency. While traffic across availability zones in the same region has low latency, it may not be fast enough for some specialized workloads.
>


| Recommendation|Benefit|
|-----------|-------- |
|[**Enable the Connection Monitor**](/azure/network-watcher/connection-monitor-overview) of Network Watcher. <br><br>Use connection monitor during testing, which can generate synthetic traffic.|You're able to collect metrics that indicate loss and latency across networks. Also, trace the entire traffic path, which is important for detecting network bottlenecks.|
|Keep the virtual network address space large enough to support scaling.|TBD|


## Tradeoffs

You might have to make design tradeoffs if you use the approaches in the pillar checklists. Here are some examples of advantages and drawbacks.

:::image type="icon" source="../_images/trade-off.svg"::: **Redundant networking stack**

When you choose to implement a redundant networking stack, including its NSGs, routes, and other configurations, there's added cost of the infrastructure and thorough testing. 

However, this upfront investment enhances reliability. You can be sure that everything works as expected and recovery during disruptions can be faster. 

:::image type="icon" source="../_images/trade-off.svg"::: **VNet peering**

Direct VNet peering enhances performance by reducing latency, because it avoids the need to route traffic through a hub where a firewall decrypts, inspects, and re-encrypts the payload. 

That performance gain comes at the cost of decreased security. Without the firewall inspections provided by hub routing, the workload is more vulnerable to potential threats. 


:::image type="icon" source="../_images/trade-off.svg"::: **Large subnets**

Large subnets provide ample address space, allowing workloads to scale out seamlessly. While large address space can safeguard against unexpected demand spikes, it can also lead to inefficient use of IP addresses, potentially causing IP address exhaustion as the workload evolves over time. Also, this strategy comes with higher operational costs. From an Operational Excellence perspective, it's ideal to keep your subnets as small as possible.

## Azure policies

Azure provides an extensive set of built-in policies related to Virtual Network and its dependencies. Define and assign policies to ensure resources comply with organizational standards. Build [Azure Policy Compliance Dashboard](/azure/governance/policy/how-to/get-compliance-data) to identify noncompliant resources and take corrective actions.

A set of Azure policies can audit some of the preceding recommendations. For example, you can set policies that automatically:

- Protects the VNet against volumetric and protocol attacks.
- Denies creation of network interfaces with public IPs. 
- Deploys network watcher for VNets.
- Enable Traffic analytics and Flow logs to monitor traffic patterns. 

For comprehensive governance, review the [Azure Policy built-in definitions](/azure/governance/policy/samples/built-in-policies#network) and other policies that might affect the security of the network layer.


## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of your web application instances.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations)
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations)
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations)
- [Performance](/azure/advisor/advisor-reference-performance-recommendations)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations)

## Next steps

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- For hub-spoke topology, use [this reference architecture](/azure/architecture/networking/architecture/hub-spoke) to set up your initial stamp. 
  
- Use the following product documentation to build your implementation expertise:

  - [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
  - [Virtual Network IP Services documentation](/azure/virtual-network/ip-services/)
  

---
title: "Architecture Best Practices for Azure Virtual WAN"
description: "Architectural recommendations for Azure Virtual WAN mapped to the principles of the Well-Architected Framework pillars."
author: josevarela
ms.author: josev
ms.topic: conceptual
ms.date: 11/03/2025
ms.subservice: waf-service-guide
products:
 - azure-virtual-wan
 - azure-vpn-gateway
azure.category:
 - networking
 - hybrid
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD025 -->

# Architecture best practices for Azure Virtual WAN

Azure Virtual WAN is a centralized networking service that unifies connectivity, security, and routing in a single interface. It enables global branch and user connectivity through VPN and ExpressRoute, supports secure private links with encryption, and integrates Azure Firewall for protection. The Virtual WAN architecture is a hub and spoke architecture with scale and performance to enable a global transit network architecture.

The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Virtual WAN
- Azure VPN Gateway

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review service limits and known issues:** [Azure Virtual WAN limits documentation](/azure/virtual-wan/virtual-wan-faq) and [known issues list](/azure/virtual-wan/virtual-wan-faq#known-issues) before architecture planning. Virtual WAN enforces limits on connected resources per hub and per subscription. Known issues include available workarounds.
>
>    Choose Virtual WAN [Standard SKU](/azure/virtual-wan/virtual-wan-about#basicstandard) for production deployments to access zone redundancy and advanced routing capabilities.
>
> - **Anticipate potential failures through failure mode analysis:** Failure mode analysis provides a systematic approach to anticipating potential failure scenarios and developing mitigation strategies to ensure messaging service resilience.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Region outage affecting a single-region vWAN hub| Deploy in multiple regions with multiple hubs, ensuring continued connectivity when one region becomes unavailable. |
>    | Failure of ExpressRoute connection | Deploy redundant circuits or backup VPN connections to maintain hybrid connectivity during outages. |
>    | Branch site VPN connection network failure | Provision the site-to-site VPN multiple with redundant ISP Links, protecting against provider-level connectivity issues. |
>
> - **Understand SLA and composite reliability targets:** Virtual WAN hub routing infrastructure has a 99.95% SLA. Downtime means inability to establish or maintain connections. See SLA terms for service credits.
>
>    End-to-end connectivity requires composite SLA calculation. Hubs, gateways, and firewalls each have independent SLAs. Calculate the combined SLA for your critical connectivity paths.
>
>    The SLA excludes customer responsibilities including on-premises equipment failures, internet connectivity issues outside Azure's control, and customer configuration errors.
>
> - **Plan for multi-region redundancy for hub deployment:** Distribute virtual hubs across multiple Azure regions to achieve region-level redundancy.

> Region selection should prioritize:

> - Proximity to users and workloads.
> - Paired regions for disaster recovery.
> - Cross-region connectivity requirements.
> - Regulatory and data residency constraints.
>
> Configure routing to support automatic or manual region failover. Plan for asymmetric routing patterns during failures and document expected outage behavior. Regulatory and data residency requirements may constrain multi-region deployment strategies.
>
> - **Plan for layered redundancy:** Virtual WAN supports three redundancy layers, each addressing distinct failure scenarios:

> | Layer | Protection Against |
> |-------|-------------------|
> | Zone-level | Datacenter failures within a region. |
> | Connection-level | Single path failures. |
> | Route-level | Enables traffic engineering and failover control. |
>
> Balance redundancy decisions against availability needs, implementation complexity, and cost. Evaluate each failure domain's impact on workload operations. Choose routing patterns (symmetric or asymmetric) that match operational capabilities.

> - **Plan for reliable hub and gateway scaling:** Virtual WAN requires careful capacity planning. The hub router supports autoscaling but takes up to 25 minutes to complete scaling operations, while gateways require manual sizing and cannot scale dynamically.
>
>    Size infrastructure for expected growth, not current traffic. Overprovision capacity to maintain reliability. Reactive scaling risks capacity exhaustion during demand spikes, and gateway resizing requires scheduled maintenance.
>
> - **Configure resilience features for connectivity:** Design resilience by mapping each connectivity pattern to specific Virtual WAN features. For Point-to-Site, enable a global VPN profile so clients get automatic hub selection and failover. For ExpressRoute, configure a VPN backup path and set BGP preference so ExpressRoute is preferred but VPN takes over when needed. Validate routing and NVA behavior during failover and document expected traffic flows and client impact.
>
>    Plan capacity and addressing with failover scenarios in mind. Size P2S address pools to handle at least twice the expected concurrent users, provision hub and gateway capacity with headroom, and overprovision gateway and ExpressRoute throughput where reliability matters.
>
> - **Incorporate business continuity testing into your design:** Test your failover configuration to avoid surprises during actual outages.
>
> Testing should simulate:
>
> - Region failures to verify multi-region failover behavior.
> - ExpressRoute circuit failures to confirm VPN backup activation.
> - Parallel path scenarios to identify asymmetric routing patterns.
> - BGP route advertisement during failover events.
>
> - **configure monitoring and alerting for service health:** Set up monitoring for hub routers, gateways, and routing infrastructure to detect issues before users report outages. Configure alerts for performance degradation to enable early response.
>
>    Enable diagnostic logs across all Virtual WAN components to support troubleshooting through event correlation. Resource Health distinguishes Azure service issues from customer-side connectivity problems.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Choose [Virtual WAN Standard SKU](/azure/virtual-wan/virtual-wan-about#basicstandard) for production deployments to access critical reliability capabilities. The Standard SKU provides zone redundancy, supports advanced routing scenarios, and enables hub-to-hub and branch-to-branch connectivity patterns. | The Standard SKU lets you deploy zone-redundant gateways across availability zones to maintain connectivity during datacenter outages within a region. And supports global transit architecture by enabling hub-to-hub connectivity for multi-region deployments, allowing you to route traffic between branches through Virtual WAN hubs.|
| Select [Network Virtual Appliances](/azure/virtual-wan/about-nva-hub) with native Virtual WAN hub integration. Native integration provides better reliability and support while avoiding complex custom routing configurations. | Native NVA integration lets you deploy NVAs directly in the virtual hub without requiring separate VNet configurations, avoiding User Defined Route maintenance overhead while leveraging combined Microsoft and partner support. |
| Deploy virtual hubs in at least two [Azure regions](/azure/virtual-wan/virtual-wan-global-transit-network-architecture) to establish redundancy. Choose regions close to users and workloads, and consider using Azure paired regions for enhanced disaster recovery capabilities. | Multi-region deployment protects against complete region failures. You can continue network operations when the primary region fails by providing automatic or manual failover to secondary region hubs, thereby preserving branch and VNet connectivity through the alternate region.<br><br>This architecture also enables workload distribution across regions for load balancing purposes. |
| Configure VPN gateways with the [zone-redundant SKU](/azure/vpn-gateway/about-zone-redundant-vnet-gateways) to enable automatic distribution across availability zones. Verify that the target region supports availability zones and confirm successful zone distribution through gateway properties. | Zone-redundant VPN gateways eliminate single datacenter dependency. You can maintain VPN tunnel availability when a single availability zone fails through automatic distribution of gateway instances across zones, avoiding manual failover procedures during datacenter outages. |
| Configure [Site-to-Site VPN tunnels](/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) in active-active configuration by creating separate tunnels with identical settings to both VPN gateway instances. Enable BGP peering on both tunnels for dynamic routing and immediate failover without convergence delay. | Active-active VPN tunnels eliminate convergence delays during failover events. Traffic routes through the alternate tunnel instantly when the primary fails.<br><br>This configuration also improves throughput compared to active-passive deployments. |
| Configure active-active route advertisement by advertising routes from on-premises to Azure across all [ExpressRoute circuits](/azure/expressroute/designing-for-high-availability-with-expressroute). Use BGP AS path prepending for traffic engineering when needed and monitor route advertisement status on all connections. | Active-active routing maximizes ExpressRoute redundancy and bandwidth. You can advertise identical routes across all circuits to enable automatic failover and maintain connectivity when any single circuit fails.<br><br>This approach provides control over traffic distribution by using AS path prepending to influence inbound traffic routing and optimizing network utilization through active-active forwarding. |
| Configure virtual [hub routing infrastructure units](/azure/virtual-wan/hub-settings) above baseline requirements to accommodate anticipated growth. Set units based on expected traffic and growth projections.<br><br>Monitor routing capacity utilization metrics to identify scaling needs and prevent performance degradation as traffic increases. | Proactive capacity configuration avoids routing performance bottlenecks when traffic exceeds baseline capacity and eliminates the 25-minute scaling delay impact on active connections.<br><br>This approach supports business growth by accommodating workload expansion without service disruption. |
| Configure VPN and ExpressRoute [gateway scale units](/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings) for expected aggregate throughput with overprovisioning for future growth. Size gateway scale units based on throughput requirements and growth projections, recognizing that gateways don't autoscale and scale unit changes require maintenance windows. | Proper gateway sizing prevents capacity bottlenecks and service disruptions. You can prevent connectivity degradation from gateway capacity exhaustion, avoid packet loss when traffic exceeds gateway capacity, and maintain consistent connection quality for users.<br><br>This approach reduces operational overhead by minimizing the frequency of gateway resize operations and associated maintenance windows. |
| Configure a [global VPN profile](/azure/virtual-wan/global-hub-profile) across all hubs to enable automatic hub selection for Point-to-Site connections. The global profile directs VPN clients to the nearest available hub and provides resilience when the primary hub becomes unavailable.| Global VPN profile and adequate address pool sizing work together to ensure reliable remote user connectivity. Users connect to the nearest available hub for optimal latency with automatic failover to an alternate hub when the primary fails, maintaining remote user productivity during hub outages.|
| Configure the [Point-to-Site address pool](/azure/vpn-gateway/vpn-gateway-vpn-faq) with capacity to support twice the expected user count. Size the address pool to prevent exhaustion during gateway instance redistribution and monitor address pool utilization for ongoing capacity planning. | This configuration simplifies operations by allowing deployment of a single VPN profile to all users regardless of their location, while preventing address exhaustion during gateway instance redistribution. It enables a seamless user experience during gateway maintenance or failures and supports business continuity for your remote workforce.  |
| Configure Site-to-Site [VPN as backup connectivity](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-use-vpn-to-backup-my-expressroute-connection) for ExpressRoute circuits by deploying a VPN gateway alongside the ExpressRoute gateway in the virtual hub. Set BGP preference to favor ExpressRoute over VPN. | VPN backup maintains connectivity when ExpressRoute fails. Preserving application availability for services dependent on on-premises resources. |
| Configure [diagnostic settings](/azure/virtual-wan/monitor-virtual-wan) for all Virtual WAN resources with output to a Log Analytics workspace. Enable diagnostics for virtual hubs, gateways, and firewalls, and send diagnostic logs to a centralized Log Analytics workspace for comprehensive visibility. | Diagnostic logging and alerting enable proactive issue detection and rapid incident response. You can identify connectivity problems before users report outages, discover performance degradation through systematic metric analysis, and notify operations teams instantly when connectivity failures occur. |
| Configure alerts for [Virtual WAN metrics and Resource Health](/azure/virtual-wan/monitor-virtual-wan-reference) events. Create alerts for gateway connectivity failures and BGP peer session status changes, set alerts for routing infrastructure capacity thresholds, and enable Resource Health alerts. | This approach accelerates troubleshooting by providing access to centralized logs for correlation across Virtual WAN components, analyzing historical data to identify patterns, and triggering automated remediation workflows for common issues. |
| Review the [Virtual WAN disaster recovery guidance](/azure/virtual-wan/disaster-recovery-design) to understand failure scenarios and mitigation strategies. | Systematic failure mode analysis identifies reliability risks early in design phase, enabling proactive mitigation through redundancy and failover mechanisms before production deployment. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Virtual WAN.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish security baseline:** Apply [Azure Security Baseline for Virtual WAN](/security/benchmark/azure/baselines/virtual-wan-security-baseline) as your foundational security baseline. The baseline covers Virtual WAN-specific controls for hub security, gateway configuration, routing security, and connection encryption.
>
>    This standardized approach addresses Virtual WAN service endpoints including virtual hubs, VPN gateways, ExpressRoute gateways, and Azure Firewall integration, while providing compliance mapping for network infrastructure requirements.
>
> - **Implement network segmentation through routing control mechanisms:** Control network segmentation in Virtual WAN through routing tables. Create custom route tables to isolate routing domains and prevent unauthorized traffic between zones. Apply route maps when standard route table isolation doesn't meet connection requirements.
>
>    Deploy a secured virtual hub to inspect traffic centrally. Use Azure Firewall for threat intelligence and Azure service integration, or deploy partner NVAs for deep packet inspection and advanced threat prevention.
>
> - **Establish private connectivity patterns and protect public endpoints:** Deploy private endpoints in spoke networks to access Azure PaaS services without internet exposure. On-premises users reach these services through ExpressRoute or VPN connections. Routing through secured hubs enables inspection but adds latency and cost.
>
>    Protect public-facing spoke workloads with Azure DDoS Protection at the virtual network or IP level. DDoS Protection isn't available for hub public IPs.
>
> - **Implement identity and access management:** Select your Point-to-Site VPN authentication method based on your requirements:
>
> - Microsoft Entra ID authentication provides Conditional Access policies, MFA, device compliance checks, and risk-based authentication.
> - Certificate-based authentication requires PKI infrastructure and certificate lifecycle management.
> - RADIUS authentication integrates with existing RADIUS infrastructure.
>
> Control Virtual WAN configuration access using Role-Based Access Control (RBAC) assignments:
>
> - Apply RBAC assignments to control who can modify Virtual WAN configuration.
> - Use built-in Network Contributor and Reader roles for most scenarios.
> - Create custom roles to enable more granular permission boundaries when built-in roles prove too permissive.
>
> - **Control and filter network traffic using defense-in-depth approach:** Deploy security controls at multiple network layers. Use Virtual WAN Standard SKU to deploy Azure Firewall or partner security solutions in the hub for centralized application-aware filtering and threat protection. Deploy Network Security Groups in spoke networks for subnet-level filtering based on IPs, ports, and protocols.
>
> Each layer addresses different security requirements. Hub security controls enforce organization-wide policies and detect threats across all connected networks. Spoke NSGs provide workload-specific controls that teams configure based on their application requirements.
>
> - **Ensure encrypted connectivity for your connections:** Select strong encryption algorithms like AES-256-GCM or AES-256-CBC to protect data confidentiality and strong integrity algorithms like SHA-256 or SHA-384 to prevent tampering. Align IPsec/IKE encryption policies for Site-to-Site VPN between Azure and on-premises VPN devices to prevent tunnel establishment failures.
>
>    Assess ExpressRoute and Point-to-Site encryption separately. Each connection type requires different encryption configurations.
>
> - **Harden architecture using Zero Trust principles:** Apply Zero Trust principles by assuming breach and limiting blast radius. Use deny-by-default network access controls with explicit permit rules for required traffic. Enable security monitoring for breach detection.
>
> Reduce attack surface by disabling unused features such as:
>
> - Legacy or weak VPN options and cipher suites.
> - Unnecessary Point-to-Site authentication modes.
> - Unneeded public IP assignments or public-facing endpoints.
>
> - **Implement security monitoring and threat detection:** Enable diagnostic logging for Virtual WAN components including hubs, gateways, and Azure Firewall to provide security event visibility. Use Log Analytics workspace to enable centralized log collection, long-term retention, and query-based analysis.
>
>    Configure Azure Monitor alerts to provide reactive notification for specific metric thresholds or log patterns. Deploy Microsoft Sentinel to add proactive threat detection through machine learning and correlation with broader organizational signals.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create [custom route tables](/azure/virtual-wan/scenario-isolate-vnets-custom) matching your defined security zones. Set [route table associations](/azure/virtual-wan/how-to-virtual-hub-routing) on spoke virtual network connections. Set route table propagations to control which route tables receive routes from connections. Use "None" route table association when complete isolation is required with no route sharing. | You can prevent unauthorized traffic between segmented zones through route control. This approach reduces blast radius by limiting communication with compromised resources while enabling granular connectivity control without requiring separate Virtual WAN instances. |
| Create [private endpoints](/azure/virtual-wan/howto-private-link) in spoke virtual networks for PaaS services requiring private connectivity such as Storage, SQL Database, Cosmos DB, and other [supported services](/azure/private-link/private-endpoint-overview).<br><br>Configure private endpoint network integration with spoke virtual network subnets. Route private endpoint traffic through secured virtual hub when inspection is required. | Private endpoints eliminate data exfiltration risks from PaaS services accessible via public internet and reduce attack surface by removing public endpoints. Also enables secure PaaS access from on-premises networks via ExpressRoute or VPN. |
| Enable [DDoS Protection](/azure/ddos-protection/manage-ddos-protection) at the virtual network level or DDoS IP Protection for individual public IPs. Configure protection for public IPs on workload resources.<br><br>Note that DDoS Protection is unavailable for Virtual WAN hub public IPs and must be implemented at spoke level. | DDoS Protection detects and mitigates volumetric attacks that attempt to overwhelm network bandwidth. The service absorbs attack traffic before it reaches your workloads, preventing service outages. DDoS Protection monitors traffic patterns and automatically scales mitigation capacity during attacks. |
| Register the [Azure VPN application](/azure/vpn-gateway/openvpn-azure-ad-tenant) in your Entra ID tenant and configure the VPN Gateway to use Entra ID authentication. Deploy Azure VPN Client to user devices with the Entra ID authentication profile.| Entra ID authentication prevents unauthorized VPN access using compromised credentials through MFA.The configuration enables risk-based authentication adapting to user risk, sign-in risk, and device posture. |
| Create [Conditional Access policies](/azure/vpn-gateway/openvpn-azure-ad-mfa) targeting the Azure VPN application with required controls including MFA, device compliance, and location restrictions. | Conditional Access policies enforce adaptive access controls for VPN connections. You can require MFA for high-risk sign-ins, restrict access to compliant devices, and limit connections from trusted locations only, reducing the likelihood of unauthorized access. |
| Assign [Network Contributor role](/azure/virtual-wan/roles-permissions) for full management permissions and Reader role for view-only access to configurations. Create custom RBAC roles when built-in roles exceed required permissions. | RBAC assignments prevent unauthorized configuration changes through permission limits and reduce impact of compromised accounts through least privilege assignments. The approach enables separation of duties with role-based job functions. |
| Select Virtual WAN Standard SKU to enable security solution deployment. Deploy [Azure Firewall](/azure/firewall/overview) or partner security solution in virtual hub for centralized filtering. Enable [Routing Intent](/azure/virtual-wan/how-to-routing-policies) to automatically route traffic through the security solution and configure internet traffic routing intent for outbound traffic inspection. | Defense-in-depth architecture delivers traffic filtering through centralized hub security and distributed spoke controls. This approach enables workload-specific controls via spoke NSGs while maintaining hub policies and provides flexibility for implementing controls at appropriate architectural layer. |
| Implement [Network Security Groups](/azure/virtual-network/network-security-groups-overview) on subnets in spoke virtual networks for subnet-level filtering. Configure hub security solution for application and threat inspection while configuring spoke NSGs for network-level filtering based on IPs, ports, and protocols. | Spoke NSGs provide granular workload-specific controls complementing hub security policies. This layered approach enables explicit permit rules for required traffic while enforcing deny-by-default posture, reducing attack surface and limiting lateral movement in case of compromise. |
| Configure [diagnostic settings](/azure/virtual-wan/monitor-virtual-wan) on Virtual WAN resource, virtual hubs, VPN gateways, ExpressRoute gateways, P2S gateways, and Azure Firewall. Enable log categories including AllMetrics, GatewayDiagnosticLog, TunnelDiagnosticLog, RouteDiagnosticLog, and IKEDiagnosticLog.<br><br>Route logs to Log Analytics workspace for centralized collection and build queries targeting security-relevant events such as authentication failures, connection anomalies, and configuration changes. | Diagnostic logging enables security event detection through log analysis of suspicious activities and anomalous behaviors. The configuration supports compliance and audit requirements through long-term event retention while providing forensic evidence for security incident investigation. |
| Create [metric alerts](/azure/virtual-wan/monitor-virtual-wan) for security-relevant metrics including VPN Gateway connection status, ExpressRoute circuit status, BGP peer status, and bandwidth utilization anomalies. Set alert thresholds based on established baseline metrics for normal operations.<br><br>Configure action groups routing alerts to security operations or automated response systems. | Metric alerts enable rapid detection of connectivity failures indicating security issues or denial of service. The configuration identifies bandwidth anomalies indicating data exfiltration or unexpected traffic patterns.<br><br>Proactive notification of BGP routing issues enables early detection of problems impacting segmentation or security controls. |
| Deploy [Microsoft Sentinel](/azure/sentinel/data-connectors/azure-firewall) connected to the Log Analytics workspace receiving Virtual WAN logs. Enable [Azure Firewall data connector](/azure/sentinel/overview) in Sentinel.<br><br>Implement analytics rules for Virtual WAN threat scenarios such as anomalous VPN authentication patterns, unusual traffic volumes, and firewall policy violations. Create incident response playbooks for automated actions like blocking suspicious source IPs and disabling compromised VPN users. | Sentinel integration enables advanced threat detection using machine learning and behavioral analytics. The platform correlates Virtual WAN security events with broader organizational security telemetry while providing security operations center with unified monitoring platform. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Azure Virtual WAN and its environment.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Understand pricing structures to create accurate cost models:** Virtual WAN pricing has fixed and variable components. Fixed costs include SKU selection and allocated scale and connection units. Variable costs include data transfer charges and active features. Site-to-Site VPN scale units measure at 500 Mbps per unit while ExpressRoute scale units measure at 2 Gbps per unit.
>
>    Data transfer costs represent significant variable expenses. Hub-to-hub transfers across regions incur cross-region charges, while hub router processing contributes to Hub Data Processing costs. Virtual WAN combines fixed infrastructure and variable usage charges, making accurate pre-deployment estimation essential.
>
> - **Monitor costs and hub utilization for ongoing optimization:** Set up daily cost tracking to stay on top of your Virtual WAN spending. Focus on three main expenses: Hub Data Processing from your traffic flow, gateway connection usage, and data moving between hubs in different regions.
>
>    Organize your resources by business unit or application so you can see exactly where costs originate. This visibility helps you implement chargeback or showback reporting. Watch your spending against budgets to catch unexpected increases early. This early warning helps you investigate usage patterns and take action when costs behave unexpectedly.
>
> - **Establish spending guardrails with budgets and alerts for your deployments:** Implement budgets and alerts to prevent unexpected spending. Set graduated alert thresholds for early warning before budget exhaustion, enabling timely investigation and stakeholder escalation through multiple threshold levels.
>
>    Track metrics that directly affect your variable costs. Hub router traffic volume shows your Hub Data Processing charges, while connection unit usage shows your capacity costs. Monitoring these helps you understand what drives spending increases.
>
>    Apply governance policies to prevent unauthorized cost increases. Use Azure Policy to require approvals for high-cost changes, protect budget-critical settings from modification, and mandate reviews before adding capacity.
>
> - **Right-size infrastructure capacity based on actual workload requirements:** Routing infrastructure units control hub capacity and throughput. Each unit increases both your VM capacity and your costs. Size units based on what your workload actually needs, not maximum theoretical capacity. Too much capacity wastes money on unused resources. Too few units hurt performance and force disruptive scaling operations.
>
>    Each hub has fixed monthly costs even with no traffic. Add hubs only where connectivity needs require them. Gateway scale units add VPN and ExpressRoute capacity at incremental cost. Setting scale units too high wastes budget. Setting them too low slows connections. Track actual usage and adjust capacity when patterns show you need changes.
>
> - **Choose cost-effective connection types and integrated service SKUs for Virtual WAN:** Site-to-Site VPN provides cost-effective connectivity for lower bandwidth and non-critical connections, while ExpressRoute delivers higher performance at substantially higher cost. Point-to-Site VPN supports remote users with different cost characteristics.
>
>    Development and test environments benefit from VPN's lower cost and variable performance. Production workloads requiring consistent bandwidth and work best with ExpressRoute's reliability despite higher cost. Hybrid approaches let you route routine traffic through VPN while directing business-critical applications through ExpressRoute, balancing cost against risk.
>
> - **Optimize Virtual WAN routing to minimize data transfer costs:** Traffic flow patterns directly impact variable costs. Hub-to-hub communication across regions generates cross-region charges, while hub router processing contributes to Hub Data Processing costs. Data transfer costs vary by volume and network path.
>
>    Optimize your routing topology to reduce costs without sacrificing connectivity. Spoke-to-spoke communication through the hub incurs processing charges, but direct VNet peering bypasses the hub entirely and eliminates those costs. Hub location determines traffic flow. Poor placement forces more traffic across regions, increasing transfer charges. Evaluate your dominant traffic patterns and adjust your topology to route efficiently, balancing cost optimization against your security and routing policy requirements.
>
> - **Develop cost-effective scaling strategy for Virtual WAN growth:** Infrastructure costs increase proportionally with network growth. Unplanned scaling leads to unexpected costs without business value, making strategic capacity planning essential for balancing performance with cost considerations.
>
>    Hub placement decisions impact both latency and costs. Proximity to user bases reduces latency but excessive hub count increases costs without proportional benefit. Regional consolidation must balance against acceptable latency thresholds to achieve cost efficiency without sacrificing experience.
>
>    Connection unit allocation requires ongoing optimization as each unit incurs incremental costs. Provisioning should reflect actual bandwidth requirements rather than theoretical capacity, and utilization monitoring reveals optimization opportunities as demand fluctuates.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) to model deployment costs before implementation by configuring expected hubs across regions, selecting connection types with appropriate scale units, and estimating monthly data transfer volumes.<br><br>Estimate bandwidth requirements to determine gateway scale units, VPN gateways provide 500 Mbps per unit while ExpressRoute delivers 2 Gbps per unit. Calculate minimum hub capacity in routing infrastructure units based on anticipated VM workload and throughput needs. | Accurate cost estimates support budget planning and approval while enabling informed decision-making about Virtual WAN configuration. You can anticipate variable costs from data transfer and hub processing before deployment begins. |
| Apply [tags to Virtual WAN hubs](/azure/azure-resource-manager/management/tag-resources), VPN gateways, ExpressRoute gateways, and connections using consistent naming conventions for business unit, application name, environment, or cost center. Use Azure Policy to enforce mandatory tags on resource creation.<br><br>Configure Cost Management reports to aggregate and filter costs by tag values, then use tagged cost data for chargeback or showback to business units. | Accurate cost allocation to business units or applications provides visibility into cost drivers by team or workload. This approach supports chargeback models for internal cost recovery and departmental accountability. |
| Configure [Azure Monitor](/azure/virtual-wan/monitor-virtual-wan) to collect hub router traffic volume metrics continuously, then create workbooks or dashboards visualizing trends over time. Establish baseline traffic patterns to identify normal versus anomalous behavior.<br><br>Use traffic volume data to identify optimization opportunities by detecting high-volume VNet-to-VNet communication suitable for direct peering bypass, identifying unexpected spikes requiring investigation, and analyzing routing patterns to optimize hub-mediated flows. | Visibility into the primary variable cost driver for Virtual WAN enables data-driven optimization decisions based on actual traffic patterns. You can perform cost forecasting through traffic trend tracking. |
| Create [Azure Cost Management budget](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) scoped to Virtual WAN resource group or subscription aligned with cost model projections. Configure alert thresholds at 90%, 100%, and 110%, then assign stakeholder email addresses or action groups to receive alerts at each threshold.<br><br>Configure metric alerts on hub router traffic volume when approaching levels that correlate with budget concerns. Analyze historical patterns to establish normal baseline and set alert thresholds before traffic reaches budget-critical levels. | Proactive notification prevents unexpected cost overruns and enables timely intervention before costs exceed budget. Enables gaining greater visibility into Virtual WAN spending patterns for better cost management. |
| Set virtual hub routing infrastructure units to minimum level adequate for current workload, starting conservatively with baseline capacity rather than anticipated peak. Each unit supports specific VM count and [throughput capacity](/azure/virtual-wan/hub-settings#capacity).<br><br>Set VPN [gateway scale units](/azure/virtual-wan/gateway-settings) to match anticipated site-to-site throughput with each unit providing 500 Mbps capacity. Set ExpressRoute gateway scale units to match connection needs with each unit delivering 2 Gbps bandwidth.<br><br>Schedule capacity adjustments during planned maintenance windows, scaling up when utilization monitoring shows approaching limits or scaling down if monitoring indicates consistently low utilization. | Hub costs become optimized by aligning capacity with actual workload, avoiding over-provisioning waste from excessive unit allocation. Enabling you to maintain adequate capacity for performance requirements without excessive spending. |
| Assess connectivity requirements for each site, branch, or connection endpoint to determine appropriate connection types based on workload criticality and performance needs.<br><br>Configure [Site-to-Site VPN connections](/azure/virtual-wan/virtual-wan-site-to-site-portal) for development environments, testing workloads, non-production scenarios, backup connectivity paths, or moderate bandwidth requirements not requiring guaranteed SLAs.<br><br>Configure [ExpressRoute connections](/azure/virtual-wan/virtual-wan-expressroute-portal) for mission-critical production workloads requiring guaranteed bandwidth. Implement hybrid connectivity using VPN and ExpressRoute in combination, routing general connectivity through VPN while directing critical application traffic through ExpressRoute. | Connectivity costs reduce through appropriate use of lower-cost VPN options while maintaining performance and reliability for critical workloads. Workloads can achieve cost-effective scaling by adding VPN connections for non-critical growth. |
| Assess whether specific VNet-to-VNet traffic flows require hub-mediated routing by determining if centralized routing policy enforcement is necessary or if Azure Firewall or Network Virtual Appliance inspection is required.<br><br>Configure [direct VNet peering](/azure/virtual-network/virtual-network-peering-overview) between VNets with high-volume communication that doesn't require hub processing. Create peering connections between source and destination VNets, update route tables to use peering instead of hub router, and verify connectivity through the direct path. | Hub Data Processing costs reduce by eliminating the hub router from VNet-to-VNet traffic paths, decreasing overall Virtual WAN variable costs through selective direct connectivity. Direct connectivity improves network latency for peered VNets while achieving cost savings. |

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Azure Virtual WAN.

> [!div class="checklist"]
>
> - **Implement Infrastructure as Code for consistent deployments:** Infrastructure as Code ensures consistent, repeatable Virtual WAN deployments across environments. ARM templates and Bicep provide native Azure integration with the simplest deployment path. Terraform supports multi-cloud scenarios when your infrastructure spans multiple providers.
>
>    Modular design enables efficient management by separating infrastructure concerns. Core hub infrastructure changes infrequently, while connection policies and routing rules change more often. Separating these into modules lets you update what changes without redeploying stable components. Parameterization handles environment differences, such as dev using smaller scale units than production, without duplicating code.
>
>    Virtual WAN deployment requires orchestration because resources have dependencies. Gateways require hubs, connections require gateways, and routing policies require connections. Your IaC must sequence operations correctly and handle asynchronous behavior. Routing propagates across the global network over time, not instantly. Build wait conditions into your automation to verify propagation before marking deployment complete.
>
> - **Establish build and deployment pipelines for automation:** Deployment pipelines require careful design with stage separation for validation, approval, and deployment. Quality gates between stages act as checkpoints, blocking problematic changes before they advance. This staged approach prevents configuration errors from reaching production environments.
>
>    Define validation strategies appropriate for networking infrastructure including static validation for syntax checking, policy compliance, and security rule verification as well as pre-deployment testing for configuration validation against organizational standards.
>
> - **Implement monitoring for your topology, services, and dependencies:** Effective monitoring requires complete visibility across three layers: topology, service health, and operational telemetry. This includes global hub distribution and spoke connectivity, service health tracking for gateway operational status, and dependency tracking.
>
>    Define metric collection strategy including gateway performance metrics for throughput utilization and connection counts, connection reliability metrics for tunnel availability, and routing health metrics for route propagation status.
>
>    Design operational visibility with role-based views where operations teams can access real-time health while architects can identify capacity trends. Consider retention strategy that balances investigation capabilities with storage costs.
>
> - **Configure alerting for connectivity and performance issues:** Design your alerting strategy around failure scenarios that impact operations: connectivity failures that break user access, performance degradation that slows applications, and configuration anomalies that indicate mistakes. Target specific measurable conditions like tunnel disconnections, gateway CPU saturation above safe thresholds, and routing changes that differ from expected patterns. Specific conditions produce actionable alerts.
>
>    Define alert threshold philosophy that balances early detection with alert fatigue by avoiding overly sensitive thresholds that generate false positives.
>
>    Establish notification routing and escalation paths that match alert severity with appropriate notification urgency. Integrate with incident management platforms for tracking and escalation workflows.
>
> - **Automate operational tasks and connectivity validation:** Automate Virtual WAN operational tasks like connection status validation, error-prone processes like multi-hub configuration updates, and time-sensitive operations.
>
>    Design automation architecture that balances control with flexibility through scheduled automation for routine health checks, event-driven automation for configuration drift remediation, and on-demand automation for ad-hoc validation scripts and troubleshooting tools.
>
>    Leverage Virtual WAN native automation capabilities to reduce custom implementation overhead, taking advantage of automatic route advertisement and propagation across hub infrastructure plus gateway auto-scaling that responds to load patterns without manual intervention.
>
> - **Establish safe deployment practices with customer-controlled maintenance:** Progressive deployment limits blast radius by catching problems in non-production environments before they affect users. Deploy to development first, then staging, validating at each step. Run static configuration validation and policy compliance checks before deployment executes. Follow deployment with smoke tests verifying gateway reachability and connection establishment to confirm basic functionality.
>
>    Establish rollback capability for rapid recovery through configuration versioning in the IaC repository plus documented rollback procedures for reverting gateway settings and routing policies.
>
>    Separate routine deployments from emergencies. Routine changes go through full validation with approvals at each stage. Emergencies skip extended approvals but still validate syntax and run focused tests. This balance maintains safety without delaying critical fixes.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [ARM templates](/azure/templates/microsoft.network/virtualwans) or [Bicep](/azure/azure-resource-manager/bicep/overview) for Virtual WAN infrastructure deployment. Define modular templates separating resource types including hubs, VPN and ExpressRoute gateways, connections, and routing policies.<br><br>Parameterize environment-specific values such as hub address spaces, gateway scale units, connection settings, and routing preferences. Deploy templates through Azure CLI or Azure PowerShell with environment-specific parameter files. Store templates in version control with CI/CD pipeline integration. | Declarative syntax ensures consistent configuration, eliminating manual errors. Version-controlled templates track changes, support audit requirements, and enable rollback. Parameterized templates deploy identical architectures across environments. |
| Configure a CI/CD pipeline using [Azure DevOps Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) or [GitHub Actions](/azure/developer/github/github-actions) for Virtual WAN deployment automation. Define a multi-stage pipeline with separated stages for validation, development deployment, and production deployment.<br><br>Configure the validation stage with ARM template validation, Azure Policy compliance checks, and security scanning. Add approval gates requiring manual approval before production deployment. Configure deployment stages with environment-specific parameters and service connections. | Automated deployment reduces manual effort and eliminates human error. Multi-stage workflow with approval gates prevents problematic changes from reaching production. Deployment history supports compliance and facilitates incident investigation. |
| Enable [Azure Monitor Insights](/azure/virtual-wan/azure-monitor-insights) for Virtual WAN to access integrated topology visualization and [metrics workbooks](/azure/virtual-wan/monitor-virtual-wan). Configure metric collection for gateway throughput, connection counts, BGP peer status, and routing table health.<br><br>Set the metric collection interval and aggregation granularity based on monitoring requirements. For long-term analysis, export metrics to a Log Analytics workspace for retention beyond the default 93-day period. | Centralized topology view provides single-pane visibility across regions and hubs. Service-level metrics track gateway performance, connection health, and routing status. Built-in dashboards deliver immediate value without custom development.<br><br>Integrated visualization supports rapid diagnosis while historical data enables capacity planning based on actual usage patterns. |
| Enable [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) for Virtual WAN hub, VPN gateway, and ExpressRoute gateway resources. Configure log collection for GatewayDiagnosticLog, TunnelDiagnosticLog, RouteDiagnosticLog, and IKEDiagnosticLog categories. Send logs to a Log Analytics workspace for centralized analysis.<br><br>Create operational dashboards using [Azure Dashboard](/azure/azure-monitor/visualize/tutorial-logs-dashboards) or [Azure Workbooks](/azure/azure-monitor/visualize/workbooks-overview) to visualize gateway status, connection counts, error trends, and throughput metrics. Configure dashboard refresh intervals and time ranges for operational monitoring needs. | Detailed telemetry enables effective troubleshooting. Logs capture gateway events, tunnel establishment, routing changes, and state transitions for comprehensive operational visibility.<br><br>Log Analytics enables correlation across resources and time ranges. Consolidated dashboards provide at-a-glance health status. Logs support audit and compliance requirements through change documentation. |
| Configure [metric alert rules](/azure/azure-monitor/alerts/alerts-create-metric-alert-rule) for Virtual WAN gateway and hub resources. Set alert conditions for VPN Gateway metrics including TunnelEgressPacketDropCount, TunnelIngressPacketDropCount, and TunnelBandwidth thresholds. Set ExpressRoute Gateway alerts for BitsInPerSecond and BitsOutPerSecond approaching circuit capacity.<br><br>Configure Virtual WAN Hub alerts for PacketCount drops and RoutingState changes indicating routing propagation issues. Create [action groups](/azure/azure-monitor/alerts/action-groups) specifying notification methods such as email, SMS, or webhook with recipients based on alert severity.<br><br>Set alert evaluation frequency and lookback window balancing detection latency with alert accuracy. This prevents excessive false positives while maintaining timely issue detection. | Rapid detection minimizes business impact from connectivity or performance issues. Gateway health alerts provide early warning before critical failures.<br><br>Historical alert data supports root cause analysis while alert trends enable proactive capacity planning and issue prevention. |
| Configure [Network Watcher Connection Monitor](/azure/network-watcher/connection-monitor-overview) for automated connectivity testing across Virtual WAN infrastructure. Define test groups with source and destination endpoints covering hub-to-hub, hub-to-spoke, and on-premises paths.<br><br>Configure test parameters including protocol (TCP or ICMP), port numbers, probe frequency, and success thresholds. Set alert thresholds for test failures and latency exceeding acceptable limits. Integrate results with operational dashboards for continuous connectivity visibility. | Continuous validation detects connectivity issues before user impact. Automated testing validates all paths, identifying failures immediately.<br><br>End-to-end testing verifies complete connectivity including routing and network paths. This reduces mean time to detection compared to reactive user reporting. |
| Configure [Azure Automation](/azure/automation/automation-intro) runbooks for repetitive Virtual WAN tasks. Examples include gateway scaling automation that monitors capacity metrics and updates configuration, VPN site management for bulk operations, and health validation that checks gateway status and routing tables.<br><br>Configure runbook execution through scheduled triggers for routine tasks or event-driven triggers based on Azure Monitor alerts. This enables both proactive maintenance and reactive responses to operational events.<br><br>Configure [Azure Policy](/azure/governance/policy/concepts/effects) for drift detection and automatic remediation. Define policies validating gateway SKU compliance, diagnostic settings, and network security rules. Enable automatic remediation using DeployIfNotExists or Modify effects. | Automation eliminates time-consuming tasks, freeing teams for higher-value activities. Consistent automation applies standards uniformly across hubs and gateways.<br><br>Drift remediation maintains desired state while automated execution reduces misconfiguration risk from manual operations. |
| Configure [customer-controlled maintenance](/azure/virtual-wan/customer-controlled-gateway-maintenance) windows for VPN and ExpressRoute gateways. Define a maintenance schedule including day of week, start time, and duration aligned with low-traffic periods. Apply schedules to gateways individually or stagger across hubs.<br><br>Configure phased deployment in your [CI/CD pipeline](/azure/architecture/framework/devops/release-engineering-cd). Deploy to development and test environments first with connectivity validation. Deploy to the first production hub with a monitoring period before expanding to remaining hubs.<br><br>Configure rollback capability through IaC version control. Maintain previous configuration versions with rollback tags in your Git repository. Deploy rollbacks using previous ARM template versions and parameter files. Consider blue-green deployment patterns for instant traffic shifting. | Predictable maintenance timing reduces unplanned disruption risk. Scheduling maintenance during planned periods enables coordination with application teams.<br><br>Phased rollout detects issues in development before full production impact. Automated rollback enables immediate recovery while blue-green patterns support instant traffic shifting. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Virtual WAN.

> [!div class="checklist"]
>
> - **Conduct capacity planning for your components:** Plan your Virtual WAN capacity by assessing your VPN and ExpressRoute gateways, hub routers, and routing infrastructure. Start by estimating VPN gateway throughput per tunnel because all your active tunnels share the total gateway capacity.
>
>    Monitor your current utilization across gateways and hub routers to establish performance baselines. Use these baselines to project future capacity needs accurately. Your planning should cover peak traffic scenarios, expected business growth, and any new site connections you anticipate adding.
>
> - **Implement performance monitoring:** Implement performance monitoring for every Virtual WAN component to maintain optimal operation. Establish a performance baseline during normal operations so you understand typical behavior patterns and can recognize when something changes.
>
>    Monitoring data supports capacity planning initiatives and identifies optimization opportunities before performance degradation occurs. Tracking performance trends over time enables you to predict future requirements and plan infrastructure changes proactively.
>
> - **Design for scalable architecture:** Plan for horizontal scaling by adding hubs within regions when requirements exceed per-hub limits. Design routing infrastructure to support anticipated inter-hub and spoke-to-spoke traffic volumes while balancing performance requirements with cost considerations.
>
>    Validate scaling effectiveness through performance monitoring and capacity utilization analysis to ensure the architecture meets business needs as requirements evolve.
>
> - **Establish performance testing practices:** Performance testing practices require deploying non-production Virtual WAN environments that match production configuration for validation. Test routing configurations, connectivity scenarios, and feature enablement before production deployment to ensure optimal performance.
>
>    Testing must validate performance for branch-to-Azure, branch-to-branch, and VNet-to-VNet traffic patterns while evaluating gateway performance under peak traffic and concurrent connection loads. Comparing test results against performance targets and baselines before implementing changes prevents performance issues in production environments.
>
> - **Optimize hub placement for minimal latency:** Hub placement optimization requires positioning hubs in regions close to user populations and branch office locations to minimize network latency. Optimize hub placement for dominant traffic patterns such as branch-to-Azure, branch-to-branch, or VNet-to-VNet connectivity while considering cross-region traffic requirements and inter-hub connectivity performance.
>
> - **Optimize your configurations for performance:** Evaluate gateway sizing strategies by assessing VPN and ExpressRoute gateway capacity against bandwidth requirements and traffic projections. Account for peak traffic scenarios and expected business growth when determining appropriate gateway SKUs.
>
>    Select IPSec encryption algorithms that balance security requirements with gateway CPU efficiency. Design routing path preferences to optimize business-critical traffic patterns taking into account the performance characteristics of different routing options such as hub-to-hub versus ExpressRoute paths for VNet-to-VNet scenarios.
>
> - **Validate routing for performance troubleshooting:** Routing validation establishes systematic practices for performance troubleshooting by designing operational procedures to verify route learning and propagation across hybrid network components. Document troubleshooting workflows for routing-related performance issues and apply learnings from routing incidents to improve monitoring and preventive measures.
>
> - **Continuously monitor and optimize your capacity:** Continuous capacity optimization establishes proactive practices to maintain performance as requirements evolve. Design a proactive capacity management approach with regular performance reviews and plan capacity adjustments before utilization reaches limits to avoid performance impact. This approach aligns capacity optimization with business growth and changing connectivity patterns.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Enable native [Azure Monitor metrics](/azure/virtual-wan/monitor-virtual-wan-reference) for hub router, ExpressRoute Gateway, and VPN Gateway components. Configure collection of throughput metrics, connection health indicators, bandwidth utilization, packet loss rates, and latency measurements with appropriate retention periods.<br><br>Set metric retention to support historical trend analysis and capacity planning requirements. Configure alert rules for critical performance thresholds including gateway throughput limits and hub capacity constraints. | Identifies performance bottlenecks before user impact occurs and supports capacity planning with historical data. Simplifies monitoring through native Azure Monitor integration while providing visibility into Virtual WAN component performance. |
| Configure [routing infrastructure units](/azure/virtual-wan/hub-settings) at hub deployment based on calculated inter-hub and spoke-to-spoke throughput requirements. Deploy multiple hubs within the same region when traffic volumes exceed single hub limits or when different routing configurations are needed. | Prevents bottlenecks in inter-hub and spoke-to-spoke connectivity while supporting expected traffic volumes without over-provisioning. Enables cost-effective performance with right-sized infrastructure that scales with your workload's needs. |
| Identify geographic distribution of users, branch offices, and on-premises data centers to position hubs optimally for highest-volume and most critical traffic patterns. Test network path latency to validate region selection before deployment and evaluate multi-region deployment when populations span large geographic areas. | Minimizes network latency for end users and applications while improving application performance and user experience. Decreases network round-trip times for critical workloads that depend on low-latency connectivity. |
| Configure [hub routing preference](/azure/virtual-wan/about-virtual-hub-routing-preference) setting to "ExpressRoute", "VPN Gateway", or "AS Path" based on primary connectivity type and performance requirements for spoke-to-spoke traffic. Select "ExpressRoute" preference when ExpressRoute circuits provide lower latency paths between spoke VNets.<br><br>Test selected routing preference in non-production environment before production deployment. | Reduces latency for critical connectivity scenarios while enabling efficient traffic path selection. Improves throughput efficiency for inter-hub and spoke traffic through optimized routing decisions. |
| Configure GCMAES256 algorithm for both IPSec encryption and IPSec integrity parameters on site-to-site VPN connection [custom IPSec policies](/azure/vpn-gateway/vpn-gateway-about-compliance-crypto). Apply custom IPSec policy with GCMAES256 settings to VPN connections during connection creation. | Reduces CPU overhead on VPN gateways while enabling higher throughput on VPN site-to-site connections. Maintains security while maximizing gateway capacity utilization for improved performance. |
| Configure monitoring to check [virtual hub effective routes](/azure/virtual-wan/effective-routes-virtual-hub) in hub routing tables, verifying expected routes are present and correctly learned from connected resources. Monitor VM NIC effective routes to confirm virtual machines receive correct routes from Virtual WAN hubs.<br><br>Verify route advertisements from on-premises BGP routers reach Virtual WAN hubs with expected metrics. Configure Azure Monitor alerts for routing anomalies including missing expected routes or route metric changes. | Identifies routing problems before they impact users while reducing mean time to resolution for connectivity and performance issues. Prevents performance degradation from routing misconfigurations through proactive monitoring. |
| Configure monitoring for VPN Gateway throughput metrics and ExpressRoute Gateway throughput metrics, tracking utilization against gateway SKU capacity limits. Monitor connection counts, packet rates per second, and active tunnel metrics to identify capacity constraints.<br><br>Establish utilization threshold alerts at 70% capacity to trigger gateway resizing evaluation. Resize gateway SKUs when sustained utilization exceeds 80% capacity or during performance degradation. | Proactive capacity management keeps your network performing well as usage grows. You maintain consistent connection speeds and application response times as traffic patterns change over time. Early detection at 70% capacity gives you time to plan gateway upgrades during scheduled maintenance windows. This prevents sudden performance drops that force emergency changes and disrupt users. Monitoring connection counts and packet rates helps you spot capacity trends before they become problems. |

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Virtual WAN and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Virtual hubs are protected with Azure Firewall for centralized security inspection.
- VPN gateways avoid using Basic SKU which lacks reliability and performance features.
- Point-to-site VPN connections use Azure Active Directory authentication for enhanced security.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Virtual WAN](/azure/governance/policy/samples/built-in-policies#network) and other policies that might affect the security of the networking infrastructure.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Hub-spoke network topology with Azure Virtual WAN](/azure/architecture/networking/architecture/hub-spoke-virtual-wan-architecture).

## Related content

- [Virtual WAN FAQ](/azure/virtual-wan/virtual-wan-faq)
- [Introduction to Azure Virtual WAN - Training](/training/modules/introduction-azure-virtual-wan/)
- [Virtual WAN partners, regions, and locations](/azure/virtual-wan/virtual-wan-locations-partners)
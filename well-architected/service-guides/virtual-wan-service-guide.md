---
title: Azure Virtual WAN service guide
description: Architectural recommendations for Azure Virtual WAN mapped to the Well-Architected Framework pillars.
author: josevarela
ms.author: josev
ms.topic: conceptual
ms.date: 11/13/2025
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

# Azure Virtual WAN service guide

Azure Virtual WAN is a networking service that provides unified connectivity, security, and routing. It enables global branch and user connectivity through VPN and ExpressRoute, supports encrypted private links, and integrates Azure Firewall for protection. Virtual WAN uses a hub-and-spoke architecture with scale and performance for global transit networks.

This guide provides architectural recommendations for Azure Virtual WAN mapped to the [Well-Architected Framework pillars](/azure/well-architected/pillars).

<!-- markdownlint-disable-next-line MD036 -->
**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Virtual WAN
- Azure VPN Gateway

## Reliability

The Reliability pillar provides continued functionality by **building resilience and recovering from failures**.

The [Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the nature of your application and the criticality of its components. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - **Review service limits and known issues:** Review [Azure Virtual WAN limits](/azure/virtual-wan/virtual-wan-faq) and [known issues](/azure/virtual-wan/virtual-wan-faq#known-issues) before planning your architecture. Virtual WAN enforces limits on connected resources per hub and per subscription. Known issues include available workarounds.
>
>    Choose Virtual WAN [Standard SKU](/azure/virtual-wan/virtual-wan-about#basicstandard) for production deployments to access zone redundancy and advanced routing capabilities.
>
> - **Anticipate potential failures through failure mode analysis:** Failure mode analysis helps you anticipate failure scenarios and develop mitigation strategies.
>
>    | Failure | Mitigation |
>    |---------|------------|
>    | Region outage affecting a single-region Virtual WAN hub | Deploy multiple hubs across regions to maintain connectivity when one region becomes unavailable. |
>    | ExpressRoute connection failure | Deploy redundant circuits or backup VPN connections to maintain hybrid connectivity during outages. |
>    | Branch site VPN connection failure | Provision site-to-site VPN with redundant ISP links to protect against provider-level connectivity issues. |
>
> - **Understand SLA and composite reliability targets:** Virtual WAN hub routing infrastructure has a 99.95% SLA. Downtime means you can't establish or maintain connections. See SLA terms for service credits.
>
>    End-to-end connectivity requires composite SLA calculation. Hubs, gateways, and firewalls each have independent SLAs. Calculate the combined SLA for critical connectivity paths.
>
>    The SLA excludes customer responsibilities: on-premises equipment failures, internet connectivity issues outside Azure's control, and configuration errors.
>
> - **Plan multi-region redundancy for hub deployment:** Distribute virtual hubs across multiple Azure regions to achieve region-level redundancy.
>
>   Region selection should prioritize:
>
>   - Proximity to users and workloads.
>   - Paired regions for disaster recovery.
>   - Cross-region connectivity requirements.
>   - Regulatory and data residency constraints.
>
>    Configure routing to support automatic or manual region failover. Plan for asymmetric routing patterns during failures and document expected outage behavior. Regulatory and data residency requirements might constrain multi-region deployment strategies.
>
> - **Plan for layered redundancy:** Virtual WAN supports three redundancy layers, each addressing distinct failure scenarios:
>
>     | Layer | Protection Against |
>     |-------|-------------------|
>     | Zone-level | Datacenter failures within a region. |
>     | Connection-level | Single path failures. |
>     | Route-level | Enables traffic engineering and failover control. |
>
>     Balance redundancy decisions against availability needs, implementation complexity, and cost. Evaluate each failure domain's impact on workload operations. Choose routing patterns (symmetric or asymmetric) that match operational capabilities.
>
> - **Plan for reliable hub and gateway scaling:** Size infrastructure for expected growth rather than current traffic. Hub routers autoscale but require up to 25 minutes. Gateways require manual sizing and scheduled maintenance for changes. Overprovision capacity to maintain reliability during demand spikes.
>
> - **Design resilience for Point-to-Site VPN:** Enable global VPN profile for automatic hub selection and failover. Size Point-to-Site address pools to handle at least twice expected concurrent users to prevent exhaustion during gateway redistribution.
>
> - **Configure VPN backup for failover:** Deploy VPN gateway alongside ExpressRoute gateway. Set BGP preference to favor ExpressRoute over VPN for automatic failover.
>
> - **Validate failover behavior:** Test routing and NVA behavior during failover scenarios. Document expected traffic flows and client impact for each failover pattern.
>
> - **Incorporate business continuity testing:** Test your failover configuration to avoid surprises during actual outages.
>
>   Testing should simulate:
>
>   - Region failures to verify multi-region failover behavior.
>   - ExpressRoute circuit failures to confirm VPN backup activation.
>   - Parallel path scenarios to identify asymmetric routing patterns.
>   - BGP route advertisement during failover events.
>
> - **Configure monitoring and alerting for service health:** Set up monitoring for hub routers, gateways, and routing infrastructure to detect issues before users report outages. Configure alerts for performance degradation for early response.
>
>    Enable diagnostic logs across all Virtual WAN components to support troubleshooting through event correlation. Resource Health distinguishes Azure service issues from customer-side connectivity problems.

### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Choose [Virtual WAN Standard SKU](/azure/virtual-wan/virtual-wan-about#basicstandard) for production deployments to access critical reliability capabilities. Standard SKU provides zone redundancy, supports advanced routing scenarios, and enables hub-to-hub and branch-to-branch connectivity patterns. | Enables zone-redundant gateway deployment across availability zones for datacenter-level fault tolerance. Supports global transit architecture for multi-region connectivity patterns.|
| Select [Network Virtual Appliances](/azure/virtual-wan/about-nva-hub) with native Virtual WAN hub integration. Native integration provides better reliability and support while avoiding complex custom routing configurations. | Eliminates user-defined route maintenance overhead. Provides combined Microsoft and partner support for integrated solutions. |
| Deploy virtual hubs in at least two [Azure regions](/azure/virtual-wan/virtual-wan-global-transit-network-architecture) to establish redundancy. Choose regions close to users and workloads. Consider Azure paired regions for enhanced disaster recovery capabilities. | Maintains network operations during complete region failures. Preserves branch and virtual network connectivity through alternate region hubs. Enables workload distribution for load balancing. |
| Configure VPN gateways with the [zone-redundant SKU](/azure/vpn-gateway/about-zone-redundant-vnet-gateways) to enable automatic distribution across availability zones. Verify the target region supports availability zones and confirm successful zone distribution through gateway properties. | Maintains VPN tunnel availability during availability zone failures. Eliminates single datacenter dependency through automatic instance distribution. |
| Configure [Site-to-Site VPN tunnels](/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) in active-active configuration by creating separate tunnels with identical settings to both VPN gateway instances. Enable BGP peering on both tunnels for dynamic routing and immediate failover without convergence delay. | Eliminates convergence delays during failover events. Provides instant traffic routing through alternate tunnel when primary fails. Improves throughput compared to active-passive deployments. |
| Configure active-active route advertisement by advertising routes from on-premises to Azure across all [ExpressRoute circuits](/azure/expressroute/designing-for-high-availability-with-expressroute). Use BGP AS path prepending for traffic engineering when needed. Monitor route advertisement status on all connections. | Maximizes ExpressRoute redundancy and bandwidth through automatic failover. Enables traffic distribution control via AS path prepending. Optimizes network utilization through active-active forwarding. |
| Configure virtual [hub routing infrastructure units](/azure/virtual-wan/hub-settings) above baseline requirements to accommodate anticipated growth. Set units based on expected traffic and growth projections. Monitor routing capacity utilization metrics to identify scaling needs and prevent performance degradation as traffic increases. | Avoids routing performance bottlenecks when traffic exceeds baseline capacity. Eliminates 25-minute scaling delay impact on active connections. Supports workload expansion without service disruption. |
| Configure VPN and ExpressRoute [gateway scale units](/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings) for expected aggregate throughput with overprovisioning for future growth. Size gateway scale units based on throughput requirements and growth projections. Gateways don't autoscale and scale unit changes require maintenance windows. | Prevents capacity bottlenecks and connectivity degradation from gateway exhaustion. Avoids packet loss when traffic exceeds capacity. Reduces operational overhead by minimizing gateway resize operations. |
| Configure a [global VPN profile](/azure/virtual-wan/global-hub-profile) across all hubs to enable automatic hub selection for Point-to-Site connections. The global profile directs VPN clients to the nearest available hub and provides resilience when the primary hub becomes unavailable.| Ensures remote user connectivity during hub outages through automatic failover. Connects users to nearest hub for optimal latency. Maintains productivity during regional failures.|
| Configure the [Point-to-Site address pool](/azure/vpn-gateway/vpn-gateway-vpn-faq) with capacity to support twice the expected user count. Size the address pool to prevent exhaustion during gateway instance redistribution. Monitor address pool utilization for ongoing capacity planning. | Prevents address exhaustion during gateway maintenance or failures. Enables single VPN profile deployment to all users. Supports business continuity for remote workforce. |
| Configure Site-to-Site [VPN as backup connectivity](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-use-vpn-to-backup-my-expressroute-connection) for ExpressRoute circuits by deploying a VPN gateway alongside the ExpressRoute gateway in the virtual hub. Set BGP preference to favor ExpressRoute over VPN. | Maintains connectivity when ExpressRoute fails. Preserves application availability for on-premises dependent services. |
| Configure [diagnostic settings](/azure/virtual-wan/monitor-virtual-wan) for all Virtual WAN resources with output to a Log Analytics workspace. Enable diagnostics for virtual hubs, gateways, and firewalls. Send diagnostic logs to a centralized Log Analytics workspace for comprehensive visibility. | Enables proactive issue detection and rapid incident response. Identifies connectivity problems before users report outages. Notifies operations teams instantly when failures occur. |
| Configure alerts for [Virtual WAN metrics and Resource Health](/azure/virtual-wan/monitor-virtual-wan-reference) events. Create alerts for gateway connectivity failures and BGP peer session status changes. Set alerts for routing infrastructure capacity thresholds and enable Resource Health alerts. | Accelerates troubleshooting through centralized log correlation. Provides historical data for pattern identification. Enables automated remediation workflows for common issues. |
| Review the [Virtual WAN disaster recovery guidance](/azure/virtual-wan/disaster-recovery-design) to understand failure scenarios and mitigation strategies. | Systematic failure mode analysis identifies reliability risks early in design. Enables proactive mitigation through redundancy and failover mechanisms before production deployment. |

## Security

Security provides **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to Azure Virtual WAN.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture.

> [!div class="checklist"]
>
> - **Establish security baseline:** Apply [Azure Security Baseline for Virtual WAN](/security/benchmark/azure/baselines/virtual-wan-security-baseline) as your foundational security baseline. The baseline covers Virtual WAN-specific controls for hub security, gateway configuration, routing security, and connection encryption.
>
>    This standardized approach addresses Virtual WAN service endpoints: virtual hubs, VPN gateways, ExpressRoute gateways, and Azure Firewall integration. It provides compliance mapping for network infrastructure requirements.
>
> - **Implement network segmentation through routing controls:** Control network segmentation in Virtual WAN through routing tables. Create custom route tables to isolate routing domains and prevent unauthorized traffic between zones. Apply route maps when standard route table isolation doesn't meet connection requirements.
>
>    Deploy a secured virtual hub to inspect traffic centrally. Use Azure Firewall for threat intelligence and Azure service integration, or deploy partner network virtual appliances for deep packet inspection and advanced threat prevention.
>
> - **Establish private connectivity and protect public endpoints:** Deploy private endpoints in spoke networks to access Azure PaaS services without internet exposure. On-premises users reach these services through ExpressRoute or VPN connections. Routing through secured hubs enables inspection but adds latency and cost.
>
>    Protect public-facing spoke workloads with Azure DDoS Protection at the virtual network or IP level. DDoS Protection isn't available for hub public IPs.
>
> - **Implement identity and access management:** Select your Point-to-Site VPN authentication method based on requirements:
>
>   - Microsoft Entra ID authentication provides Conditional Access policies, MFA, device compliance checks, and risk-based authentication.
>   - Certificate-based authentication requires PKI infrastructure and certificate lifecycle management.
>   - RADIUS authentication integrates with existing RADIUS infrastructure.
>
>   Control Virtual WAN configuration access using RBAC:
>
>   - Apply RBAC assignments to control who can modify Virtual WAN configuration.
>   - Use built-in Network Contributor and Reader roles for most scenarios.
>   - Create custom roles for more granular permission boundaries when built-in roles are too permissive.
>
> - **Control and filter network traffic with defense in depth:** Deploy security controls at multiple network layers. Use Virtual WAN Standard SKU to deploy Azure Firewall or partner security solutions in the hub for centralized application-aware filtering and threat protection. Deploy Network Security Groups in spoke networks for subnet-level filtering based on IPs, ports, and protocols.
>
>     Each layer addresses different security requirements. Hub security controls enforce organization-wide policies and detect threats across all connected networks. Spoke NSGs provide workload-specific controls that teams configure based on application requirements.
>
> - **Ensure encrypted connectivity:** Select strong encryption algorithms like AES-256-GCM or AES-256-CBC to protect data confidentiality. Use strong integrity algorithms like SHA-256 or SHA-384 to prevent tampering. Align IPsec/IKE encryption policies for Site-to-Site VPN between Azure and on-premises VPN devices to prevent tunnel establishment failures.
>
> - **Configure encryption for ExpressRoute and Point-to-Site VPN:** Assess ExpressRoute encryption requirements separately from VPN. Each connection type requires different encryption configurations. Configure Point-to-Site VPN encryption based on client requirements and security policies.
>
> - **Harden architecture with Zero Trust principles:** Apply Zero Trust by assuming breach and limiting blast radius. Implement deny-by-default network access controls with explicit permit rules for required traffic only.
>
>    Disable unused features to reduce attack surface:
>
>   - Disable legacy or weak VPN cipher suites (DES, 3DES, MD5).
>   - Remove unnecessary Point-to-Site authentication modes.
>   - Eliminate unneeded public IP assignments.
>
>    Enable security monitoring for breach detection: diagnostic logging, metric alerts, and threat detection through Microsoft Sentinel.
>
> - **Implement security monitoring and threat detection:** Enable diagnostic logging for Virtual WAN components: hubs, gateways, and Azure Firewall for security event visibility. Use Log Analytics workspace to enable centralized log collection, long-term retention, and query-based analysis.
>
>    Configure Azure Monitor alerts for reactive notification of specific metric thresholds or log patterns. Deploy Microsoft Sentinel to add proactive threat detection through machine learning and correlation with broader organizational signals.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Create [custom route tables](/azure/virtual-wan/scenario-isolate-vnets-custom) matching your defined security zones. Set [route table associations](/azure/virtual-wan/how-to-virtual-hub-routing) on spoke virtual network connections. Set route table propagations to control which route tables receive routes from connections. Use "None" route table association when complete isolation is required with no route sharing. | Prevents unauthorized traffic between segmented zones through route control. Reduces blast radius by limiting communication with compromised resources. Enables granular connectivity control. |
| Create [private endpoints](/azure/virtual-wan/howto-private-link) in spoke virtual networks for PaaS services requiring private connectivity: Storage, SQL Database, Cosmos DB, and other [supported services](/azure/private-link/private-endpoint-overview). Configure private endpoint network integration with spoke virtual network subnets. Route private endpoint traffic through secured virtual hub when inspection is required. | Eliminates data exfiltration risks from PaaS services. Reduces attack surface by removing public endpoints. Enables secure PaaS access from on-premises networks. |
| Enable [DDoS Protection](/azure/ddos-protection/manage-ddos-protection) at the virtual network level or DDoS IP Protection for individual public IPs. Configure protection for public IPs on workload resources.<br><br>Note that DDoS Protection is unavailable for Virtual WAN hub public IPs and must be implemented at spoke level. | Detects and mitigates volumetric attacks attempting to overwhelm network bandwidth. Absorbs attack traffic before reaching workloads. Automatically scales mitigation capacity during attacks. |
| Register the [Azure VPN application](/azure/vpn-gateway/openvpn-azure-ad-tenant) in your Microsoft Entra ID tenant and configure the VPN Gateway to use Microsoft Entra ID authentication. Deploy Azure VPN Client to user devices with the Microsoft Entra ID authentication profile.| Prevents unauthorized VPN access through MFA. Enables risk-based authentication adapting to user risk, sign-in risk, and device posture.|
| Create [Conditional Access policies](/azure/vpn-gateway/openvpn-azure-ad-mfa) targeting the Azure VPN application with required controls: MFA, device compliance, and location restrictions. | Enforces adaptive access controls for VPN connections. Requires MFA for high-risk sign-ins. Restricts access to compliant devices and trusted locations. |
| Assign [Network Contributor role](/azure/virtual-wan/roles-permissions) for full management permissions and Reader role for view-only access. Create custom RBAC roles when built-in roles exceed required permissions. | Prevents unauthorized configuration changes through permission limits. Reduces impact of compromised accounts through least privilege. Enables separation of duties with role-based job functions. |
| Select Virtual WAN Standard SKU to enable security solution deployment. Deploy [Azure Firewall](/azure/firewall/overview) or partner security solution in virtual hub for centralized filtering. Enable [Routing Intent](/azure/virtual-wan/how-to-routing-policies) to automatically route traffic through the security solution. Configure internet traffic routing intent for outbound traffic inspection. | Delivers traffic filtering through centralized hub security and distributed spoke controls. Enables workload-specific controls via spoke NSGs. Provides flexibility for implementing controls at appropriate architectural layer. |
| Implement [Network Security Groups](/azure/virtual-network/network-security-groups-overview) on subnets in spoke virtual networks for subnet-level filtering. Configure hub security solution for application and threat inspection. Configure spoke NSGs for network-level filtering based on IPs, ports, and protocols. | Spoke NSGs provide granular workload-specific controls complementing hub security policies. This layered approach enables explicit permit rules for required traffic while enforcing deny-by-default posture, reducing attack surface and limiting lateral movement after compromise. |
| Configure [diagnostic settings](/azure/virtual-wan/monitor-virtual-wan) on Virtual WAN resource, virtual hubs, VPN gateways, ExpressRoute gateways, Point-to-Site gateways, and Azure Firewall. Enable log categories: AllMetrics, GatewayDiagnosticLog, TunnelDiagnosticLog, RouteDiagnosticLog, and IKEDiagnosticLog.<br><br>Route logs to Log Analytics workspace for centralized collection. Build queries targeting security-relevant events: authentication failures, connection anomalies, and configuration changes. | Diagnostic logging enables security event detection through log analysis of suspicious activities and anomalous behaviors. The configuration supports compliance and audit requirements through long-term event retention while providing forensic evidence for security incident investigation. |
| Create [metric alerts](/azure/virtual-wan/monitor-virtual-wan) for security-relevant metrics: VPN Gateway connection status, ExpressRoute circuit status, BGP peer status, and bandwidth utilization anomalies. Set alert thresholds based on established baseline metrics for normal operations. Configure action groups routing alerts to security operations or automated response systems. | Enables rapid detection of connectivity failures indicating security issues or denial of service. Identifies bandwidth anomalies indicating data exfiltration. Provides early detection of BGP routing issues impacting segmentation. |
| Deploy [Microsoft Sentinel](/azure/sentinel/data-connectors/azure-firewall) connected to the Log Analytics workspace receiving Virtual WAN logs. Enable [Azure Firewall data connector](/azure/sentinel/overview) in Sentinel.<br><br>Implement analytics rules for Virtual WAN threat scenarios: anomalous VPN authentication patterns, unusual traffic volumes, and firewall policy violations. Create incident response playbooks for automated actions like blocking suspicious source IPs and disabling compromised VPN users. | Sentinel integration enables advanced threat detection using machine learning and behavioral analytics. The platform correlates Virtual WAN security events with broader organizational security telemetry while providing security operations with unified monitoring. |

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet your budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs in Azure Virtual WAN.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so the workload aligns with your budget. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - **Understand pricing structures to create accurate cost models:** Virtual WAN pricing has fixed and variable components. Fixed costs include SKU selection and allocated scale and connection units. Variable costs include data transfer charges and active features.
>
>    Data transfer costs represent significant variable expenses. Hub-to-hub transfers across regions incur cross-region charges, while hub router processing contributes to hub data processing costs. Virtual WAN combines fixed infrastructure and variable usage charges, making accurate pre-deployment estimation essential.
>
> - **Monitor costs and hub utilization for ongoing optimization:** Implement daily cost tracking for Virtual WAN spending. Focus on hub data processing charges, gateway connection usage, and cross-region data transfer costs.
>
>    Organize resources by business unit or application using tags for cost allocation. Implement chargeback or showback reporting. Monitor spending against budgets to detect unexpected increases early.
>
> - **Establish spending guardrails with budgets and alerts:** Implement budgets and alerts to prevent unexpected spending. Set graduated alert thresholds for early warning before budget exhaustion.
>
>    Track metrics that directly affect variable costs. Monitor hub router traffic volume for hub data processing charges and connection unit usage for capacity costs.
>
>    Apply governance policies to prevent unauthorized cost increases. Use Azure Policy to require approvals for high-cost changes and mandate reviews before adding capacity.
>
> - **Right-size infrastructure capacity based on actual workload requirements:** Size routing infrastructure units based on actual workload needs rather than maximum theoretical capacity. Each unit increases virtual machine capacity and costs.
>
>    Each hub incurs fixed monthly costs regardless of traffic volume. Add hubs only where connectivity requirements justify the cost. Size gateway scale units based on actual throughput requirements. Track usage patterns and adjust capacity when monitoring shows sustained changes in demand.
>
> - **Choose cost-effective connection types and integrated service SKUs:** Site-to-Site VPN provides cost-effective connectivity for lower bandwidth and noncritical connections, while ExpressRoute delivers higher performance at substantially higher cost. Point-to-Site VPN supports remote users with different cost characteristics.
>
>     Use VPN for development and test environments where lower cost justifies variable performance. Use ExpressRoute for production workloads requiring consistent bandwidth and reliability. Implement hybrid approaches routing routine traffic through VPN while directing business-critical applications through ExpressRoute.
>
> - **Optimize routing to minimize data transfer costs:** Traffic flow patterns directly impact variable costs. Hub-to-hub communication across regions generates cross-region charges. Hub router processing contributes to hub data processing costs.
>
>    Optimize routing topology to reduce costs without sacrificing connectivity. Use direct virtual network peering for spoke-to-spoke communication to bypass hub processing charges. Evaluate hub location based on dominant traffic patterns to minimize cross-region transfer charges. Balance cost optimization against security and routing policy requirements.
>
> - **Develop cost-effective scaling strategy for growth:** Plan hub placement to balance latency requirements with costs. Position hubs near user populations to reduce latency while avoiding excessive hub count. Consolidate regions where acceptable latency thresholds permit.
>
>    Optimize connection unit allocation based on actual bandwidth requirements rather than theoretical capacity. Monitor utilization to identify optimization opportunities as demand fluctuates.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) to model deployment costs before implementation by configuring expected hubs across regions, selecting connection types with appropriate scale units, and estimating monthly data transfer volumes. Estimate bandwidth requirements to determine gateway scale units. Calculate minimum hub capacity in routing infrastructure units based on anticipated virtual machine workload and throughput needs. | Supports budget planning and approval with accurate cost estimates. Anticipates variable costs from data transfer and hub processing before deployment. |
| [Apply tags to Virtual WAN hubs](/azure/azure-resource-manager/management/tag-resources), VPN gateways, ExpressRoute gateways, and connections using consistent naming conventions: business unit, application name, environment, or cost center. Use Azure Policy to enforce mandatory tags on resource creation. Configure Cost Management reports to aggregate and filter costs by tag values. Use tagged cost data for chargeback or showback to business units. | Provides accurate cost allocation to business units or applications. Supports chargeback models for internal cost recovery and departmental accountability. |
| Configure [Azure Monitor](/azure/virtual-wan/monitor-virtual-wan) to collect hub router traffic volume metrics continuously. Create workbooks or dashboards visualizing trends over time. Establish baseline traffic patterns to identify normal versus anomalous behavior. Use traffic volume data to identify optimization opportunities by detecting high-volume virtual network-to-virtual network communication suitable for direct peering bypass, identifying unexpected spikes requiring investigation, and analyzing routing patterns to optimize hub-mediated flows. | Enables data-driven optimization decisions based on actual traffic patterns. Supports cost forecasting through traffic trend tracking. |
| Create [Azure Cost Management budget](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) scoped to Virtual WAN resource group or subscription aligned with cost model projections. Configure alert thresholds at 90%, 100%, and 110%. Assign stakeholder email addresses or action groups to receive alerts at each threshold. Configure metric alerts on hub router traffic volume when approaching levels that correlate with budget concerns. Analyze historical patterns to establish normal baseline and set alert thresholds before traffic reaches budget-critical levels. | Proactive notification prevents unexpected cost overruns and enables timely intervention before costs exceed budget. Provides greater visibility into Virtual WAN spending patterns for better cost management. |
| Set virtual hub routing infrastructure units to minimum level adequate for current workload. Start conservatively with baseline capacity rather than anticipated peak. Each unit supports specific virtual machine count and [throughput capacity](/azure/virtual-wan/hub-settings#capacity). Set VPN [gateway scale units](/azure/virtual-wan/gateway-settings) to match anticipated site-to-site throughput with each unit providing 500 Mbps capacity. Set ExpressRoute gateway scale units to match connection needs with each unit delivering 2 Gbps bandwidth. Schedule capacity adjustments during planned maintenance windows. Scale up when utilization monitoring shows approaching limits or scale down if monitoring indicates consistently low utilization. | Optimizes hub costs by aligning capacity with actual workload, avoiding overprovisioning waste from excessive unit allocation. Maintains adequate capacity for performance requirements without excessive spending. |
| Assess connectivity requirements for each site, branch, or connection endpoint to determine appropriate connection types based on workload criticality and performance needs. Configure [Site-to-Site VPN connections](/azure/virtual-wan/virtual-wan-site-to-site-portal) for development environments, testing workloads, nonproduction scenarios, backup connectivity paths, or moderate bandwidth requirements not requiring guaranteed SLAs. Configure [ExpressRoute connections](/azure/virtual-wan/virtual-wan-expressroute-portal) for mission-critical production workloads requiring guaranteed bandwidth. Implement hybrid connectivity using VPN and ExpressRoute in combination, routing general connectivity through VPN while directing critical application traffic through ExpressRoute. | Reduces connectivity costs through appropriate use of lower-cost VPN options. Maintains performance and reliability for critical workloads. Enables cost-effective scaling with VPN connections for noncritical growth. |
| Assess whether specific virtual network-to-virtual network traffic flows require hub-mediated routing by determining if centralized routing policy enforcement is necessary or if Azure Firewall or network virtual appliance inspection is required. Configure [direct virtual network peering](/azure/virtual-network/virtual-network-peering-overview) between virtual networks with high-volume communication that doesn't require hub processing. Create peering connections between source and destination virtual networks, update route tables to use peering instead of hub router, and verify connectivity through the direct path. |  Reduces hub data processing costs by eliminating hub router from virtual network-to-virtual network traffic paths. Improves network latency for peered virtual networks while achieving cost savings. |

## Operational Excellence

Operational Excellence focuses on **development practices, observability, and release management**. The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment.

> [!div class="checklist"]
>
> - **Implement Infrastructure as Code for consistent deployments:** Use ARM templates, Bicep, or Terraform for repeatable Virtual WAN deployments across environments. ARM templates and Bicep provide native Azure integration. Terraform supports multicloud scenarios.
>
> - **Design modular Infrastructure as Code templates:** Separate core hub infrastructure from connection policies and routing rules. Core infrastructure changes infrequently while routing rules change more often. Parameterize environment-specific values like scale units and address spaces.
>
> - **Orchestrate resource dependencies:** Sequence operations correctly because gateways require hubs, connections require gateways, and routing policies require connections. Build wait conditions into automation to verify route propagation before marking deployment complete.
>
> - **Establish build and deployment pipelines for automation:** Design deployment pipelines with stage separation for validation, approval, and deployment. Implement quality gates between stages to block problematic changes.
>
>    Include static validation for syntax checking, policy compliance, and security rule verification. Implement pre-deployment testing for configuration validation against organizational standards.
>
> - **Implement monitoring for topology, services, and dependencies:** Establish complete visibility across topology, service health, and operational telemetry. Monitor global hub distribution, spoke connectivity, and gateway operational status.
>
>    Collect gateway performance metrics for throughput utilization and connection counts. Track connection reliability metrics for tunnel availability. Monitor routing health metrics for route propagation status.
>
>    Design role-based operational views. Provide operations teams with real-time health dashboards. Enable architects to identify capacity trends. Balance retention strategy between investigation capabilities and storage costs.
>
> - **Configure alerting for connectivity and performance issues:** Design alerting around failure scenarios that impact operations. Target connectivity failures breaking user access, performance degradation slowing applications, and configuration anomalies indicating errors.
>
>    Define specific measurable conditions like tunnel disconnections, gateway CPU saturation above safe thresholds, and unexpected routing changes. Balance alert thresholds between early detection and alert fatigue. Establish notification routing matching alert severity with appropriate urgency.
>
> - **Automate operational tasks and connectivity validation:** Automate repetitive Virtual WAN tasks like connection status validation and multi-hub configuration updates. Automate time-sensitive operations to reduce manual effort.
>
>    Implement scheduled automation for routine health checks. Configure event-driven automation for configuration drift remediation. Provide on-demand automation for ad-hoc validation scripts and troubleshooting tools.
>
>    Leverage Virtual WAN native automation capabilities including automatic route advertisement, route propagation across hub infrastructure, and gateway auto-scaling responding to load patterns.
>
> - **Implement progressive deployment practices:** Deploy to development first, then staging, validating at each step. Run static configuration validation and policy compliance checks before deployment. Follow deployment with smoke tests verifying gateway reachability and connection establishment.
>
> - **Establish rollback capability:** Maintain configuration versioning in Infrastructure as Code repository. Document rollback procedures for reverting gateway settings and routing policies. Separate routine deployments from emergencies. Route routine changes through full validation with approvals. Allow emergencies to skip extended approvals while still validating syntax.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Use [ARM templates](/azure/templates/microsoft.network/virtualwans) or [Bicep](/azure/azure-resource-manager/bicep/overview) for Virtual WAN infrastructure deployment. Define modular templates separating resource types: hubs, VPN and ExpressRoute gateways, connections, and routing policies. Parameterize environment-specific values: hub address spaces, gateway scale units, connection settings, and routing preferences. Deploy templates through Azure CLI or Azure PowerShell with environment-specific parameter files. Store templates in version control with CI/CD pipeline integration. | Ensures consistent configuration through declarative syntax. Tracks changes and enables rollback through version control. Deploys identical architectures across environments with parameterization. |
| Configure a CI/CD pipeline using [Azure DevOps Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) or [GitHub Actions](/azure/developer/github/github-actions) for Virtual WAN deployment automation. Define a multi-stage pipeline with separated stages for validation, development deployment, and production deployment. Configure the validation stage with ARM template validation, Azure Policy compliance checks, and security scanning. Add approval gates requiring manual approval before production deployment. Configure deployment stages with environment-specific parameters and service connections. | Reduces manual effort and eliminates human error through automation. Prevents problematic changes from reaching production with approval gates. Supports compliance and incident investigation through deployment history. |
| Enable [Azure Monitor Insights](/azure/virtual-wan/azure-monitor-insights) for Virtual WAN to access integrated topology visualization and [metrics workbooks](/azure/virtual-wan/monitor-virtual-wan). Configure metric collection for gateway throughput, connection counts, BGP peer status, and routing table health. Set the metric collection interval and aggregation granularity based on monitoring requirements. For long-term analysis, export metrics to a Log Analytics workspace for retention beyond the default 93-day period. | Provides single-pane visibility across regions and hubs. Delivers immediate value without custom development through built-in dashboards. Supports rapid diagnosis and capacity planning with historical data. |
| Enable [diagnostic settings](/azure/azure-monitor/essentials/diagnostic-settings) for Virtual WAN hub, VPN gateway, and ExpressRoute gateway resources. Configure log collection for GatewayDiagnosticLog, TunnelDiagnosticLog, RouteDiagnosticLog, and IKEDiagnosticLog categories. Send logs to a Log Analytics workspace for centralized analysis. Create operational dashboards using [Azure Dashboard](/azure/azure-monitor/visualize/tutorial-logs-dashboards) or [Azure Workbooks](/azure/azure-monitor/visualize/workbooks-overview) to visualize gateway status, connection counts, error trends, and throughput metrics. Configure dashboard refresh intervals and time ranges for operational monitoring needs. | Enables effective troubleshooting through detailed telemetry. Provides comprehensive operational visibility capturing gateway events and state transitions. Supports audit and compliance requirements through change documentation. |
| Configure [metric alert rules](/azure/azure-monitor/alerts/alerts-create-metric-alert-rule) for Virtual WAN gateway and hub resources. Set alert conditions for VPN Gateway metrics: TunnelEgressPacketDropCount, TunnelIngressPacketDropCount, and TunnelBandwidth thresholds. Set ExpressRoute Gateway alerts for BitsInPerSecond and BitsOutPerSecond approaching circuit capacity.<br><br>Configure Virtual WAN Hub alerts for PacketCount drops and RoutingState changes indicating routing propagation issues. Create [action groups](/azure/azure-monitor/alerts/action-groups) specifying notification methods: email, SMS, or webhook with recipients based on alert severity.<br><br>Set alert evaluation frequency and lookback window balancing detection latency with alert accuracy. This prevents excessive false positives while maintaining timely issue detection. | Minimizes business impact through rapid detection of connectivity or performance issues. Provides early warning before critical failures. Supports root cause analysis and proactive capacity planning through historical alert data. |
| Configure [Network Watcher Connection Monitor](/azure/network-watcher/connection-monitor-overview) for automated connectivity testing across Virtual WAN infrastructure. Define test groups with source and destination endpoints covering hub-to-hub, hub-to-spoke, and on-premises paths.<br><br>Configure test parameters: protocol (TCP or ICMP), port numbers, probe frequency, and success thresholds. Set alert thresholds for test failures and latency exceeding acceptable limits. Integrate results with operational dashboards for continuous connectivity visibility. | Continuous validation detects connectivity issues before user impact. Automated testing validates all paths, identifying failures immediately.<br><br>End-to-end testing verifies complete connectivity including routing and network paths. This reduces mean time to detection compared to reactive user reporting. |
| Configure [Azure Automation](/azure/automation/automation-intro) runbooks for repetitive Virtual WAN tasks. Examples: gateway scaling automation that monitors capacity metrics and updates configuration, VPN site management for bulk operations, and health validation that checks gateway status and routing tables.<br><br>Configure runbook execution through scheduled triggers for routine tasks or event-driven triggers based on Azure Monitor alerts. This enables both proactive maintenance and reactive responses to operational events.<br><br>Configure [Azure Policy](/azure/governance/policy/concepts/effects) for drift detection and automatic remediation. Define policies validating gateway SKU compliance, diagnostic settings, and network security rules. Enable automatic remediation using DeployIfNotExists or Modify effects. | Eliminates time-consuming repetitive tasks. Applies standards uniformly across hubs and gateways. Maintains desired state through drift remediation while reducing misconfiguration risk. |
| Configure [customer-controlled maintenance](/azure/virtual-wan/customer-controlled-gateway-maintenance) windows for VPN and ExpressRoute gateways. Define a maintenance schedule: day of week, start time, and duration aligned with low-traffic periods. Apply schedules to gateways individually or stagger across hubs.<br><br>Configure phased deployment in your [CI/CD pipeline](/azure/architecture/framework/devops/release-engineering-cd). Deploy to development and test environments first with connectivity validation. Deploy to the first production hub with a monitoring period before expanding to remaining hubs.<br><br>Configure rollback capability through Infrastructure as Code version control. Maintain previous configuration versions with rollback tags in your Git repository. Deploy rollbacks using previous ARM template versions and parameter files. Consider blue-green deployment patterns for instant traffic shifting. | Predictable maintenance timing reduces unplanned disruption risk. Scheduling maintenance during planned periods enables coordination with application teams.<br><br>Phased rollout detects issues in development before full production impact. Automated rollback enables immediate recovery while blue-green patterns support instant traffic shifting. |

## Performance Efficiency

Performance Efficiency is about **maintaining user experience when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

<!-- markdownlint-disable-next-line MD024 -->
### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Azure Virtual WAN.

> [!div class="checklist"]
>
> - **Conduct capacity planning for components:** Plan Virtual WAN capacity by assessing VPN and ExpressRoute gateways, hub routers, and routing infrastructure. Start by estimating VPN gateway throughput per tunnel because all active tunnels share the total gateway capacity.
>
>    Monitor current utilization across gateways and hub routers to establish performance baselines. Use these baselines to project future capacity needs accurately. Planning should cover peak traffic scenarios, expected business growth, and new site connections you anticipate adding.
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
> - **Optimize configurations for performance:** Evaluate gateway sizing strategies by assessing VPN and ExpressRoute gateway capacity against bandwidth requirements and traffic projections. Account for peak traffic scenarios and expected business growth when determining appropriate gateway SKUs.
>
>    Select IPsec encryption algorithms that balance security requirements with gateway CPU efficiency. Design routing path preferences to optimize business-critical traffic patterns taking into account the performance characteristics of different routing options: hub-to-hub versus ExpressRoute paths for virtual network-to-virtual network scenarios.
>
> - **Validate routing for performance troubleshooting:** Routing validation establishes systematic practices for performance troubleshooting by designing operational procedures to verify route learning and propagation across hybrid network components. Document troubleshooting workflows for routing-related performance issues and apply learnings from routing incidents to improve monitoring and preventive measures.
>
> - **Continuously monitor and optimize capacity:** Continuous capacity optimization establishes proactive practices to maintain performance as requirements evolve. Design a proactive capacity management approach with regular performance reviews. Plan capacity adjustments before utilization reaches limits to avoid performance impact. This approach aligns capacity optimization with business growth and changing connectivity patterns.
>
<!-- markdownlint-disable-next-line MD024 -->
### Configuration recommendations

| Recommendation | Benefit |
| ----- | ----- |
| Enable native [Azure Monitor metrics](/azure/virtual-wan/monitor-virtual-wan-reference) for hub router, ExpressRoute Gateway, and VPN Gateway components. Configure collection of throughput metrics, connection health indicators, bandwidth utilization, packet loss rates, and latency measurements with appropriate retention periods. Set metric retention to support historical trend analysis and capacity planning requirements. Configure alert rules for critical performance thresholds including gateway throughput limits and hub capacity constraints. | Identifies performance bottlenecks before user impact occurs. Supports capacity planning with historical data. Simplifies monitoring through native Azure Monitor integration. |
| Configure [routing infrastructure units](/azure/virtual-wan/hub-settings) at hub deployment based on calculated inter-hub and spoke-to-spoke throughput requirements. Deploy multiple hubs within the same region when traffic volumes exceed single hub limits or when different routing configurations are needed. | Prevents bottlenecks in inter-hub and spoke-to-spoke connectivity. Supports expected traffic volumes without overprovisioning. Enables cost-effective performance with right-sized infrastructure. |
| Identify geographic distribution of users, branch offices, and on-premises data centers to position hubs optimally for highest-volume and most critical traffic patterns. Test network path latency to validate region selection before deployment. Evaluate multi-region deployment when populations span large geographic areas. | Minimizes network latency for end users and applications. Improves application performance and user experience. Decreases network round-trip times for critical workloads. |
| Configure [hub routing preference](/azure/virtual-wan/about-virtual-hub-routing-preference) setting to "ExpressRoute", "VPN Gateway", or "AS Path" based on primary connectivity type and performance requirements for spoke-to-spoke traffic. Select "ExpressRoute" preference when ExpressRoute circuits provide lower latency paths between spoke virtual networks. Test selected routing preference in nonproduction environment before production deployment. | Reduces latency for critical connectivity scenarios. Enables efficient traffic path selection. Improves throughput efficiency for inter-hub and spoke traffic. |
| Configure GCMAES256 algorithm for both IPsec encryption and IPsec integrity parameters on site-to-site VPN connection [custom IPsec policies](/azure/vpn-gateway/vpn-gateway-about-compliance-crypto). Apply custom IPsec policy with GCMAES256 settings to VPN connections during connection creation. | Reduces CPU overhead on VPN gateways while enabling higher throughput on VPN site-to-site connections. Maintains security while maximizing gateway capacity utilization for improved performance. |
| Configure monitoring to check [virtual hub effective routes](/azure/virtual-wan/effective-routes-virtual-hub) in hub routing tables. Verify expected routes are present and correctly learned from connected resources. Monitor virtual machine NIC effective routes to confirm virtual machines receive correct routes from Virtual WAN hubs.<br><br>Verify route advertisements from on-premises BGP routers reach Virtual WAN hubs with expected metrics. Configure Azure Monitor alerts for routing anomalies: missing expected routes or route metric changes. | Identifies routing problems before they impact users while reducing mean time to resolution for connectivity and performance issues. Prevents performance degradation from routing misconfigurations through proactive monitoring. |
| Configure [monitoring for VPN Gateway](/azure/vpn-gateway/monitor-vpn-gateway) throughput metrics and [ExpressRoute Gateway](/azure/expressroute/monitor-expressroute) throughput metrics. Track utilization against gateway SKU capacity limits. Monitor connection counts, packet rates per second, and active tunnel metrics to identify capacity constraints.<br><br>Establish utilization threshold alerts at 70% capacity to trigger gateway resizing evaluation. Resize gateway SKUs when sustained utilization exceeds 80% capacity or during performance degradation. | Proactive capacity management maintains network performance as usage grows. Maintains consistent connection speeds and application response times as traffic patterns change. Early detection at 70% capacity provides time to plan gateway upgrades during scheduled maintenance windows. Prevents sudden performance drops that force emergency changes and disrupt users. Monitoring connection counts and packet rates helps spot capacity trends before they become problems. |

## Azure Policy

Azure provides an extensive set of built-in policies related to Azure Virtual WAN and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Virtual hubs are protected with Azure Firewall for centralized security inspection.
- VPN gateways avoid using Basic SKU that lacks reliability and performance features.
- Point-to-Site VPN connections use Microsoft Entra ID authentication for enhanced security.

For comprehensive governance, review the [Azure Policy built-in definitions for Azure Virtual WAN](/azure/governance/policy/samples/built-in-policies#network) and other policies that might affect network infrastructure security.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Hub-spoke network topology with Azure Virtual WAN](/azure/architecture/networking/architecture/hub-spoke-virtual-wan-architecture).

## Related content

- [Virtual WAN FAQ](/azure/virtual-wan/virtual-wan-faq)
- [Introduction to Azure Virtual WAN - Training](/training/modules/introduction-azure-virtual-wan/)
- [Virtual WAN partners, regions, and locations](/azure/virtual-wan/virtual-wan-locations-partners)

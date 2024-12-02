---
title: Azure Well-Architected Framework perspective on Azure Kubernetes Service (AKS)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Kubernetes Service (AKS).
author: schaffererin
ms.author: schaffererin
ms.topic: conceptual
ms.date: 11/26/2024
ms.product: azure-kubernetes-service
azure.category:
  - containers
---

# Azure Well-Architected Framework perspective on Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) is a managed Kubernetes service that you can use to deploy and manage containerized applications. AKS reduces the complexity and operational overhead of managing Kubernetes by offloading much of that responsibility to Azure. AKS is an ideal platform for deploying and managing containerized applications that require high availability, scalability, and portability.

This article assumes that as an architect, you reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose AKS as the compute for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are *recommendations* on the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations available for Azure Kubernetes Service and their dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [Baseline architecture for an Azure Kubernetes Service (AKS) cluster](/azure/architecture/reference-architectures/containers/aks/baseline-aks).

## Technology scope

This review focuses on the interrelated decisions for the following Azure resources:

- Azure Kubernetes Service

When discussing the Well Architected pillars best practices with Azure Kubernetes Service, it's important to distinguish between *cluster* and *workload*. Cluster best practices are a shared responsibility between the cluster admin and their resource provider, while workload best practices are the domain of a developer. This article has considerations and recommendations for both of these roles.

> [!NOTE]
> In the **design checklist** and **list of recommendations** under each pillar below, callouts are made to indicate whether each choice is applicable to *cluster* architecture, *workload* architecture, or both.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of Azure Kubernetes Service and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Cluster) For critical workloads, use [availability zones](/azure/aks/availability-zones) for your AKS clusters.
>
> - (Cluster) Plan the IP address space to ensure your cluster can reliably scale, including handling of failover traffic in multi-cluster topologies.
> - (Cluster) Review the [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers) to determine the best monitoring strategy for your workloads.
> - (Workload) Ensure workloads are built to support horizontal scaling and report application readiness and health.
> - (Cluster and Workload) Ensure your workload is running on user node pools and chose the right size SKU. At a minimum, include two nodes for user node pools and three nodes for the system node pool.
> - (Cluster) Use the AKS Uptime SLA to meet availability targets for production workloads.

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster and Workload) Control pod scheduling using node selectors and affinity.|Allows the Kubernetes scheduler to logically isolate workloads by hardware in the node. Unlike [tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/), pods without a matching node selector can be scheduled on labeled nodes, which allows unused resources on the nodes to consume, but gives priority to pods that define the matching node selector. Use node affinity for more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|(Cluster) Ensure proper selection of network plugin based on network requirements and cluster sizing.|Azure CNI is required for specific scenarios, for example, Windows-based node pools, specific networking requirements and Kubernetes Network Policies. Reference [Kubenet versus Azure CNI](/azure/aks/concepts-network#compare-network-models) for more information.|
|(Cluster and Workload) Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production grade clusters.|The AKS Uptime SLA guarantees:<br> - `99.95%` availability of the Kubernetes API server endpoint for AKS Clusters that use Azure Availability Zones, or <br> - `99.9%` availability for AKS Clusters that don't use Azure Availability Zones.|
|(Cluster and Workload) Review the [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers) to determine the best monitoring strategy for your workloads. | N/A |
|(Cluster) Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.|By spreading node pools across multiple zones, nodes in one node pool will continue running even if another zone has gone down. If colocality requirements exist, either a regular Virtual Machine Scale Sets based AKS deployment into a single zone or [proximity placement groups](/azure/aks/reduce-latency-ppg) can be used to minimize internode latency.|
|(Cluster) Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) to route traffic globally across AKS clusters.|
| (Cluster and Workload) Define Pod resource requests and limits in application deployment manifests, and enforce with Azure Policy.| Container CPU and memory resource limits are necessary to prevent resource exhaustion in your Kubernetes cluster.|
|(Cluster and Workload) Keep the System node pool isolated from application workloads.|System node pools require a VM SKU of at least 2 vCPUs and 4 GB memory, but 4 vCPU or more is recommended. Reference [System and user node pools](/azure/aks/use-system-pools#system-and-user-node-pools) for detailed requirements.|
|(Cluster and Workload) Separate applications to dedicated node pools based on specific requirements.|Applications may share the same configuration and need GPU-enabled VMs, CPU or memory optimized VMs, or the ability to scale-to-zero. Avoid large number of node pools to reduce extra management overhead.|
|(Cluster) Use a NAT gateway for clusters that run workloads that make many concurrent outbound connections.|To avoid reliability issues with Azure Load Balancer limitations with high concurrent outbound traffic, us a [NAT Gateway](/azure/aks/nat-gateway) instead to support reliable egress traffic at scale.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Azure Kubernetes Service.

### Design checklist

Start your design strategy based on the [**design review checklist for Security**](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Familiarize yourself with [AKS security concepts](/azure/aks/concepts-security) and evaluate our security hardening recommendations based on the [CIS Kubernetes benchmark](/azure/aks/cis-kubernetes). Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Cluster) **Integrate with  Microsoft Entra ID for [identity and access mangement](/azure/well-architected/security/identity-access#the-role-of-an-identity-provider).** Using Microsoft Entra ID centralizes identity management for your cluster. Any change in user account or group status is automatically updated in access to the AKS cluster. [Establish identity as the primary security perimeter](/azure/well-architected/security/segmentation#establish-identity-as-the-primary-security-perimeter), the developers and application owners of your Kubernetes cluster need access to different resources.
>
>   Use [Managed Identities](/azure/aks/use-managed-identity) to avoid managing and rotating service principles.
>
>   Use Kubernetes role-based access control (RBAC) with Microsoft Entra ID for [least privilege](/azure/aks/azure-ad-rbac) access and minimize granting administrator privileges to protect configuration, and secrets access.
> - (Cluster) **Integrate with security monitoring and SIEM tools.** Use Microsoft Defender for containers with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your cluster and workloads running on them. Enable [Azure Kubernetes Service (AKS) connector for Microsoft Sentinel](/azure/sentinel/data-connectors/azure-kubernetes-service-aks) to stream your AKS diagnostics logs into Microsoft Sentinel.
> - (Cluster and Workload) **Implement segmentation and network controls.** To prevent data exfiltration, ensure that only authorized and safe traffic is allowed, and contain the blast radius of a security breach.
>
>   Deploy a private AKS cluster to ensure cluster management traffic to your API server remains on your private network. Or use the API server allow list for non-private clusters.
>
>   Use a Web Application Firewall to secure HTTP(S) traffic.
> - (Workload) **Maintain a hardened workload's software supply chain.**  Ensure your CI/CID pipeline is hardened with container-aware scanning. Review [secure software supply chain solution for the Azure Kubernetes Service (AKS)](/devsecops/playbook/Enterprise-Solutions/governance-solutions/secure-software-supply-chain/solution) for additional strategies.
> - (Cluster and Workload) **Implement extra protection for specialized secure workloads.** Review [AKS regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro) if your cluster needs to run a sensitive workload that meets requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1). To learn about DoD Impact Level 5 (IL5) support and requirements with AKS, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-kubernetes-service).

### Recommendations

|Recommendation|Benefit|
|----------------------------------|-----------|
|(Cluster) Use Microsoft Entra ID to [Authenticate with Azure Container Registry from Azure Kubernetes Service](/azure/aks/cluster-container-registry-integration?tabs=azure-cli).|Using Microsoft Entra ID enables AKS to authenticate with Azure Container Registry without the use of `imagePullSecrets` secrets.|
|(Cluster) Secure network traffic to your API server with [private AKS cluster](/azure/aks/private-clusters).|By default, network traffic between your node pools and the API server travels the Microsoft backbone network; by using a private cluster, you can ensure network traffic to your API server remains on the private network only.|
|(Cluster) For non-private AKS clusters, use API server authorized IP ranges. Include sources like the public IPs of your deployment build agents, operations management, and node pools' egress point (such as Azure Firewall).|When using public clusters, you can significantly reduce the attack surface of your AKS cluster by limiting the traffic that can reach your clusters API server.|
|(Cluster) Protect the API server with Microsoft Entra RBAC.<br><br>[Disable local accounts](/azure/aks/managed-aad#disable-local-accounts) to enforce all cluster access using Microsoft Entra ID-based identities.|Securing access to the Kubernetes API Server is one of the most important things you can do to secure your cluster. Integrate Kubernetes role-based access control (RBAC) with Microsoft Entra ID to control access to the API server. |
|(Cluster) Use [Azure network policies](/azure/aks/use-network-policies) or [Calico](/azure/aks/hybrid/concepts-container-networking#kubernetes-networks).| With policies, you can secure and control network traffic between pods in a cluster. Calico provides a richer set capabilities including: policy ordering/priority, deny rules, and more flexible match rules.|
|(Cluster) Secure clusters and pods with [Azure Policy](/azure/aks/use-azure-policy). | Azure Policy can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. |
|(Cluster) Secure container access to resources. Limit access to actions that containers can perform. Provide the least number of permissions, and avoid the use of root or privileged escalation.<br><br>For Linux based containers, see [Security container access to resources using built-in Linux security features](/azure/aks/secure-container-access).|By restricting permissions and avoiding the use of root or privileged escalation, you reduce the risk of security breaches. Ensuring that even if a container is compromised, the potential damage is minimized.|
|(Workload) Use a Web Application Firewall to scan incoming traffic for potential attacks. Use a web application firewall such as [Azure Web Application Firewall (WAF) on Azure Application Gateway](/azure/web-application-firewall/ag/ag-overview) or [Azure Front Door](/azure/web-application-firewall/afds/afds-overview).|WAF can detect and mitigate threats in real-time, ensuring that malicious traffic is blocked before it reaches your applications. It provides robust protection against common web-based attacks such as SQL injection, cross-site scripting (XSS), and other Open Web Application Security Project (OWASP) vulnerabilities.|
|(Cluster) Control cluster egress traffic by ensuring your cluster's outbound traffic is passing through a network security point such as [Azure Firewall](/azure/aks/limit-egress-traffic#restrict-egress-traffic-using-azure-firewall) or an [HTTP proxy](/azure/aks/http-proxy).|Routing outbound traffic through Azure Firewall or an HTTP proxy enables you to enforce security policies that prevent unauthorized access and data exfiltration. This approach also simplifies the administration of security policies and makes it easier to enforce consistent rules across your entire AKS cluster|
|(Cluster) Use the open-source [Microsoft Entra Workload ID](https://github.com/Azure/azure-workload-identity) and [Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) with Azure Key Vault.| These features enable you to protect and rotate secrets, certificates, and connection strings in Azure Key Vault with strong encryption. Provides an access audit log, and keeps core secrets out of the deployment pipeline.|
|(Cluster) Use [Microsoft Defender for Containers](/azure/defender-for-cloud/defender-for-containers-introduction). |Microsoft Defender for Containers helps you monitor and maintain the security of your clusters, containers, and their applications.|

## Cost optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to [Azure offering] and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments and fine tune the design so that the workload is aligned with the budget allocated for the workload.

Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time. Review [Optimize Costs in Azure Kubernetes Service](/azure/aks/best-practices-cost).

> [!div class="checklist"]
>
> - (Cluster) **Familiarize yourself with the [Pricing tiers for AKS](/azure/aks/free-standard-pricing-tiers)** and [How pricing and cost management work in Azure Kubernetes Service (AKS) compared to Amazon Elastic Kubernetes Service (Amazon EKS)](/azure/architecture/aws-professional/eks-to-aks/cost-management). To estimate costs, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Kubernetes Service** from the available products. You can test different configuration and payment plans in the calculator.
> - (Cluster) **Get the best rates for your workload.** Use appropriate VM SKU per node pool and reserved instances where long-term capacity is expected. If you're running AKS on-premises or at the edge, [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) can also be used to further reduce costs when running containerized applications in those scenarios.
> - (Cluster and Workload) **Optimize workload components cost.** Remove any resources that are unused and optimize [underutilized workload resources](/azure/well-architected/cost-optimization/optimize-component-costs#optimize-workload-resources).
>
>   Review the Cost Optimization recommendations in [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers#cost-optimization) to determine the best monitoring strategy for your workloads. Analyze performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
>
>   Use appropriate managed disk tier and size.
> - (Cluster and Workload) **Optimize workload scaling costs.** Consider alternative vertical and horizontal scaling configurations to reduce scaling costs while still meeting all workload requirements. Use autoscalers to scale in when workloads are less active.
> - (Cluster and Workload) **Collect and analyze cost data.** The foundation of enabling cost optimization is the spread of a cost saving cluster. A [financial operations approach (FinOps)](https://www.finops.org/introduction/what-is-finops/) is often used to help organizations reduce cloud costs. It's a practice involving collaboration between finance, operations, and engineering teams to drive alignment on cost saving goals and bring transparency to cloud costs.

### Recommendations

| Recommendation | Benefit |
|----------------------------------|-----------|
|(Cluster and Workload) Align [SKU selection](/azure/aks/free-standard-pricing-tiers) and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|
|(Cluster) Select the right virtual machine instance type.<br><br>To determine the right virtual machine instance type, consider workload characteristics, resource requirements, and availability needs.|Selecting the right virtual machine instance type is critical as it directly impacts the cost of running applications on AKS. Choosing a high-performance instance without proper utilization can lead to wasteful spending, while choosing a less powerful instance can lead to performance issues and increased downtime.|
|(Cluster) Select [virtual machines based on the Arm architecture](/azure/virtual-machines/dplsv5-dpldsv5-series). AKS supports [creating Arm64 Ubuntu agent nodes](/azure/aks/use-multiple-node-pools#add-an-arm64-node-pool), as well as a mix of Intel and ARM architecture nodes within a cluster.| The ARM64 architecture provides a better price-to-performance ratio due to its lower power utilization and efficient compute performance that and can bring better performance at a lower cost.|
|(Cluster) Select [Azure Spot Virtual Machines](/azure/virtual-machines/spot-vms). |Spot VMs allow you to take advantage of unutilized Azure capacity with significant discounts (up to 90% as compared to pay-as-you-go prices). If Azure needs capacity back, the Azure infrastructure evicts the Spot nodes. |
|(Cluster) Select the appropriate region.<br><br>Evaluate the cost, latency, and compliance requirements to ensure you're running your workload cost-effectively and it doesn't affect your end-users or create extra networking charges.|The region where you deploy your workload in Azure can significantly impact the cost. Due to many factors, cost of resources varies per region in Azure.|
|(Workload) Maintain small and optimized images.|Streamlining your images helps reduce costs since new nodes need to download these images. Build images in a way that allows the container to start as soon as possible to help avoid user request failures or timeouts while the application is starting up, potentially leading to overprovisioning.|
|(Cluster) Enable [Cluster Autoscaler](/azure/aks/cluster-autoscaler) to automatically reduce the number of agent nodes in response to excess resource capacity. |Automatically scaling down the number of nodes in your AKS cluster lets you run an efficient cluster when demand is low and scale up when demand returns.|
|(Cluster) Enable [Node Autoprovision](/azure/aks/node-autoprovision?tabs=azure-cli) to automate VM SKU selection. |Node Autoprovision simplifies the SKU selection process and decides, based on pending pod resource requirements, the optimal VM configuration to run workloads in the most efficient and cost effective manner. |
|(Workload) Use the [Horizontal Pod Autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler).|Adjust the number of pods in a deployment depending on CPU utilization or other select metrics, which support cluster scale-in operations.|
|(Workload) Use [Vertical Pod Autoscaler](/azure/aks/vertical-pod-autoscaler) (preview).|Rightsize your pods and dynamically set [requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) based on historic usage.|
|(Cluster) Sign up for [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations) or [Azure Savings Plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview). | If you properly planned for capacity, your workload is predictable and exists for an extended period of time, sign up for an [Azure Reservation](/azure/aks/faq#can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes) or a [savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview#determine-your-savings-plan-commitment) to further reduce your resource costs.|
|(Cluster) Configure the [AKS Cost Analysis add-on](/azure/aks/cost-analysis). | The cost analysis cluster extension enables you to obtain granular insight into costs associated with various Kubernetes resources in your clusters or namespaces.|

## Operational excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment. Review  [AKS best practices](/azure/aks/best-practices) and [Day-2 operations guide](/azure/architecture/operator-guides/aks/day-2-operations-guide) to learn about key considerations to understand and implement.

> [!div class="checklist"]
>
> - (Cluster) **Infrastructure as code (IaC) deployment approach.** Use a template-based deployment using Bicep, Terraform, or others. Make sure that all deployments are repeatable, traceable, and stored in a source code repo.
>
> - (Cluster and Workload) **Automate infrastructure and workload deployments.** Use standard software solutions to manage, integrate, and automate the deployment of your cluster and workloads. Integrate deployment pipelines with your source control system and incorporate automated tests.
>
>   Build an automated process to ensure your clusters are bootstrapped with the necessary cluster-wide configurations and deployments. This is often performed using GitOps.
>
>   Use a repeatable and automated deployment processes for your workload within your software development lifecycle.
> - (Cluster and Workload) **Implement a comprehensive monitoring strategy.** Collect logs and metrics so you can monitor the health of the workload, identify trends in performance and reliability, and troubleshoot problems. Review the [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers) and the Well-Architected [Recommendations for designing and creating a monitoring system](/azure/well-architected/operational-excellence/observability) to determine the best monitoring strategy for your workloads.
>
>    Enable diagnostics settings to ensure control plane or core API server interactions are logged.
>
>    The workload should be designed to emit telemetry that can be collected, which should also include liveliness and readiness statuses.
> - (Cluster and Workload) **Implement testing in production strategies.** Testing in production uses real deployments to validate and measure an application's behavior and performance in the production environment. Use chaos engineering practices that target Kubernetes to identify application or platform reliability issues.
> - (Cluster and Workload) **Enforce cluster and workload governance using Azure Policy.** Azure Policy helps ensure consistent compliance with organizational standards, automates policy enforcement, and provides centralized visibility and control over your resources. Review the [Azure policies](#azure-policies) section to learn more about the available built-in policies for AKS.
> - (Cluster and Workload) **Make workloads more sustainable by optimizing cost.** Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md), requires combining efforts around _cost optimization_, _reducing carbon emissions_, and _optimizing energy consumption_. Optimizing the application's cost is the initial step in making workloads more sustainable.
>
>   Learn how to build sustainable and efficient AKS workloads, in [Sustainable software engineering principles in Azure Kubernetes Service (AKS)](/azure/aks/concepts-sustainable-software-engineering).

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster and Workload) Review [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-tutorial-aks-portal).| Azure Chaos Studio can help simulate faults and trigger disaster recovery situations.|
|(Cluster) Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions. <br><br>Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) to route traffic globally across AKS clusters.|A multiregion strategy helps maximize availability and provide business continuity.|
|(Cluster) Operationalize clusters and pods configuration standards with [Azure Policy](/azure/aks/use-azure-policy). | Azure Policy can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. |
| (Workload) Use platform capabilities in your release engineering process.|Kubernetes and ingress controllers support many advanced deployment patterns for inclusion in your release engineering process. Consider patterns like blue-green deployments or canary releases.|
|(Cluster and Workload) For mission-critical workloads, use [stamp-level blue/green deployments](/azure/well-architected/mission-critical/mission-critical-deployment-testing#ephemeral-blue-green-deployments).|Stamp-level blue/green deployments approach increases confidence in releasing changes and enables zero-downtime upgrades because compatibilities with downstream dependencies like the Azure platform, resource providers, and IaC modules can be validated.|
|xx(Workload) Use [Kubernetes Event Driven Autoscaling](/azure/aks/keda-about) (KEDA).|KEDA allows your applications to scale based on events, like the number of events being processed. Choose from a rich catalog of 50+ KEDA scalers.|

## Performance efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Azure Kubernetes Service.

> [!div class="checklist"]
>
> - (Cluster and Workload) **Conduct capacity planning.** Perform and iterate on a detailed capacity plan exercise that includes SKU, autoscale settings, IP addressing, and failover considerations.
>
>   After formalizing your capacity plan, it should be frequently updated by continuously observing the resource utilization of the cluster.
> - (Cluster) **Define a cluster scaling strategy.** Configure scaling to ensure that resources are adjusted efficiently to meet workload demands without overuse or waste.
>
>   Review the [Scaling and Partitioning](/azure/well-architected/performance-efficiency/scale-partition) guide to understand the various aspects of scaling configuration.
>
>   Use AKS features like cluster autoscaling and Horizontal pod autoscaler to dynamically meet your workload needs with reduced operational burden.
>
>   Optimize your workload to operate and deploy efficiently in a container.
> - (Cluster and Workload) **Conduct performance testing.** Perform ongoing load testing activities that exercise both the pod and cluster autoscaler. Compare results against the performance targets and and established baselines.
> - (Cluster and Workload) **Scale workloads and flows independently.** Follow the guidance in [Optimize workload design using flows](/azure/well-architected/design-guides/optimize-workload-using-flows) to identify and prioritize your flows. Separate workloads and flows into different node pools allowing independent scalling.

### Recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster) Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to workload demands.<br><br>Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.|The ability to automatically scale up or down the number of nodes and the number of pods in your AKS cluster lets you run an efficient, cost-effective cluster.|
|(Cluster and Workload) Separate workloads into different node pools and consider [scaling](/azure/aks/scale-cluster) user node pools.|Unlike System node pools that always require running nodes, user node pools allow you to scale up or down.|
|(Workload) Use AKS [advanced scheduler features](/azure/aks/operator-best-practices-advanced-scheduler) to implement advanced balancing of resources for workloads that require them. | As you manage AKS clusters, you often need to isolate teams and workloads. Advanced features provided by the Kubernetes scheduler let you control which pods can be scheduled on certain nodes, and how multi-pod applications can be appropriately distributed across the cluster.|
|(Workload) Use [Kubernetes Event-driven Autoscaling (KEDA)](/training/modules/aks-app-scale-keda/) to build a meaningful auto scale ruleset based on signals that are specific to your workload.|Not all scale decisions can be derived from CPU or memory metrics. Often scale considerations will come from more complex or even external data points. KEDA allows your applications to scale based on events, such as the number of messages in a queue or the length of a topic lag.|

## Azure policies

Azure provides an extensive set of built-in policies related to Azure Kubernetes Service that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant. In addition to the built-in Azure Policy definitions, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes.

Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

#### Cluster policies

- Clusters have readiness or liveness health probes configured for your pod spec.
- Microsoft Defender for Cloud-based policies
- Authentication mode and configuration policies (Microsoft Entra ID, RBAC, disable local authentication)
- API Server network access policies, including private cluster
- GitOps configuration policies
- Diagnostics settings policies
- AKS version restrictions
- Prevent command invoke

#### Cluster and workload policies

- Kubernetes cluster pod security initiatives Linux-based workloads
- Include pod and container capability policies such as AppArmor, sysctl, security caps, SELinux, seccomp, privileged containers, automount cluster API credentials
- Mount, volume drivers, and filesystem policies
- Pod/Container networking policies, such as host network, port, allowed external IPs, HTTPs, and internal load balancers
- Namespace deployment restrictions
- CPU and memory resource limits

For comprehensive governance, review the [Azure Policy built-in definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes) and other policies that might impact the security of the compute layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of [Azure offering].

- [Reliability](/azure/advisor/advisor-reference-reliability-recommendations#azure-kubernetes-service-aks)
- [Security](/azure/advisor/advisor-security-recommendations)
- [Cost Optimization](/azure/advisor/advisor-reference-cost-recommendations#compute)
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#containers)'
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#containers)

## Related resources

### Azure Architecture Center guidance

- [AKS baseline architecture](/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks)
- [Advanced AKS microservices architecture](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices-advanced)
- [AKS cluster for a PCI-DSS workload](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro)
- [AKS baseline for multiregion clusters](/azure/architecture/reference-architectures/containers/aks-multi-region/aks-multi-cluster)

### Cloud Adoption Framework guidance

- [AKS Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/aks/landing-zone-accelerator)

## Next steps

> [!div class="nextstepaction"]
> [Quickstart: Deploy an Azure Kubernetes Service (AKS) cluster using the Azure CLI](/azure/aks/kubernetes-walkthrough)

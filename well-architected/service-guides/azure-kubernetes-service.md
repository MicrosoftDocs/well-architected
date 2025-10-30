---
title: Architecture Best Practices for Azure Kubernetes Service (AKS)
description: Learn how to use Azure Kubernetes Service (AKS) features to boost reliability, security, and scalability. Streamline your workload operations and control costs. 
author: schaffererin
ms.author: schaffererin
ms.topic: concept-article
ms.date: 10/13/2025
ms.service: azure-waf
ms.subservice: waf-service-guide
products:
  - azure-kubernetes-service
azure.category:
  - containers
---

# Architecture best practices for Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) is a managed Kubernetes service that you can use to deploy and manage containerized applications. Similar to other managed services, AKS offloads much of the operational overhead to Azure while providing high availability, scalability, and portability features to the workload.

This article assumes that, as an architect, you reviewed the [compute decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) and chose AKS as the compute for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Azure Well-Architected Framework pillars](/azure/well-architected/pillars).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- AKS

When you discuss the Well-Architected Framework pillars' best practices for AKS, it's important to distinguish between *cluster* and *workload*. Cluster best practices are a shared responsibility between the cluster admin and their resource provider, while workload best practices are the domain of a developer. This article has considerations and recommendations for each of these roles.

> [!NOTE]
> The following pillars include a **design checklist** and a **list of recommendations** that indicate whether each choice is applicable to *cluster* architecture, *workload* architecture, or both.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](/azure/well-architected/resiliency/principles) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the features of AKS and its dependencies. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Cluster) **Build redundancy to improve resiliency.** Use availability zones for your AKS clusters as part of your resiliency strategy to increase availability when you deploy to a single region. Many Azure regions provide availability zones. The zones are close enough to have low-latency connections among them, but far enough apart to reduce the likelihood that local outages will affect more than one zone.
>
>   For critical workloads, deploy multiple clusters across different Azure regions. By geographically distributing AKS clusters, you can achieve higher resiliency and minimize the effects of regional failures. A multiregion strategy helps maximize availability and provide business continuity.
>   Internet-facing workloads should use [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) to route traffic globally across AKS clusters. For more information, see [Multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment).
>
>    Plan the IP address space to ensure that your cluster can reliably scale and handle failover traffic in multiple-cluster topologies.
>
> - (Cluster and workload) **Monitor reliability and overall health indicators of the cluster and workloads.** Collect logs and metrics to monitor workload health, identify performance and reliability trends, and troubleshoot problems.
>    Review [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers) and the Well-Architected [Health modeling for workloads](/azure/well-architected/design-guides/health-modeling) guide for help designing the reliability and health monitoring solution for your AKS solution.
>
>   Ensure that workloads are built to support horizontal scaling and report application readiness and health.
> - (Cluster and workload) **Host application pods in user node pools.** By isolating system pods from application workloads, you help ensure that AKS essential services are unaffected by the resource demands or potential problems caused by a workload that runs user node pools.
>
>   Ensure that your workload runs on user node pools and choose the right size SKU. At a minimum, include two nodes for user node pools and three nodes for the system node pool.
> - (Cluster and workload) **Factor the AKS uptime service-level agreement (SLA) into your availability and recovery targets.** To define the reliability and recovery targets for your cluster and workload, follow the guidance in [Recommendations for defining reliability targets](/azure/well-architected/reliability/metrics). Then formulate a design that meets those targets.
>
> -  (Cluster and workload) Protect the AKS cluster service using Azure Backup by storing recovery points in a Backup vault and perform restore during any disaster scenario. To back up and restore the containerized applications and data running in AKS clusters, follow the guidance in the AKS backup overview for configuring protection.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster and workload) Control pod scheduling by using node selectors and affinity. <br><br>In AKS, the Kubernetes scheduler can logically isolate workloads by hardware in the node. Unlike [tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/), pods that don't have a matching node selector can be scheduled on labeled nodes, but priority is given to pods that define the matching node selector.| Node affinity results in more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|(Cluster) Choose the appropriate network plugin based on network requirements and cluster sizing. <br><br>Different network plugins provide varying levels of functionality. Azure Container Networking Interface (Azure CNI) is required for specific scenarios, such as Windows-based node pools, some networking requirements, and Kubernetes network policies. <br><br>For enhanced IP address management, consider [Azure CNI with static block allocation](/azure/aks/configure-azure-cni-static-block-allocation) which improves network planning and reduces IP address exhaustion while maintaining network security integration with firewall rules and network policies. <br><br> For more information, see [Kubenet versus Azure CNI](/azure/aks/concepts-network#compare-network-models).|The right network plugin can help ensure better compatibility and performance. Static block allocation enables consistent IP ranges for better security policy configuration and capacity planning.|
|(Cluster and workload) Use the [AKS uptime SLA](/azure/aks/free-standard-pricing-tiers#uptime-sla-terms-and-conditions) for production-grade clusters.|The workload can support higher availability targets because of the higher availability guarantees of the Kubernetes API server endpoint for AKS clusters.|
|(Cluster) Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate datacenters.<br><br>If colocality requirements exist, use a regular virtual machine scale sets-based AKS deployment into a single zone or use [proximity placement groups](/azure/aks/reduce-latency-ppg) to minimize internode latency.|By spreading node pools across multiple zones, nodes in one node pool continue to run even if another zone goes down.|
|(Cluster and workload) Define pod resource requests and limits in application deployment manifests. Enforce those limits by using Azure Policy.| Container CPU and memory resource limits are necessary to prevent resource exhaustion in your Kubernetes cluster.|
|(Cluster and workload) Keep the system node pool isolated from application workloads.<br><br>System node pools require a virtual machine (VM) SKU of at least 2 vCPUs and 4 GB of memory. We recommend that you use 4 vCPU or more. For more information, see [System and user node pools](/azure/aks/use-system-pools#system-and-user-node-pools).|The system node pool hosts critical system pods that are essential for the control plane of your cluster. By isolating these system pods from application workloads, you help ensure that the essential services are unaffected by the resource demands or potential problems caused by a workload.|
|(Cluster and workload) Separate applications to dedicated node pools based on specific requirements and be intentional about that segmentation. <br><br> Avoid large numbers of node pools to reduce management overhead. For example, you can colocate [multiple Virtual Machines SKUs in a single node pool](/azure/aks/use-multiple-node-pools) as long as they meet the same requirement. |Applications can share the same configuration and need GPU-enabled VMs, CPU or memory-optimized VMs, or the ability to scale to zero. By dedicating node pools to specific applications, you can help ensure that each application gets the resources it needs without overprovisioning or underutilizing resources.|
|(Cluster) Use a [NAT gateway](/azure/aks/nat-gateway) for clusters that run workloads that make many concurrent outbound connections.|Azure NAT Gateway supports reliable egress traffic at scale and helps you avoid reliability problems by applying Azure Load Balancer limitations to high concurrent outbound traffic.|
| (Cluster and workload) Use Azure Backup to [protect AKS cluster and restore](/azure/backup/azure-kubernetes-service-backup-overview) to alternate regions during disaster.  Azure Backup supports the backup and restore operations of containerized applications and data running for both cluster state and application data.<br><br> You can [use the backups in a regional disaster scenario and recover backups](/azure/backup/azure-kubernetes-service-cluster-restore).  | Azure Backup with Azure Kubernetes Service (AKS) offers a fully managed, scalable, secure, and cost-effective solution. Enhances the reliability of the workload without the complexities of setting up and maintaining backup infrastructure. |

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](/azure/well-architected/security/security-principles) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of AKS.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Familiarize yourself with [AKS security concepts](/azure/aks/concepts-security) and evaluate the security hardening recommendations based on the [CIS Kubernetes benchmark](/azure/aks/cis-kubernetes). Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
>
> - (Cluster) **Integrate with  Microsoft Entra ID for [identity and access mangement](/azure/well-architected/security/identity-access#the-role-of-an-identity-provider).** Centralize identity management for your cluster by using Microsoft Entra ID. Any change in user account or group status is automatically updated in access to the AKS cluster. [Establish identity as the primary security perimeter](/azure/well-architected/security/segmentation#establish-identity-as-the-primary-security-perimeter). The developers and application owners of your Kubernetes cluster need access to different resources.
>
>   Use Kubernetes role-based access control (RBAC) with Microsoft Entra ID for [least privilege access](/azure/aks/azure-ad-rbac). Protect configuration and secrets by minimizing the allocation of administrator privileges.
>
> - (Cluster) **Integrate with security monitoring and security information and event management tools.** Use Microsoft Defender for Containers with [Microsoft Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your clusters and the workloads that run on them. Enable [AKS connector for Microsoft Sentinel](https://docs.azure.cn/en-us/sentinel/data-connectors/azure-kubernetes-service-aks) to stream your AKS diagnostics logs into Microsoft Sentinel.
>
> - (Cluster and workload) **Implement segmentation and network controls.** To prevent data exfiltration, ensure that only authorized and safe traffic is allowed, and contain the blast radius of a security breach.
>
>   Consider using a private AKS cluster to help ensure that cluster-management traffic to your API server remains on your private network. Or use the API server allowlist for public clusters.
>
> - (Workload) **Use a web application firewall (WAF) to scan incoming traffic for potential attacks.** WAF can detect and mitigate threats in real time to help block malicious traffic before it reaches your applications. It provides robust protection against common web-based attacks, such as SQL injection, cross-site scripting, and other Open Web Application Security Project vulnerabilities. Some load balancers, such as [Azure Application Gateway](/azure/web-application-firewall/ag/ag-overview) or [Azure Front Door](/azure/web-application-firewall/afds/afds-overview) have an integrated WAF.
>
> - (Workload) **Maintain a hardened workload's software supply chain.**  Ensure that your continuous integration and continuous delivery pipeline is hardened with container-aware scanning.
>
> - (Cluster and workload) **Implement extra protection for specialized secure workloads.** If your cluster needs to run a sensitive workload, you might need to deploy a private cluster. Here are some examples:
>   - Payment Card Industry Data Security Standard (PCI-DSS 3.2.1):  [AKS regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro)
>   - DoD Impact Level 5 (IL5) support and requirements with AKS: [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-kubernetes-service). 
>
> - (Cluster) **Leverage enhanced security from cluster extensions managed in the control plane.** The [Extension Manager](/azure/aks/cluster-extensions) runs in the Azure-managed control plane, reducing attack surface for worker nodes and simplifying cluster networking requirements while maintaining extension functionality. This centralized extension management eliminates the need to secure and maintain extension components on customer worker nodes. 

### Configuration recommendations

|Recommendation|Benefit|
|----------------------------------|-----------|
|(Cluster) Use [managed identities](/azure/aks/use-managed-identity) on the cluster.|You can avoid the overhead associated with managing and rotating service principles.|
|(Workload) Use [Microsoft Entra Workload ID with AKS](/azure/aks/workload-identity-overview) to access Microsoft Entra protected resources, such as Azure Key Vault and Microsoft Graph, from your workload. | Use AKS Workload IDs to protect access to Azure resources by using Microsoft Entra ID RBAC without having to manage credentials directly in your code.| 
|(Cluster) Use Microsoft Entra ID to [authenticate with Azure Container Registry from AKS](/azure/aks/cluster-container-registry-integration?tabs=azure-cli).|By using Microsoft Entra ID, AKS can authenticate with Container Registry without the use of `imagePullSecrets` secrets.|
|(Cluster) Secure network traffic to your API server by using [private AKS cluster](/azure/aks/private-clusters) if the workload requirements require higher levels of segmentation.| By default, network traffic between your node pools and the API server travels the Microsoft backbone network. By using a private cluster, you can help ensure that network traffic to your API server remains on the private network only.|
|(Cluster) For public AKS clusters, use API server-authorized IP address ranges. Include sources like the public IP addresses of your deployment build agents, operations management, and node pools' egress point, such as Azure Firewall.|When you use public clusters, you can significantly reduce the attack surface of your AKS cluster by limiting the traffic that can reach the API server of your clusters.|
|(Cluster) Protect the API server by using Microsoft Entra ID RBAC.<br><br>[Disable local accounts](/azure/aks/managed-aad#disable-local-accounts) to enforce all cluster access by using Microsoft Entra ID-based identities.|Securing access to the Kubernetes API server is one of the most important things that you can do to secure your cluster. Integrate Kubernetes RBAC with Microsoft Entra ID to control access to the API server. |
|(Cluster) Use [Azure network policies](/azure/aks/use-network-policies) or [Calico](/azure/aks/hybrid/concepts-container-networking#kubernetes-networks).| By using policies, you can secure and control network traffic between pods in a cluster. Calico provides a richer set of capabilities, including policy ordering and priority, deny rules, and more flexible match rules.|
|(Cluster) Secure clusters and pods by using [Azure Policy](/azure/aks/use-azure-policy). | Azure Policy can help apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and detect whether anything is running against company policy. |
|(Cluster) Secure container access to resources. Limit access to actions that containers can perform. Provide the least number of permissions, and avoid the use of root or privileged escalation.<br><br>For Linux based containers, see [Security container access to resources using built-in Linux security features](/azure/aks/secure-container-access).|By restricting permissions and avoiding the use of root or privileged escalation, you help reduce the risk of security breaches. You can help ensure that, even if a container is compromised, the potential damage is minimized.|
|(Cluster) Control cluster egress traffic by ensuring that your cluster's outbound traffic passes through a network security point such as [Azure Firewall](/azure/aks/limit-egress-traffic#restrict-egress-traffic-using-azure-firewall) or an [HTTP proxy](/azure/aks/http-proxy).|By routing outbound traffic through Azure Firewall or an HTTP proxy, you can help enforce security policies that prevent unauthorized access and data exfiltration. This approach also simplifies the administration of security policies and makes it easier to enforce consistent rules across your entire AKS cluster.|
|(Cluster) Configure [HTTP proxy support](/azure/aks/http-proxy) for existing AKS clusters in proxy-dependent environments. <br><br> Enable HTTP proxy configuration for both AKS nodes and pods to integrate with existing enterprise network security architectures and compliance requirements. | Critical for financial services and other regulated industries requiring all internet traffic to flow through corporate HTTP proxies for security scanning and compliance monitoring. Enables seamless integration with existing enterprise network security infrastructure. |
|(Cluster) Implement [custom certificate authority support](/azure/aks/custom-certificate-authority) to establish trust relationships between AKS clusters and enterprise PKI infrastructure. <br><br> Configure custom CA to enable trust for private registries, proxies, and firewalls within existing organizational certificate management frameworks. | Essential for enterprises with internal certificate authority infrastructure. Enables AKS clusters to trust organizational private registries and internal services while maintaining corporate security policies and compliance requirements. |
|(Cluster) Use the open-source [Microsoft Entra Workload ID](https://github.com/Azure/azure-workload-identity) and [Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) with Key Vault.| These features help you protect and rotate secrets, certificates, and connection strings in Key Vault by using strong encryption. They provide an access audit log and keep core secrets out of the deployment pipeline.|
|(Cluster) Use [Microsoft Defender for Containers](/azure/defender-for-cloud/defender-for-containers-introduction). |Microsoft Defender for Containers helps you monitor and maintain the security of your clusters, containers, and their applications.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](/azure/well-architected/cost-optimization/principles) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to AKS and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
>
> - (Cluster) **Include the [pricing tiers for AKS](/azure/aks/free-standard-pricing-tiers) in your cost model.** To estimate costs, use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and test different configuration and payment plans in the calculator.
>   
> - (Cluster) **Get the best rates for your workload.** Use the appropriate VM SKU for each node pool because it directly affects the cost to run your workloads. Choosing a high-performance VM without proper utilization can lead to wasteful spending. Selecting a less powerful VM can cause performance problems and increased downtime.
>
>   If you properly planned for capacity and your workload is predictable and will exist for an extended period of time, sign up for [Azure Reservations](/azure/aks/faq#can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes) or a [savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview#determine-your-savings-plan-commitment) to reduce your resource costs.
>
>   Choose [Azure Spot Virtual Machines](/azure/virtual-machines/spot-vms) to use unutilized Azure capacity with significant discounts. These discounts can reach up to 90% of pay-as-you-go prices. If Azure needs capacity back, the Azure infrastructure evicts the Spot nodes.
>
>   If you run AKS on-premises or at the edge, you can also use [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) to reduce costs when you run containerized applications in those scenarios.
>
> - (Cluster and workload) **Optimize workload components costs.** Choose the most cost-effective region for your workload. Evaluate the cost, latency, and compliance requirements to ensure that you run your workload cost-effectively and that it doesn't affect your customers or create extra networking charges. The region where you deploy your workload in Azure can significantly affect the cost. Because of many factors, the cost of resources varies for each region in Azure.
>
>   Maintain small and optimized images to help reduce costs because new nodes need to download those images. User request failures or timeouts when the application is starting up can lead to overprovisioning. Build images in a way that allows the container to start as soon as possible to help avoid failures and timeouts.
>
>   Review the Cost Optimization recommendations in [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers#cost-optimization) to determine the best monitoring strategy for your workloads. Analyze performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
>
> - (Cluster and workload) **Optimize workload scaling costs.** Consider alternative vertical and horizontal scaling configurations to reduce scaling costs while still meeting all workload requirements. Use autoscalers to scale in when workloads are less active.
>
> - (Cluster and workload) **Collect and analyze cost data.** The foundation of enabling cost optimization is the spread of a cost-saving cluster. Develop a cost-efficiency mindset that includes collaboration between finance, operations, and engineering teams to drive alignment on cost-saving goals and bring transparency to cloud costs.

### Configuration recommendations

| Recommendation | Benefit |
|----------------------------------|-----------|
|(Cluster and workload) Align [AKS SKU selection](/azure/aks/free-standard-pricing-tiers) and managed disk size with workload requirements.|Matching your selection to your workload demands helps ensure that you don't pay for unneeded resources.|
|(Cluster) Choose the right VM instance types for your [AKS node pools](/azure/aks/create-node-pools).<br><br>To determine the right VM instance types, consider workload characteristics, resource requirements, and availability needs.|Selecting the right VM instance type is crucial because it directly affects the cost to run applications on AKS. Choosing a high-performance instance without proper utilization can lead to wasteful spending. Choosing a less powerful instance can lead to performance problems and increased downtime.|
|(Cluster) Choose VMs based on the more power efficient Azure Resource Manager architecture. AKS supports [creating Arm64 node pools](/azure/aks/create-node-pools#arm64-node-pools) and a mix of Intel and Resource Manager architecture nodes within a cluster.| The Arm64 architecture provides a better price-to-performance ratio because of its lower power utilization and efficient compute performance. These capabilities can bring better performance at a lower cost.|
|(Cluster) Enable the [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically reduce the number of agent nodes in response to excess resource capacity. |Automatically scaling down the number of nodes in your AKS cluster lets you run an efficient cluster when demand is low and scale up when demand increases.|
|(Cluster) Enable [node autoprovisioning](/azure/aks/node-autoprovision?tabs=azure-cli) to automate VM SKU selection. |Node autoprovision simplifies the SKU selection process and decides, based on pending pod resource requirements, the optimal VM configuration to run workloads in the most efficient and cost-effective manner. |
|(Workload) Use [HorizontalPodAutoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other metrics.| Automatically scaling down the number of pods when demand is low and scaling out when demand increases results in a more cost-effective operation of your workload.|
|(Workload) Use [VerticalPodAutoscaler (preview)](/azure/aks/vertical-pod-autoscaler) to rightsize your pods and dynamically [set requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) based on historic usage.|By setting resource requests and limits on containers for each workload, VerticalPodAutoscaler frees up CPU and memory for other pods and helps ensure effective utilization of your AKS clusters.|
|(Cluster) Configure the [AKS cost analysis add-on](/azure/aks/cost-analysis). | The cost analysis cluster extension enables you to obtain granular insight into costs that are associated with various Kubernetes resources in your clusters or namespaces.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](/azure/well-architected/operational-excellence/principles) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment. See [AKS best practices](/azure/aks/best-practices) and [Day-2 operations guide](/azure/architecture/operator-guides/aks/day-2-operations-guide) to learn about key considerations to understand and implement.

> [!div class="checklist"]
>
> - (Cluster) **Implement an infrastructure as code (IaC) deployment approach.** Use a declarative, template-based deployment approach by using Bicep, Terraform, or similar tools. Make sure that all deployments are repeatable, traceable, and stored in a source code repo. For more information, see the [quickstarts](/azure/aks/learn/quick-kubernetes-automatic-deploy) in the AKS product documentation.
>
> - (Cluster and workload) **Automate infrastructure and workload deployments.** Use standard software solutions to manage, integrate, and automate the deployment of your cluster and workloads. Integrate deployment pipelines with your source control system and incorporate automated tests.
>
>   Build an automated process to help ensure that your clusters are bootstrapped with the necessary cluster-wide configurations and deployments. This process is typically performed by using GitOps.
>
>   Use a repeatable and automated deployment processes for your workload within your software development lifecycle.
>   
> - (Cluster and workload) **Implement a comprehensive monitoring strategy.** Collect logs and metrics to monitor the health of the workload, identify trends in performance and reliability, and troubleshoot problems. Review the [Best practices for monitoring Kubernetes with Azure Monitor](/azure/azure-monitor/best-practices-containers) and the Well-Architected [Recommendations for designing and creating a monitoring system](/azure/well-architected/operational-excellence/observability) to determine the best monitoring strategy for your workloads.
>
>    Enable diagnostics settings to ensure that control plane or core API server interactions are logged.
>
>    The workload should be designed to emit telemetry that can be collected, which should also include liveness and readiness statuses.
>   
> - (Cluster and workload) **Implement testing in production strategies.** Testing in production uses real deployments to validate and measure an application's behavior and performance in the production environment. Use chaos engineering practices that target Kubernetes to identify application or platform reliability issues.
>
>   [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-tutorial-aks-portal) can help simulate faults and trigger disaster recovery situations.
>
> - (Cluster and workload) **Enforce workload governance.** Azure Policy helps ensure consistent compliance with organizational standards, automates policy enforcement, and provides centralized visibility and control over your cluster resources.
>
>   Review the [Azure policies](#azure-policies) section to learn more about the available built-in policies for AKS.
>
> - (Cluster and workload) **Use [stamp-level, blue-green deployments](/azure/well-architected/mission-critical/mission-critical-deployment-testing#ephemeral-blue-green-deployments) for mission-critical workloads.** A stamp-level, blue-green deployment approach can increase confidence in releasing changes and enables zero-downtime upgrades because compatibilities with downstream dependencies like the Azure platform, resource providers, and IaC modules can be validated.
>
>    Kubernetes and ingress controllers support many advanced deployment patterns for inclusion in your release engineering process. Consider patterns like blue-green deployments or canary releases.
>
> - (Cluster and workload) **Make workloads more sustainable.** Making workloads more [sustainable and cloud efficient](../sustainability/sustainability-get-started.md) requires combining efforts around _cost optimization_, _reducing carbon emissions_, and _optimizing energy consumption_. Optimizing the application's cost is the initial step in making workloads more sustainable.
>
>   See [Sustainable software engineering principles in AKS](/azure/aks/concepts-sustainable-software-engineering) to learn how to build sustainable and efficient AKS workloads.

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster) Operationalize cluster and pod configuration standards by using [Azure policies for AKS](/azure/aks/use-azure-policy). | Azure policies for AKS can help you apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. Use policies to define the permissions granted to pods and ensure compliance with company policies.|
|(Cluster) Configure [Azure CNI static block allocation](/azure/aks/configure-azure-cni) for predictable pod subnet allocation and improved IP address management in AKS clusters. <br><br> Implement static block allocation to provide predictable IP address assignment for Kubernetes pods within defined subnet ranges, enabling integration with existing network security policies. | Essential for development teams running multiple AKS clusters needing predictable IP address allocation to integrate with existing network security policies and firewall rules. Enhances operational excellence through predictable network addressing and simplified IP management. |
|(Workload) Use [Kubernetes Event Driven Autoscaler (KEDA)](/azure/aks/keda-about).|KEDA allows your applications to scale based on events, like the number of events being processed. You can choose from a rich catalog of more than 50 KEDA scalers.|
|(Cluster) Use the [KAITO AI Toolchain Operator](/azure/aks/ai-toolchain-operator) for efficient deployment and management of AI model serving workloads. | KAITO simplifies AI model deployment and scaling on Kubernetes while providing optimized infrastructure for machine learning inference workloads. Data science teams can deploy and scale large language models or other AI models on AKS using KAITO, which automatically handles GPU resources, model serving infrastructure, and scaling policies without manual Kubernetes configuration, reducing operational complexity.|
|(Cluster) Enable [AKS Automatic](/azure/aks/intro-aks-automatic) for fully managed Kubernetes experience with automated cluster management. | AKS Automatic reduces operational overhead for Kubernetes cluster management by automating routine tasks while ensuring optimal configuration and security posture. Development teams can deploy applications to AKS without managing cluster configuration, updates, or optimization, allowing them to focus on application development while ensuring their Kubernetes infrastructure follows best practices automatically.|

## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](/azure/well-architected/performance-efficiency/principles) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for AKS.

> [!div class="checklist"]
>
> - (Cluster and workload) **Conduct capacity planning.** Perform and iterate on a detailed capacity plan exercise that includes SKU, autoscale settings, IP addressing, and failover considerations.
>
>   After you formalize your capacity plan, frequently update the plan by continuously observing the resource utilization of the cluster.
> - (Cluster) **Define a scaling strategy.** Configure scaling to ensure that resources are adjusted efficiently to meet workload demands without overuse or waste. Use AKS features like cluster autoscaling and HorizontalPodAutoscaler to dynamically meet your workload needs with less strain on operations. Optimize your workload to operate and deploy efficiently in a container.
>
>   Review the [Scaling and partitioning](/azure/well-architected/performance-efficiency/scale-partition) guide to understand the various aspects of scaling configuration.
>
> - (Cluster and workload) **Conduct performance testing.** Perform ongoing load testing activities that exercise both the pod and cluster autoscaler. Compare results against the performance targets and the established baselines.
>   
> - (Cluster and workload) **Scale workloads and flows independently.** Separate workloads and flows into different node pools to allow independent scaling. Follow the guidance in [Optimize workload design using flows](/azure/well-architected/design-guides/optimize-workload-using-flows) to identify and prioritize your flows. 

### Configuration recommendations

| Recommendation | Benefit |
|--------|----|
|(Cluster) Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to workload demands.<br><br>Use the [HorizontalPodAutoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other metrics.|The ability to automatically scale up or scale down the number of nodes and the number of pods in your AKS cluster lets you run an efficient, cost-effective cluster.|
|(Cluster and workload) Separate workloads into different node pools and consider [scaling user node pool](/azure/aks/scale-cluster)s.|Unlike system node pools that always require running nodes, user node pools allow you to scale up or scale down.|
|(Workload) Use AKS [advanced scheduler features](/azure/aks/operator-best-practices-advanced-scheduler) to implement advanced balancing of resources for workloads that require them. | As you manage AKS clusters, you often need to isolate teams and workloads. Advanced features that the Kubernetes scheduler provides let you control which pods can be scheduled on certain nodes. They also let you control how multipod applications can be appropriately distributed across the cluster.|
|(Workload) Use [KEDA](/training/modules/aks-app-scale-keda/) to build a meaningful autoscale ruleset based on signals that are specific to your workload.|Not all scale decisions can be derived from CPU or memory metrics. Scale considerations often come from more complex or even external data points. KEDA allows your applications to scale based on events, such as the number of messages in a queue or the length of a topic lag.|
|(Cluster) Enable [Azure Monitor High Scale mode for Container Insights](/azure/azure-monitor/containers/container-insights-high-scale#enable-high-scale-mode-for-monitoring-add-on) for large-scale Kubernetes deployments with hundreds of nodes. High Scale mode optimizes monitoring performance and reduces resource consumption while maintaining comprehensive observability.|High Scale mode enables efficient monitoring of large-scale Kubernetes deployments by reducing agent resource overhead and improving data collection performance. This optimization is essential for enterprise environments running hundreds of nodes where standard monitoring approaches can impact cluster performance and increase costs.|

## Azure policies

Azure provides an extensive set of built-in policies related to AKS that apply to the Azure resource, like typical Azure policies and the Azure Policy add-on for Kubernetes, and within the cluster. Many of the Azure resource policies come in both *Audit/Deny* and a *Deploy If Not Exists* variants. In addition to the built-in Azure Policy definitions, you can create custom policies for both the AKS resource and for the Azure Policy add-on for Kubernetes.

Some of the recommendations in this article can be audited through Azure Policy. For example, you can check the following cluster policies:

- Clusters have readiness or liveness health probes configured for your pod spec.
- Microsoft Defender for cloud-based policies.
- Authentication mode and configuration policies, like Microsoft Entra ID, RBAC, and disable local authentication.
- API server network access policies, including private cluster.
- GitOps configuration policies.
- Diagnostics settings policies.
- AKS version restrictions.
- Prevent command invoke.

You can also check the following cluster and workload policies:

- Kubernetes cluster pod security initiatives for Linux-based workloads.
- Include pod and container capability policies, such as AppArmor, sysctl, security caps, SELinux, seccomp, privileged containers, and automount cluster API credentials.
- Mount, volume drivers, and filesystem policies.
- Pod and container networking policies, such as host network, port, allowed external IPs, HTTPs, and internal load balancers.
- Namespace deployment restrictions.
- CPU and memory resource limits.

For comprehensive governance, review the [Azure Policy built-in definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes) and other policies that might affect the security of the compute layer.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [AKS baseline architecture](/azure/architecture/reference-architectures/containers/aks/baseline-aks).

## Related content

Consider the following articles as resources that demonstrate the recommendations highlighted in this article.

- [AKS baseline architecture](/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks)
- [Advanced AKS microservices architecture](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices-advanced)
- [AKS cluster for a PCI-DSS workload](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro)
- [AKS baseline for multiregion clusters](/azure/architecture/reference-architectures/containers/aks-multi-region/aks-multi-cluster)
- [AKS Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/aks/landing-zone-accelerator)

Build implementation expertise by using the following product documentation:

-  [AKS product documentation](/azure/aks)



<!-- Updated: October 13, 2025 for Azure Update 503263, 503235 -->
<!-- Updated: August 17, 2025 for Azure Update 498166, 491880 -->
<!-- Updated: August 17, 2025 for Azure Update 498166 -->
<!-- Updated: August 17, 2025 for Azure Update 498258 -->
<!-- Updated: August 17, 2025 for Azure Update 498242 -->
<!-- Updated: October 13, 2025 for Azure Update 503034 -->



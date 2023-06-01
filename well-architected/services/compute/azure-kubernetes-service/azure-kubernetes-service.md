---
title: Azure Well-Architected Framework review for Azure Kubernetes Service (AKS)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Kubernetes Service (AKS).
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.custom:
  - ignite-2022
---

# Azure Well-Architected Framework review - Azure Kubernetes Service (AKS)

This article provides architectural best practices for Azure Kubernetes Service (AKS). The guidance is based on the five pillars of architecture excellence:

- Reliability
- Security
- Cost optimization
- Operational excellence
- Performance efficiency

We assume that you understand system design principles, have working knowledge of Azure Kubernetes Service, and are well versed with its features. For more information, see [Azure Kubernetes Service](/azure/aks/intro-kubernetes).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with the [baseline architecture for an Azure Kubernetes Service (AKS) cluster](/azure/architecture/reference-architectures/containers/aks/baseline-aks) and [Microservices architecture on Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices). Also review the [AKS landing zone accelerator](/azure/cloud-adoption-framework/scenarios/aks/enterprise-scale-landing-zone), which provides an architectural approach and reference implementation to prepare landing zone subscriptions for a scalable Azure Kubernetes Service (AKS) cluster.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

When discussing reliability with Azure Kubernetes Service, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is a shared responsibility between the cluster admin and their resource provider, while workload reliability is the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** For critical workloads, use [availability zones](/azure/aks/availability-zones) for your AKS clusters.
> - **Cluster architecture:** Plan the IP address space to ensure your cluster can reliably scale, including handling of failover traffic in multi-cluster topologies.
> - **Cluster architecture:** Enable [Container insights](/azure/azure-monitor/containers/container-insights-overview) to monitor your cluster and configure alerts for reliability-impacting events.
> - **Workload architecture:** Ensure workloads are built to support horizontal scaling and report application readiness and health.
> - **Cluster and workload architectures:** Ensure your workload is running on user node pools and chose the right size SKU. At a minimum, include two nodes for user node pools and three nodes for the system node pool.
> - **Cluster architecture:** Use the AKS Uptime SLA to meet availability targets for production workloads.

### AKS configuration recommendations

Explore the following table of recommendations to optimize your AKS configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Control pod scheduling using node selectors and affinity.|Allows the Kubernetes scheduler to logically isolate workloads by hardware in the node. Unlike [tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/), pods without a matching node selector can be scheduled on labeled nodes, which allows unused resources on the nodes to consume, but gives priority to pods that define the matching node selector. Use node affinity for more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|**Cluster architecture:** Ensure proper selection of network plugin based on network requirements and cluster sizing.|Azure CNI is required for specific scenarios, for example, Windows-based node pools, specific networking requirements and Kubernetes Network Policies. Reference [Kubenet versus Azure CNI](/azure/aks/concepts-network#compare-network-models) for more information.|
|**Cluster and workload architectures:** Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production grade clusters.|The AKS Uptime SLA guarantees:<br> - `99.95%` availability of the Kubernetes API server endpoint for AKS Clusters that use Azure Availability Zones, or <br> - `99.9%` availability for AKS Clusters that don't use Azure Availability Zones.|
|**Cluster and workload architectures:** Configure monitoring of cluster with [Container insights](/azure/azure-monitor/containers/container-insights-overview). | Container insights help monitor the health and performance of controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Integration with Prometheus enables collection of application and workload metrics. |
|**Cluster architecture:** Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.|By spreading node pools across multiple zones, nodes in one node pool will continue running even if another zone has gone down. If colocality requirements exist, either a regular VMSS-based AKS deployment into a single zone or [proximity placement groups](/azure/aks/reduce-latency-ppg) can be used to minimize internode latency.|
|**Cluster architecture:** Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) to route traffic globally across AKS clusters.|
| **Cluster and workload architectures:** Define Pod resource requests and limits in application deployment manifests, and enforce with Azure Policy.| Container CPU and memory resource limits are necessary to prevent resource exhaustion in your Kubernetes cluster.|
|**Cluster and workload architectures:** Keep the System node pool isolated from application workloads.|System node pools require a VM SKU of at least 2 vCPUs and 4 GB memory, but 4 vCPU or more is recommended. Reference [System and user node pools](/azure/aks/use-system-pools#system-and-user-node-pools) for detailed requirements.|
|**Cluster and workload architectures:** Separate applications to dedicated node pools based on specific requirements.|Applications may share the same configuration and need GPU-enabled VMs, CPU or memory optimized VMs, or the ability to scale-to-zero. Avoid large number of node pools to reduce extra management overhead.|
|**Cluster architecture:** Use a NAT gateway for clusters that run workloads that make many concurrent outbound connections.|To avoid reliability issues with Azure Load Balancer limitations with high concurrent outbound traffic, us a [NAT Gateway](/azure/aks/nat-gateway) instead to support reliable egress traffic at scale.|

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

### Azure Policy

Azure Kubernetes Service offers a wide variety of built-in Azure Policies that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Kubernetes, also within the cluster. There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster and workload architecture

- Clusters have readiness or liveness [health probes](/azure/application-gateway/ingress-controller-add-health-probes) configured for your pod spec.

In addition to the built-in Azure Policy definitions, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

## Security

Security is one of the most important aspects of any architecture. To explore how AKS can bolster the security of your application workload, we recommend you review the [Security design principles](../../../security/security-principles.md). If your Azure Kubernetes Service cluster needs to be designed to run a sensitive workload that meets the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [AKS regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro).

To learn about DoD Impact Level 5 (IL5) support and requirements with AKS, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-kubernetes-service).

When discussing security with Azure Kubernetes Service, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the cluster admin and their resource provider, while workload security is the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use [Managed Identities](/azure/aks/use-managed-identity) to avoid managing and rotating service principles.
> - **Cluster architecture:** Use Kubernetes role-based access control (RBAC) with Azure AD for [least privilege](/azure/aks/azure-ad-rbac) access and minimize granting administrator privileges to protect configuration, and secrets access.
> - **Cluster architecture:** Use Microsoft Defender for containers with [Azure Sentinel](/azure/sentinel/overview) to detect and quickly respond to threats across your cluster and workloads running on them.
> - **Cluster architecture:** Deploy a private AKS cluster to ensure cluster management traffic to your API server remains on your private network. Or use the API server allow list for non-private clusters.
> - **Workload architecture:** Use a Web Application Firewall to secure HTTP(S) traffic.
> - **Workload architecture:** Ensure your CI/CID pipeline is hardened with container-aware scanning.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|**Cluster architecture:** Use Azure Active Directory integration.|Using Azure AD centralizes the identity management component. Any change in user account or group status is automatically updated in access to the AKS cluster. The developers and application owners of your Kubernetes cluster need access to different resources.|
|**Cluster architecture:** Authenticate with Azure Active Directory (Azure AD) to Azure Container Registry.|AKS and Azure AD enables authentication with Azure Container Registry without the use of `imagePullSecrets` secrets. Review [Authenticate with Azure Container Registry from Azure Kubernetes Service](/azure/aks/cluster-container-registry-integration?tabs=azure-cli) for more information.|
|**Cluster architecture:** Secure network traffic to your API server with [private AKS cluster](/azure/aks/private-clusters).|By default, network traffic between your node pools and the API server travels the Microsoft backbone network; by using a private cluster, you can ensure network traffic to your API server remains on the private network only.|
|**Cluster architecture:** For non-private AKS clusters, use API server authorized IP ranges.|When using public clusters, you can still limit the traffic that can reach your clusters API server by using the authorized IP range feature. Include sources like the public IPs of your deployment build agents, operations management, and node pools' egress point (such as Azure Firewall).|
|**Cluster architecture:** Protect the API server with Azure Active Directory RBAC.|Securing access to the Kubernetes API Server is one of the most important things you can do to secure your cluster. Integrate Kubernetes role-based access control (RBAC) with Azure AD to control access to the API server. [Disable local accounts](/azure/aks/managed-aad#disable-local-accounts) to enforce all cluster access using Azure AD-based identities.|
|**Cluster architecture:** Use [Azure network policies](/azure/aks/use-network-policies) or Calico.| Secure and control network traffic between pods in a cluster.|
|**Cluster architecture:** Secure clusters and pods with [Azure Policy](/azure/aks/use-azure-policy). | Azure Policy can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. |
|**Cluster architecture:** Secure container access to resources.|Limit access to actions that containers can perform. Provide the least number of permissions, and avoid the use of root or privileged escalation.|
|**Workload architecture:** Use a Web Application Firewall to secure HTTP(S) traffic.|To scan incoming traffic for potential attacks, use a web application firewall such as [Azure Web Application Firewall (WAF) on Azure Application Gateway](/azure/web-application-firewall/ag/ag-overview) or [Azure Front Door](/azure/web-application-firewall/afds/afds-overview).|
|**Cluster architecture:** Control cluster egress traffic.|Ensure your cluster's outbound traffic is passing through a network security point such as [Azure Firewall](/azure/aks/limit-egress-traffic#restrict-egress-traffic-using-azure-firewall) or an [HTTP proxy](/azure/aks/http-proxy).|
|**Cluster architecture:** Use the open-source [Azure AD Workload Identity](https://github.com/Azure/azure-workload-identity) and [Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) with Azure Key Vault.| Protect and rotate secrets, certificates, and connection strings in Azure Key Vault with strong encryption. Provides an access audit log, and keeps core secrets out of the deployment pipeline.|
|**Cluster architecture:** Use [Microsoft Defender for Containers](/azure/defender-for-cloud/defender-for-containers-introduction). |Monitor and maintain the security of your clusters, containers, and their applications.|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps ensure and improve Azure Kubernetes service. It makes recommendations on a subset of the items listed in the policy section below, such as clusters without RBAC configured, missing Microsoft Defender configuration, unrestricted network access to the API Server. Likewise, it makes workload recommendations for some of the pod security initiative items. Review the [recommendations](/azure/advisor/advisor-security-recommendations).

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster architecture

- Microsoft Defender for Cloud-based policies
- Authentication mode and configuration policies (Azure AD, RBAC, disable local authentication)
- API Server network access policies, including private cluster

#### Cluster and workload architecture

- Kubernetes cluster pod security initiatives Linux-based workloads
- Include pod and container capability policies such as AppArmor, sysctl, security caps, SELinux, seccomp, privileged containers, automount cluster API credentials
- Mount, volume drivers, and filesystem policies
- Pod/Container networking policies, such as host network, port, allowed external IPs, HTTPs, and internal load balancers

Azure Kubernetes Service deployments often also use Azure Container Registry for Helm charts and container images. Azure Container Registry also supports a wide variety of Azure policies that spans network restrictions, access control, and Microsoft Defender for Cloud, which complements a secure AKS architecture.

In addition to the built-in policies, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

For more suggestions, see [AKS security concepts](/azure/aks/concepts-security) and evaluate our security hardening recommendations based on the [CIS Kubernetes benchmark](/azure/aks/cis-kubernetes).

## Cost optimization

Cost optimization is about understanding your different configuration options and recommended best practices to reduce unnecessary expenses and improve operational efficiencies. Before you follow the guidance in this article, we recommend you review the following resources:

* [Cost optimization design principles](/azure/architecture/framework/cost/principles).
* [How pricing and cost management work in Azure Kubernetes Service (AKS) compared to Amazon Elastic Kubernetes Service (Amazon EKS)](/azure/architecture/aws-professional/eks-to-aks/cost-management).
* If you are running AKS on-premises or at the edge, [Azure Hybrid Benefit](/windows-server/get-started/azure-hybrid-benefit) can also be used to further reduce costs when running containerized applications in those scenarios.

When discussing cost optimization with Azure Kubernetes Service, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations**, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Kubernetes Service** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use appropriate VM SKU per node pool and reserved instances where long-term capacity is expected.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.
> - **Cluster architecture:** Review performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
> - **Cluster architecture:** Use cluster autoscaler to scale in when workloads are less active.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster and workload architectures:** Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|
|**Cluster architecture:** Select the right virtual machine instance type. |Selecting the right virtual machine instance type is critical as it directly impacts the cost of running applications on AKS. Choosing a high-performance instance without proper utilization can lead to wasteful spending, while choosing a powerful instance can lead to performance issues and increased downtime. To determine the right virtual machine instance type, consider workload characteristics, resource requirements, and availability needs.|
|**Cluster architecture:** Select [virtual machines based on the Arm architecture](/azure/virtual-machines/dplsv5-dpldsv5-series). |AKS supports [creating ARM64 Ubuntu agent nodes](/azure/aks/use-multiple-node-pools#add-an-arm64-node-pool), as well as a of mix Intel and ARM architecture nodes within a cluster that can bring better performance at a lower cost.|
|**Cluster architecture:** Select the appropriate region. |Due to many factors, cost of resources varies per region in Azure. Evaluate the cost, latency, and compliance requirements to ensure you are running your workload cost-effectively and it doesn't affect your end-users or create extra networking charges.|
|**Workload architecture:** Maintain small and optimized images.|Streamlining your images helps reduce costs since new nodes need to download these images. Build images in a way that allows the container start as soon as possible to help avoid user request failures or timeouts while the application is starting up, potentially leading to overprovisioning.|
|**Cluster architecture:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically reduce the number of agent nodes in response to excess resource capacity. |Automatically scale down the number of nodes in your AKS cluster lets you run an efficient cluster when demand is low, scale up when demand returns.|
|**Workload architecture:** Use the [Horizontal Pod Autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler).|Adjust the number of pods in a deployment depending on CPU utilization or other select metrics, which support cluster scale-in operations.|
|**Workload architecture:** Use [Vertical Pod Autoscaler](/azure/aks/vertical-pod-autoscaler) (preview).|Rightsize your pods and dynamically set [requests and limits](/azure/aks/developer-best-practices-resource-management#define-pod-resource-requests-and-limits) based on historic usage.|
|**Workload architecture:** Use [Kubernetes Event Driven Autoscaling](/azure/aks/keda-about) (KEDA).|Scale based on the number of events being processed. Choose from a rich catalogue of 50+ KEDA scalers.|
|**Cluster and workload architectures:** Adopt a cloud financial discipline and cultural practice to drive ownership of cloud usage. | The foundation of enabling cost optimization is the spread of a cost saving cluster. A [financial operations approach (FinOps)](https://www.finops.org/introduction/what-is-finops/) is often used to help organizations reduce cloud costs. It is a practice involving collaboration between finance, operations, and engineering teams to drive alignment on cost saving goals and bring transparency to cloud costs.|
|**Cluster architecture:** Sign up for [Azure Reservations](/azure/cost-management-billing/reservations/save-compute-costs-reservations) or [Azure Savings Plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview). | If you properly planned for capacity, your workload is predictable and exists for an extended period of time, sign up for an [Azure Reservation](/azure/aks/faq#can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes) or a [savings plan](/azure/cost-management-billing/savings-plan/savings-plan-compute-overview#determine-your-savings-plan-commitment) to further reduce your resource costs.|
|**Cluster architecture:** Configure monitoring of cluster with [Container insights](/azure/azure-monitor/containers/container-insights-overview). | Container insights help provides actionable insights into your clusters idle and unallocated resources. Container insights also supports collecting Prometheus metrics and integrates with Azure Managed Grafana to get a holistic view of your application and infrastructure.|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

### Cloud efficiency

Making workloads more [sustainable and cloud efficient](../../../sustainability/sustainability-get-started.md), requires combining efforts around **cost optimization**, **reducing carbon emissions**, and **optimizing energy consumption**. Optimizing the application's cost is the initial step in making workloads more sustainable.

Learn how to build sustainable and efficient AKS workloads, in [Sustainable software engineering principles in Azure Kubernetes Service (AKS)](/azure/aks/concepts-sustainable-software-engineering).

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics, but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](/azure/architecture/framework/devops/principles) and the [Day-2 operations guide](/azure/architecture/operator-guides/aks/day-2-operations-guide).

When discussing operational excellence with Azure Kubernetes Service, it's important to distinguish between *cluster operational excellence* and *workload operational excellence*. Cluster operations are a shared responsibility between the cluster admin and their resource provider, while workload operations are the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Use a template-based deployment using Bicep, Terraform, or others. Make sure that all deployments are repeatable, traceable, and stored in a source code repo.
> - **Cluster architecture:** Build an automated process to ensure your clusters are bootstrapped with the necessary cluster-wide configurations and deployments. This is often performed using GitOps.
> - **Workload architecture:** Use a repeatable and automated deployment processes for your workload within your software development lifecycle.
> - **Cluster architecture:** Enable diagnostics settings to ensure control plane or core API server interactions are logged.
> - **Cluster and workload architectures:** Enable [Container insights](/azure/azure-monitor/containers/container-insights-overview) to collect metrics, logs, and diagnostics to monitor the availability and performance of the cluster and workloads running on it.
> - **Workload architecture:** The workload should be designed to emit telemetry that can be collected, which should also include liveliness and readiness statuses.
> - **Cluster and workload architectures:** Use chaos engineering practices that target Kubernetes to identify application or platform reliability issues.
> - **Workload architecture:** Optimize your workload to operate and deploy efficiently in a container.
> - **Cluster and workload architectures:** Enforce cluster and workload governance using Azure Policy.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for operations.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Review [AKS best practices](/azure/aks/best-practices) documentation.|To build and run applications successfully in AKS, there are key considerations to understand and implement. These areas include multi-tenancy and scheduler features, cluster, and pod security, or business continuity and disaster recovery.|
|**Cluster and workload architectures:** Review [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-tutorial-aks-portal).| Azure Chaos Studio can help simulate faults and trigger disaster recovery situations.|
|**Cluster and workload architectures:** Configure monitoring of cluster with [Container insights](/azure/azure-monitor/containers/container-insights-overview). | Container insights help monitor the performance of containers by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API and container logs. |
|**Workload architecture:** Monitor application performance with Azure Monitor. |Configure [Application Insights](/azure/azure-monitor/app/app-insights-overview) for code-based monitoring of applications running in an AKS cluster.|
|**Workload architecture:** Configure scraping of Prometheus metrics with Container insights.|Container insights, which are part of Azure Monitor, provide a seamless onboarding experience to collect Prometheus metrics. Reference [Configure scraping of Prometheus metrics](/azure/azure-monitor/containers/container-insights-prometheus-integration) for more information.|
|**Cluster architecture:** Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview) or [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic) to route traffic globally across AKS clusters.|
|**Cluster architecture:** Operationalize clusters and pods configuration standards with [Azure Policy](/azure/aks/use-azure-policy). | Azure Policy can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. |
| **Workload architecture:** Use platform capabilities in your release engineering process.|Kubernetes and ingress controllers support many advanced deployment patterns for inclusion in your release engineering process. Consider patterns like blue-greem deployments or canary releases.|
|**Cluster and workload architectures:** For mission-critical workloads, use stamp-level blue/green deployments.|Automate your mission-critical design areas, including [deployment and testing](/azure/architecture/framework/mission-critical/mission-critical-deployment-testing#ephemeral-blue-green-deployments).|

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported AKS versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster architecture

- Azure Policy add-on for Kubernetes
- GitOps configuration policies
- Diagnostics settings policies
- AKS version restrictions
- Prevent command invoke

#### Cluster and workload architecture

- Namespace deployment restrictions

In addition to the built-in policies, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. We recommend you review the [Performance efficiency principles](/azure/architecture/framework/scalability/principles).

When discussing performance with Azure Kubernetes Service, it's important to distinguish between *cluster performance* and *workload performance*. Cluster performance is a shared responsibility between the cluster admin and their resource provider, while workload performance is the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

As you make design choices for Azure Kubernetes Service, review the [Performance efficiency principles](/azure/architecture/framework/scalability/principles).

> [!div class="checklist"]
> - **Cluster and workload architectures:** Perform and iterate on a detailed capacity plan exercise that includes SKU, autoscale settings, IP addressing, and failover considerations.
> - **Cluster architecture:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response workload demands.
> - **Cluster architecture:** Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.
> - **Cluster and workload architectures:** Perform ongoing load testing activities that exercise both the pod and cluster autoscaler.
> - **Cluster and workload architectures:** Separate workloads into different node pools allowing independent scalling.

### Recommendations

Explore the following table of recommendations to optimize your Azure Kubernetes Service configuration for performance.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Develop a detailed capacity plan and continually review and revise.|After formalizing your capacity plan, it should be frequently updated by continuously observing the resource utilization of the cluster.|
|**Cluster architecture:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your AKS cluster lets you run an efficient, cost-effective cluster.|
|**Cluster and workload architectures:** Separate workloads into different node pools and consider [scaling](/azure/aks/scale-cluster) user node pools.|Unlike System node pools that always require running nodes, user node pools allow you to scale up or down.|
|**Workload architecture:** Use AKS [advanced scheduler features](/azure/aks/operator-best-practices-advanced-scheduler). | Helps control balancing of resources for workloads that require them.|
|**Workload architecture:** Use meaningful workload scaling metrics.|Not all scale decisions can be derived from CPU or memory metrics. Often scale considerations will come from more complex or even external data points. Use [KEDA](/training/modules/aks-app-scale-keda/) to build a meaningful auto scale ruleset based on signals that are specific to your workload.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

### Policy definitions

Azure Policy offers various built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster and workload architecture

- CPU and memory resource limits

In addition to the built-in policies, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Additional resources

### Azure Architecture Center guidance

- [AKS baseline architecture](/azure/architecture/reference-architectures/containers/aks/secure-baseline-aks)
- [Advanced AKS microservices architecture](/azure/architecture/reference-architectures/containers/aks-microservices/aks-microservices-advanced)
- [AKS cluster for a PCI-DSS workload](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro)
- [AKS baseline for multiregion clusters](/azure/architecture/reference-architectures/containers/aks-multi-region/aks-multi-cluster)

### Cloud Adoption Framework guidance

- [AKS Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/app-platform/aks/landing-zone-accelerator)

## Next steps

- Deploy an Azure Kubernetes Service (AKS) cluster using the Azure CLI [Quickstart: Deploy an Azure Kubernetes Service (AKS) cluster using the Azure CLI](/azure/aks/kubernetes-walkthrough)

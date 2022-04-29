---
title: Azure Well-Architected Framework review for Azure Kubernetes Service (AKS)
description: Provides a template for a Well-Architected Framework (WAF) article that is specific to Azure Kubernetes Service (AKS).
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
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

For context, consider reviewing a reference architecture that reflects these considerations in its design. We recommend that you start with the [baseline architecture for an Azure Kubernetes Service (AKS) cluster](/architecture/reference-architectures/containers/aks/secure-baseline-aks) and [Microservices architecture on Azure Kubernetes Service](/architecture/reference-architectures/containers/aks-microservices/aks-microservices). Also review the [AKS landing zone accelerator](/cloud-adoption-framework/scenarios/aks/enterprise-scale-landing-zone), which provides an architectural approach and reference implementation to prepare landing zone subscriptions for a scalable Azure Kubernetes Service (AKS) cluster.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

When discussing reliability with Azure Kubernetes Service, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is a shared responsibility between the cluster admin and their resource provider, while workload reliability is the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** For critical workloads, use [availability zones](/azure/aks/availability-zones) for your AKS clusters.
> - **Cluster architecture:** Plan the IP address space to ensure your cluster can reliably scale, handle failover traffic in a multi-cluster topology, and communicate with other Azure services over Private Link.
> - **Cluster architecture:** Enable [Container insights](/azure/azure-monitor/containers/container-insights-overview) to monitor your cluster and configure alerts for reliability-impacting events.
> - **Cluster architecture:** Use a smaller VM size for the system node pool and chose the right node sizes, with a minimum of two nodes, for your workload depending on your requirements.

### AKS configuration recommendations

Explore the following table of recommendations to optimize your AKS configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Control pod scheduling using node selectors and affinity.|Allows the Kubernetes scheduler to logically isolate workloads by hardware in the node. Unlike [tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/), pods without a matching node selector can be scheduled on labeled nodes, which allows unused resources on the nodes to consume, but gives priority to pods that define the matching node selector. Use node affinity for more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|**Cluster architecture:** Ensure proper selection of network plugin based on network requirements and cluster sizing.|Azure CNI is required for specific scenarios, for example, Windows-based node pools, specific networking requirements and Kubernetes Network Policies. Reference [Kubenet versus Azure CNI](/azure/aks/concepts-network#compare-network-models) for more information.|
|**Cluster and workload architectures:** Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production grade clusters.|The AKS Uptime SLA guarantees:<br> - `99.95%` availability of the Kubernetes API server endpoint for AKS Clusters that use Azure Availability Zones, or <br> - `99.9%` availability for AKS Clusters that don't use Azure Availability Zones.|
|**Cluster architecture:** Authenticate with Azure Active Directory (Azure AD) to Azure Container Registry.|AKS and Azure AD enables authentication with Azure Container Registry without the use of K8s and `imagePullSecrets` secrets. Review [Authenticate with Azure Container Registry from Azure Kubernetes Service](/azure/aks/cluster-container-registry-integration?tabs=azure-cli) for more information.|
|**Cluster and workload architectures:** Configure monitoring of cluster with [Container insights](/azure/azure-monitor/containers/container-insights-overview). | Container insights helps monitor the health and performance of containers of controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Integration with Prometheus enables collection of application and workload metrics. |
|**Cluster architecture:** Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.|Where colocality requirements exist, either a regular VMSS-based AKS deployment into a single zone or [proximity placement groups](/azure/aks/reduce-latency-ppg) can be used to minimize internode latency.|
|**Cluster architecture:** Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview), [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic), or a third-party CDN to route traffic globally across AKS clusters.|
| **Cluster and workload architectures:** Define Pod resource requests and limits using Azure Policy, in application deployment manifests.| Enforce container CPU and memory resource limits to prevent resource exhaustion in your Kubernetes cluster.|
|**Cluster and workload architectures:** Keep the System node pool isolated from application workloads.|System node pools require a VM SKU of at least `2` `vCPUs` and `4GB` memory. Reference [System and user node pools](/azure/aks/use-system-pools#system-and-user-node-pools) for detailed requirements.|
|**Cluster and Workload architectures:** Separate applications to dedicated node pools based on specific requirements.|Applications may share the same configuration and need GPU-enabled VMs, CPU or memory optimized VMs, scale-to-zero, and so on. Avoid large number of node pools to reduce extra management overhead.|

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

Azure Advisor helps you ensure and improve Azure Kubernetes Service, for this category there aren't any related recommendations.

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
> - **Cluster architecture:** Use [Azure AD integration](/azure/aks/managed-aad) to centralize account management, application access management, and identity protection.
> - **Cluster architecture:** Use Kubernetes role-based access control (RBAC) with Azure AD for [least privilege](/azure/aks/azure-ad-rbac) access and minimize granting administrator privileges to protect configuration, and secrets access.


### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|**Cluster architecture:** Use Azure Active Directory integration.|Using Azure AD centralizes the identity management component. Any change in user account or group status is automatically updated in access to the AKS cluster. The developers and application owners of your Kubernetes cluster need access to different resources. Kubernetes doesn't provide an identity management solution to control which users can interact with what resources.|
|**Cluster architecture:** Use pod-managed identities.|Don't use fixed credentials within pods or container images because they're at risk of exposure or abuse. Instead, use pod-managed identities to associate managed identities for Azure resources and identities in Azure AD with pods. *Note: Pod identities are intended for use with Linux pods and container images only.*|
|**Cluster architecture:** Protect the API server with Azure Active Directory RBAC.|Securing access to the Kubernetes API Server is one of the most important things you can do to secure your cluster. Integrate Kubernetes role-based access control (RBAC) with Azure AD to control access to the API server. These controls let you secure AKS the same way that you secure access of resources in your Azure subscription. The Kubernetes API server provides a single connection point for requests to perform actions within a cluster. To secure and audit access to the API server, limit access and provide the least-privileged access permissions required. This approach isn't unique to Kubernetes, but is especially important when the AKS cluster is logically isolated for multi-tenant use.|
|**Cluster architecture:** Secure clusters and pods with Azure Policy.|[Azure Policy](/azure/aks/use-azure-policy) can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. This access is defined through built-in policy definitions provided by Azure Policy. By providing more control over the security aspects of your pod's specification, like root privileges, Azure Policy enables stricter security adherence and visibility into what you've deployed in your cluster. If a pod doesn't meet conditions specified in the policy, Azure Policy can disallow the pod to start or flag a violation.|
|**Cluster architecture:** Secure container access to resources.|Limit access to actions that containers can perform. Provide the least number of permissions, and avoid the use of root or privileged escalation. In the same way that you should grant users or groups the least privileges required, containers should also be limited to only the actions and process that they need. To minimize the risk of attack, don't configure applications and containers that require escalated privileges or root access.|
|**Cluster architecture:** Use a Web Application Firewall to secure traffic.|To scan incoming traffic for potential attacks, use a web application firewall such as Barracuda WAF for Azure or Azure Application Gateway. An ingress controller that distributes traffic to services and applications is typically a Kubernetes resource in your AKS cluster. In larger environments, you often want to offload some of this traffic routing or TLS termination to a network resource outside of the AKS cluster. You also want to scan incoming traffic for potential attacks.|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps ensure and improve Azure Kubernetes service. It makes recommendations on a subset of the items listed in the policy section below, such as clusters without RBAC configured, missing Microsoft Defender configuration, unrestricted network access to the API Server. Likewise, it makes workload recommendations for some of the pod security initiative items. Review the [recommendations](/azure/advisor/advisor-security-recommendations).

### Policy definitions

Azure Policy offers a variety of built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster architecture

- Microsoft Defender for Cloud-based policies
- Authentication mode and configuration policies (Azure AD, RBAC, disable local authentication)
- API Server network access policies, including private cluster

#### Cluster and workload architecture

- Kubernetes cluster pod security initiatives Linux-based workloads
- Include pod and container capability policies such as AppArmor, sysctl, security caps, SELinux, seccomp, privileged containers, automount cluster API credentials
- Mount, volume drivers, and filesystem policies
- Pod/Container networking policies, such as host network, port, allowed external Ips, HTTPs, internal load balancers

Azure Kubernetes Service deployments often also use Azure Container Registry for Helm charts and container images. Azure Container Registry also supports a wide variety of Azure policies that spans network restrictions, access control, and Microsoft Defender for Cloud, which complements a secure AKS architecture.

In addition to the built-in policies, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

For more suggestions, see [AKS security concepts](/azure/aks/concepts-security) and evalaute our security hardening recommendations based on the [CIS Kubernetes benchmark](/azure/aks/cis-kubernetes).

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](/azure/architecture/framework/cost/principles).

When discussing cost optimization with Azure Kubernetes Service, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources is a shared responsibility between the cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Kubernetes Service** from the available products. You can test different configuration and payment plans in the calculator.

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** Select appropriate VM SKU.
> - **Cluster architecture:** Use appropriate node type and size, IP address space, and reserved instances.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.
> - **Cluster architecture:** Consolidate workloads and stop nodes that are not in use by scaling node pools using the [cluster autoscaler](/azure/aks/cluster-autoscaler) feature.
> - **Workload architecture:** Consider using [Azure Spot VMs](/azure/aks/spot-node-pool) for workloads that can handle interruptions, early terminations, and evictions.
> - **Workload architecture:** Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics, which supports cluster scale-in operations.
> - **Cluster architecture:** Review performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for cost.

| Recommendation | Benefit |
|----------------------------------|-----------|
|**Cluster architecture:** Avoid VM SKUs with temporary disk offerings.|AKS uses managed disks by default, so avoiding temp disk offerings ensures you don't pay for unneeded resources.|
|**Cluster and workload architectures:** Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|
|**Cluster and workload architectures:** Use the [Start and Stop feature](/azure/aks/start-stop-cluster?tabs=azure-cli) in Azure Kubernetes Services (AKS).|The AKS Stop and Start cluster feature allows AKS customers to pause an AKS cluster, saving time and cost. The stop and start feature keeps cluster configurations in place and customers can pick up where they left off without reconfiguring the clusters.|
|**Cluster and workload architectures:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints. |Automatically scale up or down the number of nodes in your AKS cluster lets you run an efficient, cost-effective cluster.|
|**Workload architecture:** Consider using [Azure Spot VMs](/azure/aks/spot-node-pool) for workloads that can handle interruptions, early terminations, or evictions.|For example, workloads such as batch processing jobs, development, and testing environments, and large compute workloads may be good candidates for you to schedule on a spot node pool. Using spot VMs for nodes with your AKS cluster allows you to take advantage of unused capacity in Azure at a significant cost savings.|
|**Cluster architecture:** Enforce [resource quotas](/azure/aks/operator-best-practices-scheduler) at the namespace level.|Resource quotas provide a way to reserve and limit resources across a development team or project. These quotas are defined on a namespace and can be used to set quotas on Compute resources, Storage resources, and Object counts. When you define resource quotas, all pods created in the namespace must provide limits or requests in their pod specifications.|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve Azure Kubernetes Service, for this category there aren't any related recommendations.

### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](/azure/architecture/framework/devops/principles) and the [Day-2 operations guide](/architecture/operator-guides/aks/day-2-operations-guide).

When discussing operational excellence with Azure Kubernetes Service, it's important to distinguish between *cluster operational excellence* and *workload operational excellence*. Cluster security is a shared responsibility between the cluster admin and their resource provider, while workload security is the domain of a developer. Azure Kubernetes Service has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

> [!div class="checklist"]
> - **Cluster and workload architectures:** Review [AKS best practices](/azure/aks/best-practices) documentation.
> - **Cluster architecture:** Don't modify resources in the [node resource group (for example MC_)](/azure/aks/faq#why-are-two-resource-groups-created-with-aks). You should *only* make modifications at [cluster creation time](/azure/aks/faq#can-i-provide-my-own-name-for-the-aks-node-resource-group), or with assistance from [Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/supportPlans).
> - **Cluster and workload architectures:** Monitor your clusters using [Container insights](/azure/azure-monitor/containers/container-insights-overview) to collect metrics, logs, and diagnostics to monitor the availability and performance of the cluster and workloads running on it. Enable and review [Kubernetes master node logs](/azure/azure-monitor/containers/container-insights-log-query#resource-logs) and additionally, configure [scraping of Prometheus metrics](/azure/azure-monitor/containers/container-insights-prometheus-integration) with Container insights.
> - **Cluster and workload architecture:** Remove dependencies on public container registries. Store all container images within [Azure Container Registry](/azure/container-registry/container-registry-intro) and enable [geo-replication](/azure/container-registry/container-registry-geo-replication) to manage a single registry across all regions. Enforce this using Azure Policy.
> - **Cluster architecture:** Use a template-based deployment using Bicep, Terraform, or others. Make sure that all deployments are repeatable, traceable, and stored in a source code repo. Can be combined with GitOps.
> - **Cluster architecture:** Enable diagnostics settings to ensure control plane or core API server interactions are logged.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for operations.

| Recommendation | Benefit |
|--------|----|
|**Cluster and workload architectures:** Review AKS best practices documentation.|To build and run applications successfully in AKS, there are key considerations to understand and implement. These areas include multi-tenancy and scheduler features, cluster, and pod security, or business continuity and disaster recovery.|
|**Cluster and workload architectures:** Configure monitoring of cluster with [Container insights](/azure/azure-monitor/containers/container-insights-overview). | Container insights helps monitor the performance of containers by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Container logs are also collected.|
|**Workload architecture:** Monitor application performance with Azure Monitor. |Configure [Application Insights](/azure/azure-monitor/app/app-insights-overview) for code-based monitoring of applications running in an AKS cluster.|
|**Workload architecture:** Configure scraping of Prometheus metrics with Container insights.|Container insights, which is part of Azure Monitor, provides a seamless onboarding experience to collect Prometheus metrics. Reference [Configure scraping of Prometheus metrics](/azure/azure-monitor/containers/container-insights-prometheus-integration) for more information.|
|**Cluster architecture:** Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview), [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic), or a third-party CDN to route traffic globally across AKS clusters.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor also makes recommendations on a subset of the items listed in the policy section below, such unsupported AKS versions and unconfigured diagnostic settings. Likewise, it makes workload recommendations around the use of the default namespace.

### Policy definitions

Azure Policy offers a variety of built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

#### Cluster architecture

- Azure Policy add-on for Kubernetes
- GitOps configuration policies
- Diagnostics settings policies
- AKS versions version restrictions
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
> - **Cluster architecture:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.
> - **Cluster architecture:** Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.
> - **Cluster and workload architectures:** Separate workloads into different node pools and consider scaling user node pools to zero.
> - **Workload architecture:** Define your resource profile to match the application performance levels that your application requires.

### Recommendations

Explore the following table of recommendations to optimize your Azure Kubernetes Service configuration for performance.

| Recommendation | Benefit |
|--------|----|
|**Cluster architecture:** Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your AKS cluster lets you run an efficient, cost-effective cluster.|
|**Cluster and workload architectures:** Separate workloads into different node pools and consider [scaling](/azure/aks/scale-cluster) user node pools.|Unlike System node pools that always require running nodes, user node pools allow you to scale to zero.|
|**Workload architecture:** Use AKS [advanced scheduler features](/azure/aks/operator-best-practices-advanced-scheduler). | Helps control balancing of resources for workloads that require them.|  

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

Azure Advisor helps you ensure and improve Azure Kubernetes Service, for this category there aren't any related recommendations.

### Policy definitions

Azure Policy offers a variety of built-in policy definitions that apply to both the Azure resource and AKS like standard policy definitions, and using the Azure Policy add-on for Kubernetes, also within the cluster. Many of the Azure resource policies come in both *Audit/Deny*, but also in a *Deploy If Not Exists* variant.

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

## Next steps

- Deploy an Azure Kubernetes Service (AKS) cluster using the Azure CLI [Quickstart: Deploy an Azure Kubernetes Service (AKS) cluster using the Azure CLI](/azure/aks/kubernetes-walkthrough)
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

We assume that you have working knowledge of Azure Kubernetes Service and are well versed with its features. For more information, see [Azure Kubernetes Service](/azure/aks/intro-kubernetes).

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.

Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with the [baseline architecture for an Azure Kubernetes Service (AKS) cluster](/architecture/reference-architectures/containers/aks/secure-baseline-aks) and [Microservices architecture on Azure Kubernetes Service](/architecture/reference-architectures/containers/aks-microservices/aks-microservices). Also review the [AKS landing zone accelerator](/cloud-adoption-framework/scenarios/aks/enterprise-scale-landing-zone), which provides an architectural approach and reference implementation to prepare landing zone subscriptions for a scalable Azure Kubernetes Service (AKS) cluster.

## Reliability

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize failed instances.

### Design checklist

> [!div class="checklist"]
> - Run resource-intensive applications, such as ingress controllers, or workloads that require specialized hardware (such as large data processing workloads like machine learning (ML) or artificial intelligence (AI)).
> - Don't modify resources in the node resource group (for example MC_). You should only make modifications at cluster creation time, or with assistance from Azure Support.
> - Define Pod resource requests and limits in application deployment manifests.
> - Control pod scheduling using node selectors and affinity.
> - Use [Azure network policies](/azure/aks/use-network-policies) or Calico to control traffic between pods, which requires [CNI Network plug-in](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/). Ensure proper selection of network plugin based on network requirements and cluster sizing.
> - Use the [AKS Uptime SLA](/azure/aks/uptime-sla) with production workloads to ensure availability of control plane components.
> - Store container images within [Azure Container Registry](/azure/container-registry/container-registry-intro) and enable [geo-replication](/azure/container-registry/container-registry-geo-replication) to manage a single registry across all regions.
> - Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.
> - Use a template-based deployment using Terraform, Ansible, and others only. Make sure that all deployments are repeatable and traceable, and stored in a source code repo. Can be combined with GitOps.
> - Adopt a [multi-region](/azure/architecture/reference-architectures/containers/aks-multi-region/aks-multi-cluster) strategy by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.

### Node pool design checklist

> [!div class="checklist"]
> - Use [multiple node pools](/azure/aks/use-multiple-node-pools) to support workloads that have different compute or storage demands. 
> - Keep the System node pool isolated from application workloads.
> - Use dedicated node pools for infrastructure tools that require high resource utilization, such as Istio, or have a special scale or load behavior.
> - Separate applications to dedicated node pools based on specific requirements.
> - Use [taints and tolerations](/azure/aks/operator-best-practices-advanced-scheduler#provide-dedicated-nodes-using-taints-and-tolerations) to provide dedicated nodes and limit resource intensive applications.
> - Consider the use of [Virtual Nodes](/azure/aks/virtual-nodes-cli) [(vKubelet)](https://github.com/virtual-kubelet/virtual-kubelet) with ACI for rapid, massive, and infinite scale.

### AKS configuration recommendations

Explore the following table of recommendations to optimize your AKS configuration for Reliability.

| Recommendation | Benefit |
|--------|----|
|Control pod scheduling using node selectors and affinity.|Allows the Kubernetes scheduler to logically isolate workloads by hardware in the node. Unlike [tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/), pods without a matching node selector can be scheduled on labeled nodes, which allows unused resources on the nodes to consume, but gives priority to pods that define the matching node selector. Use node affinity for more flexibility, which allows you to define what happens if the pod can't be matched with a node.|
|Ensure proper selection of network plugin based on network requirements and cluster sizing.|Azure CNI is required for specific scenarios, for example, Windows-based node pools, specific networking requirements and Kubernetes Network Policies. Reference [Kubenet versus Azure CNI](/azure/aks/concepts-network#compare-network-models) for more information.|
|Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production grade clusters.|The AKS Uptime SLA guarantees:<br> - `99.95%` availability of the Kubernetes API server endpoint for AKS Clusters that use Azure Availability Zones, or <br> - `99.9%` availability for AKS Clusters that don't use Azure Availability Zones.|
|Authenticate with Azure AD to Azure Container Registry.|AKS and Azure AD enables authentication with Azure Container Registry without the use of K8s and `imagePullSecrets` secrets. Reference [Authenticate with Azure Container Registry from Azure Kubernetes Service](/azure/aks/cluster-container-registry-integration?tabs=azure-cli) for more information.|
|Use [availability zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.|Where colocality requirements exist, either a regular VMSS-based AKS deployment into a single zone or [proximity placement groups](/azure/aks/reduce-latency-ppg) can be used to minimize internode latency.|
|Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview), [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic), or a third-party CDN to route traffic globally across AKS clusters.|

### Node pool design recommendations

The following table reflects node pool design recommendations and descriptions related to the overall AKS configuration recommendations:

|Recommendation| Benefit|
|--------------------------------|-----------|
|Keep the System node pool isolated from application workloads.|System node pools require a VM SKU of at least `2` `vCPUs` and `4GB` memory. Reference [System and user node pools](/azure/aks/use-system-pools#system-and-user-node-pools) for detailed requirements.|
|Separate applications to dedicated node pools based on specific requirements.|Applications may share the same configuration and need GPU-enabled VMs, CPU or memory optimized VMs, scale-to-zero, and so on. Avoid large number of node pools to reduce extra management overhead.|

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

Azure Advisor helps you ensure and improve Azure Kubernetes Service, for this category there aren't any related recommendations.

### Azure Policy

Azure Kubernetes Service offers a wide variety of built-in Azure Policies that apply to both the Azure resource like typical Azure Policies and, using the Azure Policy add-on for Kubernetes, also within the cluster. There are a numerous number of policies, and key policies related to this pillar are summarized here. For a more detailed view, see [built-in policy definitions for Kubernetes](/azure/governance/policy/samples/built-in-policies#kubernetes).

### Cluster and workload architecture

- Ensure clusters have readiness or liveness probes.

In addition to the built-in Azure Policy definitions, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

## Security

Security is one of the most important aspects of any architecture. To explore how AKS can bolster the security of your application workload, we recommend you review the [Security design principles](../../security/security-principles.md). If your Azure Kubernetes Service cluster needs to be designed to run a sensitive workload that meets the regulatory requirements of the Payment Card Industry Data Security Standard (PCI-DSS 3.2.1), review [AKS regulated cluster for PCI-DSS 3.2.1](/azure/architecture/reference-architectures/containers/aks-pci/aks-pci-intro).

To learn about DoD Impact Level 5 (IL5) support and requirements with AKS, review [Azure Government IL5 isolation requirements](/azure/azure-government/documentation-government-impact-level-5#azure-kubernetes-service).

### Design checklist

> [!div class="checklist"]
> - Use [Managed Identities](/azure/aks/use-managed-identity) to avoid managing and rotating service principles.
> - Use [Azure Active Directory (Azure AD) integration](/azure/aks/managed-aad) to centralize account management, passwords, application access management, and identity protection.
> - Use Kubernetes role-based access control (RBAC) with Azure AD for [least privilege](/azure/aks/azure-ad-rbac) access and minimize granting administrator privileges to protect configuration, and secrets access.
> - Limit access to [Kubernetes cluster configuration](/azure/aks/control-kubeconfig-access) file with Azure role-based access control.
> - Limit access to [actions that containers can perform](/azure/aks/developer-best-practices-pod-security#secure-pod-access-to-resources). Provide the least number of permissions, and avoid the use of root, or privileged escalation.
> - Evaluate the use of the built-in [AppArmor security module](/azure/aks/operator-best-practices-cluster-security#app-armor) to limit actions that containers can perform such as read, write, or execute, or system functions such as mounting files systems.
> - Evaluate the use of the [seccomp (secure computing)](/azure/aks/operator-best-practices-cluster-security#secure-computing). Seccomp works at the process level and allows you to limit the process calls that containers can perform.
> - Use [Pod Identities](/azure/aks/operator-best-practices-identity#use-pod-identities) and [Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage) with Azure Key Vault to protect secrets, certificates, and connection strings.
> - Protect the API server with Azure Active Directory RBAC.
> - Use [Microsoft Defender for Containers](/azure/defender-for-cloud/defender-for-containers-introduction) to monitor, and maintain the security of your clusters, containers, and their applications.
> - Secure clusters and pods with [Azure Policy](/azure/aks/use-azure-policy).
> - Secure container access to resources.
> - Use a Web Application Firewall to secure traffic.
> - Use Kubernetes network policies for controlling traffic flow between pods.
> - Securely connect to Kubernetes nodes through a bastion host.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for security.

|Recommendation|Benefit|
|----------------------------------|-----------|
|Use Azure Active Directory integration.|Using Azure AD centralizes the identity management component. Any change in user account or group status is automatically updated in access to the AKS cluster. The developers and application owners of your Kubernetes cluster need access to different resources. Kubernetes doesn't provide an identity management solution to control which users can interact with what resources.|
|Use pod-managed identities.|Don't use fixed credentials within pods or container images because they're at risk of exposure or abuse. Instead, use pod-managed identities to associate managed identities for Azure resources and identities in Azure AD with pods. *Note: Pod identities are intended for use with Linux pods and container images only.*|
|Protect the API server with Azure Active Directory RBAC.|Securing access to the Kubernetes API Server is one of the most important things you can do to secure your cluster. Integrate Kubernetes role-based access control (RBAC) with Azure AD to control access to the API server. These controls let you secure AKS the same way that you secure access of resources in your Azure subscription. The Kubernetes API server provides a single connection point for requests to perform actions within a cluster. To secure and audit access to the API server, limit access and provide the least-privileged access permissions required. This approach isn't unique to Kubernetes, but is especially important when the AKS cluster is logically isolated for multi-tenant use.|
|Secure clusters and pods with Azure Policy.|[Azure Policy](/azure/aks/use-azure-policy) can help to apply at-scale enforcement and safeguards on your clusters in a centralized, consistent manner. It can also control what functions pods are granted and if anything is running against company policy. This access is defined through built-in policy definitions provided by Azure Policy. By providing more control over the security aspects of your pod's specification, like root privileges, Azure Policy enables stricter security adherence and visibility into what you've deployed in your cluster. If a pod doesn't meet conditions specified in the policy, Azure Policy can disallow the pod to start or flag a violation.|
|Secure container access to resources.|Limit access to actions that containers can perform. Provide the least number of permissions, and avoid the use of root or privileged escalation. In the same way that you should grant users or groups the least privileges required, containers should also be limited to only the actions and process that they need. To minimize the risk of attack, don't configure applications and containers that require escalated privileges or root access.|
|Use a Web Application Firewall to secure traffic.|To scan incoming traffic for potential attacks, use a web application firewall such as Barracuda WAF for Azure or Azure Application Gateway. An ingress controller that distributes traffic to services and applications is typically a Kubernetes resource in your AKS cluster. In larger environments, you often want to offload some of this traffic routing or TLS termination to a network resource outside of the AKS cluster. You also want to scan incoming traffic for potential attacks.|
|Use Kubernetes network policies for controlling traffic flow between pods.|Use network policies to allow or deny traffic to pods. By default, all traffic is allowed between pods within a cluster. For improved security, define rules that limit pod communication. *Note: Network policy should only be used for Linux-based nodes and pods in AKS.*|
|Securely connect to Kubernetes nodes through a bastion host.|Don't expose remote connectivity to your AKS nodes. Create a bastion host, or jump box, in a management virtual network. Use the bastion host to securely route traffic into your AKS cluster to remote management tasks. *Note: Most operations in AKS can be completed using the Azure management tools or though the Kubernetes API server.* AKS nodes aren't connected to the public internet, and are only available on a private network. To connect to nodes and perform maintenance or troubleshoot issues, route your connections through a bastion host, or jump box.|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps ensure and improve Azure Kubernetes service. It makes recommendations on a subset of the items listed in the policy section below, such as clusters without RBAC configured, missing Microsoft Defender configuration, unrestricted network access to the API Server. Likewise, it makes workload recommendations for some of the pod security initiative items. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

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

## Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. We recommend you review the [Cost optimization design principles](/azure/architecture/framework/cost/principles).

### Design checklist

> [!div class="checklist"]
> - Familiarize yourself with Azure Kubernetes Services pricing
> - Stop nodes that are not in use by scaling all the User node pools manually or automatically using the [cluster autoscaler](/azure/aks/cluster-autoscaler) feature in an AKS cluster.
> - Consider using [Azure Spot VMs](/azure/aks/spot-node-pool) for workloads that can handle interruptions, early terminations, or evictions.
> - Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.
> - Review performance metrics, starting with CPU, memory, storage, and network, to identify cost optimization opportunities by cluster, nodes, and namespace.
> - Assign pod requests and limits on AKS cluster.
> - Define pod distribution budgets (PDB) for workloads.
> - Separate workloads into different node pools and consider scaling user node pools to zero.

### Recommendations

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|----------------------------------|-----------|
|Use the [Start and Stop feature](/azure/aks/start-stop-cluster?tabs=azure-cli) in Azure Kubernetes Services (AKS).|The AKS Stop and Start cluster feature allows AKS customers to completely pause an AKS cluster, saving time and cost. The stop and start feature keeps cluster configurations in place and customers can pick up where they left off without reconfiguring the clusters.|
|Enforce resource quotas at the namespace level.|Resource quotas provide a way to reserve and limit resources across a development team or project. These quotas are defined on a namespace and can be used to set quotas on Compute resources, Storage resources, and Object counts. When you define resource quotas, all pods created in the namespace must provide limits or requests in their pod specifications. If they don't provide these values, you can reject the deployment.|
|Assign pod requests and limits on AKS cluster.|In your pod specifications, it's best practice to define requests and limits for CPU and memory consumption. If you don't include these values, the Kubernetes scheduler can't take into account the resources your applications require to aid in scheduling decisions. Set pod requests and limits on all pods in your YAML manifests. If the AKS cluster uses resource quotas, your deployment may be rejected if you don't define these values.|
|Define pod distribution budgets (PDB) for workloads.|To maintain the availability of applications, define PDBs to make sure that a minimum number of pods are available in the cluster.|
|Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your AKS cluster lets you run an efficient, cost-effective cluster.|
|Consider using [Azure Spot VMs](/azure/aks/spot-node-pool) for workloads that can handle interruptions, early terminations, or evictions.|For example, workloads such as batch processing jobs, development, and testing environments, and large compute workloads may be good candidates for you to schedule on a spot node pool. Using spot VMs for nodes with your AKS cluster allows you to take advantage of unused capacity in Azure at a significant cost savings.|
|Separate workloads into different node pools and consider scaling user node pools to zero.|Unlike System node pools that always require running nodes, user node pools allow you to scale to `0`.|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve Azure Kubernetes Service, for this category there aren't any related recommendations.

### Policy definitions

While there are no built-in policies that are related to cost optimization, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional cost optimization constraints you'd like to enforce in your cluster and workload architecture.

## Operational excellence

Monitoring and diagnostics are crucial. Not only can you measure performance statistics but also use metrics troubleshoot and remediate issues quickly. We recommend you review the [Operational excellence design principles](/azure/architecture/framework/devops/principles).

### Design checklist

> [!div class="checklist"]
> - Review [AKS best practices](/azure/aks/best-practices) documentation.
> - Run multiple workloads in a single AKS cluster.
> - Reboot nodes only when updates and patches require it.
> - Don't modify resources in the [node resource group (for example MC_)](/azure/aks/faq#why-are-two-resource-groups-created-with-aks). You should *only* make modifications at [cluster creation time](/azure/aks/faq#can-i-provide-my-own-name-for-the-aks-node-resource-group), or with assistance from [Azure Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/supportPlans).
> - Monitor your clusters using [Azure Monitor and App Insights](/azure/azure-monitor/containers/container-insights-overview) to collect metrics, logs, and diagnostics to monitor the availability and performance of the cluster and workloads running on it. Enable and review [Kubernetes master node logs](/azure/azure-monitor/containers/container-insights-log-query#resource-logs) and additionally, configure [scraping of Prometheus metrics](/azure/azure-monitor/containers/container-insights-prometheus-integration) with Container insights.
> - Use the [AKS Uptime SLA](/azure/aks/uptime-sla) for production grade clusters.
> - Store container images within Azure Container Registry and enable [geo-replication](/azure/aks/operator-best-practices-multi-region#enable-geo-replication-for-container-images) to replicate container images across leveraged AKS regions.
> - Use [Availability Zones](/azure/aks/availability-zones) to maximize resilience within an Azure region by distributing AKS agent nodes across physically separate data centers.
> - Use a template-based deployment using Terraform, Ansible, and others only. Make sure that all deployments are repeatable and traceable, and stored in a source code repo. Can be combined with GitOps.
> - Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.

### Node pool design checklist

> [!div class="checklist"]
> - Use [multiple node pools](/azure/aks/use-multiple-node-pools) to support workloads that have different compute or storage demands. 
> - Keep the System node pool isolated from application workloads.
> - Use dedicated node pools for infrastructure tools that require high resource utilization, such as Istio, or have a special scale, or load behavior.

### Recommendations

Explore the following table of recommendations to optimize your AKS configuration for operations.

| Recommendation | Benefit |
|--------|----|
|Review AKS best practices documentation.|To build and run applications successfully in AKS, there are key considerations to understand and implement. These areas include multi-tenancy and scheduler features, cluster, and pod security, or business continuity and disaster recovery.|
|Configure monitoring of cluster with Container insights. | Container insights helps monitor the performance of containers by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Container logs are also collected.|
|Monitor application performance with Azure Monitor. |Configure [Application Insights](/azure/azure-monitor/app/app-insights-overview) for code-based monitoring of applications running in an AKS cluster.|
|Configure scraping of Prometheus metrics with Container insights.|Container insights, which is part of Azure Monitor, provides a seamless onboarding experience to collect Prometheus metrics. Reference [Configure scraping of Prometheus metrics](/azure/azure-monitor/containers/container-insights-prometheus-integration) for more information.|
|Adopt a [multiregion strategy](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.|Internet facing workloads should leverage [Azure Front Door](/azure/frontdoor/front-door-overview), [Azure Traffic Manager](/azure/aks/operator-best-practices-multi-region#use-azure-traffic-manager-to-route-traffic), or a third-party CDN to route traffic globally across AKS clusters.|

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

### Design checklist

As you make design choices for Azure Kubernetes Service, review the [Performance efficiency principles](/azure/architecture/framework/scalability/principles).

[!div class="checklist"]
> - Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.
> - Use the [Horizontal pod autoscaler](/azure/aks/concepts-scale#horizontal-pod-autoscaler) to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.
> - Separate workloads into different node pools and consider scaling user node pools to zero.
> - Define your resource profile to match the application performance levels that your application requires.
> - Use node affinity and pod affinity to specify certain nodes your pods run on.

### Recommendations

Explore the following table of recommendations to optimize your Azure Kubernetes Service configuration for performance.

| Recommendation | Benefit |
|--------|----|
|Enable [cluster autoscaler](/azure/aks/cluster-autoscaler) to automatically adjust the number of agent nodes in response to resource constraints.|The ability to automatically scale up or down the number of nodes in your AKS cluster lets you run an efficient, cost-effective cluster.|
|Separate workloads into different node pools and consider [scaling](azure/aks/scale-cluster) user node pools to zero.|Unlike System node pools that always require running nodes, user node pools allow you to scale to `0`.|
|Use AKS [advanced scheduler features](/azure/aks/operator-best-practices-advanced-scheduler). | Helps control balancing of resources for workloads that require them.|  

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
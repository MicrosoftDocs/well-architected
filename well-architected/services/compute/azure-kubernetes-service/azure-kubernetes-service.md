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

Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with the [baseline architecture for an Azure Kubernetes Service (AKS) cluster](/architecture/reference-architectures/containers/aks/secure-baseline-aks) and [Microservices architecture on Azure Kubernetes Service](/architecture/reference-architectures/containers/aks-microservices/aks-microservices).

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
> - Adopt a [multi-region](/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) strategy by deploying AKS clusters deployed across different Azure regions to maximize availability and provide business continuity.

### Node pool design checklist

> [!div class="checklist"]
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

### Cluster & workload architecture

- Ensure clusters have readiness or liveness probes

  In addition to the built-in Azure Policy definitions, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional reliability constraints you'd like to enforce in your cluster and workload architecture.

## Security

Security is one of the most important aspects of any architecture. To explore how AKS can bolster the security of your application workload, we recommend you review the [Security design principles](../../security/security-principles.md).

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

#### Cluster Architecture

- Microsoft Defender-based policies
- Authentication mode and configuration policies (Azure AD, RBAC, disable local auth)
- API Server network access policies, including private cluster

#### Cluster & Workload Architecture

- Kubernetes cluster pod security initiatives Linux-based workloads
- Including pod and container capability policies such as AppArmor, sysctl, security caps, SELinux, seccomp, privileged containers, automount cluster API credentials
- Mount, volume drivers, and filesystem policies
- Pod/Container networking policies, such as host network, port, allowed external Ips, HTTPs, internal load balancers

Azure Kubernetes Service deployments often also use Azure Container Registry for Helm charts and container images. Azure Container Registry also supports a wide variety of Azure policies that spans network restrictions, access control, and Microsoft Defender for Cloud, which complements a secure AKS architecture.

In addition to the built-in policies, custom policies can be created for both the AKS resource and for the Azure Policy add-on for Kubernetes. This allows you to add additional security constraints you'd like to enforce in your cluster and workload architecture.

## Cost optimization

<!-- 5C. ----------------------------------------------------

    Follow the Cost optimization H2 heading with a sentence about how the section contributes to the framework. 
-->

[H2 section introduction here.]

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Operational excellence

<!-- 5D. ----------------------------------------------------

    Follow the Operational excellence H2 heading with a sentence about how the section contributes to the framework. 
-->

[H2 section introduction here.]

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Performance efficiency

<!-- 5E. ----------------------------------------------------

    Follow the Performance efficiency H2 heading with a sentence about how the section contributes to the framework. 
-->

### Design checklist

<!--

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

As you make design choices for \<product>, review the \[design principles](\<design principles link>) for \<pillar>.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

<!--

    Required    

    The Recommendations H3 is always the second section in each pillar. The content in this section should be formatted as a table.

-->

Explore the following table of recommendations to optimize your \<product> configuration for \<pillar>.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md).

### Policy definitions

<!-- 

    Required

    A list of policy definitions is always the third section in each pillar.

    Provide a list of policies the customer can use for resource governance. After the list of policies, provide a list of all policy definitions available in Azure.
-->

- \<Policy>
- \<Policy>
- \<Policy>

All built-in policy definitions related to Azure Virtual Machines are listed in \[Built-in policies - \<category>]\(/azure/governance/policy/samples/built-in-policies#\<anchorlink>\).

## Azure Advisor recommendations

<!-- 6. Azure Advisor recommendations -----------------------------------------

    Required    

    Follow the Azure Advisor recommendations H2 heading with a sentence about how recommendations might help improve the configuration of the resource. 

-->

## Additional resources

<!-- 7. Additional resources ----------------------------------------------------

    Optional

    The resources should be separated in sections. The H3 heading should indicate the purpose of the resource. 

-->

- \[\<link text>]\(\<link>)
- \[\<link-text>]\(\<link>)

## Next steps

<!-- 8. Next steps ------------------------------------------------------------

    Required

    Add a context sentence for the following links: 

    Best practices:
        - Provide at least one next step and no more than three.
        - Include some context, so the customer can determine why they would select the link.
        - Do not use a "More information" or "See also" section.
        - Do provide a link to relevant quickstarts in the product documentation.
        - Do provide a link to a Learn module that covers resource provisioning.

-->

- \[\<link text>]\(\<link>)

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->
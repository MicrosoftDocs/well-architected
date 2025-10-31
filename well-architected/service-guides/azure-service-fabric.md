---
title: Architecture Best Practices for Azure Service Fabric
description: Learn how to incorporate the Well-Architected Framework pillars when you use Azure Service Fabric to manage scalable and reliable microservices and containers.
author: tomvcassidy
ms.author: tomcassidy
ms.date: 02/19/2025
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-service-guide
products: azure-service-fabric
azure.category:
  - containers
---

# Architecture best practices for Azure Service Fabric

Azure Service Fabric is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers. These resources are deployed onto a network-connected set of virtual or physical machines, known as a *cluster*.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/microservices/design/compute-options) and chose Service Fabric as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Service Fabric

> [!NOTE]
> 
> This service guide builds on guidance in the [Azure Virtual Machines and scale sets service guide](./virtual-machines.md). Virtual machine (VM) scale sets support Service Fabric nodes. Refer to that service guide for recommendations about operating the compute back end for your Service Fabric nodes.

It's important to distinguish between *cluster* and *workload* in Service Fabric architectural and configuration guidance. The Service Fabric cluster admin and their resource provider share responsibility for cluster configuration. Developers handle workload configuration. This guide provides considerations and recommendations for both roles.

The following design checklists and recommendations indicate whether the guidance is applicable to cluster architecture, workload architecture, or both.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Workload design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the reliability of Service Fabric. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - (Cluster) Determine the appropriate [reliability level](/azure/service-fabric/service-fabric-cluster-capacity) for your cluster based on the overall reliability target metric for the workload. The reliability level of the cluster that you identify dictates the minimum number of nodes to deploy for your primary node type. For more information, see [Reliability levels](/azure/service-fabric/service-fabric-best-practices-capacity-scaling#reliability-levels).
>
> - (Cluster) For critical workloads, consider using [availability zones](/azure/service-fabric/how-to-managed-cluster-availability-zones) for your Service Fabric clusters.
> - (Cluster) For production scenarios, use the Standard managed cluster SKU with a [Silver durability tier](/azure/service-fabric/service-fabric-cluster-capacity#durability-characteristics-of-the-cluster), which requires at least five VMs. The Standard SKU provides greater reliability than the Basic SKU, which is suitable for nonproduction scenarios.
> - (Cluster) Create extra secondary node types for your workloads to isolate different workload types, such as front-end services and back-end services. Then you can manage and scale those services independently. Each [node type is backed by its own scale set](/azure/service-fabric/service-fabric-cluster-nodetypes).

### Configuration recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) [Azure API Management can integrate with Service Fabric](/azure/service-fabric/service-fabric-api-management-overview) directly. Consider using API Management to expose and offload the cross-cutting functionality for cluster-hosted APIs.|  API Management is a feature-rich application gateway that helps you securely publish, manage, and monitor APIs that are deployed to the Service Fabric cluster.|
| (Workload) For stateful workload scenarios, consider using the [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction) model.|The Reliable Services model keeps your services active when you experience system failures or network problems, or if the services themselves encounter malfunctions. For stateful services, your state is preserved when malfunctions occur.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Service Fabric.

### Workload design checklist

Start your design strategy based on the [design review checklist for Security](../security/checklist.md) and identify vulnerabilities and controls to improve the security posture. Extend the strategy to include more approaches as needed.

> [!div class="checklist"]
> - (Cluster and workload) Familiarize yourself with Service Fabric product security guidance. For more information, see the following resources:
>   - [Security best practices](/azure/service-fabric/service-fabric-best-practices-security)
>   - [Cluster security scenarios](/azure/service-fabric/service-fabric-cluster-security)
>   - [Application and service security](/azure/service-fabric/service-fabric-application-and-service-security)
>
> - (Cluster) Apply network segmentation and controls. Configure network security groups (NSGs) to restrict traffic flow between subnets and node types.
> - (Cluster) Use native tools to securely manage application secrets and client certificates. To manage application secrets, use the Service Fabric Secret Store. To manage certificates, use Azure Key Vault.
> - (Cluster) Consider using your [existing load balancer](/azure/service-fabric/how-to-managed-cluster-networking#bring-your-own-azure-load-balancer) so that you can use an internal load balancer and define different load balancers and NSGs for each node type.
> - (Cluster) Enable Microsoft Entra integration to help securely control access to the cluster. Users can authenticate with their Entra credentials. Alternatively, you can use cluster client and admin certificates. Don't distribute the cluster client certificates among Service Fabric Explorer users.
> - (Cluster and workload) Create a process to monitor the expiration date of client certificates.
> - (Cluster and workload) Maintain separate clusters for development, staging, and production. Production environments typically require stricter security controls than nonproduction environments. And isolating environments from each other adds a layer of security if one environment is compromised. 

### Configuration recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) Ensure that you open the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) for application deployment and workloads.| This configuration helps ensure secure communication between the Service Fabric resources and the rest of the workload.|
| (Cluster) Use a separate [data encipherment certificate](/azure/service-fabric/how-to-managed-cluster-application-secrets#create-a-data-encipherment-certificate) to encrypt values when you use the Service Fabric Secret Store to distribute secrets.| A separate encipherment certificate isolates certificates, which reduces the risk of a single point of failure and provides more granular access control.|
| (Cluster) Apply an [access control list (ACL)](/azure/service-fabric/service-fabric-best-practices-security#apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster) to your client certificate for your Service Fabric cluster.| An ACL provides an extra level of authentication so that you can better control access to certificates. |
| (Cluster) Use [resource requests and limits](/azure/service-fabric/service-fabric-resource-governance#resource-governance-mechanism) to govern resource usage across the nodes in your cluster.| Resource limits help ensure that one service doesn't consume too many resources and deprive other services.|
| (Workload) Include client certificates in Service Fabric applications.|Client certificates for authentication provide enhanced security at both the cluster and workload level.|
| (Workload) Use a [managed identity](/azure/service-fabric/how-to-managed-identity-managed-cluster-virtual-machine-scale-sets) to authenticate Service Fabric applications to Azure resources.| Managed identities help you securely manage the credentials in your code for authenticating to various services, without saving them locally on a developer's workstation or in source control.|
| (Cluster and workload) To host untrusted applications: <br> - Use the strongest sandboxing technology available. <br> - Remove access to Service Fabric runtime. <br> - Follow other [Service Fabric best practices](/azure/service-fabric/service-fabric-best-practices-security#hosting-untrusted-applications-in-a-service-fabric-cluster).| These practices help ensure that only trusted and verified applications can interact with critical components. They also reduce the impact of untrusted applications that have vulnerabilities or malicious code on normal cluster operations.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Service Fabric and its environment.

### Workload design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - (Workload and cluster) Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate the intial cost. The compute instances, storage, networking resources, and IP addresses that you choose when you create a Service Fabric cluster incur charges. Service Fabric itself doesn't incur charges. For more information about cost modeling, see the [example cost calculation process for application planning](/azure/service-fabric/service-fabric-capacity-planning#use-a-spreadsheet-for-cost-calculation).
>
> - (Cluster) Select appropriate VM SKUs. Choose VMs based on workload characteristics. For example, determine whether the workload is CPU intensive or runs interruptible processes.
> - (Cluster) Select appropriate cluster SKUs. Use the Standard SKU for production environments and the Basic SKU for nonproduction environments, unless you have a specific reason to do otherwise. Use appropriate node types and sizes in each environment.
> - (Cluster and workload) Select appropriate managed disk tiers and sizes. Review the Well-Architected Framework [service guide for disk storage](./azure-disk-storage.md#cost-optimization). To prevent unnecessary resource expenses, don't use VM SKUs that include temporary disk offerings. 

### Configuration recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) Consider a VM SKU that includes [temporary disk support](/azure/service-fabric/how-to-managed-cluster-stateless-node-type#temporary-disk-support) if you don't need to maintain statefulness. | Optimize the resources that you pay for. A temporary disk instead of a managed disk can reduce costs for stateless workloads.|
| (Cluster and workload) Align your [VM SKU selection](/azure/virtual-machines/sizes) with workload requirements. Ensure that you identify the correct node types, which are [hosted on scale sets](/azure/service-fabric/service-fabric-cluster-nodetypes), to meet your requirements.|Matching your selection to your workload demands helps you avoid paying for unnecessary and expensive VM SKUs.|
| (Cluster and workload) Align your [disk type selection](/azure/service-fabric/how-to-managed-cluster-managed-disk) with workload requirements.| An appropriate managed disk type helps you avoid paying for expensive and unnecessary disk types.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Workload design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Service Fabric.

> [!div class="checklist"]
> - (Cluster and workload) Integrate your Service Fabric components, including clusters, related infrastructure, and the application itself, into your monitoring and alerting platform. For more information, see [Monitoring best practices](/azure/service-fabric/service-fabric-best-practices-monitoring).
>
> - (Cluster and workload) Use the Service Fabric [health model](/azure/service-fabric/service-fabric-health-introduction) to continuously monitor the health of your solution. This tool should complement your overall [workload health model](../design-guides/health-modeling.md).
> - (Cluster and workload) Create a process to monitor the expiration date of client certificates. For example, a feature of Key Vault sends an email when a specific percentage of the certificate's lifespan elapses.
> - (Cluster and workload) Use continuous integration and continuous deployment (CI/CD) practices to manage your cluster deployments. Use a purposely built tool like Azure Pipelines or Github Actions to manage your CI/CD pipelines. These tools help you centrally manage all workload deployments in all environments with proper source control strategies.

### Configuration recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Workload) Use Application Insights to [monitor your workloads](/azure/service-fabric/service-fabric-best-practices-monitoring#application-monitoring).|Application Insights provides comprehensive application performance monitoring for live web applications, which enables you to collect and analyze application telemetry. This practice improves your application health and performance monitoring.|
| (Cluster and workload) Use Azure Monitor to [monitor cluster and container infrastructure events](/azure/service-fabric/service-fabric-best-practices-monitoring#cluster-monitoring).|Azure Monitor provides comprehensive monitoring and diagnostic capabilities that you can use to collect and analyze logs and metrics from your applications and Azure infrastructure. Azure Monitor integrates well with the Azure platform, including Service Fabric.|
| (Cluster) Implement appropriate [cluster health policies](/azure/service-fabric/service-fabric-health-introduction#cluster-health-policy) as part of your health modeling.| These policies enable you to customize how failures are assessed in terms of cluster health. For example, you can set the maximum percentage of unhealthy nodes that are tolerated before the cluster is considered to be in error. 
| (Cluster) Implement appropriate [application and service type health policies](/azure/service-fabric/service-fabric-health-introduction#application-health-policy) as part of your health modeling.| The application health policy describes how events are evaluated and how the health states for applications and their child components are aggregated. Service Fabric considers an entity unhealthy if it has a health report or if any of its child components are in a warning or error state.|
| (Cluster and workload) Use [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-chaos-experiments) to inject malfunctions into your solution as part of your testing strategy.|Purposely introducing malfunctions into your solution helps you identify potential failure points and practice incident response measures.|
| (Cluster and workload) Use [Azure Pipelines as your CI/CD solution](/azure/service-fabric/service-fabric-tutorial-deploy-app-with-cicd-vsts).| A CI/CD solution like Azure Pipelines helps you manage your deployments efficiently, consistently, and safely. Azure Pipelines has native support for Service Fabric deployments.|


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Workload design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md) for defining a baseline based on key performance indicators for Service Fabric.

> [!div class="checklist"]
> - (Cluster) Take advantage of performance optimization and enhancing features that your workload requires. For recommendations about the underlying compute platform, see the [Virtual Machines service guide](./virtual-machines.md#performance-efficiency).
>
> - (Cluster) Deploy VMs and disk sizes that meet your performance requirements without incurring unnecessary expenses for unused capacity. Ensure that you can easily add capacity to meet your future growth plans.
> - (Workload) Understand the programming models that Service Fabric supports. Choose the best model for your workload requirements. Each programming model has unique advantages and disadvantages, and your particular workload requirements might align with one model better than others.
> - (Workload) Use established cloud architecture patterns to design your workload. Patterns that complement Service Fabric application designs include [microservice](/azure/architecture/guide/architecture-styles/microservices), [event-driven](/azure/architecture/guide/architecture-styles/event-driven), and [background-processing](/azure/architecture/guide/architecture-styles/web-queue-worker) architecture patterns.

### Configuration recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) [Exclude the Service Fabric processes](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) that run on your Windows VMs from Microsoft Defender Antivirus if your security policies allow you to exclude processes and paths for open-source software.| This configuration reduces the performance impact and resource consumption overhead that Microsoft Defender Antivirus incurs.|
| (Cluster) Enable [autoscaling](/azure/service-fabric/how-to-managed-cluster-autoscale) for your cluster to add or reduce nodes on demand for a secondary node type.| Autoscaling reduces management overhead and potential business impact because you can monitor and optimize the number of nodes that service your workload.|
| (Cluster) Consider using [accelerated networking](/azure/service-fabric/how-to-managed-cluster-networking#enable-accelerated-networking).|Accelerated networking enables a high-performance path that bypasses the host from the data path, which reduces latency, jitter, and CPU usage for the most demanding network workloads.|
| (Cluster) Consider using [encryption at host](/azure/service-fabric/how-to-managed-cluster-enable-disk-encryption#enable-encryption-at-host) instead of Azure Disk Encryption.|Encryption at host supports all OS types and images, including custom images, by encrypting your VM data in Azure Storage. Azure Disk Encryption doesn't support these features. |
| (Workload) Implement the [Service Fabric programming models](/azure/service-fabric/service-fabric-choose-framework) that best suit your workload.|An appropriate programming model takes advantage of built-in features that support workload requirements, like state management, concurrency, and reuse of your existing codebase. To maintain your deployment standards, choose a programming model that aligns with those standards.|
| (Cluster and workload) Implement scaling to meet your business requirements. Find the right [scaling mechanism](/azure/service-fabric/service-fabric-concepts-scalability) for your workload. |Scaling enables maximum resource usage for your solution.|

## Azure policies

Azure provides an extensive set of built-in policies related to Service Fabric and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Service Fabric clusters are configured to be zone redundant.
- Service Fabric clusters have the `ClusterProtectionLevel` property set to `EncryptAndSign`.
- Service Fabric clusters are configured to only use Microsoft Entra ID for client authentication.

For comprehensive governance, review the [Azure Policy built-in definitions for Service Fabric](/azure/governance/policy/samples/built-in-policies#service-fabric) and other policies that might affect the security of clusters and applications.

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments.

For more information, see [Azure Advisor](/azure/advisor).

## Example architecture

Foundational architecture that demonstrates the key recommendations: [Container orchestration for microservices](/azure/architecture/microservices/design/orchestration).

## Related content

- [Service Fabric managed cluster configuration options](/azure/service-fabric/how-to-managed-cluster-configuration)
- [Azure application architecture fundamentals](/azure/architecture/guide/)
- [Deploy a Service Fabric managed cluster with an Azure Resource Manager template](/azure/service-fabric/quickstart-managed-cluster-template)
- [Deploy a Service Fabric managed cluster by using the Azure portal](/azure/service-fabric/quickstart-managed-cluster-portal)

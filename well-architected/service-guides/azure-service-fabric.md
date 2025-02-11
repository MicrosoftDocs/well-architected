---
title: Well-Architected Framework perspective on Azure Service Fabric
description: Design considerations and recommendations about Azure Service Fabric.
author: tomvcassidy
ms.author: tomcassidy
ms.date: 1/28/2025
ms.topic: conceptual
ms.service: azure-waf
ms.subservice: waf-service-guide
products: azure-service-fabric
azure.category:
  - containers
---

# Well-Architected Framework perspective on Azure Service Fabric

Azure Service Fabric is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers. These resources are deployed onto a network-connected set of virtual or physical machines, which is called a **cluster**.

This article assumes that as an architect, you've reviewed the [compute decision tree](/azure/architecture/microservices/design/compute-options) and chose Service Fabric as the compute platform for your workload. The guidance in this article provides architectural recommendations that are mapped to the principles of the [Well-Architected Framework pillars](../pillars.md).

> [!IMPORTANT]
>
> **How to use this guide**
>
> Each section has a *design checklist* that presents architectural areas of concern along with design strategies localized to the technology scope.
>
> Also included are recommendations for the technology capabilities that can help materialize those strategies. The recommendations don't represent an exhaustive list of all configurations that are available for Azure Service Fabric and its dependencies. Instead, they list the key recommendations mapped to the design perspectives. Use the recommendations to build your proof-of-concept or to optimize your existing environments.
>
> Foundational architecture that demonstrates the key recommendations: [Microservices architecture on Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric).

**Technology scope**

This review focuses on the interrelated decisions for the following Azure resources:

- Service Fabric

> [!NOTE]
> 
> This service guide builds upon guidance found in the [Virtual Machines and scale sets](./virtual-machines.md) service guide. Service Fabric nodes are backed by VM scale sets, so refer to that service guide for recommendations on operating the compute backend for your Service Fabric nodes.

When discussing architectural considerations and configuration recommendations with Azure Service Fabric, it's important to distinguish between *cluster* and *workload*. Cluster configuration is a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload configuration is the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

## Reliability

The purpose of the Reliability pillar is to provide continued functionality by **building enough resilience and the ability to recover fast from failures**.

[Reliability design principles](../reliability/principles.md) provide a high-level design strategy applied for individual components, system flows, and the system as a whole.

### Design checklist

Start your design strategy based on the [design review checklist for Reliability](../reliability/checklist.md). Determine its relevance to your business requirements while keeping in mind the [offering-specific-aspects]. Extend the strategy to include more approaches
as needed.

> [!div class="checklist"]
> - (Cluster) Determine the appropriate [reliability level](/azure/service-fabric/service-fabric-cluster-capacity) for your cluster based on the overall reliablity target metric for the workload. The reliablity level of the cluster that you identify will dictate the minimum number of nodes to deploy for your primary node type. See the [capacity planning documentation](/azure/service-fabric/service-fabric-best-practices-capacity-scaling#reliability-levels) to learn about making these determinations.
> - (Cluster) For critical workloads, consider using [Availability Zones](/azure/service-fabric/how-to-managed-cluster-availability-zones) for your Service Fabric clusters.
> - (Cluster) For production scenarios, use the Standard managed cluster SKU with a [Silver durability tier](/azure/service-fabric/service-fabric-cluster-capacity#durability-characteristics-of-the-cluster) (5 VMs) or greater . This SKU offers higher reliability capabilities than the Basic SKU, which should be used for nonproduction scenarios.
> - (Cluster) Create additional, secondary node types for your workloads to isolate different workload types. This can help you separate front-end services from backend services, allowing you to manage and scale those services independently. Each [node type is backed by its own scale set](/azure/service-fabric/service-fabric-cluster-nodetypes).

### Recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) API Management (APIM) can [integrate](/azure/service-fabric/service-fabric-api-management-overview) with Service Fabric directly. Consider using it to expose and offload cross-cutting functionality for APIs hosted on the cluster.|  APIM is a feature-rich application gateway that helps you securely publish, manage, and monitor APIs deployed to the Service Fabric cluster.|
| (Workload)  For stateful workload scenarios, consider using [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction).|The Reliable Services model allows your services to stay up when you experience system failures or network issues, or in cases where the services themselves encounter malfunctions. For stateful services, your state is preserved when malfunctions occur.|

## Security

The purpose of the Security pillar is to provide **confidentiality, integrity, and availability** guarantees to the workload.

The [Security design principles](../security/principles.md) provide a high-level design strategy for achieving those goals by applying approaches to the technical design of Service Fabric.

### Design checklist

Base your design strategy on the [design review checklist for Security](../security/checklist.md).

> [!div class="checklist"]
> - (Cluster and workload) Familiarize yourself with Service Fabric product security guidance. See [security best practices](/azure/service-fabric/service-fabric-best-practices-security), [cluster security scenarios](/azure/service-fabric/service-fabric-cluster-security), and [Service Fabric application and service security](/azure/service-fabric/service-fabric-application-and-service-security).
> - (Cluster) Apply network segmentation and controls by configuring NSGs to restrict traffic flow between subnets and node types.
> - (Cluster) Securely manage application secrets and client certificates using native tools. Application secrets should be managed with the Service Fabric Secret Store and certifcates should be managed with Key Vault.
> - (Cluster) Consider [bringing your own load balancer](/azure/service-fabric/how-to-managed-cluster-networking#bring-your-own-azure-load-balancer), which allows you to use an internal load balancer and to define different load balancers and NSGs for each node type.
> - (Cluster) Securely control access to the cluster by enabling Microsoft Entra integration, allowing users to authenticate with their Entra credentials. Alternatively, you can use cluster client and admin certificates. Don't distribute the cluster client certificates among users of Service Fabric Explorer.
> - (Cluster and workload) Create a process for monitoring the expiration date of client certificates.
> - (Cluster and workload) Maintain separate clusters for development, staging, and production. Production environments typically require stricter security controls than nonproduction environments and isolating environments from each other adds a layer of secuirty if one environment is compromised. 

### Recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for application deployment and workloads.| This configuration ensures that communication between the Service Fabric resources and the rest of the workload are secured.|
| (Cluster) When using the Service Fabric Secret Store to distribute secrets, use a separate [data encipherment certificate](/azure/service-fabric/how-to-managed-cluster-application-secrets?branch=main#create-a-data-encipherment-certificate) to encrypt the values.| Using a separate encipherment certificate ensures isolation between certificates, reducing the risk of a single point of failure and allowing for more granular access control.|
| (Cluster) Apply an [Access Control List (ACL)](/azure/service-fabric/service-fabric-best-practices-security#apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster) to your client certificate for your Service Fabric cluster.|Using an ACL provides an additional level of authentication, enabling more granular control over who can access the certificates.|
| (Cluster) Use [resource requests and limits](/azure/service-fabric/service-fabric-resource-governance#resource-governance-mechanism) to govern resource usage across the nodes in your cluster.|Enforcing resource limits helps ensure that one service doesn't consume too many resources and starve other services.|
| (Workload)  Include client certificates in Service Fabric applications.|Having your applications use client certificates for authentication provides opportunities for security at both the cluster and workload level.|
| (Workload)  Authenticate Service Fabric applications to Azure Resources using [Managed Identity](/azure/service-fabric/how-to-managed-identity-managed-cluster-virtual-machine-scale-sets).|Using Managed Identity allow you to securely manage the credentials in your code for authenticating to various services without saving them locally on a developer workstation or in source control.|
| (Cluster and workload) When hosting untrusted applications, use the strongest sandboxing technology applicable, remove access to Service Fabric runtime, and follow other [Service Fabric best practices](/azure/service-fabric/service-fabric-best-practices-security#hosting-untrusted-applications-in-a-service-fabric-cluster).|Following the best practices provided helps ensure that only trusted and verified applications are allowed to interact with critical components,  limiting the impact untrusted applications with vulnerabilities or malicious code can have on the normal operation of the cluster.|

## Cost Optimization

Cost Optimization focuses on **detecting spend patterns, prioritizing investments in critical areas, and optimizing in others** to meet the organization's budget while meeting business requirements.

The [Cost Optimization design principles](../cost-optimization/principles.md) provide a high-level design strategy for achieving those goals and making tradeoffs as necessary in the technical design related to Service Fabric and its environment.

### Design checklist

Start your design strategy based on the [design review checklist for Cost Optimization](../cost-optimization/checklist.md) for investments. Fine-tune the design so that the workload is aligned with the budget that's allocated for the workload. Your design should use the right Azure capabilities, monitor investments, and find opportunities to optimize over time.

> [!div class="checklist"]
> - (Workload and cluster) Estimate the intial cost using the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/). You are only charged for the compute instances, storage, networking resources, and IP addresses you choose when creating a Service Fabric cluster. There is no charge for the service offered by Service Fabric itself. To help get started in your cost modeling, see the [example cost calculation process for application planning](/azure/service-fabric/service-fabric-capacity-planning#use-a-spreadsheet-for-cost-calculation).
> - (Cluster) Select appropriate VM SKUs. Choose VMs based on workload characteristics. Is the workload CPU intensive or does it run interruptible processes?
> - (Cluster) Select appropriate cluster SKUs. Use Standard for production environments and Basic for nonproduction environments, unless there is a compelling reason to do otherwise. Use appropriate node types and sizes in each environment.
> - (Cluster and workload) Select appropriate managed disk tiers and sizes. Review the WAF [service guide for disk storage](./azure-disk-storage.md?branch=main#cost-optimization). Avoid using VM SKUs with temp disk offerings to avoid paying for unnecessary resources. 

### Recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) If you don't have requirements to maintain statefulness, consider choosing a VM SKU with [temporary disk support](/azure/service-fabric/how-to-managed-cluster-stateless-node-type#temporary-disk-support).|Make the most of the resources you're paying for. Using a temporary disk instead of a managed disk can reduce costs for stateless workloads.|
| (Cluster and workload) Align [VM SKU selection](/azure/virtual-machines/sizes) with workload requirements. Ensure that you've identified the correct node types, which are [hosted on scale sets](/azure/service-fabric/service-fabric-cluster-nodetypes), to meet your requirements.|Matching your selection to your workload demands helps you avoid paying for expensive VM SKUs that you might not need.|
| (Cluster and workload) Align [disk type selection](/azure/service-fabric/how-to-managed-cluster-managed-disk) with workload requirements.|Choosing the right managed disk type helps you avoid paying for expensive types that you might not need.|

## Operational Excellence

Operational Excellence primarily focuses on procedures for **development practices, observability, and release management**.

The [Operational Excellence design principles](../operational-excellence/principles.md) provide a high-level design strategy for achieving those goals for the operational requirements of the workload.

### Design checklist

Start your design strategy based on the [design review checklist for Operational Excellence](../operational-excellence/checklist.md) for defining processes for observability, testing, and deployment related to Service Fabric.

> [!div class="checklist"]
> - (Cluster and workload) Integrate your Service Fabric components, including clusters, related infrastructure, and the application itself, into your monitoring and alerting platform. See the [monitoring best practices](/azure/service-fabric/service-fabric-best-practices-monitoring) article for detailed guidance.
> - (Cluster and workload) Use the Service Fabric [health model](/azure/service-fabric/service-fabric-health-introduction) to continuously monitor the health of your solution. This tool should complement your overall [workload health model](../design-guides/health-modeling.md)
> - (Cluster and workload) Create a process for monitoring the expiration date of client certificates. For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.
> - (Cluster and workload) Use continuous integration and continuous deployment practices to manage your cluster deployments. Use a purposely built tool like Azure Pipelines or Github Actions to manage your CI/CD pipelines, which allows you to centrally manage all of your workload deployments in all environments with proper source control strategies.

### Recommendations

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Workload)  Use [Application Insights to monitor your workloads](/azure/service-fabric/service-fabric-best-practices-monitoring#application-monitoring).|Application Insights provides comprehensive application performance monitoring (APM) for live web applications, allowing you to collect and analyze application telemetry, enhancing your application health and performance monitoring.|
| (Cluster and workload) Use [Azure Monitor to monitor cluster and container infrastructure events](/azure/service-fabric/service-fabric-best-practices-monitoring#cluster-monitoring).|Azure Monitor provides comprehensive monitoring and diagnostic capabilities, allowing you to collect and analyze logs and metrics from your applications and Azure infrastructure. Azure Monitor integrates well with the Azure platform, including Service Fabric.|
| (Cluster) Implement appropriate [cluster health policies](/azure/service-fabric/service-fabric-health-introduction#cluster-health-policy) as part of your health modeling.| The policies allow you to customize how failures are interpreted from a cluster health perspective. For example, you can set the maximum tolerated percentage of nodes that can be unhealthy before the cluster is considered in error. 
| (Cluster) Implement appropriate [application and service type health policies](/azure/service-fabric/service-fabric-health-introduction#application-health-policy) as part of your health modeling.| The application health policy describes how the evaluation of events and child-states aggregation is done for applications and their children. Service Fabric assumes that the entity is unhealthy if it has a health report or a child at the warning or error health state.|
| (Cluster and workload) Use [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-chaos-experiments) to inject malfunctions into your solution as part of your testing strategy.|Purposely introducing malfunctions into your solution will help you identify potential failure points and practice your incident response measures.|
| (Cluster and workload) Use [Azure Pipelines for your continuous integration and deployment (CI/CD) solution](/azure/service-fabric/service-fabric-tutorial-deploy-app-with-cicd-vsts).|Using a CI/CD solution like Azure Pipelines helps you manage your deployments efficiently, consistently, and safely. Azure Pipelines has native support for Service Fabric deployments.|


## Performance Efficiency

Performance Efficiency is about **maintaining user experience even when there's an increase in load** by managing capacity. The strategy includes scaling resources, identifying and optimizing potential bottlenecks, and optimizing for peak performance.

The [Performance Efficiency design principles](../performance-efficiency/principles.md) provide a high-level design strategy for achieving those capacity goals against the expected usage.

### Design checklist

Start your design strategy based on the [design review checklist for Performance Efficiency](../performance-efficiency/checklist.md). Define a baseline that's based on key performance indicators for Service Fabric.

> [!div class="checklist"]
> - (Cluster) Take advantage of performance optimization and enhancing features as required by the workload. See the [VM service guide](./virtual-machines.md#performance-efficiency) for recommendations related to the underlying compute platform.
> - (Cluster) Deploy VM and disk sizes that meet your perfomance requirements without incurring unnecessary expenses for unused capacity. Ensure that you will be able to easily add capacity to meet your future growth plans.
> - (Workload) Understand the programming models supported by Service Fabric and choose the best model for your workload requirements. Each programming model has unique advantages and disadvantages, and your particular workload requirements may align with one model better than others.
> - (Workload) Use established cloud architecture patterns to design your workload. [Microsservices](/azure/architecture/guide/architecture-styles/microservices), [event-driven](/azure/architecture/guide/architecture-styles/event-driven), and [background processing](/azure/architecture/guide/architecture-styles/web-queue-worker) architecture patterns are all good candidates for Service Fabric application designs.

### Recommendation

|Recommendation|Benefit|
|-----------------------------------|-----------|
| (Cluster) [Exclude the Service Fabric processes running on your Windows VMs from Windows Defender](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) if your security policies allow you to exclude processes and paths for open-source software.| Excluding Service Fabric processes reduces the performance impact and resource consumption overhead incurred by Windows Defender|
| (Cluster) Consider using [Autoscaling](/azure/service-fabric/how-to-managed-cluster-autoscale) for your cluster to enable the addition or reduction of nodes on demand on a secondary node type.| Autoscaling reduces the management overhead and potential business impact by monitoring and optimizing the amount of nodes servicing your workload.|
| (Cluster) Consider using [Accelerated Networking](/azure/service-fabric/how-to-managed-cluster-networking#enable-accelerated-networking).|Accelerated networking enables a high-performance path that bypasses the host from the data path, which reduces latency, jitter, and CPU utilization for the most demanding network workloads.|
| (Cluster) Consider using [encryption at host](/azure/service-fabric/how-to-managed-cluster-enable-disk-encryption?tabs=azure-powershell#enable-encryption-at-host) instead of Azure Disk Encryption (ADE).|Encryption at host improves on ADE by supporting all OS types and images, including custom images, for your VMs by encrypting data in the Azure Storage service.|
| (Workload) Implement the [Service Fabric programming models](/azure/service-fabric/service-fabric-choose-framework) best suited for your workload.|Choosing an appropriate programming model allows you to take advantage of built-in features that support workload requirements like like state management, concurrency, and reuse of your existing codebase. You can also ensure that your deployment standards are maintained by selecting a programming model that aligns with those standards.|
| (Cluster and workload) Implement scaling to meet your business requirements. [Find the right scaling mechanism for your workload.](/azure/service-fabric/service-fabric-concepts-scalability).|You can use scaling to enable maximum resource utilization for your solution.|

## Azure policies

Azure provides an extensive set of built-in policies related to Service Fabric and its dependencies. Some of the preceding recommendations can be audited through Azure Policy. For example, you can check whether:

- Service Fabric Clusters are configured to be Zone Redundant.
- Service Fabric clusters have the ClusterProtectionLevel property set to `EncryptAndSign`.
- Service Fabric clusters are configured to only use Azure Active Directory for client authentication.

For comprehensive governance, review the [Azure Policy built-in definitions for Service Fabric](/azure/governance/policy/samples/built-in-policies#[Azure-offering-anchor]) and other policies that might impact the security of the [cloud-infrastructure-area].

## Azure Advisor recommendations

Azure Advisor is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence of Service Fabric.

- [Reliability](/azure/advisor/advisor-high-availability-recommendations#[offering-or-infrastructure-area-anchor])
- [Security](/azure/defender-for-cloud/recommendations-reference#compute-recommendations#[offering-or-infrastructure-area-anchor])
- [Cost Optimization](/azure/advisor/advisor-cost-recommendations#[offering-or-infrastructure-area-anchor])
- [Performance](/azure/advisor/advisor-reference-performance-recommendations#[offering-or-infrastructure-area-anchor])
- [Operational Excellence](/azure/advisor/advisor-reference-operational-excellence-recommendations#[offering-or-infrastructure-area-anchor])

## Related content

Check out the [Azure Service Fabric managed cluster configuration options article](/azure/service-fabric/how-to-managed-cluster-configuration) for a list of all the options you have while creating and maintaining your cluster.

Review the [Azure application architecture fundamentals](/azure/architecture/guide/) for guidance on how to develop your workloads. While Service Fabric can be used solely as a container hosting platform, using well-architected workloads leverages Service Fabric's full functionality.

Use these recommendations as you create your Service Fabric managed cluster using an ARM template or through the Azure portal:

- [Quickstart: Deploy a Service Fabric managed cluster with an Azure Resource Manager template](/azure/service-fabric/quickstart-managed-cluster-template)
- [Quickstart: Deploy a Service Fabric managed cluster using the Azure portal](/azure/service-fabric/quickstart-managed-cluster-portal)

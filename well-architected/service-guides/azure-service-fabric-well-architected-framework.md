---
title: Azure Well-Architected Framework (WAF) review for Azure Service Fabric
description: Design considerations and recommendations about Azure Service Fabric.
author: tomvcassidy
ms.author: tomcassidy
ms.topic: conceptual
ms.date: 3/18/2022
products: azure-service-fabric
---

# Azure Well-Architected Framework review - Azure Service Fabric

[Azure Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers. These resources are deployed onto a network-connected set of virtual or physical machines, which is called a **cluster**.

There are two clusters models in Azure Service Fabric: **standard clusters** and **managed clusters**.

**Standard clusters** require you to define a cluster resource alongside a number of supporting resources. These resources must be set up correctly upon deployment and maintained correctly throughout the lifecycle of the cluster. Otherwise, the cluster and your services will not function properly.

**Managed clusters** simplify your deployment and management operations. The managed cluster model consists of a single Service Fabric managed cluster resource that encapsulates and abstracts away the underlying resources.

This article primarily discusses the **managed cluster** model for simplicity. However, call-outs are made for any special considerations that apply to the **standard cluster** model.

In this article, you learn architectural best practices for Azure Service Fabric. The guidance is based on the five pillars of architectural excellence:

* Reliability
* Security
* Cost optimization
* Operational excellence
* Performance efficiency

## Prerequisites

* Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. Check out the [Azure Well-Architected Framework overview page](../pillars.md) to review the five pillars of architectural excellence.

* Reviewing the [core concepts of Azure Service Fabric](/azure/service-fabric/service-fabric-content-roadmap) and [microservice architecture](/azure/architecture/reference-architectures/microservices/service-fabric) can help you understand the context of the best practices provided in this article.

## Reliability

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and reliability.

When discussing reliability with Azure Service Fabric, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload reliability is the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For more information about Azure Service Fabric cluster reliability, check out the [capacity planning documentation](/azure/service-fabric/service-fabric-best-practices-capacity-scaling#reliability-levels).

For more information about Azure Service Fabric workload reliability, reference the [Reliability subsystem](/azure/service-fabric/service-fabric-architecture#reliability-subsystem) included in the Service Fabric architecture.

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/well-architected/resiliency/principles) for adding reliability to the architecture.

> [!div class="checklist"]
> - **Cluster architecture:** Use [Standard SKU](/azure/service-fabric/overview-managed-cluster#service-fabric-managed-cluster-skus) for production scenarios. **Standard cluster:** Use [durability level Silver](/azure/service-fabric/service-fabric-cluster-capacity#durability-characteristics-of-the-cluster) (5 VMs) or greater for production scenarios.
> - **Cluster architecture:** For critical workloads, consider using [Availability Zones](/azure/service-fabric/how-to-managed-cluster-availability-zones) for your Service Fabric clusters.
> - **Cluster architecture:** For production scenarios, use the Standard tier load balancer. Managed clusters create an Azure public Standard Load Balancer and fully qualified domain name with a static public IP for both the primary and secondary node types. You can also [bring your own load balancer](/azure/service-fabric/how-to-managed-cluster-networking#bring-your-own-azure-load-balancer), which supports both Basic and Standard SKU load balancers.
> - **Cluster architecture:** Create additional, secondary node types for your workloads.

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for service reliability:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Cluster architecture:** Use Standard SKU for production scenarios.|This level ensures the resource provider maintains cluster reliability. **Standard cluster:** A Standard SKU managed cluster provides the equivalent of durability level Silver. To achieve this using the standard cluster model, you will need to use 5 VMs (or more).|
|**Cluster architecture:** Consider using Availability Zones for your Service Fabric clusters.|Service Fabric managed cluster supports deployments that span across multiple Availability Zones to provide zone resiliency. This configuration will ensure high-availability of the critical system services and your applications to protect from single-points-of-failure.|
|**Cluster architecture:** Consider using Azure API Management to expose and offload cross-cutting functionality for APIs hosted on the cluster.|API Management can [integrate](/azure/service-fabric/service-fabric-api-management-overview) with Service Fabric directly.|
|**Workload architecture:** For stateful workload scenarios, consider using [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction).|The Reliable Services model allows your services to stay up even in unreliable environments where your machines fail or hit network issues, or in cases where the services themselves encounter errors and crash or fail. For stateful services, your state is preserved even in the presence of network or other failures.|

For more suggestions, see [Principles of the reliability pillar](/azure/well-architected/resiliency/principles).

## Security

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and security.

When discussing security with Azure Service Fabric, it's important to distinguish between *cluster security* and *workload security*. Cluster security is a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload security is the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For more information about Azure Service Fabric cluster security, check out [Service Fabric cluster security scenarios](/azure/service-fabric/service-fabric-cluster-security).

For more information about Azure Service Fabric workload security, reference [Service Fabric application and service security](/azure/service-fabric/service-fabric-application-and-service-security).

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/well-architected/security/security-principles) for adding security to the architecture.

> [!div class="checklist"]
> - **Cluster architecture:** Ensure Network Security Groups (NSG) are configured to restrict traffic flow between subnets and node types. Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for application deployment and workloads.
> - **Cluster architecture:** When using the Service Fabric Secret Store to distribute secrets, use a separate data encipherment certificate to encrypt the values.
> - **Cluster architecture:** [Deploy client certificates by adding them to Azure Key Vault](/azure/service-fabric/how-to-managed-cluster-application-secrets) and referencing the URI in your deployment.
> - **Cluster architecture:** Enable Microsoft Entra integration for your cluster to ensure users can access Service Fabric Explorer using their Microsoft Entra credentials. Don't distribute the cluster client certificates among users to access Explorer.
> - **Cluster architecture:** For client authentication, use admin and read-only client certificates and/or Microsoft Entra authentication.
> - **Cluster and workload architectures:** Create a process for monitoring the expiration date of client certificates.
> - **Cluster and workload architectures:** Maintain separate clusters for development, staging, and production.

### Recommendations

Consider the following recommendations to optimize your Azure Service Fabric configuration for security:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Cluster architecture:** Ensure Network Security Groups (NSG) are configured to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend).|
|**Cluster architecture:** Deploy Key Vault certificates to Service Fabric cluster virtual machine scale sets.|Centralizing storage of application secrets in Azure Key Vault allows you to control their distribution. Key Vault greatly reduces the chances that secrets may be accidentally leaked.|
|**Cluster architecture:** Apply an Access Control List (ACL) to your client certificate for your Service Fabric cluster.|Using an ACL provides an additional level of authentication.|
|**Cluster architecture:** Use [resource requests and limits](/azure/service-fabric/service-fabric-resource-governance#resource-governance-mechanism) to govern resource usage across the nodes in your cluster.|Enforcing resource limits helps ensure that one service doesn't consume too many resources and starve other services.|
|**Workload architecture:** Encrypt Service Fabric package secret values.|Encryption on your secret values provides an additional level of security.|
|**Workload architecture:** Include client certificates in Service Fabric applications.|Having your applications use client certificates for authentication provides opportunities for security at both the cluster and workload level.|
|**Workload architecture:** Authenticate Service Fabric applications to Azure Resources using [Managed Identity](/azure/service-fabric/how-to-managed-identity-managed-cluster-virtual-machine-scale-sets).|Using Managed Identity allow you to securely manage the credentials in your code for authenticating to various services without saving them locally on a developer workstation or in source control.|
|**Cluster and workload architectures:** Follow [Service Fabric best practices](/azure/service-fabric/service-fabric-best-practices-security#hosting-untrusted-applications-in-a-service-fabric-cluster) when hosting untrusted applications.|Following the best practices provides a security standard to follow.|

For more suggestions, see [Principles of the security pillar](/azure/well-architected/security/security-principles).

Azure Advisor helps you ensure and improve the security of Azure Service Fabric. You can review the recommendations in the [Azure Advisor section of this article](#azure-advisor-recommendations).

### Policy definitions

Azure Policy helps maintain organizational standards and assess compliance across your resources. Keep the following built-in policies in mind as you configure Azure Service Fabric:

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`. This is the default value for managed clusters and isn't changeable. **Standard cluster:** Ensure you set ClusterProtectionLevel to `EncryptAndSign`.
* Service Fabric clusters should only use Microsoft Entra ID for client authentication.

All built-in policy definitions related to Azure Service Fabric are listed in [Built-in policies - Service Fabric](/azure/governance/policy/samples/built-in-policies#service-fabric).

## Cost optimization

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and cost optimization.

When discussing cost optimization with Azure Service Fabric, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload resources are the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For cluster cost optimization, go to the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) and select **Azure Service Fabric** from the available products. You can test different configuration and payment plans in the calculator.

For more information about Azure Service Fabric workload pricing, check out the [example cost calculation process for application planning](/azure/service-fabric/service-fabric-capacity-planning#use-a-spreadsheet-for-cost-calculation).

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](../cost-optimization/principles.md) for optimizing the cost of your architecture.

> [!div class="checklist"]
> - **Cluster architecture:** Select appropriate VM SKU.
> - **Cluster architecture:** Use appropriate node type and size.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for cost:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Cluster architecture:** Avoid VM SKUs with temp disk offerings.|Service Fabric uses managed disks by default, so avoiding temp disk offerings ensures you don't pay for unneeded resources.|
|**Cluster architecture:** If you need to select a certain VM SKU for capacity reasons and it happens to offer temp disk, consider using [temporary disk support](/azure/service-fabric/how-to-managed-cluster-stateless-node-type#temporary-disk-support) for your stateless workloads.|Make the most of the resources you're paying for. Using a temporary disk instead of a managed disk can reduce costs for stateless workloads.|
|**Cluster and workload architectures:** Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|

For more suggestions, see [Principles of the cost optimization pillar](../cost-optimization/index.yml).

## Operational excellence

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and operational excellence.

When discussing security with Azure Service Fabric, it's important to distinguish between *cluster operation* and *workload operation*. Cluster operation is a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload operation is the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/well-architected/devops/principles) for operational excellence.

> [!div class="checklist"]
> - **Cluster architecture:** Prepare a [cluster monitoring solution](/azure/service-fabric/service-fabric-best-practices-monitoring#cluster-monitoring).
> - **Cluster architecture:** Review the [cluster health policies](/azure/service-fabric/service-fabric-health-introduction#health-policies) in the Service Fabric health model.
> - **Workload architecture:** Prepare an [application monitoring solution](/azure/service-fabric/service-fabric-best-practices-monitoring#application-monitoring).
> - **Workload architecture:** Review the [application and service type health policies](/azure/service-fabric/service-fabric-health-introduction#health-policies) in the Service Fabric health model.
> - **Cluster and workload architectures:** Prepare an [infrastructure monitoring solution](/azure/service-fabric/service-fabric-best-practices-monitoring#infrastructure-monitoring).
> - **Cluster and workload architectures:** Design your cluster with build and release pipelines for continuous integration and deployment.

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for operational excellence:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Workload architecture:** Use [Application Insights to monitor your workloads](/azure/service-fabric/service-fabric-best-practices-monitoring#application-monitoring).|Application Insights integrates with the Azure platform, including Service Fabric.|
|**Cluster and workload architectures:** Create a process for monitoring the expiration date of client certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|**Cluster and workload architectures:** For pre-production clusters use [Azure Chaos Studio](/azure/chaos-studio/chaos-studio-chaos-experiments) to drill service disruption on a Virtual Machine Scale Set instance failure.|Practicing service disruption scenarios will help you understand what is at-risk in your infrastructure and how to best mitigate the issues if they arise.|
|**Cluster and workload architectures:** Use [Azure Monitor to monitor cluster and container infrastructure events](/azure/service-fabric/service-fabric-best-practices-monitoring#cluster-monitoring).|Azure Monitor integrates well with the Azure platform, including Service Fabric.|
|**Cluster and workload architectures:** Use [Azure Pipelines for your continuous integration and deployment solution](/azure/service-fabric/service-fabric-tutorial-deploy-app-with-cicd-vsts).|Azure Pipelines integrates well with the Azure platform, including Service Fabric.|

For more suggestions, see [Principles of the operational excellence pillar](/azure/well-architected/devops/principles).

## Performance efficiency

The following section covers configuration recommendations, specific to Azure Service Fabric and performance efficiency.

When discussing security with Azure Service Fabric, it's important to distinguish between *cluster operation* and *workload operation*. Cluster performance is a shared responsibility between the Service Fabric cluster admin and their resource provider, while workload performance is the domain of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For more information about how Azure Service Fabric can reduce performance issues for your workload with Service Fabric performance counters, reference [Monitoring and diagnostic best practices for Azure Service Fabric](/azure/service-fabric/service-fabric-best-practices-monitoring).

### Design checklist

> [!div class="checklist"]
> - **Cluster architecture:** [Exclude the Service Fabric processes from Windows Defender](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) to improve performance.
> - **Cluster architecture:** Select appropriate VM SKU.
> - **Workload architecture:** Decide what [programming model](/azure/service-fabric/service-fabric-choose-framework) you will use for your services.
> - **Cluster and workload architectures:** Use appropriate managed disk tier and size.

### Recommendations

Consider the following recommendations to optimize your Azure Service Fabric configuration for performance efficiency:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Cluster architecture:** Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/).|
|**Cluster architecture:** Consider using [Autoscaling](/azure/service-fabric/how-to-managed-cluster-autoscale) for your cluster.|Autoscaling gives great elasticity and enables addition or reduction of nodes on demand on a secondary node type. This automated and elastic behavior reduces the management overhead and potential business impact by monitoring and optimizing the amount of nodes servicing your workload.|
|**Cluster architecture:** Consider using [Accelerated Networking](/azure/service-fabric/how-to-managed-cluster-networking#enable-accelerated-networking).|Accelerated networking enables a high-performance path that bypasses the host from the data path, which reduces latency, jitter, and CPU utilization for the most demanding network workloads.|
|**Cluster architecture:** Considering using [encryption at host](/azure/service-fabric/how-to-managed-cluster-enable-disk-encryption?tabs=azure-powershell#enable-encryption-at-host) instead of Azure Disk Encryption (ADE).|This encryption method improves on ADE by supporting all OS types and images, including custom images, for your VMs by encrypting data in the Azure Storage service.|
|**Workload architecture:** Review the [Service Fabric programming models](/azure/service-fabric/service-fabric-choose-framework) to decide what model would best suit your services.|Service Fabric supports several programming models. Each come with their own advantages and disadvantages. Knowing about the available programming models can help you make the best choices for designing your services.|
|**Workload architecture:** Leverage loosely-coupled [microservices](/azure/architecture/guide/architecture-styles/microservices) for your workloads where appropriate.|Using microservices allows you to get the most out of Service Fabric's features.|
|**Workload architecture:** Leverage [event-driven architecture](/azure/architecture/guide/architecture-styles/event-driven) for your workloads where appropriate.|Using event-driven architecture allows you to get the most out of Service Fabric's features.|
|**Workload architecture:** Leverage [background processing](/azure/architecture/guide/architecture-styles/web-queue-worker) for your workloads where appropriate.|Using background processing allows you to get the most out of Service Fabric's features.|
|**Cluster and workload architectures:** Review the [different ways you can scale your solution in Service Fabric](/azure/service-fabric/service-fabric-concepts-scalability).|You can use scaling to enable maximum resource utilization for your solution.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/well-architected/scalability/principles).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence when using Azure Service Fabric.

### Security

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`. This is the default value for managed clusters and isn't changeable. **Standard cluster:** Ensure you set ClusterProtectionLevel to `EncryptAndSign`.
* Service Fabric clusters should only use Microsoft Entra ID for client authentication.

## Additional resources

Check out the [Azure Service Fabric managed cluster configuration options article](/azure/service-fabric/how-to-managed-cluster-configuration) for a list of all the options you have while creating and maintaining your cluster.

Review the [Azure application architecture fundamentals](/azure/architecture/guide/) for guidance on how to develop your workloads. While Service Fabric can be used solely as a container hosting platform, using well-architected workloads leverages Service Fabric's full functionality.

## Next steps

Use these recommendations as you create your Service Fabric managed cluster using an ARM template or through the Azure portal:

* [Quickstart: Deploy a Service Fabric managed cluster with an Azure Resource Manager template](/azure/service-fabric/quickstart-managed-cluster-template)
* [Quickstart: Deploy a Service Fabric managed cluster using the Azure portal](/azure/service-fabric/quickstart-managed-cluster-portal)

---
title: Azure Well-Architected Framework (WAF) review for Azure Service Fabric
description: Design considerations and recommendations about Azure Service Fabric.
author: tomvcassidy
ms.author: tomcassidy
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.date: 3/18/2022
---

# Azure Well-Architected Framework review - Azure Service Fabric

[Azure Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers. These resources are deployed onto a network-connected set of virtual or physical machines, which is called a **cluster**.

There are two clusters models in Azure Service Fabric: **standard clusters** and **managed clusters**.

**Standard clusters** require you to define a cluster resource alongside a number of supporting resources. These resources must be set up correctly upon deployment and throughout the lifecycle of the cluster. Otherwise, the cluster and your services will not function properly.

**Managed clusters** are an evolution of the standard cluster resource model that simplifies your deployment and management operations. The managed cluster model consists of a single Service Fabric managed cluster resource that encapsulates and abstracts away the underlying resources.

This article primarily discusses the **managed cluster** model for simplicity. However, call-outs are made for any special considerations that apply to the **standard cluster** model.

<!-- Tom comment: new question, is this now too much context or is this good? I like the information Mike's suggestion gave, but guidance does recommend the intro be short, and if we're already assuming someone has selected SFMC, do we need to give even a brief explanation of SFMC? Personally, I like this, but I just suspect we'll get a note in review to shorten this -->

In this article, you learn architectural best practices for Azure Service Fabric. The guidance is based on the five pillars of architectural excellence:

* Reliability
* Security
* Cost optimization
* Operational excellence
* Performance efficiency

## Prerequisites

* Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. Check out the [Azure Well-Architected Framework overview page](../index.md) to review the five pillars of architectural excellence.

* Reviewing the [core concepts of Azure Service Fabric](/azure/service-fabric/service-fabric-content-roadmap) and [microservice architecture](/azure/architecture/reference-architectures/microservices/service-fabric) can help you understand the context of the best practices provided in this article.

## Reliability

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and reliability.

When discussing reliability with Azure Service Fabric, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability is the domain of a Service Fabric cluster admin, while workload reliability is the responsibility of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For more information about Azure Service Fabric cluster reliability, check out the [capacity planning documentation](/azure/service-fabric/service-fabric-best-practices-capacity-scaling#reliability-levels). <!-- Tom comment: There are probably lots of good links to hunt for the SF docs here. Is this a good pick? Should I put more? -->

For more information about Azure Service Fabric workload reliability, reference the [Reliability subsystem](/azure/service-fabric/service-fabric-architecture#reliability-subsystem) included in the Service Fabric architecture. <!-- Tom comment: Same as above. There are probably lots of good links to put here. Does this one still work? Should we include anything about Reliable Services: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-introduction ? -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

<!-- Tom comment: Add architecture responsibility call-outs -->

**Have you configured Azure Service Fabric with reliability in mind?**
***
> [!div class="checklist"]
> - When using secrets such as connection strings and passwords in Service Fabric services, either retrieve secrets directly from Key Vault at runtime or use the [Service Fabric Secrets Store](/azure/service-fabric/service-fabric-application-secret-store).
> - Use durability level Silver (5 VMs) or greater for production scenarios.
> - For critical workloads, consider using Availability Zones for your Service Fabric clusters.
> - To expose services on the Service Fabric cluster, use a reverse proxy such as the Service Fabric reverse proxy or Traefik. When exposing APIs hosted on the cluster, consider using Azure API Management.
> - For production scenarios, use the Standard tier load balancer. The Basic SKU is free, but doesn't have an SLA.
> - Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types. Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for managing the cluster.
> - Review the [Service Fabric production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).
> - When using the Service Fabric Secret Store to distribute secrets, use a separate data encipherment certificate to encrypt the values.
> - Don't use self-signed certificates for production scenarios. Either provision a certificate through your PKI or use a public certificate authority.
> - Deploy certificates by adding them to Azure Key Vault and referencing the URI in your deployment.
> - Create a process for monitoring the expiration date of certificates.
> - Enable Azure Active Directory integration for your cluster to ensure users can access Service Fabric Explorer using their AAD credentials. Don't distribute the cluster certificate among users to access Explorer.
> - Exclude the Service Fabric processes from Windows Defender to improve performance.
> - Keep the different node types and gateway services on different subnets.

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for service reliability:

<!-- Tom comment: Add architecture responsibility call-outs -->

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Consider using Azure API Management to expose APIs hosted on the cluster.|API Management can [integrate](/azure/service-fabric/service-fabric-api-management-overview) with Service Fabric directly.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) the Service Fabric executables from Defender scans.|

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

## Security

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and security.

When discussing security with Azure Service Fabric, it's important to distinguish between *cluster security* and *workload security*. Cluster reliability is the domain of a Service Fabric cluster admin, while workload reliability is the responsibility of a developer. Azure Service Fabric has considerations and recommendations for both of these roles.

In the **design checklist** and **list of recommendations** below, call-outs are made to indicate whether each choice is applicable to cluster architecture, workload architecture, or both.

For more information about Azure Service Fabric cluster security, check out [Service Fabric cluster security scenarios](/azure/service-fabric/service-fabric-cluster-security). <!-- Tom comment: There are probably lots of good links to hunt for the SF docs here. Is this a good pick? Should I put more? -->

For more information about Azure Service Fabric workload security, reference [Service Fabric application and service security](/azure/service-fabric/service-fabric-application-and-service-security). <!-- Tom comment: Same as above. There are probably lots of good links to put here -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/security/security-principles) for adding security to the architecture.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them. -->

**Have you configured Azure Service Fabric with security in mind?**
***
> [!div class="checklist"]
> - **Cluster architecture:** Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types. Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for managing the cluster.
> - **Cluster architecture:** Use Common Name (CN) certificates and avoid using self-signed certificates.
> - **Cluster architecture:** Use primary and secondary certificates to avoid lockouts.
> - **Cluster architecture:** When using the Service Fabric Secret Store to distribute secrets, use a separate data encipherment certificate to encrypt the values.
> - **Cluster architecture:** Deploy certificates by adding them to Azure Key Vault and referencing the URI in your deployment.
> - **Cluster architecture:** Enable Azure Active Directory integration for your cluster to ensure users can access Service Fabric Explorer using their AAD credentials. Don't distribute the cluster certificate among users to access Explorer.
> - **Cluster and workload architectures:** Create a process for monitoring the expiration date of certificates.
> - **Cluster and workload architectures:** Maintain separate clusters for development, staging, and production.

### Recommendations

Consider the following recommendations to optimize your Azure Service Fabric configuration for security:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|**Cluster architecture:** Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|**Cluster architecture:** Create certificate authority issued Service Fabric certificate.||
|**Cluster architecture:** Deploy Key Vault certificates to Service Fabric cluster virtual machine scale sets.||
|**Cluster architecture:** Apply an Access Control List (ACL) to your certificate for your Service Fabric cluster.||
|**Cluster architecture:** Secure your Service Fabric cluster certificates by Common Name.||
|**Workload architecture:** Encrypt Service Fabric package secret values.||
|**Workload architecture:** Include certificate in Service Fabric applications.||
|**Workload architecture:** Authenticate Service Fabric applications to Azure Resources using Managed Service Identity (MSI).||
|**Cluster and workload architecture:** Follow [Service Fabric best practices](/azure/service-fabric/service-fabric-best-practices-security#hosting-untrusted-applications-in-a-service-fabric-cluster) when hosting untrusted applications.||

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve the security of Azure Service Fabric. You can review the recommendations in the [Azure Advisor section of this article](#azure-advisor-recommendations).

### Policy definitions

Azure Policy helps maintain organizational standards and assess compliance across your resources. Keep the following built-in policies in mind as your configure Azure Service Fabric:

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`.
* Service Fabric clusters should only use Azure Active Directory for client authentication.

All built-in policy definitions related to Azure Service Fabric are listed in [Built-in policies - Service Fabric](/azure/governance/policy/samples/built-in-policies#service-fabric).

## Cost optimization

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and cost optimization.

When discussing cost optimization with Azure Service Fabric, it's important to distinguish between *cost of cluster resources* and *cost of workload resources*. Cluster resources are the domain of a Service Fabric cluster admin, while workload resources are the responsibility of a developer. Azure Service Fabric has considerations and recommendations for both of these roles. <!-- I tried to follow the pattern here for the sake of consistency, but I'm actually not sure this is true for cost optimization. Cost of resources is basically solely a cluster admin's job, right? Knowing workload requirements would be a developer's responsibility, but actually provisioning those resource and handling the costs wouldn't, correct? -->

<!-- Tom comment: should I add a sentence like: "In the design checklist below, call-outs will be made to indicate whether each choice is applicable to an admin, a developer, or both. (or maybe: ...applicable to cluster architecture, workload architecture, or both.)" ? Then, of course, I would follow-up by putting those callouts in the list :) -->

For more information about Azure Service Fabric pricing details, reference [Azure Service Fabric pricing](https://azure.microsoft.com/pricing/details/service-fabric). <!-- Is this a good link to point to? I didn't find any docs about pricing at a quick glance. -->

<!-- Tom comment: I don't think there are separate pricing docs to point to for cluster vs. workload, right? Similar to my comment above, this pillar seems like it's mostly a cluster admin's responsibility. No problem if I'm wrong. Just need the appropriate links! -->

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

**Have you configured Azure Service Fabric with cost optimization in mind?**
***
### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/cost/principles) for optimizing the cost of your architecture.

<!-- Tom comment: Happy to take suggestions here! :) I just put in some initial points based on Mike's input; I'll refine them as we go -->

> [!div class="checklist"]
> - Appropriate node type sizing
> - Appropriate VM SKU selection
> - Appropriate managed disk tier and size

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for cost:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|Avoid VM SKUs with temp disk offerings.|Service Fabric uses managed disks by default, so avoiding temp disk offerings ensures you don't pay for unneeded resources.|
|Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

## Operational excellence

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and operational excellence.

When discussing security with Azure Service Fabric, it's important to distinguish between *cluster operation* and *workload operation*. Cluster operation is the domain of a Service Fabric cluster admin, while workload operation is the responsibility of a developer. Azure Service Fabric has considerations and recommendations for both of these roles. <!-- Tom comment: should I add a sentence like: "In the design checklist below, call-outs will be made to indicate whether each choice is applicable to an admin, a developer, or both. (or maybe: ...applicable to cluster architecture, workload architecture, or both.)" ? Then, of course, I would follow-up by putting those callouts in the list :) -->

For more information about how Azure Service Fabric creates scalable and reliable production-ready workloads, reference the [Production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

**Have you configured Azure Service Fabric with operational excellence in mind?**
***
### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/devops/principles) for operational excellence.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

> [!div class="checklist"]
> - When using secrets such as connection strings and passwords in Service Fabric services, either retrieve secrets directly from Key Vault at runtime or use the [Service Fabric Secrets Store](/azure/service-fabric/service-fabric-application-secret-store).
> - Use durability level Silver (5 VMs) or greater for production scenarios.
> - For critical workloads, consider using Availability Zones for your Service Fabric clusters.
> - For production scenarios, use the Standard tier load balancer. The Basic SKU is free, but doesn't have an SLA.
> - Keep the different node types and gateway services on different subnets.
> - Review the [Service Fabric production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for operational excellence:

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/).|

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

**Have you configured Azure Service Fabric with performance efficiency in mind?**
***
## Performance efficiency

The following section covers configuration recommendations, specific to Azure Service Fabric, and performance efficiency.

For more information about how Azure Service Fabric can reduce performance issues for your workload with Service Fabric performance counters, reference [Monitoring and diagnostic best practices for Azure Service Fabric](/azure/service-fabric/service-fabric-best-practices-monitoring).

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

### Design checklist

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

<!-- Tom comment: We need at least two more items for the checklist. -->

> [!div class="checklist"]
> - Exclude the Service Fabric processes from Windows Defender to improve performance.
> - Placeholder (at least 3 required)
> - Placeholder (at least 3 required)

### Recommendations

Consider the following recommendation to optimize your Azure Service Fabric configuration for performance efficiency:

<!-- Tom comment: Technically we don't need to expand this, but I'm open to any recommendations on items to add here :) -->

<!-- Tom comment: reminder to change the singular phrasing of this when the table is expanded -->

|Azure Service Fabric Recommendation|Benefit|
|-----------------------------------|-----------|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can exclude the Service Fabric executables from Defender scans.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence when using Azure Service Fabric.

### Security

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`.
* Service Fabric clusters should only use Azure Active Directory for client authentication.

## Additional resources

### Reliability

For cluster reliability, review the [best practices for cluster planning](/azure/service-fabric/service-fabric-best-practices-capacity-scaling) and the [supporting how-to guide](/azure/service-fabric/service-fabric-cluster-capacity).

For workload reliability, consider using [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction) or [Reliable Actors](/azure/service-fabric/service-fabric-reliable-actors-introduction) in your workloads.

### Security

Review the [Azure Service Fabric security best practices](/azure/service-fabric/service-fabric-best-practices-security).

For cluster security, see some of the security-themed how-to guides, such as [Set up Azure Active Directory for client authentication](/azure/service-fabric/service-fabric-cluster-creation-setup-aad) and [Deploy a Service Fabric cluster that uses certificate common name instead of thumbprint](/azure/service-fabric/service-fabric-create-cluster-using-cert-cn).

For workload security, check out the catalog of application security how-to guides, like [Manage encrypted secrets in Service Fabric applications](/azure/service-fabric/service-fabric-application-secret-management).

### Cost Optimization

For cluster cost optimization, go to the Azure pricing calculator and select **Azure Service Fabric** from the available products. You can test different configuration and payment plans in the calculator.

For workload cost optimization, check out the [example cost calculation process for application planning](/azure/service-fabric/service-fabric-capacity-planning#use-a-spreadsheet-for-cost-calculation).

### Operational Excellence

For cluster operational excellence, 

For workload operational excellence,

### Performance Efficiency

For cluster performance efficiency, 

For workload performance efficiency,

## Next steps

Use these recommendations as you create your Service Fabric managed cluster using an ARM template or through the Azure portal:

* [Quickstart: Deploy a Service Fabric managed cluster with an Azure Resource Manager template](/azure/service-fabric/quickstart-managed-cluster-template)
* [Quickstart: Deploy a Service Fabric managed cluster using the Azure portal](/azure/service-fabric/quickstart-managed-cluster-portal)
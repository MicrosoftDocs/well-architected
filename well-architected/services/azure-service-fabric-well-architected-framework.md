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

**Managed clusters** are an evolution of the standard cluster resource model that simplifies your deployment and management operations. The managed clusters model consists of a single Service Fabric managed cluster resource that encapsulates and abstracts away the underlying resources.

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

When discussing reliability with Azure Service Fabric, it's important to distinguish between *cluster reliability* and *workload reliability*. Cluster reliability would be the domain of a Service Fabric cluster admin, while workload reliability would be responsibility of a developer. Azure Service Fabric has considerations and recommendations for both of these roles. <!-- Tom comment: should I add a sentence like: "In the design checklist below, call-outs will be made to indicate whether each choice is applicable to an admin, a developer, or both. (or maybe: ...applicable to cluster architecture, workload architecture, or both.)" ? Then, of course, I would follow-up by putting those callouts in the list :) -->

For more information about Azure Service Fabric cluster reliability, check out the [capacity planning documentation](/azure/service-fabric/service-fabric-best-practices-capacity-scaling#reliability-levels). <!-- Tom comment: There are probably lots of good links to hunt for the SF docs here. Is this a good pick? Should I put more? -->

For more information about Azure Service Fabric workload reliability, reference the [Reliability subsystem](/azure/service-fabric/service-fabric-architecture#reliability-subsystem) included in the Service Fabric architecture. <!-- Tom comment: Same as above. There are probably lots of good links to put here. Does this one still work? Should we include anything about Reliable Services: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-introduction ? -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

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

|Azure Service Fabric Recommendation|Description|
|-----------------------------------|-----------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Consider using Azure API Management to expose APIs hosted on the cluster.|API Management can [integrate](/azure/service-fabric/service-fabric-api-management-overview) with Service Fabric directly.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) the Service Fabric executables from Defender scans.|

For more suggestions, see [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

<!-- Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md). 

Tom comment: So if there are no Azure Advisor recommendations, do we simply remove this Azure Advisor callout? -->

<!-- ### Policy definitions

Tom comment: As per Chad's analysis, there are no Azure policies that apply to reliability; should this H3 be removed or should this H3 be left in with a callout that there are no policy considerations? -->

## Security

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and security.

For more information about how Azure Service Fabric creates and maintains a secure state, reference [Azure Service Fabric security](/azure/service-fabric/service-fabric-best-practices-security).

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/security/security-principles) for adding security to the architecture.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

<!-- Tom comment: We need at least two more items for the checklist. -->

> [!div class="checklist"]
> - Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types. Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for managing the cluster.
> - Placeholder (at least 3 required)
> - Placeholder (at least 3 required)

### Recommendations

Consider the following recommendation to optimize your Azure Service Fabric configuration for security:

<!-- Tom comment: Technically we don't need to expand this, but I'm open to any recommendations on items to add here :) -->

<!-- Tom comment: reminder to change the singular phrasing of this if the table is expanded -->

|Azure Service Fabric Recommendation|Description|
|-----------------------------------|-----------|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|

For more suggestions, see [Principles of the security pillar](/azure/architecture/framework/security/security-principles).

Azure Advisor helps you ensure and improve the security of Azure Service Fabric. Review the recommendations.

<!-- Tom comment: ask Chad where to find the list of Azure Advisor security recommendations for Service Fabric; write blurb about how they relate to the Policy definitions -->

### Policy definitions

Azure Policy helps maintain organizational standards and assess compliance across your resources. Keep the following built-in policies in mind as your configure Azure Service Fabric:

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`.
* Service Fabric clusters should only use Azure Active Directory for client authentication.

All built-in policy definitions related to Azure Service Fabric are listed in [Built-in policies - Service Fabric](/azure/governance/policy/samples/built-in-policies#service-fabric).

## Cost optimization

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and cost optimization.

For more information about Azure Service Fabric pricing details, reference [Azure Service Fabric pricing](https://azure.microsoft.com/pricing/details/service-fabric). <!-- Is this a good link to point to? I didn't find any docs about pricing at a quick glance. -->

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/cost/principles) for optimizing the cost of your architecture.

<!-- Tom comment: Happy to take suggestions here! :) I just put in some initial points based on Mike's input; I'll refine them as we go -->

> [!div class="checklist"]
> - Appropriate node type sizing
> - Appropriate VM SKU selection
> - Appropriate managed disk tier and size

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for cost:

|Azure Service Fabric Recommendation|Description|
|-----------------------------------|-----------|
|Avoid VM SKUs with temp disk offerings.|Service Fabric uses managed disks by default, so avoiding temp disk offerings ensures you don't pay for unneeded resources.|
|Align SKU selection and managed disk size with workload requirements.|Matching your selection to your workload demands ensures you don't pay for unneeded resources.|

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

<!-- Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md). 

Tom comment: So if there are no Azure Advisor recommendations, do we simply remove this Azure Advisor callout? -->

<!-- ### Policy definitions

Tom comment: As per Chad's analysis, there are no Azure policies that apply to cost optimization; should this H3 be removed or should this H3 be left in with a callout that there are no policy considerations? -->

## Operational excellence

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and operational excellence.

For more information about how Azure Service Fabric creates scalable and reliable production-ready workloads, reference the [Production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).

<!-- Tom comment: Expand this pillar intro with information on cluster and workload sides, like reliability -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/devops/principles) for operational excellence.

<!-- Tom comment: I'll be reviewing this checklist; definitely could use some input on them, as I did not write them -->

> [!div class="checklist"]
> - When using secrets such as connection strings and passwords in Service Fabric services, either retrieve secrets directly from Key Vault at runtime or use the [Service Fabric Secrets Store](/azure/service-fabric/service-fabric-application-secret-store).
> - Use durability level Silver (5 VMs) or greater for production scenarios.
> - For critical workloads, consider using Availability Zones for your Service Fabric clusters.
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

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for operational excellence:

|Azure Service Fabric Recommendation|Description|
|-----------------------------------|-----------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/).|

For more suggestions, see [Principles of the operational excellence pillar](/azure/architecture/framework/devops/principles).

<!-- Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md). 

Tom comment: So if there are no Azure Advisor recommendations, do we simply remove this Azure Advisor callout? -->

<!-- ### Policy definitions

Tom comment: As per Chad's analysis, there are no Azure policies that apply to cost optimization; should this H3 be removed or should this H3 be left in with a callout that there are no policy considerations? -->

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

|Azure Service Fabric Recommendation|Description|
|-----------------------------------|-----------|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can exclude the Service Fabric executables from Defender scans.|

For more suggestions, see [Principles of the performance efficiency pillar](/azure/architecture/framework/scalability/principles).

<!-- Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md). 

Tom comment: So if there are no Azure Advisor recommendations, do we simply remove this Azure Advisor callout? -->

<!-- ### Policy definitions

Tom comment: As per Chad's analysis, there are no Azure policies that apply to cost optimization; should this H3 be removed or should this H3 be left in with a callout that there are no policy considerations? -->

## Azure Advisor recommendations

[Azure Advisor](/azure/advisor/) is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Here are some recommendations that can help you improve the reliability, security, cost effectiveness, performance, and operational excellence when using Azure Service Fabric.

### Security

* Service Fabric clusters should have the ClusterProtectionLevel property set to `EncryptAndSign`.
* Service Fabric clusters should only use Azure Active Directory for client authentication.

## Additional resources

<!-- Tom comment: This is an optional section, but it's probably good we put some links to supporting material here. I'll hunt around, but any suggestions are welcome! :) -->

<!--
- \[\<link text>]\(\<link>)
- \[\<link-text>]\(\<link>)
-->

## Next steps

<!-- I put links to the managed cluster quickstarts for ARM and portal. I'm currently working on a learn module, so I can probably update this with that link later. Any other suggestions are welcome :) -->

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

Use these recommendations as you create your Service Fabric managed cluster using an ARM template or through the Azure portal:

* [Quickstart: Deploy a Service Fabric managed cluster with an Azure Resource Manager template](/azure/service-fabric/quickstart-managed-cluster-template)
* [Quickstart: Deploy a Service Fabric managed cluster using the Azure portal](/azure/service-fabric/quickstart-managed-cluster-portal)

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->
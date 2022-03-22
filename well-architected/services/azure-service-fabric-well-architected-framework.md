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

[Azure Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.

<!-- Tom comment: I'd like to put in a brief discussion of the difference between SFRP and SFMC here and directly state that SFMC will be primarily discussed with some special consideration callouts for SFRP. However, I'm not too familiar with how to explain the differences between the two. Any input would be greatly appreciated! -->

This article provides architectural best practices for Azure Service Fabric. The guidance is based on the five pillars of architectural excellence:

* Reliability
* Security
* Cost optimization
* Operational excellence
* Performance efficiency

<!-- Tom comment: Guidance suggests to keep this short; however, I'm not sure this intro covers enough of the "why would I want to use Azure Service Fabric?" customer question. Is that the job of a Well-Architected Framework? -->

<!-- Tom comment: Somewhere in the intro, I'd like to do an "In this article, you learn: (bullet point list)," but it's not required -->

## Prerequisites

Understanding the Well-Architected Framework pillars can help produce a high quality, stable, and efficient cloud architecture. Check out the [Azure Well-Architected Framework overview page](../../../index.md) to review the five pillars of architectural excellence.

<!-- Tom comment: I'd like to put something else here; the virtual machines example points to an introductory "run a Linux VM" article. The only thing in the architecture center for Service Fabric is this multiplayer example: https://review.docs.microsoft.com/en-us/gaming/azure/reference-architectures/multiplayer-synchronous-sf?toc=https%3A%2F%2Freview.docs.microsoft.com%2Fen-us%2Fazure%2Farchitecture%2Ftoc.json&bc=https%3A%2F%2Freview.docs.microsoft.com%2Fen-us%2Fazure%2Farchitecture%2Fbread%2Ftoc.json&branch=main> -->

## Reliability

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and reliability.

For more information about how Azure Service Fabric creates a highly available and reliable state, reference the [Reliability subsystem](/azure/service-fabric/service-fabric-architecture#reliability-subsystem) included in the [Service Fabric architecture](/azure/service-fabric/service-fabric-architecture).

<!-- Tom comment: Is this intro blurb sufficient? -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/resiliency/principles) for adding reliability to the architecture.

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

<!-- Tom comment: Is this intro blurb sufficient? -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/security/security-principles) for adding security to the architecture.

> [!div class="checklist"]
> - Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types. Ensure that the [correct ports](/azure/service-fabric/service-fabric-best-practices-networking#cluster-networking) are opened for managing the cluster.

### Recommendations

Consider the following recommendation to optimize your Azure Service Fabric configuration for security:

<!-- Tom comment: reminder to change the singular phrasing of this when the table is expanded -->

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

For more information about Azure Service Fabric pricing details, reference [Azure Service Fabric pricing](https://azure.microsoft.com/pricing/details/service-fabric).

<!-- Tom comment: Is this intro blurb sufficient? 

Is this a good link to point to? I didn't find any docs about pricing at a quick glance.
-->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/cost/principles) for optimizing the cost of your architecture.

<!-->
> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
-->

### Recommendations

Explore the following table of recommendations to optimize the cost your Azure Service Fabric configuration.

<!--
| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
-->

For more suggestions, see [Principles of the cost optimization pillar](/azure/architecture/framework/cost/overview).

<!-- Azure Advisor helps you ensure and improve \<pillar-specific text>. Review the [recommendations](../../contribute-how-to-write-waf-for-azure-offerings.md). 

Tom comment: So if there are no Azure Advisor recommendations, do we simply remove this Azure Advisor callout? -->

<!-- ### Policy definitions

Tom comment: As per Chad's analysis, there are no Azure policies that apply to cost optimization; should this H3 be removed or should this H3 be left in with a callout that there are no policy considerations? -->

## Operational excellence

The following sections cover design considerations and configuration recommendations, specific to Azure Service Fabric and operational excellence.

For more information about how Azure Service Fabric creates scalable and reliable production-ready workloads, reference the [Production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).

<!-- Tom comment: Is this intro blurb sufficient? -->

### Design checklist

As you make design choices for Azure Service Fabric, review the [design principles](/azure/architecture/framework/devops/principles) for operational excellence.

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

<!-- Tom comment: Is this intro blurb sufficient? -->

### Design checklist

> [!div class="checklist"]
> - Exclude the Service Fabric processes from Windows Defender to improve performance.

### Recommendations

Consider the following recommendation to optimize your Azure Service Fabric configuration for performance efficiency:

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

<!-- 6. Azure Advisor recommendations -----------------------------------------

    Required    

    Follow the Azure Advisor recommendations H2 heading with a sentence about how recommendations might help improve the configuration of the resource. 

-->

<!-- Tom comment: ask Chad where to find the list of Azure Advisor security recommendations for Service Fabric; write blurb about how they relate to the Policy definitions -->

## Additional resources

<!-- 7. Additional resources ----------------------------------------------------

    Optional

    The resources should be separated in sections. The H3 heading should indicate the purpose of the resource. 

-->

<!--
- \[\<link text>]\(\<link>)
- \[\<link-text>]\(\<link>)
-->

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

<!--
- \[\<link text>]\(\<link>)
-->

<!-- Tom comment: Just keeping this comment around for my own reference...

    Required    

    A design checklist is always the first H3 in each pillar section.

    Make sure the considerations you document:
    - Are presented in a checklist format.
    - Map to the design principles. Identify the appropriate principle, and then think about your recommendation in relation to it.
    - Focus on the salient features for that pillar as they relate to the product.
    - Provide full coverage of the design principles. (If not all design principles exist, cover no fewer than three design principles.)
-->

<!-- Tom comment: Clean up four old docs after main one completed -->

<!-- Remove all the comments in this template before you sign off or merge to the main branch. -->
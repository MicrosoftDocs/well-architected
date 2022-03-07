---
title: Azure Well-Architected Framework review - Azure Service Fabric
description: Describes the Azure Service Fabric offering in the context of the Azure Well-Architected Framework pillars.
author: v-stacywray 
ms.author: robbymillsap
ms.date: 03/07/2022
ms.topic: conceptual 
ms.service: architecture-center 
ms.subservice: well-architected 
---

# Azure Well-Architected Framework review - Azure Service Fabric

[Azure Service Fabric](/azure/service-fabric/service-fabric-overview) is a distributed systems platform that makes it easy to:

- Package
- Deploy
- Manage scalable and reliable microservices, and containers.

This article provides architectural best practices for the Azure Virtual Network based on the five pillars of architectural excellence: Reliability, Security, Cost optimization, Operational excellence, and Performance efficiency. If you're using Azure Service Fabric in your design, consider the design principles and recommendations described in this article.

## Prerequisites

- Understanding the Well-Architected Framework pillars can help produce a high-quality, stable, and efficient cloud architecture. We recommend that you review your workload by using the [Azure Well-Architected Framework Review](/assessments/?id=azure-architecture-review&mode=pre-assessment) assessment.
- Use a reference architecture to review the considerations based on the guidance provided in this article. We recommend that you start with [Microservices architecture on Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric).

## Reliability

As you make design choices for your virtual network, review the [design principles](../resiliency/principles.md) for adding reliability to the architecture.

### Design checklist

> [!div class="checklist"]
> - Azure Service Fabric doesn't provide its own SLA. The availability of Service Fabric clusters is based on the underlying Virtual Machine and storage resources used.
> - Virtual Machine Scale Sets don't have an SLA, since the SLA for Virtual Machines applies here. If the Virtual Machine Scale Set includes Virtual Machines in at least two fault domains, the availability of the underlying Virtual Machines SLA for two or more instances applies. If the scale set contains a single Virtual Machine, the availability for a Single Instance Virtual Machine applies.
> - For more SLA information, reference [Service Fabric SLA](https://azure.microsoft.com/support/legal/sla/service-fabric/v1_0/) and [Virtual Machine Scale Set SLA](https://azure.microsoft.com/support/legal/sla/virtual-machine-scale-sets/v1_1/).

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for service reliability:

|Recommendation|Benefit|
|--------------|-------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Consider using Azure API Management to expose APIs hosted on the cluster.|API Management can [integrate](/azure/service-fabric/service-fabric-api-management-overview) with Service Fabric directly.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/service-fabric-best-practices-security#windows-defender) the Service Fabric executables from Defender scans.|

For more suggestions, reference [Principles of the reliability pillar](/azure/architecture/framework/resiliency/principles).

Azure Advisor helps you ensure and improve the continuity of your business-critical applications. Review the following [recommendations](#azure-advisor-recommendations).

### Policy definitions

- Azure policy definition is *Service Fabric provides three levels of protection (None, Sign and EncryptAndSign) for node-to-node communication using a primary cluster certificate. Set the protection level to ensure that all node-to-node messages are encrypted and digitally signed.* 
- Audit usage of client authentication only via Azure Active Directory in Service Fabric.

All built-in policy definitions related to Azure Service Fabric are listed in [Built-in policies - Service Fabric](/azure/governance/policy/samples/built-in-policies#service-fabric).

## Security

To learn how Azure Service Fabric creates a secure and reliable state, reference [Azure Service Fabric security](/azure/service-fabric/service-fabric-best-practices-security).

To optimize security, review the [design principles](../security/security-principles.md)

### Design considerations

As you make design choices for Azure Service Fabric, review the following design considerations related to security:

<!---Add Security Design Considerations--->

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for security.

|Recommendation|Benefit|
|--------------|-------|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|

Azure Advisor helps you ensure and improve the security of your business-critical applications. Review the [recommendations](#azure-advisor-recommendations).

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

As you make design choices for Azure Service Fabric, review the [design principles](../cost/principles.md) for Cost optimization.

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

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for cost optimization.

| Recommendation | Benefit |
|--------|----|
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |
| \<Configuration recommendation> | What problem this recommendation will mitigate. |

Azure Advisor helps you ensure and improve cost reductions for your business-critical applications. Review the [recommendations](#azure-advisor-recommendations).

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

To learn how Azure Service Fabric creates scalable and reliable production-ready workloads, reference the [Production readiness checklist](/azure/service-fabric/service-fabric-production-readiness-checklist).

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

As you make design choices for Azure Service Fabric, review the [design principles](../devops/principles.md) for Operational excellence.

> [!div class="checklist"]
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>
> - \<Design consideration>

### Recommendations

Explore the following table of recommendations to optimize your Azure Service Fabric configuration for Operational excellence:

|Recommendation|Benefit|
|--------------|-------|
|Use durability level Silver (5 VMs) or greater for production scenarios.|This level ensures the Azure infrastructure communicates with the Service Fabric controller on scheduling reboots, and so on.|
|Consider using Availability Zones for your Service Fabric clusters.|Deploy a primary NodeType (and by extension a virtual machine scale set) to each AZ, which ensures the Service Fabric system services are spread across zones.|
|Apply Network Security Groups (NSG) to restrict traffic flow between subnets and node types.|For example, you may have an API Management instance (one subnet), a frontend subnet (exposing a website directly), and a backend subnet (accessible only to frontend), each implemented on a different virtual machine scale set.|
|Use a separate data encipherment certificate to encrypt the values, when using the Service Fabric Secret Store to distribute secrets.|This certificate is deployed to the virtual machine scale set nodes to decrypt the secret values. When using this approach, ensure that secrets are inserted and encrypted at release time. Using this approach means that changing the secrets requires a deployment. Make sure your key-rotation process is fully automated to follow this process without downtime.|
|Create a process for monitoring the expiration date of certificates.|For example, Key Vault offers a feature that sends an email when `x%` of the certificate's lifespan has elapsed.|
|Exclude the Service Fabric processes from Windows Defender to improve performance.|By default, Windows Defender antivirus is installed on Windows Server 2016 and 2019. To reduce any performance impact and resource consumption overhead incurred by Windows Defender, and if your security policies allow you to exclude processes and paths for open-source software, you can [exclude](/azure/service-fabric/).|

Azure Advisor helps you ensure and improve the operational excellence of your business-critical applications. Review the [recommendations](#azure-advisor-recommendations).

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
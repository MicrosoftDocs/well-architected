---
title: Azure VMware Solution workloads
description: See general guidance and resources for using Azure VMware Solution to run an on-premises VMware technology stack on Azure.
author: sblair01
ms.author: prwilk
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Azure VMware Solution workloads

This guidance is intended for **workload owners**, **technical stakeholders**, and **business stakeholders**. Specifically, this guidance is appropriate for people who play an integral role in harnessing, developing, and upholding applications throughout the Azure VMware Solution software-defined datacenter (SDDC) lifecycle. This documentation provides prescriptive guidance and best practices for key design areas that have a technical foundation in Azure Well-Architected Framework pillars. The recommendations center on Azure VMware Solution, a managed service for migrating on-premises VMware workloads to Azure.

You can use this workload documentation as your go-to resource for optimizing the lifecycle of particular applications in Azure VMware Solution.

## What is an Azure VMware Solution workload?

VMware is a leading provider of virtualization and cloud computing software and services. VMware collaborates with Microsoft Azure to offer Azure VMware Solution. This solution seamlessly integrates the VMware hypervisor with Azure dedicated BareMetal infrastructure. When you use this integration, you can take advantage of Azure native resources like virtual machines (VMs), storage disks, and network components. Azure VMware Solution also provides a way for workloads to migrate to Azure with minimal environmental modifications.

:::image type="content" source="./images/azure-vmware-solution-workload.png" alt-text="Architecture diagram that shows how Azure VMware Solution connects an on-premises system with VMware components and Azure services." border="false":::

When you use this unique hybrid environment, you can retain certain workloads on-premises. But you also have the flexibility of extending to the cloud, where you can benefit from the following advantages:

- Infrastructure that's suited for bursting scenarios
- Azure native integrations
- Disaster recovery locations

Azure VMware Solution offers a consistent VMware infrastructure within Azure. When you employ this service, your team can use familiar VMware processes, skills, and tools like VMware vSphere, VMware HCX, and VMware NSX-T Data Center. You can also streamline the management of your cloud-based workloads.

## The Well-Architected Framework approach

A well-architected workload is structured to meet specific performance, reliability, security, and cost optimization objectives. By following architectural principles and guidelines that are specific to Azure VMware Solution, you can enhance end-user experiences and deliver consistency and reliability. This guidance is tailored to address one Azure VMware Solution workload at a time. This approach optimizes specific applications in Azure VMware Solution, such as three-tier apps or virtual desktops.

:::image type="content" source="./images/well-architected-azure-vmware-solution-workload.png" alt-text="Architecture diagram that shows how Azure VMware Solution uses tiers and stretched networks." border="false":::

The Well-Architected Framework pillars also aim to involve modularity, separation of roles, and a way to improve operational productivity. This approach results in application workloads that avoid unnecessary complexities and unforeseen costs.

Consider making your Azure VMware Solution application well architected for the following reasons:

- **Reliability and availability**. Your application is more resilient to failure when you take advantage of fault tolerance mechanisms and redundancy measures.
- **Cost optimization**. Efficiently using Azure resources such as VMs helps you to effectively manage expenses without compromising performance.
- **Streamlined operations and simplified management**. You perform operations such as updates, troubleshooting, and monitoring more efficiently when you use streamlined processes.
- **Future-proofing and integration with Azure services**. When your application is well architected, it's well equipped to adapt to future changes and take advantage of new offerings in Azure and Azure VMware Solution.

If you don't apply these principles to your Azure VMware Solution application, you might encounter a range of operational, performance, and security challenges. These challenges can significantly impede your application's success and limit your organization's ability to take full advantage of Azure VMware Solution benefits.

Azure VMware Solution operates as a hybrid solution. As a result, an inadequately designed application can lead to intricate management challenges and complications in integrating with other Azure services. Poorly architected applications can suffer from many disadvantages:

- Performance degradation
- Inadequate scalability
- Heightened security vulnerabilities
- Escalated costs from inefficient resource utilization

These shortcomings can prevent your business from fully harnessing the opportunities that Azure VMware Solution and Azure offer. They can also hinder support for critical initiatives.

### Choose a migration approach

Common approaches for migrating or modernizing to the cloud are rehosting, refactoring, rearchitecting, and rebuilding. Each approach requires careful consideration.

The [modernization approach](https://azure.microsoft.com/resources/cloud-computing-dictionary/what-is-application-modernization), or updating current apps and data to a cloud-first model, can meet business needs at reduced costs. Consider modernizing based on the purpose of the application, life expectancy, supportability, cost, and service-level agreements (SLAs).

You can run multiple workloads in Azure VMware Solution. But it's important to assess whether there are Azure native offerings that are a better fit or more cost-effective than instantiating another Azure VMware Solution node.

For help with determining potential placement for migrated servers, see the [application assessment](/azure/azure-netapp-files/faq-integration) results.

## What are the key design areas?

Azure VMware Solution uses VMware and Microsoft propriety services. The following design areas focus on the technical decision points for infrastructure components that are part of a workload and their interactivity with the shared services.

|Design area|Summary|
|---|---|
|[Application platform](./application-platform.md)| Optimize the performance, security, and manageability of application platforms that run on Azure VMware Solution. Efficiently manage VM sizing, application deployment, orchestration, connectivity, and access controls.|
|[Infrastructure](./infrastructure.md)|Manage the underlying infrastructure of a workload. Examples include right-sizing VMs, designing for fault tolerance, defining traffic flow, and applying security best practices such as encryption and access controls.|
|[Monitoring](./monitoring.md)|Gain insights into application behavior by monitoring resource utilization, health, availability, and capacity expansion. Use visualization tools to track trends and anomalies.|
|[Networking](./networking.md)|Enable seamless connectivity and communication between resources. Efficiently allocate IP addresses to avoid conflicts. Isolate workloads for enhanced security and performance. Design for redundancy and high availability.|
|[Operations](./operations.md)|Ensure that day-to-day management and maintenance of your Azure VMware Solution environment remains secure, efficient, and reliable over time.|
|[Security](./security.md)|Implement security measures that help protect your workload from threats.|

## Example workloads

Many organizations run business applications, enterprise software, and legacy applications in on-premises VMware environments. When you use Azure VMware Solution to bring these applications to the cloud, you benefit from Azure global infrastructure and service offerings. Examples of cloud applications include traditional three-tier web-based applications and virtual desktops.

For more information, see the following reference architectures:

- [About Azure VMware Solution](/azure/azure-vmware/introduction)
- [Deploy Horizon on Azure VMware Solution](/azure/azure-vmware/azure-vmware-solution-horizon)

## Azure landing zones

In an enterprise setup, your workload shares platform resources that central teams manage. The design areas can provide points of integration with those shared resources. For a description of that integration, see [Integrate an Azure VMware Solution workload with Azure landing zones](landing-zone-integration.md).

Examples that demonstrate architectural best practices back up this guidance. Use the following implementations as a starting point for your workload:

- [Baseline architecture for Azure VMware Solution](/azure/cloud-adoption-framework/scenarios/azure-vmware/example-architectures). This article focuses on the networking design area. It includes various scenarios, such as handling internet ingress and egress traffic and handling traffic to an on-premises datacenter.
- [Azure VMware Solution landing zone accelerator](/azure/cloud-adoption-framework/scenarios/azure-vmware/enterprise-scale-landing-zone).

An open-source collection of Azure Resource Manager and Bicep templates is available. It represents the strategic design path and target technical state for an Azure VMware Solution deployment.

## Assessment

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)

## Next steps

Start by reviewing design principles.

> [!div class="nextstepaction"]
> [Design principles](./design-principles.md)

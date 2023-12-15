---
title: Azure Virtual Desktop workloads
description: See general guidance and resources for using Azure Virtual Desktop to run Windows desktops and applications on Azure from any device or location.
author: BenMartinBaur
ms.author: bebaur
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Azure Virtual Desktop workloads

This guidance is intended for **workload owners**, **technical stakeholders**, and **business stakeholders**. It's appropriate for people who play an integral role designing, building, and maintaining a solution for running applications and desktops in a cloud environment.

This documentation provides actionable and authoritative guidance for building and operating a highly reliable, scalable solution on Azure. The guidance has a technical foundation in Azure Well-Architected Framework best practices. It's also based on reviews of numerous customer deployments and migrations. As a result, this article addresses the challenges of designing Azure Virtual Desktop workloads on Azure.

You can use this workload documentation as your go-to resource for optimizing the way you operate applications and desktops in [Azure Virtual Desktop](/azure/virtual-desktop/overview).

## What is an Azure Virtual Desktop workload?

Azure Virtual Desktop is a cloud service for desktop and app virtualization. When you use this cloud virtual desktop infrastructure (VDI) service, you can run Windows desktops and applications on Azure. End users can access Windows desktops and applications from any device and location.

Besides providing a way for you to stream Windows from the cloud, Azure Virtual Desktop workloads also offer the following benefits:

- Cost reduction through pooled, multi-session resources.
- Individual ownership through personal, persistent desktops.
- Plan for growth and scale out by adding more resources based on your business needs. Your autoscaling strategy is backed by the ability to scale based on the percentage capacity or based on peak hours and off-peak hours, by running the minimum number of session host virtual machines (VMs).
- A unified experience for managing desktops and apps that are on various SKUs of the Windows operating system.

##### Hybird environment with Windows 11 Enterprise Multi-Session

Azure Virtual Desktop offers a hybrid solution for delivering Windows applications from an on-premises environment. By using [Azure Stack HCI (preview)](/azure/virtual-desktop/azure-stack-hci-overview), you can run Azure Virtual Desktop session hosts on-premises. When you run a [Windows 10 and Windows 11 Enterprise Multi-Session](/azure/virtual-desktop/windows-10-multisession-faq), you can manage your workloads anywhere.

## What are the common challenges?

VDI workloads often involve complex scenarios in customer environments. Many applications run on various on-premises and edge devices and operating system versions. Azure Virtual Desktop supports these scenarios.

Traditionally, VDI services require upfront capital expenditure for hardware and software licenses. They also involve ongoing operational costs for maintenance and support for brokering, web access, load balancer, or licensing services.

You can use Azure Virtual Desktop to manage disparate workload architectures. Azure Virtual Desktop helps you secure data and organizational resources. It also meets employee-specific needs by providing support to connect from various devices and form-factors.

The Azure Virtual Desktop control plane handles web access, gateway, broker, diagnostics, and extensibility components. You can configure these services by using the Azure portal, Azure Resource Manager, Bicep, or Terraform.

## What are the key design areas?

Most Azure Virtual Desktop customers classify VDI environments as business-critical workloads. These environments provide access to application resources that support common business goals and processes and that work together to deliver specific end-to-end functionality.

The workload must always be available, resilient to failures, and operational.

The Azure Virtual Desktop documentation includes articles about design areas that have a technical foundation in Well-Architected Framework pillars. The following table lists each design area and provides a summary of its corresponding article.

|Design area|Summary|
|---|---|
|[Application delivery](./application-delivery.md)| Discusses how to use a scale-unit architecture in the context of building a highly reliable application. Explores cloud application design patterns that support scaling and error handling. Makes recommendations about selecting, designing, and configuring an appropriate application hosting platform, application dependencies, frameworks, and libraries. |
|[Storage](./storage.md)| Examines data store technology choices by evaluating required aspects such as volume, velocity, variety, and veracity. |
|[Networking and connectivity](./networking.md)| Presents network topology concepts at an application level while considering requisite connectivity and redundant traffic management. Offers critical recommendations for designing a secure and scalable global network topology. |
|[Monitoring](./monitoring.md)| Offers processes for building a robust health model that quantifies application health states. Uses observability and operational constructs to achieve operational maturity. |
|[Business continuity](./business-continuity.md)| Recommends business continuity and disaster recovery strategies to help keep data safe and strengthen Azure Virtual Desktop deployments. |
|[Security and identity and access management (IAM)](./security.md)| Provides guidance to help you protect your application against threats that intend to directly or indirectly compromise its reliability. |
|[Operational procedures](./operations.md)| Discusses how you can adopt DevOps and related deployment methods to drive effective and consistent operational procedures. |

## Example workloads

Consider the following resources when you design your Azure Virtual Desktop workload on Azure:

- [Azure Virtual Desktop for the enterprise](/azure/architecture/example-scenario/wvd/windows-virtual-desktop). The Azure Virtual Desktop landing zone accelerator provides a design-oriented overview of an enterprise-scale landing zone for Azure Virtual Desktop. It provides a foundational overview for building an Azure Virtual Desktop environment that's highly scalable and accessible from any device. Azure Virtual Desktop is accessed over the public internet and doesn't require private network connectivity to a surrounding organizational technical estate.
- [Multiple forests with AD DS and Microsoft Entra ID](/azure/architecture/example-scenario/wvd/multi-forest). This article provides technical recommendations for using multiple Active Directory Domain Services (AD DS) forests in an Azure Virtual Desktop scenario.
- [Multi-region business continuity and disaster recovery for Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/azure-virtual-desktop-multi-region-bcdr). This article discusses business continuity and disaster recovery prerequisites, deployment steps, and best practices for multi-region deployments of Azure Virtual Desktop.

## Assessment

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)

## Next steps

Start by reviewing design principles.

> [!div class="nextstepaction"]
> [Design principles](./design-principles.md)

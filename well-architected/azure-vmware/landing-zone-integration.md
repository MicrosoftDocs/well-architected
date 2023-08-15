---
title: Integrate an Azure VMware Solution workload with Azure landing zones
description: Examine Azure landing zones and cloud operating models. See the responsibilities of platform teams and workload teams in Azure VMware Solution design areas.
author: Mahesh-MSFT
ms.author: prwilk
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Integrate an Azure VMware Solution workload with Azure landing zones

Each organization manages workloads and operates its cloud environment uniquely. The common cloud operating models are _decentralized_, _centralized_, _enterprise_, and _distributed_.

The most important difference between the various models is the level of ownership. In the decentralized model, workload owners have autonomy without any central IT oversight for governance. For example, they manage their own networking, monitoring, and identity requirements. At the other end of the spectrum is the centralized model, where the workload owners adhere to the governance requirements that central IT teams set.

For a detailed discussion of the models, see [Review and compare common cloud operating models](/azure/cloud-adoption-framework/operating-model/compare).

As a workload owner, you should understand the operating model that your organization uses. That choice influences the technical decisions that you're accountable for and the technical requirements that you drive to your central teams.

To make the most of Azure VMware Solution features and capabilities, you should take advantage of the best practices that apply to organizations. The platform provides adaptability and flexibility, which helps your Azure VMware Solution environment accommodate future growth.

## Azure landing zones

An Azure landing zone is a conceptual architecture that depicts the overall cloud footprint for an organization. It has multiple subscriptions, each with a unique purpose. Central teams own some of the subscriptions, such as _Azure platform landing zones_.

To familiarize yourself with the concept of Azure landing zones, see [What is an Azure landing zone?](/azure/cloud-adoption-framework/ready/landing-zone).

> [!IMPORTANT]
> Azure VMware Solution has specific considerations and requirements, especially ones that are related to integration with Azure services. The Azure VMware Solution landing zone accelerator and the Azure Well-Architected Framework guidance for Azure VMware Solution aim to highlight these necessary customizations. These resources also incorporate cloud-adoption framework perspectives for a holistic approach to cloud readiness.

:::image type="content" source="./images/azure-vmware-solution-landing-zone-architecture.png" alt-text="Architecture diagram that shows the landing zone integration of an Azure VMware Solution workload." border="false" lightbox="./images/azure-vmware-solution-landing-zone-architecture.png":::

### Platform landing zones

Sometimes an Azure VMware Solution private cloud is deployed before a workload gets migrated. Other times, the private cloud is deployed on a workload. In both cases, the private cloud needs to interact with multiple external services. Central teams might own some of these services as part of platform landing zones. Examples of these services include domain resolution, network connectivity, and security services. Interaction with these external services is a foundational concern. To be fully functional, workloads that are deployed on an Azure VMware Solution private cloud need the platform team and the workload team to share the same responsibility mindset.

For a demonstration of the platform landing zones that you need for an Azure VMware Solution workload to run, see [Azure landing zone review for Microsoft Azure VMware Solution](/azure/cloud-adoption-framework/scenarios/azure-vmware/ready). This article describes a solid platform foundation that accelerates migration from an on-premises VMware environment to an Azure VMware Solution private cloud.

### Application landing zones

There's a separate subscription, which is also known as an _Azure application landing zone_, that's intended for workload owners. This application landing zone is where you deploy your VMware workload. It has access to platform landing zones that provide the basic infrastructure that you need to run your workload. Examples include networking, identity access management, policy, and monitoring infrastructure.

Guidance about application landing zones applies to Azure VMware Solution workloads. For more information, see [Platform landing zones vs. application landing zones](/azure/cloud-adoption-framework/ready/landing-zone/#platform-landing-zones-vs-application-landing-zones). This guidance includes recommendations for efficiently governing and managing your workload.

For a demonstration of an application landing zone for an Azure VMware Solution workload, see the baseline reference architecture in [Example architectures for Azure VMware Solutions](/azure/cloud-adoption-framework/scenarios/azure-vmware/example-architectures). Initially, workload density and maturity are minimal inside an Azure VMware Solution private cloud. The density and maturity are expected to increase after the initial accelerator deployment. This guidance is applicable when the density and maturity of the private cloud start to increase.

## Design area integration

This section highlights the solid foundation that the platform provides. The discussion also covers the areas of shared responsibility between the platform team and the workload team.

### Platform responsibilities

The Azure VMware Solution platform team ensures that the infrastructure is ready for application teams to build. Some common tasks include:

- **Requesting capacity** by ensuring that the Azure VMware Solution software-defined datacenter (SDDC) activation has occurred and has defined the regions, nodes, and network settings. The platform team then allocates compute resources, resource pools, virtual storage area network (vSAN) storage, and clustering.
- **Designing to meet recovery point objective (RPO) and recovery time objective (RTO) targets** by strategically building infrastructure to meet service-level agreements (SLAs).
- **Securing and optimizing connectivity** to on-premises systems, Azure, and the internet. This task includes routing, setting up firewall entries, and managing centralized network appliances.
- **Managing Azure integrations**, such as integrations with Azure DNS, Azure Backup, Azure Monitor, Log Analytics, Azure Active Directory (Azure AD), and Azure Key Vault.

### Shared responsibilities

The workload team and the platform team have distinct responsibilities. But both teams often work closely together to help ensure workload availability and recoverability. The teams coordinate efforts for the overall success of the workloads that run in Azure VMware Solution. Effective collaboration between the platform and application teams is vital for successfully deploying cloud-based applications.

The design areas of the platform and application landing zones are tightly coupled.

- For a description of changes in platform resources that are needed for a workload, see [Azure landing zone review for Microsoft Azure VMware Solution](/azure/cloud-adoption-framework/scenarios/azure-vmware/ready).
- For a description of the technical specification of a workload, see [What are the key design areas?](../azure-vmware/overview.md#what-are-the-key-design-areas).

#### Design area - Infrastructure

**Backup and disaster recovery** is an area of infrastructure design that the application and platform teams both have roles in implementing.

- The Azure VMware Solution platform team sets up infrastructure-level backups and replication for virtual machines (VMs) and Azure VMware Solution components.
- The application team is responsible for application-level backups and data recovery procedures.

In some organizations, some operations are shared responsibilities. The following table lists examples:

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - **Infrastructure backups**. Implement backups for Azure VMware Solution components, VMs, and core infrastructure.<br>- **Configuration of VM image backups**. Quickly recover infrastructure from formats such as VM disks (VMDKs) during failures.<br>- **Disaster recovery planning**. Define mechanisms for site failover, data replication, and maintaining RPO and RTO targets for the Azure VMware Solution infrastructure. |- **Data backup**. Back up application-specific data and databases to a storage location by using an agent-based, VMware-compatible backup solution.<br>- **Application configurations**. Manage application configurations, settings, and libraries that the application needs to operate.<br>- **Task prioritization**. Discern important tasks from nonessential tasks.<br>- **Data restore and recovery**. Restore application data from backups periodically. Ensure that the application returns to a functional state in real-world scenarios. |

#### Design area - Networking

**DNS resolution** is a key concept in the networking design area.

The DNS configuration in Azure VMware Solution involves mapping host names to IP addresses. This mapping establishes connectivity between VMs and services within Azure VMware Solution and the broader network. The following table lists DNS responsibilities:

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Spin up VMs as domain controllers.<br>- Create private DNS zones.<br>- Manage domain names.<br>- Configure reverse DNS lookup. |- Configure host names.<br>- Manage application time to live (TTL) settings.<br>- Manage internal DNS resolution.<br>- Manage DNS monitoring and up-down alerts that affect the application. |

#### Design area - Operations

**Key management** is an important area in operations.

The application and platform teams both have responsibilities in key and password management. The roles that they play help ensure the security and access control of the applications that run in Azure VMware Solution. The following table lists the differences in the teams' responsibilities:

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - **Infrastructure key management**. Manage encryption key and infrastructure-level data such as encrypted disks and VM templates.<br>- **Infrastructure credentials**. Manage Azure VMware Solution administrative credentials for components like VMware vCenter Server and VMware ESXi hosts.<br>- **Platform access control**. Define user roles and permissions in the Azure VMware Solution environment. <br>- **Key Vault:** Create the Key Vault instance, configure policies to protect the vault, manage infrastructure and platform secrets, and manage encryption and decryption operations. | - Manage application-specific credentials and keys such as ones that are used for accessing APIs, databases, and secrets.<br>- Implement regular password rotation and credential expiry policies to prevent unauthorized access.<br>- Ensure that application credentials are stored securely and aren't hard-coded in application code or configuration files.<br>- Define access policies for key vaults that are specific to the application or services that require access to those secrets. |

## Next steps

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)

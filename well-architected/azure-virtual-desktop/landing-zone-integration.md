---
title: Integrate an Azure Virtual Desktop workload with Azure landing zones
description: Examine Azure landing zones and cloud operating models. See the responsibilities of platform teams and workload teams in Azure Virtual Desktop design areas.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Integrate an Azure Virtual Desktop workload with Azure landing zones

Migrating an organization's end-user desktops to the cloud is a common scenario in cloud migrations. Doing so helps improve employee productivity and accelerate the migration of various workloads to support the organization's user experience. Each organization manages workloads and operates its cloud environment uniquely. The common cloud operating models are *decentralized*, *centralized*, *enterprise*, and *distributed*.

The most important difference between the various models is the level of ownership. In the decentralized model, workload owners have autonomy without any central IT oversight for governance. For example, they manage their own networking, monitoring, and identity requirements. At the other end of the spectrum is the centralized model, where the workload owners adhere to the governance requirements that central IT teams set.

For a detailed discussion of the models, see [Review and compare common cloud operating models](/azure/cloud-adoption-framework/operating-model/compare).

As a workload owner, you should understand the operating model that your organization uses. That choice influences the technical decisions that you're accountable for and the technical requirements that you enforce for your central teams.

To make the most of Azure Virtual Desktop features and capabilities, you should take advantage of best practices that apply to organizations. The platform provides adaptability and flexibility, which helps your Virtual Desktop environment accommodate future growth.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Azure landing zones

An Azure landing zone is a conceptual architecture that depicts the overall cloud footprint for an organization. It has multiple subscriptions, each with a unique purpose. Central teams own some of the subscriptions, such as *Azure platform landing zones*.

To familiarize yourself with the concept of Azure landing zones, see [What is an Azure landing zone?](/azure/cloud-adoption-framework/ready/landing-zone/).

> [!IMPORTANT]
> Virtual Desktop has specific considerations and requirements, especially ones that are related to integrations with Azure services. The Virtual Desktop landing zone accelerator and the Azure Well-Architected Framework guidance for Virtual Desktop aim to highlight these necessary customizations. These resources also incorporate Cloud Adoption Framework perspectives for a holistic approach to cloud readiness.

### Platform landing zones

Virtual Desktop needs to interact with multiple external services. Central teams might own some of these services as part of platform landing zones. Examples of these services include identity services, network connectivity, and security services. Interaction with these external services is a foundational concern. To be fully functional, a Virtual Desktop workload needs the platform team and the workload team to share the same responsibility mindset.

For a demonstration of the platform landing zones that you need for a Virtual Desktop workload to run, see [Azure landing zone review for Microsoft Azure Virtual Desktop](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone). This article describes a solid platform foundation that accelerates migration from an on-premises environment to a Virtual Desktop private cloud.

### Application landing zones

There's a separate subscription, which is also known as an *Azure application landing zone*, that's intended for workload owners. This application landing zone is where you deploy your Virtual Desktop workload. It has access to platform landing zones that provide the basic infrastructure that you need to run your workload. Examples include networking, identity access management, policy, and monitoring infrastructure.

Guidance about application landing zones applies to Virtual Desktop workloads. For more information, see [Platform landing zones vs. application landing zones](/azure/cloud-adoption-framework/ready/landing-zone/#platform-landing-zones-vs-application-landing-zones). This guidance includes recommendations for efficiently governing and managing your workload.

For a demonstration of an application landing zone for a Virtual Desktop workload, see the baseline reference architecture in [Example architectures for Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop).

## Design area integration

This section highlights the solid foundation that the platform provides. The discussion also covers the areas of shared responsibility between the platform team and the workload team.

### Platform responsibilities

The Virtual Desktop platform team ensures that the infrastructure is ready for workload teams to build. Some common tasks include:

- **Managing capacity** by setting subscription and regional quotas that are sufficient for deployment.
- **Securing and optimizing connectivity** to on-premises systems, Azure, and the internet. This task includes routing, setting up firewall entries, and managing centralized network appliances.
- **Managing Azure integrations**, such as integrations with Azure Storage, Azure Monitor, Log Analytics, Microsoft Entra ID, and Azure Key Vault.

### Shared responsibilities

The workload team and the platform team have distinct responsibilities. But both teams often work closely together to ensure workload availability and recoverability. The teams coordinate efforts for the overall success of the workloads that run in Virtual Desktop. Effective collaboration between the platform and application teams is vital for successfully deploying Virtual Desktop.

The design areas of the platform and application landing zones are tightly coupled.

- For a description of changes in platform resources that are needed for a workload, see [Azure Virtual Desktop Azure landing zone review](/azure/cloud-adoption-framework/scenarios/wvd/ready).
- For a description of the technical specification of a workload, see [What are the key design areas?](./overview.md#what-are-the-key-design-areas).

#### Design area – Enterprise enrollment

The cloud platform team must understand existing enterprise enrollment or Microsoft Entra tenant decisions.

#### Design area – Identity and access management

Identity is critical to Virtual Desktop functionality, because users must be authenticated in order to use the service. The platform team is responsible for designing an identity solution, which might involve Microsoft Entra ID, Microsoft Entra Domain Services, or Active Directory Domain Services (AD DS). The platform team also ensures that the Virtual Desktop environment maintains a line of sight to identity services.

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Designing identity services for Microsoft Entra ID, Domain Services, AD DS, and Microsoft Entra Connect<br>- Using role-based access control (RBAC) to implement separation of duties<br>- Configuring Microsoft Entra Conditional Access policies<br>- Managing Active Directory organizational units and group policies | - Using RBAC assignments to control access to Virtual Desktop sessions and infrastructure for management purposes |

#### Design area - Networking

Platform services connectivity is a key networking concept. The platform team is responsible for ensuring that the Virtual Desktop environment has proper connectivity to:

- Identity services for authentication.
- The Domain Name System (DNS) for proper resolution.
- Other workloads in a hybrid environment.

Platform team responsibilities include:

- Configuring private endpoints and private DNS zones.
- Ensuring that bandwidth, latency, and quality of service considerations are addressed.
- Implementing network security policies.
- Ensuring access to required internet endpoints.
- Planning for business continuity and disaster recovery.

#### Design area – Resource organization

The platform team responsibilities include structuring management groups and resource groups to simplify access management. This responsibility includes defining naming and tagging standards. The workload team ensures compliance with these standards.

#### Design area – Management

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Planning for and developing a monitoring strategy<br>- Using Azure Policy to enforce enterprise-scale compliance<br>- Developing a cost management strategy | - Configuring the Virtual Desktop deployment for monitoring<br>- Managing user access<br>- Monitoring Virtual Desktop and collaborating with the platform team on monitoring needs<br>- Setting budgets and alerts<br>- Managing the user experience and support<br>- Ensuring compliance with platform and monitoring guidelines |

#### Design area – Business continuity and disaster recovery

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Designing a business continuity and disaster recovery strategy, including establishing recovery point objective (RPO) and recovery time objective (RTO) targets<br>- Coordinating with the workload team to ensure business continuity and disaster recovery alignment | - Configuring Virtual Desktop infrastructure and components to align with the business continuity and disaster recovery strategy<br>- Implementing disaster recovery procedures<br>- Training users on the appropriate use of Virtual Desktop |

#### Design area – Security and governance

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Understanding what the organization needs to comply with regulatory requirements like the Health Insurance Portability and Accountability Act (HIPAA), National Institute of Standards and Technology (NIST) standards, and Payment Card Industry (PCI) standards, and using Microsoft Defender for Cloud to apply compliance standards<br>- Ensuring that management plane resources are deployed in geographies in a way that meets data residency requirements<br>- Using Microsoft Entra Conditional Access policies and multifactor authentication to help secure user access<br>- Ensuring that a security information and event management (SIEM) tool like Microsoft Sentinel is used to collect and monitor user and administrator activity data<br>- Enabling threat and vulnerability management assessments, for instance, by integrating with Defender for Cloud or a third-party vulnerability management solution<br>- Configuring a firewall and using service tags and application security groups to define network access rules<br>- Creating a dedicated organizational unit in Active Directory for Virtual Desktop session hosts<br>- Using Azure Policy guest configurations to audit and harden session host operating systems<br>- Enabling disk encryption<br>- Monitoring network traffic and implementing distributed denial-of-service (DDoS) protection | - Using the least-privileged access principle and Azure RBAC to establish administrative, operations, and engineering roles<br>- Applying a dedicated group policy to Virtual Desktop organizational units<br>- Managing the patches and hardening of session hosts<br>- Monitoring and managing user activity |

#### Design area – Platform automation and DevOps considerations

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| - Developing infrastructure as code (IaC) and DevOps strategies | - Creating images<br>- Maintaining an image build pipeline<br>- Updating host pools<br>- Installing applications<br>- Managing language deployments |

#### Design area – Operational procedures

Operational procedures help ensure the security of applications that run in Virtual Desktop. Operational procedures also help in the area of access control.

| Platform team responsibilities | Workload team responsibilities |
| --- | --- |
| Controlling access to the platform by defining user roles and permissions in the Virtual Desktop environment | - Analyzing the performance and latency of Virtual Desktop deployments for insights about possible areas of improvement<br>- Updating the operating system, applications, and FSLogix<br>- Managing keys<br>- Managing FSLogix and analyzing data to determine when to make adjustments |

## Next steps

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
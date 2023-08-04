---
title: Azure VMware Solution design principles
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to Azure VMware Solution workloads.
author: Mahesh-MSFT
ms.author: maksh
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Azure VMware Solution design principles

Guidance about well-architected Azure VMware Solution workloads is built on the Azure Well-Architected Framework and its five pillars of architectural excellence. The following table lists each pillar and a summary of its goals.

| Well-Architected Framework pillar | Summary |
| --- | --- |
| Reliability | An Azure VMware Solution workload requires platform resiliency and high availability to protect critical business data. You can use the Well-Architected Framework to assess hybrid workloads that run in Azure VMware Solution and that have an application footprint that extends to Azure native services. |
| Security | This pillar is concerned with implementing measures that help protect your workload from threats. Examples include adding multiple security layers to your applications, including identity and access management (IAM), input validation, data sovereignty, and encryption for distributed denial-of-service (DDoS) mitigation. Other measures include blocking bad actors, preventing data exfiltration, and providing protection from operating system vulnerabilities. |
| Cost optimization | A well-architected application deployment meets performance expectations while reducing the total cost of ownership. |
| Performance efficiency | When your application demonstrates performance efficiency, it can optimize disk operations and has sufficient resources to scale with demand. It also minimizes network latency to make communication between the components of a distributed workload efficient. This pillar also includes load balancing, geographic placement, and caching mechanisms. |
| Operational excellence | This pillar focuses on making operations smooth and maximizing the value that you derive from VMware deployments. To achieve these goals, you combine automation, monitoring, the implementation of security measures, disaster recovery planning, performance optimization, and effective documentation. |

## Reliability

An optimal Azure VMware Solution workload exhibits resilience and availability.

- Resiliency refers to recovering from failures and maintaining functionality.
- Availability ensures uninterrupted uptime. High availability minimizes application downtime during critical maintenance activities. It also enhances recovery from incidents like virtual machine (VM) crashes, backend updates, extended downtimes, and ransomware attacks.

Because failures can occur on-premises and in the cloud, it's crucial that you design your Azure VMware Solution workload with a focus on resilience and availability.

**Conduct a reliability assessment.** Before you can standardize the reliability of an Azure VMware Solution workload, you need to assess its reliability. Then you can take steps to fix issues or solidify configurations.

To take an assessment, go to [Azure Well-Architected Review](/assessments/azure-architecture-review). Select **Start Assessment**, and then select **Azure VMware Solution**.

This assessment asks questions about your workload and provides specific recommendations to focus on. The assessment builds on itself, so you can save your progress between sessions without restarting.

## Security

In a shared-responsibility model:

- Organizations are primarily responsible for managing and operating workloads.
- Microsoft manages the physical and virtual infrastructure of Azure VMware Solution.

We strongly recommended that you regularly assess the services and technologies that you use to ensure that your security posture adapts to the evolving threat landscape. It's also essential to establish a clear understanding of the shared responsibility model when you collaborate with vendors to implement suitable security measures.

You can employ several methods to secure your Azure VMware Solution environment:

- Implement network isolation by using segments, virtual LANs, and network security groups for Azure native services.
- Manage patches effectively.
- Conduct environmental audits regularly.
- Monitor security with a security information and event management (SIEM) solution like Microsoft Sentinel.
- Implement encryption for data at rest and in transit.
- Use robust IAM practices:
  - Enforce multi-factor authentication.
  - Integrate your workload with Azure Active Directory (Azure AD).
  - Implement the principle of least privilege, and use role-based access control to assign roles.

## Cost optimization

Microsoft and VMware make significant investments in:

- The fast evolution of hardware.
- The VMware hypervisor.
- Azure native services.

These investments help provide more value for less. For example, they help suppress egress charges out of the software-defined datacenter (SDDC). They also incorporate enterprise licensing of several services into base costs.

The capabilities of Azure hardware expand frequently. As a result, opportunities regularly come up for Azure VMware Solution workloads to optimize costs, eliminate waste, and improve technologies such as purchasing reservations.

Consider creating a plan for each of your Azure VMware Solution applications and its integration services. Each plan should contain the objectives and motivations for the workload. Organizational objectives and investment priorities should drive cost optimization initiatives for your application, application platform, and data platform.

## Performance efficiency

This pillar focuses on optimizing workloads that run on Azure-dedicated hardware. That focus includes many aspects:

- Allocating resources, which includes the ability to adjust CPU, storage, memory, and network resources to meet application demands.
- Continuously monitoring performance and detecting anomalies by using tools such as VMware vRealize Operations Manager.
- Configuring specific settings at the application layer to improve overall performance, such as:
  - Query optimization at the data tier.
  - In-memory caching of your application.
  - The configuration of HTTP headers and the web tier.

Considering each of these aspects helps you build a well-architected application that delivers a consistent, cohesive user experience.

## Operational excellence

Operational excellence involves fully using the capabilities of Azure VMware Solution. Another part of operational excellence is adhering to well-architected best practices to secure, optimize, and scale workloads. This practice involves:

- Having processes in place for installing up-to-date patches and upgrades.
- Maintaining governance and compliance.
- Analyzing the performance and health of your environment.
- Following comprehensive processes.
- Maintaining documentation that captures:
  - Troubleshooting procedures.
  - Disaster recovery plans.
  - Remediation guidance on how to accelerate the process of resolving problems.

These steps help teams to collaborate in a way that's efficient and transparent.

## Next steps

The design principles that are incorporated into Azure VMware Solution guidance cover specific design domains. Each design domain offers focused guidance that helps you quickly access the information that you need for enhanced productivity in a minimal amount of time. Consider the headings as navigational tools that guide you in the right direction for networking, core infrastructure, the application platform, monitoring, security, and operational procedures.

Start by reviewing the design considerations for Azure infrastructure that are needed to support a workload.

> [!div class="nextstepaction"]
> [Infrastructure](vmware-infrastructure.md)
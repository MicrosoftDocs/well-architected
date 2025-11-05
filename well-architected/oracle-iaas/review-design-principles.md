---
title: Design principles for an Oracle workload on Azure infrastructure as a service (IaaS)
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: jhaessler
ms.date: 04/22/2024
ms.topic: concept-article
ms.service: azure-waf
ms.subservice: waf-workload-oracle
---

# Design principles for an Oracle workload on Azure

Guidance about well-architected Oracle on Azure infrastructure as a service (IaaS) workloads is built on the Azure Well-Architected Framework and its five pillars of architectural excellence. The following table lists each pillar and a summary of its goals.

| Well-Architected Framework pillar | Summary |
| --- | --- |
| Reliability | A reliable Oracle workload is both resilient and recoverable to maintain availability of the system. High availability reduces Oracle database and application downtime during critical maintenance. Recoverability addresses restoration after larger incidents. Design explicitly for each area.|
| Security | Security is about implementing measures that help protect your workload from threats. Examples include adding multiple security layers to your Oracle applications, including identity and access management (IAM), input validation, data sovereignty, encryption, and distributed denial-of-service (DDoS) mitigation. Other measures include blocking bad actors, preventing data exfiltration, and providing protection from operating system vulnerabilities. |
| Cost Optimization | Cost Optimization is about keeping costs at a minimum while achieving required technical and business objectives.|
| Performance Efficiency | Performance Efficiency is about accelerating digital transformation with less. The goal is to get the most out of your Oracle workload and meet user demand without overprovisioning or underprovisioning resources. Inefficient performance can degrade user experience and inflate costs. Performance affects productivity for both databases and applications.|
| Operational Excellence | Operational Excellence is about creating efficient processes to support and monitor your Oracle workloads. Health monitoring for Oracle databases and virtual machines (VMs) is crucial in order to guarantee the required performance of your Oracle workloads.|

## Reliability

Because failures can occur on-premises and in the cloud, it's important to focus on resilience and availability when you design an Oracle on Azure IaaS workload.

- *Resiliency* is the ability to withstand infrastructure faults, transient errors, and demand spikes while continuing to operate (possibly in a degraded mode) without user‑impacting downtime.
- *Availability* ensures uninterrupted uptime. High availability minimizes application and database downtime during critical maintenance activities. High availability also enhances recovery from incidents like VM crashes, back-end updates, extended downtimes, and ransomware attacks.

Failures can happen in the cloud. Instead of trying to prevent failures altogether, your goal should be to minimize the effects of a single failing component. Use the following information to minimize downtime and ensure that recommended practices for high availability are built into Azure and Oracle.

When discussing reliability with Oracle on Azure, you need to consider not only the database but also the connected tiers on separate VMs, virtual network subnets, and disaster recovery in case of failures. Consider the following recommendations for each design consideration:

- Scale workloads vertically by selecting a VM SKU and storage configuration that's appropriate for your workload's resources such as CPU, memory, input/output operations per second (IOPS), throughput, and database size. Scale horizontally by adding VM instances or disk storage.
- Design for high availability by implementing redundancy and failover mechanisms to help ensure continuous operations. Distribute traffic by considering strategies such as load balancing to distribute traffic across multiple back-end sources.
- Implement fault-tolerant storage to help ensure data integrity and availability by using techniques such as striping disks (RAID-0).
- Understand backup and restore capabilities for data recovery in case of accidental deletion, corruption, or other data-loss scenarios. A backup and restore procedure helps to streamline recovery processes.

## Security

In a shared-responsibility model:

- Organizations are primarily responsible for managing and operating workloads.
- Microsoft manages the physical and virtual infrastructure of the Azure infrastructure.

We strongly recommend that you regularly assess your services and technologies to ensure that your security posture adapts to the evolving threat landscape. It's also essential to establish a clear understanding of the shared responsibility model when you collaborate with vendors to implement suitable security measures.

You can employ several methods to secure your Oracle workloads:

- Implement the principle of least privilege, and use role-based access control (RBAC) to assign roles.
- Familiarize yourself with single sign-on (SSO) methods that are compatible with Oracle applications.
- Conduct environmental audits regularly.
- Implement encryption for data at rest and data in transit.
- Implement operating system hardening to prevent vulnerabilities that could be exploited to attack the Oracle database.
- Use network security groups to filter traffic.
- Integrate audit trails.

## Cost optimization

The first cost-optimization opportunity occurs during the right-size assessment. This assessment helps you avoid overprovisioning your infrastructure.  Frequent improvements in Azure hardware capabilities provide regular opportunities for Oracle workloads to optimize costs, eliminate waste, and improve technology.

To align your Oracle workload with Azure, we recommend creating a plan for each Oracle workload. The plan should contain the objectives and motivations for the workload. Organizational objectives and investment priorities should drive cost-optimization initiatives for your database, application platform, and data platform. Your recovery point objectives (RPOs), recovery time objectives (RTOs), and uptime requirements can lead to significant cost-optimization options.

## Performance efficiency

Performance efficiency is the ability of your workload to efficiently scale to meet the demands placed on it by users.

Oracle on Azure IaaS has components that are distributed across various Azure services. These components are the capacity-planning targets for your application servers and database-sizing choices. This includes load-balancing configurations for even traffic distribution and Oracle-native solutions, like Oracle Data Guard for fast-start failover database setups.

## Operational excellence

Operational excellence is about creating efficient processes to support your Oracle workload. Operations are the longest phase of the Oracle workload lifecycle, and teams must be equipped with operational best practices to manage day-to-day tasks. Operation failures affect the other design areas and the overall success of the Oracle workload. It’s critical to tailor your operational processes to support an Oracle workload in production. The following recommendations drive operational excellence:

- Have processes in place for installing up-to-date patches and upgrades.
- Maintain governance and compliance.
- Analyze your environment's performance and health in production.
- Maintain documentation that captures:
  - Troubleshooting procedures.
  - Disaster-recovery plans.
  - Remediation guidance on how to accelerate the process of resolving problems.

These recommendations can help your team collaborate in a way that's efficient and transparent.

## Next steps

The Well-Architected Framework design principles are incorporated into Oracle on Azure design areas. Each design area offers focused guidance that helps you quickly access the information that you need for enhanced productivity in a minimal amount of time.

Start by reviewing the design considerations for Azure infrastructure that are needed to support a workload.

> [!div class="nextstepaction"]
> [Choose compute and storage](choose-compute-storage.md)

> [!div class="nextstepaction"]
> [Optimize business continuity and disaster recovery](optimize-business-continuity-disaster-recovery.md)

> [!div class="nextstepaction"]
> [Optimize security](optimize-security.md)

> [!div class="nextstepaction"]
> [Monitor workloads](monitor-workloads.md)

> [!div class="nextstepaction"]
> [Design Oracle applications](design-applications.md)

---
title: Oracle on Azure IaaS design principles
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to Oracle on Azure IaaS workloads.
author: jessiehaessler
ms.author: 
ms.date: 
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-Oracle
---

# Oracle on Azure IaaS Design Principles

Guidance about well-architected Oracle on Azure IaaS workloads is built on the Azure Well-Architected Framework and its five pillars of architectural excellence. The following table lists each pillar and a summary of its goals.

| Well-Architected Framework pillar | Summary |
| --- | --- |
| Reliability | A reliable Oracle workload is both resilient and available. Resiliency is the ability to recover from failures and continue to function. Availability is uptime. High availability reduces Oracle Database and Application downtime during critical maintenance and improves recovery from failures. Failures happen on-premises and in the cloud, so it’s important to design your Oracle workload for resiliency and availability.|
| Security | This pillar is concerned with implementing measures that help protect your workload from threats. Examples include adding multiple security layers to your Oracle applications, including identity and access management (IAM), input validation, data sovereignty, encryption and distributed denial-of-service (DDoS) mitigation. Other measures include blocking bad actors, preventing data exfiltration, and providing protection from operating system vulnerabilities. |
| Cost optimization | Cost optimization is about keeping the cost at a minimum while achieving required technical and business objectives.|
| Performance efficiency | Performance efficiency is about accelerating digital transformation with less. The goal is to get the most out of your Oracle workload and meet user demand without over or under provisioning resources. Inefficient performance can degrade user experience and inflate costs. Performance affects productivity for databases and applications.|
| Operational excellence | Operational excellence is about creating efficient processes to support and monitor your Oracle workloads. Health monitoring for Oracle Databases and VMs is crucial in order to guarantee the required performance of your Oracle workloads.|

## Reliability

Because failures can occur on-premises and in the cloud, it's important to focus on resilience and availability when you design an Oracle on Azure IaaS workload.

- _Resiliency_ refers to recovering from failures and maintaining functionality.
  
- _Availability_ ensures uninterrupted uptime. High availability minimizes application and database downtime during critical maintenance activities. It also enhances recovery from incidents like virtual machine (VM) crashes, backend updates, extended downtimes and ransomware attacks.

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component. Use the following information to minimize down time and ensure that recommended practices for high availability are built into Azure and Oracle.
When discussing reliability with Oracle in Azure, it’s important to take into consideration not only the database but also the connected tiers on separate VMs, virtual network subnets and disaster recovery in the case of failures. Oracle on Azure IaaS can achieve these design considerations and have recommendations for each item.


- Scale workloads **vertically** by selecting a VM SKU and storage that's appropriate for your workload's resources such as CPU, memory, IOPS, throughput and Database size. Scale **horizontally** by adding VM instances or disk.
- **Design for high availability** by implementing redundancy and failover mechanisms to help ensure continuous operations. **Distribute traffic** by considering strategies such as load balancing to distribute traffic across multiple back-end sources.
- **Implement fault-tolerant** storage to help ensure data integrity and availability by using technologies such as striping disks (RAID-0).
- **Understand backup and restore capabilities** for data recovery in case of accidental deletion, corruption, or other data loss scenarios. A backup and restore procedures helps to streamline recovery processes.

## Security

In a shared-responsibility model:

- Organizations are primarily responsible for managing and operating workloads.
- Microsoft manages the physical and virtual infrastructure of the Azure infrastructure.

We strongly recommend that you regularly assess the services and technologies to ensure that your security posture adapts to the evolving threat landscape. Establishing a clear understanding of the shared responsibility model when you collaborate with vendors to implement suitable security measures is also essential.

You can employ several methods to secure your Oracle workloads:

- Implement the principle of least privilege, and use role-based access control to assign roles.
- Familiarize yourself with SSO methods supported for Oracle applications.
- Conduct environmental audits regularly.
- Implement encryption for data at rest and in transit.
- Implement operating system hardening to prevent vulnerabilities that could be exploited to attack the Oracle database.
- Use Network Security Groups to filter traffic.
- Integrate audit trails.

## Cost optimization

The first cost optimization opportunity is set during the right-size assessment. This supports you to not over-provision your infrastructure.  The frequent increase in Azure hardware capability provides regular opportunity for an Oracle workloads to optimize costs, eliminate waste, and improve technology. To align Azure and your Oracle workload, we recommend creating a plan for each Oracle workload. The plan should contain the objectives and motivations for the workload. Organizational objectives and investment priorities should drive cost optimization initiatives for your database, application platform, and data platform. Your RTO/RPO and uptime requirements can lead to significant cost optimization options.

## Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. 

Oracle on Azure IaaS has components that are distributed across various Azure services. These components are the capacity planning targets for your application servers and database sizing choices. This includes load balancing configurations for even traffic distribution and Oracle-native solutions, like Oracle Data Guard for fast-start failover database setups.

## Operational excellence

Operational excellence is about creating efficient processes to support your Oracle workload. Operations will be the longest phase of the Oracle workload lifecycle, and teams must be equipped with operational best practices to manage the day-today tasks. Failure in operations will affect the other design areas and the overall success of the Oracle workload. It’s critical to tailor your operational processes to support an Oracle workload in production. Below are recommendations to drive operational excellence.

- Having processes in place for installing up-to-date patches and upgrades.
- Maintaining governance and compliance.
- Analyzing the performance and health of your environment also in production.
- Maintaining documentation that captures:
  - Troubleshooting procedures.
  - Disaster recovery plans.
  - Remediation guidance on how to accelerate the process of resolving problems.

These steps help teams to collaborate in a way that's efficient and transparent.

## Next steps

The described design principles are incorporated into Oracle on Azure Design Areas. Each design area offers focused guidance that helps you quickly access the information that you need for enhanced productivity in a minimal amount of time. 

Start by reviewing the design considerations for Azure infrastructure that are needed to support a workload.

## Next steps
> [!div class="nextstepaction"]
> [Compute and Storage](./compute-and-storage.md)

> [!div class="nextstepaction"]
> [Backup and Disaster Recovery](./bcdr.md)

> [!div class="nextstepaction"]
> [Security](./security-new.md)

> [!div class="nextstepaction"]
> [Monitoring](./monitoring.md)

> [!div class="nextstepaction"]
> [Oracle Applications](./oracle-applications.md)


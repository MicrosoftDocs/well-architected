---
title: Azure Virtual Desktop design principles
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to Azure Virtual Desktop workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 09/15/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Azure Virtual Desktop design principles

Guidance about Azure Virtual Desktop workloads is built on the Azure Well-Architected Framework and its five pillars of architectural excellence. The following table lists each pillar and a summary of its goals.

| Well-Architected Framework pillar | Summary |
| --- | --- |
| Reliability | A virtual desktop environment demands a high level of service reliability to help ensure a consistent and uninterrupted user experience. It's essential to establish a robust infrastructure that enables reliable access and optimal performance of the virtual desktop environment. Evaluate virtual desktop deployments, particularly those that integrate with Azure native services. This approach helps ensure a seamless and dependable user experience. |
| Security | This aspect focuses on the implementation of strategies for safeguarding your workload against potential threats. These strategies incorporate a range of security layers into your applications. These measures encompass critical elements such as robust identity and access management (IAM) and strict data sovereignty adherence. Another critical element is comprehensive encryption to counter distributed denial-of-service (DDoS) attacks. Other crucial components that help ensure the security of the environment include proactive steps. Examples include thwarting malicious entities, preempting data exfiltration, and furnishing defense against operating system vulnerabilities. |
| Cost Optimization | When you optimize your virtual desktop environment for cost, you meet performance expectations and reduce the total cost of ownership. Optimizing costs for Virtual Desktop involves several steps. Examples include choosing the right size virtual machines (VMs), using reserved instances or savings plans, setting up scaling plans, and using Microsoft Cost Management to monitor and optimize spending. |
| Performance Efficiency | Performance Efficiency focuses on the ability of your environment to change to meet required performance in the cloud. In your virtual desktop environment, you can meet your required performance with ideal efficiency by using a scaling plan. |
| Operational Excellence | Apply the principles of DevOps to achieve superior operational practices by standardizing deployments, adding monitoring, and applying automation where applicable. The deployments must be consistent and stable and must follow a repeatable structure. |

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-a-virtual-desktop-workload).

## Reliability

Reliability is a foundational pillar in a Virtual Desktop environment. Because disruptions can materialize on-premises and in the cloud, you need to pay meticulous attention to the virtual desktop setup. Make it a priority to keep user sessions and profiles continuously available.

- *Resiliency* refers to recovering from failures and maintaining functionality.
- *Availability* ensures uninterrupted uptime. High availability minimizes application downtime during critical maintenance activities. It also enhances recovery from incidents like VM crashes, back-end updates, extended downtimes, and ransomware attacks.

Achieving reliability requires a comprehensive approach that spans architecture, operational procedures, automation, monitoring, regular testing, and validation.

- **Define service-level agreements (SLAs)**. Establishing well-defined SLAs is central to fostering reliability. These agreements specify precise expectations for the availability and performance of user sessions and profiles. By doing so, they lay down a clear benchmark that you can use to assess the operational effectiveness of your system.
- **Develop effective scaling strategies**. With vertical scaling, you opt for a VM SKU that aligns with the resource needs of user sessions. You also factor in aspects like CPU and memory requirements. With horizontal scaling, you add extra VM instances to accommodate escalating demands while sustaining system stability.
- **Design for high availability**. This process entails integrating redundancy and failover mechanisms that help ensure uninterrupted operations. High availability minimizes potential disruptions and guarantees users a seamless experience even during unexpected events.
- **Implement fault tolerance**. The inclusion of fault-tolerant storage plays a critical role in safeguarding the integrity and availability of roaming profiles and user data. This strategy offers a layer of protection against data loss by helping ensure that essential user data remains intact and accessible during failures.
- **Understand backup and restore capabilities**. Robust backup practices help ensure that you maintain operational continuity in the face of adversity.

## Security

In a shared-responsibility model:

- Organizations are primarily responsible for managing and operating Virtual Desktop workloads.
- Microsoft manages the control plane that supports a Virtual Desktop workload.

We strongly recommended that you regularly assess your services and technologies to help ensure that your security posture adapts to evolving threat landscapes. When you collaborate with vendors to implement suitable security measures, it's essential to establish a clear understanding of the shared-responsibility model.

You can use several methods to help secure your virtual desktop environment:

- **Network isolation**. Use network isolation techniques such as subnets and network security groups to fortify interactions between Azure native services. This compartmentalization enhances overall security.
- **Patch management**. Regularly apply patches and updates to bolster defense against vulnerabilities that can be exploited.
- **Environmental audits**. Regular audits of your environment provide insight into potential security gaps. This practice facilitates early identification and rectification of vulnerabilities.
- **Security information and event management (SIEM)**. Use a SIEM solution like Microsoft Sentinel. This tool offers real-time monitoring of security events, which helps you respond promptly to potential threats.
- **Data encryption**. Implement encryption mechanisms for data at rest and data in transit. This practice helps ensure data confidentiality and integrity, even during unauthorized access attempts.
- **IAM**.
  - Multifactor authentication: Strengthen the identity verification process by enforcing multifactor authentication. This practice helps deter unauthorized access attempts.
  - Integration with Microsoft Entra ID: Delegate your identity and access management to Microsoft Entra ID for streamlined access control.
  - Principle of least privilege: Minimize the potential for misuse by applying the principle of least privilege. Use this principle to restrict access to resources according to roles that focus on just-enough access (JEA) and just-in-time access (JIT) methods.
  - Role-based access control (RBAC): Promote controlled access by using Azure RBAC to assign permissions that are based on predefined roles.

## Cost Optimization

Cost-effective workloads that align with business goals and provide a positive return on investment (ROI) are essential for staying within budget. The principles of Cost Optimization are critical considerations that can help you achieve business objectives and cost justification for Virtual Desktop.

Some key considerations and methods for optimizing Virtual Desktop costs include:

- **Right-sizing VMs**. With Virtual Desktop, you can choose the size of the VMs that host your desktops and applications. Choosing the right size helps ensure that you don't pay for more resources than you need.
- **Reserved instances or savings plans**. Azure offers reserved instances and savings plans. When you use these plans, you save money by committing to a one-year or three-year term for your VMs. This strategy provides predictable pricing and can help you reduce costs over time.
- **The autoscale feature and scaling plans**. With Virtual Desktop, you can set up the autoscale feature with scaling plans for your VMs. This strategy reduces costs by helping to ensure that the correct number of session hosts are running when needed.
- **Cost Management**. When you use Cost Management, you can monitor and optimize your Azure spending. You can use this tool to identify areas where you can reduce costs and optimize your Virtual Desktop deployment.

Keep in mind that the capabilities of Azure hardware expand frequently. As a result, opportunities regularly come up for workloads to further optimize costs, eliminate waste, and improve performance-to-cost ratios. Organizations find it beneficial to revisit and tweak their cost-saving measures periodically.

## Performance Efficiency

This pillar focuses on optimizing workloads that run your Virtual Desktop environment. That focus includes many aspects:

- Allocating the proper size, family, and number of session hosts  
- Continuously monitoring performance and detecting anomalies by using tools such as Azure Monitor, Log Analytics, Application Insights, and alerts
- Configuring a proper scaling plan so that you have an ideal number of session hosts available for users

Considering each of these aspects helps you build a Virtual Desktop environment that delivers a consistent, cohesive user experience.

## Operational Excellence

In Virtual Desktop, Operational Excellence means ensuring the consistency, repeatability, and stability of your operational procedures in the development, deployment, and operating phases.

- Using infrastructure as code (IaC) for repeatable, consistent deployment.
- Proper monitoring of resources to maintain the health of your environment.
- An adequate continuous integration/continuous delivery (CI/CD) pipeline that helps you achieve:
  - Fast disaster recovery.
  - Preproduction testing.
  - Replication of resources.
- Maintaining governance and compliance standards for your host pool.
- Documentation that covers all the points in this list.

These steps help teams collaborate in a way that's efficient and transparent.

## Next steps

The design principles are incorporated into specific technical design areas. Each area offers focused guidance that helps you quickly access the information that you need for enhanced productivity in a minimal amount of time. Consider the headings as navigational tools that guide you in the right direction for networking, core infrastructure, the application delivery, monitoring, security, and operational procedures.

Start by reviewing the design considerations for application delivery that are needed to support a workload.

> [!div class="nextstepaction"]
> [Application delivery](application-delivery.md)

---
title: Azure Virtual Desktop design principles
description: Review the design principles of the Azure Well-Architected Framework. See how to apply these principles to Azure Virtual Desktop workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/12/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avd
---

# Azure Virtual Desktop design principles

Guidance about Azure Virtual Desktop workloads is built on the Azure Well-Architected Framework and its five pillars of architectural excellence. The following table lists each pillar and a summary of its goals.

| Well-Architected Framework pillar | Summary |
| --- | --- |
| Reliability | A virtual desktop environment demands a high level of service reliability to help ensure a consistent and uninterrupted user experience. It's essential to establish a robust infrastructure that enables reliable access and optimal performance of the virtual desktop environment. Evaluate virtual desktop deployments, particularly those that integrate with Azure native services. Specifically, evaluate the native compute, networking, and storage services that your deployment uses. Also evaluate third-party products and partner solutions that you use with Azure Virtual Desktop. These components affect reliability, because they're part of the landing zone design. This approach helps ensure a seamless and dependable user experience. |
| Security | The security pillar focuses on the implementation of strategies for safeguarding your workload against threats and vulnerabilities. Security also includes considerations for insider risk and data loss protection. Within Azure Virtual Desktop, it's critical to assess your end-to-end security topics. These topics range from identity and access management (IAM), operating systems, applications, networking, and Azure platforms to data. We highly encourage you to review or develop strategies to incorporate a range of security layers across your Azure estate. These strategies should cover your desktops and the applications that are presented under Azure Virtual Desktop. |
| Cost Optimization | When you optimize your virtual desktop environment for cost, you meet performance expectations that are set by business requirements. You also reduce the total cost of ownership (TCO) by minimizing overspending. You can reduce the TCO by taking advantage of the power of the Azure Virtual Desktop hyperscale infrastructure. Optimizing costs for Azure Virtual Desktop involves several steps. Examples include choosing the right size virtual machines (VMs), using reserved instances or savings plans, setting up scaling plans, and using Microsoft Cost Management to monitor and optimize spending. It's important to continually evaluate your Azure Virtual Desktop deployments to track your usage and identify opportunities for optimizing your workloads. |
| Performance Efficiency | Performance Efficiency focuses on the ability of your environment to meet or exceed the business requirements that are set for your virtual desktop and applications. In your Azure Virtual Desktop environment, you can meet your required performance with ideal efficiency by ensuring that you select optimal compute families and disk sizes and types. If you use FSLogix profiles, you also need to select optimal storage. In general, it's critical to assess and test a series of VM configurations to understand how your users and workloads use the Azure resources that make up the session hosts within Azure Virtual Desktop. After deployment, we recommend that you continually monitor consumption of your production resources to ensure that you meet or exceed your desired target performance. |
| Operational Excellence | Apply the principles of DevOps within your Azure virtual desktop workload team. Encourage the adoption of a DevOps mindset, developing standards, and the approach of executing while evolving. With Azure Virtual Desktop, you can establish set procedures for deploying, managing, and maintaining your workloads with ease. These procedures can involve the use of Azure Resource Manager templates, Bicep, Terraform, and other forms of infrastructure as code (IaC). To customize session hosts, you can automate your image build by using tools like the [custom image template feature](/azure/virtual-desktop/create-custom-image-templates) with [Azure DevOps](/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops&preserve-view=true). These DevOps strategies and others help your organization establish, maintain, and manage your Azure Virtual Desktop environments efficiently. |

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected Framework Azure Virtual Desktop workload](./index.yml) series. If you aren't familiar with this series, we recommend that you start with [What is an Azure Virtual Desktop workload?](./overview.md#what-is-an-azure-virtual-desktop-workload).

## Reliability

Reliability is a foundational pillar in an Azure Virtual Desktop environment. Disruptions can materialize on-premises and in the cloud. As a result, you need to pay meticulous attention to your virtual desktop components, which include Azure services and your on-premises infrastructure. Assess your workload's user data (profiles), applications, and compute availability requirements to determine your strategy for business continuity and disaster recovery. Take into account the different levels of availability that you achieve in Azure Virtual Desktop with different deployment options, such as active-active or active-passive host pools. Also assess your disaster recovery design considerations to help ensure smooth failovers during outages.

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

- Organizations are primarily responsible for managing and operating Azure Virtual Desktop workloads.
- Microsoft manages the control plane that supports an Azure Virtual Desktop workload.

We strongly recommended that you regularly assess your services and technologies to help ensure that your security posture adapts to evolving threat landscapes. When you collaborate with vendors to implement suitable security measures, it's essential to establish a clear understanding of the shared-responsibility model.

You can use several methods to help secure your virtual desktop environment:

- **Network isolation**. Use network isolation techniques such as subnets and network security groups to fortify interactions between Azure native services. This compartmentalization enhances overall security.
- **Patch management**. Regularly apply patches and updates to bolster defense against vulnerabilities that can be exploited.
- **Environmental audits**. Regular audits of your environment provide insight into potential security gaps. This practice facilitates early identification and rectification of vulnerabilities.
- **Security information and event management (SIEM)**. Use a SIEM solution like Microsoft Sentinel. This tool offers real-time monitoring of security events, which helps you respond promptly to potential threats.
- **Data encryption**. Implement encryption mechanisms for data at rest and data in transit. This practice helps ensure data confidentiality and integrity, even during unauthorized access attempts.
- **Identity and access management**.
  - Multifactor authentication: Strengthen the identity verification process by enforcing multifactor authentication. This practice helps deter unauthorized access attempts.
  - Integration with Microsoft Entra ID: Delegate your identity and access management to Microsoft Entra ID for streamlined access control.
  - Principle of least privilege: Minimize the potential for misuse by applying the principle of least privilege. Use this principle to restrict access to resources according to roles that focus on just-enough access (JEA) and just-in-time access (JIT) methods.
  - Role-based access control (RBAC): Promote controlled access by using Azure RBAC to assign permissions that are based on predefined roles.

## Cost Optimization

Azure Virtual Desktop is a cost-effective modernization of Remote Desktop Services (RDS). Certain components are removed from your infrastructure and provided as native services to all Azure regions. Azure Virtual Desktop reduces the requirements for Windows Server and RDS client access licenses (CALs) by offering Windows 10 or Windows 11 Enterprise multi-session remote desktops. In these desktops, the use of existing per-user Microsoft 365 licensing is supported. This support benefits workloads that only have applications that are supported on modern operating systems.

Some key considerations and methods for optimizing Azure Virtual Desktop costs include:

- **Using cost-effective VMs**. Choosing the right size VM helps ensure that you don't pay for more resources than you need. Determine which VM series best fits your workload's consumption of VM resources. Find an appropriate balance between performance and cost effectiveness.

- **Reserved instances or savings plans**. Azure offers reserved instances and savings plans. When you use these plans, you save money by committing to a one-year or three-year term for your VMs. This strategy provides predictable pricing and can help you reduce costs over time.

  Cost Management works with Azure Advisor to identify savings opportunities for reserved instances and savings plans. Ensure that you optimize your Azure Virtual Desktop workloads before you commit to reserved instances or savings plans. Also be aware of how reserved instances and savings plans differ concerning scoping and shared consumption of plans and instances.

- **Service tags**. Instead of using specific IP addresses for Azure services, you can use [service tags](/azure/virtual-network/service-tags-overview). Because addresses change, this approach minimizes the complexity of frequently updating network security rules. By reducing the effort that's required for maintaining networks, service tags help to lower overall costs.

- **Scaling plans**. With Azure Virtual Desktop, you can set up scaling plans for your VMs. This strategy reduces costs by helping to ensure that the correct number of session hosts are running when needed.

- **Cost Management**. When you use Cost Management, you can monitor and optimize your Azure spending. You can use this tool to identify areas where you can reduce costs and optimize your Azure Virtual Desktop deployment. Specifically, you can create budgets in Cost Management, and you can set alerts on those budgets. When spending exceeds your budget, a review is triggered to investigate why costs increased beyond the set level.

Keep in mind that the capabilities of Azure hardware expand frequently. As a result, opportunities regularly come up for workloads to further optimize costs, eliminate waste, and improve performance-to-cost ratios. Organizations find it beneficial to revisit and tweak their cost-saving measures periodically.

## Performance Efficiency

This pillar focuses on optimizing workloads that run your Azure Virtual Desktop environment. That focus includes many aspects:

- Allocating the proper size, family, and number of session hosts  
- Continuously monitoring performance and detecting anomalies by using tools such as Azure Monitor, Log Analytics, Application Insights, and alerts
- Configuring a proper scaling plan so that you have an ideal number of session hosts available for users

Considering each of these aspects helps you build an Azure Virtual Desktop environment that delivers a consistent, cohesive user experience.

## Operational Excellence

In Azure Virtual Desktop, Operational Excellence means ensuring the consistency, repeatability, and stability of your operational procedures in the development, deployment, and operating phases.

- Using IaC for repeatable, consistent deployment.
- Proper monitoring of resources to maintain the health of your environment.
- An adequate continuous integration/continuous delivery (CI/CD) pipeline that helps you achieve:
  - Timely disaster recovery.
  - Preproduction testing.
  - Replication of resources.
- Implementing Azure policy-driven governance to help ensure security and compliance within enterprise technical estates.
- Designing an RBAC delegation schema that implements least-privilege access by using built-in Azure Virtual Desktop roles to streamline the process of assigning and managing access permissions.
- Maintaining governance and compliance standards for your host pool.
- Documentation that covers all the points in this list.

These steps help teams collaborate in a way that's efficient and transparent.

## Next steps

The design principles are incorporated into specific technical design areas. Each area offers focused guidance that helps you quickly access the information that you need for enhanced productivity in a minimal amount of time. Consider the headings as navigational tools that guide you in the right direction for networking, core infrastructure, the application delivery, monitoring, security, and operational procedures.

Start by reviewing the design considerations for application delivery that are needed to support a workload.

> [!div class="nextstepaction"]
> [Application delivery](application-delivery.md)

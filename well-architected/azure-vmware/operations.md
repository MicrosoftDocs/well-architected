---
title: Operations considerations for Azure VMware Solution workloads
description: Examine Azure VMware Solution operational procedures. See how to use tools and practices like tagging and incident management to efficiently operate workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 08/10/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Operations considerations for Azure VMware Solution workloads

This article discusses the operations design area for Azure VMware Solution. The aim of this article is to build an operating model for Azure VMware Solution and the applications within the WMware software-defined datacenter (SDDC). Standard operating procedures (SOPs) are documented processes for managing a workload. Each Azure VMware Solution workload should have SOPs to govern operations. To stay aligned with business objectives and to help prevent drift from best practices, use SOPs in a continuous cycle of assessment and health checks that you run on your Azure VMware Solution workload.

## Track application dependencies

*Impact: Operational excellence*

IT teams continuously look to optimize the deployment, management, and maintenance of applications, sites, and services. This practice helps to ensure high performance, reliability, scalability, and security. Optimizing involves understanding how applications flow inside the Azure VMware Solution platform. It also involves examining external dependencies and relationships that are outside the private cloud. A dependency map is a valuable tool for developers, application architects, and IT teams as they seek to understand the structure and behavior of applications. Having insight into application components such as software and infrastructure, services, and external dependencies provides a visual way to understand data flows, functionality, and API calls.

##### Recommendations

- Use Application Insights to track dependencies such as databases, API calls, and external services.
- Use the service map feature of Azure Monitor to automatically discover and visualize different application and infrastructure components.
- Use third-party tools like New Relic and Datadog to discover and map dependencies.
- Use custom scripts or third-party configuration management tools that track the automation and deployment of dependencies.

## Use automation, version control systems, and blue-green deployments

*Impact: Operational excellence*

Organizations can decrease time to market and benefit from improved collaboration and software quality by adopting DevOps practices. For example, automation can expedite the deployment and maintenance of applications.

When you use infrastructure as code (IaC) to organize infrastructure deployments, you can benefit from improved efficiency in your infrastructure provisioning. IaC can also facilitate the adoption of DevOps principles in infrastructure management.

- You can use IaC to create several resources in Azure VMware Solution. Examples include the entire private cloud or individual components like clusters, network appliances, and storage. Tools such as Azure Resource Manager, Bicep, Terraform, the Azure CLI, and PowerShell automate the provisioning and configuration of resources in Azure VMware Solution.
- When you use IaC, you can update your infrastructure by modifying code. This approach reduces the time and effort that are required for manual configuration and provisioning.
- The output that's returned from deploying IaC can serve as documentation to help maintain and provide extra visibility into the state and configuration of provisioned resources.

Version control systems provide a way for you to manage your code and use versioning to track and roll back changes as needed.

When you need to update application code across servers, blue-green deployments can be helpful in many ways:

- They aid in managing the lifecycle of an application from development to production.
- They help give customers a consistent web experience when updates and patches are being applied.
- They use weighted algorithms to distribute traffic only to healthy servers during maintenance.

Azure VMware Solution doesn't offer methods that a cloud-native application offers for achieving blue-green deployments. But these deployments are still possible in Azure VMware Solution:

- Before you make changes to your application configuration, take snapshots of your environment.
- Use version control to ensure that you can return to a last-known good state.
- Consider creating a staging environment that mirrors production and deploys updates before you go live.
- From the staging environment, perform rolling updates to a subset of servers and test your application.

You can reduce manual effort, minimize errors, and improve resource usage by automating routine tasks like provisioning, scaling, and patching. DevOps methodologies are an important element of a well-architected solution for streamlining operations, saving time, and helping teams focus on value-added activities.

##### Recommendations

- Use IaC to deploy and provision infrastructure in a way that's repeatable, auditable, and consistent.
- Automate expansion and contraction by using IaC.
- Use version control systems such as Azure DevOps or Git to track changes, collaborate, and roll back code to previous versions as needed.
- Take advantage of the blue-green concept by creating a staging environment that mirrors production and test environments before you go live.
- Maintain the last good state of your application by using snapshots, cloning your disks, and having version-controlled code.

## Define roles and processes

*Impact: Operational excellence*

Well-defined roles and responsibilities help ensure clarity, accountability, and effective management of a well-architected Azure VMware Solution workload. Having a defined set of standards and structured processes and knowing who runs them leads to efficient operations and helps IT organizations align their technical offerings with business objectives and strategies. As the Azure VMware Solution environment grows and evolves, well-defined roles and responsibilities lead to easier task delegation and the potential to scale the solution without disruption. The result is a better experience for the application's users.

It's important to have a culture of continual improvement that focuses on efficient day-to-day operations for applications in the private cloud. Examples of operations include maintaining service-level agreements (SLAs), maintaining availability, having the capacity to minimize service disruptions, and having a smooth delivery. For instance, Azure VMware Solution makes it possible to expand an environment with minimal user input. If you manually expand your contract, you should document who performs the associated activities and how to carry them out. Azure VMware Solution operators should ensure node reservation is available for expanding the environment as needed. For example, designate individuals who are responsible for identifying underutilized or idle resources. Provide those individuals with a process for right-sizing virtual machines (VMs) to reduce unnecessary costs.

Application teams and developers should define coding guidelines for code structure, exceptions, and error handling. You should also have methods for tracking changes, such as regular code reviews, API documentation, and regular code refactoring. When you use version control, enforce best practices such as branching, commit messages, and approval workflows. These practices can help make your code consistent, easy to debug, and maintainable.

##### Recommendations

- Work with the cloud center of excellence (CCoE) team to understand the standards and guidelines for compliance, security, application architecture, and operational processes.
- Have a security and compliance team focus on security policies that are specific to Azure VMware Solution. Also have the team perform risk assessments and ensure compliance with regulatory requirements.
- Adopt a framework such as the Information Technology Infrastructure Library (ITIL). Or use International Organization for Standardization (ISO) practices to map day-to-day operations, processes, and activities. These practices can result in faster knowledge transfers, continuous improvements, and improved change management.
- Define coding standards and implement security practices during application development.

## Use tagging strategies and best practices

*Impact: Operational excellence*

You can use a tagging strategy for chargeback and resource tracking. Tags are key-pair values that you define at the resource level and apply during provisioning. You can use IaC to create, update, and destroy guest VMs. Tags and IaC work together with configuration management tools. You can use tags in the following areas:

- Environments. You can apply tags like *production*, *QA*, or *dev test* to identify resources.
- Cost centers, for tracking resource costs and expenses.
- SLAs, to prioritize SLA requirements of resources.
- Lifecycles. You can label applications as *active*, *archived*, or *retired*.
- Criticality, by labeling resources based on their business impact and significance.

As part of your governance and compliance strategy, a group should be responsible for identifying resources without tags. That group can combine automation, auditing, and processes to help identify and remediate resources that don't meet tagging compliance policies.

These tagging considerations are general. It's important that your tagging strategy supports effective resource categorization, resource lifecycle management, and reporting within Azure VMware Solution.

##### Recommendations

- Apply tags for resource management by using an organizational taxonomy to identify workloads and infrastructure. The taxonomy should include the host, business, owner, and environment.
- Use IaC to maintain and apply tags programmatically during provisioning.
- Use tags that align with your organization's compliance and governance initiatives, such as SLAs, chargeback policies, and lifecycle management practices.
- Have processes in place to identify and remedy resources that don't adhere to tagging requirements.

## Establish incident response teams

*Impact: Operational excellence*

To track workload status in a private cloud, it's essential to monitor metrics such as CPU usage, operating system logs, and security alerts. To ensure the effectiveness of your alerting system, you need to evaluate several key operational aspects. Specifically, check that:

- All critical components are identified, such as databases, network devices, and storage.
- Thresholds are set appropriately.
- Alerts are specific and actionable.
- The right people receive alerts.
- There isn't a substantial amount of noise and false positives.
- Adequate escalation procedures are in place.

Before an incident or outage, it's crucial to establish a well-defined notification process to ensure timely communication. Identifying the relevant personnel responsible for resolution is vital. A dedicated remediation team can include operations personnel, application owners, and DevOps experts who possess the expertise that's needed to resolve issues quickly. The operations team must be aware of the appropriate individuals to involve in triaging each problem.

An incident response team can effectively coordinate responses by maintaining a comprehensive distribution list. This list should include key stakeholders from business-critical departments and designated escalation contacts. Business stakeholders must be informed of any potential impact on operations that result from an incident. The assigned escalation contacts should be individuals who are capable of making decisions or escalating issues to higher levels for guidance.

Regularly reviewing the distribution list is essential to ensure its accuracy and alignment with current roles and responsibilities. Reviews ensure that key stakeholders are promptly informed about significant events that occur in Azure VMware Solution.

An IT service management (ITSM) solution can map events to tasks. For example, an Azure native ITSM might use Azure DevOps to manage tasks. It might use Azure Automation for automating IT processes and Azure Logic Apps for building workflows. The result is a customized solution for problem management in Azure VMware Solution.  

##### Recommendations

- Define the appropriate recipients for Azure VMware Solution alerts and incidents.
- Clearly define escalation contacts who should be reachable and authorized to make decisions or escalate issues.
- Identify key business stakeholders or representatives to ensure visibility into any potential impact and to provide guidance.
- Have a remediation team in place that consists of administrators, infrastructure engineers, and personnel who have the expertise that's needed to address and resolve issues.
- Integrate alerts with an ITSM like Azure DevOps, JIRA, or ServiceNow.

## Document procedures

*Impact: Reliability*

It's important to have a clear understanding of the backup and recovery infrastructure that exists in your environment. To configure a backup solution, you first need to define backup targets for your infrastructure. You should back up your applications, databases, and assets in blob storage or an Azure backup vault. You should also designate owners who are responsible for backing up and restoring your application.

##### Recommendations

- Clearly document your backup and recovery infrastructure.
- Clearly document your backup and recovery procedures.

## Implement backup and restore solutions

*Impact: Reliability*

The SDDC should protect against data loss, minimize downtime, and maintain the continuity of operations when there are unexpected disruptions or disasters.

For business continuity, you need to implement robust data protection to help ensure the availability, integrity, and recoverability of your VMs and the critical data within the Azure VMware Solution environment. The backup tools need to be in place, and you must also confirm that they work. A key principle of Azure VMware Solution is to provide independent software vendor (ISV) technology support that's validated with Azure VMware Solution. Understanding the partners and options that are available to you is critical to your backup success.

##### Recommendations

- Use backup solutions that Microsoft supports, such as Microsoft Azure Backup Server, or approved third-party vendors.

## Use Azure Site Recovery

*Impact: Reliability*

Azure Site Recovery is a disaster recovery solution that's designed to minimize downtime of the VMs in an Azure VMware Solution environment when there's a disaster. Azure Site Recovery automates and orchestrates failover and failback. Built-in nondisruptive testing helps ensure your recovery time objectives (RTOs) are met. Azure Site Recovery simplifies management through automation and helps ensure fast and highly predictable recovery times.

##### Recommendations

- In a prolonged regional outage, protect your workloads by replicating them to an alternate Azure region.
- Configure Azure Site Recovery to send backups to an alternate region.

## Rotate secrets

*Impact: Security*

It's more challenging for attackers to access or misuse encrypted data if they don't have access to encryption keys. You should securely store keys, secrets, and certificates, and you should rotate them frequently. Comprehensive steps for securing and maintaining data integrity include:

- Encrypting data.
- Storing keys securely.
- Encrypting data at the application level before you transmit data.

##### Recommendations

- Use Azure Key Vault to store encryption keys.

## Next steps

Now that you've looked at operational management procedures, see how to integrate an Azure VMware Solution workload with Azure landing zones.

> [!div class="nextstepaction"]
> [Landing zone integration](./landing-zone-integration.md)

Use the assessment tool to evaluate your design choices.

> [!div class="nextstepaction"]
> [Assessment](./assessment.md)
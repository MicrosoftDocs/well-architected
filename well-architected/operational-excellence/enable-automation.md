---
title: Recommendations for enabling automation in a workload
description: Learn how to design your workload to enable automation. Automation simplifies maintenance tasks, allowing you to update, patch, and upgrade your systems more efficiently and reliably.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for enabling automation in your workload

This guide describes the best practices for designing and implementing your workload to enable the use of automation across the breadth of the workload. Designing with automation in mind ensures that routine tasks such as provisioning resources, scaling, and deployments, can be performed quickly and reliably and prepares your architecture for future technologies and practices. Automation simplifies maintenance tasks, allowing you to update, patch, and upgrade your systems more efficiently and reliably. 

## Key design strategies

### Workload design

Designing your workload to enable automation starts at the ideation phase and carries through to on-going workload improvement practices. When planning your workload design, consider the ways that you want to leverage automation and ensure that you are putting the necessary pieces in place to enable that automation. Thinking about the workload in terms of the Well-Architected Framework  pillars can help you plan for the types of automation that you will design to enable: you can automate many of functions you will eventually have in place for security, reliability, performance, operations and cost control.  Using this perspective, you can plan for automation across the workload lifecycle.

By designing with automation in mind, you can make determinations early on that will minimize the need to do refactoring once the workload is running. Understanding your workload requirements will inform your plans for implementing automation and help you decide on which automation tools that you will standardize on. There may be off-the-shelf automation tools, like orchestration tools for example, that your team has familiarity with and adopting those tools can make the path towards automating workload functions easier, but be mindful of their limitations and compatibility with your cloud platform. In some cases, you might have to spend time developing custom software to overcome limitations and compatibility issues that you wouldn’t face by using another off-the-shelf tool that may require a bit of learning for your team. For example, some tools may integrate well with CLI tooling, while others may require REST interfaces. Be sure to investigate the tools that your cloud platform provides and plan to implement them when they can provide the functionality that you require. Using proprietary tools for your cloud platform will ensure that the automation handled by those tools is easily manageable for your team.

Examples of ways that you can proactively plan for automation include:

- You should automate your application and infrastructure deployments as much as possible to ensure that all deployments are done in a standard, predictable manner. To ensure that you will be able to automate your deployments when the time comes, ensure that you have planned for the necessary infrastructure, you have developed the deployment standards that will be followed, and that your team has been trained on using the standard set of DevOps tools that you will use. 
- All compliance requirements should be regularly validated against your workload automatically either through orchestration or through policies. When designing your workload to enable this, ensure that you have identified the validation mechanism and planned for the required systems to be in place (like orchestration servers, for example).
- To help you achieve your reliability and performance requirements, plan to make use of automatic scaling throughout your infrastructure. To design your workload to enable this, ensure that you are pre-allocating IP address space and subnets to account for the amount of systems that will be brought online during scaling operations, in addition to your planning for redundancy and natural growth.

### On-going workload improvements

After your workload is running in the cloud, it is important to view it through the lens of continuous improvement. In the context of enabling automation, new tools will become available to you to help automate functions of your workload and you should evaluate them and adopt those that can replace functions that you have in place or bolster existing automation. Likewise, over time the workload will evolve: new functionality will be added or you may find that using different infrastructure components will benefit you in a significant way. So, as your workload evolves, the automation that you can take advantage of may change and your mindset should reflect an embrace of evolution in every facet of the workload. 

Especially important to continuous improvement is a focus on applying learnings that you gain through observation of your workload. Analyze the usage patterns and customer behavior related to your workload and look for ways that existing automation can be enhanced or new automation can help your customer experience. For example, you may have automated scaling enabled to scale out your compute nodes when a load threshold is exceeded, and that is working to handle the increased load. If that load increase is short-lived at times, you may want to build some scale-in automation to decrease the number of compute nodes when the load decreases past the same threshold for a certain number of minutes.

The following sections of this guide offer recommendations on specific areas of automation that can help you in your workload design and implementation.

### Bootstrapping

Bootstrapping refers to the configuration updates to a resource that must be made after the resource (like a VM) is provisioned, but before it is available to the workload as part of the workload pool. Often bootstrapping is thought of in relation to VMs, but many other resources need to be configured as part of the deployment process including PaaS app hosting technologies and container hosting technologies like Kubernetes.

When there are bootstrapping solutions provided by your cloud platform, prefer these over developing your own if they can provide the functionality that you need. For example, you can take advantage of VM Extensions in Azure that will allow you to make certain pre-defined configuration changes during the deployment process or you can customize your configuration changes by injecting PowerShell scripts.

### Authentication and authorization

Take automation into account when designing your authentication and authorization strategy. Maintaining the highest level of security for authentication and authorization is critical for production workloads, but can impact your automation strategy. For example, always requiring the use of biometric or multi-factor authentication adds complexity that must be accounted for in your automation design. Use non-human, secure accounts for automated authentication to run services and tasks. These accounts can be things like managed identities, workload identities or certificates. Ensure that you have included secret and key management in your authentication automation.

### Design variability into your workload

Avoid unnecessarily deploying new infrastructure when small changes are made by using a strategy of designing a level of variability into your artifacts. For example, rather than always redeploying your infrastructure when a feature flag changes, you can have parameters that can be set to update things like app configurations. Be sure to clearly define and document how variability is used to avoid its overuse and the risk of configuration drift.

### Building a control plane

A control plane in this context is the backend system or suite of tools that you use to manage the application and its dependencies through a unified interface. Build your control plane interface to support automation by external tools (like a REST interface, CLI, or webhook).

Expose maintenance operations through the control plane that allows you to encapsulate coordination and dependencies among workload components. For example, orderly backup and restore, bootstrapping, configuration, import/export, batching operations. Pick the right level of granularity when deciding the operations to expose through the control plane.

### Monitoring and logging

Develop your monitoring strategy to capture metrics that can drive the type of automation you require. Use structured logging and custom metrics that provide the information required by automation in a manner that is easy to recognize with the automation tools. The metrics that you capture should be combined with thresholds defined in the monitoring system that will trigger alerts and automated actions when appropriate. These actions can include things like automatic notifications and self-healing mechanisms (link to Reliability self-healing guide). 

### User lifecycle

Design your application and infrastructure to allow you to automate onboarding and offboarding of users, whether that means individuals or multitenant customers. This can include planning for automated database updating through scripts and infrastructure provisioning and deprovisioning, along with credential and secret management.

### Orchestration and policy use

In your on-going management of your workload, automating the desired state configuration (DSC) of your resources will help you ensure that you are certain that those resources all meet your compliance and business requirements, and configuration drift is caught and remediated quickly. You can approach this automation with two types of tools: orchestration tools or policy management tools. Orchestration that is integrated into your CI/CD pipeline (for example, Azure DevOps or Jenkins) can be thought of as a push-based mechanism, meaning that configuration updates are pushed out through a workflow event (like a manual or automated deployment). These updates are run as part of a task sequence defined in your deployment script that runs until the execution terminates. On the other hand, policy managed configuration updates can be thought of as a pull-based mechanism, meaning that a system runs at the foundational level of your workload that periodically polls the workload to check its state against your defined DSC and takes corrective action when it identifies misalignment.  Determining which approach is a better fit for your workload comes down to a few factors that you should consider.

- Orchestration tools take care of the configuration changes required for your workload during deployment but do not have built-in capabilities to proactively poll your workload for configuration drift.  Orchestration tools should be integrated into your CI/CD pipeline to maintain a standard for IaC deployments and management.
  - An advantage of using orchestration is that resources are always fully configured when deployed 
- Policy management tools allow you to define policies that affect one or more groups of resources. These policies are enforced when the resource checks in with the policy management system and misalignments are caught during audits performed by the system.
  - An advantage of using policy management is that these systems are using not code driven, so operators may have an easier learning curve adopting this technology.

When weighing your options, consider whether the configuration updates that you need to make on new resources must be present at deployment time or they can happen sometime afterward. Also consider whether defining them in code and updating that code, when necessary, will fit your operational practices. Additionally, consider how many different configurations of a given resource type you will need to deploy. If there are many different configurations across resource types, policies may be an easier way to manage those updates rather than adding complexity to your pipeline.

## Azure facilitation

### Policy management

**Azure Policy**

[Azure Policy](/azure/governance/policy/overview) is a unified policy management platform that allows organizations to enforce organizational standards and to assess compliance at-scale. Through its compliance dashboard, it provides an aggregated view to evaluate the overall state of the environment, with the ability to drill down to the per-resource, per-policy granularity. It also helps to bring your resources to compliance through bulk remediation for existing resources and automatic remediation for new resources.

### Bootstrap automation

**Azure VM extensions**

Azure VM extensions are small packages that run post-deployment configuration and automation on Azure VMs. Several extensions are available for many different configuration tasks, such as running scripts, configuring antimalware solutions, and configuring logging solutions. These extensions can be installed and run on VMs by using an ARM template, the Azure CLI, Azure PowerShell module, or the Azure portal. Each Azure VM has a VM Agent installed, and this agent manages the lifecycle of the extension.

A typical VM extension use case is to use a custom script extension to install software, run commands, and perform configurations on a virtual machine or Azure Virtual Machine Scale Sets. The custom script extension uses the Azure VM Agent to download and run a script. The custom script extensions can be configured to run as part of infrastructure as code deployments such that the VM is created, and then the script extension is run on the VM. Extensions can also be run outside of an Azure deployment by using the Azure CLI, PowerShell module, or the Azure portal.

**cloud-init**

cloud-init is a known industry tool for configuring Linux virtual machines on first boot. Much like the Azure custom script extension, cloud-init lets you install packages and run commands on Linux virtual machines. cloud-init can be used for things like software installation, system configurations, and content staging. Azure includes many cloud-init enabled Marketplace virtual machine images across many of the most well-known Linux distributions. For a full list, see cloud-init support for virtual machines in Azure.

**Azure deployment script resource**

When you perform Azure deployments, you might need to run arbitrary code for bootstrapping things like managing user accounts, Kubernetes pods, or querying data from a non-Azure system. Because none of these operations are accessible through the Azure control plane, some other mechanism is required for performing this automation. To run arbitrary code with an Azure deployment, see the Microsoft.Resources/deploymentScripts Azure resource.

The deployment script resource behaves like any other Azure resource in the following ways:

- Can be used in an ARM template.
- Contains ARM template dependencies on other resources.
- Consumes input, produces output.
- Uses a user-assigned managed identity for authentication.

When deployed, the deployment script runs PowerShell or Azure CLI commands and scripts. Script execution and logging can be observed in the Azure portal or with the Azure CLI and PowerShell module. Many options can be configured like environment variables for the execution environment, timeout options, and what to do with the resource after a script failure.

**Bootstrapping AKS clusters with GitOps**

You can bootstrap a newly provisioned AKS cluster using GitOps and the Flux v2 cluster extension. To bootstrap your cluster in this method, you declare your configuration settings in Git repositories. Because these files are stored in a Git repository, they're versioned, and changes between versions are easily tracked. Kubernetes controllers run in the clusters and continually reconcile the cluster state with the desired state declared in the Git repository. These operators pull the files from the Git repositories and apply the desired state to the clusters. The operators also continuously assure that the cluster remains in the desired state. See the AKS Baseline reference architecture bootstrapping [section](/azure/architecture/reference-architectures/containers/aks/baseline-aks) for more information and guidance.

### Configuration Management

**Azure Automanage State Configuration**

[Azure Automanage State Configuration](/azure/governance/machine-configuration/overview) is a DSC management tool built on top of Azure Policy that extends its capabilities to performing tasks usually associated with orchestration tools. Using a VM extension, you can apply many pre-defined configuration updates directly to individual VMs or to groups of VMs as defined within Azure Policy.

**Azure App Configuration**

[Azure App Configuration](/azure/azure-app-configuration/overview) provides a service to centrally manage application settings and feature flags.  It works in concert with Azure Key Vault to let you easily manage a wide variety of application configurations across your environments securely.

## Tradeoffs

When designing your workload to enable automation, consider the degree of control that you want to maintain vs the efficiency you can gain through automation. In some cases, your workload might not be mature enough to automate some functions or you might need some level of flexibility that automation limits. 

Also consider the skill set that your team brings to the table when designing your workload. If a high degree of automation will ultimately require tools that your team isn’t equipped to support, then you may need to use a less comprehensive design as an intermediate step.

## Related links

[GitOps Flux v2 configurations with AKS and Azure Arc-enabled Kubernetes - Azure Arc](/azure/azure-arc/kubernetes/conceptual-gitops-flux2)


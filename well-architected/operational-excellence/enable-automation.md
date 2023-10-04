---
title: Recommendations for enabling automation in a workload
description: Learn how to design your workload to enable automation. Automation simplifies maintenance tasks, allowing you to update, patch, and upgrade your systems more efficiently and reliably.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for enabling automation in your workload

**Applies to this Azure Well-Architected Framework Operation Excellence checklist recommendation:**

|[OE:10](checklist.md)| Design and implement automation upfront for operations such as lifecycle concerns, bootstrapping, and applying governance and compliance guardrails. Don't try to retrofit automation later. Choose automation features that your platform provides. |
|---|---|

This guide describes the recommendations for designing and implementing your workload to enable automation. Design your workload with automation in mind to ensure that routine tasks such as provisioning resources, scaling, and deployments are performed quickly and reliably. Automation simplifies maintenance tasks and allows you to update, patch, and upgrade your systems more efficiently.

## Key design strategies

### Workload design

You can design your workload to support automation from the ideation phase to the on-going improvement phase. First, consider how you want to apply automation in your workload to help ensure that you're putting the necessary pieces in place. Think about your workload in terms of the Well-Architected Framework pillars to help plan for the types of automation you'll use. You can automate many functions of security, reliability, performance, operations and cost control.

Design with automation in mind to minimize refactoring once your workload is running. Consider your workload requirements when deciding which automation tools you'll use. There might be off-the-shelf automation tools that your team is already familiar with. Adopting those tools can make the path towards automating your workload easier, but be mindful of their limitations and compatibility with your cloud platform. For example, some automation tools might integrate well with Azure CLI tooling, while others might require REST interfaces. Always investigate the tools that your cloud platform provides to ensure they're compatible and provide the functionality you require. Examples of ways that you can proactively plan for automation include:

- *Deployment*: Automate your application and infrastructure deployments to ensure a predictable standard. Plan for automated deployment by developing deployment standards, training your team on the tools that you'll use, and implementing the necessary infrastructure.

- *Validation*: Automatically validate compliance requirements against your workload using orchestration or policy tools. Identify the appropriate validation tool for your workload and plan to implement the required systems, for example, orchestration servers.

- *Automatic scaling*: Use automatic scaling throughout your infrastructure to help you achieve your reliability and performance requirements. You should allocate IP address space and subnets in your workload ahead of time to account for scaling operations, in addition to planning for redundancy and natural growth.

### Continuous workload improvements

Once your workload is running in the cloud, it's important to prioritize continuous improvement. Observe your workload in action, analyze usage patterns, and review customer behavior related to your workload to identify areas where you can improve automation. Look for ways to enhance existing automation or introduce new automation to improve your customer experience. For example, you might have automated scaling enabled, but the workload increase is short-lived. You can integrate scale-in automation to decrease CPU usage when the load drops below the threshold.

The following sections of this guide offer recommendations on specific areas of automation that can help you in your workload design and implementation.

### Bootstrapping

Bootstrapping refers to the configuration updates to a resource that must be made after it's provisioned, but before it's available as part of the workload pool. Bootstrapping is often associated with virtual machines (VMs), but many other resources must be set up as part of the deployment process including platform as a service (PaaS) technologies and container hosting technologies like Azure Kubernetes Services (AKS).

Your cloud platform might provide bootstrapping solutions for you, which you should use where possible. For example, using VM Extensions in Azure you can make certain pre-defined configuration changes during the deployment process and customize your configuration changes by injecting PowerShell scripts.

### Authentication and authorization

Take automation into account when designing your authentication and authorization strategy. It's important to maintain the highest level of security in production workloads, but this can affect automation. For example, the use of biometric or multifactor authentication adds complexity that must be accounted for in your automation design. Use non-human, secure accounts for automated authentication, such as managed identities, workload identities, or certificates. Ensure that you have included secret and key management in your automation for increased authentication security.

### Design variability into your workload

Avoid unnecessarily deploying new infrastructure when small changes are made by building flexibility into your artifacts. For example, rather than redeploying your infrastructure when a feature flag changes, you can use parameters that are set to update things like app configurations. Be sure to clearly define and document how variability is used to avoid overuse and configuration drift.

### Build a control plane

A control plane is the backend system or suite of tools that you use to manage the application and its dependencies through a unified interface. Build your control plane like a REST interface, CLI, or webhook to support automation by external tools.

Identify maintenance operations through the control plane that allow you to coordinate workload components, for example: orderly backup and restore, bootstrapping, configuration, import/export, and batching operations. Be careful to pick the right level of granularity when deciding the operations to expose through the control plane.

### Monitor and log

Develop a monitoring strategy to capture metrics that drive the type of automation you require. Use structured logging and custom metrics to provide the information required by automation in a format that is easy to recognize with automation tools. The metrics that you capture should be paired with thresholds defined in the monitoring system that trigger alerts and automated actions, like notifications or self-healing mechanisms, when appropriate. For more information, see [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md).

### User lifecycle

Design your application and infrastructure to allow for automated user onboarding and offboarding, including individuals or multitenant customers. Plan for automated database updates through scripts, infrastructure provisioning and deprovisioning, and credential and secret management.

### Orchestration and policy use

As part of your continuous workload management, you can automate the Desired State Configuration (DSC) of your resources to help ensure that they meet compliance and business requirements. DSC automation helps ensure that configuration drift is caught and remediated quickly. You can automate DSC using orchestration tools or policy management tools. Think of orchestration tools, like Azure DevOps services or Jenkins, as a push-based mechanism. Using orchestration tools, configuration updates are pushed out through a workflow event, like a manual or automated deployment. These updates are run as part of a task sequence defined in your deployment script. Policy management tools use a pull-based mechanism, meaning that a system runs at the foundational level of your workload that periodically polls the workload to check its state against your defined DSC. If the poll identifies a misalignment or configuration drift the tool takes corrective action. Consider the following factors when deciding between orchestration and policy management tools:

- Orchestration tools don't have built-in capabilities to proactively poll your workload for configuration drift. Orchestration tools should be integrated into your CI/CD pipeline to maintain a standard for infrastructure as code (IaC) deployment and management. An advantage of using orchestration tools is that resources are always fully configured when deployed.

- Policy management tools allow you to define policies that affect one or more groups of resources. These policies are enforced when the resource checks in with the policy management system. An advantage of using policy management is that these systems aren't code driven, so they might be easier for operators on your team to adopt.

When deciding between orchestration or policy tools, consider whether the configuration updates you're planning to make on new resources must be made at the time of deployment. Also consider if defining updates in code fits your operational practices and how many resource types you plan to deploy. If there are many different configurations across resource types, policy tools might be an easier way to manage updates.

## Azure facilitation

### Policy management

**Azure Policy:** Using [Azure Policy](/azure/governance/policy/overview), organizations can enforce standards and assess compliance at-scale. Azure Policy provides an aggregated view to evaluate the overall state of the workload environment in the compliance dashboard. Using Azure Policy you can drill down to per-resource or per-policy granularity. You can also use Azure Policy to remediate new resources automatically or existing resources in bulk.

### Bootstrap automation

**Azure Virtual Machines extensions:** Virtual Machines extensions are small packages that run post-deployment configuration and automation on VMs. Several extensions are available for different configuration tasks, such as running scripts, configuring anti-malware solutions, and configuring logging solutions. Install and run these extensions on VMs by using an ARM template, Azure CLI, Azure PowerShell module, or the Azure portal. Each VM has a VM agent installed that manages the lifecycle of the extension.

Typically, VM extensions use a custom script extension to install software, run commands, and perform configurations on a virtual machine or Azure Virtual Machine Scale Sets. You can set these extensions up to run as part of IaC deployments so that they run on new VMs using Azure VM agent. Extensions can also be run outside of an Azure deployment by using the Azure CLI, PowerShell module, or the Azure portal.

**Cloud-init:** Cloud-init is a known industry tool for configuring Linux virtual machines on first boot. Much like Azure custom script extensions, cloud-init lets you install packages and run commands on Linux virtual machines. You can use cloud-init for software installation, system configuration, and content staging. Azure includes many cloud-init enabled VM images across some of the most well-known Linux distributions. For a full list, see [cloud-init support for VMs in Azure](/azure/virtual-machines/linux/using-cloud-init).

**Azure deployment script resource:** When you deploy using Azure, you might need to run arbitrary code for bootstrapping the management of user accounts, Kubernetes pods, or querying data from a non-Azure system. Because none of these operations are accessible through the Azure control plane, a separate mechanism is required. For more information, see the [Microsoft.Resources deploymentScripts Azure resource](/azure/templates/microsoft.resources/deploymentscripts?pivots=deployment-language-bicep). The deployment script resource behaves like any other Azure resource in the following ways:

- It can be used in an ARM template.

- It contains ARM template dependencies on other resources.

- It consumes input and produces output.

- It uses a user-assigned managed identity for authentication.

When deployed, the deployment script runs PowerShell or Azure CLI commands and scripts. Script runs and logging can be observed in the Azure portal or with the Azure CLI and PowerShell module. You can customize the variables for the run environment, timeout options, and resource management after a script failure.

**Bootstrap AKS clusters with GitOps.** You can bootstrap a newly provisioned AKS cluster using GitOps and the Flux v2 cluster extension by declaring your configuration settings in Git repositories. Because AKS cluster files are stored in a Git repository, they're versioned, and changes between versions are easily tracked. Kubernetes controllers run in the clusters and continually reconcile the cluster state with the desired state declared in the Git repository by pulling the files from the repository. For more information, see [AKS Baseline reference architecture bootstrapping](/azure/architecture/reference-architectures/containers/aks/baseline-aks).

### Configuration management

[**Azure Automanage State Configuration**](/azure/governance/machine-configuration/overview) is a DSC management tool built on top of Azure Policy that performs as an orchestration tool. Using a VM extension, you can directly apply many pre-defined configuration updates to individual VMs or groups of VMs as defined within Azure Policy.

[**Azure App Configuration**](/azure/azure-app-configuration/overview) provides a service to centrally manage application settings and feature flags. It works with Azure Key Vault to let you securely manage a wide variety of application configurations across your environment.

## Tradeoffs

When designing your workload to enable automation, consider the degree of control that you want to maintain versus the efficiency you can gain through automation. In some cases, your workload might not be mature enough to automate some functions or you might need a level of flexibility that automation doesn't provide.

Also consider the skill set of your team when designing your workload. If a high degree of automation requires tools that your team isn’t equipped to support, then you might need to use a less comprehensive design as an intermediate step.

## Related links

[GitOps Flux v2 configurations with AKS and Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/conceptual-gitops-flux2)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

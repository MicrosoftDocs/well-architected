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

|[OE:10](checklist.md)| Design and implement the workload with the expectation of using automation for items such as lifecycle concerns, bootstrapping, and applying governance & compliance guardrails up front instead of retrofitting automation later. Prefer the use of automation features provided natively by your platform.|
|---|---|

This guide describes the recommendations for designing and implementing your workload to enable automation. Design your workload with automation in mind to ensure that routine tasks such as provisioning resources, scaling, and deployments are performed quickly and reliably. Automation simplifies maintenance tasks, which allows you to update, patch, and upgrade your systems more efficiently and reliably.

## Key design strategies

### Workload design

You can design your workload for automation from the ideation phase to the on-going improvement phase. First, consider how you want to apply automation to ensure that you're putting the necessary pieces in place when planning your workload. Think about your workload in terms of the Well-Architected Framework pillars to help plan for the types of automation you'll need. You can automate many of the functions for security, reliability, performance, operations and cost control.

Design with automation in mind to minimize the need to refactor once your workload is running. Your workload requirements inform your plans for implementing automation and help you decide which automation tools you'll use. There might be off-the-shelf automation tools, like orchestration tools, that your team is already familiar with. Adopting those tools can make the path towards automating your workload easier, but be mindful of their limitations and compatibility with your cloud platform. For example, some tools might integrate well with CLI tooling, while others might require REST interfaces. Always investigate the tools that your cloud platform provides to ensure they're compatible and provide the functionality you require. Using proprietary tools for your cloud platform ensures that the automation handled by those tools is easily manageable for your team.

Examples of ways that you can proactively plan for automation include:

- Automate your application and infrastructure deployments to ensure a predictable standard. Plan for automation by developing deployment standards, training your team on the tools that you'll use, and implementing the necessary infrastructure.
- Automatically validate compliance requirements against your workload. Identify the validation mechanism and plan for the required systems to be in place, for example, orchestration servers.
- Use automatic scaling throughout your infrastructure to help you achieve your reliability and performance requirements. You should allocate IP address space and subnets in your workload to account for new systems during scaling operations, in addition to planning for redundancy and natural growth.

### Continuous workload improvements

Once your workload is running in the cloud, it's important to view it through the lens of continuous improvement. New tools might become available to help automate workload functions, you should evaluate and adopt tools that can replace functions or bolster existing automation.

Observe your workload in action to identify areas where automation can be improved. Analyze usage patterns and customer behavior related to your workload. Look for ways that existing automation can be enhanced or new automation can be introduced to help your customer experience. For example, you have automated scaling enabled, but the load increase is short-lived, so you integrate scale-in automation to decrease the number of compute nodes in use when the load drops below the threshold for a certain number of minutes.

The following sections of this guide offer recommendations on specific areas of automation that can help you in your workload design and implementation.

### Bootstrapping

Bootstrapping refers to configuration updates to a resource that must be made after the resource is provisioned, but before it's available to the workload as part of the workload pool. Bootstrapping is often thought of in relation to VMs, but many other resources need to be set up as part of the deployment process including platform as a service (PaaS) app hosting technologies and container hosting technologies like Kubernetes.

Your cloud platform might provide bootstrapping solutions for you, which you should use instead of developing your own where possible. For example, you can take advantage of VM Extensions in Azure that allow you to make certain pre-defined configuration changes during the deployment process or you can customize your configuration changes by injecting PowerShell scripts.

### Authentication and authorization

Take automation into account when designing your authentication and authorization strategy. It's important to maintain the highest level of security in production workloads, but this can affect your automation strategy. For example, the use of biometric or multifactor authentication adds complexity that must be accounted for in your automation design. Use non-human, secure accounts for automated authentication to run services and tasks. These accounts include managed identities, workload identities, or certificates. Ensure that you have included secret and key management in your authentication automation.

### Design variability into your workload

Avoid unnecessarily deploying new infrastructure when small changes are made by building a level of flexibility into your artifacts. For example, rather than redeploying your infrastructure when a feature flag changes, you can use parameters that are set to update things like app configurations. Be sure to clearly define and document how variability is used to avoid its overuse and the risk of configuration drift.

### Build a control plane

A control plane in this context is the backend system or suite of tools that you use to manage the application and its dependencies through a unified interface. Build your control plane interface to support automation by external tools, like a REST interface, CLI, or webhook.

Expose maintenance operations through the control plane that allow you to encapsulate coordination and dependencies among workload components. For example, orderly backup and restore, bootstrapping, configuration, import/export, and batching operations. Pick the right level of granularity when deciding the operations to expose through the control plane.

### Monitoring and logging

Develop your monitoring strategy to capture metrics that drive the type of automation you require. Use structured logging and custom metrics that provide the information required by automation in a manner that is easy to recognize with the automation tools. The metrics that you capture should be combined with thresholds defined in the monitoring system that trigger alerts and automated actions when appropriate. These actions can include things like automatic notifications and self-healing mechanisms For more information, see [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md).

### User lifecycle

Design your application and infrastructure to allow you to automate user onboarding and offboarding, whether that means individuals or multitenant customers. This can include planning for automated database updating through scripts and infrastructure provisioning and deprovisioning, along with credential and secret management.

### Orchestration and policy use

You can automate the desired state configuration (DSC) of your resources to help ensure that they meet compliance and business requirements. DSC automation helps ensure that configuration drift is caught and remediated quickly. You can implement this automation with orchestration tools or policy management tools. Orchestration tools that are integrated into your CI/CD pipeline, like Azure DevOps or Jenkins, can be thought of as a push-based mechanism, meaning that configuration updates are pushed out through a workflow event, like a manual or automated deployment. These updates are run as part of a task sequence defined in your deployment script. On the other hand, policy management tools can be thought of as a pull-based mechanism, meaning that a system runs at the foundational level of your workload that periodically polls the workload to check its state against your defined DSC and takes corrective action when it identifies misalignment. Consider the following factors when deciding between orchestration and policy management tools:

- Orchestration tools don't have built-in capabilities to proactively poll your workload for configuration drift. Orchestration tools should be integrated into your CI/CD pipeline to maintain a standard for infrastructure as code (IaC) deployment and management. An advantage of using orchestration is that resources are always fully configured when deployed.
- Policy management tools allow you to define policies that affect one or more groups of resources. These policies are enforced when the resource checks in with the policy management system. An advantage of using policy management is that these systems aren't code driven, so they might be easier for operators to adopt.

When weighing your options, consider whether configuration updates must be made at the time of deployment or not. Also consider whether defining updates in code fits your operational practices and how many different configurations of a given resource type you'll need to deploy. If there are many different configurations across resource types, policy tools might be an easier way to manage those updates rather than adding complexity to your pipeline.

## Azure facilitation

### Policy management

**Azure Policy:** Using [Azure Policy](/azure/governance/policy/overview), organizations can enforce standards and assess compliance at scale. Through its compliance dashboard, Azure Policy provides an aggregated view to evaluate the overall state of the environment, with the ability to drill down to per-resource or per-policy granularity. You can also use Azure Policy to remediate existing resources in bulk or new resources automatically.

### Bootstrap automation

**Azure VM extensions:** Azure VM extensions are small packages that run post-deployment configuration and automation on Azure VMs. Several extensions are available for many different configuration tasks, such as running scripts, configuring anti-malware solutions, and configuring logging solutions. These extensions can be installed and run on VMs by using an ARM template, the Azure CLI, Azure PowerShell module, or the Azure portal. Each Azure VM has a VM Agent installed, and this agent manages the lifecycle of the extension.

A typical VM extension use case is to use a custom script extension to install software, run commands, and perform configurations on a virtual machine or Azure Virtual Machine Scale Sets. The custom script extension uses the Azure VM Agent to download and run a script. The custom script extensions can be set up to run as part of IaC deployments so that they run on new VMs. Extensions can also be run outside of an Azure deployment by using the Azure CLI, PowerShell module, or the Azure portal.

**Cloud-init:** Cloud-init is a known industry tool for configuring Linux virtual machines on first boot. Much like Azure custom script extensions, cloud-init lets you install packages and run commands on Linux virtual machines. Cloud-init can be used for software installation, system configuration, and content staging. Azure includes many cloud-init enabled Marketplace virtual machine images across some of the most well-known Linux distributions. For a full list, see [cloud-init support for virtual machines in Azure](/azure/virtual-machines/linux/using-cloud-init).

**Azure deployment script resources**: When you deploy using Azure, you might need to run arbitrary code for bootstrapping things like managing user accounts, Kubernetes pods, or querying data from a non-Azure system. Because none of these operations are accessible through the Azure control plane, some other mechanism is required for to perform this automation. For more information, see the [Microsoft.Resources deploymentScripts Azure resource](/azure/templates/microsoft.resources/deploymentscripts?pivots=deployment-language-bicep).

The deployment script resource behaves like any other Azure resource in the following ways:

- Can be used in an ARM template.
- Contains ARM template dependencies on other resources.
- Consumes input and produces output.
- Uses a user-assigned managed identity for authentication.

When deployed, the deployment script runs PowerShell or Azure CLI commands and scripts. Script running and logging can be observed in the Azure portal or with the Azure CLI and PowerShell module. Many options can be set up like environment variables for the run environment, timeout options, and resource management after a script failure.

**Bootstrap Azure Kubernetes Service (AKS) clusters with GitOps.** You can bootstrap a newly provisioned AKS cluster using GitOps and the Flux v2 cluster extension. To bootstrap your cluster in this method, declare your configuration settings in Git repositories. Because these files are stored in a Git repository, they're versioned, and changes between versions are easily tracked. Kubernetes controllers run in the clusters and continually reconcile the cluster state with the desired state declared in the Git repository. These operators pull the files from the Git repositories and apply the desired state to the clusters. The operators also continuously assure that the cluster remains in the desired state. For more information and guidance, see [AKS Baseline reference architecture bootstrapping](/azure/architecture/reference-architectures/containers/aks/baseline-aks).

### Configuration management

[**Azure Automanage State Configuration**](/azure/governance/machine-configuration/overview) is a DSC management tool built on top of Azure Policy that extends its capabilities to performing tasks usually associated with orchestration tools. Using a VM extension, you can apply many pre-defined configuration updates directly to individual VMs or to groups of VMs as defined within Azure Policy.

[**Azure App Configuration**](/azure/azure-app-configuration/overview) provides a service to centrally manage application settings and feature flags. It works in concert with Azure Key Vault to let you easily manage a wide variety of application configurations across your environments securely.

## Tradeoffs

When designing your workload to enable automation, consider the degree of control that you want to maintain versus the efficiency you can gain through automation. In some cases, your workload might not be mature enough to automate some functions or you might need a level of flexibility that automation limits.

Also consider the skillset of your team when designing your workload. If a high degree of automation requires tools that your team isn’t equipped to support, then you may need to use a less comprehensive design as an intermediate step.

## Related links

[GitOps Flux v2 configurations with AKS and Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/conceptual-gitops-flux2)

## Operational Excellence checklist

Refer to the complete set of recommendations.

[Operational Excellence checklist](checklist.md)

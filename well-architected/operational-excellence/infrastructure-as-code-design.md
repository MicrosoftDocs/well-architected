---
title: Using infrastructure as code in deployments
description: Learn how to use infrastructure as code (IaC) as the standard for all of your infrastructure deployments.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for using infrastructure-as-code

**Applies to: OE 05**

This guide describes the recommendations for using infrastructure-as-code (IaC) as the standard for all of your infrastructure deployments. Using IaC allows you to integrate your infrastructure deployments and management into your existing software development practices, providing you with a consistent, standard methodology for development and deployment for all components of your workload. Relying on manual deployments puts your workload at risk of inconsistent configurations and potentially insecure design.

**Definitions**

| Term | Definition |
|---|---|
| Declarative tools | Declarative tools define the end-state of a deployment and rely on the system to determine how to deploy the resources to match the defined end-state. |
| Imperative tools | Imperative tools list the steps to execute that will result in the desired end-state. |
| Modules | A method of abstracting groups of resources to simplify complex deployments. |
| Mutable infrastructure | Mutable infrastructure is meant to be changed in place. Deployments would change the configuration of the infrastructure, rather than replacing it with new infrastructure. |
| Immutable infrastructure | Immutable infrastructure is meant to be replaced with new infrastructure running the new configuration with each deployment and must not be changed in place. |

## Key design strategies

As discussed in the supply chain (link to OpEx supply chain guide) and safe deployment practices (link to OpEx safe deployment guide) guides, you should have a strict policy of only deploying infrastructure changes (including configuration changes) through code and you should deploy IaC through your continuous integration / continuous deployment (CI/CD) pipelines. Adopting such a policy will enforce consistency in processes for all IaC deployments, minimize the risk of configuration drift across your environments, and ensure infrastructure consistency across your environments. Additionally, you should standardize your IaC development and deployment tools and processes in a style guide. Recommendations for your style guide include:

**Prefer declarative over imperative tools.** Declarative tools and their associated files are a better overall choice for deploying and managing IaC than imperative tools. Declarative tools use a simpler syntax for their definition files, only defining what you want the environment to look like after the deployment finishes. Imperative tools depend on you defining the steps required to get to the desired end-state, so they can be much more complex than declarative files. Declarative definition files also help reduce the technical debt of maintaining imperative code, such as deployment scripts, that can accrue over time.

**Use native tools for your cloud platform and other industry-proven tools with native platform integration.** Your cloud platform has tools that they have developed and that they offer to make deploying IaC easy and straightforward. Take advantage of these tools and other third-party tools with native integration, like Terraform for example, rather than developing your own solutions as the native tools are supported by your platform and have most of the functionality that you will need built in, and they will be continuously updated by the platform provider, making them more useful as the platform evolves.

**Use the right tools for specific tasks and infrastructure types.** There are multiple tasks involved in an infrastructure lifecycle beyond deployments. Configuration needs to be applied and maintained, for example, and the tool you use to script deployments, like Terraform, might not be the best tool for every management operation. Likewise, applying desired state configuration (DSC) for different infrastructure types might need different tools. For example, there are specific tools like Ansible for managing DSC for VMs, whereas Flux is a good tool for managing DSC on Kubernetes clusters. Likewise, platfom-as-a-service (PaaS) services might have different tools for configuration management (like Azure App Configuration for example) that can be handled through IaC and software-as-a-service (SaaS) services may be more limited as they are more tightly controlled by the platform. So, think about all the tasks and types of infrastructure that will be in scope for your IaC practices and standardize on tools that do the jobs that you need them to do and can be integrated into your development and management practices.

**Your scripts and templates should be flexible enough to easily deploy a variety of environments.** Use parameters, variables, and configuration files to deploy a standard set of resources that can be modified to deploy any environment in your code promotion stack. Abstract things like resource size, count, name, locations to deploy into, and some configuration settings. Be careful not to abstract too much however; there are things that can be abstracted with a parameter or variable that may not actually change over the course of the workload lifecycle or very rarely, so those shouldn’t be abstracted.

> [!NOTE]
> Avoid using different IaC assets for different environments: you should not have different Terraform files for production and test environments, for example. All environments should use one file and that file can be manipulated to deploy into different environments as needed.

**Strategize and standardize on the use of modules.** In the same way as parameters and variables, [modules](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code) are useful for making your infrastructure deployments repeatable, but be thoughtful about how they are used. Having a standardized abstraction strategy will help ensure that modules are built to meet specific, agreed upon goals. Use modules to encapsulate complex configurations or combinations of resources. Avoid modules if you’re only using the default configuration of the resource. Additionally, be judicious in developing new modules. Opt for use of maintained open-source modules when appropriate, like non-sensitive scenarios.

**Document standards to cover manual steps.** There may be steps related to deploying and maintaining infrastructure particular to your environment that require manual intervention. Ensure that these steps are minimized to the extent possible and clearly documented. As part of your style guide and standard operating procedures, ensure that manual steps are standardized to ensure that tasks are performed safely and consistently.

**Document standards to deal with orphaned resources.** Depending on the tools you use for configuration management and their limitations, there may be times when a particular resource is no longer needed for your workload and that resource cannot be automatically removed by your IaC tools. For example, you may be moving from VMs to a PaaS service for some function and the IaC tooling doesn’t have logic to remove the retired resources. Those resources can then become orphaned if the workload team doesn’t remember to manually delete them. To deal with these scenarios, standardize a strategy to scan for orphaned resources and delete them. You will also need to consider how you will ensure that your templates are up to date. Be sure to research the limitations of your IaC tooling to understand what you may need to plan for in these situations.

### Other IaC strategies

Consider the following recommendations when using IaC for your workload:

**Use a layered approach to align your IaC pipelines within the workload stack**. Separating your IaC pipelines into layers will help you manage complex environments. Deploying dozens or hundreds of resources as a monolithic package is inefficient and can introduce multiple issues, like broken dependencies. Having multiple pipelines aligned with layers that are composed of resources whose deployment lifecycles or factors like functionality closely match makes managing IaC deployments easier. Core infrastructure like networking resources rarely need changes more complex than configuration updates so those resources should make up a “low-touch” IaC pipeline. You may have one or more “medium-touch” and “high-touch” IaC pipelines for resources depending on the complexity of your workload. Using a Kubernetes-based application stack as an example, one medium-touch layer might be composed of the clusters, storage resources and database services. High-touch layers would be composed of the application containers that would be updated very frequently in a continuous delivery mode

**Treat your IaC and application code the same.** Treating your IaC artifacts the same as your application code artifacts will help you use the same rigor for managing code across all of the pipelines. Moreover, IaC development and deployment practices should mirror application practices: standards for version control, branching, code promotion, and quality should all be identical. Also consider colocating your IaC assets alongside your application code assets. This helps ensure that the same processes are followed with every deployment and helps you avoid issues like inadvertently deploying infrastructure before the necessary application code or vice versa.

**Collaborate with other teams in your organization for standardization and reusability.** Large organizations can sometimes have multiple teams developing and supporting workloads, so collaboration across teams to agree on standards will help you reuse libraries, templates, and modules to gain efficiency and consistency across workload environments. Likewise, IaC tools should be standardized across the organization to the extent practical.

**Apply the principle of “security as code” to ensure that security is part of the deployment pipeline.** Include vulnerability scanning and configuration hardening as part of IaC development process.  Scan your IaC repos for keys and secrets being exposed. An advantage of using IaC is that security-focused team members can review the code before deployment to ensure that the configuration that has been blessed by security for release is actually what is deployed to production. See the security development lifecycle guide (link to SE02-security-development-lifecycle guide) for detailed guidance.

**Test routine and non-routine activities.** Test deployments, configuration updates, and recovery processes, including deployment rollback processes. 

### Mutable vs immutable infrastructure

The choice between deploying mutable vs immutable infrastructure depends on a few factors. If your workload is mission critical, it is best to use immutable infrastructure. Likewise, if you have a very mature, [deployment stamp](/azure/architecture/patterns/deployment-stamp)-based infrastructure design, using immutable infrastructure can make sense, as you can deploy application code and new infrastructure reliably. Conversely, using mutable infrastructure can be a better choice if your safe deployment practices (link to OpEx SDP guide) dictate that rolling forward with deployments when mitigatable deployment issues arise is the preferred option. In this case, you would likely be updating the infrastructure in place.

## Azure facilitation

[Azure Resource Manager templates (ARM templates)](/azure/azure-resource-manager/templates/overview) and [Bicep](/azure/azure-resource-manager/bicep/overview) are Azure-native tools to deploy infrastructure through declarative syntax. ARM templates are written in JSON, whereas Bicep is a domain-specific language. Both can easily be integrated into [Azure DevOps Pipelines](/azure/azure-resource-manager/bicep/add-template-to-azure-pipelines) or [GitHub Actions](/devops/deliver/iac-github-actions) CI/CD pipelines.

[Terraform](/azure/developer/terraform/overview) is another declarative IaC tool that is fully supported in Azure. It can be used to deploy and manage infrastructure, and can be integrated into your CI/CD pipeline.

Discover misconfigurations in IaC with [Microsoft Defender for Cloud](/azure/defender-for-cloud/iac-vulnerabilities).

## Tradeoffs

_[Expand on the tradeoff mentioned in the checklist.]_

## Example

See the [Azure Virtual Desktop Landing Zone Accelerator](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone) architecture and the associated [reference implementation](https://github.com/Azure/avdaccelerator/) for an example of an Azure Virtual Desktop implementation that can be deployed with ARM, Bicep or Terraform using the files included in the implementation. 

## Related links

[Enterprise infrastructure as code using Bicep and Azure Container Registry - Azure Architecture Center | Microsoft Learn](/azure/architecture/guide/azure-resource-manager/advanced-templates/enterprise-infrastructure-bicep-container-registry)  

[What is infrastructure as code (IaC)? - Azure DevOps | Microsoft Learn](/devops/deliver/what-is-infrastructure-as-code)

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 
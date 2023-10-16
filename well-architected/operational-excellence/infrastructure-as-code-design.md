---
title: Recommendations for using infrastructure as code
description: Learn how to use infrastructure as code (IaC) as the standard for all of your infrastructure deployments.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for using infrastructure as code

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:05](checklist.md)| Prepare resources and their configurations by using a standardized infrastructure as code (IaC) approach. Like other code, design IaC with consistent styles, appropriate modularization, and quality assurance. Prefer a declarative approach when possible.| 
|---|---|

This guide describes the recommendations for using IaC as the standard for your infrastructure deployments. Using IaC enables you to integrate your infrastructure deployments and management into your existing software development practices. It provides a consistent, standard methodology for development and deployment for all components of your workload. Relying on manual deployments puts your workload at risk of inconsistent configurations and potentially insecure design.

**Definitions**

| Term | Definition |
|---|---|
|Declarative tools | A category of tools that define the end-state of a deployment and rely on the system to determine how to deploy the resources to match the defined end-state. |
|Immutable infrastructure | An infrastructure that's intended to be replaced with new infrastructure that runs the new configuration with each deployment. It must not be changed in place. |
|Imperative tools | A category of tools that list the execution steps that result in the desired end-state. |
|Module | A unit of abstraction for dividing groups of resources to simplify complex deployments. |
|Mutable infrastructure | An infrastructure that's intended to be changed in place. Deployments change the configuration of the infrastructure rather than replacing it with new infrastructure. |

## Key design strategies

As discussed in the [supply chain](workload-supply-chain.md) and [standardizing tools and processes](tools-processes.md) guides, you should have a strict policy of  deploying infrastructure changes (including configuration changes) only through code. You should deploy IaC through your continuous integration and continuous delivery (CI/CD) pipelines. Adopting these policies enforces consistency in processes for all IaC deployments, minimizes the risk of configuration drift across your environments, and ensures infrastructure consistency across your environments. Additionally, you should standardize your IaC development and deployment tools and processes in a style guide. Recommendations for your style guide include:

**Prefer declarative over imperative tools.** Declarative tools and their associated files are a better overall choice for deploying and managing IaC than imperative tools. Declarative tools use a simpler syntax for their definition files, defining only the desired state of the environment after the deployment finishes. Imperative tools depend on you defining the steps required to get to the desired end-state, so the files can be much more complex than declarative files. Declarative definition files also help reduce the technical debt of maintaining imperative code, like deployment scripts, that can accrue over time.

**Use your cloud platform's native tools and other industry-proven tools that natively integrate into the platform.** Your cloud platform provides tools to make deploying IaC easy and straightforward. Take advantage of these tools and other third-party tools that have native integration, like Terraform, rather than developing your own solutions. Native tools are supported by the platform and include  built-in functionality for most of your needs. They're continuously updated by the platform provider, making them more useful as the platform evolves.

**Use the right tools for specific tasks and infrastructure types.** Multiple tasks, beyond deployments, are involved in an infrastructure lifecycle. Configuration needs to be applied and maintained, for example, and the tool you use to script deployments, like Bicep, might not be the best tool for every management operation. 

Likewise, applying desired state configuration (DSC) for different infrastructure types might require different tools. For example, there are specific tools like Ansible for managing DSC for VMs, whereas Flux is a good tool for managing DSC on Kubernetes clusters. Platform as a service (PaaS) services might provide different tools for configuration management (like Azure App Configuration) that can be handled through IaC. Software as a service (SaaS) services might be more limited because they're more tightly controlled by the platform. 

Think about all the tasks and types of infrastructure that are in scope for your IaC practices and standardize on tools that do the jobs that you need them to do and can be integrated into your development and management practices.

**Your scripts and templates should be flexible enough to easily deploy a variety of environments.** Use parameters, variables, and configuration files to deploy a standard set of resources that can be modified to deploy any environment in your code promotion stack. Abstract settings like resource size, count, name, locations to deploy into, and some configuration settings. Be careful not to abstract too much, however. There are settings that can be abstracted with a parameter or variable that might not actually change over the course of the workload lifecycle, or that might change rarely. They shouldn't be abstracted.

> [!NOTE]
> Avoid using different IaC assets for different environments. You shouldn't have different Terraform files for production and test environments, for example. All environments should use one file. You can manipulate that file to deploy into different environments as needed.

**Strategize and standardize on the use of modules.** Like parameters and variables, [modules](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code) can make your infrastructure deployments repeatable. Be thoughtful, however, about how you use them. A standardized abstraction strategy helps ensure that modules are built to meet specific, agreed-upon goals. Use modules to encapsulate complex configurations or combinations of resources. Avoid modules if you're using only the default configuration of the resource. Additionally, be judicious in developing new modules. Use maintained open-source modules when appropriate, in, for example, nonsensitive scenarios.

**Document standards for manual steps.** There might be steps related to deploying and maintaining infrastructure that are particular to your environment and that require manual intervention. Ensure that these steps are minimized as much as possible and clearly documented. In your style guide and standard operating procedures, standardize manual steps to ensure that tasks are performed safely and consistently.

**Document standards to handle orphaned resources.** Depending on the tools you use for configuration management and their limitations, there might be times when a particular resource is no longer needed by your workload and your IaC tools can't automatically remove the resource. For example, say you're moving from VMs to a PaaS service for some function, and the IaC tooling doesn't have logic to remove the retired resources. Those resources can become orphaned if the workload team doesn't remember to manually delete them. To handle these scenarios, standardize a strategy to scan for orphaned resources and delete them. You also need to consider how to ensure that your templates are up to date. Research the limitations of your IaC tooling to understand what you might need to plan for in these situations.

### Other IaC strategies

Consider the following recommendations that apply to using IaC for your workload:

**Use a layered approach to align your IaC pipelines within the workload stack**. Separating your IaC pipelines into layers helps you manage complex environments. Deploying dozens or hundreds of resources as a monolithic package is inefficient and can introduce multiple issues, like broken dependencies. The use of multiple pipelines that are aligned with layers composed of resources whose deployment lifecycles or factors like functionality closely match makes managing IaC deployments easier. 

Core infrastructure like networking resources rarely need changes more complex than configuration updates, so those resources should make up a *low-touch* IaC pipeline. You might have one or more *medium-touch* and *high-touch* IaC pipelines for resources, depending on the complexity of your workload. Using a Kubernetes-based application stack as an example, one medium-touch layer might be composed of the clusters, storage resources, and database services. High-touch layers would be composed of the application containers that are updated very frequently in a continuous delivery mode.

**Treat your IaC and application code the same.** Treating your IaC artifacts the same as your application code artifacts helps you apply the same rigor for managing code across all pipelines. Moreover, IaC development and deployment practices should mirror application practices. Standards for version control, branching, code promotion, and quality should all be identical. Also consider collocating your IaC assets together with your application code assets. Doing so helps ensure that the same processes are followed with every deployment and helps you avoid issues like inadvertently deploying infrastructure before the necessary application code, or vice versa.

**Collaborate with other teams in your organization for standardization and reusability.** Large organizations can sometimes have multiple teams developing and supporting workloads. Collaboration across teams to agree on standards helps you reuse libraries, templates, and modules to gain efficiency and consistency across workload environments. Likewise, IaC tools should be standardized across the organization to the extent that doing so is practical.

**Apply the principle of "security as code" to ensure that security is part of the deployment pipeline.** Include vulnerability scanning and configuration hardening as part of the IaC development process. Scan your IaC repos for keys and secrets that are exposed. One advantage of using IaC is that security-focused team members can review code before deployment to ensure that the configuration that's approved for release by security is actually what's deployed to production. For detailed guidance, see [Recommendations for securing a development lifecycle](../security/secure-development-lifecycle.md).

**Test routine and non-routine activities.** Test deployments, configuration updates, and recovery processes, including deployment-rollback processes. 

### Mutable vs. immutable infrastructure

The choice between deploying mutable versus immutable infrastructure depends on a few factors. If your workload is business critical, it's best to use immutable infrastructure. Likewise, if you have a mature infrastructure design that's based on [deployment stamps](/azure/architecture/patterns/deployment-stamp), using immutable infrastructure can make sense, because you can deploy application code and new infrastructure reliably. Conversely, using mutable infrastructure can be a better choice if your [safe deployment practices](safe-deployments.md) dictate that rolling forward with deployments when mitigable deployment issues arise is the preferred option. In this case, you would probably update the infrastructure in place.

## Azure facilitation

[Azure Resource Manager templates (ARM templates)](/azure/azure-resource-manager/templates/overview) and [Bicep](/azure/azure-resource-manager/bicep/overview) are Azure-native tools for deploying infrastructure by using declarative syntax. ARM templates are written in JSON, whereas Bicep is a domain-specific language. Both can easily be integrated into [Azure Pipelines](/azure/azure-resource-manager/bicep/add-template-to-azure-pipelines) or [GitHub Actions](/devops/deliver/iac-github-actions) CI/CD pipelines.

[Terraform](/azure/developer/terraform/overview) is another declarative IaC tool that's fully supported in Azure. It can be used to deploy and manage infrastructure, and can be integrated into your CI/CD pipeline.

You can use Microsoft Defender for Cloud to [discover misconfigurations in IaC](/azure/defender-for-cloud/iac-vulnerabilities).

## Tradeoffs

**Increased specialization:** In some cases, introducing new languages in your workload team comes with a learning curve, and vendor lock-in can make it a poor choice. Training your team members and analyzing the right tool based on your cloud providers' tooling support is required.

**Increased maintenance effort:** Code base and tooling maintenance are required to keep your IaC implementation current and secure. Properly track your technical debt and foster a culture where reducing debt is rewarded.

**Increased time for configuration changes:** Deploying infrastructure by using command-line instructions or directly from a portal requires no coding time and/or testing artifacts. Minimize deployment time by following recommended practices like code reviews and quality assurance practices.

**Increased complexity of modularization:** Using more modules and parameterization increases the time it takes to debug and document the system and adds a layer of abstraction. Balance the use of modularization to reduce complexity and avoid over-engineering. 

## Example

See the [Azure Virtual Desktop landing zone accelerator](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone) architecture and the associated [reference implementation](https://github.com/Azure/avdaccelerator/) for an example of a Virtual Desktop implementation that can be deployed via provided Resource Manager, Bicep, or Terraform files. 

## Related links

- [Infrastructure as Code](/azure/cloud-adoption-framework/ready/considerations/infrastructure-as-code)
- [What is infrastructure as code (IaC)?](/devops/deliver/what-is-infrastructure-as-code)
- [Enterprise infrastructure as code using Bicep and Azure Container Registry](/azure/architecture/guide/azure-resource-manager/advanced-templates/enterprise-infrastructure-bicep-container-registry) 
- [Discover misconfigurations in IaC](/azure/defender-for-cloud/iac-vulnerabilities)
- [Recommendations for designing a workload development supply chain](workload-supply-chain.md)
- [Recommendations for standardizing tools and processes](tools-processes.md)
- [Recommendations for securing a development lifecycle](../security/secure-development-lifecycle.md)
- [Recommendations for using safe deployment practices](safe-deployments.md) 
- [Deployment Stamps pattern](/azure/architecture/patterns/deployment-stamp)
- [Azure Resource Manager templates (ARM templates)](/azure/azure-resource-manager/templates/overview)
- [Bicep](/azure/azure-resource-manager/bicep/overview)
- [Azure Pipelines](/azure/azure-resource-manager/bicep/add-template-to-azure-pipelines)
- [GitHub Actions](/devops/deliver/iac-github-actions) 
- [Terraform](/azure/developer/terraform/overview)
- [Azure Virtual Desktop landing zone accelerator](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone)
- [Azure Virtual Desktop landing zone accelerator implementation](https://github.com/Azure/avdaccelerator/)

## Next steps

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 

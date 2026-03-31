---
title: Architecture strategies for enabling and implementing automation in a workload
description: Learn how to design your workload to enable automation that eliminates repetitive, error-prone tasks. Automation simplifies maintenance tasks, allowing you to update, patch, and upgrade your systems more efficiently and reliably.
author: simipaul
ms.author: simipaul
ms.date: 02/11/2026
ms.topic: concept-article
---

# Architecture strategies for enabling and implementing automation

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|**OE:10**| Design automation to be reliable, secure, and maintainable across the workload lifecycle. Automate repetitive, procedural tasks that deliver clear return on investment.|
|---|---|

Design your workload with automation in mind to ensure routine tasks like provisioning, scaling, deployments, and maintenance are performed quickly, reliably, and consistently. Automating repetitive, error-prone tasks enables teams to work more efficiently while reducing human error.

Actively look for automation opportunities to reduce management overhead and improve reliability. Evaluate each opportunity based on organizational value. Prioritize straightforward, procedural tasks that are expected to remain relevant over time.

Automation doesn't need to be all or nothing. Some workstreams require human judgment at key decision points but can still benefit from automation elsewhere. Thoughtfully apply automation to empower teams, streamline operations, and build resilient workloads.

## Design workload components to support automation

Design your workload to support automation from ideation through ongoing improvement. Consider how you want to apply automation to ensure necessary pieces are in place. Use the Well-Architected Framework pillars to plan automation types across security, reliability, performance, operations, and cost control.

Design with automation in mind to minimize refactoring. Choose tools based on workload requirements, team familiarity, and cloud platform compatibility. Some tools integrate well with Azure CLI, while others require REST interfaces. Investigate platform-provided tools to ensure compatibility and functionality. 

Examples of proactive automation planning:

- *Deployment*: Plan for deployment standards, team training, and the required infrastructure upfront so that automated application and infrastructure deployments consistently meet predictable standards.

- *Validation*: Automatically validate compliance requirements by using orchestration or policy tools. Identify and implement the appropriate validation tools and supporting systems.

- *Automatic scaling*: Use automatic scaling to meet reliability and performance requirements. Plan and allocate IP address space and subnets ahead of time for scaling, redundancy, and growth.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Balance control versus efficiency when enabling automation. Your workload might not be mature enough for certain automation, or you might need flexibility that automation doesn't provide. Consider your team's skill set. If automation requires tools that your team isn't equipped to support, use a less comprehensive design as an intermediate step.

## Evaluate tasks to automate

Consider the following recommendations to ensure that you prioritize tasks that benefit the most from automation:

- **Aim for easy wins.** Focus on tasks that are highly procedural and susceptible to human error. These tasks are highly automatable. They're clearly defined, low in complexity, and performed as part of normal operations. Conversely, don't prioritize automating tasks that require complex scripting to handle variability, or tasks that rarely occur.

  Examples of highly automatable tasks include rebooting servers, creating accounts, and transferring logs to a data store. These tasks might occur on a schedule, as a response to an event or monitoring alert, or as needed based on external factors.

- **Look for ways to empower operators and free up your SMEs.** Avoid unnecessary escalations. For example, database administrators might routinely get requests to create new databases when onboarding multitenant customers. A self-service portal enables help desk teams to safely create databases themselves. Or as an intermediary step, you can automate the requests and steps through scripts for the SME.

- **Focus on your return on investment.** High-value automation requires minimal management overhead and adds a demonstrable degree of efficiency. For example, if you save your operations team an hour each day by automating database entries, they get time to find other areas for improvement.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Complex automation that requires a high degree of maintenance can be difficult for operations teams to manage and troubleshoot. Keep automated tasks focused on only performing discrete jobs. Try to minimize dependencies on other tools or components.

### Areas to implement automation

Adopt automation throughout your entire workload lifecycle, from development to day-to-day management. Use the following list of examples to help you consider the broad areas of your workload lifecycle that can benefit from automation. You can automate:

- **Pipeline definition, execution, and management**: Use continuous integration and continuous delivery (CI/CD) tools like Azure DevOps to automatically define pipelines and automate build, test, deployment, and reporting tasks. Use predefined rules to automate approval decisions for workflows and tightly scope and test your automated approvals to reduce risk especially in production.

- **Deployments**: Use tools like Azure Resource Manager templates, Bicep, Terraform, and Ansible to automate your workload development and release processes. Deploy and update your infrastructure by using the same automation platforms with an infrastructure as code (IaC) approach.

- **Testing**: Many tools are available for automating your testing processes. These tools can relieve a significant burden from your quality assurance team and ensure that tests are standardized and reliable.

- **Scaling and self-healing**: Use platform-provided functionality to automatically scale your infrastructure when load increases or decreases. Trigger automated remediation actions from monitoring alerts to recover malfunctioning components or jobs. For more information, see [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md).

- **Monitoring and alerting**: Use tooling that's available in your monitoring solution to automatically enroll newly deployed resources and configure alert-triggered actions to help hasten remediation when problems arise. Take advantage of the automation functionality that your observability platform provides. Automatically enroll new devices to monitor and alert on anomalies.

- **Configuration management and other operational tasks**: Use orchestration and policy tooling to ensure that all of your resources run the same configuration and that compliance requirements are enforced across your workload. Automate repetitive tasks associated with onboarding new application users or employees, like database updates, credential creation or DNS updates.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Optimize low-risk production settings like cache sizes or timeout values as workloads evolve. Pair AI agents with telemetry so teams can safely adjust settings within human-defined limits, monitor results, and improve performance and reliability. Teams can build hybrid solutions that use predictive models trained on historical telemetry to forecast and adjust parameter values in real time.

## Choose an appropriate automation tool

Developing automation in-house is time intensive and introduces a management burden. Use off-the-shelf tools whenever possible. Between commercial, open source, and cloud platform tools, many options are available. Rely on your team's expertise to guide tool selection. Focus on solutions that match their skills without a steep learning curve. Prioritize tools that address specific automation tasks rather than acquiring general-purpose tools first.

Be mindful of factors that complicate operations, like version lock-in and plugin overuse. Plugins for tools like Jenkins or Azure DevOps add functionality and should be adopted when beneficial. However, using multiple plugins for a single task complicates updates and troubleshooting. Be judicious with plugins. Avoid solutions with framework version dependencies as they're a burden to maintain over time. Standardize your automation tools and plugins, and use source control for all automation projects.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Sometimes the efficiencies you gain from automation outweigh the management burden of developing your own solution. Be judicious. Narrowly focus on filling gaps that off-the-shelf solutions can't address, and minimize complexities like dependencies.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Deliver secure and reliable deployments faster by using AI to create consistent automation scripts. Tools like GitHub Copilot can generate CI/CD scripts, templatize configurations, and validate compliance. Advanced agent-based solutions can translate product requirements and standards directly into code. These solutions require accurate, up-to-date design documents, standards, and access to your codebase. Always review and test AI-generated code to ensure it meets your quality and security standards.

## Integrate automation into your workload

For any tool you use, make it easily accessible and manageable for operators. Provide easy-to-use interfaces and access to CI/CD pipelines, APIs, and libraries. Manage automation holistically like the workload it supports. Secure it to the same degree, monitor it, and subject it to the same testing protocols.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: As your automation environment grows, AI can make data-driven decisions about which automations deliver real value and proactively surface conflicts before they cause incidents. You boost productivity, reduce costs, and keep automation maintained instead of neglected.
>
> For high-value workloads, use large language models (LLMs) to analyze and correlate code, telemetry, and incident data. Detect patterns, measure effectiveness, track existing automations, and automatically discover new opportunities across your codebase.

## Revisit automation design during the lifecycle

After your workload is running, prioritize continuous improvement. Observe usage patterns and customer behavior to identify automation opportunities. Enhance existing automation or introduce new automation to improve customer experience. For example, if you have automated scaling enabled and experience a brief spike in workload, you can implement scale-in automation to reduce CPU usage once the demand falls below the specified threshold.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Identify and improve automations that are regularly bypassed or manually overridden. Integrate automation logs, IcM systems, and monitoring data with AI tools to find patterns that trigger exceptions or bypasses. Agent-based solutions grounded in your project data can evaluate the effectiveness of your automations and recommend updates or retirement.

The following sections of this guide offer recommendations on specific areas of automation that can help you in your workload design and implementation.

## Automate bootstrapping

Bootstrapping refers to the configuration updates to a resource that you must make after provisioning it, but before it's available as part of the workload pool. While often associated with virtual machines (VMs), many resources require setup during deployment, including platform as a service (PaaS) technologies and container hosting technologies like Azure Kubernetes Service (AKS).

Your cloud platform might provide bootstrapping solutions. For example, use VM extensions in Azure to make predefined configuration changes during deployment and customize changes by injecting PowerShell scripts.

> :::image type="icon" source="../_images/ai.svg"::: **AI opportunity**: Tools like Copilot or Claude Code can streamline repetitive configuration tasks like DSC setups, VM initialization, and environment variable management. Give these tools secure read-only access to analyze your code and design artifacts, then generate and validate configuration code in your IDE using quality prompts.
>
> For advanced automation, agent-based solutions can monitor production for drift, determine the intended state, and automatically update bootstrapping definitions. These solutions require significant engineering investment and ongoing human oversight when you integrate them with your existing tools.

## Incorporate automation into access management

Take automation into account when designing authentication and authorization strategy. Maintain the highest security level in production workloads, but understand that this security can affect automation. For example, biometric or multifactor authentication adds complexity. Use managed identities, workload identities, or certificates for automated authentication. Include secret and key management for increased authentication security.

## Design variability into your workload

Avoid unnecessarily deploying new infrastructure for small changes by building flexibility into your artifacts. For example, rather than redeploying infrastructure when a feature flag changes, use parameters to update components like app configurations. Clearly define and document how variability is used to avoid overuse and configuration drift.

## Build a control plane

A control plane is the back-end system or suite of tools for managing the application and its dependencies through a unified interface. Build your control plane as a REST interface, CLI, or webhook to support automation by external tools.

Expose maintenance operations through the control plane to coordinate workload components. For example, orderly backup and restore, bootstrapping, configuration, import/export, and batching operations. Choose the right granularity level when deciding operations to expose through the control plane.

## Adopt a data-driven approach to develop automation

Develop a monitoring strategy to capture metrics that drive automation. Use structured logging and custom metrics to provide information in formats that automation tools can easily recognize. Pair captured metrics with thresholds defined in the monitoring system that trigger alerts and automated actions, like notifications or self-healing mechanisms. For more information, see [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md).

## Automate user lifecycle events

Design your application and infrastructure to allow for automated user onboarding and offboarding for individuals or multitenant customers. Plan for automated database updates through scripts, infrastructure provisioning and deprovisioning, and credential and secret management.

## Automate Desired State Configuration

Automate Desired State Configuration (DSC) in your resources to ensure they meet compliance and business requirements. DSC automation quickly catches and remediates configuration drift. You can automate DSC by using orchestration tools or policy management tools.

Choose between orchestration tools or policy management tools:

- **Orchestration tools** like Azure DevOps services or Jenkins, don't have built-in capabilities to proactively poll your workload for configuration drift. Integrate them into your CI/CD pipeline to maintain a standard for IaC deployment and management. An advantage of using orchestration tools is that resources are always fully configured when deployed.

- **Policy management tools** allow you to define policies that affect one or more groups of resources. These policies are enforced when the resource checks in with the policy management system. An advantage of using policy management is that these systems aren't code driven, making them easier for operators to adopt.

When deciding between orchestration or policy tools, consider whether configuration updates must be made at deployment time, if defining updates in code fits your operational practices, and how many resource types you plan to deploy. Policy tools might be easier for managing many configurations across resource types.

## Azure facilitation
Azure offers many tools to help you automate tasks for your workload.

**IaC tools**: Use Terraform, Bicep, and Azure Resource Manager for IaC deployments based on your requirements and team familiarity.

**Azure Functions**: [Azure Functions](/azure/azure-functions/functions-overview) is a serverless tool for automating tasks in your preferred development language. Functions provides event-driven triggers and bindings that connect to other services without extra code.

**GitHub Actions for Azure**: Use [GitHub Actions for Azure](https://azure.github.io/actions/#automate) to automate CI/CD processes. Create workflows that build and test every pull request or deploy merged pull requests to production. GitHub Actions goes beyond DevOps. It enables you to run workflows when other events occur in your repository. For example, a workflow to automatically add appropriate labels when a new issue is created in your repository.

**Azure Automation**: PowerShell and Python are popular for automating operational tasks like restarting services, transferring logs, and scaling infrastructure. Alone, these languages lack native platforms for centralized management, version control, run history, or event-driven triggers. [Automation](/azure/automation/overview) provides an Azure-hosted platform for running PowerShell and Python code across cloud and on-premises environments. PowerShell and Python code is stored in Automation runbooks that can:

- Trigger runbooks on demand, on schedule, or through webhooks
- Provide run history and logging
- Integrate secrets store and source control

**Azure Update Manager**: [Update Manager](/azure/update-manager/overview) is a unified service to manage and govern updates for virtual machines. Monitor Windows and Linux update compliance, make real-time updates, or schedule them within maintenance windows. Use Update Manager to:

**Network infrastructure automation**: Azure provides automated management capabilities for networking services that reduce operational overhead. Here are some examples:

- **Azure Firewall customer-controlled maintenance**: Configure [predictable maintenance schedules](/azure/firewall/customer-controlled-maintenance) with minimum 5-hour windows and daily recurrence. Plan maintenance around business requirements and low-traffic periods to ensure firewall infrastructure receives necessary security updates without unexpected downtime.

- **Azure Firewall FQDN filtering in DNAT rules**: Use [domain name-based routing](/azure/firewall/firewall-faq#how-can-i-use-fqdn-filtering-in-dnat-rules) instead of static IP addresses in destination NAT configurations. This approach reduces operational overhead when backend infrastructure changes IP addresses due to scaling operations, deployments, or infrastructure updates.

- **Azure Front Door managed certificates**: Implement [automatic certificate provisioning and renewal](/azure/frontdoor/front-door-custom-domain-https) for wildcard domains. This automation supports multiple subdomains under a single certificate, simplifying SSL certificate management for organizations with complex domain structures and reducing manual certificate lifecycle management tasks.

- Oversee compliance on your entire fleet
- Schedule recurring updates and deploy critical updates

**Azure Deployment Environments**: [Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments) enables development teams to quickly create consistent app infrastructure using project-based templates that minimize setup time and maximize security, compliance, and cost efficiency. A deployment environment is Azure resources deployed in predefined subscriptions. Infrastructure administrators enforce enterprise security policies and provide curated IaC templates.

Administrators define deployment environments as catalog items hosted in a GitHub or Azure DevOps repository (*catalog*). Each catalog item consists of an IaC template and a manifest.yaml file. You can script the creation and programmatic management of deployment environments.

**Azure Logic Apps and Microsoft Power Automate**: When building custom digital process automation (DPA) for tasks like approval flows or ChatOps integrations, consider [Logic Apps](/azure/logic-apps/) or [Power Automate](https://azure.microsoft.com/products/power-automate/). Construct workflows from built-in connectors and templates. Both are built on the same underlying technology and well-suited for trigger-based or time-based tasks.

**Automatic scaling**: Many Azure technologies have built-in automatic scaling capabilities. You can also program other services to automatically scale by using APIs. For more information, see [Recommendations for designing a reliable scaling strategy](../reliability/scaling.md).

**Azure Monitor action groups**: To automatically run self-healing operations when alerts trigger, use [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups). Define operations using runbooks, Azure functions, or webhooks.

**Example**

For an example of using Automation in tandem with other Azure services, see [Ops automation by using Azure Event Grid](/azure/architecture/solution-ideas/articles/ops-automation-using-event-grid). This example uses Logic Apps and Event Grid to automate operational tasks.

### Policy management

**Azure Policy**: By using [Azure Policy](/azure/governance/policy/overview), you can enforce standards and assess compliance at scale. Azure Policy provides an aggregated compliance dashboard view and granular resource and policy evaluation. It can automatically remediate new resources or remediate existing resources in bulk.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Offloading automation from your CI/CD pipeline to platform tools like Azure Policy can simplify your pipeline but adds management burden of using multiple systems. Execution failures in platform services don't appear in pipeline logs and must be intelligently fed into your observability platform.

### Bootstrap automation

**Azure Virtual Machines extensions:** VM extensions are small packages that run post-deployment configuration and automation on VMs. Several extensions are available for tasks like running scripts, configuring anti-malware, and configuring logging. You can install and run extensions by using Azure Resource Manager templates, Azure CLI, Azure PowerShell, or the Azure portal. Each VM has a VM agent that manages extension lifecycle.

VM extensions typically use custom script extensions to install software, run commands, and configure VMs or Virtual Machine Scale Sets. Set up extensions to run as part of IaC deployments via the Azure VM Agent, or run them outside deployments by using Azure CLI, PowerShell, or the portal.

**Cloud-init:** Cloud-init is an industry tool for configuring Linux VMs on first boot. Like Azure custom script extensions, cloud-init installs packages and runs commands on Linux VMs. Use cloud-init for software installation, system configuration, and content staging. Azure includes many cloud-init-enabled VM images across Linux distributions. For a full list, see [cloud-init support for VMs in Azure](/azure/virtual-machines/linux/using-cloud-init).

**Azure deployment script resource:** When deploying with Azure, you might need to run arbitrary code for bootstrapping user accounts, Kubernetes pods, or querying non-Azure systems. Since these operations aren't accessible through the Azure control plane, a separate mechanism is required. See [Microsoft.Resources deploymentScripts](/azure/templates/microsoft.resources/deploymentscripts?pivots=deployment-language-bicep). Like any Azure resource, the deployment script resource:

- Can be used in Azure Resource Manager templates.
- Contains Azure Resource Manager templates dependencies in other resources.
- Consumes input and produces output.
- Uses user-assigned managed identities for authentication.

When deployed, the deployment script runs PowerShell or Azure CLI commands and scripts. You can observe script runs and logging in the Azure portal or by using Azure CLI and PowerShell. Customize run environment variables, timeout options, and resource management after script failure.

**Bootstrap AKS clusters with GitOps**: Bootstrap newly provisioned AKS clusters by using GitOps and the Flux v2 cluster extension by declaring configuration settings in GitHub repositories. AKS cluster files stored in GitHub are versioned, and changes are easily tracked. Kubernetes controllers run in clusters and continually reconcile cluster state with desired state declared in the Git repository. For more information, see [AKS baseline reference architecture](/azure/architecture/reference-architectures/containers/aks/baseline-aks#cluster-bootstrapping).

### Configuration management

[Azure Automation State Configuration](/azure/automation/automation-dsc-overview) is a DSC management tool managed by Azure Policy [guest configuration feature](/azure/governance/machine-configuration/overview) for writing, managing, and compiling PowerShell DSC configurations for nodes in any cloud or on-premises datacenter. Import DSC resources and assign configurations to target nodes.

[Azure App Configuration](/azure/azure-app-configuration/overview) centrally manages application settings and feature flags. It works with Azure Key Vault to securely manage application configurations across your environment.

### Change tracking and inventory

[Change tracking and inventory using Azure Monitoring Agent](/azure/automation/change-tracking/overview-monitoring-agent) tracks OS configuration drift in Azure VMs and Arc-enabled VMs. This solution automates detection of drift, the inventory running services, and installed packages on the virtual machines in your workload. Items tracked include:

- Installed Windows and Linux software
- Key Windows and Linux files
- Windows registry keys
- Windows services and Linux daemons


## Related links

- [Automation](/azure/automation/overview)
- [Azure Update Manager](/azure/update-manager/overview)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups)
- [Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments)
- [GitHub Actions for Azure](https://azure.github.io/actions/#automate)
- [Logic Apps](/azure/logic-apps/)
- [Ops automation by using Event Grid](/azure/architecture/solution-ideas/articles/ops-automation-using-event-grid)
- [Power Automate](https://azure.microsoft.com/products/power-automate/)
- [AKS baseline reference architecture](/azure/architecture/reference-architectures/containers/aks/baseline-aks#cluster-bootstrapping)
- [Azure App Configuration](/azure/azure-app-configuration/overview)
- [Azure Automation State Configuration](/azure/automation/automation-dsc-overview)
- [Azure Policy](/azure/governance/policy/overview)
- [Cloud-init support for VMs in Azure](/azure/virtual-machines/linux/using-cloud-init)
- [GitOps Flux v2 configurations with AKS and Azure Arc-enabled Kubernetes](/azure/azure-arc/kubernetes/conceptual-gitops-flux2)
- [Microsoft.Resources deploymentScripts](/azure/templates/microsoft.resources/deploymentscripts?pivots=deployment-language-bicep)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)
- [Recommendations for designing a reliable scaling strategy](../reliability/scaling.md)
- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

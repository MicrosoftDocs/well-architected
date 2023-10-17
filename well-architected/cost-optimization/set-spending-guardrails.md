---
title: Recommendations for setting spending guardrails
description: Learn best practices for setting spending guardrails.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for setting spending guardrails

**Applies to this Azure Well-Architected Framework Cost Optimization checklist recommendation:**

|[CO:04](checklist.md)| Set spending guardrails. Guardrails should include release gates, governance policies, resource limits, and access controls. Prioritize platform automation over manual processes.|
|---|---|

This guide describes the recommendations for setting spending guardrails. Set spending guardrails by implementing measures to control and manage your costs within a specified budget. Spending guardrails can help prevent unexpected or excessive spending. Spending guardrails can help you ensure that your cloud resources are utilized efficiently and cost-effectively. Without spending guardrails, your workload costs might exceed your budget, leading to unplanned expenses that can strain your financial resources.

**Definitions**

| Term | Definition |
|---|----|
| Governance | A set of policies, processes, and controls that help ensure that the workload is managed effectively, securely, and in compliance with organizational and regulatory requirements.   |
| Governance policies | A set of rules that enforce compliance and enable auditing of workload resources. |
| Infrastructure as code (IaC) | A descriptive model for defining and deploying infrastructure, including networks, virtual machines, load balancers, and connection topologies. |
| Release gate  | A condition or checkpoint in a release pipeline that must be satisfied before the deployment can proceed. A release gate helps to ensure that specific criteria are met before software is released. |

## Key design strategies

Implement key strategies, such as governance policies, access controls, release gates, budget thresholds, alerts, and automation, to help you effectively manage and control your costs. Combine these strategies to establish comprehensive spending guardrails that promote cost optimization and efficient resource management. These strategies can help you stay within budget, prevent cost overruns, and maximize the value of your cloud investments.

Prioritize platform automation over manual processes. Use automation tools and services provided by the cloud platform to streamline resource provisioning, configuration, and management. Automation reduces the risk of human error, improves efficiency, and enables consistent application of spending guardrails.

### Use governance policies

Governance policies provide spending guardrails on various aspects of resources, such as resource types, configurations, tags, location, and data management. Many cloud platforms have a service that automates the enforcement of governance policies. Use automation to control resource usage, enforce accountability, and eliminate spending on restricted resource types. Here are some of the policies you should consider enforcing:

- *Restricted resource types*: Policies can specify which types of resources are allowed or disallowed within an organization. For example, an organization might have a policy that restricts the use of certain expensive resource types to control costs.

- *Resource limits*: Set resource limits to controls costs and prevent overprovisioning. Include limits on the number of resources that can be provisioned, the size of resources, and the duration of resource usage in your policy. These limits can help you to prevent excessive spending and optimize resource utilization. For example, resource limits can minimize the effects of an unauthorized account breach related to crypto mining.

- *Defined resource configurations*: Policies can define specific configurations for resources. You can enforce settings on resources, such as automatic scaling and data archiving, that promote cost optimization.

- *Restricted locations*: You can use policies to restrict the deployment of resources to specific regions or locations. Consider restricting locations to avoid costly data transfer fees and comply with data sovereignty regulations.

- *Managed data*: Use policies to enforce data management practices that help optimize costs. For example, you can implement policies that require the use of lower-cost storage tiers for less frequently accessed data or policies that define expiration rules for data retention.

- *Enforced metadata*: Use policies to mandate the use of specific metadata on resources, which enables better tracking and cost allocation. You can also use metadata in your automation or manual review. For example, you can use metadata to automate resources backups by using a backup tag. Establish a consistent metadata policy to help align costs to spending guardrails.

- *Limited idle resources*: Use policies to identify idle resources so you can delete or repurpose them. Consider setting policies that automatically shut down instances during the hours they’re not in use.

> ![Risk icon](../_images/risk.svg) **Risk**: If you implement automatic scaling, set a maximum scaling threshold based on testing. Maximum thresholds can help you avoid massive scaling spikes that cause cost overruns, but a maximum that’s set too low might negatively affect performance. For more information, see [Recommendations for optimizing scaling](optimize-scaling.md).

### Configure access controls

Configure access controls to set restrictions to prevent overspending and to help ensure that only authorized individuals can consume resources. Access controls can help reduce the risk of accidental or unnecessary changes that negatively affect cost optimization. To implement access controls for cost optimization, follow these steps:

1. Identify the resources and services that need access controls to optimize costs.
1. Define access policies based on the principle of least-privilege access, granting users only the necessary permissions to perform their tasks. For example, some users might need only read access, while others might also require write or delete permissions.
1. Implement authentication methods, like username/password, multifactor authentication, or integration with identity providers, to help ensure that only authorized users can access resources.
1. Set up role-based access controls (RBAC) to assign roles and permissions to users based on their job responsibilities. Using RBAC can help you manage resource access effectively.
1. Regularly review and update access controls to ensure that they align with the changing needs of the organization. Remove unnecessary access permissions and adjust access levels as-needed.

### Use release gates

Release gates are checkpoints or conditions that must be met before a release or deployment can proceed. Use release gates to help ensure that the release is cost-effective and aligns with optimization goals. Release gates offer a structured approach to the identification and implementation of cost-saving measures. To implement release gates for workload cost optimization, consider the following steps:

1. Establish the conditions or criteria that must be met before resources are released or deployed. Include factors such as spending limits, resource utilization thresholds, or project milestones.
1. Incorporate the release gates into the deployment pipeline. You can use automation tools or custom scripts to ensure that resource deployments are subject to the defined criteria.
1. Continuously monitor spending and resource usage against the defined criteria. If the spending thresholds or other conditions are exceeded, the release gates should prevent further deployments until the issues are addressed.

### Configure cost alerts

It's important to set alerts for budgets, cost anomalies, and prepaid plan utilization to optimize costs. These alerts provide visibility into your cloud spending and enable proactive cost management. Manage who receives notifications on alerts and keep the recipient list up to date with current responsibilities and access. Some alerts you might create in order to optimize costs include:

- *Budget alerts*: Set alerts on budgets to track your spending against pre-defined thresholds. You can monitor your costs and receive notifications when you approach or exceed the budgeted amount by creating a monthly budget, billing account, or resource group. Budget alerts help you to stay informed on your spending and take preventative actions to control costs.
- *Cost anomaly alerts*: Anomaly alerts notify you about unexpected cost variations that might indicate inefficiencies or abnormal spending patterns. You can configure these alerts to identify anomalies in the actual or forecasted costs. Use cost anomaly alerts to investigate the underlying cause of a cost variation and take corrective actions when necessary.
- *Prepaid plan utilization alerts*: If you have prepaid plans or commitments in place, alerts on plan utilization can help you effectively manage and maximize the value of these commitments. Use prepaid plan utilization alerts to monitor and optimize the usage of your prepaid resources and ensure that the benefits of your prepayment are used. You can also configure these alerts to notify stakeholders if the utilization of prepaid resources drops below a desired threshold.

### Use IaC

Infrastructure as code is the practice of managing and provisioning infrastructure resources using code, typically in the form of configuration files. Use this strategy to define and automate the deployment and configuration of infrastructure resources, such as virtual machines, networks, and storage, using code-based templates.

IaC strategies provide a structured and repeatable approach to managing and controlling infrastructure resources. IaC can help you to provision resources as-needed, delete resources without running them continuously, and optimize costs by ensuring you provision and configure resources according to predefined rules. Follow these steps to use IaC for cost optimization:

1. Create a code-based template language to define your infrastructure resources and their configurations. These templates let you specify the desired state of your infrastructure resources in a declarative manner. Implement best practices for cost optimization in your infrastructure code. Consider right-sizing your resources by using reserved instances or savings plans. Use cost-effective storage options and apply resource metadata for cost allocation and tracking.
1. Store your infrastructure templates in a version control system, such as Git, to track changes and manage different versions. You can use version control to maintain a history of your infrastructure configurations and foster collaboration among team members.
1. Use parameters in your templates to make them reusable and configurable. By using parameters, you can easily customize your infrastructure deployments for different environments or scenarios.
1. Use ephemeral environments for development, testing, and staging purposes to optimize costs. Ephemeral environments should only be run when necessary. Create these environments by using IaC tools and delete the environment when you're finished.
1. Use IaC tools and frameworks to automate the deployment and configuration of your infrastructure resources. Use automation to consistently and reliably provision resources according to your defined policies.
1. Regularly monitor your infrastructure resources and their costs to ensure compliance with your spending policies. Use monitoring and alerting tools to identify any deviations from the defined guardrails and take corrective actions as-needed. Check for unused resources and delete them, preferably with automation.

### Azure facilitation

**Governance policies.** Use [Azure Policy](/azure/governance/policy/overview) to define and enforce governance policies that align with your cost optimization goals. You can use Azure Policy to set rules on management groups, subscriptions, and resource groups. These policies can regulate resource provisioning, usage limits, and cost allocation. Use policies to promote rightsizing of resources, identify and eliminate idle or underutilized resources, and encourage the use of cost-effective services and architectures. Azure allows you to set limits or quotas to prevent unexpected costs. You can define limits on the number of resources that can be provisioned as well as the size and duration of resource usage. Set these limits to help prevent overprovisioning and to control costs.

**Identify underused or idle resources.** Use [Azure Advisor](/azure/advisor/advisor-cost-recommendations) to optimize and reduce your overall Azure costs by identifying idle and under-utilized resources. Receive cost recommendations from the *cost* section in the *advisor* dashboard.

**Add resource metadata.** Use Azure governance to implement resource tagging and categorization. Tag resources using relevant metadata to track and allocate costs to different departments, projects, or cost centers. Visibility into cost attribution can help you identify areas of high spending, optimize resource allocation, and facilitate better cost management.

**Cost management.** Use Microsoft Cost Management to optimize costs and enforce spending guardrails. You can use cost management features to set budgets and alerts, visualize cost information by using tools like Power BI, and analyze cost patterns and performance bottlenecks.

**Create release gates.** Use Azure DevOps release management capabilities to define and enforce your release gates. You can set up manual or automated checkpoints to help ensure that specific criteria, such as security checks, compliance requirements, and cost thresholds, are met.

**Set access controls.** Use Azure RBAC to manage access to resources. You can use RBAC to grant permissions to users, groups, or applications based on their roles. Implement RBAC to help ensure that only authorized users have access to resources, reducing the risk of unauthorized resource usage and potential cost implications.

**Implement IaC.** You can use Azure tools and services to deploy and manage infrastructure resources using code. By using tools like Azure Resource Manager templates, Azure Bicep, and Azure DevOps, you can define and deploy your infrastructure resources in a declarative manner. Use Azure DevOps or other continuous integration and continuous delivery (CI/CD) tools to automate the deployment and closure of your ephemeral environments.

Consider using lower-cost resource SKUs for your ephemeral or nonproduction environments to optimize costs. Azure provides different pricing tiers for resources. Azure DevTest Labs pricing and Azure Reservations are cost-saving methods that you can explore for ephemeral environments.

Automated repositories, such as Azure Repos, provide version control capabilities for managing code and infrastructure configurations. Teams and developers can use automated repositories to collaborate, track changes, and maintain a history of their codebase.

Use pipelines, such as Azure Pipelines, to automate the build, test, and deployment processes. Teams can use pipelines to define a series of steps and actions that are run automatically whenever changes are made to the codebase. Automate these processes to reduce manual effort, ensure consistency, and accelerate the delivery of software.

[Azure Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments) empower development teams to quickly and easily create app infrastructure by using project-based templates that establish consistency and best practices while maximizing security. On-demand access to secure environments accelerates the stages of the software development lifecycle in a compliant and cost-efficient way.

The [Azure Developer CLI](/azure/developer/azure-developer-cli/overview) is an open-source tool that accelerates the time it takes for you to get your application from a local development environment to Azure. The Azure Developer CLI provides best practice, developer-friendly commands that map to key stages in your workflow, whether you’re working in the terminal, an integrated development environment (IDE), or CI/CD.
The [Azure Developer CLI](/azure/developer/azure-developer-cli/overview) (`azd`) is an open-source tool that accelerates the time it takes for you to get your application from local development environment to Azure. The Azure Developer CLI provides best practice, developer-friendly commands that map to key stages in your workflow, whether you’re working in the terminal, your editor or integrated development environment (IDE), or CI/CD (continuous integration/continuous deployment).

## Organizational alignment

Cloud Adoption Framework provides guidance for central teams on how to set up cost guardrails across the organization so workload teams can know what is possibly being provided by central teams.

For more information, see [Cost Management policy compliance processes](/azure/cloud-adoption-framework/govern/cost-management/compliance-processes) and [Develop cost governance policy statements](/azure/cloud-adoption-framework/govern/cost-management/#develop-governance-policy-statements). The organization is generally encouraged to [Adopt policy-driven guardrails](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance) for the implementation.

## Related links

- [Cost Management tools in Azure](/azure/cloud-adoption-framework/govern/cost-management/toolchain)
- [Create and manage budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets)
- [Assign access to Cost Management data](/azure/cost-management-billing/costs/assign-access-acm-data)
- [Monitor usage and spending with cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)
- [Identify anomalies and unexpected changes in cost](/azure/cost-management-billing/understand/analyze-unexpected-charges)

## Cost Optimization checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Cost Optimization checklist](checklist.md)

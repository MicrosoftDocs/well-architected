---
title: Recommendations for setting spending guardrails
description: Learn best practices for setting spending guardrails.  
author: stephen-sumner
ms.author: ssumner
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for setting spending guardrails: CO <##>

This guide describes the recommendations for setting spending guardrails. Setting spending guardrails means implementing measures to control and manage your costs within a specified budget. It helps prevent unexpected or excessive spending. By setting spending guardrails, you can establish controls and guidelines to ensure that your cloud resources are utilized efficiently and cost-effectively. Without guardrails, your workload costs may exceed your budget, leading to unplanned expenses that can strain your financial resources.

**Definitions**

 | Term | Definition |
|---|----|
| Governance | The set of policies, processes, and controls put in place to ensure that the workload is managed effectively, securely, and in compliance with organizational and regulatory requirements.   |
| Governance policies | A set of rules to enforce compliance and enable auditing of workload resources. |
| Release gate  | A condition or checkpoint in a release pipeline that must be satisfied before the deployment can proceed and helps ensure that specific criteria or requirements are met before releasing the software. |
| Infrastructure as code | The practice of managing and provisioning infrastructure resources through machine-readable configuration files and allows for consistent, repeatable, and automated deployment of infrastructure. |

## Key strategies

When it comes to setting spending guardrails, there are key strategies that can help you effectively manage and control your costs. Use governance policies, access controls, release gates, budget thresholds, alerts, and automation to optimize your spending. By combining these strategies, you can establish comprehensive spending guardrails that promote cost optimization and efficient resource management. These strategies help you stay within budget, prevent cost overruns, and maximize the value of your cloud investments.

Prioritize platform automation over manual processes. Use automation tools and services provided by the cloud platform to streamline resource provisioning, configuration, and management. Automation reduces the risk of human error, improves efficiency, and enables consistent application of spending guardrails.

### Use governance policies

Governance policies provide spending guardrails on various aspects of resources, such as resource types, configurations, tags, location, and data management. Many cloud platforms have a service that automates the enforcement of governance policies. Using automation allows you to control resource usage, enforce accountability, and eliminate spending on restricted resource types. Here are some of the policies you should consider enforcing.

- *Restrict resource types*: Policies can specify which types of resources are allowed or disallowed within an organization. For example, an organization may have a policy that restricts the use of certain expensive resource types to control costs.
- *Set resource limits*: Set resource limits to prevent overprovisioning and control costs. Include limits on the number of resources that you can provision, the size of resources, or the duration of resource usage. Setting these limits can prevent excessive spending and optimize resource utilization. For example, it helps limit the effects of unauthorized account breach for incidents relating to crypto mining.
- *Define resource configurations*: Policies can define specific configurations for resources. For instance, you can enforce settings on resources that promote cost optimization such as autoscaling and data archiving.

:::image type="icon" source="../_images/risk.svg"::: **Risk:** Set a maximum scaling threshold based on testing so that you avoid massive scaling spikes that cause cost overruns. You also don’t want to set a maximum that’s too low and negatively affects performance.

- *Restrict locations*: Policies can restrict the deployment of resources to specific regions or locations. It helps avoid costly data transfer fees or comply with data sovereignty regulations.
- *Manage data*: Policies can enforce data management practices that help optimize costs. For example, you can have policies that require the use of lower-cost storage tiers for less frequently accessed data or define expiration rules for data retention.
- *Enforce metadata*: Policies can mandate the use of specific resource metadata on resources, enabling better tracking and cost allocation. You can also use metadata in your automation or manual review. For example, you can use metadata to automate backups of resources with a backup tag. Having a consistent metadata policy in place helps align costs to spending guardrails.
- *Limit idle resources*: Use policies to identify idle resources so you can delete or repurpose. Consider setting policies that automatically shut down instances during hours they’re not in use.

### Configure access controls

Access controls help ensure only authorized individuals can consume resources and set restrictions to prevent changes that could lead to overspending. It also helps reduce the risk of accidental or unnecessary changes that affect cost optimization. To implement access controls for cost optimization, organizations can follow these steps:

- *Identify resources and services*: Determine the resources and services that need access controls to optimize costs.
- *Define access policies*: Define access policies based on the principle of least privilege, granting users only the necessary permissions to perform their tasks. For example, some users may only need read access, while others may require write or delete permissions.
- *Implement authentication mechanisms*: Implement authentication mechanisms such as username/password, multi-factor authentication, or integration with identity providers to ensure that only authorized users can access the resources.
- *Set up role-based access controls (RBAC)*: Use RBAC to assign roles and permissions to users based on their job responsibilities. It helps with managing resource access effectively.
- *Regularly review and update access controls*: Periodically review and update access controls to ensure that they align with the changing needs of the organization. Remove unnecessary access permissions and adjust access levels as required.

### Use release gates

Release gates refer to the checkpoints or conditions that you need to met before a release or deployment can proceed. These gates help ensure that the release is cost-effective and aligns with the optimization goals. It provides a structured approach to identify and implement cost-saving measures. To implement or execute guidance for workload cost optimization, consider the following steps:

- *Establish release criteria*: Establish the conditions or criteria that must be met before resources are released or deployed. Include factors such as spending limits, resource utilization thresholds, or project milestones.
- *Integrate with deployment pipeline*: Incorporate the release gates into the deployment pipeline, ensuring that resource deployments are subject to the defined criteria. You can use automation tools or custom scripts.
- *Monitor and enforce*: Continuously monitor spending and resource usage against the defined criteria. If the spending thresholds or other conditions are exceeded, the release gates should prevent further deployments until the issues are addressed.

### Setup budgets and alerts

Setting alerts on budgets, cost anomalies, and prepaid plan utilization is an important aspect of cost optimization. These alerts serve as spending guardrails by providing visibility into your cloud spending and enabling proactive cost management. You need to manage who receives notifications on alerts. Ensure you keep the recipients up to date and in line with current responsibilities and access.

- *Set budget alerts*: Setting alerts on budgets allows you to track your spending against predefined thresholds. By creating a monthly budget at the desired scope (billing account, resource group), you can monitor your costs and receive notifications when you approach or exceed the budgeted amount. The notifications help you stay informed about your spending and take preventative actions to control costs.
- *Create cost anomaly alerts*: Anomaly alerts notify you of unexpected cost variations that may indicate inefficiencies or abnormal spending patterns. You can configure these alerts to identify anomalies in actual or forecasted costs and notify stakeholders when triggered. By being aware of such anomalies, you can investigate the underlying causes and take corrective actions to optimize costs.
- *Set prepaid plan utilization alerts*: If you have prepaid plans or commitments in place, setting alerts on prepaid plan utilization can help you effectively manage and maximize the value of these commitments. You can configure alerts to notify stakeholders if the utilization of prepaid resources drops below a desired threshold. The alerts enable you to monitor and optimize the usage of your prepaid resources and ensures you utilize the benefits of your prepayment.

### Use infrastructure as code

Infrastructure as code refers to the practice of managing and provisioning infrastructure resources using code, typically in the form of configuration files. It allows you to define and automate the deployment and configuration of infrastructure resources, such as virtual machines, networks, and storage, using code-based templates.

Infrastructure provides a structured and repeatable approach to managing and controlling infrastructure resources. You can provision resources whenever needed and delete once the need is over without running it continuously. It helps optimize costs by ensuring you provision and configure resources according to predefined rules and best practices. To use IaC for cost optimization, you can follow these steps:

- *Create infrastructure templates*: Use a code-based template language to define your infrastructure resources and their configurations. These templates allow you to specify the desired state of your infrastructure resources in a declarative manner. Implement best practices for cost optimization in your infrastructure code. Consider right-sizing your resources and using reserved instances or savings plans. Use cost-effective storage options and apply resource metadata for cost allocation and tracking.
- *Version your templates*: Store your infrastructure templates in a version control system, such as Git, to track changes and manage different versions. It ensures you have a history of your infrastructure configurations and allows for collaboration among team members.
- *Use parameters:* Using parameters in your templates to make them reusable and configurable. They allow you to easily customize your infrastructure deployments for different environments or scenarios.
- *Use ephemeral environments*: Ephemeral environments are typically used for development, testing, and staging purposes, allowing you to optimize costs by only running these environments when necessary. Create these environments using IaC and delete the environment when done.
- *Automate deployment and configuration*: Use infrastructure as code tools and frameworks to automate the deployment and configuration of your infrastructure resources. It allows you to consistently and reliably provision resources according to your defined policies.
- *Continuously monitor:* Regularly monitor your infrastructure resources and their costs to ensure compliance with your spending policies. Use monitoring and alerting tools to identify any deviations from the defined guardrails and take corrective actions as needed. Check for unused resources and delete them, preferably with automation.

### Azure Facilitation

**Governance policies.** Azure Policy enables you to define and enforce governance policies that align with your cost optimization goals. You can use Azure Policy to set rules on management groups, subscriptions, and resource groups. These policies can control various aspects, including resource provisioning, usage limits, and cost allocation. You can promote rightsizing of resources, identify and eliminate idle or underutilized resources, and encourage the use of cost-effective services and architectures. Azure allows you to set limits or quotas to prevent unexpected costs. You can define limits on the number of resources that can be provisioned, the size of resources, or the duration of resource usage. By setting these limits, you can prevent overprovisioning and control costs.

**Identifying underused or idle resources.** [Azure Advisor](/azure/advisor/advisor-cost-recommendations) helps you optimize and reduce your overall Azure spend by identifying idle and underutilized resources. You can get cost recommendations from the Cost section in the Advisor dashboard.

**Adding resource metadata.** Azure governance enables the use of resource tagging and categorization. By tagging resources with relevant metadata, you can track and allocate costs to different departments, projects, or cost centers. This visibility into cost attribution helps identify areas of high spending, optimize resource allocation, and facilitate better cost management.

**Cost management.** Azure Cost Management provides features to help optimize costs and enforce spending guardrails. It allows you to set budgets and alerts, visualize cost information using tools like Power BI, and analyze cost patterns and performance bottlenecks.

**Creating release gates.** Azure DevOps provides release management capabilities that allow you to define and enforce release gates. You can set up manual or automated checkpoints to ensure that specific criteria. Criteria can include releases meet security checks, compliance requirements, and cost thresholds, before proceeding.

**Setting access controls.** Azure provides role-based access control (RBAC) to manage access to resources. RBAC allows you to grant permissions to users, groups, or applications based on their roles. By implementing RBAC, you can ensure that only authorized users have access to resources, reducing the risk of unauthorized resource usage and potential cost implications.

**Infrastructure as code.** Azure supports infrastructure as code (IaC) for cost optimization by providing tools and services that enable the deployment and management of infrastructure resources using code. With Azure, you can use tools like Azure Resource Manager (ARM) templates, Azure Bicep, and Azure DevOps to define and deploy your infrastructure resources in a declarative manner. Use Azure DevOps or other CI/CD tools to automate the deployment and teardown of your ephemeral environments.

Consider using lower-cost resource SKUs for your ephemeral environments. Azure provides different pricing tiers for resources, and using lower-cost SKUs for nonproduction environments can help optimize costs. Azure Dev/Test pricing and Azure Reservations are cost-saving methods that you can explore for ephemeral environments.

Automated repositories, such as Azure Repos, provide version control capabilities for managing code and infrastructure configurations. They enable teams to collaborate, track changes, and maintain a history of their codebase. With automated repositories, developers can easily manage and share their code, ensuring that the latest versions are always available.

Pipelines, such as Azure Pipelines, automate the build, test, and deployment processes. They allow teams to define a series of steps and actions that are executed automatically whenever changes are made to the codebase. By automating these processes, organizations can reduce manual effort, ensure consistency, and accelerate the delivery of software.

[Azure Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments) empowers development teams to quickly and easily spin up app infrastructure with project-based templates that establish consistency and best practices while maximizing security. This on-demand access to secure environments accelerates the stages of the software development lifecycle in a compliant and cost-efficient way.

The [Azure Developer CLI](/azure/developer/azure-developer-cli/overview) (`azd`) is an open-source tool that accelerates the time it takes for you to get your application from local development environment to Azure. The Azure Developer CLI provides best practice, developer-friendly commands that map to key stages in your workflow, whether you’re working in the terminal, your editor or integrated development environment (IDE), or CI/CD (continuous integration/continuous deployment).

## Related links

- [Cost Management tools in Azure](/azure/cloud-adoption-framework/govern/cost-management/toolchain) 
- [Adopt policy-driven guardrails](/azure/cloud-adoption-framework/ready/enterprise-scale/dine-guidance)
- [Cost Management policy compliance processes](/azure/cloud-adoption-framework/govern/cost-management/compliance-processes)
- [Create and manage budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) 
- [Assign access to Cost Management data](/azure/cost-management-billing/costs/assign-access-acm-data)
- [Monitor usage and spending with cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending)
- [Identify anomalies and unexpected changes in cost](/azure/cost-management-billing/understand/analyze-unexpected-charges)
- [Develop governance policy statements](/azure/cloud-adoption-framework/govern/cost-management/#develop-governance-policy-statements)
- [Cost Management sample policy statements](/azure/cloud-adoption-framework/govern/cost-management/policy-statements)

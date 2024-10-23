---
title: Governance for SaaS Workloads for ISVs
description: Learn about governance to reduce risks, help ensure compliance, and support your organization's business objectives.
author: joshuawaddell
ms.author: jowaddel
ms.date: 10/24/2024
ms.topic: conceptual
---

# Governance for SaaS workloads for ISVs

[!INCLUDE [header_file](includes/temporary-warning.md)]

Governance is the set of controls, practices, and tools that you can use to organize, control, and help regulate your use of cloud services. You can think of governance as a series of guardrails that sets standards for acceptable use, prevents unauthorized access and modifications, and aligns cloud activities with your overall cloud strategy. Effective governance reduces risks, helps ensure compliance, and supports your organization's business objectives. When you build a software as a service (SaaS) solution, it's crucial to prioritize governance from the start. This approach lays the groundwork for a secure, cost-effective, and efficient solution.

## Cost governance

To help ensure the success of your business, it's critical to understand the costs to run your solution. You need to analyze, manage, and optimize these costs effectively while maintaining control over them. When you start building your solution on Azure, you can use tools like the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate your costs. After you deploy resources, you can use [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) to analyze, manage, and optimize your cloud spending.

For more information about how to track and control costs for SaaS and how to bill your customers, see [Usage monitoring and billing for SaaS workloads on Azure](./usage-monitoring-billing.md).

### Design considerations

- **Develop a naming convention and tagging strategy**. Names and tags provide metadata that you can use to govern your resources and quickly determine ownership. A consistent [resource naming strategy](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming) can help you manage and govern your Azure resources. [Resource tags](/azure/azure-resource-manager/management/tag-resources) are metadata key-value pairs applied to your resources that you can use to identity them.

  Consider using metadata to help you track information such as:

  - The type of resource.
  - The associated workload.
  - The environment in which it's used, such as production, staging, or development.
  - The location of the resource.
  - The customer or group of customers that use the resource, specifically for customer-specific deployments.

- **Implement automated governance through policies.** [Azure Policy](/azure/governance/policy/overview) helps you define organization standards and evaluate the compliance of your workloads and resources. It's a governance tool that you can use to achieve resource consistency, regulatory compliance, security, management, and cost efficiency.

  Azure Policy allows you to create a service catalog of permitted services and service types that's tailored to your workload requirements. This catalog can prevent accidental overspending by helping ensure that only approved services are used. For example, after you determine the type, series, and size of the virtual machines (VMs) that you need, you can implement a policy that only allows the deployment of those VMs. Policies are enforced uniformly across all users and principals, regardless of their permission level.

- **Use cost management tooling.** [Cost Management](/azure/cost-management-billing/costs/overview-cost-management) is a suite of finance operations tools that organizations can use to analyze, monitor, and optimize their cloud spend. The tools are available within the Azure portal and to anyone that has access to a billing account, subscription, resource group, or management group.

  Cost Management provides several tools to support cost governance, such as:

  - [Cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) is a tool that you can use to access analytics and insights into your cloud spend. You're able to review these costs through different smart and customizable views. These views detail insights such as costs by resource groups, services, and subscriptions. By using cost analysis, you can analyze accumulated costs, daily costs, and review the details of your invoices.
  
  - [Budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets?tabs=psbudget) in Cost Management is a tool that you can use to establish spending guardrails and alerts at different scopes within Azure. You can configure budget alerts based on actual spend or forecasted spend. You can also assign budgets at various levels, including management groups, subscriptions, or resource groups.
  
  - [Cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending) allow you to monitor your cloud spend through three separate types of alerts.
  
  - [Budget alerts](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) notify recipients when budget thresholds are reached or when forecasted thresholds will soon be reached.

  - [Anomaly alerts](/azure/cost-management-billing/understand/analyze-unexpected-charges) notify recipients when unexpected changes in your cloud spend occur.

  - [Scheduled alerts](/azure/cost-management-billing/costs/save-share-views#subscribe-to-scheduled-alerts) send recipients daily, weekly, or monthly reports on overall cloud spend.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Enable [Cost Management](/azure/cost-management-billing/costs/overview-cost-management).| You gain access to tools that analyze, monitor, and optimize your spend in the Microsoft Cloud. |
| Create policies to help enforce cost controls, such as permitted resource types and locations. | Enforce consistent standards, control the resources that can be deployed, and track compliance of your resources and cloud spend. |
| Enable appropriate [cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending). | Cost alerts notify you about unexpected cloud spend, or when you approach predefined limits. |
| Use a consistent naming convention and resource tags. Apply tags to indicate which resources are dedicated to a specific customer. | Consistent metadata helps you track which resources belong to which customer. This practice is especially important when you deploy resources that are dedicated to customers. |

## Security and compliance

Security and compliance are foundational design principles for a cloud workload and a key component of proper cloud governance. Security controls, such as [Azure role-based access control (Azure RBAC)](/azure/role-based-access-control/overview), help determine the actions that users can perform in your environment. Compliance controls, such as [Azure Policy](/azure/governance/policy/overview), help you achieve specific regulatory compliance standards for your deployed workloads.

When you develop a SaaS solution, your customers depend on you to safeguard their data and support their business operations. To operate a SaaS solution on behalf of customers, you must meet or exceed their security expectations. You might also need to meet specific compliance requirements imposed by your customers. This requirement is common with clients in regulated industries like healthcare and financial services, and for many enterprise customers.

### Design considerations

- **Define Microsoft Entra tenants.** A Microsoft Entra tenant defines the boundary for the identities that can manage your Azure resources. For most organizations, it's a good practice to use a single Microsoft Entra tenant across all of your resources. When you build SaaS, there are different approaches that you can use to combine or separate Microsoft Entra tenants depending on your needs.

  When deciding whether to use SaaS, it's important to consider three distinct types of use cases:

  - *Internal SaaS*, sometimes called *enterprise* or *corporate*, is when you host your own organization's resources, including Microsoft 365 and other tools that you use yourselves.

  - *Production SaaS* is when you host the Azure resources for your SaaS solution, that customers connect to and use.

  - *Nonproduction SaaS* is when you host the Azure resources for any nonproduction environments of your SaaS solution, such as development, test, and staging environments.

  Most independent software vendors (ISVs) use a single Microsoft Entra tenant for all of the purposes in the preceding list.

  Occasionally, you might have a specific business justification for separating some of the purposes across multiple Microsoft Entra tenants. For example, if you work with high-security government customers, they might require you to use distinct directories for your internal applications and for your production and nonproduction SaaS workloads. These requirements are uncommon.

> [!IMPORTANT]
> It can be difficult to manage multiple Microsoft Entra tenants. Managing multiple tenants increases your management overhead and costs. If you're not careful, multiple tenants can increase your security risks. Only use multiple Microsoft Entra tenants when absolutely necessary.

For more information about how to configure Microsoft Entra tenants when you deploy SaaS, see [ISV considerations for Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone).

- **Manage your identities.** Identity is the cornerstone of cloud security that forms the foundation of access management. When you develop SaaS, you have various types of identities to consider. For more information about identity in SaaS solutions, see [Identity and access management for SaaS workloads on Azure](./identity-access.md).

- **Control access to your Azure resources.** Your Azure resources are critical components of your solution. Azure provides multiple ways to protect your resources.

  - [Azure RBAC](/azure/role-based-access-control/overview) is the authorization system that controls access to the Azure control plane and the resources in your environment. Azure RBAC is a collection of predefined and custom roles that determine what actions you can take against Azure resources. Roles are categorized as [privileged administrator roles](/azure/role-based-access-control/role-assignments-steps#privileged-administrator-roles) and [job function roles](/azure/role-based-access-control/role-assignments-steps#job-function-roles). These roles limit what you can do to a set of resources in a scope that you define. Azure RBAC can grant least privileged access to anyone that manages the workload.

  - [Azure locks](/azure/azure-resource-manager/management/lock-resources?tabs=json) can help prevent accidental deletions and modifications of your Azure resources. When you apply a lock to a resource, even users that have privileged administrator roles can't delete the resource unless they delete the lock first.

- **Comply with regulatory standards**: Many customers need to place strict controls on their resources to meet specific compliance regulations. Azure provides multiple tools to help your organization build a solution on Azure that meets your compliance needs.

  - [Azure Policy](/azure/governance/policy/overview) can help you define organizational standards, and evaluate and enforce the compliance of your workloads and resources. You can implement predefined standards or your own custom compliance standards. Azure Policy includes many [built-in policy initiatives](/azure/compliance/), or groups of policies, for common regulatory standards. These policies include FedRAMP High, HIPPA, HITRUST, PCI DSS, and ISO 27001. When you apply the policies to your environment, the compliance dashboard provides a detailed score of your overall compliance. You can use this dashboard when you create a remediation plan to bring your environment up to standards. You can use Azure Policy to:

    - Deny the deployment of resources based on criteria that are defined in a policy. For example, you can prevent data resources from being deployed in Azure regions where your data residency requirements would be violated.

    - Audit the deployment or configuration of resources to determine if they're deployed with configurations that meet your compliance standards. For example, you can audit VMs to verify that they have backup configured, and to list the VMs that don't.

    - Remediate the deployment of a resource. You can configure policies to remediate incompliant resources by deploying extensions or changing the configuration of new or existing resources. For example, you can use a remediation task to deploy the Microsoft Defender for Endpoint to your VMs automatically.

  - [Microsoft Defender for Cloud](/azure/defender-for-cloud/) provides a continual assessment of the configuration of your resources against compliance controls and best practices in the standards and benchmarks that you apply in your subscriptions. Defender for Cloud calculates an overall compliance score, which helps you determine changes that you need to make.

    By default, Defender for Cloud uses the [Microsoft cloud security benchmark (MCSB)](/security/benchmark/azure/overview) as a baseline standard for security and compliance based practices. The MCSB is a set of compliance controls provided by Microsoft that we recommend for most workloads on Azure. If you need to meet another standard, you can use other available compliance offerings, including FedRAMP High, HIPPA HITRUST, PCI DSS, and ISO2 7001.

> [!TIP]
> Even if you don't need to immediately comply with a regulatory standard, you should anyway. It's much easier to adhere to a standard like MCSB from when you start to deploy your solution than it is to retroactively apply it later.

  You can apply compliance standards to various scopes. For example, you might define a specific Azure subscription as in scope for a specific standard. You can also use Defender for Cloud to evaluate configuration of resources that are hosted in other cloud providers.

  For more information, see [Architectural approaches for governance and compliance in multitenant solutions](/azure/architecture/guide/multitenant/approaches/governance-compliance).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Grant the least amount of access necessary for users and groups to complete their job functions. <br><br> Limit the number of privileged role assignments. Determine if you can use a job function-specific role instead of a privileged administrator role. | You can reduce the exposure if a credential is compromised. |
| Limit the number of Azure subscription owners. | Too many subscription owners increase the risk of a compromised credential.|
| Assign roles to groups instead of users. | This approach reduces the required number of role assignments, which reduces administrative overhead. |
| Adopt a security baseline early in the design process. Consider the MCSB as a starting point. The MCSB provides clear, actionable advice to improve the security of your applications on Azure and across environments in other clouds and on-premises. | By focusing on cloud-specific controls, the MCSB helps you strengthen your overall security posture. |
| Use Azure Locks to prevent accidental changes to your environment.| Locks can help prevent accidental modifications and deletions of resources, resource groups, and subscriptions. |
| Use Azure Policy or Defender for Cloud to assess compliance.| Policies can help enforce organization standards and satisfy regulatory compliance. |

## Next step

- [Azure governance for ISVs](https://aka.ms/ftaisvgovernance) is a set of videos that describe resource and subscription management approaches for ISVs that build SaaS.

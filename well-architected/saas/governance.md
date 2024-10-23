---
title: Governance for SaaS workloads on Azure
description: Learn about the governance to reduce risks, ensures compliance, and supports your organization's business objectives.
author: joshuawaddell
ms.author: jowaddel
ms.date: 09/30/2024
ms.topic: conceptual
ms.collection: learn-startups
---

# Governance for SaaS workloads on Azure

Governance is the set of controls, practices, and tools to help you organize, control, and regulate your use of cloud services. You can think of governance as a series of guardrails that sets standards for acceptable use, prevents unauthorized access and modifications, and aligns cloud activities with your overall strategy. Effective governance reduces risks, ensures compliance, and supports your organization's business objectives. When you build SaaS, you should consider governance early in your journey so you set the foundation for running a secure, cost effective, and efficient solution.

## Cost governance

Understanding how much it costs to run your solution is critical to the sucess of your business. Not only do you need to be able to analyze, manage, and optimize your costs, you also need to be able to control your costs. As you begin to build your solution on Azure, there are many tools, such as the [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/), which can help you estimate your solution costs. After resources are deployed to Azure, you can use [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) to help you to analyze, manage, and optimze your cloud spend.

For more information about how to track and control costs for SaaS, as well as how to bill your customers, see [Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md).

### Design considerations

- **Develop a naming convention and tagging strategy**. Names and tags provide metadata that you can use to govern your resources and quickly determine ownership. A consistent [resource naming strategy](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming) helps you to manage and govern your Azure resources. [Resource tags](/azure/azure-resource-manager/management/tag-resources) are metadata key-value pairs applied to your resources to help you identity them.

  Consider using metadata to help you to track information like:

  - The type of resource
  - Which workload it is associated with
  - The environment it's used in, such as production, staging, or development
  - Where the resource is located
  - If you deploy customer-specific resources, the customer or group of customers that use the resource

    <!-- TODO update this -->
    :::image type="icon" source="../_images/tags.png" border="false"::: 

- **Implement automated governance through policies.** [Azure Policy](/azure/governance/policy/overview) helps you to define organization standards and evaluate the compliance of your workloads and resources. It is a governance tool that helps you achieve resource consistency, regulatory compliance, security, management, and cost.

    You can use Azure Policy to create a service catalog of allowed services and service types your workload requires, which can help prevent accidental overspending.  For example, if you have determined the type, series, and size of the virtual machines you need, you can implement a policy that will only allow those virtual machines to be deployed. Policies are enforced uniformly across all users and principals, regardless of their permission level.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and operational efficiency.** Implementing too many policies can reduce the productivity of your team. Strive to implement automated controls on the most essential elements.

- **Use cost management tooling.** [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management) is a suite of finance operations (FinOps) tools that help organizations analyze, monitor, and optimize their cloud spend. The tools are available within the Azure portal and can be used by anyone with access to a billing account, subscription, resource group, or management group.

  Cost Management provides a number of tools to support cost governance, such as:

  - [Cost Analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) is a tool that allows you to access analytics and insights into your cloud spend. You are able to review these costs through different smart and customizable views that detail insights such as costs by resource groups, services, and subscriptions. You can analyze accumulated costs, daily costs, and review the details of your invoices.
  - [Budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets?tabs=psbudget) in Azure Cost Management is a tool that can help you establish spending guardrails and alerts at different scopes within Azure. Budget alerts can be configured based on actual or forecasted spend. You can assign budgets at the management group, subscription, or resource group scopes.
  - [Cost Alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending) allow you to monitor your cloud spend through three separate types of alerts.
    - [Budget alerts](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) notify recipients when actual thresholds have been reached or when forcasted thresholds are soon to be reached.
    - [Anomaly alerts](/azure/cost-management-billing/understand/analyze-unexpected-charges) notifiy recipeients when unexpected changes in your cloud spend occur.
    - [Scheduled alerts](/azure/cost-management-billing/costs/save-share-views#subscribe-to-scheduled-alerts) notify recipients with daily, weekly, or monthly reports on overall cloud spend.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Enable [Microsoft Cost Management](/azure/cost-management-billing/costs/overview-cost-management).| You'll get access to tools that analyze, monitor and optimize your spend in the Microsoft Cloud. |
| Create policies to enforce cost controls, such as allowed resource types and locations. | Enforce consistent standards, control the resources that can be deployed, and track compliance of your resources and cloud spend. |
| Enable appropriate [cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending). | You'll be warned about unexpected cloud spend, or when you're approaching predefined limits. |
| Use a consistent naming convention and resource tags. Apply tags to indicate any resources dedicated to a specific customer. | Consistent metadata helps you to track which resources belong to which customer, which is especially important when you deploy resources that are dedicated to customers. |

## Security and compliance

Security and compliance are foundational design principles for a cloud workload and a key component of proper cloud governance. Security controls, such as [Azure role-based access control (RBAC)](/azure/role-based-access-control/overview), help to determine the actions that users are permitted to perform in your environment, while compliance controls, like [Azure Policy](/azure/governance/policy/overview), help you achieve specific regulatory compliance standards for your deployed workloads.

When you create a SaaS solution, your customers place a great deal of trust in you with their data and to support their business processes. Operating SaaS on behalf of customers means you need to meet or exceed customer expectations for security. You also might need to meet specific compliance requirements that your customers impose, which is common with customers in regulated industries like healthcare and financial services, as well as many enterprise customers.

### Design considerations

- **Define Microsoft Entra tenants.** A Microsoft Entra tenant is the boundary for the identities that can manage your Azure resources. For most organizations, it's a good practice to use a single Entra tenant across all of your resources. However, when you build SaaS, there are different approaches you can follow to combine or separate Microsoft Entra tenants depending on your needs.

    When you consider SaaS, it's a good idea to consider three distinct types of use cases:

    - *Internal*, sometimes called *enterprise* or *corporate*, is where you host your own organization's resources, including Microsoft 365 and other tools that you use yourselves. 
    - *Production SaaS* is where you host the Azure resources for your SaaS solution, which customers connect to and use.
    - *Non-production SaaS* is where you host the Azure resources for any non-production environments of your SaaS solution, such as development, test, and staging environments.

    Most ISVs use a single Microsoft Entra tenant for all of the purposes in the preceding list.

    Occasionally, you might have a specific business justification for separating some of the purposes across multiple Microsoft Entra tenants. For example, if you work with high-security government customers, they might mandate that you use distinct directories for your internal applications and for your production and non-production SaaS workloads. These requirements are uncommon.

    > [!IMPORTANT]
    > Managing multiple Microsoft Entra tenants is complex. It increases your management overhead, licensing complexity, and costs, and if not done carefully, it can increase your security risks. It's best to only use multiple Microsoft Entra tenants if your situation truly requires it.

    For more information about how to configure Microsoft Entra tenants when you deploy SaaS, see [Independent software vendor (ISV) considerations for Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone).

- **Manage your identities.** Identity is the cornerstone of cloud security, and forms the foundation of access management. When you develop SaaS, you have multiple types of identities to consider. For more information about identity in SaaS solutions, see [Identity and access management for SaaS workloads on Azure](./identity-access.md).

- **Control access to your Azure resources.** Your Azure resources are critical components of your solution. Azure provides multiple ways to protect your resources.

    - [Azure role-based access control](/azure/role-based-access-control/overview) (RBAC) is the authorization system used to control access to the Azure control plane, and more specifically the resources in your environment. Azure RBAC is a collection of pre-defined and custom roles that determine what actions are allowed to be taken against Azure resources. Roles are categorized as [privileged administrator roles](/azure/role-based-access-control/role-assignments-steps#privileged-administrator-roles) and [job function roles](/azure/role-based-access-control/role-assignments-steps#job-function-roles), and they limit what you can do to a set of resources in a scope that you define. Azure RBAC can be enables least privileged access to anybody responsible for managing the workload.

    - [Azure Locks](/azure/azure-resource-manager/management/lock-resources?tabs=json) help to prevent accidental deletions and modifications of your Azure resources. When a lock is applied to a resource, even users with privileged administrator roles can't delete the resource unless they first take explicit action to delete the lock.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and operational efficiency.** RBAC and locks are important elements of a cloud security and governance strategy. However, be aware of operational complexities that might emerge when you severely restrict who can perform common operations. Try to strike a balance between your security and functional needs. Have a clear plan for escalation of responsibilities if there's an emergency or if key people are unavailable.

- **Comply with regulatory standards**: Many customers need to place strict controls on their resources to meet specific compliance regulations. Azure provides multiple tools to help your organization build a solution on Azure that meets your compliance needs.

    - [Azure Policy](/azure/governance/policy/overview) helps you to define organizational standards, and to to evaluate and enforce the compliance of your workloads and resources.
    
        You can enforce predefined or your own custom compliance standards. Azure Policy includes a number of [built-in policy initiatives](/azure/compliance/) (groups of policies) for common regulatory standards. When the policies are applied to your environment, the compliance dashboard provides a detailed score of your overall compliance. You can use this dashboard when creating a remediation plan to bring your environment up to standards.
        
        Azure Policy can be used in a number of ways, including:

        - Deny the deployment of resources based on criteria that are defined in a policy. For example, you can prevent data resources from being deployed in Azure regions where your data residency requirements would be violated.
        - Audit the deployment or configuration of resources to determine if it they're deployed with configuration that meets your compliance standards. For example, you can audit virtual machines to verify that they have backup configured, and to list the virtual machines that don't.
        - Remediate the deployment of a resource. Policies can be configured to remediate incompliant resources in several ways, such as by deploying extensions or changing the configuration of new or existing resources. For example, you can use a remediation task to deploy the Microsoft Defender for Endpoint to your virtual machines automatically.

    - [Microsoft Defender for Cloud](/azure/defender-for-cloud/) provides a continual assessment of the configuration of your resources against compliance controls and best practices in the standards and benchmarks you’ve applied in your subscriptions. Defender for Cloud calculates an overall compliance score, which helps to determine changes that need to be made.
    
        By default, Defender for Cloud uses the [Microsoft cloud security benchmark](/security/benchmark/azure/overview) (MCSB) as a baseline standard for security and compliance based practices. The MCSB is a Microsoft-provided set of compliance controls that we recommend for most workloads on Azure. If you need to meet another standard, you can use other available compliance offerings.

        > [!TIP]
        > Even if you don't have an immediate need to comply with a regulatory standard, it's a good practice to do so anyway. It's much easier to follow a standard like MCSB from when you start deploying your solution than it is to retroactively apply it later.

        Compliance standards can be applied to a variety of scopes. For example, you might define a specific Azure subscription as in scope for a specific standard. You can also use Microsoft Defender for Cloud to evaluate configuration of resources hosted in other cloud providers.

        For more information, see [Architectural approaches for governance and compliance in multitenant solutions](/azure/architecture/guide/multitenant/approaches/governance-compliance).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Provide the least amount of access necessary for users and groups to complete their job functions. <br><br> Limit the number of privileged role assignments. Determine if a job function-specific role can be used in place of a privileged administrator role. | You'll reduce the exposure if a credential is compromised. |
| Limit the number of Azure subscription owners. | Too many subscription owners increases the risk of potential impact due to a comprimised credential.|
| Assign roles to groups instead of users. | You'll reduce overall number of role assignments required, which reduces administrative overhead. |
| Adopt a security baseline early in the design process. Consider the Microsoft cloud security benchmark (MCSB) as a starting point. The MCSB offers clear, actionable advice to improve the security of your applications on Azure and across environments in other clouds and on-premises. | By focusing on cloud-specific controls, the MCSB helps you strengthen your overall security posture. |
| Use Azure Locks to prevent accidential changes to your environment.| Locks help to prevent accidental modifications and deletions of resources, resource groups, and subscriptions. |
| Use Azure Policy or Microsoft Defender for Cloud to assess compliance.| Policies can help to enforce organization standards and satisfy regulatory compliance. |

## Community links

- [Azure Governance for ISVs](https://aka.ms/ftaisvgovernance) is a set of videos that describe resource and subscription management approaches for ISVs building SaaS.

## Next step

Learn about the strategies for choosing the right Azure regions for your resources and developing a resource organization strategy to support the growth and evolution of your SaaS solution.

> [!div class="nextstepaction"]
> [Design area: Resource organization for SaaS workloads on Azure](./resource-organization.md)

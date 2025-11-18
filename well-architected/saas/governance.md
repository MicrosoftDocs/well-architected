---
title: Governance for SaaS Workloads on Azure
description: Learn about governance to reduce risks, help ensure compliance, and support your organization's business objectives.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/03/2025
ms.update-cycle: 1095-days
ms.topic: concept-article
ms.collection: learn-startups
---

# Governance for SaaS workloads on Azure

Governance is the set of controls, practices, and tools that you can use to organize, control, and help regulate your use of cloud services. You can think of governance as a series of guardrails that set standards for acceptable use, prevent unauthorized access and modifications, and align cloud activities with your overall cloud strategy. Effective governance reduces risks, helps ensure compliance, and supports your organization's business objectives. When you build a software as a service (SaaS) solution, it's crucial to prioritize governance from the start. This approach lays the groundwork for a secure, cost-effective, and efficient solution.

## Cost governance

To help ensure the success of your business, it's critical to understand the costs to run your solution. You need to analyze, manage, and optimize these costs effectively while maintaining control over them. When you start building your solution on Azure, you can use tools like the pricing calculators and cost analyzers to estimate your costs. 

For more information about how to track and control costs for SaaS and how to bill your customers, see [Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md).

### Design considerations

- **Develop a naming convention and tagging strategy.** Names and tags provide metadata that you can use to govern your resources and quickly determine ownership. Consistent resource naming can help you manage and govern your Azure resources. Azure resource tags are metadata key-value pairs that you apply to your resources and use to identify them.

  Consider using metadata to help you track information such as:

  - The type of resource.
  - The associated workload.
  - The environment in which it's used, such as production, staging, or development.
  - The location of the resource.
  - The customer or group of customers that uses the resource, for customer-specific deployments.
  
  :::image type="content" source="./images/tags.png" alt-text="Diagram that shows Azure resources with tags that indicate which customer they're assigned to." lightbox="./images/tags.png" border="false":::

  For strategies on resource naming, see [Cloud Adoption Framework: Resource naming](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming).

- **Implement automated governance through policies.** Policies are instrumental in defining organization standards and evaluating the compliance of your workloads and resources. It's a governance tool that you can use to achieve resource consistency, regulatory compliance, security, management, and cost efficiency.

  Use Azure Policy to create a service catalog of permitted services and service types that's customized for your workload requirements. This catalog can prevent accidental overspending by helping ensure that only approved services are used. For example, after you determine the type, series, and size of the virtual machines (VMs) that you need, you can implement a policy that only allows the deployment of those VMs. Enforce policies uniformly across all users and principals, regardless of their permission level.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and operational efficiency.** Implementing too many policies can reduce the productivity of your team. Strive to implement automated controls on the most essential elements.

- **Use cost management tooling.** Microsoft Cost Management provides several tools to support cost governance, such as:

  - [Cost analysis](/azure/cost-management-billing/costs/quick-acm-cost-analysis) is a tool that you can use to access analytics and insights into your cloud spend. You can review these costs through different smart and customizable views. These views detail insights such as costs by resource groups, services, and subscriptions. By using cost analysis, you can analyze accumulated and daily costs and review the details of your invoices.
  
  - [Budgets](/azure/cost-management-billing/costs/tutorial-acm-create-budgets?tabs=psbudget) in Cost Management is a tool that you can use to establish spending guardrails and alerts at different scopes within Azure. You can configure budget alerts based on actual spend or forecasted spend. You can also assign budgets at various levels, including management groups, subscriptions, or resource groups.
  
  - [Cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending) help you monitor your cloud spend through three separate types of alerts.
  
    - [Budget alerts](/azure/cost-management-billing/costs/tutorial-acm-create-budgets) notify recipients when you reach budget thresholds or when you'll reach forecasted thresholds soon.

    - [Anomaly alerts](/azure/cost-management-billing/understand/analyze-unexpected-charges) notify recipients when unexpected changes in your cloud spend occur.

    - [Scheduled alerts](/azure/cost-management-billing/costs/save-share-views#subscribe-to-scheduled-alerts) send recipients daily, weekly, or monthly reports on overall cloud spend.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Enable [Cost Management](/azure/cost-management-billing/costs/overview-cost-management). <br><br> The tools are available within the Azure portal and to anyone who has access to a billing account, subscription, resource group, or management group.| You gain access to tools that analyze, monitor, and optimize your spend in the Microsoft Cloud. |
| Create [Azure Policy](/azure/governance/policy/overview) to help enforce cost controls, such as permitted resource types and locations. | This strategy helps you enforce consistent standards, control the resources that can be deployed, and track compliance of your resources and cloud spend. |
| Enable appropriate [cost alerts](/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending). | Cost alerts notify you about unexpected cloud spend or when you approach predefined limits. |
| Use a consistent naming convention and resource tags. Apply [Azure resource tags](/azure/azure-resource-manager/management/tag-resources) to indicate which resources are dedicated to a specific customer. | Consistent metadata helps you track which resources belong to which customer. This practice is especially important when you deploy resources that are dedicated to customers. |

## Security and compliance

Security and compliance are foundational design principles for a cloud workload and a key component of proper cloud governance. Security controls, such as role-based access controls, help determine the actions that users can perform in your environment. Controls through policies can help you achieve specific regulatory compliance standards for your deployed workloads.

For more information, see [Azure role-based access control (RBAC)](/azure/role-based-access-control/overview) and [Azure Policy](/azure/governance/policy/overview).

When you develop a SaaS solution, your customers depend on you to safeguard their data and support their business operations. To operate a SaaS solution on behalf of customers, you must meet or exceed their security expectations. You might also need to meet specific compliance requirements imposed by your customers. This requirement is common with customers in regulated industries like healthcare and financial services and for many enterprise customers.

### Design considerations

- **Define Microsoft Entra tenants.** A Microsoft Entra tenant defines the boundary for the identities that can manage your Azure resources. For most organizations, it's a good practice to use a single Microsoft Entra tenant across all of your resources. When you build SaaS, there are different approaches that you can use to combine or separate Microsoft Entra tenants depending on your needs.

  When you're deciding whether to use SaaS, it's important to consider three distinct types of use cases:

  - *Internal SaaS*, sometimes called *enterprise* or *corporate*, is when you host your own organization's resources, including Microsoft 365 and other tools that you use yourselves.

  - *Production SaaS* is when you host the Azure resources for your SaaS solution that customers connect to and use.

  - *Nonproduction SaaS* is when you host the Azure resources for any nonproduction environments of your SaaS solution, such as development, test, and staging environments.

  Most independent software vendors (ISVs) use a single Microsoft Entra tenant for all of the purposes in the preceding list.

  Occasionally, you might have a specific business justification for separating some of the purposes across multiple Microsoft Entra tenants. For example, if you work with high-security government customers, they might require you to use distinct directories for your internal applications and for your production and nonproduction SaaS workloads. These requirements are uncommon.

   > [!IMPORTANT]
   > It can be difficult to manage multiple Microsoft Entra tenants. Managing multiple tenants increases your management overhead and costs. If you're not careful, multiple tenants can increase your security risks. Only use multiple Microsoft Entra tenants when absolutely necessary.

   For more information about how to configure Microsoft Entra tenants when you deploy SaaS, see [ISV considerations for Azure landing zones](/azure/cloud-adoption-framework/ready/landing-zone/isv-landing-zone).

- **Manage your identities.** Identity is the cornerstone of cloud security that forms the foundation of access management. When you develop SaaS, you have various types of identities to consider. For more information about identity in SaaS solutions, see [Identity and access management for SaaS workloads on Azure](./identity-access.md).

- **Control access to your Azure resources.** Your Azure resources are critical components of your solution. Azure provides multiple ways to protect your resources.

  - [Azure RBAC](/azure/role-based-access-control/overview) is the authorization system that controls access to the Azure control plane and the resources in your environment. Azure RBAC is a collection of predefined and custom roles that determine what actions you can take against Azure resources. Roles are categorized as [privileged administrator roles](/azure/role-based-access-control/role-assignments-steps#privileged-administrator-roles) and [job function roles](/azure/role-based-access-control/role-assignments-steps#job-function-roles). These roles limit what you can do to a set of resources in a scope that you define. Azure RBAC can grant least privileged access to anyone that manages the workload.

  - [Azure locks](/azure/azure-resource-manager/management/lock-resources) can help prevent accidental deletions and modifications of your Azure resources. When you apply a lock to a resource, even users who have privileged administrator roles can't delete the resource unless they explicitly delete the lock first.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Security and operational efficiency.** RBAC and locks are important elements of a cloud security and governance strategy. However, consider operational complexities that might occur when you severely restrict who can perform common operations. Try to balance your security and functional needs. Have a clear plan to escalate responsibilities if there's an emergency or if key people are unavailable.

- **Comply with regulatory standards.** Many customers need to place strict controls on their resources to meet specific compliance regulations. Azure provides multiple tools to help your organization build a solution on Azure that meets your compliance needs.

  - [Azure Policy](/azure/governance/policy/overview) can help you define organizational standards and evaluate and enforce the compliance of your workloads and resources. You can implement predefined standards or your own custom compliance standards. Azure Policy includes many [built-in policy initiatives](/azure/compliance/), or groups of policies, for common regulatory standards. These policies include FedRAMP High, HIPAA, HITRUST, PCI DSS, and ISO 27001. When you apply the policies to your environment, the compliance dashboard provides a detailed score of your overall compliance. You can use this dashboard when you create a remediation plan to bring your environment up to standards. You can use Azure Policy to:

    - Deny the deployment of resources based on criteria that are defined in a policy. For example, you can prevent data resources from being deployed in Azure regions where your data residency requirements would be violated.

    - Audit the deployment or configuration of resources to determine if they're deployed with configurations that meet your compliance standards. For example, you can audit VMs to verify that they have backup configured and to list the VMs that don't.

    - Remediate the deployment of a resource. You can configure policies to remediate incompliant resources by deploying extensions or changing the configuration of new or existing resources. For example, you can use a remediation task to deploy the Microsoft Defender for Endpoint to your VMs automatically.

  - [Microsoft Defender for Cloud](/azure/defender-for-cloud/) provides a continual assessment of the configuration of your resources against compliance controls and best practices in the standards and benchmarks that you apply in your subscriptions. Defender for Cloud calculates an overall compliance score, which helps you determine changes that you need to make.

    By default, Defender for Cloud uses the [Microsoft cloud security benchmark (MCSB)](/security/benchmark/azure/overview) as a baseline standard for security and compliance-based practices. The MCSB is a set of compliance controls provided by Microsoft that we recommend for most workloads on Azure. If you need to meet another standard, you can use other available compliance offerings.

   > [!TIP]
   > Even if you don't need to immediately comply with a regulatory standard, you should anyway. It's much easier to adhere to a standard like MCSB from when you start to deploy your solution than it is to retroactively apply it later.

  You can apply compliance standards to various scopes. For example, you might define a specific Azure subscription as in scope for a specific standard. You can also use Defender for Cloud to evaluate the configuration of resources that are hosted in other cloud providers.


### Design recommendations

| Recommendation | Benefit |
|---|---|
| Grant the least amount of access necessary for users and groups to complete their job functions. <br><br> Limit the number of privileged role assignments. Determine if you can use a job function-specific role instead of a privileged administrator role. | You can reduce the exposure if a credential is compromised. |
| Limit the number of Azure subscription owners. | Too many subscription owners increase the risk of a compromised credential.|
| Assign roles to groups instead of users. | This approach reduces the required number of role assignments, which reduces administrative overhead. |
| Adopt a security baseline early in the design process. Consider the MCSB as a starting point. The MCSB provides clear, actionable advice to improve the security of your applications on Azure and across environments in other clouds and on-premises. | By focusing on cloud-specific controls, the MCSB helps you strengthen your overall security posture. |
| Use Azure locks to prevent accidental changes to your environment.| Locks can help prevent accidental modifications and deletions of resources, resource groups, and subscriptions. |
| Use Azure Policy or Defender for Cloud to assess compliance.| Policies can help enforce organization standards and satisfy regulatory compliance. |


## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about governance considerations:

- [Architectural approaches for governance and compliance in multitenant solutions](/azure/architecture/guide/multitenant/approaches/governance-compliance)

## Next step

Learn strategies for how to choose the right Azure regions for your resources and develop a resource organization strategy to support the growth and evolution of your SaaS solution.

> [!div class="nextstepaction"]
> [Design area: Resource organization for SaaS workloads on Azure](./resource-organization.md)

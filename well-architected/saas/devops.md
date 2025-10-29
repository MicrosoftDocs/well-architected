---
title: DevOps Practices for SaaS Workloads on Azure
description: Learn about DevOps considerations for SaaS workloads, including efficient customer lifecycle management and safe deployment practices.
author: brandonmartinez
ms.author: prwilk
ms.date: 11/22/2024
ms.topic: concept-article
ms.collection: learn-startups
---

# DevOps practices for SaaS workloads on Azure

DevOps practices are integral to managing workloads on Azure, especially for software as a service (SaaS) applications. Key aspects of managing a workload include onboarding, offboarding, and modifying customer instances. These practices streamline operations and enhance scalability and reliability, which minimizes the chances of outages.

This article describes design considerations for efficient customer lifecycle management and safe deployment practices.

## Manage customer lifecycles

Managing customer lifecycle events is crucial for any SaaS application. Typically, these events include:

- **Onboarding:** When a customer signs up.
- **Altering:** When a customer's instance is modified, such as a change in their pricing tier.
- **Offboarding:** When a customer cancels their account.

You might encounter other lifecycle events. For example, you might allow your customers to pause their subscription while retaining their data for a set period and resume their subscription later. Each event can have unique implications for your application.

In some solutions, customer lifecycle management might only require creating or managing data in a database table. For other solutions, it might include orchestrating the deployment of Azure infrastructure, application code, and more complex configuration.

Lifecycle management is a key responsibility of a SaaS solution's control plane. Initially, your team might handle these activities manually. But over time, try to transition more functionality into a formalized control plane solution or application.

### Design considerations

- **Consistency**. When you plan your lifecycle management strategy, consider the complexity of actions that each customer lifecycle event requires. These actions include the size of your solution, customer base, and organizational overhead. Have a clear understanding of necessary steps for each event and invest in controls to maintain consistency. Regularly review and update your processes to help ensure that they remain valid as your solution evolves.

- **Tenancy model**. Your approach to handling customer lifecycle events depends on your tenancy model.

  - **Fully multitenant solutions that have infrastructure resources**. Onboarding or offboarding a customer typically includes updating a customer list and associated data in your application's data store.
  
  - **Dedicated resources per customer**. The tasks typically include initiating deployments to Azure, monitoring progress, and handling deployment failures, possibly with human intervention.
  
  - **Customer-deployed resources**. You might need to interface directly with the customer's engineering team for onboarding or offboarding.

- **Tiers**. Consider your pricing model and the different infrastructure needs of each tier, especially if you allow customers to freely change their SKU at any time. For example, if your SaaS solution includes a core application and multiple paid add-on modules, make sure the core app's resources are deployed during onboarding. Also, allow for dynamic addition and removal of add-on modules. When a module is removed, decide whether to delete associated data or store it for potential reactivation.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Document each type of customer lifecycle event.<br><br> Make sure you capture step-by-step details of the process for each event. | You can plan how to respond to each event in your solution design. <br><br> Clear instructions help human operators maintain consistency and serve as the foundation for future automation.|
| Communicate the shared responsibility between you and your customer for each lifecycle event. Communicate clearly and early about what actions you expect the customers to take in order to complete a lifecycle stage. | You can reduce potential errors and customer frustration caused by miscommunication. |
| Do capacity planning for each lifecycle event. For example, when you onboard a new customer, plan to deploy a new instance of your application if existing instances lack sufficient capacity to handle the extra load. <br><br> For more information, see [Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md). | You can scale more easily and prevent deployment failures. |
| Automate your lifecycle events when practical. <br><br> For low-volume or early-stage solutions, manual deployment and configuration might be sufficient. But should still use scripts, even if an engineer runs them each time a lifecycle event occurs. <br><br> As your solution matures, integrate these responsibilities into a full control plane to reduce human error and support higher scale. | You can reduce the significant risk of human error and support higher scale. |

## Plan your infrastructure management strategy

Develop a strategy for deploying, maintaining, and managing Azure infrastructure early on. As you scale your SaaS, the number of resources grows. It's easier to follow a management strategy from the start than to reconcile infrastructure later when it becomes too complex to handle manually.

### Design considerations

- **Customer resource management**. Your tenancy model affects resource deployment in SaaS solutions. You might deploy dedicated Azure resources for each customer or share resources among a set number of customers. Alternatively, you could use a single set of shared resources and reconfigure them as you onboard new customers. Consider these typical approaches for managing the lifecycle of the resources:

  - Treat your customer list as a configuration of the resources to deploy. Use centralized deployment pipelines to deploy and configure those resources.
  
  - Treat your customer list as data. Use a control plane application to provision and configure infrastructure.

- **Infrastructure automation**. Many organizations start by manually deploying cloud infrastructure via the Azure portal. This strategy is easy at first but doesn't scale well over time. Plan to automate your infrastructure setup by using infrastructure as code (IaC) tools like Bicep or Terraform. For more complex requirements, create a control plane that uses Azure Resource Manager APIs directly.

- **Infrastructure attribution**. Keep track of which customers are deployed on which infrastructure. Tracking is important for accurate capacity planning and cost attribution. You can track customer infrastructure centrally in a customer database. Or for dedicated infrastructure, use Azure resource metadata with customer-specific resource groups and resource tags. For more information, see [Resource organization for SaaS workloads](./resource-organization.md).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Build infrastructure automation by using deployment pipelines, scripts, or templates with tools that your team is already familiar with. | Familiar tools reduce the risk of errors because infrastructure automation can be disruptive if the tools aren't understood.|
| Deploy your infrastructure by using IaC when possible. | IaC helps reduce manual maintenance, which becomes riskier and more burdensome as the amount of infrastructure grows. |
| Separate core infrastructure from customer-level infrastructure. | Different types of infrastructure have distinct lifecycles and management activities. By separating them, you can manage each set independently on its own schedule. |
| Use Azure Managed Applications to deploy and manage customer-deployed resources. | Azure Managed Applications provides a range of capabilities that you can use to deploy and manage resources within a customer's Azure subscription. |

## Plan for application deployments

To enhance functionality, regularly update application code and configuration. Customers expect consistent uptime during updates and safe rollouts to minimize the risk of outages.

### Design considerations

- **Standardize tooling and processes.** Industry-proven DevOps tooling helps ensure consistency across functions and maturity in processes for managing your application deployments. In most situations, developing your own tools is considered an antipattern. For more information, see [OE:03 Software development practices](../operational-excellence/formalize-development-practices.md).


  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complexity and cost.** Familiar DevOps tools can be cost-efficient in terms of money and skills. However, they add the operational burden of managing each tool separately. It's important to remain open to new technological innovations that could benefit your workload.

- **Deploy updates progressively.** Roll out updates to customers in phases. Divide users into logical groups and deploy changes to one group at a time. Apply the same rigor to configuration changes because they can alter code behavior and cause outages. Follow a deployment process for these changes.

- **Adopt a versioning strategy.** When customers can choose their application version, it adds flexibility but complicates your operations. Set clear expectations for deprecating old versions and outline what happens when they're no longer supported.

- **Implement automation.** Manual deployments are prone to risks because of human error and lack of consistency. Even if your deployments are triggered manually, you should automate your deployment process as much as possible and require minimal human oversight. Consider the steps of your deployment process and how to best automate them.

- **Integrate testing.** Integrate testing into your deployment process by running:

  - Unit tests during code build.
  - Integration tests after deployment.
  - Regular performance tests.
  - Regular security and penetration tests.

Decide on a plan of action if any tests fail at any stage.

- **Failed deployments.** Plan for deployment failures by considering necessary actions and preparing a rollback strategy.

- **Access to customer environments.** If you deploy resources into customer environments, understand how you can apply updates within those environments. Consider capabilities that Azure Managed Applications provides, such as [deployment of updates to applications](/azure/azure-resource-manager/managed-applications/update-managed-resources).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Use established, industry-proven DevOps tooling and processes to manage your application deployments. In most situations, developing your own tools is considered an antipattern. <br><br> For more information, see [OE:03 Software development practices](/azure/well-architected/operational-excellence/formalize-development-practices#establish-collaboration-and-communication-standards). | This strategy can help ensure that your engineering team deploys applications effectively by not having to learn custom-built tooling. |
| Proactively notify customers of any upcoming or completed deployments. | This strategy can help ensure that proper expectations are set with your customers about upcoming changes to your application. |
| Adopt safe deployment practices that deploy updates to customer groups via strategies like progressive exposure and health models. Start with less-sensitive or early-adopter customers before you move to a broader customer base. <br> For more information, see [Recommendations for safe deployment practices](/azure/well-architected/operational-excellence/safe-deployments). | This strategy can help you identify problems before they affect all customers. |
| Treat configuration as code. | You can reduce the likelihood of downtime and adopt a consistent process for production changes. This approach centralizes operational responsibilities, such as testing changes and progressively rolling out updates to configuration and code. |
| Define a change management process and communicate a version update policy to make sure that customers know who triggers updates, their frequency, and conditions. <br><br> If customers can choose their application version, set clear guidelines for how to deprecate old versions. Minimize the number of application versions that run in production. | Maintaining older versions causes operational inefficiency. Provide the necessary control for your customers and avoid overburdening your team by setting clear expectations and policies. |
| Avoid customizing applications for a single customer. <br><br> To support different customer needs, you can either create various tiers of your solution or use feature flags to enable specific functionalities for certain users. | Avoid ambiguity about which features are deployed into which version, and reduce your maintenance burden.  |
| Have a rollback plan for failed deployments, including criteria for triggering and necessary approvals.|Rollback plans help ensure that you can recover from deployment mistakes even in unforeseen circumstances.|
| Test your application regularly and at multiple stages in the software development process. Adopt a *shift-left* mindset and catch bugs and deviations early in the lifecycle. | Help prevent critical errors from affecting your customers. |

## Other resources

Multitenancy is a core business methodology for designing SaaS workloads. The following articles provide more information about how to adopt DevOps practices:

- [Architectural approaches for the deployment and configuration of multitenant solutions](/azure/architecture/guide/multitenant/approaches/deployment-configuration)
- [Considerations for updating a multitenant solution](/azure/architecture/guide/multitenant/considerations/updates)
- [Considerations for multitenant control planes](/azure/architecture/guide/multitenant/considerations/control-planes)
- [Architectural approaches for control planes in multitenant solutions](/azure/architecture/guide/multitenant/approaches/control-planes)

## Next step

Learn about incident management considerations to implement processes and tools that support a SaaS solution on Azure.

> [!div class="nextstepaction"]
> [Design area: Incident management for SaaS workloads on Azure](./incident-management.md)

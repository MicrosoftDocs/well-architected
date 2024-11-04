---
title: DevOps Practices for SaaS Workloads on Azure
description: Learn about DevOps consideration for SaaS workloads, including efficient customer lifecycle management and safe deployment practices.
author: brandonmartinez
ms.author: prwilk
ms.date: 11/04/2024
ms.topic: conceptual
ms.collection: learn-startups
---

# DevOps practices for SaaS workloads on Azure

DevOps practices are integral to managing workloads on Azure, especially for SaaS applications. Key aspects include onboarding, offboarding, and modifying customer instances. These practices not only streamline operations but also enhance scalability and reliability, minimizing the chances of outages.

This article describes design considerations for efficient customer lifecycle management and safe deployment practices.

## Manage customer lifecycles

Managing *customer lifecycle events* is crucial for any SaaS application. Typically, these events include:

- **Onboarding**: When a customer signs up.
- **Altering**: Modifying a customer's instance, such as changing their pricing tier.
- **Offboarding**: When a customer cancels their account.

You might encounter additional lifecycle events. For example, you might allow your customers to pause their subscription while retaining their data for a set period, and resume their subscription later. Each event can have unique implications for your application.

In some solutions, customer lifecycle management might just require creating or managing data in a database table. For other solutions, it might involve orchestrating the deployment of Azure infrastructure, application code, and more complex configuration.

Lifecycle management is a key responsibility of a SaaS solution's control plane. Initially, your team might handle these activities manually, but over time, try to transition more functionality into a formalized control plane solution or application.

### Design considerations

- **Consistency**. When planning your lifecycle management strategy, consider the complexity of actions required for each customer lifecycle event. This includes the size of your solution, customer base, and organizational overhead. Ensure you have a clear understanding of necessary steps for each event and invest in controls to maintain consistency. Regularly review and update your processes to help ensure they remain valid as your solution evolves.

- **Tenancy model**. Your approach to handling customer lifecycle events depends on your tenancy model.

  - **Fully multitenant solutions with infrastructure resources**. Onboarding or offboarding a customer typically involves updating a customer list and associated data in your application's data store.
  - **Dedicated resources per customer**. The tasks would typically involve initiating deployments to Azure, monitoring progress, and handling deployment failures, possibly with human intervention.
  - **Customer-deployed resources**. You might need to interface directly with the customer's engineering team for onboarding or offboarding.

- **Tiers**. Consider your pricing model and the different infrastructure needs of each tier, especially if you allow customers to freely change their SKU at any time.

  - For example, if your SaaS solution includes a core application and multiple paid add-on modules, make sure the core app's resources are deployed during onboarding. Additionally, allow for dynamic addition and removal of add-on modules. When a module is removed, decide whether to delete associated data or store it for potential reactivation.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Document each type of customer lifecycle event.<br><br> Make sure you capture step-by-step details of the process for each event. | By understanding the events, you can plan how to respond to each event in your solution design. <br> Clear instructions help human operators maintain consistency and serve as the foundation for future automation.|
| Communicate the shared responsibility between you and your customer for each lifecycle event. Communicate clearly and early on what actions you expect the customers to do in order to complete a lifecycle stage. | You can reduce potential errors and customer frustration caused by miscommunication. |
| Do capacity planning for each lifecycle event. <br><br> For example, when onboarding a new customer, plan to deploy a new instance of your application if existing instances lack sufficient capacity to handle the extra load. <br><br> For more information, see [Billing and cost management for SaaS workloads on Azure](./billing-cost-management.md). | You can support your ability to scale more easily and avoid deployment failures. |
| Automate your lifecycle events, when practical. <br><br> For low-volume or early-stage solutions, manual deployment and configuration might be sufficient, but should still use scripts, even if an engineer runs them each time a lifecycle event occurs. <br><br> As your solution matures, integrate these responsibilities into a full control plane to reduce human error and support higher scale. | You can reduce the significant risk of human error and support higher scale. |

## Plan your infrastructure management strategy

Develop a strategy for deploying, maintaining, and managing Azure infrastructure early on. As you scale your SaaS, the number of resources grows. It's easier to follow a management strategy from the start than to reconcile infrastructure later when it becomes too complex to handle manually.

### Design considerations

- **Customer resource management**. Your tenancy model affects resource deployment in SaaS solutions. You might deploy dedicated Azure resources for each customer or share resources among a set number of customers. Alternatively, you could use a single set of shared resources, reconfiguring them as you onboard new customers. Common approaches for managing the lifecycle of the resources:

  - Treat your customer list as a configuration of the resources to deploy. Use centralized deployment pipelines to deploy and configure those resources.
  - Treat your customer list as data. Use a control plane application to provision and configure infrastructure.

- **Infrastructure automation**. Many organizations start by manually deploying cloud infrastructure via the Azure portal, which is easy initially but doesn't scale well. Plan to automate your infrastructure setup using Infrastructure as Code (IaC) tools like Bicep or Terraform. For more complex requirements, create a control plane that uses Azure Resource Manager APIs directly.

- **Infrastructure attribution**. Keep track of which customers are deployed on which infrastructure. Tracking is important for accurate capacity planning and cost attribution. You can track customer infrastructure centrally in a customer database or, for dedicated infrastructure, use Azure resource metadata with customer-specific resource groups and resource tags. For more information, see [Resource organization for SaaS workloads](./resource-organization.md).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Build infrastructure automation using deployment pipelines, scripts, or templates with tools your team is already familiar with. | Using known tools reduces the risk of errors, as infrastructure automation can be disruptive if the tools aren't understood.|
| Deploy your infrastructure using IaC where possible. | Manually maintaining infrastructure becomes riskier and more burdensome as the amount of infrastructure grows. |
| Separate core infrastructure from customer-level infrastructure. | Different types of infrastructure have distinct lifecycles and management activities. By separating them, you can manage each set independently on its own schedule. |
| Use Azure Managed Applications to deploy and manage customer-deployed resources. | Azure Managed Applications provide a range of capabilities that enable you to deploy and manage resources within a customer's Azure subscription. |

## Plan for application deployments

Regularly update application code and configuration to enhance functionality. Customers expect consistent uptime during updates and safe rollouts to minimize the risk of outages.

### Design considerations

- **Standardize tooling and processes.** Industry-proven DevOps tooling ensures consistency across functions and maturity in processes for managing your application deployments. Developing your own tools is considered an antipattern in most situations.
    > Refer to [OE:03 Software development practices](../operational-excellence/formalize-development-practices.md).

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complexity and cost.** Using familiar DevOps tools can be cost-efficient in terms of money and skills. However, it adds the operational burden of managing each tool separately. It's important to remain open to new technological innovations that could benefit your workload.

- **Progressively deploy updates.** Roll out updates to a subset of customers at a time, dividing users into logical groupings. Apply the same rigor to configuration changes, because they can alter code behavior and cause outages. Follow a deployment process for these changes.

- **Adopt a versioning strategy.** Allowing customers to choose their application version adds flexibility but complicates your operations. Set clear expectations for deprecating old versions and outline what happens when they're no longer supported.

- **Automation.** Manual deployments are prone to risks due to human errors and lack of consistency. Even if your deployments are triggered manually, your deployment process should be automated as much as possible and should require minimal human intervention. Consider the steps of your deployment process and how to best automate them.

- **Test.** Integrate testing into your deployment process by running:

  - Unit tests during code build
  - Integration tests post-deployment
  - Regular performance tests
  - Regular security and penetration tests

Decide on actions to take if any tests fail at any stage.

- **Failed deployments.** Plan for deployment failures by considering necessary actions and preparing a rollback strategy.

- **Access to customer environments.** If you deploy resources into customer environments, understand how you can apply updates within those environments. Consider capabilities provided by Azure Managed Applications, such as [deployment of updates to applications](/azure/azure-resource-manager/managed-applications/update-managed-resources).

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Use established, industry proven DevOps tooling and processes to manage your application deployments. Developing your own tools is considered an antipattern in most situations. <br><br> For more information, see [OE:03 Software development practices](/azure/well-architected/operational-excellence/formalize-development-practices#establish-collaboration-and-communication-standards) | You can ensure that your engineering team deploys effectively by not having to learn custom built tooling. |
| Proactively notify customers of any upcoming or completed deployments. | You can ensure that proper expectations are set with your customers about changes coming to your application. |
| Adopt safe deployment practices that deploy updates to customer groups using strategies like progressive exposure, health models, and others. Start with less sensitive or early adopter customers before moving to more important ones. <br> For more information, see [Recommendations for safe deployment practices](/azure/well-architected/operational-excellence/safe-deployments). | This approach helps identify problems before they affect all customers. |
| Treat configuration as code. | You can reduce the likelihood of downtime and adopt a consistent process for production changes. This enables centralized operational responsibilities, such as testing changes and progressively rolling out updates to configuration and code. |
| Define a change management process and communicate a version update policy to make sure that customers know who triggers updates, their frequency, and conditions. <br><br> If customers are allowed to choose their application version, set clear guidelines for deprecating old versions. Minimize the number of application versions running in production. | Maintaining older versions causes operational inefficiency. Provide the necessary control for your customers while avoiding overburdening your team by setting clear expectations and policies. |
| Avoid customizing applications for a single customer. <br><br> To support different customer needs, you can either create various tiers of your solution or use feature flags to enable specific functionalities for certain users. | Avoid ambiguity about which features are deployed into which version, and reduce your maintenance burden.  |
| Have a rollback plan for failed deployments, including criteria for triggering and necessary approvals.|Rollback plans help to ensure that you can recover from deployment mistakes even in unforeseen circumstances.|
| Test your application regularly and at multiple stages in the software development process. Adopt a "shift-left" mindset and catch bugs and deviations early in the lifecycle. | Help prevent critical errors from affecting your customers. |

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about adopting DevOps practices:

- [Architectural approaches for the deployment and configuration of multitenant solutions](/azure/architecture/guide/multitenant/approaches/deployment-configuration)
- [Considerations for updating a multitenant solution](/azure/architecture/guide/multitenant/considerations/updates)
- [Considerations for multitenant control planes](/azure/architecture/guide/multitenant/considerations/control-planes)
- [Architectural approaches for control planes in multitenant solutions](/azure/architecture/guide/multitenant/approaches/control-planes)

## Next step

Learn about incident management considerations for implementing processes and tools that support a SaaS solution on Azure.

> [!div class="nextstepaction"]
> [Design area: Incident management for SaaS workloads on Azure](./incident-management.md)

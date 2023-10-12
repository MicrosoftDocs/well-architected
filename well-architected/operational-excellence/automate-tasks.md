---
title: Recommendations for implementing automation
description: Learn how to automate tasks that are repetitive and prone to human error to gain efficiency, adhere to standards, and create consistency.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for implementing automation

**Applies to this Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:09](checklist.md)| Automate all tasks that don't benefit from the insight and adaptability of human intervention, are highly procedural, and have a shelf-life that yields a return on automation investment. When possible, choose off-the-shelf software for automation versus custom implementations. Treat all automation the same as workload components, and apply the Well-Architected Framework pillars to their design and implementation.|
|---|---|

This guide describes the recommendations for adopting automation in your workload. You can automate tasks that are repetitive and prone to human error to help your teams gain efficiency and adhere to standards. Automate tasks to make your workload streamlined and consistent. Automation enables your operations and engineering teams to be more efficient because it gives them more time to work on other improvements. Automation is a powerful tool in all aspects of workload management. Thoughtfully implement automation to empower your organization.

## Key design strategies

As you develop your workload, look for opportunities to take advantage of automation in order to reduce management burden and minimize human error. Evaluate these opportunities, and consider the value that they bring to your organization. To maximize the value of your investment in automation, prioritize tasks that are straightforward, procedural, and have a long shelf life. Applying automation isn't an all-or-nothing tactic. There are workstreams that might have operations that require human intervention, like decision-making points. These workstreams can still benefit from automation to perform other tasks.

### Target tasks to automate

Consider the following recommendations to ensure that you prioritize tasks that benefit the most from automation:

- **Aim for easy wins.** Focus on tasks that are highly procedural and susceptible to human error. These tasks are highly automatable. They're clearly defined, they're free from variables that add complexity, and they're performed as part of normal operations. Conversely, don't prioritize automating tasks that require writing complex scripts to account for variable phenomena, or tasks that rarely occur.

  Examples of highly automatable tasks include rebooting servers, creating accounts, and transferring logs to a data store. These tasks might occur on a schedule, as a response to an event or monitoring alert, or as needed based on external factors.

- **Look for ways to empower operators and free up your SMEs.** You might have experts in your organization that are relied upon for escalations that might be unnecessary. For example, your database administrators might routinely get requests to create new databases when you onboard new customers to your multitenant solution. If you build a self-service portal for your help desk team, you can enable them to safely create an empty database themselves. Or as an intermediary step, you can automate the requests and the steps for the SME to perform by creating scripts to run.

- **Focus on your return on investment.** High-value automation requires minimal management overhead and adds a demonstrable degree of efficiency. If you can save your operations team an hour each day by automating database entries, for example, you give them time to find other areas for improvement.

### Areas to implement automation

Adopt automation throughout your entire workload lifecycle, from development to day-to-day management. Use the following list of examples to help you consider the broad areas of your workload lifecycle that can benefit from automation. You can automate:

- **Pipeline definition, execution, and management**: Use continuous integration and continuous delivery (CI/CD) tools, like Azure DevOps and other DevOps tools, to automatically define a pipeline and how it runs. These tools can help you automate CI/CD tasks or other tasks, like creating reports.

- **Deployments**: Use tools like Azure Resource Manager templates, Bicep, Terraform, and Ansible to automate your workload development and release processes. Deploy and update your infrastructure with the same automation platforms by using an infrastructure as code (IaC) approach.

- **Testing**: Many tools are available for automating your testing processes. These tools can relieve a significant burden from your quality assurance team and ensure that tests are standardized and reliable.

- **Scaling**: Use platform-provided functionality and other tools, like orchestration tools, to automatically scale your infrastructure when load increases or decreases.

- **Monitoring and alerting**: Use tooling that's available in your monitoring solution to automatically enroll newly deployed resources and configure alert-triggered actions to help hasten remediation when issues arise.

- **Self-healing**: Use alerts that are generated by your monitoring system to automate actions and recover malfunctioning components or jobs. For more information, see [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md).

- **Configuration management**: Use orchestration and policy tooling to ensure that all of your resources run the same configuration and that compliance requirements are enforced across your workload.

- **Other administrative tasks**: Use scripts to automate repetitive tasks like updating database records or DNS records.

- **Approvals**: Enable systems to automatically make approval decisions based on predefined rules to improve efficiency for workflows that have approval gates. This method encourages the use of standardized forms and templates, which increases the efficiency of the processes. Automatic approval in high environments can be risky. Tightly focus and test your automated approvals to ensure that specific criteria are defined to grant approval.

- **New user and new employee onboarding**: You can automate many tasks associated with onboarding new application users or new employees, like database updates and credential creation.

- **Monitoring and alerting**: Take advantage of the automation functionality that your observability platform provides. Automatically enroll new devices to monitor and alert on anomalies.

### Choose an appropriate automation tool

Developing your own automation in-house is time intensive and can introduce management burden to your development team. They need to maintain an in-house automation tool like they do any other in-house software. It's recommended that you use off-the-shelf tools whenever they can meet your needs. Between commercial, open source, and cloud platform provided tools, there are many options available.  It's likely that you'll use a variety of tools to build the automation that you need. Rely on your in-house expertise to help guide your decisions when evaluating tools. Your team might be more familiar with certain development languages and frameworks. You can initially focus on off-the-shelf tools that they can use without a high learning curve. Reflect on the tasks that you plan to address with automation, and invest in the tools that can specifically address those tasks. Don't procure tools that you generally prefer and then consider the tasks afterward.

Be mindful of factors that can complicate your operations when you build your automation, like version lock-in and plugin overuse. Plugins, like Jenkins or Azure DevOps plugins, are a great way to add functionality. You should adopt plugins when it benefits your automation goals. But when you use multiple plugins to perform a single task, it can make automation updates and troubleshooting difficult. Be judicious in your use of plugins. Also avoid solutions that have framework version dependencies because they're a burden to maintain over time. To help minimize the risk of these types of issues, standardize your selection of automation tools and plugins, and use source control for all automation projects.

### Integrate automation into your workload

For any tool that you use to build your automation, make it easily accessible and manageable for your operators. Provide clear and easy-to-use interfaces for your workload team. You can provide access to CI/CD pipelines, APIs, and libraries. Like the workload that the automation supports, you need to manage the automation holistically. Secure automation to the same degree as other workload components. Monitor automation and subject it to the same testing protocols as other workload components.

## Azure facilitation

Azure offers many tools to help you automate tasks for your workload.

**IaC tools**: You can use Terraform, Bicep, and Azure Resource Manager for IaC deployments. Depending on your requirements and your team's familiarity with the tools, you might use one or more of these tools for your deployments and management of resources.

**Azure Functions**: [Azure Functions](/azure/azure-functions/functions-overview) is a serverless tool that you can use to automate tasks by using your preferred development language. Functions provides a comprehensive set of event-driven triggers and bindings that connect your functions to other services. You don't have to write extra code.

**GitHub Actions for Azure**: You can use [GitHub Actions for Azure](https://azure.github.io/actions/#automate) to automate CI/CD processes. GitHub Actions integrates with Azure to simplify deployments. You can create workflows that build and test every pull request in your repository, or deploy merged pull requests to production.

GitHub Actions goes beyond just DevOps and enables you to run workflows when other events occur in your repository. For example, you can run a workflow to automatically add appropriate labels when someone creates a new issue in your repository.

**Azure Automation**: PowerShell and Python are popular programming languages for automating operational tasks. Use these languages to perform operations like restarting services, transferring logs between data stores, and scaling infrastructure to meet demand. You can express these operations in code and run them on demand. Alone, these languages don't offer a platform for centralized management, version control, or run history. The languages also lack a native mechanism for responding to events like monitoring-driven alerts. To provide these capabilities, you need an automation platform.

[Automation](/azure/automation/overview) provides an Azure-hosted platform for hosting and running PowerShell and Python code across cloud and on-premises environments, both Azure and non-Azure. PowerShell and Python code is stored in an Automation runbook. Use Automation to:

- Trigger runbooks on demand, on a schedule, or through a webhook.

- Run history and logging.

- Integrate a secrets store.

- Integrate source control.

**Azure Deployment Environments**: [Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments) enables development teams to quickly create consistent app infrastructure by using project-based templates. These templates minimize setup time and maximize security, compliance, and cost efficiency. A deployment environment is a collection of Azure resources that are deployed in predefined subscriptions. Development infrastructure administrators can enforce enterprise security policies and provide a curated set of predefined IaC templates.

Development infrastructure administrators define deployment environments as catalog items. Catalog items are hosted in a GitHub or Azure DevOps repository, called a *catalog*. A catalog item consists of an IaC template and a manifest.yaml file.

You can script the creation of deployment environments and programmatically manage the environments.

**Azure Logic Apps and Microsoft Power Automate**: When you build custom digital process automation (DPA) to handle workload tasks like approval flows or building ChatOps integrations, consider using [Logic Apps](/azure/logic-apps/) or [Power Automate](https://azure.microsoft.com/products/power-automate/). You can construct workflows from built-in connectors and templates. Logic Apps and Power Automate are built on the same underlying technology and are both well-suited for trigger-based or time-based tasks.

**Automatic scaling**: Many Azure technologies have built-in automatic scaling capabilities. You can also program other services to automatically scale by using APIs. For more information, see [Recommendations for designing a reliable scaling strategy](../reliability/scaling.md).

**Azure Monitor action groups**: To automatically run self-healing operations when an alert is triggered, use [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups). You can define these operations by using a runbook, an Azure function, or a webhook.

## Tradeoffs

Sometimes the efficiencies you gain from automation outweigh the management burden of developing your own solution if no off-the-shelf solutions fit your requirements. In these cases, be judicious in your development efforts. Narrowly focus on developing only what you need to cover gaps that you can't solve with off-the-shelf solutions, and minimize complexities like dependencies.

Complex automation that requires a high degree of maintenance can be difficult for operations teams to manage and troubleshoot. Keep automated tasks tightly focused on only performing discrete jobs. Try to minimize dependencies on other tools or components.

Be thoughtful about using manual processes. If you decide not to automate an operation, thoroughly document the manual process by creating a step-by-step checklist for operators. This practice reduces the chances of human error, like an operator mistakenly running the wrong process. This documentation also helps you design automation for that process in the future.

When you use a hybrid manual and automated approach, you need to be especially careful. If a script runs most of a process but then defers to a human for a specific part or decision, it's important that you give the person the necessary context and information to make an informed decision.

## Example

For an example of using Automation in tandem with other Azure services, see [Ops automation by using Azure Event Grid](/azure/architecture/solution-ideas/articles/ops-automation-using-event-grid). This example uses Logic Apps and Event Grid to automate operational tasks.

## Related links

- [Automation](/azure/automation/overview)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups)
- [Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments)
- [GitHub Actions for Azure](https://azure.github.io/actions/#automate)
- [Logic Apps](/azure/logic-apps/)
- [Ops automation by using Event Grid](/azure/architecture/solution-ideas/articles/ops-automation-using-event-grid)
- [Power Automate](https://azure.microsoft.com/products/power-automate/)
- [Recommendations for designing a reliability testing strategy](../reliability/testing-strategy.md)
- [Recommendations for designing a reliable scaling strategy](../reliability/scaling.md)
- [Recommendations for self-healing and self-preservation](../reliability/self-preservation.md)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

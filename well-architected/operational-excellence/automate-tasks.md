---
title: Recommendations for implementing automation
description: Learn how to implement automation in your workload management. Automate tasks that are repetitive and prone to human error to gain efficiency and adhere to standards, making the management of the workload less complicated and more consistent.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for implementing automation

This guide describes the best practices for adopting automation in your workload management. Automating tasks that are repetitive and prone to human error can help your teams gain efficiency and adhere to standards, making the management of the workload less complicated and more consistent. Additionally, you can empower your operations and engineering teams by giving them more time to concentrate on workload or other improvements. Automation is a powerful tool in all aspects of workload management and implementing it thoughtfully will empower your organization in many ways.

## Key design strategies

As you develop your workload, there will be many opportunities to leverage automation to reduce management burden and minimize the risk of human error when performing operational tasks. Evaluate these opportunities from the perspective of the value they bring to your organization. To maximize the value of your investment in automation, be thoughtful about your approach by focusing on those tasks that are straightforward, procedural and have a long shelf life. Be mindful that applying automation is not an all-or-nothing tactic; there will be workstreams that may have one or more operations that require human intervention, like decision-making points, but may still benefit from automation to perform the actions associated with the operations.

### Targeting tasks to automate

Use the following recommendations to ensure that you are targeting the tasks that will benefit most from automation:

- **Aim for easy wins:** Focus first on the tasks that are both highly procedural and susceptible to human error. These tasks are highly automatable, meaning they are clearly defined, free from variables that add complexity, and they are performed as part of normal operations. Conversely, do not prioritize automating tasks that would require writing complex scripts to account for variable phenomena or tasks that happen rarely.

  - Some examples of highly automatable tasks include rebooting servers, creating accounts, and shipping logs to a data store. These tasks may occur on a schedule, as a response to an event or monitoring alert, or ad-hoc based on external factors.

- **Look for ways to empower operators and free up your SMEs:** You may have experts in your organization that are relied upon for escalations that may be unnecessary. For example, your database administrators might routinely get requests to provision new databases when you onboard new customers to your multi-tenant solution. Building a self-service portal for your help desk team could allow them to safely create an empty database themselves. Or as an intermediary step, you could automate the requests and the steps for the SME to perform through scripts that would be run.

- **Focus on your return on investment:** High value automation requires very little management overhead and adds a demonstrable degree of efficiency. If you can save your operations team an hour each day by automating database entries, for example, you will give them time to find other areas for improvement.

### Workload aspects that can benefit from automation

Plan to adopt automation throughout the entire workload lifecycle, from development through day-to-day management. The following list of examples is not exhaustive and is meant to help you think about the broad areas of your workload lifecycle that can benefit from automation.

- **Automated pipeline definition, execution and management**: CI/CD tools like Azure DevOps and other DevOps tools allow you to automatically define a pipeline and its execution. This can help you automate CI and CD tasks or other tasks like creating reports.

- **Automated deployments**: Use tools like ARM templates, Bicep, Terraform and Ansible to automate your workload development and release processes. Deploy and update your infrastructure through the same automation platforms using an infrastructure-as-code approach.

- **Automated testing:** Many tools are available for automating your testing processes. These can relieve a significant burden from your quality assurance team and ensure that tests are standardized and reliable.

- **Automatic scaling**: Make use of platform-provided functionality and other tools (like orchestration tools for example) to automatically scale your infrastructure when load increases or decreases.

- **Automated monitoring and alerting**: Use tooling available in your monitoring solution to automatically enroll newly deployed resources and configure alert-triggered actions to help hasten remediation when issues arise.

- **Automated self-healing:** Using alerts generated by your monitoring system, automate actions to recover malfunctioning components or jobs. See the (self-preservation reliability guide) for more information.

- **Automated configuration management**: Make use of orchestration and policy tooling to ensure that all of your resources are running the same configuration and that compliance requirements are enforced across your workload.

- **Automation of other administrative tasks**: Use scripts to automate repetitive tasks like updating database records or DNS records.

- **Automated approvals:** You can make workflows that have approval gates more efficient by allowing systems to make approval decisions automatically based on predefined rules. This will also encourage the use of standardized forms and templates which will further increase the efficiency of the processes. Be judicious with this kind of automation, as there are risks with automatically granting approval in higher environments. Ensure that your automated approvals are very tightly focused and tested to ensure that only very specific criteria are defined to grant approval.

- **Automate new user and new employee onboarding:** Many of the tasks involved in onboarding new application users or new employees can be automated, like database updates and credential creation, for example.

- **Automate monitoring and alerting to the extent possible:** Take advantage of the automation functionality provided by your observability platform to automatically enroll new devices into monitoring and alert on anomalies.

### Choosing the right automation tool

Developing your own automation in-house is time intensive and can introduce more management burden to your development team as they will need to maintain it as they would any other in-house software. For that reason, prefer off-the-shelf tools whenever they can meet your needs. Between commercial, open source and cloud platform provided tools, there are many options available, and it is likely that you will use a variety of them to build the automation that you need. Rely on your in-house expertise to help guide your decisions when evaluating tools. Your team may have more familiarity with certain development languages and frameworks, and you can initially focus on off-the-shelf tools that they can use without a high learning curve. Finally, reflect on the tasks that you plan to address with automation and invest in the tools that can specifically address those tasks rather than procuring tools that you prefer generally and then thinking about the tasks afterward.

Be mindful of factors that can complicate your operations when building your automation like version lock-in and plugin overuse. Plugins (like Jenkins or Azure DevOps plugins for example) are a great way to add functionality easily, and you should adopt them when it benefits your automation goals. Be mindful that using multiple plugins to perform any single task can make updates to the automation difficult and issues hard to troubleshoot, so be judicious in your use of plugins. Likewise, avoid solutions that have framework version dependencies as they will become a burden to maintain over time. To help minimize the risk of these types of issues, standardize on your selection of automation tools and plugins and use source control for all of your automation projects.

### Treat your workload automation as a part of your workload

No matter which tools you use to build your automation, focus on making it easily accessible and manageable for your operators. To help make it accessible and manageable, provide clear and easy-to-use interfaces for your workload team. Useful interfaces can include providing access to CI/CD pipelines, APIs and libraries. Like the workload the automation supports, the automation needs to be managed holistically: it should be secured to the same degree as all other workload components, it should be monitored alongside other workload components and it should be subject to the same testing protocols as the other workload components.

## Azure facilitation

Azure offers multiple tools to help you automate tasks for your workload.

**IaC tools**

Terraform, Bicep and ARM can all be used for IaC deployments. Depending on your requirements and your team's familiarity with the tools, you may use one or more of these tools for your deployments and management of the resources.

**Azure Functions**

[Azure Functions](/azure/azure-functions/functions-overview) is a serverless tool that allows you to easily automate tasks using the development language you prefer. Functions provides a comprehensive set of event-driven triggers and bindings that connect your functions to other services without having to write extra code.

**GitHub Actions for Azure**

[GitHub Actions for Azure](https://azure.github.io/actions/#automate) allows you to automate CI/CD processes and integrates directly with Azure to simplify deployments. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

GitHub Actions goes beyond just DevOps and lets you run workflows when other events happen in your repository. For example, you can run a workflow to automatically add the appropriate labels whenever someone creates a new issue in your repository.

**Azure Automation**

PowerShell and Python are popular programming languages for automating operational tasks. Using these languages, performing operations like restarting services, moving logs between data stores, and scaling infrastructure to meet demand can be expressed in code and executed on demand. Alone, these languages do not offer a platform for centralized management, version control, or execution history. The languages also lack a native mechanism for responding to events like monitoring driven alerts. To provide these capabilities, an automation platform is needed.

[Azure Automation](/azure/automation/overview) provides an Azure-hosted platform for hosting and running PowerShell and Python code across Azure, non-Azure cloud, and on-premises environments. PowerShell and Python code is stored in an Azure Automation Runbook, which has the following attributes:

- Execute Runbooks on demand, on a schedule, or through a webhook.

- Execution history and logging.

- Integrated secrets store.

- Source Control integration.

**Azure Deployment Environments**

[Azure Deployment Environments](/azure/deployment-environments/overview-what-is-azure-deployment-environments) enables development teams to quickly spin up consistent app infrastructure by using project-based templates, minimizing setup time while maximizing security, compliance, and cost efficiency. A deployment environment is a collection of Azure resources deployed in predefined subscriptions. Development infrastructure (dev infra) admins can enforce enterprise security policies and provide a curated set of predefined infrastructure as code (IaC) templates.

Dev infra admins define deployment environments as catalog items hosted in a GitHub or Azure DevOps repository called a catalog. A catalog item consists of an IaC template and a manifest.yaml file.

The creation of deployment environments can be scripted, and the environments can be managed programmatically.

**Logic Apps and Power Automate**

When building custom automation digital process automation (DPA) to handle workload tasks like approval flows or building ChatOps integrations, consider the use of [Logic Apps](/azure/logic-apps/) or [Power Automate](https://azure.microsoft.com/products/power-automate/) from the Microsoft Power Platform. Workflows can be easily constructed from built-in connectors and templates. Both Logic Apps and Power Automate are built on the same underlying technology and are both well suited for trigger-based or time-based tasks.

**Autoscaling**

Many Azure technologies have built-in autoscaling capabilities and many others can be programmed to automatically scale using APIs. Refer to the (Reliabilty scaling guide) for more information.

**Azure Monitor action groups**

Use [Azure Monitor action groups](/azure/azure-monitor/alerts/action-groups) to automatically run self-healing operations when an alert is triggered. These operations can be defined by a runbook, an Azure Function or a webhook.

## Tradeoffs

There may be times when the efficiencies you will gain from automation outweigh the management burden of developing your own solution when no off-the-shelf solutions fit the requirements. In these cases, be judicious in your development efforts; narrowly focus on developing only what you need to cover gaps that can't be solved with off-the-shelf solutions and minimize complexities like dependencies

Complex automation that requires a high degree of maintenance can be difficult for operations teams to manage and troubleshoot. Strive to keep automated tasks tightly focused on only performing discreet jobs and try to minimize dependencies on other tools or components.

Be thoughtful about using manual processes. If you decide not to automate something, be sure to thoroughly document the manual process by creating a step-by-step checklist that operators manually execute. This practice reduces the chances of human error, like an operator mistakenly running the wrong process. The documentation will also help you design automation for that process in the future if you decide to automate it later.

When using a hybrid manual and automated approach, you need to be especially careful. If a script runs the majority of a process but then defers to a human for a specific part or a decision, it's very important that you give the human the necessary context and information to be able to make an informed decision.

## Example

Refer to the [Ops automation using Event Grid](/azure/architecture/solution-ideas/articles/ops-automation-using-event-grid) solution idea for an example of using Azure Automation in tandem with other Azure services like Logic Apps and Event Grid to automate operational tasks.

## Related links

Reliability scaling guide (re05-scaling)

Reliability testing guide (re07-testing)

Reliability self-preservation guide (re06-self-preservation)

## Next steps

We recommend that you review the Operational Excellence checklist to explore other concepts. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 
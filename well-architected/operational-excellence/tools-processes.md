---
title: Recommendations for standardizing tools and processes
description: Learn how to optimize development practices by standardizing tools and processes. Define consistent practices to optimize efficiency and quality of work.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for standardizing tools and processes

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:04](checklist.md)| Optimize software development and quality assurance processes by following industry-proven practices for development and testing. For unambiguous role designation, standardize practices across components such as tooling, source control, application design patterns, documentation, and style guides. |
|---|---|

**Related guide**: [Improve build velocity](release-engineering-performance.md) | [Use continuous integration](release-engineering-continuous-integration.md)

This guide describes the recommendations for defining standards for software development tools and processes. Defining consistent practices leads to an efficient workload team and high-quality work. High-performing teams use industry-proven tools and processes to minimize wasted effort and potential code errors.

## Key design strategies

The first step of optimizing development practices is standardizing tools and processes. When possible, use industry-proven solutions rather than developing in-house solutions. To further optimize your practices, adopt low-code and no-code tools. These tools enable you to focus efforts on your application and help you save time. For all tools and processes that you standardize, implement training so your teams understand and use them efficiently. To define standards that help optimize your development practices, consider the following recommendations.

**Use well-known and mature off-the-shelf tools**, and standardize their use. Highly effective engineering teams adopt the best-in-class tools. This approach minimizes the need to develop solutions for planning, development, testing, collaboration, and continuous integration and continuous delivery (CI/CD). Many enterprises give developers a choice between a few tools, but all options are standard tools for the organization and are validated internally. Most importantly, choose tools that meet the requirements for your workload. Off-the-shelf tools should provide the following functions:

- Work planning and backlog management

- Version control and repositories

- CI/CD pipelines

- Testing, such as integration, smoke, synthetic user, simulation, chaos, and other quality tests

- Code development

In some cases, one tool or a suite of tools might provide several functions. Ensure that you understand the capabilities of your tools and their limitations so they meet your requirements across functions.

Determine if you should invest in expensive tools or premium versions of tools. Consider the time and effort of developing your own solutions compared to features that the premium tools provide. Consider one-time costs versus recurring costs. In most cases, off-the-shelf tools provide higher value to your team.

**Use low-code, no-code, and AI tools** when practical. Low-code and no-code tools save experienced developers time by allowing them to easily plug in functionality rather than performing the entire code development process. These tools also allow workload team members that might not be trained developers to contribute to the operation of the workload. AI tools can help with code development, reviews, and optimization.

**Standardize your branching strategy.** Choose a trunk-based model when possible. Trunk-based branching keeps the workload development team in sync and encourages continuous delivery. Define branch policies to protect important branches, like the main branch. For more information, see [Adopt a Git branching strategy](/azure/devops/repos/git/git-branching-guidance) and [Branch policies and settings](/azure/devops/repos/git/branch-policies).

**Evaluate metrics to quantify effectiveness.** Software development and quality assurance teams can only improve if they can quantify their effectiveness. To quantify effectiveness, they must identify the metrics that measure [developer velocity](https://azure.microsoft.com/solutions/developer-velocity) and define KPIs. Examples of these metrics include:

- *Deployment frequency*: The number of deployments that each developer deploys each day.

- *Lead time*: The time that it takes for a task or user story to go from the backlog to a production deployment.

- *Mean time to resolution*: The average time that's spent fixing bugs or defects in code.

- *Change failure rate*: The percentage of changes that result in a failure.

To help stakeholders and the workload team easily track velocity, visualize KPIs by using dashboards or other reporting tools.

**Standardize how your workload team writes, reviews, and documents code** by using a style guide. A standard style makes collaboration easy and helps with on-boarding new developers. To work effectively, new developers need to know how the workload team operates. A style guide with clearly defined standards can ease their training process. In the style guide, define standards for development languages, libraries, frameworks, and other conventions.

When it's practical, use tooling to enforce code-formatting standards. For example, Visual Studio offers several [tools](/visualstudio/code-quality/roslyn-analyzers-overview) that scan code for style, quality, maintainability, design, and other issues. For infrastructure as code (IaC), you can use Checkov or Terrascan for Terraform.

To ensure consistency and avoid potential confusion, the style guide should include standard naming conventions for artifacts, environments, branches, builds, and runs.

You should also set guidelines and standards for the allowable degree of variance in your environments. If there are new languages, frameworks, or other technologies that workload team members want to add to the standard list, implement a process for using those tools in a sandbox or lower environment. Test their viability, and replace existing technologies when appropriate.

**Use architecture decision records (ADRs)** to keep a historical record of your workload team's design decisions. ADRs help your teams maintain a fresh understanding of the workload. They also help new team members learn about the design decisions that are made during the workload's lifecycle. Ensure that ADRs are version controlled.

In your ADR, include:

- Specific tools and technologies, for example using SQL or NoSQL, that your team chooses.

- The reasons for your team's decisions.

- Other options that were considered, which helps contextualize the final decision.

- Functional and nonfunctional requirements that are factored into decisions.

- The context of the decision-making process, like the problem that was addressed.

**Implement standards and guidelines for addressing technical debt.** Adopt a mindset that technical debt is intentional and necessary for your workload team's deliverables. This mindset motivates your team to consider and address technical debt regularly to avoid accumulation. Address technical debt as a regularly recurring task in the backlog.

For example, suppose your team standardized on a library. Over time, you need to switch to a different library for new functionality in the workload. That transition might result in technical debt. Frequently, transitions like this can leave the workload team supporting two technologies because they can't fully transition smoothly. The workload team must prioritize completing the transition because when the workload achieves the new functionality, stakeholders are satisfied and are less likely to consider the technical debt.

**Standardize how you apply versioning to your artifacts** and how versioning is exposed internally and externally. For example, client-facing systems should expose their running version in the user interface. This technique is helpful when the workload team troubleshoots issues because the customer can easily communicate which version they use. REST interfaces can expose versions for certain components or databases. You might use a specific table in the metadata for a schema to expose the schema version.

**Use industry-proven application [design patterns](/azure/architecture/patterns)** to ensure that your application is reliable, performant, and secure. Use these patterns to save time and effort compared to developing your own solutions for your application. Choose the patterns that benefit your workload. Regularly review design patterns to ensure that you use the right patterns as your workload evolves.

**Implement a shift-left approach to testing** by performing unit testing early and often throughout the development process. Frequent testing in each development environment helps developers gain confidence in their applications. To help create your testing strategy with a shift-left approach, consider the following principles:

- *Write tests at the lowest level possible*. Favor tests with the fewest external dependencies, and run tests as part of the build.

- *Write tests once, and run tests everywhere, including production*. Write tests that you can run in every development environment without accounting for factors that are specific to one environment, like encrypted secrets or configurations.

- *Design your workload for testing*. When you develop your application, make testability a requirement.

- *Treat test code as application code*. Apply the same quality and development standards to application code and test code. Store test code alongside application code. Develop and maintain test code with application code. To ensure the quality of tests, discard tests that aren't reliable.

- *Consider test ownership, which is based on workload ownership*. Your workload team owns their testing and shouldn't rely on other teams to test their code.

- *Automate tests as much as possible*. Automated code relieves the burden on your workload team and enforces consistent quality.

For detailed guidance about implementing a DevOps test strategy, see [Shift testing left with unit tests](/devops/develop/shift-left-make-testing-fast-reliable).

**Require [DevSecOps](/devops/operate/security-in-devops) practices as part of your standard operating procedures.** Your workload team should understand the security practices related to software development and quality assurance. They must follow these practices without exception. For more information, see [Security development lifecycle guide](../security/secure-development-lifecycle.md).

## Azure facilitation

- [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) is a collection of services that you can use to build a collaborative, efficient, and consistent development practice. Azure DevOps bundles the following solutions:

  - Azure Pipelines provides build and release services to support the CI/CD of your applications.

  - [Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards) is a web-based work-management tool that supports Agile practices like Scrum and Kanban.

  - Azure Repos is a version control tool that supports the [Git distributed version control system](/azure/devops/repos/get-started/what-is-repos#git) and the [Team Foundation Version Control](/azure/devops/repos/get-started/what-is-repos#tfvc) system.

  - [Azure Test Plans](/azure/devops/test/overview) is a browser-based test management solution that provides capabilities that are required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

  - [Azure Artifacts](/azure/devops/artifacts/start-using-azure-artifacts) is used to enable developers to efficiently share their code and manage their packages.

- [GitHub Actions for Azure](https://azure.github.io/actions) is a tool that you can use to automate CI/CD processes. It integrates directly with Azure to simplify deployments. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

- [GitHub Projects](https://docs.github.com/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) is a work-management tool that you can use to create Kanban boards, reports, dashboards, and other functions.

- Low-code and no-code tools include:

  - [Azure Logic Apps](/azure/logic-apps/logic-apps-overview)

  - [Azure Functions](/azure/azure-functions/functions-overview)

  - [Microsoft Power Platform](https://powerapps.microsoft.com/low-code-platform)

- [Azure Resource Manager templates](/azure/azure-resource-manager/templates/overview) and [Bicep](/azure/azure-resource-manager/bicep/overview) are Azure-native tools that you can use to deploy IaC. [Terraform](/azure/developer/terraform/overview) is another Azure-supported IaC tool that you can use to deploy and manage infrastructure.

- [Visual Studio](/visualstudio/windows) is a robust development tool that integrates with Azure and supports many languages.

- [GitHub Copilot](https://docs.github.com/copilot/overview-of-github-copilot/about-github-copilot-for-individuals) is an AI service that acts as a pair programmer and provides autocomplete style suggestions as you code. Copilot is available as an extension in Visual Studio and several other development tools.

- [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) is a fully managed load testing service that you can use to generate high-scale load by simulating traffic for your applications, regardless of where they're hosted.

## Tradeoffs

Investing in powerful commercial tools can be costly, so choose the appropriate tools to meet your requirements.

## Related links

- [Adopt a Git branching strategy](/azure/devops/repos/git/git-branching-guidance)
- [Branch policies and settings](/azure/devops/repos/git/branch-policies)
- [Cloud design patterns](/azure/architecture/patterns)
- [Developer velocity](https://azure.microsoft.com/solutions/developer-velocity)
- [DevOps resource center](/devops)
- [Enable DevSecOps with Azure and GitHub](/devops/devsecops/enable-devsecops-azure-github)
- [Overview of source code analysis](/visualstudio/code-quality/roslyn-analyzers-overview)
- [Security development lifecycle guide](../security/secure-development-lifecycle.md)
- [Security in DevOps (DevSecOps)](/devops/operate/security-in-devops)
- [Shift testing left with unit tests](/devops/develop/shift-left-make-testing-fast-reliable)
- [Video series: Introduction to GitHub CoPilot](/shows/introduction-to-github-copilot/what-is-github-copilot-1-of-6)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

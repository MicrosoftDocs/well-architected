---
title: Recommendations for optimizing development practices through standardization
description: Learn how to optimize your development practices by standardizing your tools and processes.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for optimizing development practices through standardization

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:04](checklist.md)| Optimize software development and quality assurance processes by following industry-proven practices for development and testing. For unambiguous role designation, standardize practices across components such as tooling, source control, application design patterns, documentation, and style guides. | 
|---|---| 

This guide describes the recommendations for defining standards for your software development tools and processes. Defining consistent practices will lead to a more productive workload team and higher quality work than can be achieved with this structure. High-performing teams use industry-proven tools and processes to minimize wasted effort and risk of introducing risks in their code.

## Key design strategies

Optimizing your development practices starts with standardization of tools and processes. Moreover, your standard tools and processes should be industry-proven solutions rather than developed in-house as much as possible. This standardization using existing tools extends to adopting low-code and no-code tools which further optimizes your practices by allowing you to focus your efforts on your application rather than problems that have already been solved. For all of the tools and processes that you standardize on, training is essential for the team to become familiar and efficient in their use. Consider the following recommendations to develop the standards that will help optimize your development practices:

-   Use well-known and mature off-the-shelf tools and standardize their use. Highly effective engineering teams minimize the development of bespoke solutions for planning, development, testing, collaboration and continuous integration and delivery (CI/CD) by adopting best-in-class tools. Choose tools that meet specific requirements for your workload first and foremost. Many enterprises give developers some degree of choice between a few tools, but all of the tools that can be chosen are standard tools for the organization and have been validated internally. The following functions are some of those that should be provided by off-the-shelf tools

    -   Work planning / backlog management

    -   Version control and repositories (repos)

    -   CI/CD pipelines

    -   Testing (integration, smoke, synthetic user, simulation and chaos testing among other quality tests)

    -   Code development

    In some cases, one tool or suite of tools may provide many functions, but ensure that you understand your tools capabilities and limitations to make sure that they meet your requirements across functions.
    
    When deciding on whether to invest in more expensive tools or premium versions of tools, weigh the time and effort involved in developing your own solutions that would otherwise be provided by those tools vs the one-time and recurring costs. In most cases, the off-the-shelf tools will be the better value to your team.
    
-   Use low-code, no-code, and artificial intelligence (AI) tools when practical. Low-code and no-code tools not only save experienced developers time by allowing them to easily plug in functionality rather than going through the entire code development process, but they also allow other workload team members that may not be trained developers to contribute to the operation of the workload. AI tools can help with code development, review, and optimization.

-   Standardize your branching strategy, preferring a trunk-based model. Trunk-based branching keeps the workload development team in sync at all times and encourages continuous delivery. Define branch policies to protect the important branches, like main. See the [Adopt a Git branching strategy](/azure/devops/repos/git/git-branching-guidance) article for detailed guidance about building an effective branching strategy and the [Branch policies and settings](/azure/devops/repos/git/branch-policies) article for guidance about creating branch policies.

-   Software development and quality assurance teams can only improve if they can quantify their effectiveness. To be able to quantify effectiveness, they must identify the metrics that can be used to measure [developer velocity](https://azure.microsoft.com/solutions/developer-velocity/) and define KPIs. Examples of these metrics include:

    -   **Deployment frequency**: the number of deployments per developer, per day

    -   **Lead time**: how long it takes for a task or user story to go from being added to the backlog to a production deployment

    -   **Mean time to resolution**: the average time spent fixing bugs or defects in code

    -   **Change failure rate**: the percentage of changes that result in a failure of some kind

    Visualize KPIs using dashboards or other reporting tools to help stakeholders and the workload team easily track velocity.

-   Standardize the way that the workload team writes, reviews, and documents code through a style guide. A standard style will make collaboration easier and help with on-boarding new developers. New developers need to know how the workload team operates to be effective and standards that are clearly laid out in a style guide will ease their ramping process. The style guide should define standards for development languages, libraries, frameworks and other conventions.

    Use tooling when practical to enforce code formatting standards. For example, Visual Studio offers several [tools](/visualstudio/code-quality/roslyn-analyzers-overview) to scan code for style, quality, maintainability, design, and other issues. Likewise, for IaC code, you can use Checkov or Terrascan for Terraform.

    The style guide should include standard naming conventions for artifacts, environments, branches, builds and runs to ensure consistency and avoid potential confusion among the team.
    
    You should also set guidelines and standards for the allowable degree of variance in your environments. If there are new languages, frameworks or other technologies that workload team members are interested in adding to the standard list, there should be a standardized process for using those tools in sandbox and lower environments to test their viability and replacing existing technologies when appropriate.

-   Use architecture decision records (ADRs) to keep a historical record of the design decisions that the workload team makes. ADRs should be version controlled and should document specific tools and technologies that have been chosen (using SQL or NoSQL for example) and specific reasons why they were chosen. Be sure to include the other options that were considered to help contextualize the reasons for the choices that were made. Also include functional and non-functional requirements that contributed to decisions and a description of the context for the decision-making process (what was the problem that was being addressed). ADRs help teams keep a fresh understanding of the workload and especially help new team members learn about the design decisions that have been made over the course of the workload's lifecycle.

-   Have standards and guidelines for addressing technical debt. The workload team should adopt a mindset that technical debt is intentional and necessary for their deliverables. By adopting this mindset, the team can be motivated to care about and address technical debt regularly to avoid accumulation. Include work to address technical debt as a regularly recurring task in the backlog. As an example, if your team has standardized on a specific library and over time it becomes necessary to move to a different library to add new functionality to the workload that transition may result in technical debt. Oftentimes, transitions like this can leave the workload team supporting two technologies because they are unable to fully transition smoothly. The workload team must prioritize completing the transition because once the workload has achieved the new functionality stakeholders will be satisfied and will be less likely to care about the technical debt.

-   Standardize how you apply versioning to your artifacts and how versioning is exposed internally and externally. For example, client facing systems should expose their running version through the user interface, which is helpful for the workload team when troubleshooting issues, as the end-user can easily communicate which version they are using. Other ways of exposing the version would be REST interfaces for certain components or for databases, you may use a specific table in the metadata for a schema to expose the schema version.

-   Use industry-proven application [design patterns](/azure/architecture/patterns/) to ensure that your application is reliable, performant and secure. Using these patterns will save you time and effort in developing your own optimizations for your application. Choose the patterns that will benefit your workload and review design patterns regularly to ensure that as your workload evolves, you are using the right patterns for the current state and planned future state.

-   Take a shift-left approach to testing by performing unit testing early and often throughout the development process. Frequent testing in each development environment helps developers gain confidence in their applications. The following principles will help you build your testing strategy with a shift-left approach:

    -   **Tests should be written at the lowest level possible.** favor tests with the fewest external dependencies and focus on running most tests as part of the build.

    -   **Write once, run everywhere including production.** write tests that can be run in every development environment without modifications to account for things like encrypted secrets or configurations specific to one environment.

    -   **The workload is designed for testing.** When developing your application, testability should be a requirement.

    -   **Treat test code as application code.** The same attention to quality and the same development standards should apply to application code and test code. Test code should be stored alongside application code and should be built and maintained with the application code. Be rigorous with your dedication to quality tests by discarding tests that do not prove reliable.

    -   **Test ownership follows workload ownership.** the workload team owns their testing. The workload team should not rely on other teams to test their code.

    -   **Automate tests to the extent practical.** automated code relieves burden on the workload team and enforces consistent quality.

    See the [Shift left with unit tests](/devops/develop/shift-left-make-testing-fast-reliable) article for detailed guidance about implementing a DevOps test strategy.

-   [DevSecOps](/devops/operate/security-in-devops) practices should be required as part of your standard operating procedures. Everyone on the workload team should understand the security practices related to software development and quality assurance and must adopt the mindset these practices must be followed without exception. See the [security development lifecycle guide](../security/secure-development-lifecycle.md) for detailed guidance on DevSecOps practices.

## Azure facilitation

-   [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops) is a collection of services that help you build a collaborative, efficient, and consistent development practice. Azure DevOps bundles the following solutions:

    -   Azure Pipelines provides build and release services to support continuous integration and delivery of your applications.

    -   [Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards?view=azure-devops&preserve-view=true) is a web-based work management tool that supports Agile practices like Scrum and Kanban boards.

    -   Azure Repos is a version control tool that supports the [Git](/azure/devops/repos/get-started/what-is-repos?view=azure-devops#git&preserve-view=true) distributed version control system and the [Team Foundation Version Control](/azure/devops/repos/get-started/what-is-repos?view=azure-devops#tfvc&preserve-view=true) centralized version control system.

    -   [Azure Test Plans](/azure/devops/test/overview?view=azure-devops&preserve-view=true) is an easy-to-use, browser-based test management solution that provides all the capabilities required for planned manual testing, user acceptance testing, exploratory testing, and gathering feedback from stakeholders.

    -   [Azure Artifacts](/azure/devops/artifacts/start-using-azure-artifacts?view=azure-devops&preserve-view=true) enables developers to share their code efficiently and manage all their packages from one place.

-   [GitHub for Actions for Azure](https://azure.github.io/actions/) allows you to automate CI/CD processes and integrates directly with Azure to simplify deployments. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production. 

-   [GitHub Projects](https://docs.github.com/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) is a work management tool that you can use to create Kanban boards, reports, dashboards and other functions.

-   Low-code and no-code tools include:

    -   [Azure Logic Apps](/azure/logic-apps/logic-apps-overview)

    -   [Azure Functions](/azure/azure-functions/functions-overview)

    -   [Microsoft Power Platform](https://powerapps.microsoft.com/low-code-platform/)

-   [Azure Resource Manager templates (ARM templates)](/azure/azure-resource-manager/templates/overview) and [Bicep](/azure/azure-resource-manager/bicep/overview?tabs=bicep) are Azure-native tools to deploy infrastructure-as-code (IaC). [Terraform](/azure/developer/terraform/overview) is another IaC tool that is fully supported in Azure that can be used to deploy and manage infrastructure.

-   [Visual Studio](/visualstudio/windows) is a robust development tool supporting numerous languages with native Azure integration.

-   [GitHub Copilot](https://docs.github.com/copilot/overview-of-github-copilot/about-github-copilot-for-individuals) is an AI service that acts as a pair programmer, offering auto-complete style suggestions as you code. It is available as an extension in Visual Studio and several other development tools.

-   [Azure Load Testing](/azure/load-testing/overview-what-is-azure-load-testing) is a fully managed load-testing service that enables you to generate high-scale load by simulating traffic for your applications, regardless of where they\'re hosted.

## Tradeoff

Investing in powerful commercial tools can be costly, so ensure that you choose just the right tools to meet your requirements.

## Related links

[Introduction to GitHub CoPilot](/shows/introduction-to-github-copilot/): a six-part video series providing instructions for using GitHub Copilot.

[DevOps resource center - Azure DevOps](/devops/): learn more about DevOps practices and how Microsoft applies them.

[Cloud design patterns - Azure Architecture Center](/azure/architecture/patterns/): learn more about design patterns

[Enable DevSecOps with Azure and GitHub - Azure DevOps](/devops/devsecops/enable-devsecops-azure-github): learn more about Azure and GitHub tools to apply DevSecOps practices

## Operational Excellence checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"] 
> [Operational Excellence checklist](checklist.md) 
---
title: Recommendations for formalizing software development management practices
description: Learn how to establish standards for managing your workload team's software development practices.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for formalizing software development and management

**Applies to this Azure Well-Architected Framework Operational Excellence checklist recommendation:**

|[OE:03](checklist.md)| Formalize software ideation and planning processes. Draw from established industry and organizational standards. Use a common, prioritized backlog and sufficiently detailed specifications. Based on outcomes, drive continuous improvements in your planning process. |
|---|---|

This guide describes the recommendations for managing your team's software development practices via established standards. A team's ability to produce high quality software relies on a structured, collaborative approach to development planning. Product owners and managers must be able to clearly understand and articulate to stakeholders the work that developers are doing at any time in a development cycle. Conversely, developers must understand the goals of the development cycle via well-written features, user stories, and acceptance criteria. Established standards define how development practices should be performed and allow the workload team to collaborate effectively, reducing the risk of confusion on goals and expectations.

## Key design strategies

This article focuses on formalizing the management of your workload team's software development practices. To review guidance on development practices, see the Continuous Integration and Continuous Delivery guide (link to OpEx CI/CD guide). Formalize your software development practices to help ensure that product owners, project managers, and developers understand the goals of each sprint and deliver consistent quality for stakeholders.

### Standards for development planning

- **Tools**: Use established, industry-proven tools and processes, like [Agile](/devops/plan/what-is-agile-development), [Scrum](/devops/plan/what-is-scrum), and [Kanban boards](/devops/plan/what-is-kanban).

- **Deployment**: Plan for frequent small deployments rather than large infrequent deployments.

- **Terms**: Standardize your definition of *finished* development to help ensure that supporting functions, including testing, documentation, and accessibility features, are successfully completed.

- **Communication**: Define the standard protocols for product owners and project managers to promote upcoming releases internally and externally. For example, you might establish a standard for communications to external parties about upcoming releases. The standard might dictate that communication should be sent two weeks prior to the release and a reminder should be sent 24 hours prior to the release.

- **User stories**: Standardize a template for user stories. Ensure that each user story is a discrete unit of work, written from the perspective of the end user.

- **Acceptance criteria**: Standardize a template for acceptance criteria. Ensure that acceptance criteria relate specifically to the user story and can be unambiguously proven using one or more acceptance tests.

- **Tracing**: Ensure that the development process is traceable. You should be able to clearly trace the state of your production workload and the associated code back to quality assurance testing, acceptance criteria, user stories, and features. Detailed tracing may also be a regulatory requirement in some cases, like healthcare, for example.

- **Review**: Regularly perform internal audits of your development practices via sprint retrospectives and postmortems. Process reflection should be blameless and should focus on learnings that can be applied as improvements.

- **Reports**: Standardize reports for stakeholders that provide useful metrics focusing on change. Focusing on change allows you to track product acceleration and deceleration. Helpful metrics can include:

  - Change in monthly growth rate of adoption.

  - Change in performance.

  - Change in time to learn.

  - Change in frequency of incidents.

  Reporting shouldn't be used as a tool to evaluate the work of individuals, so avoid metrics like story points per engineer or lines of code per engineer.

## Azure facilitation

[Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards) is a web-based service that enables teams to plan, track, and discuss work across the entire development process. It's well suited for Agile-based development practices.

[GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) is a customizable project management tool that can organize projects and integrates by using issues and pull requests in GitHub.

## Tradeoffs

Agile methodology can become too strict if it's overly prescriptive. Strive for a balance between well-defined standards and innovation.

## Related links

- [Best practices for Agile project management](/azure/devops/boards/best-practices-agile-project-management)
- [Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards)

## Community links

- [GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

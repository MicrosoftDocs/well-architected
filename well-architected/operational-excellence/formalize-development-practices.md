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

This guide describes the recommendations for managing software development practices in accordance with established standards. Your team's ability to produce high quality software relies on a structured, collaborative approach to development planning. Product owners and managers must be able to clearly understand and articulate to stakeholders the work that developers are doing at any time in a development cycle. Conversely, developers must understand the goals of the development cycle via well-written features, user stories, and acceptance criteria. Established standards define how development practices should be performed and allow the workload team to collaborate effectively, reducing the risk of confusion on goals and expectations.

## Key design strategies

Formalize your software development practices to help ensure that product owners, project managers, and developers understand the goals of each sprint and deliver consistent quality to stakeholders.
 To review guidance on development practices, see the [continuous integration and continuous delivery (CI/CD) guide](release-engineering-continuous-integration.md).

### Standards for development planning

- **Tools**: Use established, industry-proven tools and processes, like [Agile](/devops/plan/what-is-agile-development), [Scrum](/devops/plan/what-is-scrum), and [Kanban boards](/devops/plan/what-is-kanban).

- **Deployment**: Plan to use frequent small deployments instead of large infrequent deployments.

- **Terms**: Standardize your definition of *finished* development cycles to help ensure that supporting functions, including testing, documentation, and accessibility features, are successfully completed.

- **Communication**: Define the standard protocols for product owners and project managers to promote upcoming releases internally and externally. For example, you might establish a standard for communications to external parties about upcoming releases. The standard might dictate that communication should be sent two weeks before the release and a reminder should be sent 24 hours before the release.

- **User stories**: Standardize a template for user stories. Ensure that each user story is a discrete unit of work, written from the perspective of the end user.

- **Acceptance criteria**: Standardize a template for acceptance criteria. Ensure that acceptance criteria relates specifically to the user story and can be unambiguously proven using one or more acceptance tests.

- **Tracing**: Ensure that the development process is traceable. You should clearly trace the state of your production workload and the associated code back to quality assurance testing, acceptance criteria, user stories, and features. Detailed tracing might also be a regulatory requirement in some cases, like healthcare, for example.

- **Review**: Regularly perform internal audits of your development practices via sprint retrospectives and postmortems. Process reflection should be blameless and should focus on learning that can be applied as improvements.

- **Reports**: Standardize reports for stakeholders that provide useful metrics focusing on change. Focusing on change allows you to track product acceleration and deceleration. Helpful metrics can include changes in:

  - The monthly growth rate of adoption.

  - Performance.

  - Training time.

  - The frequency of incidents.

  Reporting shouldn't be used as a tool to evaluate the work of individuals, so avoid metrics like story points or lines of code for each engineer.

## Azure facilitation

[Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards) is a web-based service that enables teams to plan, track, and discuss work across the entire development process. It's well suited for Agile-based development practices.

[GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) is a customizable project management tool that can organize projects and integrates by using issues and pull requests in GitHub.

## Tradeoffs

Agile methodology can become too strict if it's overly prescriptive. Strive for a balance between well-defined standards and innovation.

## Related links

- [Best practices for Agile project management](/azure/devops/boards/best-practices-agile-project-management)
- [Azure Boards](/azure/devops/boards/get-started/what-is-azure-boards)

## Community links

- [GitHub Projects](https://docs.github.com/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects)

## Operational Excellence checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Operational Excellence checklist](checklist.md)

---
title: Recommendations for formalizing software development management practices
description: Learn how to establish standards for managing your workload team's software development practices.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for formalizing software development management practices

**Applies to this Well-Architected Framework Operational Excellence checklist recommendation:** 

|[OE:03](checklist.md)| Formalize the software ideation and planning process, drawing from established industry and organizational standards. Use a common, prioritized backlog and sufficiently detailed specifications. Drive continuous improvements in the planning process based on outcomes. | 
|---|---| 

This guide describes the best practices for establishing standards for managing your workload team's software development practices. A team's ability to consistently produce high quality software relies on a structured, collaborative approach to development planning. Product owners and project managers must be able to clearly understand and articulate to stakeholders the work that developers are doing at any given time in a development cycle. Conversely, developers need to clearly understand the goals of each development cycle through well-written features, user stories and acceptance criteria. A set of agreed-upon standards defining how development practices are performed allows the entire workload team to collaborate effectively, reducing the risk of confusion about goals and expectations.

## Key design strategies

This article focuses on formalizing the management of your workload team's software development practices. To review guidance specifically on development practices, see the Continuous Integration and Continuous Delivery guide (link to OpEx CI/CD guide). Formalizing the ways that you manage your software development practices ensures that product owners, project managers, and developers all understand the goals of each sprint and work in concert to deliver consistent quality that can be appreciated by stakeholders.

Standards for development planning:

- Use established industry-proven tools and processes, like [Agile](/devops/plan/what-is-agile-development) with [Scrum](/devops/plan/what-is-scrum) and [Kanban boards](/devops/plan/what-is-kanban) rather than developing your own.

- Plan for frequent small deployments rather than large infrequent deployments. 

- Standardize your definition of done for development sprints to help ensure that all supporting functions including testing, documentation and accessibility features have been successfully completed.

- Define the standard protocols for product owners and project managers to promote upcoming releases internally and externally. As an example, you might have a standard dictating that communications to external parties about upcoming releases should be sent two weeks prior to the release and a reminder should be sent 24 hours prior to the release.

- Standardize on a specific template that user stories must be created with. Ensure that each user story is written from the perspective of the end user and is a discreet unit of work that can be delivered by the end of a sprint.

- Standardize on a specific template that acceptance criteria must be created with. Ensure that acceptance criteria relates specifically to the user story and can be unambiguously proven using one or more acceptance tests.

- Ensure that you have a method to produce traceability throughout the development process. You should be able to clearly trace the state of your production workload and the associated code back to quality assurance testing, acceptance criteria, user stories and features. This may be a regulatory requirement in cases like healthcare for example. 

- Regularly perform internal audits of your development practices through sprint retrospectives and post-mortems. Like incident post-mortems, reflecting on processes should be blameless and should focus on learnings that can be applied as improvements.

- Produce standardized reporting for stakeholders that provides useful metrics focusing on change. Focusing on change allows you to track product acceleration or deceleration. These metrics can include:

  - Change in monthly growth rate of adoption

  - Change in performance

  - Change in time to learn

  - Change in frequency of incidents

  Reporting shouldn't be used as a tool to evaluate the work of individuals, so avoid metrics like story points per engineer or lines of code per engineer. 

## Azure facilitation

[Azure DevOps Boards](/azure/devops/boards/get-started/what-is-azure-boards) is a web-based service that enables teams to plan, track, and discuss work across the entire development process and is well suited for Agile-based development practices.

[GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) is a customizable project management tool that can organize projects and integrates with issues and pull requests in GitHub. 

## Tradeoff

Agile methodology can become too strict if it is overly prescriptive. Strive for a balance between well-defined standards and allowing for innovation.

## Related links

[Best practices for Agile project management - Azure Boards | Microsoft Learn](/azure/devops/boards/best-practices-agile-project-management)

## Operational Excellence checklist

Refer to the complete set of recommendations. 

[Operational Excellence checklist](checklist.md)
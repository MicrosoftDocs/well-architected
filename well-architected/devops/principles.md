---
title: Operational excellence design principles
description: Understand the design principles for operational excellence within the Azure Well-Architected Framework.
author: martinekuan
ms.author: martinek
ms.date: 04/07/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Operational excellence design principles

The principles of operational excellence are a series of considerations that can help achieve superior operational practices.

To achieve a higher competency in operations, consider and improve how software is:

- Developed
- Deployed
- Operated
- Maintained

Equally important, provide a team culture, which includes:

- Experimentation and growth
- Solutions for rationalizing the current state of operations
- Incident response plans

To assess your workload using the tenets found in the Azure Well-Architected Framework, reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

The following design principles provide:

- Context for questions
- Why a certain aspect is important
- How an aspect is applicable to Operational excellence

These critical design principles are used as lenses to assess the Operational excellence of an application deployed on Azure. These lenses provide a framework for the application assessment questions.

## Optimize build and release processes

Embrace software engineering disciplines across your entire environment, which include the following disciplines:
  
- Provision with Infrastructure as Code
- Build and release with continuous integration and continuous delivery (CI/CD) pipelines
- Use automated testing methods
- Avoid configuration drift through configuration as code
  
This approach ensures the creation and management of environments throughout the software development lifecycle. It enables:

- Consistency
- Repetition
- Early detection of issues
  
## Understand operational health

Implement systems and processes to monitor all aspects of your workload. Including:

- Build and release processes
- Infrastructure health
- Application health

Robust monitoring ensures the observability of a workload and allows you to correlate events and take proactive mitigating issues.

In addition, customer data is critical to understanding the health of a workload and whether the service is meeting the business goals.

> [!IMPORTANT]
> The Health Modeling section of the [Azure Mission-Critical](/azure/architecture/framework/mission-critical/mission-critical-health-modeling) framework contains further in-depth guidance and examples on how to build a health model for a given workload.

## Rehearse recovery and practice failure

Rehearse recovery and practice failure using the following methods:

- Run disaster recovery (DR) drills on a regular cadence.  
- Use chaos engineering practices to identify and remediate weak points in application reliability.
- Rehearse failure to validate the effectiveness of recovery processes and ensure teams are familiar with their responsibilities.
- Document past failures and automate their remediation where possible.

## Embrace continuous operational improvement

Teams that embrace continuous operational improvement continuously evaluate and refine operational procedures and tasks.
They strive to reduce complexity and ambiguity whenever possible.
  
Adopting a continuous improvement culture helps organizations:

- Evolve processes over time.
- Optimize inefficiencies and associated processes.
- Learn from failures.
- Continuously evaluate new opportunities.


## Use loosely coupled architecture

Use modern architecture patterns such as:

- microservices
- loosely coupled 
- serverless 

and pair this with cloud design patterns such as:

- Circuit breakers
- Load-Leveling
- Throttling

and advanced deployment strategies like:

- Canary
- Blue-green 
- Staggered

 to enable teams to build and deploy services independently and minimize the impact if there is a service failure.
  
This principle also extends to procedural decoupling. Teams will be able to take full advantage of their loosely coupled architecture if they do not have to depend on partner teams to support, approve, or operate their workloads.

### Next step

> [!div class="nextstepaction"]
> [Automation overview](automation-overview.md)

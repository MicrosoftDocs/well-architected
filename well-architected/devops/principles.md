---
title: Operational excellence design principles
description: Understand the design principles for operational excellence within the Azure Well-Architected Framework.
author: david-stanford
ms.author: robbymillsap
ms.date: 02/02/2022
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

Design principles:

- [Optimize build and release processes](#optimize-build-and-release-processes)
- [Understand operational health](#understand-operational-health)
- [Rehearse recovery and practice failure](#rehearse-recovery-and-practice-failure)
- [Embrace continuous operational improvement](#embrace-continuous-operational-improvement)
- [Use loosely coupled architecture](#use-loosely-coupled-architecture)

These critical design principles are used as lenses to assess the Operational excellence of an application deployed on Azure. These lenses provide a framework for the application assessment questions.

## Optimize build and release processes

Embrace software engineering disciplines across your entire environment, which include the following disciplines:
  
- Provision with Infrastructure as Code
- Build and release with continuous integration and continuous delivery (CI/CD) pipelines
- Automated testing
  
This approach ensures the creation and management of environments throughout the software development lifecycle enables:

- Consistency
- Repetition
- Early detection of issues
  
## Understand operational health

Evaluate operational health through focused and assertive monitoring.

Implement systems and processes to monitor the following components:
  
- Build and release processes
- Infrastructure health
- Application health
  
Customer data is critical to understanding the health of a workload and whether the service is meeting the business goals.

## Rehearse recovery and practice failure

Rehearse recovery and practice failure using the following methods:

- Run disaster recovery (DR) drills on a regular cadence.  
- Use chaos engineering practices to identify and remediate weak points in application reliability.
- Rehearse failure to validate the effectiveness of recovery processes and ensure teams are familiar with their responsibilities.

## Embrace continuous operational improvement

Embrace continuous operational improvement through the following methods:

- Continuously evaluate and refine operational procedures and tasks.
- Strive to reduce complexity and ambiguity.
  
This approach enables an organization to:

- Evolve processes over time.
- Optimize inefficiencies.
- Learn from failures.

## Use loosely coupled architecture

Use loosely coupled architecture to enable teams to independently:

- Test
- Deploy
- Update their systems on demand
  
Without depending on other teams for:
  
- Support
- Services
- Resources
- Approvals

### Next step

> [!div class="nextstepaction"]
> [Automation overview](automation-overview.md)

---
title: Reliability design principles
description: Understand the design principles of the reliability pillar.
author: v-stacywray
ms.author: robbymillsap
ms.date: 02/02/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - fasttrack-edit
  - overview
---

# Reliability design principles

Building a reliable application in the cloud is different from traditional application development. Historically, you may have purchased levels of redundant higher-end hardware to minimize the chance of an entire application platform failing.

In the cloud, we acknowledge that failures happen. Instead of trying to prevent failures altogether, the goal is to minimize the effects of a single failing component.

To assess your workload using the tenets found in the Azure Well-Architected Framework, reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

The following design principles provide:

- Context for questions
- Why a certain aspect is important
- How an aspect is applicable to Reliability

Design principles:

- [Design for business requirements](#design-for-business-requirements)
- [Design for failure](#design-for-failure)
- [Observe application health](#observe-application-health)
- [Drive automation](#drive-automation)
- [Design for self-healing](#design-for-self-healing)
- [Design for scale-out](#design-for-scale-out)

These critical design principles are used as lenses to assess the Reliability of an application deployed on Azure. These lenses provide a framework for the application assessment questions.

## Design for business requirements

Reliability is a subjective concept. For an application to be appropriately reliable, it must reflect the business requirements surrounding it.

For example, a mission-critical application with a `99.999%` service level agreement (SLA) requires a higher level of reliability than another application with an SLA of `95%`.

Cost implications are inevitable when introducing greater reliability and high availability. This trade-off should be carefully considered.

## Design for failure

Failure is impossible to avoid in a highly distributed and multi-tenant environment like Azure.

By anticipating failures, from individual components to entire Azure regions, a solution can be developed in a resilient way to increase reliability.

## Observe application health

Before mitigating issues that impact application reliability, you must first detect these issues.

By monitoring the operation of an application relative to a healthy state, you can detect and predict reliability issues.

Monitoring allows you to take swift and remedial action.

## Drive automation

One of the leading causes of application downtime is human error due to the deployment of insufficiently tested software or through misconfiguration.

To minimize the possibility and consequence of human errors, it's vital to strive for automation in all aspects of a cloud solution.

Automation improves:

- Reliability
- Automated testing
- Deployment
- Management

## Design for self-healing

*Self-healing* describes the ability of a system to deal with failures automatically. Handling failures happens through pre-defined remediation protocols. These protocols connect to failure modes within the solution.

It's an advanced concept that requires a high level of system maturity with monitoring and automation.

From inception, self-healing should be an aspiration to maximize reliability.

## Design for scale-out

*Scale-out* is a concept that focuses on the ability of a system to respond to demand through horizontal growth. As traffic grows, *more* resource units are added in parallel, instead of increasing the size of the existing resources.

Through scale units, a system can handle expected and unexpected traffic increases, essential to overall reliability.

Scale units further reduce the effects of a single resource failure.

### Next step

> [!div class="nextstepaction"]
> [Design](./design-checklist.md)

---
title: Operational excellence design principles
description: Understand the design principles for operational excellence within the Azure Well-Architected Framework.
author: david-stanford
ms.author: robbymillsap
ms.date: 03/03/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
---

# Operational excellence design principles

The operational excellence design principles are a series of considerations that can help achieve superior operational practices.

To achieve a higher competency in operations, consider and improve how software is:

- Developed
- Deployed
- Operated
- Maintained

Equally important, foster a team culture, which...

- values those who find solutions for rationalizing the current state of operations
- provides accurate and targeted responses to incidents 
- commits to continuous learning, ongoing experimentation, and growth

To assess your workload using the tenets found in the Azure Well-Architected Framework, reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

The following design principles provide:

- Context for the questions in the Well-Architected Review
- Reasons why a certain aspect is important
- Explanations of how particular aspects apply to operational excellence

These critical design principles are used as lenses through which the operational excellence of an application deployed on Azure is assessed.

## Maximize automation

Adopt and apply modern DevOps practices like continuous integration/continuous delivery (CI/CD), automated testing, infrastructure as code (IaC) and configuration as code to support cloud workloads.
  
This approach ensures consistent creation and management of environments throughout the software development lifecycle and encourages teams to practice deployments often. This - in turn - gives them an opportunity to detect issues early.

## Maximize continuous improvement efforts

Embrace and foster continuous improvement culture to evolve business processes, reduce complexity, and eliminate inefficiencies as much as possible.

An organization that models a continuous improvement culture has teams that...
- periodically evaluate new technology
- share learning from failure
- suggest improvements business processes
- review options to reduce complexity for workloads and processes
  
## Maximize observability to minimize disruption

Establish observability within your workload and across your cloud infrastructure through robust monitoring and alerting solutions. 

Enable robust event correlation to allow your teams to take proactive mitigating actions, troubleshoot issues, and efficiently respond to incidents.

In addition, user data is incredibly valuable when it comes to understanding the user experience and whether your workload meets its business goals. 

## Minimize the blast radius

Make use of modern architecture patterns and cloud design patterns to create workloads that are designed to anticipate failure, thus minimizing the "blast radius" of an outage or service failure.

Modern architecture patterns include:
 - loosely-coupled architecture
 - microservices
 - serverless design

Cloud design patterns include:
 - circuit breakers
 - load-leveling
 - throttling
 - queuing 

In addition, adopting modern deployment practices can help reduce cross-team reliance and help build confidence into automated processes. These practices include:
 - canary
 - blue/green deployment
 - staggered deployment

## Minimize time to recovery

Adopt modern engineering practices to swiftly recover in the case of failure. 

- Document top system failures
- Leverage chaos engineering practices to stress test the workload
- Create clear disaster recovery and remediation plans and rehearse them often 
- Leverage automation to resolve escalations more easily
- Document, test, and update your escalation processes frequently

### Next step

> [!div class="nextstepaction"]
> [Automation overview](automation-overview.md)

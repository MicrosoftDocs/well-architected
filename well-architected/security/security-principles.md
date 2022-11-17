---
title: Security design principles
description: Security design principles describe a securely architected system hosted on cloud or on-premises datacenters (or a combination of both).
author: PageWriter-MSFT
ms.author: martinekuan
ms.date: 02/10/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom:
  - article
---

# Security design principles

Security design principles describe a securely architected system hosted on cloud or on-premises datacenters (or a combination of both). Application of these principles dramatically increases the likelihood your security architecture assures confidentiality, integrity, and availability.

To assess your workload using the tenets found in the Azure Well-Architected Framework, reference the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

The following design principles provide:

- Context for questions
- Why a certain aspect is important
- How an aspect is applicable to Security

These critical design principles are used as lenses to assess the Security of an application deployed on Azure. These lenses provide a framework for the application assessment questions.

## Plan resources and how to harden them

Recommendations:

- Consider security when planning workload resources.
- Understand how individual cloud services are protected.
- Use a service enablement framework to evaluate.

## Automate and use least privilege

Recommendations:

- Implement least privilege throughout the application and control plane to protect against data exfiltration and malicious actor scenarios.
- Drive automation through DevSecOps to minimize the need for human interaction.

## Classify and encrypt data

Recommendations:

- Classify data according to risk.
- Apply industry-standard encryption at rest and in transit, which ensures keys and certificates are stored securely and managed properly.

## Monitor system security, plan incident response

Recommendations:

- Correlate security and audit events to model application health.
- Correlate security and audit events to identify active threats.
- Establish automated and manual procedures to respond to incidents.
- Use security information and event management (SIEM) tooling for tracking.

## Identify and protect endpoints

Recommendations:

- Monitor and protect the network integrity of internal and external endpoints through security appliances or Azure services, such as:
  - Firewalls
  - Web application firewalls
- Use industry standard approaches to protect against common attack vectors, such as distributed denial of service (DDoS) attacks like SlowLoris.

## Protect against code-level vulnerabilities

Recommendations:

- Identify and mitigate code-level vulnerabilities, such as cross-site scripting and structured query language (SQL) injection.
- In the operational lifecycle, regularly incorporate:
  - Security fixes
  - Codebase and dependency patching

## Model and test against potential threats

Recommendations:

- Establish procedures to identify and mitigate known threats.
- Use penetration testing to verify threat mitigation.
- Use static code analysis to detect and prevent future vulnerabilities.
- Use code scanning to detect and prevent future vulnerabilities

### Next step

> [!div class="nextstepaction"]
> [Design governance](./design-governance.md)

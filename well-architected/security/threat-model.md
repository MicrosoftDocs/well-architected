---
title: Application threat analysis
description: Use threat modeling to identify threats, attacks, vulnerabilities, and countermeasures that can affect an application.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for threat analysis

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:02](checklist.md)|Establish a security baseline that's aligned to compliance requirements, industry standards, and platform recommendations. Regularly measure your workload architecture and operations against the baseline to sustain or improve your security posture over time.|
|---|---|

**Related guide**: [Recommendations for securing a development lifecycle](secure-development-lifecycle.md)

A comprehensive analysis to identify threats, attacks, vulnerabilities, and countermeasures is crucial during the design phase of a workload. *Threat modeling* is an engineering exercise that includes defining security requirements, identifying and mitigating threats, and validating those mitigations. You can use this technique at any stage of application development or production, but it's most effective during the design stages of new functionality.

This guide describes the recommendations for doing threat modeling so that you can identify security gaps quickly and design your security defenses.

**Definitions** 

|Term   |Definition|
|---------|---------|
|Software development lifecycle (SDLC)|A multistage, systematic process for developing software systems. |
|STRIDE| A Microsoft-defined taxonomy for categorizing types of threats.|
|Threat modeling| A process for identifying potential security vulnerabilities in the application and system, mitigating risks, and validating security controls.|

## Key design strategies

Threat modeling is a crucial process that an organization should integrate into its SDLC. Threat modeling is not solely a developer's task. It's a shared responsibility between:

- The workload team, which is responsible for the technical aspects of the system.
- Business stakeholders, who understand the business outcomes and have a vested interest in security.

There's often a disconnect between organizational leadership and technical teams regarding business requirements for critical workloads. This disconnect can lead to unwanted outcomes, particularly for security investments.

When the workload team is doing a threat modeling exercise, it should consider both business and technical requirements. The workload team and business stakeholders must agree on security-specific needs of the workload so that they can make adequate investments in the countermeasures.

The security requirements serve as guide for the entire process of threat modeling. To make it an effective exercise, the workload team should have a security mindset and be trained in threat modeling tools.

### Understand the scope of the exercise

A clear understanding of the scope is crucial for effective threat modeling. It helps focus efforts and resources on the most critical areas. This strategy involves defining the boundaries of the system, taking inventory of the assets that need to be protected, and understanding the level of investment that's required in security controls.

### Gather information about each component

A workload architecture diagram is a starting point for gathering information because it provides a visual representation of the system. The diagram highlights technical dimensions of the system. For example, it shows user flows, how data moves through the network, data sensitivity levels and information types, and identity access paths.

This detailed analysis can often provide insight into potential vulnerabilities in the design. It's important to understand the functionality of each component and its dependencies.

### Evaluate the potential threats

Analyze each component from an outside-in perspective. For example, how easily can an attacker gain access to sensitive data? If attackers gain access to the environment, can they move laterally and potentially access or even manipulate other resources? These questions help you understand how an attacker might exploit workload assets.

### Classify the threats by using an industry methodology

One methodology for classifying threats is [STRIDE](/azure/security/develop/threat-modeling-tool-threats), which the Microsoft Security Development Lifecycle uses. Classifying threats helps you understand the nature of each threat and use appropriate security controls.

### Mitigate the threats

Document all the identified threats. For each threat, define security controls and the response to an attack if those controls fail. Define a process and timeline that minimize exposure to any identified vulnerabilities in the workload, so that those vulnerabilities can't be left unaddressed.

Use the *assume breach* approach. It can help identify controls needed in the design to mitigate risk if a primary security control fails. Evaluate how likely it is for the primary control to fail. If it does fail, what is the extent of the potential organizational risk? Also, what is the effectiveness of the compensating control? Based on the evaluation, apply defense-in-depth measures to address potential failures of security controls.

Here's an example:

|Ask this question|To determine controls that...|
|---|---|
|Are connections authenticated through Microsoft Entra ID, Transport Layer Security (TLS) with mutual authentication, or another modern security protocol that the security team approved: <br><br> - Between users and the application? <br><br> - Between application components and services?|Prevent unauthorized access to the application components and data.|
|Are you limiting access to only accounts that need to write or modify data in the application?| Prevent unauthorized data tampering or alteration.|
|Is the application activity logged and fed into a security information and event management (SIEM) system through Azure Monitor or a similar solution?|Detect and investigate attacks quickly.|
|Is critical data protected with encryption that the security team approved?| Prevent unauthorized copying of data at rest.|
|Are inbound and outbound network traffic encrypted through TLS?|Prevent unauthorized copying of data in transit.|
|Is the application protected against distributed denial of service (DDoS) attacks through services such as Azure DDoS Protection?|Detect attacks designed to overload the application so it can't be used.|
|Does the application store sign-in credentials or keys to access other applications, databases, or services?| Identify whether an attack can use your application to attack other systems.|
|Do the application controls allow you to fulfill regulatory requirements?| Protect users' private data and avoid compliance fines.|

### Track threat modeling results

We highly recommend that you use a **threat modeling tool**. Tools can automate the process of identifying threats and produce a comprehensive report of all identified threats. Be sure to communicate the results to all interested teams.

Track the results as part of the workload team's backlog to allow for accountability in a timely way. Assign tasks to individuals who are responsible for mitigating a particular risk that threat modeling identified.

As you add new features to the solution, update the threat model and integrate it into the code management process. If you find a security problem, make sure there's a process to triage the problem based on severity. The process should help you determine when and how to remediate the problem (for example, in the next release cycle or in a faster release).

### Regularly review business-critical workload requirements

Meet regularly with executive sponsors to define requirements. These reviews provide an opportunity to align expectations and ensure operational resource allocation to the initiative.

## Azure facilitation

The Microsoft Security Development Lifecycle provides a threat modeling tool to assist with the threat modeling process. This tool is available at no additional cost. For more information, see the [Threat Modeling page](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling).

## Related links

- [STRIDE](/azure/security/develop/threat-modeling-tool-threats)
- [Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling)

## Community links

[Open Web Application Security Project (OWASP)](https://owasp.org/www-community/Threat_Modeling_Process) has documented a threat modeling approach for applications.

## Security checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

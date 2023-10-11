---
title: Application threat analysis
description: Use threat modeling to identify threats, attacks, vulnerabilities, and countermeasures that can affect an application.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for threat analysis

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:02](checklist.md)|Establish a security baseline aligned to compliance requirements, industry standards, and platform recommendations. Regularly measure your workload architecture and operations against the baseline to sustain or improve your security posture over time.|
|---|---|

**Related guide**: [Secure development lifecycle](secure-development-lifecycle.md)

A comprehensive analysis to identify threats, attacks, vulnerabilities, and counter measures are crucial during the design phase of a workload. _Threat modeling_ is an engineering exercise that includes **defining security requirements, identifying and mitigating threats, and validating those mitigations**. This technique can be used at any stage of application development or production, but it's most effective during the design stages of a new functionality.

This guide provides recommendations on the common approaches for doing threat modeling so that you can identify security gaps quickly and design your security defenses. 

**Definitions** 

|Terms   |Definition|
|---------|---------|
|Threat modeling| A process for identifying potential security vulnerabilities in the application and system, mitigating risks, and validating security controls.|
|Software development lifecycle (SDLC)|A multi-stage systematic process for developing software systems. |
| STRIDE| Microsoft-defined taxonomy for categorizing different types of threats.|



## Key design strategies

Threat modeling is a crucial process that should be **integrated into the software development lifecycle (SDLC)**. It's not solely a developer-centric task but a shared responsibility between various teams and stakeholders within an organization.

The process of threat modeling involves both the workload team, responsible for the technical aspects of the system, and business stakeholders, who understand the business outcomes and have a vested interest in security. There can often be a disconnect between organizational leadership and technical teams regarding business requirements for critical workloads. It can lead to undesired outcomes, particularly in relation security investments. **Both business and technical requirements should be considered when doing a threat modeling exercise**. Security-specific needs of the workload must be agreed on so that adequate investments can be made on the countermeasures. 

The security requirements serve as guide for the entire threat modeling process. To make it an effective exercise, the team should have a security mindset and be trained in threat modeling tools.

**Understand the scope of the exercise**. This involves defining the boundaries of the system, taking inventory of the assets that need to be protected, and **understanding the level of investment required in security controls**. A clear understanding of the scope is crucial for effective threat modeling as it helps to focus the team’s efforts and resources on the most critical areas. 

**Gather information about each component**. The workload architecture diagram should be used as a starting point because it provides a **visual representation of the system**.The diagram should highlight various technical dimensions of the system. For example, user flows, how data moves through the network, data sensitivity levels and information types, identity access paths, and so on. 

This detailed analysis can often provide insight into potential vulnerabilities in the design. It’s important to understand the functionality of each component and its dependencies. 

**Evaluate the potential threats**. Analyze each component from an outside-in perspective. For example, how easily can an attacker gain access to sensitive data? If an attacker gains access to the environment, can they move laterally and potentially access or even manipulate other resources? These questions help you **understand how an attacker might exploit workload assets**. 

**Classify the threats using an industry methodology**. One such methodology is **[STRIDE](/azure/security/develop/threat-modeling-tool-threats)**, which is used by Microsoft Security Development Lifecycle. Classifying potential threats give you an understanding of the nature of each threat and use appropriate security controls. 

**Mitigate the threats**. Document all the threats identified. For each threat, **define secure controls** and also the response to an attack if that control fails. Define a process and timeline that minimizes exposure to any identified vulnerabilities in the workload, so that those vulnerabilities can't be left unaddressed.

Use the _assume breach_ approach. This can help **identify controls needed in the design to mitigate risk if a primary security control fails**. Evaluate how likely it is for the primary control to fail. If it does, what is the extent of the potential organizational risk? Also, what is the effectiveness of the compensating control. Based on the evaluation apply defense-in-depth measures to address potential failures of security controls.

Here's an example:

|Ask this question ...|To determine controls that ...|
|---|---|
|Are connections authenticated using Azure AD, TLS (with mutual authentication), or another modern security protocol approved by the security team? <br><br> - Between users and the application <br><br> - Between different application components and services|Prevent unauthorized access to the application component and data.|
|Are you limiting access to only those accounts that have the need to write or modify data in the application| Prevent unauthorized data tampering or alteration.|
|Is the application activity logged and fed into a Security Information and Event Management (SIEM) through Azure Monitor or a similar solution?|Detect and investigate attacks quickly.|
|Is critical data protected with encryption that has been approved by the security team?| Prevent unauthorized copying of data at rest.|
|Is inbound and outbound network traffic encrypted using TLS?|Prevent unauthorized copying of data in transit.|
|Is the application protected against Distributed Denial of Service (DDoS) attacks using services such as Azure DDoS protection?|Detect attacks designed to overload the application so it can't be used.|
|Does the application store any sign in credentials or keys to access other applications, databases, or services?| Identify whether an attack can use your application to attack other systems.|
|Do the application controls allow you to fulfill regulatory requirements?| Protect user's private data and avoid compliance fines.|

**Track threat modeling results**. Using a **threat modeling tool** is highly recommended. Tools can automate the process of identifying threats and produce **a comprehensive report of all threats identified**. Ensure that the results are communicated effectively to all interested teams.

The results must be **tracked as part of team's backlog** to allow for accountability in a timely manner. Tasks should be assigned to individuals who are responsible for mitigating a particular risk identified during threat modeling.

As new features are added to the solution, the threat model should be updated and integrated into the code management process. If a security issue is found, there should be a **process to triage issue severity and determine when and how to remediate** (such as in the next release cycle, or a faster release).

**Reguarly review business critical workload requirements** with executive sponsors to define requirements. It provides an opportunity to align expectations and ensure operational resource allocation to the initiative.

## Azure facilitation

Microsoft Security Development Lifecycle provides a tool to assist with the threat modeling process. This tool is available at no additional cost. For more information, see [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling).

## Community resources

[Open Web Application Security Project (OWASP)](https://owasp.org/www-community/Threat_Modeling_Process) has documented a threat modeling approach for applications.

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)

---
title: Recommendations for security testing
description: Learn about the key design strategies for security testing. Find out about the different types of tests and their frequency. Learn about the tools and methodologies you can use to test the security posture of your workload.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for security testing

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:10](checklist.md)|Establish a comprehensive testing regimen that combines various testing approaches to prevent security issues, validate threat prevention implementations, and test threat detection mechanisms.|
|---|---|

A good security design must be backed by rigorous testing. Testing is a tactical form of validation to make sure the controls in place are working as intended. Testing is also a proactive way to detect vulnerabilities in the system.

Testing rigor must be established through cadence and incorporating verification by multiple perspectives. You want to include both inside-out viewpoints that test platform and infrastructure, outside-in evaluations that test the system like an external attacker.

This guide provides recommendations for testing the security posture of your workload to ensure its resistance to attacks, maintain confidentiality, integrity, and availability of resources.

**Definitions** 


|Terms   |Definition   |
|---------|---------|
|Microsoft Security Development Lifecycle (SDL)     |   A set of practice provided by Microsoft that serves to support security assurance and compliance requirements.      |
|Software development life cycle (SDLC)     |  A multi-stage systematic process for developing software systems.       |
|White box testing|A testing methodology where the structure of the code is known to the practitioner.|
|Black box testing|A testing methodology that validates the  externally visible application behavior without knowledge of the internals of the system.|
|Application security testing (AST) | An SDL technique to check for security vulnerabilities in code using white box and black box testing methodologies.|
|Penetration testing | A testing that validates the security defense of a system with ethical hacking techniques.|
|Red team     |  A team in a war game exercise, which plays the role of an adversary and attempt to hack the system.|
|Blue team     | A team in a war game exercise, which defends against the attacks of the red team.        |


## Key design strategies

Testing is a non-negotiable strategy especially for security. It allows you to proactively discover and address security issues before they can be exploited and validate that implemented security controls are functioning as designed.

The scope of testing must include application, infrastructure, automated and human processes.

> [!NOTE] 
> This guidance makes a distinction between testing and incident response. While testing is a detection mechanism that ideally fixes issues prior to production as a preventative measure, it's not to be confused with remediation or investigation that's done as part of incident response. The aspect of recovering from security incidents is described in [Incident Response recommendations](incident-response.md).

Microsoft Security Development Lifecycle (SDL) suggests several types of testing that catch vulnerabilities in code, verifies runtime components, and uses ethical hacking as a way to test the security resilience of the system. It's also encouraged that this is a key shift-left activity, starting security testing as early in the development process as possible. For example, static code analysis and automated scanning of Infrastructure-as-Code (IaC) are tests that should be run early.

**Be involved in test planning**. The test cases may or may not be designed by the workload team. Often in enterprises, that function is centralized or externalized to security experts. The workload team should be involved in that design process so that the security assurances are evaluated keeping in mind the application functionality.

**Think like an attacker**. It's recommended that you design your test cases assuming that the system has been attacked. You'll be able to uncover the potential vulnerabilities and prioritize the tests accordingly.

**Run tests in a structured manner and with a repeatable process**. It's recommended that you build your testing rigor around cadence, types of tests, driving factors, and intended outcomes.

**Use the right tool for the job**. Appropriate tooling that's configured to work with the characteristic of the workload. If you don't have a tool, buy the tool. Don't build it. Security tools are highly specialized, and building your own tool might introduce risks that need the expertise to remediate. Take advantage of the expertise and tooling offered by central SecOps team or through other external means if expertise isn't embedded in the workload team.

**Set up separate environments**. Tests can be classified as destructive and nondestructive. Nondestructive aren't invasive as in they can indicate there's a problem but don't necessarily prove the damage. Destructive tests might try to demonstrate the damage, such as deleting data from a database.

Production environments give you the best information but are the most disruptive. You tend to only do the nondestructive tests. Nonproduction environments are typically nondisruptive and nondestructive but might not always accurately represent the production environment\'s configuration in ways that are important to security tests.

If you deploy by using IaC and automation, consider whether you can create an isolated clone of your production environment to test against. If you have a continuous process for routine tests, having a dedicated environment is recommended.

**Always evaluate the test results**. Testing is a wasted effort if test results aren't used to prioritize the actions, and make improvements upstream. Document the security guidelines that you might have uncovered including best practices. Proper documentation that captures the results and remediation plans will ensure appropriate follow-up to educate the team about the various means that attackers may use. Conduct regular security training for developers, admins, and testers.

When you're designing your test plans, think about:

-   How often do you expect the test to run and what's the impact on our environment?

-   What are the different test types that you should run?

### How often do you expect the tests to run?

The workload must be tested regularly to make sure changes don't introduce security risks and there aren't any regressions. The team must also be ready to respond to organizational security validations that might be conducted at any time. Also, there are tests that are conducted because of security incident response. These sections provide recommendations on the frequency aspect of tests.

#### Routine tests

Routine tests are conducted at regular cadence, as part of your standard operating procedures. They can also be driven by compliance requirements. Various tests may be run at different cadences, the key is that they're conducted periodically on a schedule.

These tests should be integrated into your software development life cycle (SDLC) because they provide defense in depth at each stage. The test suite must also be diverse in that they verify various assurances for identity, data storage and transmission, communication channels, and others. Because the same tests are conducted in different points of the life cycle, they ensure that there aren't any regressions. Routine tests can help establish an initial benchmark. However that's just a starting point. As new issues are uncovered at same points of the lifecycle, new test cases are added and the tests themselves become better with repetition.

At each stage, these tests should validate new code that's added or removed, or configuration settings that are changed. You want to detect the security impact of those changes. Efficacy should be improved with automation and balanced with peer reviews.

Consider running security tests as part of an automated pipelines or scheduled test runs. The sooner we can discover new security issues, the easier it's to relate it to the code or configuration changes that might have caused it.

Don't only rely on automated tests. Add manual testing to detect vulnerabilities that can only be caught by human expertise. Manual testing is good for exploratory use cases and finding unknown risks.

#### Ad hoc tests

Ad hoc tests provide a point-in-time validation of security defenses. These tests are usually triggered by security alerts, which may or may not have an impact on the workload at that time. Organizational mandate might require a pause-and-test mindset to verify if the defenses will be effective if the alert escalates to an emergency.

The benefit is preparedness for a real incident. Ad-hoc tests can be a forcing function to do user acceptance testing (UAT).

The security team might audit all workloads and run these tests as needed. As a workload owner, you need to facilitate and collaborate with security teams. Negotiate enough lead time with security teams so that you can prepare. Acknowledge and communicate to your team and stakeholders that these are necessary disruptions.

In other cases, you might be required to run tests and report the security state of the system against the potential threat.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Because they are disruptive events, expect reprioritization of tasks, which may delay other planned work.

> ![Risk icon](../_images/risk.svg) **Risk**: There's risk of the unknown. They might be one-time efforts without established processes or tools. But the predominant risk is the potential interruption of the rhythm of business, we need to evaluate those risks relative to the benefit gained by this action.

#### Security incident tests

There are tests that detect the cause of a security incident at source. These security gaps must be resolved to make sure the incident isn't repeated.

The incidents also indicate the existing gaps in the test cases, making them better over time. The team should apply the lessons learned from the incident and incorporate the improvements as part of the routine tests.

### What are the different types of tests?

Tests can mainly be categorized **by technology** and **testing methodologies**. Combine those categories and approaches within those categories to get complete coverage.

By adding multiple tests and types of tests, you can uncover:

-   Gaps in security controls or compensating controls.

-   Misconfigurations.

-   Gaps in observability and detection methods.

A good threat modeling exercise can point to key areas to ensure test coverage and their frequency. Recommendations on threat modeling is covered in [Secure Development Lifecycle](security-development-lifecycle.md) (SDL).

Most tests described in these sections can be run as routine tests. However, repeatability can incur costs in some cases and cause disruption. Consider those tradeoffs carefully.

#### Tests that validate the technology stack

Here are some examples of types of tests and their focus areas. This isn't an exhaustive list. Test the entire stack including application stack, frontend, backend, APIs, databases and any external integrations.

-   Data security: Test the effectiveness of data encryption and access controls to ensure data is properly protected from unauthorized access and tampering.

-   Network and connectivity: Test your firewalls to ensure they only allow expected, allowed, and safe traffic to the workload.

-   Application: Test source code through application security testing (AST) techniques to make sure secure coding practices are being followed. Also, runtime errors are caught, like memory corruption, privilege issues, and others. For details, see these [community resources](#community-resources).

-   Identity: Evaluate if the role assignments and conditional checks work as intended.

#### Test methodology 

There are many perspectives on testing methodologies. Tests that enable threat hunting by simulating real-world attacks are highly recommended. They can identify potential threat actors, their techniques, and exploits, which may pose a threat to the workload. Make the attacks as realistic as possible. Use all potential threats vectors identified during threat modeling.

Here are some advantages of testing through real-world attacks:

-   By integrating these attacks as part of routine testing, the workload will get checked through with an outside-in perspective to make sure the defense can withstand an attack.

-   Based on the lessons learned, the team will be able to upgrade their knowledge and skill level. The team will be able to improve situational awareness and self-assess their readiness to incidents.

> ![Risk icon](../_images/risk.svg) **Risk**: Testing in general can impact performance. There might be business continuity problems if destructive tests delete or corrupt data. There's also risks associated with information exposure; make sure that the confidentiality of data is maintained. Integrity of data also should be ensured after testing is completed.

Some examples of simulated tests include penetration testing, war game exercises, and others.

##### Black-box and white-box testing

A perspective on type of testing is black-box testing and white-box testing. In black-box tests, the internals of the system aren't visible. In white-box testing, the tester has a good understanding of the application and even has access to code, logs, and resource topology and configuration to conduct the experiment.

> ![Risk icon](../_images/risk.svg) **Risk**: The difference between the two types is upfront cost. White-box testing can be expensive in terms of time taken to understand the requirement. In some cases, also monetary investment because it might require purchasing of specialized tools. Black-box testing doesn't need ramp up time. But it might not be as effective or take extra effort to uncover issues. It\'s a time investment tradeoff.

##### Tests that simulate attacks through penetration testing (pentesting)

Pentesting is conducted by security experts who aren't part of the organization\'s IT or application teams. They look at the system in a way that malicious actors scope an attack surface. The goal is to find security gaps by gathering information, analyzing vulnerabilities, and reporting.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Penetration tests are ad hoc and can also be expensive in terms of disruptions and financially because pentesting is typically a paid offering by third-party practitioners.

> ![Risk icon](../_images/risk.svg) **Risk**: A pentesting exercise might affect the runtime environment and might disrupt the availability for normal traffic.

the practitioners may need access to sensitive data of the entire organization. Follow the rules of engagement to make sure that access and the intent isn't misused. See the resources listed in [Related links](#related-links).

##### Tests that simulate attacks through war game exercises

In this methodology of simulated attacks, there are two teams:

-   The *red* team is the adversary attempting to model real-world attacks. If they're successful, you'll be able to find gaps in your security design and evaluate the blast radius containment of their breaches.

-   The *blue* team is the workload team that defends against the attacks. They test their ability to detect, respond, and remediate the attacks. They'll be able to validate defenses that have been put in place to protect workload resources.

If conducted as routine tests, war game exercises can help provide ongoing visibility and assurance that your defenses work as designed, potentially testing across different levels within your workload(s).

A popular choice to simulate realistic attack scenarios is [Office 365 Attack Simulator](/office365/securitycompliance/attack-simulator).

For more information, see [Insights and reports Attack simulation training](/microsoft-365/security/office-365-security/attack-simulation-training-insights).

For information about red team and blue team setup, see [Microsoft Cloud Red Teaming](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf).

## Azure facilitation

Microsoft Sentinel and is a native control that combines security information event management (SIEM) and security orchestration automated response (SOAR) capabilities. It analyzes events and logs from various connected sources. Based on the data sources and their alerts, Sentinel creates incidents, performs threat analysis for early detection. Through intelligent analytics and queries, you can be proactive with hunting activities. In case of incidents, you can automate workflows. Also, with workbook templates you can quickly gain insights through visualization.

For product documentation, see [Hunting capabilities in Microsoft Sentinel](/azure/sentinel/hunting).

Microsoft Defender for Cloud offers vulnerability scanning for various technology areas. For details, see [Enable vulnerability scanning with Microsoft Defender Vulnerability Management - Microsoft Defender for Cloud](/azure/defender-for-cloud/deploy-vulnerability-assessment-defender-vulnerability-management).

The practice of DevSecOps integrates security testing as part of ongoing and continuous improvement mindset. War game exercises are a common practice at Microsoft that's integrated in the rhythm of the business. For more information, see </devops/operate/security-in-devops>.

Azure DevOps supports third-party tools that can be automated as part of the continuous integration/continuous deployment pipelines. For details, see [Enable DevSecOps with Azure and GitHub - Azure DevOps](/devops/devsecops/enable-devsecops-azure-github).

## Related links

Follow the rules of engagement to make sure that access and the intent isn't misused. For guidance about planning and executing simulated attacks, see these articles:

[Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement)

[Penetration testing](/azure/security/fundamentals/pen-testing)

You can simulate Denial of Service (DoS) attacks in Azure. Be sure to follow these rules carefully, [Azure DDoS Protection simulation testing](/azure/ddos-protection/test-through-simulations).

### Community resources

[Application security testing: tools, types and best practices - GitHub Resources](https://resources.github.com/security/application-security-testing/) describes the types of testing methodologies that can test the build time and run time defenses of the application.

[Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page) provides guidelines about common scenarios and the activities required to establish a baseline.

[OWASP Top Ten | OWASP Foundation](https://owasp.org/www-project-top-ten/) provides security best practices for applications and the test cases covering common threats.

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)
---
title: Recommendations for security testing
description: Learn about the key design strategies for security testing. Find out about the different types of tests and their frequency. Learn about the tools and methodologies you can use to test the security posture of your workload.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for security testing

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:11](checklist.md)|Establish a comprehensive testing regimen that combines approaches to prevent security issues, validate threat prevention implementations, and test threat detection mechanisms.|
|---|---|

A good security design must be backed by rigorous testing. Testing is a tactical form of validation to make sure controls are working as intended. Testing is also a proactive way to detect vulnerabilities in the system.

Establish testing rigor through cadence and verification from multiple perspectives. You want to include both inside-out viewpoints that test platform and infrastructure and outside-in evaluations that test the system like an external attacker.

This guide provides recommendations for testing the security posture of your workload. Implement these testing methods to improve your workload's resistance to attacks and maintain confidentiality, integrity, and availability of resources.

**Definitions**

|Term   |Definition   |
|---------|---------|
|Application security testing (AST) | An SDL technique that uses white box and black box testing methodologies to check for security vulnerabilities in code.|
|Black box testing| A testing methodology that validates the externally visible application behavior without knowledge of the internals of the system.|
|Blue team     | A team that defends against the attacks of the red team in a war game exercise.        |
|Penetration testing | A testing methodology that uses ethical hacking techniques to validate the security defense of a system.|
|Red team     |  A team that plays the role of an adversary and attempts to hack the system in a war game exercise.|
|Security Development Lifecycle (SDL)     |   A set of practices provided by Microsoft that supports security assurance and compliance requirements.      |
|Software development life cycle (SDLC)     |  A multi-stage systematic process for developing software systems.       |
|White box testing| A testing methodology where the structure of the code is known to the practitioner.|

## Key design strategies

Testing is a nonnegotiable strategy, especially for security. It allows you to proactively discover and address security issues before they can be exploited and to validate that the security controls you implemented are functioning as designed.

The scope of testing must include the application, infrastructure, and automated and human processes.

> [!NOTE]
> This guidance makes a distinction between testing and incident response. While testing is a detection mechanism that ideally fixes issues prior to production, it shouldn't be confused with the remediation or investigation that's done as part of incident response. The aspect of recovering from security incidents is described in [Incident Response recommendations](incident-response.md).

Microsoft Security Development Lifecycle (SDL) suggests several types of testing that catch vulnerabilities in code, verify runtime components, and use ethical hacking as a way to test the security resilience of the system. SDL is a key shift-left activity. You should run tests like static code analysis and automated scanning of Infrastructure-as-Code (IaC) as early in the development process as possible.

**Be involved in test planning.** The workload team might not design the test cases. That task is often centralized in the enterprise or completed by external security experts. The workload team should be involved in that design process to ensure that security assurances integrate with the application's functionality.

**Think like an attacker.** Design your test cases with the assumption that the system has been attacked. That way, you can uncover the potential vulnerabilities and prioritize the tests accordingly.

**Run tests in a structured manner and with a repeatable process.** Build your testing rigor around cadence, types of tests, driving factors, and intended outcomes.

**Use the right tool for the job.** Use tools that are configured to work with the workload. If you don't have a tool, buy the tool. Don't build it. Security tools are highly specialized, and building your own tool might introduce risks. Take advantage of the expertise and tools offered by central SecOps teams or by external means if the workload team doesn't have that expertise.

**Set up separate environments.** Tests can be classified as destructive or nondestructive. Nondestructive tests aren't invasive. They indicate there's a problem, but they don't alter functionality in order to remediate the problem. Destructive tests are invasive and might damage functionality by deleting data from a database.

Testing in production environments gives you the best information but causes the most disruption. You tend to do only nondestructive tests in production environments. Testing in nonproduction environments is typically less disruptive but might not accurately represent the production environment's configuration in ways that are important to security.

If you deploy by using IaC and automation, consider whether you can create an isolated clone of your production environment for testing. If you have a continuous process for routine tests, we recommend using a dedicated environment.

**Always evaluate the test results.** Testing is a wasted effort if the results aren't used to prioritize actions and make improvements upstream. Document the security guidelines, including best practices, that you uncover. Proper documentation that captures the results and remediation plans educates the team about the various ways that attackers might try to breach security. Conduct regular security training for developers, admins, and testers.

When you're designing your test plans, think about the following questions:

- How often do you expect the test to run, and what's the impact on your environment?

- What are the different test types that you should run?

### How often do you expect the tests to run?

Test the workload regularly to make sure changes don't introduce security risks and that there aren't any regressions. The team must also be ready to respond to organizational security validations that might be conducted at any time. There are also tests conducted in response to a security incident. The following sections provide recommendations on the frequency of tests.

#### Routine tests

Routine tests are conducted at a regular cadence, as part of your standard operating procedures. They're also driven by compliance requirements. Various tests might be run at different cadences, but the key is that they're conducted periodically and on a schedule.

You should integrate these tests into your software development lifecycle (SDLC) because they provide defense in depth at each stage. Diversify the test suite to verify assurances for identity, data storage and transmission, and communication channels. Conduct the same tests at different points in the lifecycle to ensure that there aren't any regressions. Routine tests help establish an initial benchmark. However that's just a starting point. As you uncover new issues at the same points of the lifecycle, you add new test cases. The tests also improve with repetition.

At each stage, these tests should validate code that's added or removed or configuration settings that have changed. You want to detect the security impact of those changes. You should improve the tests' efficacy with automation, balanced with peer reviews.

Consider running security tests as part of an automated pipeline or scheduled test run. The sooner you discover security issues, the easier it is to find the code or configuration change that might have caused them.

Don't rely only on automated tests. Use manual testing to detect vulnerabilities that can only be caught by human expertise. Manual testing is good for exploratory use cases and finding unknown risks.

#### Improvised tests

Improvised tests provide point-in-time validation of security defenses. These tests are usually triggered by security alerts that might have an impact on the workload at that time. Organizational mandates might require a pause-and-test mindset to verify the effectiveness of defense strategies if the alert escalates to an emergency.

The benefit of improvised tests is preparedness for a real incident. These tests can be a forcing function to do user acceptance testing (UAT).

The security team might audit all workloads and run these tests as needed. As a workload owner, you need to facilitate and collaborate with security teams. Negotiate enough lead time with security teams so that you can prepare. Acknowledge and communicate to your team and stakeholders that these are necessary disruptions.

In other cases, you might be required to run tests and report the security state of the system against the potential threat.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Because they are disruptive events, expect to reprioritize tasks, which may delay other planned work.

> ![Risk icon](../_images/risk.svg) **Risk**: There's risk of the unknown. Improvised tests might be one-time efforts without established processes or tools. But the predominant risk is the potential interruption of the rhythm of business. We need to evaluate those risks relative to the benefits.

#### Security incident tests

There are tests that detect the cause of a security incident at its source. These security gaps must be resolved to make sure the incident isn't repeated.

Incidents also indicate existing gaps in the test cases, which improves them over time. The team should apply the lessons learned from the incident and routinely incorporate improvements.

### What are the different types of tests?

Tests can be categorized by **technology** and by **testing methodologies**. Combine those categories and approaches within those categories to get complete coverage.

By adding multiple tests and types of tests, you can uncover:

- Gaps in security controls or compensating controls.

- Misconfigurations.

- Gaps in observability and detection methods.

A good threat modeling exercise can point to key areas to ensure test coverage and frequency. Recommendations on threat modeling are covered in [Security Development Lifecycle](security-development-lifecycle.md) (SDL).

Most tests described in these sections can be run as routine tests. However, repeatability can incur costs in some cases and cause disruption. Consider those tradeoffs carefully.

#### Tests that validate the technology stack

Here are some examples of types of tests and their focus areas. This isn't an exhaustive list. Test the entire stack, including the application stack, frontend, backend, APIs, databases, and any external integrations.

- Data security: Test the effectiveness of data encryption and access controls to ensure data is properly protected from unauthorized access and tampering.

- Network and connectivity: Test your firewalls to ensure they only allow expected, allowed, and safe traffic to the workload.

- Application: Test source code through application security testing (AST) techniques to make sure you're following secure coding practices and to catch runtime errors like memory corruption and privilege issues. For details, see these [community resources](#community-resources).

- Identity: Evaluate if the role assignments and conditional checks work as intended.

#### Test methodology

There are many perspectives on testing methodologies. We recommend tests that enable threat hunting by simulating real-world attacks. They can identify potential threat actors, their techniques, and their exploits that pose a threat to the workload. Make the attacks as realistic as possible. Use all of the potential threat vectors that you identified during threat modeling.

Here are some advantages of testing through real-world attacks:

- By integrating these attacks as part of routine testing, the workload is checked through an outside-in perspective to make sure the defense can withstand an attack.

- Based on the lessons they've learned, the team upgrades their knowledge and skill level. The team improves situational awareness and can self-assess their readiness to respond to incidents.

> ![Risk icon](../_images/risk.svg) **Risk**: Testing in general can impact performance. There might be business continuity problems if destructive tests delete or corrupt data. There are also risks associated with information exposure; make sure to maintain the confidentiality of data. Ensure the integrity of data after you complete testing.

Some examples of simulated tests include black-box and white-box testing, penetration testing, and war game exercises.

##### Black-box and white-box testing

These test types offer two different perspectives. In black-box tests, the internals of the system aren't visible. In white-box tests, the tester has a good understanding of the application and even has access to code, logs, resource topology, and configuration to conduct the experiment.

> ![Risk icon](../_images/risk.svg) **Risk**: The difference between the two types is upfront cost. White-box testing can be expensive in terms of time taken to understand the system. In some cases, white-box testing requires you to purchase specialized tools. Black-box testing doesn't need ramp-up time, but it might not be as effective. You might need to put in extra effort to uncover issues. It's a time investment tradeoff.

##### Tests that simulate attacks through penetration testing ("pentesting")

Security experts who aren't part of the organization's IT or application teams conduct penetration testing, or pentesting. They look at the system in the way that malicious actors scope an attack surface. Their goal is to find security gaps by gathering information, analyzing vulnerabilities, and reporting the results.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Penetration tests are improvised and can be expensive in terms of disruptions and monetary investment because pentesting is typically a paid offering by third-party practitioners.

> ![Risk icon](../_images/risk.svg) **Risk**: A pentesting exercise might affect the runtime environment and might disrupt the availability for normal traffic.

The practitioners might need access to sensitive data in the entire organization. Follow the rules of engagement to ensure that access isn't misused. See the resources listed in [Related links](#related-links).

##### Tests that simulate attacks through war game exercises

In this methodology of simulated attacks, there are two teams:

- The *red* team is the adversary attempting to model real-world attacks. If they're successful, you find gaps in your security design and evaluate the blast radius containment of their breaches.

- The *blue* team is the workload team that defends against the attacks. They test their ability to detect, respond, and remediate the attacks. They validate the defenses that have been implemented to protect workload resources.

If they're conducted as routine tests, war game exercises can provide ongoing visibility and assurance that your defenses work as designed. War game exercises can potentially test across levels within your workload(s).

A popular choice to simulate realistic attack scenarios is the Microsoft Defender for Office 365 [Attack simulation training](/microsoft-365/security/office-365-security/attack-simulation-training-get-started).

For more information, see [Insights and reports Attack simulation training](/microsoft-365/security/office-365-security/attack-simulation-training-insights).

For information about red-team and blue-team setup, see [Microsoft Cloud Red Teaming](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf).

## Azure facilitation

Microsoft Sentinel is a native control that combines security information event management (SIEM) and security orchestration automated response (SOAR) capabilities. It analyzes events and logs from various connected sources. Based on data sources and their alerts, Microsoft Sentinel creates incidents and performs threat analysis for early detection. Through intelligent analytics and queries, you can proactively hunt for security issues. In case of incidents, you can automate workflows. Also, with workbook templates, you can quickly gain insights through visualization.

For product documentation, see [Hunting capabilities in Microsoft Sentinel](/azure/sentinel/hunting).

Microsoft Defender for Cloud offers vulnerability scanning for various technology areas. For details, see [Enable vulnerability scanning with Microsoft Defender Vulnerability Management - Microsoft Defender for Cloud](/azure/defender-for-cloud/deploy-vulnerability-assessment-defender-vulnerability-management).

The practice of DevSecOps integrates security testing as part of an ongoing and continuous improvement mindset. War game exercises are a common practice that's integrated into the rhythm of business at Microsoft. For more information, see [Security in DevOps (DevSecOps)](/devops/operate/security-in-devops).

Azure DevOps supports third-party tools that can be automated as part of the continuous integration/continuous deployment pipelines. For details, see [Enable DevSecOps with Azure and GitHub - Azure DevOps](/devops/devsecops/enable-devsecops-azure-github).

## Related links

Follow the rules of engagement to make sure that access isn't misused. For guidance about planning and executing simulated attacks, see the following articles:

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement)

- [Penetration testing](/azure/security/fundamentals/pen-testing)

You can simulate Denial of Service (DoS) attacks in Azure. Be sure to follow the policies laid out in [Azure DDoS Protection simulation testing](/azure/ddos-protection/test-through-simulations).

## Community links

[Application security testing: Tools, types, and best practices - GitHub Resources](https://resources.github.com/security/application-security-testing/) describes the types of testing methodologies that can test the build-time and runtime defenses of the application.

[Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page) provides guidelines about common scenarios and the activities required to establish a baseline.

[OWASP Top Ten | OWASP Foundation](https://owasp.org/www-project-top-ten/) provides security best practices for applications and test cases that cover common threats.

## Security checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)
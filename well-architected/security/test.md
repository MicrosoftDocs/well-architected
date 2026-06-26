---
title: Architecture strategies for security testing
description: Learn about the Architecture strategies for security testing, including the tools and methodologies you can use to test the security posture of your workload.
author: simipaul
ms.author: simipaul
ms.reviewer: simipaul
ms.date: 06/24/2026
ms.topic: concept-article
---

# Architecture strategies for security testing

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|**SE:11**|Establish a testing regimen that combines approaches to prevent security problems, validate threat prevention implementations, and test threat detection mechanisms.|
|---|---|

Rigorous testing is the foundation of good security design. Testing is also a proactive way to detect vulnerabilities in the system.

Establish testing rigor through cadence and verification from multiple perspectives. Include inside-out viewpoints that test platform and infrastructure and outside-in evaluations that test the system like an external attacker.

The key strategies in this article build on the foundational testing practices described in [OE:09 Architecture strategies for testing](../operational-excellence/testing.md). Review that article first. This guide provides recommendations for testing the security posture of your workload. Implement these testing methods to improve your workload's resistance to attacks and maintain confidentiality, integrity, and availability of resources.

**Terminology**

|Term   |Definition   |
|---------|---------|
|Application security testing (AST) | A Microsoft Security Development Lifecycle (SDL) technique that uses white-box and black-box testing methodologies to check for security vulnerabilities in code.|
|Black-box testing| A testing methodology that validates the externally visible application behavior without knowledge of the internals of the system.|
|White-box testing| A testing methodology where the structure of the code is known to the practitioner.|
|Red team | A team that plays the role of an adversary and attempts to hack the system in a war game exercise.|
|Blue team | A team that defends against the attacks of the red team in a war game exercise.|
|Penetration testing | A testing methodology that uses ethical hacking techniques to validate the security defenses of a system.|
|Security Development Lifecycle (SDL) | A set of practices provided by Microsoft that supports security assurance and compliance requirements.|

## Collaborate with security experts to design tests

**Be involved in test planning.** Often, an organization centralizes this task. Make sure that your team is involved in that design process so that security assurances align with the application's functionality.

**Build an assume-breach mindset.** Design your test cases with the assumption that the system is under attack and the attacker is operating inside the environment. Simulate your tests to reflect realistic attack scenarios such as validation of the lateral movement containment within a compromised application VM. That way, you can uncover potential vulnerabilities and prioritize the tests accordingly. 

Share the architectural diagrams, threat model, and other relevant documentation to test the workload meaningfully.

## Prioritize tests based on threat modeling and critical flows

Threat modeling is a key practice to identify potential threats and vulnerabilities in your workload. Use the severity ratings from your threat model to prioritize and scope your testing efforts. The highest severity threats against your most critical flows deserve the most coverage.

Cover the entire attack surface of the workload. Evaluate identity, application code, infrastructure controls, third-party components, libraries and services, and the automated and human processes such as approval workflows and access reviews. 

Start with identity and access controls because a compromised identity bypasses most downstream defenses. Then validate network boundaries, and finally application-layer defenses. 

Prioritize flows that handle authentication, sensitive data, or financial transactions. For each critical flow, identify the threats with the highest severity rating in your threat model. Create risk-driven test cases that map each threat to the control intended to mitigate it. 

A good threat modeling exercise points to key areas for test coverage and frequency. For recommendations on threat modeling, see [Recommendations for securing a development lifecycle](secure-development-lifecycle.md).

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Stale threat models can lead to misaligned testing efforts. Regularly update your threat model to reflect changes in the workload and the evolving threat landscape.

## Take advantage of third-party expertise

Internal teams can have blind spots. Outside experts and crowdsourced researchers see your workload the way an attacker does. Bring in specialized experts to test your workload from an adversary's perspective and provide insights into the latest attack techniques and trends. 

Avoid giving overly permissive access to your workload. Give external testers only the access their specific engagement requires. A black-box penetration test needs no code or internal access, while a white-box review needs source code, design documents, or logs. 

Evaluate whether your team has the capacity to triage external reports before you launch a program. Then, establish a bug bounty program or a mechanism for the community to report security issues. Triage every reported finding, feed confirmed vulnerabilities back into your threat model, and add a test case to catch any regression.

## Test compliance controls and generate audit-ready evidence

Compliance isn't a one-time review. Treat each regulatory control as a testable requirement so you always have fresh evidence for auditors.

Identify the regulations your workload must satisfy. Map each regulatory control to a specific test case. Schedule the tests to run on a recurring cadence and before every go-live. Store the test output in an auditable location so you can produce evidence on demand.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Tests for regulatory controls can slow down operations. For example, predeployment tests add pipeline latency. There's also an added cost of running those operations. Prioritize tests for controls with the highest audit and risk impact. 

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Audit evidence is itself sensitive. Without integrity protection and access logging, the evidence store becomes both an attack target and a potential compliance violation.

## Protect test assets

Test assets are themselves an attack surface. Protect their confidentiality, integrity, and availability so your testing doesn't expose sensitive information or open new attack vectors.

- Use sanitized or synthetic data that contains no personally identifiable information (PII) or production data.
- Retain test data only as long as needed and delete it securely.
- Confirm that cross-border data residency rules are enforced in test environments that span regions.
- Generate dedicated test credentials, API keys, and certificates. Store them in a separate key vault instance with its own access policies.
- Set up isolated test environments that mirror production security controls such as network security groups (NSGs), role-based access control (RBAC) policies, firewall, and data loss prevention (DLP) rules. Apply the same segmentation guidance as production. For more information, see [Recommendations for segmentation strategy](segmentation.md).

### Conduct regular vulnerability scans on test assets

Scan test assets for vulnerabilities on the same cadence as production assets, including test code, infrastructure as code (IaC), container and VM images, repositories, and pipelines. Use tools that integrate with your development and deployment workflows to automate these checks. 

## Establish a continuous testing rhythm for the workload

Treat security testing as a continuous activity that keeps the workload's security posture current as threats, code, and configurations evolve. Run tests on a schedule so changes don't introduce security risks or regressions. Be ready for organizational security validations that can happen at any time, and for tests triggered by a security incident. The following sections describe the cadences to plan for.

### Routine tests

Routine tests set the baseline for your workload's security posture. Conduct them at a regular cadence, as part of your standard operating procedures and to meet compliance requirements. You might run various tests at different cadences, but the key is that you conduct them periodically and on a schedule. 

Diversify the test suite to verify assurances for identity, data storage and transmission, and communication channels. As you uncover new issues at the same points of the lifecycle, add new test cases. 

Don't just rely only on automated tests. Use manual testing to find vulnerabilities that only human expertise can catch, and for exploratory work on unknown risks.

### Improvised tests

Improvised tests provide point-in-time validation of security defenses. Security alerts that might affect the workload at that time trigger these tests. Organizational mandates might require a pause-and-test mindset to verify the effectiveness of defense strategies if the alert escalates to an emergency.

The benefit of improvised tests is preparedness for a real incident. These tests can be a forcing function to do user acceptance testing (UAT).

The security team might audit all workloads and run these tests as needed. As a workload owner, you need to facilitate and collaborate with security teams. Negotiate enough lead time with security teams so that you can prepare. Acknowledge and communicate to your team and stakeholders that these disruptions are necessary.

In other cases, you might be required to run tests and report the security state of the system against the potential threat.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff:** Because improvised tests are disruptive events, expect to reprioritize tasks, which might delay other planned work.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** There's risk of the unknown. Improvised tests might be one-time efforts without established processes or tools. But the predominant risk is the potential interruption of the rhythm of business. Evaluate those risks relative to the benefits.

### Security incident tests

Use tests that detect the cause of a security incident at its source. Resolve these security gaps to prevent the incident from recurring.

Incidents also improve test cases over time by uncovering existing gaps. The team should apply the lessons learned from the incident and routinely incorporate improvements.

> [!NOTE]
> This guidance makes a distinction between testing and incident response. Although testing is a detection mechanism that ideally fixes issues before production, don't confuse it with the remediation or investigation that's done as part of incident response. The aspect of recovering from security incidents is described in [Incident Response recommendations](incident-response.md).

## Validate security controls across the attack surface

Use a variety of testing methodologies to get complete coverage and uncover gaps in security controls, misconfigurations, and weaknesses in observability and detection. Most tests described in this section can run as routine tests. However, repeatability can incur costs and cause disruption. Consider those tradeoffs carefully.

**Test encryption controls.** Encryption failures are silent. Data looks protected until a breach reveals otherwise. 
- Validate that encryption is enforced, not just configured. 
- Re-test after every key rotation, certificate renewal, and infrastructure change. 

**Test network controls.** Network boundaries are where your segmentation is enforced. 
- Test them after any network topology change. 
- Verify that deny-by-default rules hold and that allowed traffic paths match your architecture intent. 

**Test application code.** Application-layer defenses are the last boundary before an attacker reaches data. 
- Validate that deployed applications resist common attack patterns rather than only scanning source code at build time. 
- Run application security testing (AST) techniques on source code to confirm secure coding practices and to catch runtime errors such as memory corruption and privilege issues. For details, see [Community links](#community-links).

## Simulate identity-based attacks and verify detection

Identity-based attacks are the most common initial attack vector. Simulate these attacks to validate that your identity controls work and that your monitoring captures the events.

Access controls are the first line of defense. Test them after every role or policy change and on an automated schedule. Simulate common attack patterns and confirm that your controls enforce least privilege and resist bypass attempts.

Consider these attack patterns when you design tests:

- Authorization bypass
- Token theft and replay
- Lateral movement across accounts or services
- Privilege escalation

Validate both sides of each control:

- Positive cases: authorized users succeed.
- Negative cases: unauthorized attempts are blocked and logged.

## Test threat detection and alerting

Detection that doesn't trigger an alert provides little value. Test your monitoring and alerting as part of every security control validation, and verify that the mechanisms designed to detect attacks work as expected.

Run end-to-end simulations of the attacks, then confirm each stage of the detection pipeline:

- Validate that security events such as sign-in attempts, permission changes, and token operations are logged with enough detail.
- Verify that your security information and event management (SIEM) platform or security operations dashboard correlates related events.
- Establish an alert service-level agreement (SLA) and test that alerts are actionable and surface within that timeframe.
- Verify that logs can't be tampered with or deleted by non-administrative accounts.
- Confirm that the detection mechanism for each simulated attack fires. For example, if you simulate a distributed denial-of-service (DDoS) attack with valid traffic patterns, confirm that rate limiting detects and mitigates it.

For mature workloads, validate governance guardrails as routine tests. Intentionally introduce insecure configurations and verify that the pipeline detects and responds. Confirm that Azure Policy or landing zone constraints enforce the expected protections. The platform or security team typically performs this testing, not the workload team.

## Strengthen defenses with adversary-based testing

Use tests that enable threat hunting by simulating real-world attacks. These tests can identify potential threat actors, their techniques, and their exploits that pose a threat to the workload. Make the attacks as realistic as possible. Use all the potential threat vectors that you identify during threat modeling.

Here are some advantages of testing through real-world attacks:

- When you make these attacks a part of routine testing, you use an outside-in perspective to check the workload and make sure the defense can withstand an attack.
- Based on the lessons they learn, the team upgrades their knowledge and skill level. The team improves situational awareness and can self-assess their readiness to respond to incidents.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Testing in general can affect performance. Destructive tests might delete or corrupt data and cause business continuity problems. There are also risks associated with information exposure. Maintain the confidentiality of data. Ensure the integrity of data after you complete testing.

Some examples of simulated tests include black-box and white-box testing, penetration testing, and war game exercises.

### Black-box and white-box testing

These test types offer two different perspectives. In black-box tests, the internals of the system aren't visible. In white-box tests, the tester has a good understanding of the application and even has access to code, logs, resource topology, and configurations for conducting the experiment.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: The difference between the two types is upfront cost. White-box testing can be expensive in terms of time taken to understand the system. In some cases, white-box testing requires you to purchase specialized tools. Black-box testing doesn't need ramp-up time, but it might not be as effective. You might need to put in extra effort to uncover issues. It's a time investment tradeoff.

### Tests that simulate attacks through penetration testing

Security experts who aren't part of the organization's IT or application teams conduct penetration testing, or *pentesting*. They look at the system in the way that malicious actors scope an attack surface. Their goal is to find security gaps by gathering information, analyzing vulnerabilities, and reporting the results.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Penetration tests are improvised and can be expensive in terms of disruptions and monetary investment because pentesting is typically a paid offering by third-party practitioners.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: A pentesting exercise might affect the runtime environment and might disrupt the availability for normal traffic.

The practitioners might need access to sensitive data in the entire organization. Follow the rules of engagement to ensure that access isn't misused. See the resources listed in [Related links](#related-links).

### Tests that simulate attacks through war game exercises

In this methodology of simulated attacks, two teams participate:

- The *red* team acts as the adversary and attempts to model real-world attacks. If they're successful, you find gaps in your security design and evaluate the blast radius containment of their breaches.

- The *blue* team is the workload team that defends against the attacks. They test their ability to detect, respond, and remediate the attacks. They validate the defenses that protect workload resources.

If you conduct these tests routinely, war game exercises can provide ongoing visibility and assurance that your defenses work as designed. War game exercises can potentially test across levels within your workloads.

A popular choice to simulate realistic attack scenarios is the Microsoft Defender for Office 365 [Attack simulation training](/microsoft-365/security/office-365-security/attack-simulation-training-get-started).

For more information, see [Insights and reports for Attack simulation training](/microsoft-365/security/office-365-security/attack-simulation-training-insights).

For information about red-team and blue-team setup, see [Microsoft Cloud Red Teaming](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf).

## Azure facilitation

Microsoft Sentinel is a native control that combines security information event management (SIEM) and security orchestration automated response (SOAR) capabilities. It analyzes events and logs from various connected sources. Based on data sources and their alerts, Microsoft Sentinel creates incidents and performs threat analysis for early detection. Through intelligent analytics and queries, you can proactively hunt for security issues. If there's an incident, you can automate workflows. Also, by using workbook templates, you can quickly gain insights through visualization.

For product documentation, see [Hunting capabilities in Microsoft Sentinel](/azure/sentinel/hunting).

Microsoft Defender for Cloud offers vulnerability scanning for various technology areas. For details, see [Enable vulnerability scanning with Microsoft Defender Vulnerability Management - Microsoft Defender for Cloud](/azure/defender-for-cloud/deploy-vulnerability-assessment-defender-vulnerability-management).

The practice of DevSecOps integrates security testing as part of an ongoing and continuous improvement mindset. War game exercises are a common practice that's integrated into the rhythm of business at Microsoft. For more information, see [Security in DevOps (DevSecOps)](/devops/operate/security-in-devops).

Azure DevOps supports third-party tools that you can automate as part of the continuous integration/continuous deployment pipelines. For details, see [Enable DevSecOps with Azure and GitHub - Azure DevOps](/devops/devsecops/enable-devsecops-azure-github).

## Related links

Follow the rules of engagement to make sure that access isn't misused. For guidance about planning and executing simulated attacks, see the following articles:

- [Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement)

- [Penetration testing](/azure/security/fundamentals/pen-testing)

You can simulate denial of service (DoS) attacks in Azure. Be sure to follow the policies laid out in [Azure DDoS Protection simulation testing](/azure/ddos-protection/test-through-simulations).

## Community links

[Application security testing: Tools, types, and best practices - GitHub Resources](https://resources.github.com/security/application-security-testing/) describes the types of testing methodologies that can test the build-time and runtime defenses of the application.

[Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/index.php/Main_Page) provides guidelines about common scenarios and the activities required to establish a baseline.

[OWASP Top Ten | OWASP Foundation](https://owasp.org/www-project-top-ten/) provides security best practices for applications and test cases that cover common threats.

## Security checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

---
title: Development lifecycle security recommendations
description: Learn about security recommendations for the development lifecycle. Ensure that your code and the process of developing code are free of security defects to provide guarantees of confidentiality, integrity, and availability.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for securing the development lifecycle

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:02](checklist.md)|"_Establish a security baseline aligned to compliance requirements, industry standards, and platform recommendations. Regularly measure your workload architecture and operations against the baseline to sustain or improve your security posture over time._"|
|--|---|

**Related guide**: [Threat analysis](threat-model.md)

At the core of a workload is the application code that implements business logic. The code and the process of developing code must be **free of security defects** to provide guarantees of confidentiality, integrity, and availability.  

It’s not sufficient to secure just the infrastructure plane through controls on identity, networking, and other measures. **Prevent bad implementation of code or a compromised code block** to strengthen the overall security posture. The usage plane, that is the application code, must also be hardened. The process of integrating security into your development lifecycle is essentially a hardening process. Like resource hardening, tightening up code development is also context agnostic. The focus is on enhancing security and not the functional requirements of the application.  For concepts related to hardening, see [Recommendations on security hardening](https://microsoft.sharepoint.com/teams/WAF2.0DevelopmentTeam/Shared%20Documents/Pillar%20development/Content/Security/guides/se-06-hardening.docx).

This guide covers **recommendations for hardening your code, development environment, and software supply chain** by applying security best practices throughout the development cycle. The guidance assumes knowledge of DevSecOps that integrates security into DevOps processes by automating security testing and validation and implementing tools like security pipelines to scan code and infrastructure as code (IaC) for vulnerabilities.  

**Definitions** 

| Term | Definition |
|--|--|
| Software development lifecycle (SDLC) | A multi-stage systematic process for developing software systems. |
| Secure development Lifecycle (SDL) | A set of pratices provided by Microsoft that serves to support security assurance and compliance reuirements. |

## Key design strategies

Security measures should be integrated at multiple points into your existing software development lifecycle to (SDLC) with these goal. 

|Goals|
|---|
|▪ Design choices do not lead to security gaps. <br> ▪ Application code and configuration does not produce vulnerabilities through exploitable implementation and improper coding practices. <br> ▪ Acquired software added as part of supply chain do not introduce security threats. <br> ▪ Application code, build, and deployment processes aren’t tampered. <br> ▪  Vulnerabilities revealed through incidents are mitigated. <br> ▪  Unused assets used are properly decommissioned. <br> ▪  Compliance requirements are not compromised or reduced. <br> ▪  Audit logging is implemented in developer environments.

These sections provide security strategies for the commonly practiced phases of SDLC.

### Requirements phase

The goal of the requirement phase is to **gather and analyze the functional and non-functional requirements** for the application or a new feature to an application. This phase is important because it facilitates the creation of guardrails tailored to the objectives of the application.  Protecting the data and integrity of your application should be a core requirement through every phase of the development lifecycle.  

For instance, the application needs to support critical user flows that allows the user to upload and manipulate data. The security design choices should cover assurances for user’s interaction with the application. How to authenticate and authorize the user identity, only allow permitted actions on the data, prevent SQL injection, and other requirements. Similarly, cover non-functional requirements, such as availability, scalability, and maintainability. The security choices should include segmentation boundaries, firewalls ingress and egress, and other cross-cutting security concerns. 

All those decisions should lead to a good definition of the security posture of the application. **Document the security requirements as an agreed-upon specification** and reflect it in the backlog. It should explicitly state the security investments and the tradeoffs and risks that the business is willing to undertake if those investments aren’t approved by business stakeholders.  For example, you might document the need to use a web application firewall (WAF) in front of your application, such as Azure Front Door or Azure Application Gateway. If business stakeholders aren't prepared to accept the additional cost of running a WAF, they need to accept the risk that application-layer attacks might be directed towards the application.

Security requirement gathering is a critical part of this phase. Without this effort, the design and implementation phases will work off unstated choices leading to security gaps. You might need to change the implementation later to accommodate security, which can be expensive.

### Design phase

In this phase, **the security requirements are converted to technical requirements**. In your technical specification, document all design decisions. You’ll be able to prevent ambiguity during implementation. Here are some typical tasks:

- **Define the security dimension of the system architecture**.

  Overlay the architecture with security controls. For example, what controls are practical on the isolation boundaries as per your [segmentation strategy](segmentation.md), the types of identities needed for the components of the application, type of encryption methods should be used. For some examples, see the illustrations shown in the Example sections of [Identity and access management](identity-access.md) and [Networking](networking.md) articles.

- **Evaluate the platform-provided affordances**. 

  It's important to understand the **division of responsibility between you and the cloud provider**. You want to avoid overlaps with Azure native security controls. You’ll get better security coverage and be able to reallocate development resources to the needs of the application.

  For example, if the design calls for a web application firewall on ingress, you can offload that responsibility to the choice of load balancer, such as Azure Application Gateway or Front Door. Avoid replicating features as custom code in your application. 

  **Choose only trusted frameworks, libraries, and supply chain software**. You design should also specify secure version control. Application dependencies should be sourced from trusted parties. **Third-party vendors should be able to meet your security requirements** and share their responsible disclosure plan. Any security incident should be promptly reported so that you can take necessary actions. Also, certain libraries might be prohibited by your organization. For example, software might be secure from vulnerabilities, but still not allowed due to licensing restrictions.

  To ensure that this is being adhered to by all contributors to the software, **maintain a list of approved and/or unapproved frameworks, libraries, vendors**. When possible, place guardrails in the development pipelines to support this list. **Automate the use of tools to scan dependencies** for vulnerabilities, as much as possible. 

- **Decide the security design patterns that the application code should implement**. Patterns can support security concepts like segmentation and isolation, strong authorization, uniform application security, and modern protocols.

  For more information, see [Cloud design patterns that support security](design-patterns.md).

- **Store application secrets securely**.

  Consider secure ways of using application secrets and pre-shared keys used by your application. **Credentials and application secrets should never be stored within the source code tree**.  Utilize external resources such as Azure Key Vault to ensure that if your source code becomes available to a potential attacker, no further access can be obtained. In general, find ways to avoid secrets. Using managed identities, when possible, is a way of achieving that goal. For more information, see [Recommendations on application secrets](application-secrets.md).

- **Define test plans**.

  Define crisp test cases for security requirements. Evaluate if you can **automate those tests as part of your pipelines**. If your team has processes for manual testing, then include security requirements for those tests. 

  > [!NOTE] 
  > Do threat modeling during this phase. Threat modeling can confirm that the design choices are aligned with the security requirements and find gaps that you should mitigate. If your workload handles highly sensitive data, invest in security experts who can help you conduct threat modelling. 

  The initial threat modeling exercise should occur during the design phase when the software's architecture and high-level design are being defined. This helps identify potential security issues before they are baked into the system's structure. However, it's not a one-time activity but rather a continuous process that should continue throughout the software's evolution.

  For more information, see [Recommendations on threat analysis](threat-model.md).

### Development and testing phase

From a security perspective, the goal is to **prevent security defects** and tampering in code, build, and deployment pipelines. 

- **Be well-trained in secure code practices**.

  The development team should **have formal and specialized training in secure coding practices**. For example, web and API developers might need specific training to protect against cross-site scripting attacks, and backend developers would benefit from in-depth training to avoid database-level attacks like SQL injection attacks.

  This training should be required to gain access to production source code.

  Perform internal peer code reviews as a method of continuous learning.  

- **Utilize security test tools**.

  Do threat modeling to evaluate the security of the application's architecture.

  Ensure coding standards by using **static application security testing (SAST)**. These tools should be integrated in the developer environment so that vulnerabilities can be detected in real time.  

  Also utilize **dynamic application security testing (DAST)** during run time. This tool chain can check for errors in security domains and simulate a set of attacks to test the application’s security resilience. These tools should be integrated into your build pipelines, when possible. 

  As part of your secure coding practices, follow industry standards on secure practices. For reference, see the links under [Community resources](#community-resoures). 

  Use linters and code analyzers to prevent credentials from getting pushed to the source code repository. For example, .NET Compiler Platform (Roslyn) Analyzers inspect your application code.

  During the build process, **use pipeline add-ons to catch credentials in the source code**. Scan all dependencies, such as third-party libraries and framework components, as part of the continuous integration process. Investigate vulnerable components that are flagged by the tool. Combine this task with other code scanning tasks that inspect code churn, test results, and coverage.

  Use a combination of various tests. For information about security testing in general, see [Recommendations for security testing](testing.md).

- **Write just enough code**.

  Reduced code footprint also means reduced chances of security defects. **Reuse code and libraries that’s already in use and have been through security validations** instead of duplicating code.

  Taking advantage of Azure features is another way to prevent unnecessary code. One way is to use managed services. For more information, see [Use platform as a service (PaaS) options - Azure Architecture Center](/azure/architecture/guide/design-principles/managed-services).

  **Write code with a deny-all mindset by default**. Create allow lists only for entities that need access.  For example, if your code that needs to determine whether some privileged operation should be allowed or denied, it should be written such that the "deny" outcome is the default case, and the "allow" outcome only happens when specifically permitted by code.

- P**rotect developer environments**.

  **Developer workstations need to be protected** through strong network and identity controls prevent any exposure. Make sure security updates are applied diligently. 

  Build agents are highly privileged and have access to the build server and the code. They must be protected with the same rigor as your workload components.  This means **access to build agents must be authenticated and authorized**, they should be network segmented with firewall controls, they should be subject to vulnerability scanning, and so on. The choice of Microsoft-hosted build agents should be preferred over self-hosted build agents. There are benefits such as clean virtual machine for each run of a pipeline. 

  Custom build agents add management complexity and can become an attack vector. **Build machine credentials must be stored securely** and the file system needs to be cleaned of any temporary build artifacts regularly. Network isolation can be achieved by only allowing outgoing traffic from the build agent, because it's using the pull model of communication with Azure DevOps.

  **The source code repository must be safeguarded** as well. Grant access to code repositories on a need-to-know basis and reduce vulnerability exposure as much as possible to delay the unintended attack exploits. **Have a thorough process to review the code** for security vulnerabilities. Use security groups for that purpose and have an approval process based on business justifications.

- **Secure code deployments**.

  It's not enough to just secure code. If it runs in exploitable pipelines, then all security efforts are futile and incomplete. **Build and release environments must also be protected** because you want to prevent a bad actor from running malicious code in your pipeline. 

- **Maintain an up-to-date inventory of every component that is integrated into the software product**. 

  Every new component that is integrated into the application increases the attack surface.  To ensure proper accountability and alerting when new components are added or updated, you should always have an inventory of these components which is stored outside of the build environment.  **Check regularly that your manifest matches what is in your build process regularly**.  This will help ensure that no new components are added unexpectedly which may contain back doors or other malware.

  **Pipeline tasks**. **Pull tasks in your pipeline from trusted sources**, such as Azure Marketplace. Run tasks authored by your pipeline vendor. GitHub tasks or GitHub actions are recommended. If you're using GitHub workflows, prefer Microsoft-authored tasks. Also, validate the tasks because they run in the security context of your pipeline. 

  **Pipeline secrets.**  Deployment assets that run inside of a pipeline have access to all the secrets in that pipeline. **Have proper segmentation in place for different stages of the pipeline** to avoid unnecessary exposure. Use secret stores that are built-into the pipeline. As a reminder, you can avoid secrets in some situations. Explore the use of workload identities (for pipeline authentication) and managed identities (for service-to-service authentication).

### Production phase

Production phase is the **last responsible opportunity to fix security gaps**. Keep a record of the golden image released in production. 

- **Keep versioned artifacts**.

  **Keep a catalog of all deployed assets with versions**. This information is useful during incident triage, mitigating issues, and getting the system back to working state. Also versioned assets can be compared against any published Common Vulnerabilities and Exposures (CVE) notice, which should be done through automation.

- **Emergency fixes**.

  Your automated pipeline design should have the flexibility to **support both regular and emergency deployments**. This is important to support the rapid and responsible security fixes. 

  A release is typically associated with multiple approval gates. Consider building an emergency process to accelerate security fixes. The process might be business and, or communication process between teams. The pipeline should allow for quick roll-forward and rollback deployments that address security fixes, critical bugs, and code updates outside of the regular deployment life cycle.

  > [!Note] 
  > Always favor security fixes over convenience. A security fix shouldn't introduce a regression or bug. If you want to accelerate the fix through an emergency pipeline, carefully consider which automated tests can be bypassed. Evaluate the value of each test against the execution time. For example, unit tests usually complete quickly. Integration or end-to-end tests can run for a long time.

- **Keep the sanctity of different environments**.

  Data used in different environments must be kept separate. **Production data shouldn’t be used in lower environments** because they might not have strict security controls like production. Avoid connecting from a non-production application to a production database, and avoid connecting non-production components to production networks.

- **Progressive exposure**.

  Use progressive exposure as a practice to **release features to a subset of users** based on certain criteria. If there are issues, the impact is minimized to those users. This approach is a common risk mitigation strategy because it reduces surface area.  As the feature matures and there’s confidence in security assurances, gradually release it to a broader set. 

### Maintenance phase

The goal of this phase is to **make sure security posture doesn’t decay over time**. SDLC is an ongoing agile process. Concepts covered in the preceding phases apply to this phase because of changing requirements over time.

**Patch management**. Keep software, libraries, and infrastructure components up to date with security patches and updates.

**Continuous improvement**. Continuously assess and improve the security of the software development process based on code reviews, feedback, lessons learned, and evolving threats.

Also, **decommission legacy assets** that are stale or no longer in use. This will reduce the surface area of the application.

Maintenance also includes incident fixes. If there are issues found in production, they need to be promptly integrated back in the process, so that there isn’t a recurrence. 

Continuously improve your secure coding practices to keep up with the threat landscape. 

## Azure facilitation

Microsoft Security Development Lifecycle (SDL) recommends secure practices that can be applied to your development lifecycle. For more information, see [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).

Defender for DevOps and the SAST tools are included as part of GitHub advanced security or Azure DevOps. They can help track secure score of the organization.

Follow Azure security recommendations described in these articles:

[Design secure applications on Microsoft Azure | Microsoft Learn](/azure/security/develop/secure-design)

[Develop secure applications on Microsoft Azure | Microsoft Learn](/azure/security/develop/secure-develop)

[Deploy secure applications on Microsoft Azure | Microsoft Learn](/azure/security/develop/secure-deploy) 

[Secure development best practices on Microsoft Azure | Microsoft Learn](/azure/security/develop/secure-dev-overview)

[Learn how Microsoft supports secure software development as part of a cybersecurity solution - Training | Microsoft Learn](/training/paths/secure-software-development-for-cybersecurity/)

## Community resources

 To catch credentials in the source code, consider using tools such as, [GitHub Advanced Security](https://docs.github.com/en/github/getting-started-with-github/about-github-advanced-security) and [OWASP source code analysis tools](https://owasp.org/www-community/Source_Code_Analysis_Tools).

Validate the security of any open-source code added to your application. Free tools to help with this assessment include:

- Mend Bolt
- [npm-audit](https://docs.npmjs.com/cli/audit)
- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/)
- [GitHub Dependabot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/about-dependabot-version-updates)
- [Configure the Microsoft Security DevOps Azure DevOps extension - Microsoft Defender for Cloud | Microsoft Learn](/azure/defender-for-cloud/azure-devops-extension)
- [OWASP Secure Coding Practices](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/stable-en/)
- [OWASP Top Ten | OWASP Foundation](https://owasp.org/www-project-top-ten/)  

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)

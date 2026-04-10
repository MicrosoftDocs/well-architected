---
title: Architecture strategies for securing a development lifecycle
description: Learn about Well-Architected Framework Security recommendations for securing a development lifecycle. 
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 03/16/2026
ms.topic: concept-article
---

# Architecture strategies for securing a development lifecycle

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|**SE:02**|Align secure development lifecycle (SDL) throughout the software development lifecycle (SDLC) to ensure confidentiality, integrity, and availability of software and adopt a security-first mindset.|
|--|---|

Application code is the core of every workload. Embed security throughout the SDLC by integrating controls at multiple stages of development. The goal is to make sure design decisions don't introduce avoidable security gaps and that code and configuration choices don't result in exploitable vulnerabilities. 

:::image type="content" source="images/secure-development-lifecycle/devops-security.png" alt-text="A diagram of the security cycle.":::

This guide describes security best practices to protect application code and prevent vulnerable implementation and the introduction of compromised components. Extend the best practices to third-party and open-source components to reduce supply chain risk. The focus is not on the infrastructure layer.

The guidance is grounded in SDL practices and assumes a foundational understanding of DevSecOps principles. It extends beyond application code to address the full software supply chain, including developer workstations, source repositories, build systems, and deployment environments. 


**Terminology**

| Term | Definition |
|--|--|
| Common Vulnerabilities and Exposures (CVE) | A publicly available database of known security vulnerabilities and exposures in software and hardware products. |
| Defense-in-depth | A security strategy that uses multiple layers of protection to safeguard systems, so if one layer fails, others continue to provide security. |
| DevSecOps | An approach that integrates security practices into the DevOps process, emphasizing collaboration between development, security, and operations teams throughout the software lifecycle. |
| Dynamic Application Security Testing (DAST) | Security testing that analyzes applications during runtime to identify vulnerabilities by simulating real-world attack scenarios. |
| Managed identity | An Azure feature that provides applications with an automatically managed identity in Microsoft Entra ID, eliminating the need to store credentials in code. |
| Progressive exposure | A deployment strategy that gradually releases changes to subsets of users to minimize risk and contain potential issues. |
| Security Development Lifecycle (SDL) | A set of practices provided by Microsoft that supports security assurance and compliance requirements. |
| Software development lifecycle (SDLC) | A multistage, systematic process for developing software systems. |
| Static Application Security Testing (SAST) | Security testing that analyzes source code, bytecode, or binaries without executing the program to identify potential vulnerabilities. |
| STRIDE | A threat modeling methodology that categorizes threats into six types: Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, and Elevation of privilege. |
| Supply chain security | Protection of the entire software supply chain, including third-party components, development tools, and processes from compromise or tampering. |
| Threat modeling | A structured process for identifying, evaluating, and mitigating potential security threats to a system early in the design phase. |


## Think about security from day one

During the earliest planning and architecture discussions, **define both functional and non-functional security requirements alongside business objectives**. Overlay the architecture with security controls. Think about isolation boundaries, outside-in and inside-out access, and sensitivity levels of that applies to workload data. We recommend following the [Security checklist](./checklist.md) diligently to develop the security dimension of the architecture. Also review [architecture design patterns that support security](design-patterns.md).

To keep the team accountable, security considerations and outcomes must be added directly into backlog items so they're designed and delivered as part of the product. For example, consider an application that supports critical user flows enabling users to upload and manipulate data. Security deliverables must address how users interact with the system (enforcing strong authentication and authorization) to ensure only permitted actions are allowed. 

Architects must explicitly document security trade-offs and formalize risk acceptance to ensure transparency and accountability. For example, a security decision might require measures to block OWASP vulnerabilities upfront. If stakeholders opt not to fund it, they must acknowledge and accept the resulting risk of application-layer attacks.


## Choose technology options that support security

Identify the security controls required to achieve your desired outcomes and leverage Azure's native capabilities where possible. For example, define a segmentation strategy using identity, network, and resource boundaries, and select web application firewalls (WAFs) such as Azure Front Door or Azure Application Gateway to protect the application. When a WAF is needed at ingress, use these managed services instead of replicating their functionality in custom application code.

Technical considerations also include selecting only trusted frameworks, libraries, and supply chain software, sourced from verified providers. Third-party vendors must meet your security requirements and maintain a responsible disclosure plan, promptly reporting any security incidents. Maintain a list of approved and disallowed assets, and, where possible, enforce guardrails in development pipelines to prevent use of unapproved components. For approved dependencies, automated scanning helps detect vulnerabilities early and consistently.

Decide how to store application secrets and pre-shared keys in a secure manner. Never store credentials or secrets in the source code repository. Use external tools, such as Azure Key Vault, so that even if the source code is exposed, attackers can't gain access. Where possible, avoid using secrets altogether, for example, by leveraging managed identities. For more guidance, see [Recommendations for managing application secrets](application-secrets.md).

## Make threat modeling a design discipline

_Threat modeling_ helps you identify vulnerabilities, evaluate threats, and define mitigations early in the design phase. Start by defining the scope: set clear system boundaries and inventory your assets so you focus on what matters most. Gather detailed information about each component, including data flows and dependencies, and analyze each one from an attacker's perspective to determine how it could be exploited. Adopt an *assume breach* mindset—plan for control failures and apply defense-in-depth to limit impact.

Use an industry methodology such as [STRIDE](/azure/security/develop/threat-modeling-tool-threats) to classify threats and drive mitigation decisions. Document every identified threat, the controls that prevent it, and the response plan if those controls fail. Define clear timelines and ownership to remediate vulnerabilities quickly so they don't remain unaddressed.

Track threat modeling results and revisit them throughout the workload lifecycle. Update the model as the architecture evolves to ensure new features and changes don't introduce unmanaged risk.

> Refer to: [Microsoft Threat Modeling Tool](/azure/security/develop/threat-modeling-tool)

## Build secure coding expertise

Ensure the development team completes **formal, role-specific training in secure coding practices**. For example, web and API developers should know how to prevent cross-site scripting (XSS), while back-end developers should understand how to mitigate risks such as SQL injection and other database-layer attacks. Require developers to complete this training before granting access to production source code. Reinforce these skills through internal peer code reviews, which promote accountability, knowledge sharing, and continuous improvement.

## Use security testing as a strategic control

Make security testing a core engineering discipline. Establish a testing strategy that evaluates architecture, code, and runtime behavior continuously throughout the development lifecycle. Use a mix of architectural analysis, static and dynamic testing, dependency scanning, and automated guardrails. 

Use static application security testing (SAST) to detect vulnerabilities in code as developers write it. Complement this with dynamic application security testing (DAST) to evaluate the running application and simulate real-world attack scenarios. 

Integrate automated security scanning into build and integration workflows so that security becomes a quality gate. Scan dependencies and third-party components continuously, and treat vulnerable libraries as supply chain risks that require active management. Use linters and code analyzers to prevent sensitive data, such as credentials, from entering source control, and reinforce these protections with pipeline controls that detect and block secret exposure.

Adopt industry standards as a way to boost confidence your security resilience. For more information, see the [Community resources](#community-links) section of this article.


## Write just enough code

Every line of code increases your attack surface. **Prioritize reuse over reinvention**. Use well-established frameworks and libraries that have already undergone security validation instead of duplicating functionality in custom code.

Adopt a platform-first mindset. Use managed Azure services and PaaS capabilities whenever possible to offload heavy lifting to Azure, such as authentication, encryption, scaling, and ingress protection. The less custom security logic you write, the lower your long-term risk.

When you do write code, **default to deny**. Design authorization logic so that access is blocked unless explicitly allowed. Use allowlists for specific, approved entities, and ensure privileged operations succeed only when clearly authorized.

## Fortify developer environments

Your development environment is part of your attack surface. **Protect developer workstations with the same rigor as production systems**. This means enforce strong identity controls, apply network protections, and maintain disciplined patching. A compromised workstation can become a direct path into your codebase and build systems.

Your **source code repository is a critical asset**. Grant access on a least-privilege, need-to-know basis. Establish structured, security-focused code review processes with clear approval workflows tied to business justification. Strong governance over repositories and pipelines reduces the likelihood of tampering and limits the impact of a breach.

Also, network access should be segmented so developers don't have direct access to production systems. 

Development tools can also help improve security by using IDE extensions that monitor code as it is written and scan local builds for vulnerabilities. Credentials must be protected by ensuring secrets are not stored in configuration files and by using credential managers when access to secrets is required. 

Developers should follow approved developer tools and use installations that are managed centrally. Development environments, such as GitHub Codespaces and Microsoft Dev Box, can enforce security by providing isolated, centrally managed workspaces. 

Tests should also be run in a controlled environment with only the minimum permissions necessary.

## Secure build and deployment pipelines

Build and deployment pipelines are prime targets. Attackers  can tamper with your pipeline, inject malicious code, access secrets, or compromise downstream environments. 

Treat build agents as high-value assets. They have privileged access to source code and build pipelines, which makes them attractive targets. **Authenticate and authorize access strictly, segment them at the network level, and subject them to continuous security monitoring.** Prefer Microsoft-hosted build agents over self-hosted ones to reduce operational risk and limit persistence, since they provide clean environments for each pipeline run. Custom agents increase management overhead and expand the attack surface.

Secure build credentials and remove temporary artifacts to prevent leakage. Where possible, isolate build agents by restricting inbound access and allowing only controlled outbound communication.

Start by maintaining clear visibility and control. **Keep an up-to-date inventory of all integrated components and dependencies**, and regularly verify that what runs in the pipeline matches what is approved. **Only use pipeline tasks and extensions from trusted, validated sources**, recognizing that they execute with privileged access. 

**Protect credentials by eliminating hard-coded secrets and favoring managed identities and secure secret stores.** **Segment pipeline stages to reduce unnecessary exposure** of sensitive assets and limit lateral movement if a stage is compromised.

Environment isolation is equally important. **Strictly separate production and non-production systems**, avoid using production data in lower environments without equivalent protections, and prevent direct connectivity that could allow a breach to spread. Control risk through progressive exposure. **Release changes gradually, using feature flags or staged rollouts**, so that if a vulnerability surfaces, you contain the impact. Design your pipelines to support both regular and emergency deployments. Security fixes often require rapid response, and your process should allow quick roll-forward or rollback without compromising system stability. Establish clear communication and approval procedures to accelerate emergency changes responsibly.

> [!Note] 
> Always prioritize security fixes over convenience. But never compromise quality or introduce regressions. If you need to accelerate a fix through an emergency pipeline, assess which automated tests can be safely bypassed. Consider the tradeoff between each test's value and its execution time. For example, unit tests are quick and important, while integration or end-to-end tests may take longer but still provide critical coverage. Make these decisions intentionally to balance speed with confidence in the fix.

## Protect code in production

Production is the **last line of defense** for protecting developer code before it reaches users. Teams should maintain a record of the **golden image** deployed to production and keep a catalog of all deployed assets and their versions to support troubleshooting, incident response, and vulnerability management. It also can influence configuration drift mechanisms. Automated checks against published CVEs help identify outdated or risky components quickly. 

Development environments shouldn't have direct production access. That access should be restricted. Also, regularly rotate secrets and certificates, and conduct security-focused exercises such as tabletop simulations and red-teaming to validate defenses. 

## Protect code from development to decomission

**Protecting code is an ongoing responsibility**. The SDLC is iterative, and requirements evolve, so practices from earlier phases must continue to be applied and reinforced over time.

Keep all software, libraries, and infrastructure components up to date with timely security patches. Continuously assess and improve your processes by incorporating lessons from code reviews, feedback, incident investigations, and emerging threats. Promptly integrate fixes from production issues back into the development lifecycle to prevent recurrence.

Decommission legacy assets that are no longer in use to reduce the attack surface and simplify maintenance. Regularly refine secure coding practices to stay ahead of evolving threats, ensuring your security posture remains strong and resilient throughout the entire lifecycle.


## Azure facilitation

Microsoft Security Development Lifecycle (SDL) recommends secure practices that you can apply to your development lifecycle. For more information, see [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/).

Defender for DevOps and the SAST tools are included as part of GitHub Advanced Security or Azure DevOps. These tools can help you track a security score for your organization.

Follow the Azure security recommendations that are described in these resources:

- [Design secure applications on Azure](/azure/security/develop/secure-design)

- [Develop secure applications on Azure](/azure/security/develop/secure-develop)

- [Deploy secure applications on Azure](/azure/security/develop/secure-deploy) 

- [Secure development best practices on Azure](/azure/security/develop/secure-dev-overview)

- [Training: Learn how Microsoft supports secure software development as part of a cybersecurity solution](/training/paths/secure-software-development-for-cybersecurity/)

## Community links

 To find credentials in source code, consider using tools like [GitHub Advanced Security](https://docs.github.com/en/github/getting-started-with-github/about-github-advanced-security) and [OWASP source code analysis tools](https://owasp.org/www-community/Source_Code_Analysis_Tools).

Validate the security of any open-source code in your application. These free tools and resources can help you with your assessment:

- [Mend Bolt](https://www.mend.io/free-developer-tools/bolt/)
- [npm-audit](https://docs.npmjs.com/cli/audit)
- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/)
- [GitHub Dependabot](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/about-dependabot-version-updates)
- [Microsoft Security DevOps Azure DevOps extension](/azure/defender-for-cloud/azure-devops-extension)
- [OWASP Secure Coding Practices](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/stable-en/)
- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)  

## Related links

- [Architecture design patterns that support security](design-patterns.md)
- [Design secure applications on Azure](/azure/security/develop/secure-design)
- [Deploy secure applications on Azure](/azure/security/develop/secure-deploy)
- [Develop secure applications on Azure](/azure/security/develop/secure-develop)
- [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/)
- [Recommendations for building a segmentation strategy](segmentation.md)
- [Recommendations for hardening resources](harden-resources.md)
- [Recommendations for managing application secrets](application-secrets.md)
- [Recommendations for security testing](test.md)
- [Recommendations for threat analysis](threat-model.md)
- [Secure development best practices on Azure](/azure/security/develop/secure-dev-overview)
- [Training: Learn how Microsoft supports secure software development as part of a cybersecurity solution](/training/paths/secure-software-development-for-cybersecurity/)
- [Use platform as a service (PaaS) options](/azure/architecture/guide/design-principles/managed-services)

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

---
title: Recommendations for securing a development lifecycle
description: Learn about Well-Architected Framework Security recommendations for securing a development lifecycle. 
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 10/09/2023
ms.topic: conceptual
---

# Recommendations for securing a development lifecycle

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:02](checklist.md)|Maintain a secure development lifecycle by using a hardened, mostly automated, and auditable software supply chain. Incorporate a secure design by using threat modeling to safeguard against security-defeating implementations.|
|--|---|

**Related guide**: [Threat analysis](threat-model.md)

This guide describes the **recommendations for hardening your code, development environment, and software supply chain** by applying security best practices throughout the development cycle. To understand this guidance, you should have knowledge of DevSecOps. 

:::image type="content" source="./images/devops-security.png" alt-text="A diagram of the security cycle.":::

DevSecOps integrates security into DevOps processes by: 

- Automating security testing and validation.

- Implementing tools like security pipelines to scan code and infrastructure as code (IaC) for vulnerabilities.  

At the core of a workload is the application code that implements business logic. The code and the process of developing code must be **free of security defects** to ensure confidentiality, integrity, and availability.  

It's not enough to secure just the infrastructure plane by using controls on identity and networking and other measures. **Prevent bad implementation of code or a compromised code block** to strengthen your overall security posture. The usage plane, that is, the application code, must also be hardened. The process of integrating security into your development lifecycle is essentially a hardening process. Like resource hardening, tightening up code development is also context-agnostic. The focus is on enhancing security and not the functional requirements of the application. For information related to hardening, see [Recommendations for hardening resources](hardening.md).

**Definitions** 

| Term | Definition |
|--|--|
| Security Development Lifecycle (SDL) | A set of practices provided by Microsoft that supports security assurance and compliance requirements. |
| Software development lifecycle (SDLC) | A multistage, systematic process for developing software systems. |

## Key design strategies

Security measures should be integrated at multiple points into your existing Software Development Lifecycle (SDLC) to ensure: 

- Design choices don't lead to security gaps. 

- Application code and configuration don't create vulnerabilities because of exploitable implementation and improper coding practices. 

- Software acquired via the supply chain doesn't introduce security threats. 

- Application code, build, and deployment processes aren't tampered with.  

- Vulnerabilities revealed through incidents are mitigated.  

- Unused assets are properly decommissioned.  

- Compliance requirements aren't compromised or reduced.  

- Audit logging is implemented in developer environments.

The following sections provide security strategies for the commonly practiced phases of SDLC.


### Requirements phase

The goal of the requirements phase is to **gather and analyze the functional and non-functional requirements** for an application or a new feature of an application. This phase is important because it facilitates the creation of guardrails that are tailored to the objectives of the application. Protecting the data and integrity of your application should be a core requirement throughout every phase of the development lifecycle.  

For example, consider an application that needs to support critical user flows that enable the user to upload and manipulate data. The security design choices should cover assurances for the user's interaction with the application, like authenticating and authorizing the user identity, allowing only permitted actions on the data, and preventing SQL injection. Similarly, cover non-functional requirements like availability, scalability, and maintainability. Security choices should include segmentation boundaries, firewall ingress and egress, and other cross-cutting security concerns. 

All these decisions should lead to a good definition of the security posture of the application. **Document the security requirements in an agreed-upon specification** and reflect it in the backlog. It should explicitly state the security investments and the tradeoffs and risks that the business is willing to take on if the investments aren't approved by business stakeholders. For example, you might document the need to use a web application firewall (WAF) in front of your application, like Azure Front Door or Azure Application Gateway. If business stakeholders aren't prepared to accept the additional cost of running a WAF, they need to accept the risk that application-layer attacks might be directed toward the application.

Security requirement gathering is a critical part of this phase. Without this effort, the design and implementation phases will be based on unstated choices, which can lead to security gaps. You might need to change the implementation later to accommodate security, which can be expensive.

### Design phase

During this phase, **the security requirements are converted to technical requirements**. In your technical specification, document all design decisions to prevent ambiguity during implementation. Here are some typical tasks:

- **Define the security dimension of the system architecture.**

  Overlay the architecture with security controls. For example, controls that are practical on the isolation boundaries per your [segmentation strategy](segmentation.md), the types of identities needed for the components of the application, and the type of encryption methods to use. For some example architectures, see the illustrations in the Example sections of the [Identity and access management](identity-access.md#example) and [Networking](networking.md#example) articles.

- **Evaluate platform-provided affordances.**

  It's important to understand the **division of responsibility between you and the cloud provider**. Avoid overlap with Azure native security controls, for example. You'll get better security coverage and be able to reallocate development resources to the needs of the application.

  For example, if your design calls for a web application firewall on ingress, you can offload that responsibility to a load balancer like Application Gateway or Azure Front Door. Avoid replicating features as custom code in your application. 

  **Choose only trusted frameworks, libraries, and supply chain software.** Your design should also specify secure version control. Application dependencies should be sourced from trusted parties. **Third-party vendors should be able to meet your security requirements** and share their responsible disclosure plan. Any security incident should be promptly reported so that you can take necessary actions. Also, certain libraries might be prohibited by your organization. For example, software might be secure from vulnerabilities but still disallowed because of licensing restrictions.

  To ensure that this guidance is followed by all contributors to the software, **maintain a list of approved and/or unapproved frameworks, libraries, and vendors**. When possible, place guardrails in the development pipelines to support the list. As much as possible, **automate the use of tools to scan dependencies** for vulnerabilities. 

- **Determine the security design patterns that the application code should implement.** Patterns can support security concerns like segmentation and isolation, strong authorization, uniform application security, and modern protocols.

  For more information, see [Cloud design patterns that support security](design-patterns.md).

- **Store application secrets securely.**

  Securely implement the use of application secrets and pre-shared keys that your application uses. **Credentials and application secrets should never be stored in the source code tree.** Use external resources like Azure Key Vault to ensure that, if your source code becomes available to a potential attacker, no further access can be obtained. In general, find ways to avoid secrets. Using managed identities, when possible, is one way to achieve that goal. For more information, see [Recommendations for managing application secrets](application-secrets.md).

- **Define test plans.**

  Define clear test cases for security requirements. Evaluate whether you can **automate those tests in your pipelines**. If your team has processes for manual testing, include security requirements for those tests. 

  > [!NOTE] 
  > Perform threat modeling during this phase. Threat modeling can confirm that design choices are aligned with security requirements and expose gaps that you should mitigate. If your workload handles highly sensitive data, invest in security experts who can help you conduct threat modelling. 

  The initial threat modeling exercise should occur during the design phase when the software's architecture and high-level design are being defined. Doing it during that phase helps you to identify potential security issues before they're incorporated into the system's structure. However, this exercise isn't a one-time activity. It's a continuous process that should continue throughout the software's evolution.

  For more information, see [Recommendations for threat analysis](threat-model.md).

### Development and testing phase

During this phase, the goal is to **prevent security defects** and tampering in code, build, and deployment pipelines. 

- **Be well-trained in secure code practices.**

  The development team should **have formal and specialized training in secure coding practices**. For example, web and API developers might need specific training to protect against cross-site scripting attacks, and back-end developers can benefit from in-depth training to avoid database-level attacks like SQL injection attacks.

  Developers should be required to complete this training before they can gain access to production source code.

  You should also perform internal peer code reviews to promote continuous learning.  

- **Use security test tools.**

  Perform threat modeling to evaluate the security of the application's architecture.

  Use **static application security testing (SAST)** to analyze code for vulnerabilities. Integrate this methodology into the developer environment to detect vulnerabilities in real time.  

  Use **dynamic application security testing (DAST)** during runtime. This tool chain can check for errors in security domains and simulate a set of attacks to test the application's security resilience. When possible, integrate this tool into your build pipelines. 

  Follow industry standards for secure coding practices. For more information, see the [Community resources](#community-links) section of this article. 

  Use linters and code analyzers to prevent credentials from getting pushed to the source code repository. For example, .NET Compiler Platform (Roslyn) Analyzers inspect your application code.

  During the build process, **use pipeline add-ons to catch credentials in the source code**. Scan all dependencies, like third-party libraries and framework components, as part of the continuous integration process. Investigate vulnerable components that are flagged by the tool. Combine this task with other code scanning tasks that inspect code churn, test results, and coverage.

  Use a combination of tests. For information about security testing in general, see [Recommendations for security testing](testing.md).

- **Write just enough code.**

  When you reduce your code footprint, you also reduce the chances of security defects. **Reuse code and libraries that are already in use and have been through security validations** instead of duplicating code.

  Taking advantage of Azure features is another way to prevent unnecessary code. One way is to use managed services. For more information, see [Use platform as a service (PaaS) options](/azure/architecture/guide/design-principles/managed-services).

  **Write code with a deny-all approach by default.** Create allowlists only for entities that need access.  For example, if you have code that needs to determine whether a privileged operation should be allowed, you should write it so that the *deny* outcome is the default case and the *allow* outcome  occurs only when specifically permitted by code.

- **Protect developer environments.**

  **Developer workstations need to be protected** with strong network and identity controls to prevent exposure. Make sure security updates are applied diligently. 

  Build agents are highly privileged and have access to the build server and the code. They must be protected with the same rigor as your workload components. This means that **access to build agents must be authenticated and authorized**, they should be network-segmented with firewall controls, they should be subject to vulnerability scanning, and so on. Microsoft-hosted build agents should be preferred over self-hosted build agents. Microsoft-hosted agents provide benefits like clean virtual machines for each run of a pipeline. 

  Custom build agents add management complexity and can become an attack vector. **Build machine credentials must be stored securely**, and you need to regularly remove any temporary build artifacts from the file system. You can achieve network isolation by only allowing outgoing traffic from the build agent, because it's using the pull model of communication with Azure DevOps.

  **The source code repository must be safeguarded** as well. Grant access to code repositories on a need-to-know basis and reduce exposure of vulnerabilities as much as possible to avoid attacks. **Have a thorough process to review code** for security vulnerabilities. Use security groups for that purpose, and implement an approval process that's based on business justifications.

- **Secure code deployments.**

  It's not enough to just secure code. If it runs in exploitable pipelines, all security efforts are futile and incomplete. **Build and release environments must also be protected** because you want to prevent bad actors from running malicious code in your pipeline. 

- **Maintain an up-to-date inventory of every component that's integrated into your application.**

  Every new component that's integrated into an application increases the attack surface. To ensure proper accountability and alerting when new components are added or updated, you should have an inventory of these components. Store it outside of the build environment. **On a regular basis, check that your manifest matches what's in your build process.** Doing so helps ensure that no new components that contain back doors or other malware are added unexpectedly.

  **Pipeline tasks.** **Pull tasks in your pipeline from trusted sources**, like Azure Marketplace. Run tasks that are written by your pipeline vendor. We recommend GitHub tasks or GitHub Actions. If you use GitHub workflows, prefer Microsoft-authored tasks. Also, validate tasks because they run in the security context of your pipeline. 

  **Pipeline secrets.**  Deployment assets that run inside a pipeline have access to all the secrets in that pipeline. **Have proper segmentation in place for different stages of the pipeline** to avoid unnecessary exposure. Use secret stores that are built into the pipeline. Remember that you can avoid using secrets in some situations. Explore the use of workload identities (for pipeline authentication) and managed identities (for service-to-service authentication).

### Production phase

The production phase presents the **last responsible opportunity to fix security gaps**. Keep a record of the golden image that's released in production. 

- **Keep versioned artifacts.**

  **Keep a catalog of all deployed assets and their versions.** This information is useful during incident triage, when you're mitigating issues, and when you're getting the system back to working state. Versioned assets can also be compared against published Common Vulnerabilities and Exposures (CVE) notices. You should use automation to perform these comparisons.

- **Emergency fixes.**

  Your automated pipeline design should have the flexibility to **support both regular and emergency deployments**. This flexibility is important to support rapid and responsible security fixes. 

  A release is typically associated with multiple approval gates. Consider creating an emergency process to accelerate security fixes. The process might involve communication among teams. The pipeline should allow for quick roll-forward and rollback deployments that address security fixes, critical bugs, and code updates that occur outside of the regular deployment lifecycle.

  > [!Note] 
  > Always prioritize security fixes over convenience. A security fix shouldn't introduce a regression or bug. If you want to accelerate the fix through an emergency pipeline, carefully consider which automated tests can be bypassed. Evaluate the value of each test against the execution time. For example, unit tests usually complete quickly. Integration or end-to-end tests can run for a long time.

- **Keep different environments separate.**

  Data used in different environments must be kept separate. **Production data shouldn't be used in lower environments** because those environments might not have the strict security controls that production has. Avoid connecting from a non-production application to a production database, and avoid connecting non-production components to production networks.

- **Progressive exposure.**

  Use progressive exposure to **release features to a subset of users** based on chosen criteria. If there are issues, the impact is minimized to those users. This approach is a common risk mitigation strategy because it reduces surface area. As the feature matures and you have more confidence in security assurances, you can gradually release it to a broader set of users. 

### Maintenance phase

The goal of this phase is to **make sure security posture doesn't decay over time**. SDLC is an ongoing agile process. Concepts covered in the preceding phases apply to this phase because requirements change over time.

**Patch management.** Keep software, libraries, and infrastructure components up to date with security patches and updates.

**Continuous improvement.** Continuously assess and improve the security of the software development process by taking into account code reviews, feedback, lessons learned, and evolving threats.

**Decommission legacy assets** that are stale or no longer in use. Doing so reduces the surface area of the application.

Maintenance also includes incident fixes. If issues are found in production, they need to be promptly integrated back into the process so that they don't recur. 

Continuously improve your secure coding practices to keep up with the threat landscape. 

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

- [Cloud design patterns that support security](design-patterns.md)
- [Design secure applications on Azure](/azure/security/develop/secure-design)
- [Deploy secure applications on Azure](/azure/security/develop/secure-deploy)
- [Develop secure applications on Azure](/azure/security/develop/secure-develop)
- [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl/)
- [Recommendations for building a segmentation strategy](segmentation.md)
- [Recommendations for hardening resources](hardening.md)
- [Recommendations for managing application secrets](application-secrets.md)
- [Recommendations for security testing](testing.md)
- [Recommendations for threat analysis](threat-model.md)
- [Secure development best practices on Azure](/azure/security/develop/secure-dev-overview)
- [Training: Learn how Microsoft supports secure software development as part of a cybersecurity solution](/training/paths/secure-software-development-for-cybersecurity/)
- [Use platform as a service (PaaS) options](/azure/architecture/guide/design-principles/managed-services)

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

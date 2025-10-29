---
title: Security Maturity Model
description: Understand the maturity model levels of the security pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 07/14/2025
ms.topic: concept-article
ms.update-cycle: 1095-days  
---

# Security maturity model

The security maturity model provides a progressive path to strengthen your workload's security posture. 

Start by establishing essential foundations like encryption and identity management, then build on this base by securing your deployment processes and hardening your systems. As you mature, you'll proactively identify threats through modeling and implement comprehensive monitoring. Use real-world production insights to continuously refine your security mechanisms, and eventually implement specialized protection for organizations facing sophisticated threats.

> :::image type="icon" source="../_images/risk.svg"::: **Security requires intentional action.** Security incidents can result in significant financial costs, regulatory penalties, and damage to customer trust. A practical, incremental approach can help you implement effective security measures without overwhelming your development process or compromising operational efficiency.

The model is structured into five distinct maturity levels, each with a primary goal and a set of core strategies. Use the tabbed views below to explore each level. Be sure to also review the highlighted tradeoffs and associated risks as you progress.

# [**Level 1: Core security**](#tab/level1)

![Goal icon](../_images/goal.svg) **Establish a minimum viable security posture to serve as a foundation to build on.**

Level 1 of the maturity model helps workload teams achieve a solid security foundation that they can expand and improve on throughout the workload's lifecycle. This foundation, known as the *security baseline*, captures the minimum security requirements and expectations that you need to implement. Anchor the baseline in well-defined and mature industry standards and regulatory framework guidance.

The baseline should inform the architectural design of the workload. It should indicate where to implement security mechanisms and how those mechanisms interact with other workload components. The baseline should inform not only security tools but also standardized processes around workload operations, including DevOps practices. Coding practices, like input validation and output encoding, must have secure processes built in by default. Conduct regular code reviews and automated security scans.

**Key strategies**
> [!div class="checklist"]
>
> - [Integrate baseline security into the development phases of the software development lifecycle (SDLC)](#-integrate-baseline-security-into-the-development-phases-of-the-software-development-lifecycle-sdlc)
> - [Externalize identity and access management to an identity provider (IdP)](#-externalize-identity-and-access-management-to-an-identity-provider-idp)
> - [Observe access patterns of key identities and apply an appropriate level of security](#-observe-access-patterns-of-key-identities-and-apply-an-appropriate-level-of-security)
> - [Encrypt data at rest](#-encrypt-data-at-rest)
> - [Encrypt data in transit](#-encrypt-data-in-transit)
> - [Protect application secrets](#-protect-application-secrets)

#### &#10003; Integrate baseline security into the development phases of the software development lifecycle (SDLC)

As you begin the development phase of your workload implementation, standardize practices that align to your security baseline requirements. These practices should include regularly occurring code reviews and automated security scans, input validation, and output encoding. For more information about best practices, see [Develop secure applications in Azure](/azure/security/develop/secure-develop).

#### &#10003; Externalize identity and access management to an identity provider (IdP)

Identity and access management can quickly become complex and burdensome as your workload development progresses. Use an IdP, like Microsoft Entra, to help maintain security standards by tightly controlling access to workload components and using nonhuman identities, like managed identities.

IdPs also enhance security and compliance through multifactor authentication and detailed access logs. These features streamline user interactions while reducing operational burden.

#### &#10003; Observe access patterns of key identities and apply an appropriate level of security

As you implement your IdP solution, take some time to observe access behaviors across your workload teams. Learn how users access different workload components so that you can determine the appropriate level of access to grant. Look for opportunities to replace human access to processes, like deployments and database changes, with managed identities. If human accounts require access to sensitive resources, standardize just-in-time access as the default mechanism.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** You might encounter resistance when you adopt these access policies. Some users might think that these policies slow down their work. Ensure that all workload team members understand that security is everyone's responsibility and implementing strong access controls helps everyone maintain a secure workload.

#### &#10003; Encrypt data at rest 

Secure data at rest to help ensure data confidentiality and integrity, two cornerstones of modern security. Use strong encryption and apply strict access controls on data stores. Azure encrypts all data stores by default at the underlying hardware level. But you can implement encryption to your workload data to add extra security measures. Configure encryption on your virtual machine (VM) disks, storage accounts, and databases by using built-in mechanisms to keep your design simple.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** You can bring your own key (BYOK) to many Azure services, instead of using a Microsoft-managed key. BYOK provides more control over your resources and might satisfy regulatory requirements. But BYOK adds operational burden because you must manage your key rotation. And if you lose your key, you risk losing access to your data.

#### &#10003; Encrypt data in transit

Secure data in transit to help protect your workload from attackers that might access your data and systems. If you don't use encryption or use a weak cipher, attackers can intercept your data. Don't use Transport Layer Security (TLS) version 1.1 or lower in any component. Migrate older versions to make TLS 1.2 the default version for all systems. All Azure services that send data across networks or the internet use TLS 1.2.

#### &#10003; Protect application secrets

Application secrets are confidential components that facilitate communication between workload components, including sensitive data such as passwords, API keys, and certificates for authentication and resource access. Properly manage these secrets to maintain security and integrity. Improper handling can lead to data breaches, service disruption, regulatory violations, and other problems. Use a solution like Azure Key Vault to manage secrets securely.

# [**Level 2: Threat prevention**](#tab/level2)

![Goal icon](../_images/goal.svg) **Strengthen deployment security and establish threat prevention measures across your workload infrastructure.**

In Level 2 of the Security pillar, you build on your baseline security configuration to further reduce potential threats during workload deployment. This stage emphasizes strengthening deployment practices, establishing a maintenance plan for code assets and workload components, developing a data classification framework, securing network ingress points, and hardening workload components—all essential steps to enhance your overall security posture.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Securing your SDLC is an interative process that requires adopting new processes and sometimes a shift in mindset for developers. Applying controls on deployments can be frustrating for developers, so it helps to foster a culture of shared responsibility for security. While potentially slowing down development velocity, securing your deployments sets your team up for long-term success.
These measures help you safely build your workload and get it ready for operational use while maintaining a solid security posture.

**Key strategies**
> [!div class="checklist"]
>
> - [Secure the deployment phase of your SDLC](#-secure-the-deployment-phase-of-your-sdlc)
> - [Develop a maintenance plan](#-develop-a-maintenance-plan)
> - [Classify data based on sensitivity needs](#-classify-data-based-on-sensitivity-needs)
> - [Apply authorization and authentication controls](#-apply-authorization-and-authentication-controls)
> - [Secure your network ingress](#-secure-your-network-ingress)
> - [Harden the attack surface](#-harden-the-attack-surface)

#### &#10003; Secure the deployment phase of your SDLC

Level 1 of the Security pillar focuses on securing the development phase of your SDLC. Level 2 assumes that you established baseline security measures for the development phase and that you're ready to deploy the first iterations of your workload or components of your workload.

In this phase, focus on building your deployment automation to optimize efficiency and security. Use deployment pipelines like [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) or [GitHub Actions](https://docs.github.com/actions/about-github-actions/understanding-github-actions) and standardize using these pipelines **exclusively for all changes** to your workload. Routinely perform good code hygiene practices to ensure that your code base is free of defects and code that might introduce risks. Finally, familiarize your team with the [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl). As your workload evolves, regularly revisit the recommendations in this guidance to ensure that your SDLC remains optimized for security.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Securing your SDLC is an iterative process that requires you to adopt new processes and sometimes a shift in mindset for developers. Applying controls on deployments can be frustrating for developers, so it helps to foster a culture of shared responsibility for security. Securing your deployments potentially reduces development velocity, but it sets your team up for long-term success.

#### &#10003; Develop a maintenance plan

In the context of security, a maintenance plan refers to standard practices that you adopt to maintain the security of your code and workload components throughout their life cycles. Build mechanisms and processes to handle emergency fixes in your deployment pipeline. For example, you might accelerate deployments through quality gates by using direct communication between teams and developing expedited roll-back and roll-forward plans.

Include software, libraries, and infrastructure patching in your standard processes to ensure that all components of your workload are always up to date. Keep a catalog of versioned assets to help during incident response, problem resolution, and system recovery. You can also use automation to compare these versions with known vulnerabilities in the Common Vulnerabilities and Exposures program.

#### &#10003; Classify data based on sensitivity needs

Adopt a data classification system and its supporting processes to help ensure that you maintain confidentiality and integrity. Start with broad categories like Public, General, Confidential, and Highly Confidential, and apply appropriate levels of security to protect those categories throughout your data stores. Consider investing in tooling like [Microsoft Purview](/purview/purview) to govern your data. For detailed best practices, see the [data classification guidance](/compliance/assurance/assurance-create-data-classification-framework) in the Microsoft compliance documentation.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Data classification can be an expensive undertaking in terms of costs and effort, even if you use tooling. Once you've created your initial categories and performed an initial classification exercise, determine how much effort will be involved with ongoing maintenance manually or with tooling. Be sure to factor in time and costs for training in your estimates.

#### &#10003; Apply authorization and authentication controls

As part of your IdP solution implementation, you can start applying controls related to authorization and authentication. Use role-based access controls to help limit access to workload components by applying granular permissions to resources based on user roles. Apply these permissions based on the principle of least access.

Further enhance your controls by using conditional access policies. These policies grant or deny access to resources based on specific conditions like a user's geographic location or whether a user's device complies with security policies. You can also take advantage of features like just-in-time access to lock down access to sensitive components.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** Administrative accounts are one of the most crucial attack vectors in your environment. You should only create and use them after you carefully consider your needs and how they align with [privileged accounts best practices](/entra/identity/role-based-access-control/best-practices). If attackers gain control of an administrative account, your entire environment might be at severe risk.

#### &#10003; Secure your network ingress

Secure your network ingress as much as practical to improve your overall security posture. A secure network ingress is your first line of defense against outside attackers. Your cloud provider might have various tools that you can use in your specific environment, but be sure to understand all possible ingress points in your workload. You might add a firewall to a virtual network or its subnets, like network security groups in Azure virtual networks. If you use platform resources like Azure SQL Database, you might have options to limit or disable public and private access within the configuration of the resource itself. Likewise, limit or disable any direct access to virtual machines to a practical extent.

In general, choose a native or partner firewall to control all ingress to your workload. You might also use a web application firewall that's built into a load balancing solution, like Azure Front Door, or an API gateway, like Azure API Management.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Firewall solutions can add a significant cost to your workload, especially if they're over-provisioned. Investigate the best solution for your scenario and ensure that you can start small and scale up as your workload evolves to keep costs under control.

#### &#10003; Harden the attack surface

Hardening the workload is an iterative process that requires continuous improvement. Be vigilant and analyze the workload for vulnerabilities. As your workload matures, use a vulnerability scanning tool to help you easily identify vulnerable components. Early in your development, a better strategy might be to perform the hardening exercise manually. Look at the configurations of your components to find potential weaknesses, such as misconfigured or unconfigured firewall rules or inappropriate permissions. Look for any unused or unnecessary components that you can shut down or remove entirely and for unused accounts that you can deactivate.

# [**Level 3: Risk assessment**](#tab/level3)

![Goal icon](../_images/goal.svg) **Proactively identify and mitigate security threats with thorough assessment and response capabilities.**

At Level 3 of the maturity model, you should integrate advanced processes and mechanisms into your workload to proactively identify and mitigate security threats. Strategies like threat modeling, network flow classifications, and advanced encryption techniques build an extra level of preparedness on the foundational mechanisms that you should already have in place. An incident response plan unifies your threat detection and mitigation strategies while standardizing the way that you manage security incidents.

**Key strategies**
> [!div class="checklist"]
>
> - [Incorporate threat modeling into your software development lifecycle (SDLC)](#-incorporate-threat-modeling-into-your-software-development-lifecycle-sdlc)
> - [Classify network traffic flows](#-classify-network-traffic-flows)
> - [Use advanced encryption strategies](#-use-advanced-encryption-strategies)
> - [Implement system auditing](#-implement-system-auditing)
> - [Build an incident response plan](#-build-an-incident-response-plan)

#### &#10003; Incorporate threat modeling into your software development lifecycle (SDLC)

Threat modeling is an engineering technique that you can use to help identify threats, attacks, vulnerabilities, and countermeasures that could affect your workload. You can use threat modeling to shape your workload's design, meet your company's security objectives, and reduce risk. When you perform a threat modeling exercise, include the following strategies:

- *Validate the workload security requirements.* Complete the process of gathering and codifying the workload security requirements early in your workload development. At Level 3, validate the requirements as a preliminary step in the threat modeling exercise.

- *Validate the workload architectural diagram.* An architectural diagram with flows should be completed early in the workload development and implementation process. At Level 3, you should revisit the design to ensure that it meets customer requirements.

- *Identify potential threats.* Analyze potential threats for each component from an outside-in perspective. Determine how an attacker might exploit a specific resource to gain further access. Classify threats according to an industry-standard methodology like [STRIDE](/azure/security/develop/threat-modeling-tool-threats) to help you understand the nature of each threat and apply appropriate security controls.

- *Plan mitigation strategies.* After you identify potential threats, start building mitigation plans to enhance your hardening design. Include these mitigation strategies in your team's backlog for tracking.

- *Use threat modeling tooling.* Use a tool like the [Microsoft Threat Modeling tool](/azure/security/develop/threat-modeling-tool) to make the exercises more efficient and standardize the approach and reporting processes.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Threat modeling is an intensive exercise and can slow development. Account for the extra effort required in your development planning.

#### &#10003; Classify network traffic flows

To classify network traffic flows, start by examining your workload architecture schematic to understand the flow's intent and characteristics. Consider the flow's network characteristics, such as protocol and packet details, and any compliance requirements. Classify the flow based on its visibility from external networks. Distinguish between public and private workloads. Implement security measures like load balancers or firewalls to protect critical flows.

#### &#10003; Use advanced encryption strategies

Review your compliance requirements and reevaluate your encryption configurations to determine how you can enhance your design by using advanced encryption strategies. For example, you might have a requirement to [use double encryption](/azure/security/fundamentals/double-encryption), or you might need to [manage your encryption keys](/azure/security/fundamentals/encryption-customer-managed-keys-support).

If you need to manage your own keys, use a key management service to mitigate the risk of losing a key or failing to rotate keys according to your requirements. Determine which service is the [best match for your use case](/azure/security/fundamentals/key-management-choose).

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Using double encryption or managing your own keys adds costs and operational burden to your workload. Be sure to research these strategies for your specific requirements before you implement them.

#### &#10003; Implement system auditing

To maintain system integrity, keep an accurate and up-to-date record of the system state to promptly address any issues. Track resource creation and decommissioning, monitor configuration changes, and ensure logs capture specifics and timing of changes. Additionally, maintain a comprehensive view of patching processes, detect changes in the operating system, and set up alerts for unexpected changes.

#### &#10003; Build an incident response plan

Create an incident response plan that allows you to rapidly detect and respond to potential and active security compromises. The plan should include the following considerations:

- *Identify the incident owners on the workload team.* One or more individuals on the workload team should be responsible for receiving alert notifications and working with triage teams to efficiently respond to incidents.

- *Investigate and triage processes.* Determine the appropriate communication methods, like asynchronous updates versus a bridge call. Only include necessary personnel to maintain focus on the immediate problem. Ensure that you keep architectural diagrams and other documentation about the workload current to ensure that the team can work efficiently.

- *Recover from incidents.* Treat security incidents like disasters, and align your incident response plan to your business continuity and disaster recovery (BCDR) plan. Minimize the risk of recurrence by mitigating the problem before you reintroduce the compromised component.

- *Learn from incidents.* Perform post-incident reviews, also known as *postmortems*, to look for improvement opportunities. Include time in your planning for implementing improvements, and include improvements in your BCDR drills.

- *Communicate with end users and stakeholders.* Ensure that you keep users and stakeholders up-to-date as you work through incidents. Define the proper communication channels and cadence to send out updates.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Investigation, mitigation, and recovery processes can affect your reliability targets. You might need to disable parts of your system during an incident. This approach might affect functional or nonfunctional requirements. Business decision-makers must decide what the acceptable recovery target should be during an incident.

# [**Level 4: System hardening**](#tab/level4)

![Goal icon](../_images/goal.svg) **Refine security controls based on production insights and operational data.**

At Level 4, your workload should have been running in production long enough to gather useful data about normal operating conditions. You should have observability data for security purposes, including audit logs, vulnerability scan reports, firewall logs, component usage patterns, incident reports, and other data points that you can analyze for improvement opportunities. Standardize a regular review of your security mechanisms to help optimize workload security and reinforce a continuous improvement mindset.

As you refine your security mechanisms, follow mature change management practices to ensure that all changes are performed safely and remain auditable.

**Key strategies**
>
> [!div class="checklist"]
>
> - [Revisit and refine the security baseline continuously](#-revisit-and-refine-the-security-baseline-continuously)
> - [Refine your security monitoring strategy](#-refine-your-security-monitoring-strategy)
> - [Tighten your network security at the edge](#-tighten-your-network-security-at-the-edge)
> - [Refine your identity and access management (IAM) configurations](#-refine-your-iam-configurations)
> - [Refine the incident response plan](#-refine-the-incident-response-plan)

#### &#10003; Revisit and refine the security baseline continuously

As part of your operational continuous improvement practices, regularly review the security baseline and look for improvement opportunities. When you enhance the workload with new features or technologies, you might introduce new security vulnerabilities. So keeping the baseline up-to-date is a necessary parallel activity. Also, as your team's security expertise grows, you might find baseline configurations that you can refine to further strengthen your security posture.

Use automated security governance tools like Azure Policy and Microsoft Defender for Cloud to streamline resource compliance with your baseline.

#### &#10003; Refine your security monitoring strategy

Use production insights to make improvements to your security monitoring and alerting. When you first implemented your resource auditing, vulnerability scanning, or other security monitoring, you might have followed a generic approach to logging levels, retention policies, or other settings. Use the data that you gather in production to refine these settings based on usage patterns that align with your organizational standards. As your workload evolves, continuously review security monitoring and alerting implementation to ensure that all resources are properly configured.

#### &#10003; Tighten your network security at the edge

Enhance network security by applying microsegmentation to prevent lateral movement across the workload. This strategy might include moving components into separate subnets protected by network security groups or using built-in features for specific resources to limit traffic. For example, many Azure database services include a built-in firewall that you can use to limit public and private network access. Consider the following strategies:

- *Use private networking only throughout the workload.* In Azure, use Azure Private Link to connect your virtual networks to platform-as-a-service and software-as-a-service resources as much as possible. For more information, see [Service availability](/azure/private-link/availability#service-availability).

- *Protect your APIs.* Use an API gateway solution, like Azure API Management, to proxy your API calls. Using a proxy minimizes network access to the back-end APIs by exposing only the proxy and none of the back-end components to callers.

- *Refine your firewall rules.* Look for opportunities based on production observations to refine your firewall rules. You might have broadly implemented or relaxed rules in place from early work in development that you can tighten, or you might have unused rules that you can remove. Likewise, new threats and vulnerabilities constantly emerge, which makes regular updates crucial for network security. Define a standard review process for your firewall configurations as part of your continuous improvement practices to regularly review and update your settings.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Microsegmentation configurations and API gateways increase costs and complexity for your workload. Apply these measures carefully to avoid unnecessary expenses and operational overhead. For example, these measures might not be necessary for nonproduction environments or internal workloads.

#### &#10003; Refine your IAM configurations

Analyze access patterns to identify areas of improvements in your IAM configurations. Apply conditional access and just-in-time (JIT) access controls to sensitive components. Review permissions for all human and non-human accounts to ensure that the principle of least privilege is correctly enforced. Managed identities often have incorrect permissions, so include them in permissions audits. Perform these audits regularly as part of your operational practices.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Conditional access and JIT access policies require ongoing management and might require user training. Ensure that they're suitable for your use case before you implement them.

#### &#10003; Refine the incident response plan

You can't fully simulate security incidents before you operate your workload in production. Real-world incidents provide valuable insights that help improve response processes. Engage all team members involved in incident response in retrospective learning sessions to determine what went right and what areas you can improve. Incorporate these insights into incident drills to make them more realistic.

# [**Level 5: Advanced defense**](#tab/level5)

![Goal icon](../_images/goal.svg) **Strategically invest in enterprise-grade security solutions and advanced threat defense capabilities.**

Level 5 of the maturity model focuses on advanced security measures for highly mature organizations. Carefully consider each of the following recommendations against other pillars of the Well-Architected Framework to ensure that they align with your workload. You should have a clear understanding of compliance requirements, organizational standards, workload life cycle plans, and other unique environmental factors that inform your decision-making.

**Key strategies**
>
> [!div class="checklist"]
>
> - [Invest in specialized threat protection](#-invest-in-specialized-threat-protection)
> - [Invest in security information and event management (SIEM) and security orchestration, automation, and response (SOAR) solutions](#-invest-in-siem-and-soar-solutions)
> - [Invest in advanced security testing](#-invest-in-advanced-security-testing)

#### &#10003; Invest in specialized threat protection

Larger organizations and specific industries are more likely to be targets of specialized threats. To mitigate these risks, evaluate whether you should invest in a higher level of protection. Consider whether your use case warrants investment in the following solutions:

- *Distributed denial of service (DDoS) protection.* Organizations that have a large public presence are the most common targets for DDoS attacks. Cloud providers like Azure typically include free basic-level DDoS protection that's sufficient for many use cases. They also often provide advanced tiers that defend against larger, more sophisticated attacks and provide a higher level of on-call support to help mitigate ongoing attacks.

- *Automated data loss prevention (DLP).* DLP is a security strategy that identifies, monitors, and protects sensitive data from unauthorized access, misuse, or accidental disclosure. Organizations that handle large volumes of sensitive information, such as financial institutions, healthcare providers, and government agencies, benefit significantly from DLP to maintain data integrity and comply with regulations. Consider using a tool like Microsoft Purview to automate your DLP policies.

- *Protect data in use by using confidential computing.* Cloud providers typically encrypt data at rest and in transit by default. To further enhance security, protect data in use by using a confidential computing solution. This solution is especially crucial for regulated industries like healthcare and finance for government entities. Azure provides confidential [virtual machines, container services, and other platform-as-a-service and software-as-a-service solutions](/azure/confidential-computing/overview-azure-products). These solutions enable you to securely work on sensitive data while preventing exposure to unauthorized users or systems and meeting compliance requirements.

#### &#10003; Invest in SIEM and SOAR solutions

SIEM and SOAR solutions, such as [Microsoft Sentinel](/azure/sentinel/overview), automate anomalous behavior detection, threat hunting, and various response activities. These solutions significantly reduce operational burden, especially for larger organizations, by managing data collection and analysis across vast, complex environments. Microsoft Sentinel provides built-in support for many Microsoft products. This support ensures seamless integration.

#### &#10003; Invest in advanced security testing

At earlier maturity levels, you standardize security testing, including network, identity, and application assessments. At Level 5, consider investing in simulated attack testing, such as war game exercises and penetration testing. In some scenarios, you might be required to engage with a non-Microsoft vendor to perform penetration testing or other security testing to meet your compliance requirements. Research and evaluate reputable vendors to ensure that they provide the best value for your organization.

---

## Next steps

- Review the [Security design review checklist](./checklist.md) to get details on the recommendations.

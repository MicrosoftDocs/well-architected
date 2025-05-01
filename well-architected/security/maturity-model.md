---
title: Security Maturity Model
description: Understand the maturity model levels of the security pillar.
author: claytonsiemens77
ms.author: csiemens
ms.date: 1/22/2025  
ms.topic: conceptual
---

<!--
This template provides the basic structure of a maturity model article. Remove all the comments in this template before you sign-off
-->

<!-- for values for the metadata tags (product and categories) see:
For values to set, see [Taxonomies for Learn](https://review.learn.microsoft.com/help/contribute/metadata-taxonomies?branch=main#azure-category). -->

# Security maturity model

In the context of the Well-Architected Framework, security is about providing confidentiality, integrity, and availability guarantees to your workload. This is accomplished through aliging your secuirty mechanisms and processes to your particular business requirements, industry standards, and relevant regulatory frameworks.

[add art]

:::image type="content" source="../_images/reliability.svg" alt-text="Example alt-text."::: 
# [**Level 1: Secure foundation**](#tab/level1)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Establish a minimum viable security posture to serve as a foundation to build on.**

Level 1 of the maturity model helps workload teams achieve a solid security foundation that they can expand and improve on throughout the workload's lifecycle. This foundation, known as the *security baseline*, captures the minimum security requirements and expectations that you need to implement. Anchor the baseline in well-defined and mature industry standards and regulatory framework guidance.

The baseline should inform the architectural design of the workload. It should indicate where to implement security mechanisms and how those mechanisms interact with other workload components. The baseline should inform not only security tools but also standardized processes around workload operations, including DevOps practices. Coding practices, like input validation and output encoding, must have secure processes built-in by default. Conduct regular code reviews and automated security scans.

#### &#10003; Integrate baseline security into the development phases of the software development lifecycle (SDLC)

As you begin the development phase of your workload implementation, standardize practices that align to your security baseline requirements. These practices should include regularly occurring code reviews and automated security scans, input validation, and output encoding. For more information about best practices, see [Develop secure applications in Azure](/azure/security/develop/secure-develop).

#### &#10003; Externalize identity and access management to an identity provider (IdP)

Identity and access management can quickly become complex and burdensome as your workload development progresses. Use an IdP, like Microsoft Entra, to help maintain security standards by tightly controlling access to workload components and using nonhuman identities, like managed identities.

IdPs also enhance security and compliance through multifactor authentication and detailed access logs. These featuers streamline user interactions while reducing operational burden.

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

# [**Level 2 - Threat prevention controls**](#tab/level2)

![Goal icon](../_images/goal.svg) **Enhance the baseline security with threat prevention measures**

Level 2 of the Security pillar focuses on applying your first enhancements to your baseline configuration to ensure that as you deploy your workload, you minimize potential threats. This level focuses on strengthening your deployment practices, developing a maintenance plan for your code assets and workload components, starting work on developing a data classification framework, securing your network ingress points, and hardening the workload components. These measures will help you safely build out your workload and prepare for operationalizing it while maintaining a solid security posture.

#### &#10003; Secure the deployment phase of your software development lifecycle (SDLC)

Level 1 of the Security pillar focuses on securing the development phase of your SDLC. Level 2 assumes that you have established baseline security measures to the development phase and you are ready to deploy the first iterations of your workload or components of your workload. In this phase, focus on building your deployment automation to optimize your efficiency and security. Use deployment pipelines like [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) or [Github Actions](https://docs.github.com/actions/about-github-actions/understanding-github-actions) and standardize on using these pipelines **exclusively for all changes** to your workload. Routinely perform good code hygiene practices to ensure that your codebase is free of defects and code that may introduce risks. Finally, familiarize your team with the [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl). As your workload evolves, regularly revisit the recommendations found in this guidance to ensure that your SDLC remains optimized for security.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Securing your SDLC is an interative process that requires adopting new processes and sometimes a shift in mindset for developers. Applying controls on deployments can be frustrating for developers, so it helps to foster a culture of shared responsibility for security. While potentially slowing down development velocity, securing your deployments sets your team up for long-term success.

#### &#10003; Develop a maintenance plan

In the context of security, a maintenance plan refers to standard practices you adopt to maintain the security of your code and workload components throughout its lifecycle. Build mechanisms and processes to handle emergency fixes in your deployment pipeline. This might include accelerating deployments through quality gates by using direct communication between teams and developing expedited roll-back and roll-forward plans. Include patching of software, libraries, and infrastructure in your standard processes to ensure that all components of your workload are always up-to-date. Kepp a catalog of versioned assets to help during incident response, issue resolution, and system recovery. You can also compare these versions with known vulnerabilities (CVEs) using automation.

#### &#10003; Classify data based on sensitivity needs

Adopting a data classification system and its supporting processes will help you ensure that you maintain confidentiality and integrity. Start with broad categories like Public, General, Confidential, and Highly Confidential, and apply appropriate levels of security to protect those categories throughout your data stores. Investigate investing in tooling to govern your data, like [Microsoft Purview](/purview/purview). See the [data classification guidance](/compliance/assurance/assurance-create-data-classification-framework) found in Microsoft's compliance documentation for detailed best practices.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Data classification can be an expensive undertaking in terms of costs and effort, even if you use tooling. Once you've created your initial categories and performed an initial classification exercise, determine how much effort will be involved with ongoing maintenance manually or with tooling. Be sure to factor in time and costs for training in your estimates.

#### &#10003; Apply authorization and authentication controls

As part of your identity provider (IdP) soloution implementation, you can start applying controls related to authorization and authentication. Using role-based access controls (RBAC) helps you limit access to workload components by applying granular permissions to resources based on user roles. Apply these permissions based on the principle of least access. Further enhance your controls by using conditional access policies. These policies grant or deny access to resources based on certain conditions like a user's geographic location or the status of a user device's compliance with security policies. You can also take advantage of features like just-in-time access to lock down access to sensitive components. 

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: Administrative accounts are one of the most critical attack vectors in your environment and you should only create and use them after careful consideration of your needs and how they align with [privileged accounts best practices](/entra/identity/role-based-access-control/best-practices). If attackers gain control of an administrative account, your entire environment could be at severe risk.

#### &#10003; Secure your network ingress

Securing your network ingress to the extent practical greatly improves your overall security posture, as it is your first line of defense against outside attackers. Your cloud provider may have a wide variety of tools that you can use in your particular environment, but be sure to understand all possible ingress points in your workload. You may have a virtual network that you can add firewalling directly into the network or its subnets, like Network Security Groups in Azure virtual networks. If you are using platform resources like Azure SQL Database, you might have options to limit or disable public and private access within the configuration of the resource itself. Likewise, limit or disable any direct access to virtual machines to the extent practical. In general, prefer using a native or third-party firewall to control all ingress to your workload. This could also be a web application firewall that is built into a load balancing solution, like Azure Front Door or an API gateway, like Azure API Management.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Firewall solutions can add a significant cost to your workload, especially if they are over-provisioned. Investigate the best solution for your scenario and ensure that you can start small and scale up as your workload evolves to keep costs under control.


#### &#10003; Harden the attack surface

Hardening the workload is an iterative process of continuous improvement. Be vigilant about analyzing the workload for vulnerabilities. As your workload matures, using a vulnerability scanning tool will help you easily identify vulnerable components, but early in your development it may be a better strategy to perform the hardening exercise manually. Look at the configurations of your components to look for potential weaknesses like misconfigured or unconfigured firewall rules or inappropriate permissions. Look for any unused or unnecessary components that can be shutdown or removed entirely and for unused accounts that can be deactivated.

# [**Level 3 - Threat evaluation and mitigation** ](#tab/level3)

![Goal icon](../_images/goal.svg) **Proactively identify and mitigate security threats**

Level 3 of the maturity model is the point where you should implement advanced processes and mechanisms into your workload to proactively identify and mitigate security threats. Using strategies like threat modelling, network flow classifications, and advanced encryption techniques builds an additional level of preparedness on the foundational mechanisms you should already have in place. Designing an incident response plan ties all of your threat detection and mitigation strategies together and standardizes how you manage security incidents.

#### &#10003; Incorporate threat modelling into your software development lifecycle (SDLC)

Threat modelling is an engineering technique you can use to help you identify threats, attacks, vulnerabilities, and countermeasures that could affect your workload. You can use threat modeling to shape your workload's design, meet your company's security objectives, and reduce risk. When performing a threat modelling exercise, include the following strategies:

- *Validate the workload security requirements.* The process of gathering and codifying the workload security requirements should've been completed early on in your workload development. At Level 3, you should validate the requirements as a premliminary step in the threat modelling exercise.
- *Validate the workload architectural diagram.* Like gathering requirements, building an architectural diagram with flows should've been completed at an earlier stage in your workload development, and at Level 3, you should validate the diagram.
- *Identify potential threats.* Analyze potential threats for each component from an outside-in perspective. Determine how an attacker might exploit a given resource to gain further access. Classify threats according to an industry standard methodology like [STRIDE](/azure/security/develop/threat-modeling-tool-threats) to help you understand the nature of each threat and apply appropriate security controls.
- *Plan mitigation strategies.* After identifying potential threats, start building mitigation plans to enhance your hardening design. Include these mitigation strategies in your team's backlog for tracking.
- *Use threat modelling tooling.* Use a tool like the [Microsoft Threat Modelling Tool](/securityengineering/sdl/threatmodeling) to make the exercises more efficient, standardizing the approach and reporting processes.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Threat modelling is an intensive exercise and can slow down development velocity. Account for the additional effort required in your development planning.

#### &#10003; Classify network traffic flows

To classify flows, start by examining your workload architecture schematic to understand the flow's intent and characteristics. Consider the flow's network characteristics, such as protocol and packet details, and any compliance requirements. Classify the flow based on its visibility from external networks, distinguishing between public and private workloads, and implement security measures like load balancers or firewalls to protect critical flows.

#### &#10003; Use advanced encryption strategies

Review your compliance requirements and reevaluate your encryption configurations to determine how you can enhance your design with advanced encryption strategies. For example, you might have a requirement to use [double encyprtion](/azure/security/fundamentals/double-encryption), or you might need to [manage your encyption keys](/azure/security/fundamentals/encryption-customer-managed-keys-support). 

If you need to manage your own keys, use a key management service to mitigate the risk of losing a key or failing to rotate keys according to your requirements. Determine which service is the best match [for your use case](/azure/security/fundamentals/key-management-choose).

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Using double encryption or managing your own keys adds costs and operational burden to your workload. Be sure that these strategies are the right decision for your particular requirments before implementing them.

#### &#10003; Build an incident response plan

Create an incident response plan that allows you to rapidly detect and respond to potential and active security compromises. The plan should include the following considerations:

- *Identification of the incident owner(s) on the workload team.* One or more individuals on the workload team should be responsible for recieving alert notifications and working with triage teams to efficiently respond to incidents.
- *Investigation and triage processes.* Determine the appropriate communication methods, like asynchronous updates vs a bridge call. Only involve necessary personnel to keep the focus on the immediate problem. Ensure architectural diagrams and other documentation about the workload are kept up to date to ensure that the team can work efficiently.
- *Incident recovery processes.* Treat security incidents like disasters, and align your incident response plan to your business continuity and disaster recovery (BCDR) plan. Minimize the risk of recurrence by mitigating the issue before reintroducing the compromised component.
- *Learn from incidents.* Perform post-incident reviews (also known as post mortems) to look for improvement opportunities. Include time in your planning for implementing improvements and include improvements in your BCDR drills.
- *End-user and stakeholder communications.* Ensure that your users and stakeholders are kept up-to-date as you work through incidents. Define the proper communication channels and cadence to send out updates.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off**: Investigatoin, mitigation, and recovery processes can impact your reliability targets. You may need to disable parts of your system during an incident, affecting functional or nonfunctional requirements. Business decision-makers must decide what the acceptable recovery target should be during an incident.

# [Level 4](#tab/level4)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Threat prevention operations

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

# [Level 5](#tab/level5)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: 

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

---

## Next steps

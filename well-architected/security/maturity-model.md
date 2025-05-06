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

<!-- No more than 5 H4 headings per tab -->

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

# [**Level 2: Threat prevention controls**](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Enhance baseline security by using threat prevention measures.**

In Level 2 of the Security pillar, you enhance your baseline security configuration to help minimize potential threats when you deploy your workload.

This level focuses on:

- Strengthening your deployment practices.
- Developing a maintenance plan for your code assets and workload components.
- Developing a data classification framework.
- Securing your network ingress points. 
- Hardening the workload components. 

These measures help you safely build your workload and get it ready for operational use while maintaining a solid security posture.

#### &#10003; Secure the deployment phase of your SDLC

Level 1 of the Security pillar focuses on securing the development phase of your SDLC. Level 2 assumes that you established baseline security measures for the development phase and that you're ready to deploy the first iterations of your workload or components of your workload.

In this phase, focus on building your deployment automation to optimize efficiency and security. Use deployment pipelines like [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) or [GitHub Actions](https://docs.github.com/actions/about-github-actions/understanding-github-actions) and standardize using these pipelines **exclusively for all changes** to your workload. Routinely perform good code hygiene practices to ensure that your code base is free of defects and code that might introduce risks. Finally, familiarize your team with the [Microsoft Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl). As your workload evolves, regularly revisit the recommendations in this guidance to ensure that your SDLC remains optimized for security.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Securing your SDLC is an iterative process that requires you to adopt new processes and sometimes a shift in mindset for developers. Applying controls on deployments can be frustrating for developers, so it helps to foster a culture of shared responsibility for security. Securing your deployments potentially reduces development velocity, but it sets your team up for long-term success.

#### &#10003; Develop a maintenance plan

In the context of security, a maintenance plan refers to standard practices that you adopt to maintain the security of your code and workload components throughout their life cycles. Build mechanisms and processes to handle emergency fixes in your deployment pipeline. For example, you might accelerate deployments through quality gates by using direct communication between teams and developing expedited roll-back and roll-forward plans.

Include software, libraries, and infrastructure patching in your standard processes to ensure that all components of your workload are always up to date. Keep a catalog of versioned assets to help during incident response, problem resolution, and system recovery. You can also use automation to compare these versions with known vulnerabilities in the Common Vulnerabilities and Exposures program.

#### &#10003; Classify data based on sensitivity needs

Adopt a data classification system and its supporting processes to help ensure that you maintain confidentiality and integrity. Start with broad categories like Public, General, Confidential, and Highly Confidential, and apply appropriate levels of security to protect those categories throughout your data stores. Consider investing in tooling like [Microsoft Purview](/purview/purview) to govern your data. For detailed best practices, see the [data classification guidance](/compliance/assurance/assurance-create-data-classification-framework) in the Microsoft compliance documentation.

> :::image type="icon" source="../_images/trade-off.svg"::: **Trade-off:** Data classification can be an expensive undertaking in terms of costs and effort, even if you use tooling. After you create your initial categories and perform an initial classification exercise, determine how much effort you plan to apply to ongoing maintenance, either manually or with tooling. Be sure to include time and costs for training in your estimates.

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

# [Level 3](#tab/level3)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Threat evaluation and mitigation

<!-- No more than 5 H4 headings per tab -->

#### Example heading

<!-- No more than 100 words under each H4 heading. -->

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

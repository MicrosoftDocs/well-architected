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

# [**Level 1 - Secure foundation**](#tab/level1)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

![Goal icon](../_images/goal.svg) **Establish a minimum viable security posture to serve as the foundation to build upon**

Level 1 of the maturity model is designed to help workload teams achieve a solid security foundation that can be expanded and improved as the workload is developed and deployed throughout its lifecylce. This foundation is known as the security baseline and it captures all of the minimum security requirements and expectations that you need to implement. The baseline should be based on well-defined and mature industry standards and regulatory framework guidance.

The baseline should inform the architectural design of the workload, showing where security mechanisms need to be built and how they interact with other workload components. This practice doesn't only apply to security tools. You also need to standardize processes around the operation of the workload, including DevOps practices. Coding practices like input validation and output encoding must have secure processes built-in by default. Conduct regular code reviews and automated security scans.

<!-- No more than 5 H4 headings per tab -->

#### &#10003; Integrate baseline security in the development the phases of software development lifecycle (SDLC)

In addition to determining where security components should interact with other workload components, you need to define how you'll design your workload operations processes and procedures to meet your security requirements. Adopting safe deployment practices in your operations standards at the outset of your workload development will help ensure that your SDLC will follow security best practices as your workload becomes more mature and complex. See the [safe deployments guide](../operational-excellence/safe-deployments) for detailed guidance on this topic. 

#### &#10003; Externalize identity and access management to an identity provider (IdP)

Identity and access management can quickly become complex and burdensome as your workload development progresses. Using an IdP, like Microsoft Entra, can help you maintain security standards by tightly controlling access to workload components and using non-human identities, like managed identities. 

Additionally, identity providers enhance security and compliance with multi-factor authentication (MFA) and detailed access logs. This improves security and streamlines user interactions while reducing operational burden.

#### &#10003; Observe access patterns of key identities and apply appropriate level of security

As you implement your IdP solution, take some time to observe access behaviors across your workload teams. By learning how users access different workload components, you can determine the appropriate level of access that can safely be granted. Additionally, you can find opportunities to replace human access to processes like deployments and database changes with managed identities. In cases where human accounts require access to sensitive resources, standardize just-in-time access as the default mechanism. 

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: You may encounter push back when adopting these access policies. Some users may have a perception that their work is being slowed down. It's important that all workload team members understand that security is everyone's responsibility and implementing strong access is part of setting everyone up for success in maintaining a secure workload.

#### &#10003; Encrypt data at rest 

Securing your data at rest helps ensure data confidentiality and integrity, two cornerstones of modern security. Use strong encryption on all of your data stores, and ensure that you apply strict access controls to the data stores. All data stores in Azure are encrypted by default at the underlying hardware level, but you can add additional security measures by impementing encryption to your workload data. Configure encyrption on your VM disks, storage accounts, and databases using built-in mechanisms to keep your design simple.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Many Azure services allow you to bring your own key (BYOK) as an alternative to using a Microsoft-managed key. Bringing your own key gives you more control over your resources and may satisfy some regulartory requirements, but it adds operational burden by managing your key rotation and puts you at risk of losing access to your data if you lose your key.

#### &#10003; Encrypt data in transit

Securing your data in transit helps protect you from attackers gaining access to your data and your systems. If you are not using encryption or using a weak cipher, your data can be intercepted and used against you by attackers. Don't use TLS versions lower than 1.2 in any component and migrate any older versions up to 1.2, making it the default version for all of your systems. All Azure services that send data across networks or the internet use TLS 1.2.

#### &#10003; Protect application secrets

Application secrets are confidential components that facilitate communication between workload components, including sensitive data such as passwords, API keys, and certificates used for authentication and resource access. Proper management of these secrets is crucial for maintaining security and integrity, as improper handling can lead to data breaches, service disruption, regulatory violations, and other issues. Use a solution like Azure KeyVault to manage secrets securely.


# [Level 2](#tab/level2)

<!-- No more than 1 H3 heading per tab. The H3 should act as the "title" for each level/tab. -->

### Strategy focus: Threat prevention controls

<!-- No more than 5 H4 headings per tab -->

#### Example heading 

<!-- No more than 100 words under each H4 heading. -->

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
<!-- Provide at least one next step and no more than three. Include some 
context so the customer can determine why they would click the link.
-->

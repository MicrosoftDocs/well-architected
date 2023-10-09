---
title: Recommendations for hardening resources
description: Learn how to reduce an attack surface and increase attackers' costs to limit opportunities for malicious actors to exploit vulnerabilities.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for hardening resources

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:08](checklist.md)|Harden all workload components by reducing extraneous surface area and tightening configurations to increase attacker cost.|
|---|---|

This guide describes the recommendations for hardening resources by building localized controls within a workload and maintaining them to withstand repeated attacks.

Security hardening is an intentional self-preservation exercise. The goal is to **reduce the attack surface** and **increase attackers' costs in the other areas**, which limits opportunities for malicious actors to exploit vulnerabilities. To protect your workload, implement security best practices and configurations.

**Security hardening is an ongoing process** that requires continuous monitoring and adaptation to evolving threats and vulnerabilities.

**Definitions** 

| Term | Definition |
|---|---|
|Surface area|A logical footprint of a workload that contains vulnerabilities. |
| Hardening |The practice of reducing an attack surface area by removing extraneous resources or adjusting configurations.|
| Privileged Access Workstation (PAW) | A dedicated and secure machine that you use to perform sensitive tasks, which reduces the risk of compromise.|
| Secure Administrative Workstation (SAW) | A specialized PAW that critical impact accounts use.|

## Key design strategies

Security hardening is a highly localized exercise that **strengthens controls at the component level**, whether it's resources or processes. When you tighten the security of each component, it improves the aggregate security assurance of your workload.

Security hardening doesn't consider the functionality of the workload, and it doesn't detect threats or perform automated scanning. **The focus is on configuration tuning with an assume-breach and defense in depth mentality.** The goal is to make it difficult for an attacker to gain control of the system. Hardening shouldn't alter the intended utility of the workload or its operations.

The hardening process starts with a comprehensive inventory of all hardware, software, and data assets. Keep your inventory up to date with new assets and decommissioned assets. For all assets in your inventory, consider the following concepts:

- **Reduce footprint**. Remove extraneous surface area or reduce the scope. **Eliminate easy targets**, or cheap and well-established attack vectors, such as unpatched software exploits and brute force attacks. Prior to the production deployment, identities, build components, and other nonrequired assets should be cleaned from the source tree.

- **Fine tune configurations**. Evaluate and **tighten the remaining surface area**. When resources are hardened, tried and tested methods that attackers use are no longer successful. It forces attackers to acquire and use advanced or untested attack methods, which increases their costs.

- **Maintain defenses**. Maintain protective measures by performing **continuous threat detection** to make sure hardening efforts don't decay over time.

Also consider the following factors.

**Trusted source**. Part of the hardening exercise is related to the software supply chain. This guidance assumes that **all components are obtained from trusted sources**. Your organization must approve software that's procured from third-party vendors. This approval applies to sources of the operating system, images, and other third-party tools. Without trusted resources, hardening can be an infinite drain of security assurances on untrusted sources.

For recommendations about security for your supply chain, see [Recommendations for securing a development lifecycle](secure-development-lifecycle.md).

**Training**. Hardening is a specialized skill. It's methodical and requires a high level of competency. You need to understand the functionality of a component and also understand how changes affect a component. A practitioner must be able discern the guidance from industry experts and the platform compared to guidance from uncertain sources. Educate your team members in creating a security-aware culture. Ensure that your team is **proficient in security best practices, has awareness of potential threats, and learns from post-incident retrospectives**.

**Documentation**. Document and publish hardening requirements, decisions, and defined methods. For transparency, also **document exceptions or deviations** from those requirements.

Hardening can be cumbersome, but it's a crucial security exercise that you must document. Harden the core components first, and then expand to other areas, such as automated processes and human processes, to tighten up potential gaps. Be meticulous about changes. For example, a necessary step is to disable the default settings because changes to default values can't affect the stability of the system. Even if the replacement configuration is the same as the default, it must be defined. The following sections describe common targets for hardening. Evaluate key design areas of your workload and follow the key strategies to harden at a component level.

### Networking

**Divide the network into segments** to isolate critical assets and sensitive data from less secure assets, which reduces lateral movements by attackers. In those segments, apply a *deny-by-default* approach. Only add access to the allowlist if it's justified.

**Disable ports and protocols that aren't actively used**. For example, on Azure App Service, if you don't need to deploy via FTP, you can disable it. Or if you perform management operations via an internal network, you can disable administrative access from the internet.

**Remove or disable legacy protocols**. Attackers exploit systems that use old versions. Review logs by using an Azure detection system to discover protocol usage. It might be difficult to remove protocols because it can disrupt the functionality of the system. Test all changes before implementation to mitigate the risk of operational interruption.

**Treat public IP (PIP) addresses as high-risk assets** because they're easy to access and have a broad worldwide reach. To reduce exposure, remove unnecessary internet access to the workload. Use shared public IP addresses that Microsoft services, like Azure Front Door, provide. These services are designed to be internet-facing, and they block access to disallowed protocols. Many such services perform initial checks on incoming requests at the network edge. With a dedicated PIP, you're responsible for managing its security aspects, allowing or blocking ports, and scanning incoming requests to ensure their validity.

For internet-facing applications, **restrict access by adding a layer 7** service that can filter invalid traffic. Explore native services that enforce distributed denial-of-service (DDoS) protection, have web application firewalls, and provide protection at the edge before traffic reaches the application tier.

DNS hardening is another network security practice. To ensure that the DNS infrastructure is secure, we recommended that you **use trusted DNS resolvers**. To validate information from DNS resolvers and provide an extra layer of security, when possible, use a DNS security protocol for highly sensitive DNS zones. To prevent attacks such as DNS cache poisoning, DDoS attacks, and amplification attacks, explore other DNS-related security controls such as query rate limiting, response rate limiting, and DNS cookies.

### Identity

**Remove unused or default accounts.** Disable unused authentication and authorization methods.

**Disable legacy authentication methods** because they're frequently attack vectors. Old protocols often lack attack-counter measures, such as account lockouts. Externalize your authentication requirements to your identity provider (IdP), such as Microsoft Entra ID.

**Prefer federation over creating duplicate identities.** If an identity is compromised, it's easier to revoke its access when it's centrally managed.

**Understand platform capabilities** for enhanced authentication and authorization. Harden access controls by taking advantage of multifactor authentication, passwordless authentication, conditional access, and other features that Microsof Entra ID offers to verify identity. You can add extra protection around sign-in events and reduce the scope in which an attacker can make a request.

**Use managed identities** and workload identities with no credentials where possible. Credentials can be leaked. For more information, see [Recommendations for protecting application secrets](application-secrets.md).

**Use the least privilege approach for your management processes.** Remove unnecessary role assignments and perform regular Microsoft Entra ID access reviews. Use role assignment descriptions to keep a paper trail of justifications, which is crucial for audits.

### Cloud resources

The preceding hardening recommendations for networking and identity apply to individual cloud services. For networking, pay special attention to **service-level firewalls**, and evaluate their inbound rules.

**Discover and disable unused capabilities** or features, such as unused data plane access and product features, that other components might cover. For example, App Service supports Kudu, which provides FTP deployments, remote debugging, and other features. If you don't need those features, turn them off.

Always **keep up with the Azure roadmap and the workload roadmap**. Apply patching and versioning updates that Azure services offer. Allow platform-provided updates, and subscribe to automated update channels.

> ![Risk icon](../_images/risk.svg) **Risk**: Cloud resources often have requirements for allowances or must run in documented configuration to be considered *supported*. Some hardening techniques, such as aggressively blocking outbound traffic, can cause a service to fall outside of a supported configuration, even if the service operates normally. Understand each cloud resource's runtime requirements from your platform to ensure that you maintain supportability of that resource.

### Applications

Evaluate areas where your application might inadvertently leak information. For example, suppose you have an API that retrieves user information. A request might have a valid user ID and your application returns a 403. But with an invalid customer ID, the request returns a 404, then you're effectively leaking information about your user IDs.

There might be more subtle cases. For example, the response latency with a valid user ID is higher than an invalid customer ID.

Consider implementing application hardening in the following areas:

- **Input validation and sanitization**: Prevent injection attacks such as SQL injection and cross-site scripting (XSS) by validating and sanitizing all user inputs. Automate input sanitization by using input validation libraries and frameworks.

- **Session management**: Protect session identifiers and tokens from theft or session fixation attacks by using secure session management techniques. Implement session timeouts, and enforce reauthentication for sensitive actions.

- **Error management**: Implement custom error handling to minimize exposing sensitive information to attackers. Securely log errors and monitor these logs for suspicious activity.

- **HTTP security headers**: Mitigate common web vulnerabilities by utilizing security headers in HTTP responses, such as the Content Security Policy (CSP), X-Content-Type-Options, and X-Frame-Options.

- **API security**: Secure your APIs with proper authentication and authorization mechanisms. To further enhance security, implement rate limiting, request validation, and access controls for API endpoints.

Follow secure coding practices when you develop and maintain applications. Regularly scan applications for vulnerabilities and conduct code reviews. For more information, see [Recommendations for securing a development lifecycle](secure-development-lifecycle.md).

### Management operations

Also harden other non-runtime resources. For example, **reduce your build operations footprint** by taking an inventory of all assets and removing unused assets from your pipeline. Then, **pull in tasks that are published by trusted sources**, and only run tasks that are validated.

Determine if you need Microsoft-hosted or self-hosted build agents.  **Self-hosted build agents need extra management and must be hardened**.

From an observability perspective, **implement a process for reviewing logs** for potential breaches. Regularly review and update access control rules based on access logs. Work with central teams to analyze security information event management (SIEM) and security orchestration automated response (SOAR) logs to detect anomalies.

Consider requiring PAWs or SAWs for privileged management operations. PAWs and SAWs are hardened physical devices that offer significant security advantages, but their implementation requires careful planning and management. For more information, see [Securing devices as part of the privileged access story](/security/privileged-access-workstations/privileged-access-devices).

## Azure facilitation

Microsoft Defender for Cloud offers several hardening capabilities:

- [Server hardening](/azure/defender-for-cloud/apply-security-baseline)
- [Adaptive network hardening](/azure/defender-for-cloud/adaptive-network-hardening)
- Docker host hardening

The Center for Internet Security (CIS) offers hardened images in Azure Marketplace.

You can use Azure VM Image Builder to build a repeatable process for hardened OS images. Microsoft's Common Base Linux-Mariner is a hardened Linux distribution that follows security standards and industry certifications. You can use it with Azure infrastructure products to build workload implementations.

## Example

The following procedure is an example of how to harden an operating system:

1. **Reduce the footprint**. Remove unnecessary components in an image. Install only what you need.

1. **Fine tune configurations**. Disable unused accounts. The default configuration of operating systems has extra accounts that are linked to security groups. If you don't use those accounts, disable or remove them from the system. Extra identities are threat vectors that can be used to gain access to the server.

   Disable unnecessary access to the file system. Encrypt the file system and fine tune access controls for identity and networking.

   Run only what's needed. Block applications and services that run by default. Approve only applications and services that are needed for workload functionality.

1. **Maintain defenses**. Regularly update operating system components with the latest security updates and patches to mitigate known vulnerabilities.

## Organizational alignment

Cloud Adoption Framework for Azure provides guidance about creating centralized identity and access management functions. For more information, see [Azure identity and access management design area](/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access).

## Related links

- [Adaptive network hardening](/azure/defender-for-cloud/adaptive-network-hardening)
- [Recommendations for protecting application secrets](application-secrets.md)
- [Recommendations for securing a development lifecycle](secure-development-lifecycle.md)
- [Securing devices as part of the privileged access story](/security/privileged-access-workstations/privileged-access-devices)
- [Server hardening](/azure/defender-for-cloud/apply-security-baseline)

## Community links

[CIS benchmarks (cisecurity.org)](https://www.cisecurity.org/cis-benchmarks)

## Security checklist

Refer to the complete set of recommendations.

[Security checklist](checklist.md)

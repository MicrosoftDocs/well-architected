---
title: Hardening recommendations for resource security
description: Learn about hardening resources recommendations for security. Reduce the attack surface and increase attackers’ costs in the remaining area to limit the opportunities for malicious actors to exploit vulnerabilities.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for hardening resources

**Applies to: SE 06** 

Security hardening is an intentional self-preservation exercise. The goal is to reduce the attack surface and increase attackers’ costs in the remaining area to limit the opportunities for malicious actors to exploit vulnerabilities. This is typically done by implementing a set of security best practices and configurations.

Security hardening is an ongoing process that requires continuous monitoring and adaptation to evolving threats and vulnerabilities.

This guide provides recommendations on building localized controls within the workload and maintaining them to withstand attacks repeatedly.

**Definitions** 

| Terms | Definition |
|---|---|
| Hardening | |
| PAWS | |
| SAWS | |

## Key design strategies

Security hardening is a highly localized exercise that strengthens controls at the component level, whether that’s resources or processes. When you tighten the security of each component, the aggregate security assurance of the workload is improved. 

The exercise itself doesn’t take into consideration the functionality of the workload. Nor is it about detecting threats or automated scanning. The focus is on configuration tuning with an assume-breach and defense in depth mentality with the goal of making it difficult for the attacker to gain control of the system. Keep in mind that hardening shouldn’t alter the intended utility of the workload or its operations.

Hardening process starts with a comprehensive inventory of all hardware, software, and data assets. The inventory must be kept up to date with new assets and decommissioned assets. The strategy and efforts should be sustained around three main concepts for all assets in the inventory:

- **Reduce footprint**. Remove extraneous surface area or reduce the scope. You want to eliminate easy targets or cheap and well-established attack vectors, such as unpatched software exploits, brute force attacks, and others.  Build components, identities, and other non-required assets should be cleaned from the source tree prior to the production deployment.
- **Fine tune configurations**. Evaluate and tighten the remaining surface area. When resources are hardened, tried and tested methods used by attackers no longer work. It forces attackers to acquire and use advanced or untested attack methods, thereby increasing their costs.
- **Maintain defenses**. Maintain those measures through continuous threat detection to make sure hardening efforts don’t decay over time.

Here are other considerations:

**Trusted source**. Part of the hardening exercise is related to the software supply chain. This guidance assumes that all components are obtained from trusted sources. Software procured from third-party vendors is approved by the organization. That’s a prerequisite and applies to sources of the operating system, images, and other third-party tools, which are in scope for the workload. Without that assumption, hardening can be an infinite drain of security assurances on untrusted sources. 

Recommendations about security your supply chain is described in [Secure SDLC Practices](security-development-lifecycle.md).

**Training**. Hardening is a specialized skill, and the actual exercise of hardening is methodical and requires a high level of competency.  Not only do you need to understand the functionality of the component but understand the impact of changes. The practitioner must be able discern guidance given by industry experts and the platform, from uncertain sources. Educate your team members in creating a security-aware culture. The team should be proficient in security best practices, have awareness of potential threats, and learn from post-incident retrospectives.

**Documentation**. Document and publish team hardening requirements and decisions and the agreed upon methods used to accomplish that result. Document any exceptions or deviations from those requirements as a way to publish explicit decisions. 

Hardening can be cumbersome but a crucial security exercise that must be documented. Start by hardening the core components, and then expand to other areas such as automated processes and even human processes to tighten up potential gaps. Be meticulous about the changes.  For example, a necessary step is to examine the default settings and remove them because any change to default values must not impact the stability of the system. Even if the replacement configuration is the same as default, it must be defined. These sections describe some of the common targets for hardening. Evaluate the key design areas of your workload and follow the key strategies to harden at the component level. 

### Networking

Divide the network into segments to isolate critical assets and sensitive data from less secure ones, reducing the potential for lateral movement by attackers. Within those segments, apply a ‘deny-by-default’ approach. Only add to the allowlist if access is justified.

Disable ports and protocols that aren’t actively used. For example, on Azure App Service, if you don’t need to deploy using FTP, disable it. If management operations are expected to be over internal network, then disable administrative access from the internet.

Remove or disable the use of legacy protocols. Attackers exploit systems using older versions. Review logs using Azure detection system to discover protocol usage. It may not be easy to remove protocols as it might disrupt the functionality of the system. Test all changes before implementation to mitigate risk of operational interruption.

Treat public IP (PIP) addresses as high risk assets because of ease of access and broad world-wide reach. Reduce exposure by removing unnecessary access to the workload over the internet. It's good to use shared public IP addresses provided by Microsoft services that are designed to be internet-facing, like Azure Front Door or other platform services. These services are designed to block access to disallowed protocols, and many such services perform initial checks on incoming requests at the network edge. With a dedicated PIP, you're responsible for managing its security aspects, allowing or blocking specific ports, and scanning incoming requests to ensure they're valid.

For internet-facing applications, restrict access by adding layer 7 service that’s capable of filtering invalid traffic. Explore native services that enforce  DDoS protection, have web application firewall, and provide protection at the edge before traffic reaches the application tier.

DNS hardening another aspect of network security. To ensure that the DNS infrastructure is secure, it's recommended to use trusted DNS resolvers. In addition, where supported,  DNS security protocol can be used for highly sensitive DNS zones, which validates information provided by DNS resolvers, and provides an additional layer of security. Explore other DNS-related security controls such as Query Rate Limiting, Response Rate Limiting, and DNS Cookies to prevent various types of attacks such as DNS cache poisoning, DDoS attacks, and amplification attacks. 

### Identity

Remove unused or default accounts and also disable unused authentication and authorization methods.

Disable legacy authentication methods as they are top attack vectors. Older protocols frequently lack other attack-counter measures, such as account lockouts. Externalize your authentication requirements to your trusted Identity Provider (IdP), such as Azure Active Directory (Azure AD). 

Prefer federation over creating duplicate identities. If an identity is compromised, it’s easier to revoke its access when it’s centrally managed. 

Have a good understanding of platform capabilities for enhanced authentication and authorization. Harden access controls by taking advantage of multifactor authentication or passwordless authentication, conditional access, and other features offered by Azure AD to verify identity. You’ll be able to add extra protection around sign-in events and reduce the scope at which an attacker can make a request.

Use managed identities and workload identities without credentials where possible. Credentials can be leaked. For details, see [Recommendations on application secrets](application-secrets.md).

Use the least privilege approach for your management processes. Remove unnecessary role assignments, perform regular Azure AD access reviews. Use role assignment descriptions to keep a paper trail of justifications. These are crucial for audits.

### Cloud resources

The preceding hardening recommendations for networking and identity apply to individual cloud services. For networking, pay special attention to service-level firewalls. Evaluate their inbound rules.

In addition, discover and disable other unused capabilities or features that might be covered by other components, such as unused data plane access and product features. For example, Azure App Services supports Kudu, which offers FTP deployments, remote debugging, and other features. If you don’t need them, turn those features off. 

As part of your “stay current, get current” mindset, always keep up with the Azure roadmap and the workload roadmap. Apply patching and versioning updates offered by Azure services. Don’t block platform-provided updates and subscribe to automated update channels.

> ![Risk icon](../_images/risk.svg) **Risk**: Cloud resources often have requirements for allowances or must run in documented configuration to be considered "supported."_  _Some hardening techniques (such as aggressively blocking outbound traffic) can cause a service to fall outside of a supported configuration, even if the service_ _is operating normally. Ensure you understand each cloud resource's runtime requirements from your platform to ensure_ _you're maintaining supportability of that resource._

### Applications

Evaluate where your application might be inadvertently leaking information. For example, if you have an API to retrieve user information. If a request has valid user ID and your application returns 403, but with an invalid customer ID it returns a 404, then you’re effectively leaking information about your user IDs. 

There might be more subtle cases. For example, the response latency with a valid user ID is higher than an invalid customer ID.

Inspect some of these areas for application hardening:

- **Input Validation and Sanitization**: Prevent injection attacks such as SQL injection and Cross-Site Scripting (XSS) by validating and sanitizing all user inputs. Automate input sanitization using input validation libraries and frameworks.

- **Session Management**: Protect session identifiers and tokens from theft or session fixation attacks using secure session management techniques. Implement session timeouts and enforce reauthentication for sensitive actions.

- **Error Handling**: Implement custom error handling to prevent the exposure of sensitive information to attackers. Securely log errors and monitor these logs for any suspicious activities.

- **HTTP Security Headers**: Mitigate common web vulnerabilities by utilizing security headers in HTTP responses, such as Content Security Policy (CSP), X-Content-Type-Options, and X-Frame-Options.

- **API Security**: Secure your APIs with proper authentication and authorization mechanisms. Implement rate limiting, request validation, and access controls for API endpoints to further enhance security.

In general, follow secure coding practices when developing and maintaining applications.  Regularly scan applications for vulnerabilities and conduct code reviews. For more information, see [Recommendations on Secure development lifecycle](security-development-lifecycle.md).

### Management operations

Other non-runtime resources must also be hardened. For instance, reduce your build operations footprint by taking an inventory of all assets and removing all unused ones from your pipeline. Then, pull in tasks that are published by trusted sources and only run tasks that have been validated. 

Evaluate the choice of Microsoft-hosted or self-hosted build agents.  Self-hosted build agents need extra management and must be hardened.   

From an observability perspective, have a process for reviewing logs for potential breaches. Regularly review and update access control rules based on access logs. Work with central teams to analyze security information event management (SIEM) and security orchestration automated response (SOAR) logs to detect anomalies.

Consider requiring PAWs/SAWs for privileged management operations. These are hardened physical devices. PAWs/SAWs offer significant security advantages, their implementation requires careful planning and management. [Why are privileged access devices important | Microsoft Learn](/security/privileged-access-workstations/privileged-access-devices)

## Azure facilitation

Microsoft Defender for Cloud offers several hardening recommendations:

- [Server hardening](/azure/defender-for-cloud/apply-security-baseline)
- [Adaptive network hardening](/azure/defender-for-cloud/adaptive-network-hardening)
- Docker host hardening

Microsoft offers hardened images in partnership with the Center for Internet Security, as a Preferred solution in the marketplace.

Azure Image Builder can help you build a repeatable process for building hardened OS images. Common Base Linux - Mariner, is hardened Linux distribution that’s developed by Microsoft, follows security standards and industry certifications. It’s used by Azure infrastructure products. Workload implementations can also be built with this image.

## Example

Here’s an example of how you might start hardening the operating system. The techniques listed here are for learning purposes and aren’t an exhaustive list. 

### Operating system hardening

1. Reducing footprint.

  **Remove unnecessary components** in the image. Install only what you need. 

1. Fine tune configurations.

  **Disable unused accounts**. Default configuration of operating systems have extra accounts that are linked to security groups. If they aren’t going to be used, disable or remove them from the system. Extra identities are threat vectors that are used to gain access to the server.

  **Disable unnecessary access to the file system**. Encrypt the filesystem and fine tune access controls for both identity and networking. 

  **Run only what’s needed**. Block applications and services from running by default and approve only those that are need for workload’s functionality.

1. Maintain defenses.

  Regularly update OS components with the latest security updates and patches to mitigate known vulnerabilities. 

### Organizational alignment

Cloud Adoption Framework provides guidance on creating centralized identity and access management functions. 

[Azure identity and access management design area - Cloud Adoption Framework | Microsoft Learn](/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access)

## Community resources

CIS Benchmarks for OS hardening: [CIS Benchmarks (cisecurity.org)](https://www.cisecurity.org/cis-benchmarks)

## Next steps

We recommend that you review the Security checklist to explore other concepts.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

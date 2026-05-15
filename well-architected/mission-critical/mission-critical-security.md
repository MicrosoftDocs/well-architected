---
title: Security considerations for mission-critical workloads on Azure
description: This section provides detailed design considerations and recommendations for the security critical design area.
author: calcof
ms.author: calcof
ms.date: 05/05/2026
ms.topic: concept-article
---

# Security considerations for mission-critical workloads on Azure

Security is one of the foundational design principles and also a key design area that must be treated as a first-class concern within the mission-critical architectural process.

Given that the primary focus of a mission-critical design is to maximize reliability so that the application remains performant and available, the security considerations and recommendations applied within this design area will focus on mitigating threats with the capacity to impact availability and hinder overall reliability. For example, successful distributed denial-of-service (DDoS) attacks can have a catastrophic impact on availability and performance. How an application mitigates attack vectors such as SlowLoris affects overall reliability. So, the application must be fully protected against threats intended to directly or indirectly compromise application reliability to be truly mission critical in nature.

It's also important to note that there are often significant trade-offs associated with a hardened security posture, particularly with respect to performance, operational agility, and in some cases reliability. For example, the inclusion of inline network virtual appliances (NVAs) for next-generation firewall (NGFW) capabilities, such as deep packet inspection, will introduce a significant performance penalty, additional operational complexity, and a reliability risk if scalability and recovery operations are not closely aligned with that of the application. It's therefore essential that additional security components and practices intended to mitigate key threat vectors are also designed to support the reliability target of an application, which will form a key aspect of the recommendations and considerations presented within this section.

> [!IMPORTANT]
> This article is part of the [Azure Well-Architected mission-critical workload](index.yml) series. If you aren't familiar with this series, we recommend you start with [what is a mission-critical workload?](mission-critical-overview.md#what-is-a-mission-critical-workload)
>

## Alignment with the Zero Trust model

The Microsoft [Zero Trust](https://www.microsoft.com/security/business/zero-trust) model provides a proactive and integrated approach to applying security across all layers of an application. The [guiding principles of Zero Trust](/security/zero-trust/) strive to explicitly and continuously verify every transaction, assert least privilege, use intelligence and advanced detection to respond to threats in near real-time. It's ultimately centered on eliminating trust inside and outside of application perimeters, enforcing verification for anything attempting to connect to the system.

### Design considerations

As you assess the security posture of the application, start with these questions as the basis for each consideration.

- Continuous security testing to validate mitigations for key security vulnerabilities.
  - *Is security testing performed as a part of automated CI/CD processes?*
  - *If not, how often is specific security testing performed?*
  - *Are test outcomes measured against a desired security posture and threat model?*

- Security level across all lower environments.
  - *Do all environments within the development lifecycle have the same security posture as the production environment?*

- Authentication and authorization continuity in the event of a failure.
  - *If authentication or authorization services are temporarily unavailable, will the application be able to continue to operate?*

- Automated security compliance and remediation.
  - *Can changes to key security settings be detected?*
  - *Are responses to remediate non-compliant changes automated?*

- Secret scanning to detect secrets before code is committed to prevent any secret leaks through source code repositories.
  - *Is authentication to services possible without having credentials as a part of code?*

- Secure the software supply chain.
  - *Is it possible to track Common Vulnerabilities and Exposures (CVEs) within utilized package dependencies?*
  - *Is there an automated process for updating package dependencies?*

- Data protection key lifecycles.
  - *Can service-managed keys be used for data integrity protection?*
  - *If customer-managed keys are required, what is the secure and reliable key lifecycle?*

- CI/CD tooling should use [workload identity federation](/azure/devops/pipelines/library/connect-to-azure) or managed identities with least-privilege Azure RBAC for control plane access.
  - *When application resources are locked down within private networks, is there a private data-plane connectivity path so that CI/CD tooling can perform application-level deployments and maintenance?*
    - This introduces additional complexity and requires a sequence within the deployment process through requisite private build agents.

### Design recommendations

- Use Azure Policy to enforce security and reliability configurations. For details, see [Policy-driven governance](mission-critical-security.md#policy-driven-governance).

- Use Microsoft Entra Privileged Identity Management (PIM) within production subscriptions to revoke sustained control plane access to production environments. This will significantly reduce the risk posed from 'malicious admin' scenarios through additional 'checks and balances'.

- Use [managed identities](/entra/identity/managed-identities-azure-resources/overview) for all services that support the capability, since it facilitates the removal of credentials from application code and removes the operational burden of identity management for service-to-service communication.

- Use [Azure RBAC](/azure/role-based-access-control/overview) for data plane authorization with all services that support the capability.

- Use [Microsoft identity platform authentication libraries](/entra/identity-platform/reference-v2-libraries) within application code to integrate with Microsoft Entra ID.

- Consider secure token caching to allow for a degraded but available experience if the chosen identity platform, isn't available or is only partially available for application authorization.
  - If the provider is unable to issue new access tokens, but still validates existing ones, the application and dependent services can operate without issues until their tokens expire.
  - Token caching is typically handled automatically by authentication libraries ([such as MSAL](/entra/architecture/resilience-client-app#use-the-microsoft-authentication-library-msal)).

- Use Infrastructure-as-Code (IaC) and automated CI/CD pipelines to drive updates to all application components, including under failure circumstances.
  - Ensure CI/CD tooling service connections are safeguarded as critical sensitive information, and shouldn't be directly available to any service team.
  - Apply granular RBAC to production CD pipelines to mitigate 'malicious admin' risks.
  - Consider the use of manual approval gates within production deployment pipelines to further mitigate 'malicious admin' risks and provide additional technical assurance for all production changes.
    - Additional security gates may come at a trade-off in terms of agility and should be carefully evaluated, with consideration given to how agility can be maintained even with manual gates.

- Define an appropriate security posture for all lower environments to ensure key vulnerabilities are mitigated.
  - Do not apply the same security posture as production, particularly with regard to data exfiltration, unless regulatory requirements stipulate the need to do so, since this will significantly compromise developer agility.

- Enable Microsoft Defender for Cloud for all subscriptions that contain the resources for a mission-critical workload.
  - Use Azure Policy to enforce compliance.
  - Enable [Microsoft Defender for Cloud plans](/azure/defender-for-cloud/defender-for-cloud-introduction) for all services that support the capability.

- Embrace [DevSecOps](/azure/devops/devsecops/) and implement security testing within CI/CD pipelines.
  - Test results should be measured against a compliant security posture to inform release approvals, be they automated or manual.
  - Apply security testing as part of the CD production process for each release.
    - If security testing each release jeopardizes operational agility, ensure a suitable security testing cadence is applied.
  
- Enable [secret scanning](https://docs.github.com/code-security/secret-scanning/about-secret-scanning) and dependency scanning within the source code repository.

## Threat modeling

Threat modeling provides a risk-based approach to security design, using identified potential threats to develop appropriate security mitigations. There are many possible threats with varying probabilities of occurrence, and in many cases threats can chain in unexpected, unpredictable, and even chaotic ways. This complexity and uncertainty is why traditional technology-requirement-based security approaches are largely unsuitable for mission-critical cloud applications. Expect the process of threat modeling for a mission-critical application to be complex and unyielding.

To help navigate these challenges, a layered defense-in-depth approach should be applied to define and implement compensating mitigations for modeled threats, considering the following defensive layers.

1. The Azure platform with foundational security capabilities and controls.
1. The application architecture and security design.
1. Security features built-in, enabled, and deployable applied to secure Azure resources.
1. Application code and security logic.
1. Operational processes and DevSecOps.

> [!NOTE]
> When deploying within an Azure landing zone, be aware that an additional threat mitigation layer through the provisioning of centralized security capabilities is provided by the landing zone implementation.

### Design considerations

[STRIDE](https://en.wikipedia.org/wiki/STRIDE_(security)) provides a lightweight risk framework for evaluating security threats across key threat vectors.

- Spoofed Identity: Impersonation of individuals with authority. For example, an attacker impersonating another user by using their:
  - Identity
  - Authentication
- Tampering Input: Modification of input sent to the application, or the breach of trust boundaries to modify application code. For example, an attacker using SQL Injection to delete data in a database table.
  - Data integrity
  - Validation
  - Blocklisting/allowlisting
- Repudiation of Action: Ability to refute actions already taken, and the ability of the application to gather evidence and drive accountability. For example, the deletion of critical data without the ability to trace to a malicious admin.
  - Audit/logging
  - Signing
- Information Disclosure: Gaining access to restricted information. An example would be an attacker gaining access to a restricted file.
  - Encryption
  - Data exfiltration
  - Man-in-the-middle attacks
- Denial of Service: Malicious application disruption to degrade user experience. For example, a DDoS botnet attack such as Slowloris.
  - DDoS
  - Botnets
  - CDN and WAF capabilities
- Elevation of Privilege: Gaining privileged application access through authorization exploits. For example, an attacker manipulating a URL string to gain access to sensitive information.
  - Remote code execution
  - Authorization
  - Isolation

### Design recommendations

- Allocate engineering budget within each sprint to evaluate potential new threats and implement mitigations.

- Ensure security mitigations are captured within common engineering criteria to drive consistency across all application service teams.
  
- Start with service-by-service threat modeling, then consolidate the threat model at the application level.

## Network intrusion protection

> [!NOTE]
> For detailed Azure Virtual Network configuration guidance, see the [Virtual Network service guide](../service-guides/virtual-network.md).

Preventing unauthorized access to a mission-critical application and its data is vital to maintain availability and safeguard data integrity.

### Design considerations

- Zero Trust assumes a breached state and verifies each request as though it originates from an uncontrolled network.
  - An advanced zero-trust network implementation employs micro-segmentation and distributed ingress/egress micro-perimeters.

- Use private connectivity for Azure PaaS services where supported, and account for CI/CD access to private data planes. For details, see [Virtual network integration](mission-critical-networking-connectivity.md#virtual-network-integration).
  - Connectivity to the private build agents from CI/CD tooling is required.
  - Use jump boxes for developer and administrative tasks that require data-plane connectivity.
  
- Use Azure DevOps [service connections with workload identity federation](/azure/devops/pipelines/library/connect-to-azure) to apply Azure RBAC without storing secrets.

- Service Tags can be applied to Network Security Groups to facilitate connectivity with Azure PaaS services. For details, see the [Virtual Network service guide](../service-guides/virtual-network.md).

- Application Security Groups don't span across multiple virtual networks.


### Design recommendations

- Limit public network access to the absolute minimum required for the application to fulfill its business purpose to reduce the external attack surface. 
  - Use [Azure Private Link](/azure/private-link/private-endpoint-overview#private-link-resource) to establish [private endpoints](/azure/private-link/private-endpoint-overview) for Azure resources that require secure network integration.
  - Use [hosted private build agents](/azure/devops/pipelines/agents/agents#install) for CI/CD tooling to deploy and configure Azure resources protected by Azure Private Link.
    - [Microsoft-hosted agents](/azure/devops/pipelines/agents/agents#microsoft-hosted-agents) won't be able to directly connect to network integrated resources.

- When dealing with private build agents, never open an RDP or SSH port directly to the internet.
  - Use [Azure Bastion](/azure/bastion/bastion-overview) to provide secure access to Azure Virtual Machines. For details, see the [Virtual Machines service guide](../service-guides/virtual-machines.md).

- Use [Azure DDoS Protection](/azure/ddos-protection/ddos-protection-overview) to secure public IP addresses that need network-layer DDoS protection.

- Use [Azure Front Door](../service-guides/azure-front-door.md) with web application firewall policies for global HTTP/S workloads. For details, see [Application delivery services](mission-critical-networking-connectivity.md#application-delivery-services).

- If additional in-line network security requirements, such as deep packet inspection or TLS inspection, mandate the use of [Azure Firewall Premium](../service-guides/azure-firewall.md) or a network virtual appliance (NVA), ensure it's configured for maximum high availability and redundancy.

- If packet capture requirements exist, use Network Watcher packets to capture despite the limited capture window.

- Use Network Security Groups and Application Security Groups to micro-segment application traffic. For details, see [Micro-segmentation and Kubernetes network policies](mission-critical-networking-connectivity.md#micro-segmentation-and-kubernetes-network-policies) and the [Virtual Network service guide](../service-guides/virtual-network.md).

- Enable [virtual network flow logs](/azure/network-watcher/vnet-flow-logs-overview) and feed them into [Traffic Analytics](/azure/network-watcher/traffic-analytics) to gain insights into internal and external traffic flows.

- Use Azure Private Link/Private Endpoints, where available, to secure access to Azure PaaS services within the application design. For information on Azure services that support Private Link, see [Azure Private Link availability](/azure/private-link/availability).

- If Private Endpoint isn't available and data exfiltration risks are acceptable, use Virtual Network Service Endpoints to secure access to Azure PaaS services from within a virtual network.
  - Don't enable virtual network service endpoints by default on all subnets as this will introduce significant data exfiltration channels.

- For hybrid application scenarios, access Azure PaaS services from on-premises via [ExpressRoute](../service-guides/azure-expressroute.md) with private peering.

> [!NOTE]
> When deploying within an Azure landing zone, be aware that network connectivity to on-premises data centers is provided by the landing zone implementation. One approach is by using ExpressRoute configured with private peering.

## Data integrity protection

Encryption is a vital step toward ensuring data integrity and is ultimately one of the most important security capabilities that can be applied to mitigate a wide array of threats. This section will therefore provide key considerations and recommendations related to encryption and key management in order to safeguard data without compromising application reliability.

### Design considerations

- Azure Key Vault has transaction limits for keys and secrets, with throttling applied per vault within a certain period.

- Azure Key Vault supports Azure RBAC and access policies for data plane authorization.
  - Use [Azure RBAC for the Key Vault data plane](/azure/key-vault/general/rbac-guide) unless a legacy access policy model is required.

- After a role assignment is changed, allow for propagation before relying on the new permission.
  - There's a limit of [4,000 Azure role assignments per subscription](/azure/role-based-access-control/troubleshoot-limits#symptom---no-more-role-assignments-can-be-created).

- Azure Key Vault underlying hardware security modules (HSMs) have [FIPS 140 validation](/azure/key-vault/keys/about-keys#compliance).
  - A dedicated [Azure Key Vault managed HSM](/azure/key-vault/managed-hsm/overview) is available for scenarios requiring FIPS 140-2 Level 3 compliance.

- Azure Key Vault provides high availability and redundancy to help maintain availability and prevent data loss.

- During a region failover, it may take a few minutes for the Key Vault service to fail over.
  - During a failover Key Vault will be in a read-only mode, so it won't be possible to change key vault properties such as firewall configurations and settings.

- If private link is used to connect to Azure Key Vault, it may take up to 20 minutes for the connection to be re-established during a regional failover.

- A backup creates a [point-in-time snapshot](/azure/key-vault/general/backup?tabs=azure-cli#overview) of a secret, key, or certificate, as an encrypted blob that can't be decrypted outside of Azure. To get usable data from the blob, it must be restored into a Key Vault within the same Azure subscription and Azure geography.
  - Secrets may renew during a backup, causing a mismatch.

- With service-managed keys, Azure will perform key management functions, such as rotation, thereby reducing the scope of application operations.

- Regulatory controls may stipulate the use of customer-managed keys for service encryption functionality.

- When traffic moves between Azure data centers, MACsec data-link layer encryption is used on the underlying network hardware to secure data in-transit outside of the physical boundaries not controlled by Microsoft or on behalf of Microsoft.

### Design recommendations

- Use service-managed keys for data protection where possible, removing the need to manage encryption keys and handle operational tasks such as key rotation.
  - Only use customer-managed keys when there's a clear regulatory requirement to do so.

- Use Azure Key Vault for secrets, certificates, and keys when you need customer-managed cryptographic material or other secrets. For details, see [Secret management](mission-critical-operational-procedures.md#secret-management).

- Deploy a separate Azure Key Vault instance within each regional deployment stamp, providing fault isolation and performance benefits through localization, as well as navigating the scale limits imposed by a single Key Vault instance.
  - Use a dedicated Azure Key Vault instance for application global resources.

- Follow a least privilege model by limiting authorization to permanently delete secrets, keys, and certificates to specialized custom Microsoft Entra roles.

- Ensure encryption keys, and certificates stored within Key Vault are backed up, providing an offline copy in the unlikely event Key Vault becomes unavailable.

- Use Key Vault certificates to [manage certificate issuance and renewal](/azure/key-vault/certificates/certificate-scenarios#creating-a-certificate-with-a-ca-partnered-with-key-vault).

- Establish an automated process for key and certificate rotation.
  - Automate the certificate management and renewal process with public certificate authorities to ease administration.
    - Set alerting and notifications, to supplement automated certificate renewals.

- Monitor key, certificate, and secret usage.
  - Define [alerts](/azure/key-vault/general/alert) for unexpected usage within Azure Monitor.

## Policy-driven governance

Security conventions are ultimately only effective if consistently and holistically enforced across all application services and teams. Azure Policy provides a framework to enforce security and reliability baselines, ensuring continued compliance with a common engineering criteria for a mission-critical application. More specifically, Azure Policy forms a key part of the Azure Resource Manager (ARM) control plane, supplementing RBAC by restricting what actions authorized users can perform, and can be used to enforce vital security and reliability conventions across utilized platform services.

This section will therefore explore key considerations and recommendations surrounding the use of Azure Policy driven governance for a mission-critical application, ensuring security and reliability conventions are continuously enforced.

### Design considerations

- Azure Policy provides a mechanism to drive compliance by enforcing security and reliability conventions, such as the use of Private Endpoints or the use of Availability Zones.

> [!NOTE]
> When deploying within an Azure landing zone, be aware that the enforcement of centralized baseline policy assignments will likely be applied in the implementation for landing zone management groups and subscriptions.

- Azure Policy can be used to drive automated management activities, such as provisioning and configuration.
  - Resource Provider registration.
  - Validation and approval of individual Azure resource configurations.

- Azure Policy assignment scope dictates coverage and the location of Azure Policy definitions informs the reusability of custom policies.

- Azure Policy has [several limits](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-policy-limits), such as the number of definitions at any particular scope.

- It can take several minutes for the execution of Deploy If Not Exist (DINE) policies to occur.

- Azure Policy provides a critical input for compliance reporting and security auditing.

### Design recommendations

- Map regulatory and compliance requirements to Azure Policy definitions.
  - For example, if there are data residency requirements, a policy should be applied to restrict available deployment regions.

- Define a common engineering criteria to capture secure and reliable configuration definitions for all utilized Azure services, ensuring this criteria is mapped to Azure Policy assignments to enforce compliance.
  - For example, apply an Azure Policy to enforce the use of Availability Zones for all relevant services, ensuring reliable intra-region deployment configurations.

> The Mission Critical reference implementation contains security and reliability-centric policies to define and enforce a sample common engineering criteria.

- Monitor service configuration drift, relative to the common engineering criteria, using Azure Policy.

> For mission-critical scenarios with multiple production subscriptions under a dedicated management group, prioritize assignments at the management group scope.

- Use built-in policies where possible to minimize operational overhead of maintaining custom policy definitions.

- Where custom policy definitions are required, deploy definitions at suitable management group scope for reuse across production and lower-environment subscriptions.
  - When aligning the application roadmap with Azure roadmaps, use available Microsoft resources to explore if critical custom definitions could be incorporated as built-in definitions.

> [!NOTE]
> When deploying within an Azure landing zone, consider deploying custom Azure Policy Definitions within the intermediate company root management group scope to enable reuse across all applications within the broader Azure estate.
> In a landing zone environment, certain centralized security policies will be applied by default within higher management group scopes to enforce security compliance across the entire Azure estate. For example, Azure policies should be applied to automatically deploy software configurations through VM extensions and enforce a compliant baseline VM configuration.

- Use Azure Policy to enforce a consistent tagging schema across the application.
  - Identify required Azure tags and use the [modify effect](/azure/governance/policy/concepts/effect-modify) to enforce usage.

> If the application is subscribed to Microsoft Mission-Critical Support, ensure that the applied tagging schema provides meaningful context to enrich the support experience with deep application understanding.

- Export Microsoft Entra audit logs and sign-in logs to the global [Log Analytics workspace](../service-guides/azure-log-analytics.md) used by the application.
  - Ensure Azure activity logs are archived within the global storage account along with operational data for long-term retention. For details, see the [Blob Storage service guide](../service-guides/azure-blob-storage.md).

> In an Azure landing zone, Microsoft Entra logs will also be ingested into the centralized platform Log Analytics workspace. Evaluate whether they also need to be retained in the application Log Analytics workspace.

- Integrate Microsoft Defender for Cloud with a security information and event management platform, such as [Microsoft Sentinel](/azure/sentinel/sentinel-overview).

## IaaS-specific considerations when using Virtual Machines

Follow the operational and security practices for IaaS VMs when VMs are required, including enabling [Microsoft Defender for Cloud](/azure/defender-for-cloud/defender-for-cloud-introduction) for all services that support the capability. For details, see [IaaS-specific considerations when using VMs](mission-critical-operational-procedures.md#iaas-specific-considerations-when-using-vms) and the [Virtual Machines service guide](../service-guides/virtual-machines.md).

## Next step

Review the best practices for operational procedures for mission-critical application scenarios.

> [!div class="nextstepaction"]
> [Operational procedures](./mission-critical-operational-procedures.md)
---
title: Overview of the security pillar
description: Learn about the key architectural considerations and principles for security and how they apply to the Microsoft Azure Well-Architected Framework.
author: martinekuan
ms.author: martinek
ms.date: 02/08/2022
ms.topic: conceptual
categories:
  - security
ms.custom:
  - overview
---

# Overview of the security pillar

Information security has always been a complex subject, and it evolves quickly with the creative ideas and implementations of attackers and security researchers. Security vulnerabilities originated by identifying and exploiting common programming errors and unexpected edge cases. However, over time, the attack surface that an attacker may explore and exploit has expanded beyond these common errors and edge cases. Attackers now freely exploit vulnerabilities in system configurations, operational practices, and the social habits of the systems' users. As system complexity, connectedness, and the variety of users increase, attackers have more opportunities to identify unprotected edge cases. Attackers can *hack* systems into doing things they weren't designed to do.

Security is one of the most important aspects of any architecture. It provides the following assurances against deliberate attacks and abuse of your valuable data and systems:

- Confidentiality
- Integrity
- Availability

Losing these assurances can negatively affect your business operations, revenue, and reputation. For the security pillar, we'll discuss key architectural considerations and principles for security and how they apply to Azure.

The security of complex systems depends on understanding the business context, social context, and technical context. As you design your system, cover these areas:

:::image type="content" source="./images/security-areas.png" alt-text="Screenshot showing security design areas.":::

Understanding an IT solution as it interacts with its surrounding environment is key to preventing unauthorized activity and identifying anomalous behaviour that may represent a security risk.

Another key factor in success: Is adopting a mindset of assuming the failure of security controls. Assuming failure allows you to design compensating controls that limit risk and damage if a primary control fails.

Assuming failures can be referred to as *assume breach* or *assume compromise*. Assume the breach is closely related to the *Zero Trust* approach of continuously validating security assurances. The Zero Trust approach is described in the [Security Design Principles](/azure/architecture/framework/security/security-principles) section in more detail.

Cloud architectures can help simplify the complex task of securing an enterprise estate through specialization and shared responsibilities:

**Specialization:**  Specialist teams at cloud providers can develop advanced capabilities to operate and secure systems for organizations. This approach is preferable to numerous organizations individually developing deep expertise in managing and securing common elements, such as:

- Datacenter physical security
- Firmware patching
- Hypervisor configuration

The economies of scale allow cloud provider specialist teams to invest in optimising management and security that far exceeds the ability of most organizations.

Cloud providers must comply with the same IT regulatory requirements as the aggregate of all their customers. Providers must develop expertise to defend against the aggregate set of adversaries who attack their customers. Consequently, the default security posture of applications deployed to the cloud is frequently much better than those hosted on-premises.

**Shared Responsibility Model:** As computing environments move from customer-controlled datacenters to the cloud, the security responsibility also shifts. Security of the operational environment is now a concern shared by cloud providers and customers. Organizations can reduce focus on activities outside core business competencies by shifting these responsibilities to a cloud service like Azure. Depending on the specific technology choices, some security protections will be built into the particular service, while addressing others will remain the customer's responsibility. To ensure that proper security controls are provided, organizations must carefully evaluate the services and technology choices.

:::image type="content" source="./images/shared-resp-color.png" alt-text="A diagram of shared responsibility and key strategies.":::

**Shared Responsibility and Key Strategies:**

After reading this document, you'll be equipped with key insights about improving your architecture's security posture.

As part of your architecture design, you should consider all relevant areas that affect the success of your application. While this article is concerned primarily with security principles, you should also prioritize other requirements of a well-designed system, such as:

- Availability
- Scalability
- Costs
- Operational characteristics (trading off one over the other as necessary)

Consistently sacrificing security for gains in other areas isn't advisable because security risks increase dynamically over time.

Increasing security risks result in three key strategies:

- **Establish a modern perimeter:** For the elements your organization controls, ensure you have a consistent set of controls (a perimeter) between those assets and their threats. Perimeters should be designed based on intercepting authentication requests for the resources (identity controls) versus intercepting network traffic on enterprise networks. This traditional approach isn't feasible for enterprise assets outside the network.

More on perimeters and how they relate to Zero Trust and Enterprise Segmentation are in the [Governance, Risk, and Compliance](/azure/architecture/framework/security/design-segmentation) and [Network Security & Containment](/azure/architecture/framework/security/design-network-segmentation) sections.

- **Modernize infrastructure security:** For operating systems and middleware elements that legacy applications require, take advantage of cloud technology to reduce the security risk to the organization. For example, knowing whether all servers in a physical datacenter are updated with security patches has always been challenging because of discoverability. Software-defined datacenters allow easy and rapid discovery of all resources. This rapid discovery enables technology like Microsoft Defender for Cloud to measure quickly and accurately the patch state of all servers and remediate them.

- **"Trust but verify" each cloud provider:** For the elements under the cloud provider's control. You should ensure that each cloud provider's security practices and regulatory compliance (large and small) meet your requirements.

To assess your workload using the tenets found in the Microsoft Azure Well-Architected Framework, see the [Microsoft Azure Well-Architected Review](/assessments/?id=azure-architecture-review&mode=pre-assessment).

:::image type="content" source="./images/devops-security.png" alt-text="A diagram of the security cycle.":::

We cover the following areas in the security pillar of the Microsoft Azure Well-Architected Framework:

| Security Topic | Description |
|-------------------|-------------|
| [Security design principles][design] | These principles describe a securely architected system hosted on cloud or on-premises datacenters, or a combination of both. |
| [Governance, risk, and compliance][compliance] | How will the organization's security be monitored, audited, and reported? What risks does the organization face while protecting identifiable information, Intellectual Property (IP), and financial information? Is there specific industry, government, or regulatory requirements that dictate or provide recommendations on criteria that your organization's security controls must meet? |
| [Regulatory compliance][regulatory] | Governments and other organizations frequently publish standards to help define good security practices (due diligence) so that organizations can avoid being negligent in security. |
| [Administration][admin] | Administration is the practice of monitoring, maintaining, and operating Information Technology (IT) systems to meet service levels that the business requires. Administration introduces some of the highest impact security risks because performing these tasks requires privileged access to a broad set of these systems and applications. |
| [Applications and services][app] | Applications and the data associated with them ultimately act as the primary store of business value on a cloud platform. |
| [Identity and access management][identity] | Identity provides the basis of a large percentage of security assurances. |
| [Information protection and storage][info] | Protecting data at rest is required to maintain confidentiality, integrity, and availability assurances across all workloads. |
| [Network security and containment][network] | Network security has been the traditional linchpin of enterprise security efforts. However, cloud computing has increased the requirement for network perimeters to be more porous. Many attackers have mastered the art of attacks on identity system elements (which nearly always bypass network controls). |
| [Security Operations][sec-ops] | Security operations maintain and restore the security assurances of the system as live adversaries attack it. The security operations tasks are described well by the NIST Cybersecurity Framework functions of Detect, Respond, and Recover. |

## Identity management

Consider using Azure Active Directory (Azure AD) to authenticate and authorize users. Azure AD is a fully managed identity and access management service. You can use it to create domains that exist purely on Azure or integrate with your on-premises Active Directory identities.

Azure AD is also used by:

- Microsoft 365
- Dynamics 365
- Many third-party applications

For consumer-facing applications, Azure Active Directory B2C lets users authenticate with their existing social accounts, such as:

- Facebook
- Google
- LinkedIn

Users can also create a new user account managed by Azure AD.

If you want to integrate an on-premises Active Directory environment with an Azure network, several approaches are possible, depending on your requirements. For more information, reference [Identity Management][identity-ref-arch] reference architectures.

## Protect your infrastructure

Control access to the Azure resources that you deploy. Every Azure subscription has a [trust relationship][ad-subscriptions] with an Azure AD tenant.

Use [Azure role-based access control (Azure RBAC role)][rbac] to grant users within your organization the correct permissions to Azure resources. Grant access by assigning Azure roles to users or groups at a certain scope. The scope can be a:

- Subscription
- Resource group
- Single resource

[Audit][resource-manager-auditing] all changes to infrastructure.

## Application security

Generally, the security best practices for application development still apply in the cloud. Best practices include:

- Encrypt data in transit with the latest supported `TLS` versions
- Protect against `CSRF` and `XSS` attacks
- Prevent SQL injection attacks

Cloud applications often use managed services that have access keys. Never check these keys into source control. Consider storing application secrets in [Azure Key Vault](/azure/key-vault/general/overview).

## Data sovereignty and encryption

Make sure that your data remains in the correct geopolitical zone when using Azure data services. Azure's geo-replicated storage uses the concept of a [paired region][paired-region] in the same geopolitical region.

Use Key Vault to safeguard cryptographic keys and secrets. By using Key Vault, you can encrypt keys and secrets using keys protected by hardware security modules (HSMs). Many Azure storage and DB services support data encryption at rest, including:

- [Azure Storage][storage-encryption]
- [Azure SQL Database][sql-db-encryption]
- [Azure Synapse Analytics][data-warehouse-encryption]
- [Azure Cosmos DB][cosmos-db-encryption]

## Security resources

- [Microsoft Defender for Cloud][security-center] provides integrated security monitoring and policy management for your workload.
- [Azure Security Documentation][security-documentation]
- [Microsoft Trust Center][trust-center]

The security pillar is part of a comprehensive set of security guidance that also includes:

- [Security in the Microsoft Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/secure): A high-level overview of a cloud security end state.
- [Security architecture design](/azure/architecture/guide/security/security-start-here): Implementation-level journey of our security architectures.
  - [Browse our security architectures](/azure/architecture/browse/?azure_categories=security)
- [Azure security benchmarks](/security/benchmark/azure): Prescriptive best practices and controls for Azure security.
- [End-to-end security in Azure](/azure/security/fundamentals/end-to-end): Documentation that introduces you to the security services in Azure.
- [Top 10 security best practices for Azure](/azure/cloud-adoption-framework/secure/security-top-10): Top Azure security best practices that Microsoft recommends based on lessons learned across customers and our own environments.
- [Microsoft Cybersecurity Architectures](/security/cybersecurity-reference-architecture/mcra): The diagrams describe how Microsoft security capabilities integrate with Microsoft platforms and 3rd-party platforms.

### Next step

> [!div class="nextstepaction"]
> [Principles](./security-principles.md)

<!-- security links -->

[app]: ./design-apps-services.md
[compliance]: ./design-governance.md
[identity]: ./design-identity.md
[network]: ./design-network.md
[design]: ./security-principles.md
[regulatory]: ./design-regulatory-compliance.md
[org-risk]: ./resilience.md
[admin]: /azure/architecture/framework/security/design-admins
[info]: ./storage-data-encryption.md
[sec-ops]: ./security-operations.md

<!-- links -->

[identity-ref-arch]: /azure/architecture/reference-architectures/identity/
[resiliency]: ../../framework/resiliency/principles.md
[ad-subscriptions]: /azure/active-directory/active-directory-how-subscriptions-associated-directory
[data-warehouse-encryption]: /azure/data-lake-store/data-lake-store-security-overview#data-protection
[cosmos-db-encryption]: /azure/cosmos-db/database-security
[rbac]: /azure/role-based-access-control/overview
[paired-region]: /azure/best-practices-availability-paired-regions
[resource-manager-auditing]: /azure/azure-resource-manager/resource-group-audit
[security-center]: https://azure.microsoft.com/services/security-center
[security-documentation]: /azure/security
[sql-db-encryption]: /azure/sql-database/sql-database-always-encrypted-azure-key-vault
[storage-encryption]: /azure/storage/storage-service-encryption
[trust-center]: https://azure.microsoft.com/support/trust-center
[admin]: ./design-admins.md
[info]: ./design-storage.md
[sec-ops]: ./monitor-security-operations.md

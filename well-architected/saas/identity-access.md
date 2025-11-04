---
title: Identity and Access Management for SaaS Workloads on Azure
description: Learn about the design considerations for identity and access management in your SaaS workload environment.
author: landonpierce
ms.author: prwilk
ms.date: 11/04/2025
ms.topic: concept-article
ms.collection: learn-startups
---

# Identity and access management for SaaS workloads on Azure

Application identity is a critical area for SaaS workloads because it serves as the first line of defense for protecting data. It's often overlooked until late in a project, but many decisions about other elements of the application depend on a solid identity strategy. Don't underestimate the importance of identity in helping to protect your customers' data.

In the context of SaaS workloads, there are two distinct types of identity.

- **Application identity**, also known as *customer identity and access management (CIAM)*, enables end users to authenticate and use your SaaS application. There are two main methods for signing users in to an application identity provider:
  
    - **Federated identities.**  Users sign in with existing credentials that are maintained by another identity provider. That provider could be a social identity provider such as Google, Facebook, or LinkedIn, or an enterprise identity provider that your customers use, such as Microsoft Entra or Okta. Maintenance of the user's account is the responsibility of the federated identity provider.

    - **Local identities.** Users create an account just for your application. The account is secured by username and password, passkey, or other authentication methods. Maintenance of the user's account is your responsibility.

- **Enterprise identity** is the identity solution that's used to authenticate internal users and workloads to business productivity tools, internal tools or services, and Azure services. You use an enterprise identity solution for your internal users and workloads to authenticate them to business productivity tools, internal tools or services, and Azure services. 

    > Refer to [SE:05 Identity and access management](/azure/well-architected/security/identity-access).

A SaaS solution commonly uses both identity types together:

:::image type="content" source="./images/identity-types.png" alt-text="Diagram that shows the relationship between application identity and enterprise identity." border="false":::

Application and enterprise identities serve different purposes and might use different identity providers. This article focuses on design considerations for application identity, though both types are likely to be present in your SaaS workload environment. 

Identity management involves two related concerns: authentication (verifying a user's identity) and authorization (granting permissions based on identity). The first three sections of this article focus on authentication for SaaS. The final section addresses authorization considerations for SaaS providers.

## Identity in a multitenant application

Keeping tenant (customer) data separate in a multitenant application is critical. That segmentation is driven by your choice of effective user authentication and authorization. Also, the choice of tenancy model significantly influences your decisions about the identity provider. Prioritize identity as your primary perimeter. 

> Refer to [SE:04 Recommendations for segmentation](/azure/well-architected/security/segmentation#establish-identity-as-the-primary-security-perimeter).

### Design considerations

**Understand the tenancy and deployment models for your application.** There might be nuances that influence your identity strategy. For example, it's a misconception that the Deployment Stamps pattern requires an identity provider in each stamp. For most identity providers, you can often use an alternative isolation model, like sharing an identity provider across all your stamps.
  
When you choose your identity provider for multitenancy, evaluate the impact of failures. Misconfigurations can potentially bring down your entire application for all tenants. Weigh the overhead costs against the risk of the potential radius of impact. 

If you deploy your solution into a customer's Azure environment and manage it on their behalf, you might need to integrate with their enterprise identity provider. Have a clear understanding of these aspects:

  - The types of users and their access needs when they interact with your application tenants. For example, User A might only need access to sign in to tenant 1, but user B might need access to sign in to both tenant 1 and tenant 2.
  - Compliance with data residency regulations, if they're applicable to your identity provider. In some cases, data that's stored by an identity provider might be subject to regulations. Many identity providers provide specific guidance and capabilities for this scenario. Assess whether this scenario is relevant to you and take necessary steps to ensure compliance.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Follow your identity provider's best practices and guidelines for partitioning the solution for multiple tenants. | Tenant isolation helps you to achieve your security and compliance goals. |
| Avoid having multiple accounts for the same user. A user should have a single account with one set of credentials, even if they need access to multiple tenants. Grant access to each tenant as needed rather than creating multiple accounts for the same user. | Creating multiple accounts for the same user increases security risks and can confuse users who need to remember multiple usernames and passwords for the same software.|
| When you consider data residency, plan how to store user data in separate locations. If you deploy a separate deployment stamp for your users in other geographies, you might also need separate identity providers. <br><br>Make sure you have a way to identify where users' data is stored so you can direct them to the correct region for sign-in, if you need to. | You'll be able to support your compliance requirements and enhance the user experience by routing users to the sign-in experience that's appropriate for their location.|

## Identity provider selection

Each identity provider offers unique features, limitations, pricing models, and implementation patterns. Microsoft Entra and Okta are popular identity as a service (IDaaS) options. There are also other open source providers, such as Keycloak and Authentik.

### Design considerations

- **Document your identity requirements.** Start by listing the features that your application needs now and will need in the future. Typical features to consider include:

    - Federated identity provider support to integrate with customers' identity solutions. This feature enables you to avoid creating new identities.
    - Customizable sign-in/sign-up flow to modify the look and feel to maintain your branding. This feature also provides the ability to inject custom business logic into the sign-in/sign-up process.
    - Separation of tenant data into distinct silos to maintain tenant isolation.
    - Audit support to retain or export sign-in logs for security management.

    > [!IMPORTANT]
    > Consider your planned user growth when you evaluate the cost of an identity solution. A solution might not remain cost effective or scalable in the long term, but it could be useful for now. Have a migration plan that you can use if the need arises.
    >
    > For example, a solution might be affordable for 500 users but unsustainable for 5 million. If it requires minimal setup and is user-friendly and easy to migrate from, it could still be the right choice until scaling costs justify switching to a different solution.

- **Research the identity provider capabilities thoroughly.** Make sure the identity solution matches your list of required features. Even if you don't currently need complex scenarios like federated identity, consider future needs. For business-to-business (B2B) SaaS solutions, federated identity will probably be necessary eventually.

- **Factor in management overhead.** Different identity providers require varying levels of management overhead. Well-known IDaaS solutions usually have less overhead because they handle hosting, maintenance, and security. However, the additional overhead of an open source solution might be worthwhile if the solution is a better fit for your specialized needs.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Don't create your own identity solution. Identity is a highly specialized area, and creating an identity solution is complex and expensive. It's difficult to create an identity solution that's secure and reliable. |You'll avoid the antipattern of creating your own provider and enhance the security, reliability, and operational efficiency of your solution.|
| Create a capability matrix of the features offered by identity providers and map it against your identity requirements.|You'll ensure your ability to evolve without being constrained by a limited set of identity features.|
| Prefer IDaaS options over open source solutions. <br> <br> Hosting an open source solution yourself incurs significant operational overhead and security risks. However, you might choose that option to meet specific requirements for compliance, data residency, or reliability that a provider can't fulfill. For more information, see [IDaaS identity providers](/azure/architecture/guide/design-principles/identity).| By using an IDaaS identity provider, you'll avoid unnecessary complexity and can focus your efforts on your core business.|

## Federated identity

Federated identity, also known as *single sign-on (SSO)*, allows users to sign in with credentials they already use elsewhere. You enable federated identity by establishing a trust relationship between your application identity provider and the customer's existing identity provider. Federated identity is a common requirement for SaaS solutions, especially in B2B, because customers prefer their employees to use corporate credentials. It offers several benefits for B2B solutions, such as centralized identity management and automatic lifecycle management. In B2C SaaS products, integrating with social identity providers is common to allow users to sign in with existing credentials.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complexity and operational efficiency.** By working with federated identity providers, you offload the complexity of managing your users' identities. However, you take on the cost of integrating with another identity provider. Decide where you want to focus your operational efforts.

Although implementing federated identity is initially simple, it becomes more complex as the number of supported identity providers increases. Careful planning is essential, especially if each customer uses a unique identity provider. Even if they use the same identity provider, unique trust relationships are often required for each customer because of specific configuration details.

This image shows the relationship between your application, your application identity provider, and the downstream identity providers that you might choose to implement by using identity federation.  

:::image type="content" source="./images/federated-identity.png" alt-text="Diagram that shows an application trusting a single identity provider, which in turn federates with multiple customer identity providers." border="false":::

### Design considerations

- **Estimate the types and number of identity providers you need to support.** You might need a static number of social identity providers, or you might need unique federated identity providers for each customer. You should know whether your customers will use OpenID Connect (OIDC), Security Assertion Markup Language (SAML), or both for integration.

- **Map out the sign-in experience.** Visualize the user flow of the sign-up and sign-in process. Note any special requirements that might alter your user flow design. For example:

    - **Custom branding.** White labeling or custom sign-in domains per customer.

    - **Custom information.** Collecting additional user information during sign-up or sign-in, such as tenant selection for users with access to multiple tenants.

    - **Identity provider selection.** If you use a single application identity provider that has many federated identity providers trusting it, decide how to select a provider. This selection might be done manually via a button or automatically based on known user information. As the number of providers increases, automatic selection becomes more practical. This capability is known as *Home Realm Discovery*.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Choose an identity provider that can scale to accommodate the number of federated identity providers you need. <br><br> Be aware of the hard limits of the provider, which can't be exceeded.|You'll ensure that your identity solution can scale as you grow.|
| Plan the onboarding of each federated identity provider and automate the process as much as possible. <br><br>This collaborative effort between your organization and your customers involves exchanging information to establish a trust relationship, typically via OIDC or SAML protocols.| Identity integration can take time and effort for both you and your customers. By planning the process, you'll improve your operational efficiency. |
| Reflect the complexity and cost of federated identity in your pricing and business model.<br><br>Allowing customers to use their own identity provider increases operational complexity and costs because of the overhead of maintaining multiple federated identity trust relationships. It's common in SaaS solutions for enterprises to pay for a higher tier that enables federated sign-in.|Federating with a customer's identity provider can be a hidden cost in SaaS solutions. By planning for it, you'll avoid unexpected costs during implementation.|
| Plan for how a user's identity provider will be selected during the sign-in flow. Consider using Home Realm Discovery. <br><br>Microsoft Entra ID provides built-in [Home Realm Discovery](/entra/identity/enterprise-apps/home-realm-discovery-policy).| You'll streamline your customer experience and ensure that users are directed to the right sign-in process. |

## Authorization

User authorization is crucial for SaaS applications, which often store data for multiple tenants. Clearly define how users will be authorized to access only their data without inadvertently accessing other tenants' data. Additionally, provide granular authorization within a tenant, allowing users to read or access certain information while restricting updates or access to other data.

### Design considerations

- **Choose the right authorization model for the use case.** There are two main types:
  
    - **Role-based authorization.** Users are assigned roles or groups, and specific features are restricted to certain roles. For example, administrators can perform any action, but users in other roles have limited permissions.  
    - **Resource-based authorization.**  Each resource has its own set of permissions. A user might be an administrator for one resource but have no access to another.

- **Decide where to store authorization data.** Authorization data for your application can be stored in:
  
    - **Your identity provider.** Take advantage of the built-in groups or roles, surfacing permissions as claims in the token issued to your application. Your application can then enforce authorization rules by using these token claims.
    - **Your application.**  Develop your own authorization logic and store user permissions in a database or similar system, allowing for fine-grained role-based or resource-level authorization controls.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff: Complexity, flexibility, and security.** Storing authorization data in an identity provider and surfacing through token claims is usually simpler than managing your own authorization system. However, claim-based authorization limits your flexibility, and you need to accept that claims are only refreshed when a token is reissued, which can cause a delay in applying changed permissions.

- **Assess the impact of delegated management.** In most SaaS applications, especially in B2B applications, role and permission management is delegated to customers. Without this functionality, you might increase your management overhead if customers frequently change their users' permissions.
  
- **Evaluate multitenant access.** In some systems, a single user might need to access data from multiple tenants. For example, consultants might need to access data from multiple tenants. Plan how customers will grant access to these users and how your sign-in flow will support selecting and switching among tenants.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Prevent users from accessing data across tenant boundaries unless that access is explicitly permitted. | Unauthorized access to another tenant's data, even accidental access, can be seen as a major security incident and erode customer trust in your platform. Blocking unnecessary access will help you avoid these situations. |
| If the data is static and changes infrequently, store it in the identity provider. If frequent changes are needed while the user is using the software, store the authorization data in your application. |Selecting the best data store for your authorization data will enhance your operational efficiency and help you meet your scalability needs. |
| If you delegate permission management to customers, provide a clear method for them to manage permissions. For instance, create a web portal that's accessible only to tenant administrators for changing user permissions. | You'll provide more control to your customers and avoid unnecessary operational burden on your support team. |

## Additional resources

Multitenancy is a core business methodology for designing SaaS workloads. These articles provide more information about identity and access management:

- [Architectural considerations for identity in multitenant solutions](/azure/architecture/guide/multitenant/considerations/identity)
- [Architectural approaches for identity in multitenant solutions](/azure/architecture/guide/multitenant/approaches/identity)

## Next step

Learn about choosing your compute hosting model, the operational aspects, and how to optimize the technology options to help you meet your service level agreements and objectives.

> [!div class="nextstepaction"]
> [Design area: Compute for SaaS workloads on Azure](./compute.md)

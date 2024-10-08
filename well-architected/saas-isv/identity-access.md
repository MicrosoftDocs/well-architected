---
title: Identity and access management for SaaS workloads on Azure
description: Learn about the identity platform considerations for SaaS workloads.
author: landonpierce
ms.author: landonpierce
ms.date: 9/13/2024
ms.topic: conceptual
---

# Identity and access management for SaaS workloads on Azure

[!INCLUDE [header_file](includes/temporary-warning.md)]

Application identity is a critical area for SaaS workloads because it serves as the first line of defense in protecting data. It's often overlooked until late in a project, but many decisions in other areas of the application depend on a solid identity strategy. Don't underestimate the importance of identity in protecting your customers' data.

In the context of SaaS workloads, there are two disctinct types of identity.

- **Application identity**, also known as Customer Identity and Access Management (CIAM), enables end users to authenticate and use your SaaS application. There are two main methods for signing users into an application identity provider:
  
    - **Federated identities**:  Users sign in with their existing credentials maintained by another identity provider. That provider could be a social identity provider such as Google, Facebook, or LinkedIn, or an enterprise identity provider used by your customers, such as Microsoft Entra or Okta.
      
    - **Local identities**: Users create an account just for your application, secured by methods like username and password, passkey, or other authentication methods. //TODO @landon who's responsible for managing this identity//

- **Enterprise identity**, the identity solution used to authenticate internal users and workloads to business productivity tools, internal tools or services, and Azure services. You use an enterprise identity solution for your internal users and workloads to authenticate them to business productivity tools, internal tools or services, and Azure services. For a holistic view of *enterprise* identity, see [SE:05 Identity and access management](/azure/well-architected/security/identity-access).

Application and enterprise identities serve different purposes and may use different identity providers. This article focuses on design considerations for application identity, though both types are likely to be present in your SaaS workload environment. 

Identity management involves two related concerns: authentication (verifying a user's identity) and authorization (granting permissions based on identity). The first three sections of this article focus on authentication for SaaS, while the final section addresses authorization considerations for SaaS providers.

## Identity in a multitenant application

Keeping tenant data separate in a multitenant application is critical. That segmentation is driven by your choice in effective user authentication and authorization. Also, the choice of tenancy model significantly impacts your decisions about the identity provider.

Prioritize identity as your primary perimeter. For more information, see [SE:04 Recommendations for segmentation](/azure/well-architected/security/segmentation#establish-identity-as-the-primary-security-perimeter).

For more information on identity in multitenant solutions, see these articles:

- [Architectural considerations for identity in a multitenant solution](/azure/architecture/guide/multitenant/considerations/identity)
- [Architectural approaches for identity in multitenant soutions](/azure/architecture/guide/multitenant/approaches/identity)

### Design considerations

**Understand the tenancy and deployment models for your application**. There might be nuances that impact your identity strategy. For example, it's a misconception that the Deployment Stamps pattern requires an identity provider in each stamp. For most identity providers, you can often use an alternative isolation model.
  
When choosing your identity provider for multitenancy, evaluate the impact of failures. Misconfigurations can potentially bring down your entire application for all tenants. Weigh the overhead costs against the risk of the potential radius of impact. 

If you deploy your solution into a customer's Azure environment and manage it on their behalf, you might need to integrate with their enterprise identity provider. Have a clear understanding of these aspects:
  - Understand the types of users and their access needs when they interact with your application tenants. For example, User A might only need access to sign into tenant 1, but user B might need access to sign into both tenant 1 and tenant 2.
  - Compliance with data residency regulations if applicable to your identity provider. In some cases, data stored by an identity provider may be subject to regulations. Many identity providers provide specific guidance and capabilities for this scenario. Assess whether this scenario is relevant to you and take necessary steps to ensure compliance.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| Follow your identity provider's best practices and guidelines for partitioning the solution for multiple tenants. | Tenant isolation helps you to achieve your security and compliance goals. |
| Avoid having multiple accounts for the same user. A user should have a single account with one set of credentials, even if they need access to multiple tenants. Grant access to each tenant as needed, rather than creating multiple accounts for the same user. | Creating multiple accounts for the same user increases security risks and can confuse users who have to remember multiple usernames and passwords for the same software.|
| When considering data residency, plan how to store user data in separate locations. If you use separate deployment stamps per region. you might also need separate identity providers for each region. //TODO @landon, this is confusing. If the deployment is different in each region, how is it a stamp? or is this simply that each stamp might need a separate identity provider? this is also a pain so maybe add a tradeoff on opex?//<br><br>Make sure you have a way to  identify where users' data is stored to direct them to the correct region for sign-in, if needed. | You'll be able to support your compliance requirements and enhance the user experience by routing users to the sign-in experience that's appropriate for their location.|

## Identity provider selection

Each identity provider offers unique features, limitations, pricing models, and implementation patterns. Popular Identity-as-a-Service (IDaaS) options include Microsoft Entra, Okta. There are also other open source providers such as Keycloak, Authentik.

### Design considerations

- **Document your identity requirements**. Start by listing the features your application needs now and in the future. Common features include:
    - Federated identity provider support to integrate with customers' identity solutions, avoiding creation of new identities.
    - Customizable sign-in/sign-up flow to modify the look and feel to maintain your branding. Also, the ability to inject custom business logic in the sign-in/sign-up process.
    - Separate tenant data into distinct silos to maintains tenant isolation.
    - Audit support to retain or export sign-in logs for security management.
 
 > [!IMPORTANT]
 > Consider your planned user growth when evaluating the cost of an identity solution. Even if a solution may not remain cost-effective or scalable long-term, have a migration plan in place if the need arises.
 >
 > For example, a solution might be affordable for 500 users but unsustainable for 5 million. If it requires minimal setup, is user-friendly, and easy to migrate from, it could still be the right choice until scaling costs justify switching to a different solution.
 
- **Research the identity provider capabilities thoroughly**. Make sure the identity solution matches your list of required features. Even if complex scenarios like federated identity aren't needed now, consider future needs. For B2B SaaS solutions, federated identity will likely be necessary eventually.

- **Factor in management overhead**: Different identity providers require varying levels of management overhead. Well-known IDaaS solutions usually have less overhead because they handle hosting, maintenance, and security. However, the additional overhead of an open-source solution might be worthwhile if it better fits your specialized needs.

### Design recommendations

| Recommendation | Benefit |
|---|---|
| **Do not** build your own identity solution. Identity is a highly specialized area, and building an identity solution is complex, expensive, and difficult to build securely and yet be extremely reliable. |You'll avoid the antipattern of building your own provider and enhance the security, reliability, and operational efficiency of your solution.|
| Create a capability matrix of the features offered by identity providers against the your identity requirements.|You'll ensure your ability to evolve without being constrained by a limited set of features from your identity provider.|
| Prefer an IDaaS options over open source solutions.  <br> Hosting an open-source solution yourself involves significant operational overhead and security risks. However, you might choose this option to meet specific requirements for compliance, data residency, or reliability that an provider cannot fulfill. For more information, see [IDaaS identity providers](/azure/architecture/guide/design-principles/identity).| By using an IDaaS identity provider, you'll avoid unnecessary complexity and can focus your efforts on your core business.|

## Federated identity

Federated identity, also known as single sign-on (SSO), allows users to sign in with credentials they already use elsewhere. This is done by establishing a trust relationship between your application identity provider and the customer's existing identity provider. It's a common requirement for SaaS solutions, especially in B2B, as customers prefer their employees to use corporate credentials. Federated identity offers several benefits for B2B solutions, such as centralized identity management and automatic lifecycle management. In B2C SaaS products, integrating with social identity providers is common to allow users to sign in with existing credentials.

While initially simple, implementing federated identity becomes more complex as the number of supported identity providers increases. Careful planning is essential, especially if each customer uses a unique identity provider. Even with the same identity provider, unique trust relationships are often required for each customer due to specific configuration details.

This image shows the relationship between your application, your application identity provider, and the downstream identity providers you may choose to implement with identity federation.  

:::image type="content" source="./images/federated-identity.drawio.png" alt-text="Diagram that shows an application trusting a single identity provider, which in turn federates with multiple customer identity providers.":::

### Design considerations

- **Estimate the types and number of identity providers you'll need to support**. This could range from a static number of social identity providers to unique federated identity providers for each customer. Technically, understand if your customers will use Open ID Connect (OIDC), Security Assertion Markup Language (SAML), or both for integration.

- **Map out the sign-in experience**. Visualize the user flow of the sign-up and sign-in process. Note any special requirements that might alter your user flow design, for example:

    - Custom branding: Whitelabeling or custom sign-in domains per customer.
      
    - Custom information: Collecting additional user information during sign-up or sign-in, such as, tenant selection for users with access to multiple tenants.
      
    - Identity provider selection:  If using a single application identity provider with many federated identity providers trusting it, decide how to select a provider, which might be manually via a button or automatically based on known user information. As the number of providers increases, automatic selection becomes more practical. This scenario is known as home realm discovery. Microsoft Entra ID provides built-in capabilities. For more information, see [Home realm discovery](/entra/identity/enterprise-apps/home-realm-discovery-policy).


### Design recommendations

| Recommendation | Benefit |
|---|---|
| Choose an identity provider that can scale to accommodate the number of federated identity providers you need. <br><br> Be aware of the hard limits on the provider, which can't be exceeded.|You'll ensure your identity solution can scale as you grow.|
| Plan the onboarding of each federated identity provider and automate the process as much as possible. <br><br>This collaborative effort between you organization and your customers involves exchanging information to establish a trust relationship, typically using OpenID Connect or SAML protocols.| Identity integration can take time and effort for both you and your customers. By planning the process, you'll improve your operational efficiency. |
| Reflect the complexity and cost of federated identity in your pricing and business model.<br><br>Allowing customers to use their own identity provider increases operational complexity and costs due to the overhead of maintaining multiple federated identity trust relationships. It's common in SaaS solutions for enterprises to pay for a higher tier that enables federated sign-in.|Federating with a customer's identity provider can be a hidden cost in SaaS solutions. By planning for it, you'll avoid unexpected costs during implementation.|
| Plan for how a user's identity provider will be selected during the sign-in flow, potentially with home realm discovery. | You'll streamline your customer experience and ensure users are directed to the right sign-in process for them. |

## Authorization

User authorization is crucial for SaaS applications, which often house data for multiple tenants. Clearly define how users will be authorized to access only their data without inadvertently accessing other tenants' data. Additionally, provide granular authorization within a tenant, allowing users to read or access certain information while restricting updates or access to other data.

### Design Considerations

- **Choose the right authorization models for the use case**. There are two main types.
  
    - **Role-based authorization**. Users are assigned roles or groups, with specific features restricted to certain roles. For example, administrators can perform any action, while users in lower roles have limited permissions.  
    - **Resource-based authorization**:  Each resource has its own set of permissions. A user might be an administrator for one resource but have no access to another.
      
- **Decide where to store authorization data**. Authorization data for your application can be stored in:
  
    - **Your identity provider**: Take advantage of the built-in groups or roles, surfacing permissions as claims in the token issued to your application. Your application can then enforce authorization rules using these token claims.
    - **Your application**:  Develop your own authorization logic and store user permissions in a database or similar system, allowing for fine-grained role-based or resource-level authorization controls.
    
- **Assess the impact of delegated management**: In most SaaS applications, especially B2B, roles and permissions management is typically delegated to customers. Without this functionality, you may increase your management overhead if customers frequently change their users' permissions.
  
- **Evaluate multi-tenant access**: In some systems, a single user might need to access data from multiple tenants. For example, consultants may need to access data from multiple tenants. Plan how customers will grant access to these users and how your sign-in flow will support selecting and switching between tenants.

### Design Recommendations

| Recommendation | Benefit |
|---|---|
| Prevent users from accessing data across tenant boundaries unless explicitly permitted. | Unauthorized access to another tenant's data, even accidentally, can be seen as a major security incident and erode customer trust in your platform. Blocking unncessary access will avoid such situations. |
| If the data is static and changes infrequently, store it in the identity provider. If frequent changes are needed while the user is using the software, store the authorization data in your application. | By selecting the best data store for your authorization data, you'll enhance your operational efficiency and can meet your scalability needs. |
| If delegating permission management to customers, provide a clear method for them to manage permissions. For instance, create a web portal accessible only to tenant administrators for changing user permissions. | You'll give more control to your customers, and avoid unnecessary operational burden on your support team. |

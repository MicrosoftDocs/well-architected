---
title: Recommendations for identity and access management 
description: Learn about recommendations for authenticating and authorizing identities that are attempting to access workload resources.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for identity and access management 

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:05](checklist.md)|Implement strict, conditional, and auditable identity and access management (IAM) across all workload users, team members, and system components. Limit access exclusively to *as necessary*. Use modern industry standards for all authentication and authorization implementations. Restrict and rigorously audit access that's not based on identity.|
|---|---|

This guide describes the recommendations for authenticating and authorizing identities that are attempting to access your workload resources.

From a technical control perspective, **identity is always the primary perimeter**. This scope doesn't just include the edges of your workload. It also includes individual components that are inside your workload. Typical identities include:

-   **Humans**: Application users, admins, operators, auditors, and bad actors.

-   **Systems**: Workload identities, managed identities, API keys, service principals, and Azure resources.

-   **Anonymous**. Entities who haven't provided any evidence about who they are.



**Definitions** 


|Terms   |Definition   |
|---------|---------|
|Authentication (AuthN)     | A process that verifies that an identity is who or what it says it is.       |
|Authorization (AuthZ)     | A process that verifies whether an identity has permission to perform the requested action.       |
|Conditional access|A set of rules that allows actions based on specified criteria. |
|IdP     |  An identity provider, like Microsoft Entra ID.       |
|Persona     |   A job function or a title that has a set of responsibilities and actions.      |
|Preshared keys     |  A type of secret that's shared between a provider and consumer and used through a secure and agreed upon mechanism.       |
|Resource identity     |   An identity defined for cloud resources that's managed by the platform.      |
|Role     |  A set of permissions that define what a user or group can do.      |
|Scope     | Different levels of organizational hierarchy where a role is permitted to operate. Also a group of features in a system.        |
|Security principal     | An identity that provides permissions. It can be a user, a group, or a service principal. Any group members get the same level of access.   |
|User identity      |  An identity for a person, like an employee or an external user.     |
|Workload identity       |   A system identity for an application, service, script, container, or other component of a workload that's used to authenticate itself to other services and resources.    |

> [!Note] 
> An identity can be grouped with other, similar identities under a parent called a *security principal*. A security group is an example of a security principal. This hierarchical relationship simplifies maintenance and improves consistency. Because identity attributes aren't handled at the individual level, chances of errors are also reduced. In this article, the term *identity* is inclusive of security principals.

### The role of an identity provider

An identity provider (IdP) is a cloud-hosted service that stores and manages users as digital identities.

**Take advantage of the capabilities provided by a trusted IdP** for your identity and access management. Don't implement custom systems to replace an IdP. IdP systems are improved frequently based on the latest attack vectors by capturing billions of signals across multiple tenants each day. Microsoft Entra ID is the IdP for Azure cloud platform.

#### Authentication

Authentication is a process that verifies identities. The requesting identity is required to provide some form of verifiable identification. For example:

-   A user name and password.

-   A preshared secret, like an API key that grants access.

-   A shared access signature (SAS) token.

-   A certificate that's used in TLS mutual authentication.

As much as possible, the verification process should be handled by the IdP.

#### Authorization

Authorization is a process that allows or denies actions that are requested by the verified identity. The action might be operational or related to resource management.

Authorization requires that you assign permissions to the identities, which you need to do by using the functionality provided by your IdP.

## Key design strategies

To get a holistic view of the identity needs for a workload, you need to catalog the flows, workload assets, and personas, and the actions the assets and personas will perform. Your strategy must cover all use cases that handle **the flows that reach the workload or its components (outside-in access) and flows that reach out from the workload to other sources (inside-out access)**.

Each use case probably have its own set of controls that you need to design with an assume-breach mindset. Based on the identity requirements of the use case or the personas, identify the conditional choices. Avoid using one solution for all use cases. Conversely, the controls shouldn't be so granular that you introduce unnecessary management overhead.

You need to log the identity access trail. Doing so helps validate the controls, and you can use the logs for compliance audits.

### Determine all identities for authentication

-   **Outside-in access**. Your identity design must authenticate all users that access the workload for various purposes. For example, an end user who accesses the application by calling APIs.

    At a granular level, components of the workload might also need access from outside. For example, an operator who needs access through the portal or access to the compute to run commands.

    Both are examples of **user identities** that have different personas.

-   **Inside-out access**. Your application will need to access other resources. For example, reading from or writing to the data platform, retrieving secrets from the secret store, and logging telemetry to monitoring services. It might even need to access third-party services. These access needs require **workload identity**, which enables the application to authenticate itself against the other resources.

    The concept applies at the component level. In the following example, the container might need access to deployment pipelines to get its configuration. These access needs require **resource identity**.

All these identities should be authenticated by your IdP.

Here's an example of how identity can be implemented in an architecture:

:::image type="content" source="images/identity-access/architecture-identity.png" alt-text="Diagram that shows how identity can be implemented in an architecture." border="false" lightbox="images/identity-access/architecture-identity.png":::

### Determine actions for authorization

Next, you need to know what each authenticated identity is trying to do so that those actions can be authorized. The actions can be divided by the type of access that they require:

-   **Data plane access**. Actions that take place in the data plane cause data transfer for inside-out or outside-in access. For example, an application reading data from a database and writing data to a database, fetching secrets, or writing logs to a monitoring sink. At the component level, compute that's pulling or pushing images to or from a registry are considered data plane operations.

-   **Control plane access**. Actions that take place in the control plane cause an Azure resource to be created, modified, or deleted. For example, changes to resource properties.

Applications typically target data plane operations, while operations often access both control and data planes. To identify authorization needs, note the operational actions that can be performed on the resource. For information about the permitted actions for each resource, see [Azure resource provider operations](/azure/role-based-access-control/resource-provider-operations).

### Provide role-based authorization

Based on the responsibility of each identity, authorize actions that should be permitted. *An identity must not be allowed to do more than it needs to do*. Before you set authorization rules, you need a clear understanding of who or what is making requests, what that role is allowed to do, and to what extent it can do it. Those factors lead to choices that combine identity, role, and scope.

Consider a workload identity as an exmaple. The application must have data plane access to the database, so read and write actions to the data resource must be allowed. However, does the application need control plane access to the secret store? If the workload identity is compromised by a bad actor, what would be the impact to the system, in terms of confidentiality, integrity, and availability?

##### Role assignment

A role is a *set of permissions* that's assigned to an identity. Assign roles that only allow the identity to complete the task, and no more. When user's permissions are restricted to their job requirements, it's easier to identify suspicious or unauthorized behavior in the system.

Ask questions like these: 

- Is read-only access enough? 
- Does the identity need permissions to delete resources?

**Limiting the level of access that users, applications, or services have to Azure resources reduces the potential attack surface.** If you grant only the minimum permissions that are required to perform specific tasks, the risk of a successful attack or unauthorized access is significantly reduced. For example, security teams only need read-only access to security attributes for all technical environments. That level is enough to assess risk factors, identify potential mitigations, and report on the risks.

There are scenarios in which users need more access because of the organizational structure and team organization. There might be an overlap between various roles, or single users might perform multiple standard roles. In this case, use multiple role assignments that are based on the business function instead of creating a custom role for each such user. Doing so makes the roles easier to manage.

**Avoid permissions that specifically reference individual resources or users.** Granular and custom permissions create complexity and confusion because they don't pass on the intention to new resources that are similar. This can create  a complex legacy configuration that's difficult to maintain and negatively impact both security and reliability.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A granular access control approach enables better auditing and monitoring of user activities.

A role also has an *associated scope*. The role can operate at the allowed management group, subscription, resource group, or resource scope, or at another custom scope. Even if the identity has a limited set of permissions, widening the scope to include resources that are outside the identity's job function is risky. For example, read access to all source code and data can be dangerous and must be controlled.

You assign roles to identities by using role-based access control (RBAC). **Always use IdP-provided RBAC** to take advantage of features that enable you to apply access control consistently and revoke it rigorously.

**Use built-in roles.** They're designed to cover most use cases. Custom roles are powerful and sometimes useful, but you should be reserved for scenarios in which built-in roles won't work. Customization leads to complexity that increases confusion and makes automation more complex, challenging, and fragile. These factors all negatively impact security.

**Grant roles that start with least privilege and add more based your operational or data access needs**. Your technical teams must have clear guidance to implement permissions.

If you want fine-grained control on RBAC, add conditions on the role assignment based on context, such as actions and attributes.

### Make conditional access choices

Don't give all identities the same level of access. Base your decisions on two main factors:

-   **Time**. How long the identity can access your environment.

-   **Privilege**. The level of permissions.

Those factors aren't mutually exclusive. A compromised identity that has more privileges and unlimited duration of access can gain more control over the system and data or use that access to continue to change the environment. Constrain those access factors both as a preventive measure and to control the blast radius.

-   *Just in Time (JIT)* approaches provide the required privileges only when they're needed.

-   *Just Enough Access (JEA)* provides only the required privileges.

Although time and privilege are the primary factors, there are other conditions that apply. For example, you can also use the device, network, and location from which the access originated to set policies.

**Use strong controls that filter, detect, and block unauthorized access**, including parameters like user identity and location, device health, workload context, data classification, and anomalies.

For example, your workload might need to be accessed by third-party identities, entities like vendors, partners, and customers. They need the appropriate level of access rather than the default permissions that you provide to full-time employees. Clear differentiation of external accounts makes it easier to prevent and detect attacks that come from these vectors.

Your choice of IdP must be able to provide that differentiation, provide built-in features that grant permissions based on the least privilege, and provide built-in threat intelligence. This includes monitoring of access requests and sign-ins. The Azure IdP is Microsoft Entra ID. For more information, see the [Azure facilitation section](#azure-facilitation) of this article.

### Critical impact accounts

Administrative identities introduce some of the highest impact security risks because the tasks they perform require privileged access to a broad set of these systems and applications. Compromise or misuse can have a detrimental effect on your business and its information systems. Security of administration is one of the most critical security areas.

Protecting privileged access against determined adversaries requires you to take a complete and thoughtful approach to isolate these systems from risks. Here are some strategies:

-   **Minimize the number of critical impact accounts.**

-   **Use separate roles** instead of elevating privileges for existing identities.

-   **Avoid permanent or standing access** by using the JIT features of your IdP. For break glass situations, follow an emergency access process.

-   **Use modern access protocols** like passwordless authentication or multifactor authentication (MFA). Externalize those mechanisms to your IdP.

-   Enforce key security attributes by using **conditional access policies**.

-   **Decommission administrative accounts** that aren't being used.

Use a single identity across environments and associate a single identity with the user or principal. Consistency of identities across cloud and on-premises environments reduces human errors and the resulting security risks. Teams in both environments that manage resources need a consistent, authoritative source in order to meet security assurances. Work with your central identity team to ensure that identities in hybrid environments are synchronized.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: There's a risk associated with synchronizing high privilege identities. An attacker can get full control of on-premises assets, and this can lead to a successful compromise of a cloud account. Evaluate your synchronization strategy by filtering out accounts that can add to the attack surface.

### Establish processes to manage the identity lifecycle

**Access to identities must not last longer than the resources that the identities access.** Ensure that you have a process for disabling or deleting identities when there are changes in team structure, or software components.

This guidance applies to source control, data, control planes, workload users, infrastructure, tooling, the monitoring of data, logs, metrics, and other entities.

**Establish an identity governance process** to manage the lifecycle of digital identities, high-privileged users, external/guest users, and workload users. Implement access reviews to ensure that when identities leave the organization or the team, their workload permissions are removed.

### Protect nonidentity based secrets

Application secrets like preshared keys should be considered as vulnerable points in the system. In the two-way communication, if the provider or consumer is compromised, significant security risks can be introduced. Those keys can also be burdensome because they introduce operational processes.

**When you can, avoid using secrets** and consider using identity-based authentication for user access to the application itself, not just to its resources.

The following list provides a summary of guidance. For more information, see [Recommendations for application secrets](./application-secrets.md).

-   Treat these secrets as entities that can be dynamically pulled from a secret store. They shouldn't be hard coded in your application code, IaC scripts, deployment pipelines, or in any other artifact.

-   Be sure that you have the **ability to revoke secrets**.

-   Apply operational practices that handle tasks like **key rotation and expiration**.

For information about rotation policies, see [Automate the rotation of a secret for resources that have two sets of authentication credentials](/azure/key-vault/secrets/tutorial-rotation-dual) and [Tutorial: Updating certificate auto-rotation frequency in Key Vault](/azure/key-vault/certificates/tutorial-rotate-certificates).

### Keep the development environments safe

All code and scripts, pipeline tooling, source control system should be considered workload assets. **Access to writes should be gated** with automation and peer review. **Read access to source code should be limited** to roles on a need-to-know basis. Code repositories must have versioning, and **security code reviews** by peers must be a regular practice that's integrated with the development lifecycle. You need to have a process in place that **scans resources regularly** and identify latest vulnerabilities.

Use workload identities to grant access to resources from deployment environments, such as GitHub.

### Maintain an audit trail

One aspect of identity management is ensuring that the system is auditable. Audits validate whether assume-breach strategies are effective. Maintaining an audit trail helps you:

-   Verify that identity is authenticated with strong authentication. **Any action must be traceable** to prevent repudiation attacks.

-   **Detect weak or missing authentication protocols** and get visibility into and insights about user and application sign-ins.

-   Evaluate access from identities to the workload based on security and **compliance requirements** and consider user account risk, device status, and other criteria and policies that you set.

-   **Track progress or deviation** from compliance requirements.

Most resources have data plane access. You need to know the identities that access resources and the actions that they perform. You can use that information for security diagnostics.

For more information, see [Recommendations on security monitoring and threat analysis](./monitoring.md).

## Azure facilitation

We recommend that you always use modern authentication protocols that take into account all available data points and use conditional access. **Microsoft Entra ID provides identity and access management in Azure**. It covers the management plane of Azure and is integrated with the data planes of most Azure services. Microsoft Entra ID is the tenant that's associated with the workload subscription. It tracks and manages identities and their allowed permissions and simplifies overall management to minimize the risk of oversight or human error.

These capabilities natively integrate into the same Microsoft Entra ID identity and permission model for user segments:

-   [Microsoft Entra ID](/azure/active-directory/). Employees and Enterprise resources.

-   [Microsoft Entra External ID](/azure/active-directory/b2b/). Partners.

-   [Azure AD B2C](/azure/active-directory-b2c/). Customers.

-   [Microsoft Entra federation compatibility list](/azure/active-directory/hybrid/connect/how-to-connect-fed-compatibility). Third-party federation solutions.

You can use Microsoft Entra ID for authentication and authorization of custom applications via Microsoft Authentication Library (MSAL) or platform features, like authentication for web apps. It covers the management plane of Azure, the data planes of most of Azure services, and integration capabilities for your applications.

You can stay current by visiting [What's new in Microsoft Entra ID](/azure/active-directory/fundamentals/whats-new).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Microsof Entra ID is a single point of failure just like any other foundational service. There's no workaround until the outage is fixed by Microsoft. However, the rich feature set of Microsoft Entra ID outweighs the risk of using custom solutions.

Azure supports open protocols like OAuth2 and OpenID Connect. We recommend that you use these standard authentication and authorization mechanisms instead of designing your own flows.

### Azure RBAC

Azure RBAC represents security principals in Microsoft Entra ID. All role assignments are done via Azure RBAC. Take advantage of built-in roles that provide most of the permissions that you need. For more information, see [Microsoft Entra built-in roles](/azure/active-directory/roles/permissions-reference).

Here are some use cases:

-   By assigning users to roles, you can control access to Azure resources. For more information, see [Overview of Azure Active Directory role-based access control (RBAC)](/azure/active-directory/roles/custom-overview).

-   You can use Privileged Identity Management to provide time-based and approval-based role activation for roles that are associated with high-impact identities. For more information, see [What is Privileged Identity Management?](/azure/active-directory/privileged-identity-management/pim-configure).

For more information about RBAC, see [Best practices for Azure RBAC](/azure/role-based-access-control/best-practices).

For information about attribute-based controls, see [What is Azure ABAC?](/azure/role-based-access-control/conditions-overview).

### Workload identity

**Microsoft Entra ID can handle your application's identity.** The service principal that's associated with the application can dictate its access scope.

For more information, see [What are workload identities?](/azure/active-directory/workload-identities/workload-identities-overview).

**The service principal is also abstracted when you use a managed identity.** The advantage is that Azure manages all credentials for the application.

Not all services support managed identities. If you can't use managed identities, you can use service principals. However, using service principals increases your management overhead. For more information, see [What are managed identities for Azure resources?](/azure/active-directory/managed-identities-azure-resources/overview).

### Resource identity

The concept of **managed identities can be extended to Azure resources**. Azure resources can use managed identities to authenticate themselves to other services that support Microsoft Entra ID authentication. For more information, see [Azure services that can use managed identities to access other services](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

### Conditional access policies

**Conditional access describes your policy** for an access decision. To use conditional access, you need to understand the restrictions that are required for the use case. Configure Microsoft Entra ID conditional access by setting up an access policy for that's based on your operational needs.

For more information, see [Conditional access: Users, groups, and workload identities](/azure/active-directory/conditional-access/concept-conditional-access-users-groups).

### Group access management

Instead of granting permissions to specific users, assign access to groups in Microsoft Entra ID. If a group doesn't exist, work with your identity team to create one. You can then add and remove group members outside of Azure and make sure that permissions are current. You can also use the group for other purposes, like mailing lists.

For more information, see [Secure access control using groups in Microsoft Entra ID](/azure/active-directory/develop/secure-group-access-control).

### Threat detection

Microsoft Entra ID Protection can help you detect, investigate, and remediate identity-based risks. For more information, see [What is Azure Active Directory Identity Protection?](/azure/active-directory/identity-protection/overview-identity-protection).

Threat detection can take the form of reacting to an alert of suspicious activity or proactively searching for anomalous events in activity logs. User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel makes it easy to detect suspicious activities. For more information, see [Identify advanced threats with UEBA](/azure/sentinel/identify-threats-with-entity-behavior-analytics).

### Hybrid systems

On Azure, **don't synchronize accounts to Microsoft Entra ID that have high privileges in your existing Active Directory**. This synchronization is blocked in the default Microsoft Entra Connect Sync configuration, so you only need to confirm that you haven't customized this configuration.

For information about filtering in Microsoft Entra ID, see [Microsoft Entra Connect Sync: Configure filtering](/azure/active-directory/hybrid/connect/how-to-connect-sync-configure-filtering).

### Identity logging

**Enable Diagnostic setting on Azure resources** to emit information that you can use as an audit trail. The diagnostic information shows you which identities attempt to access which resources and the outcome of those attempts. The collected logs are sent to Azure Monitor.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Logging incurs costs because of the data storage that's used to store the logs. It also might cause a performance impact, especially on the code and on logging solutions that you add to the application.

## Example

The following example shows an identity implementation. Different types of identities are used together to provide the required levels of access.

:::image type="content" source="images/identity-access/identity-architecture-design.png" alt-text="Diagram that shows an identity implementation." border="false" lightbox="images/identity-access/identity-architecture-design.png":::

### Identity components

-   **System-managed identities**. Microsoft Entra ID provides access to service data planes that don't face users, like Azure Key Vault and data stores. These identities also control access, via RBAC, to the Azure management plane for workload components, deployment agents, and team members.

-   **Workload identities**. The application services in the Azure Kubernetes Service (AKS) cluster use workload identities to authenticate themselves to other components in the solution.

-   **Managed identities**. System components in the client role use system-managed identities, including build agents.

-   **Human identities**. User and operator authentication is delegated to Microsoft Entra ID or Azure AD (native, B2B, or B2C).

The security of preshared secrets is critical for any application. Azure Key Vault provides a secure storage mechanism for these secrets, including Redis and third-party secrets. 

A rotation mechanism is used to help ensure that secrets aren't compromised. Tokens for the Microsoft identity platform implementation of OAuth 2 and OpenID Connect are used to authenticate users. 

Azure Policy is used to ensure that identity components like Key Vault use RBAC instead of access policies. JIT and JEA provide traditional standing permissions for human operators. 

Access logs are enabled across all components via Azure Diagnostics, or via code for code components.

## Related links

- [Tutorial: Automate the rotation of a secret for resources that have two sets of authentication credentials](/azure/key-vault/secrets/tutorial-rotation-dual) 
- [Tutorial: Updating certificate auto-rotation frequency in Key Vault](/azure/key-vault/certificates/tutorial-rotate-certificates)
- [What's new in Microsoft Entra ID?](/azure/active-directory/fundamentals/whats-new)
- [Microsoft Entra built-in roles](/azure/active-directory/roles/permissions-reference)
- [Overview of role-based access control in Microsoft Entra ID](/azure/active-directory/roles/custom-overview)
- [What are workload identities?](/azure/active-directory/workload-identities/workload-identities-overview)
- [What are managed identities for Azure resources?](/azure/active-directory/managed-identities-azure-resources/overview)
- [Conditional access: Users, groups, and workload identities](/azure/active-directory/conditional-access/concept-conditional-access-users-groups)
- [Microsoft Entra Connect Sync: Configure filtering](/azure/active-directory/hybrid/connect/how-to-connect-sync-configure-filtering)
 
## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

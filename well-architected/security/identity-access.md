---
title: Recommendations for managing identity and access
description: Learn about the identity and access management recommendations for Azure workloads.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 10/09/2023
ms.topic: conceptual
---

# Recommendations for identity and access management 

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:05](checklist.md)|Implement strict, conditional, and auditable identity and access management (IAM) control across all workload users, team members, and system components. Limit access exclusively to as-necessary. Use modern industry standards for all authentication and authorization implementations. Nonidentity based access is restricted and rigorously audited.|
|---|---|

From a technical control perspective, **identity is always the primary perimeter**. This scope not only includes the edges of your workload, but inside at the individual component level. Typical identities include:

-   **Humans**: Application users, admins, operators, auditors, bad actors.

-   **Systems**: Workload identities, managed identities, API keys, service principals, Azure resources.

-   **Anonymous**. Entities who haven't provided any evidence about who they are.

This guide provides guidance on authenticating and authorizing identities attempting to access workload resources.

**Definitions** 


|Terms   |Definition   |
|---------|---------|
|Authentication (AuthN)     | A process that verifies the identity. They're who or what they say they are.        |
|Authorization (AuthZ)     |  A process that verifies whether the identity has the permission to do the requested action.       |
| IdP     |  Identity provider such as Azure Active Directory.       |
| Role     |  A set of permissions that can be assigned to identities.       |
|Scope     | Different levels of organizational hierarchy where a role is permitted to operate. Also a group of features in a system.        |
|Preshared keys     |  A type of secret that's shared between the provider and consumer and used through a secure and agreed upon mechanism.       |
|Workload identity       |   System identity for an application, service, script, container, or other components of a workload that's used to authenticate itself to other services and resources.      |
|Resource identity     |   Identities defined for cloud resources that's managed by the platform.      |
|User identity      |  People such as employees and external users.       |
|Security principal     |  A security principal is an identity that gets permissions. It could be a user, group or a service principal. It needs the same level of access.       |
|Persona     |   A job function or a title that has a set of responsibilities and actions.      |
|Conditional access|A set of rules that allows actions based on specified criteria. |

> [!Note] 
> An identity can be grouped with other similar identities under a parent called *security principal*. A security group is an example of a security principal. This hierarchical relationship provides ease of maintenance and consistency. Because identity attributes aren't handled at the individual level, chances of errors are also reduced. In this article, the term identity is inclusive of security principals.

### Role of an identity provider

An identity provider (IdP) is a cloud-hosted service that stores and manages users as digital identities.

**Take advantage of the capabilities provided by a trusted IdP** for your identity and access management. Don't implement custom systems to replace IdP. IdP systems are improved frequently based on the latest attack vectors by capturing billions of signals across multiple tenants each day. Azure Active Directory (Azure AD) is the IdP for Azure cloud platform.

#### Authentication

Authentication is a process that verifies identities. The requesting identity is required to provide some form of verifiable identification. For example,

-   Username and password

-   Preshared secrets such as API key that grants access.

-   Shared Access Signature (SAS) tokens.

-   Certificates used in TLS mutual authentication.

The verification process should be handled by the identity provider (IdP), as much as possible.

#### Authorization

Authorization is a process that allows or denies actions requested by the verified identity. The action might be operational or related to resource management.

Authorization requires assignment of permissions to the identity, which must be done through the features offered by your IdP.

## Key design strategies

To get a holistic view of the identity dimension for the workload, you need to catalog the flows, workload assets, personas, and the actions they'll perform. Your strategy must cover all use cases that handle **flows reaching the workload or its components (outside-in access) and flows reaching out from workload to other sources (inside-out access)**.

Each case will likely have their own set of controls that must be designed with an assume-breach mindset. Based on the identity requirements of the use case or the personas, identify the conditional choices. Avoid using one solution for all use cases, and conversely the controls shouldn't be so granular that there's unnecessary management overhead.

The identity access trail must be logged. This helps validate the controls and be used for compliance audits.

### Identify all identities for authentication

-   **Outside-in access**. Your identity design must authenticate all users that access the workload for various purposes. For example, an end user who accesses the application by calling APIs.

    At a granular level, components of the workload might also need access from outside. For example, an operator who needs access through the portal or access to the compute to run commands.

    Both are examples of **user identities** with different personas.

-   **Inside-out access**. The application needs to access other resources. For example, reading or writing to the data platform, retrieving secrets from the secret store, logging telemetry to monitoring services. It might even need to access third-party services. For this, **workload identity** is needed so that it can authenticate itself against the other resources.

    The concept applies at the component level. In this example, the container might need access to get its configuration from deployment pipelines. It needs to get images from a container registry. For this, **resource identity** is needed.

All these identities should be authenticated by your IdP.

Here's an example that shows the identity dimension of an architecture.

:::image type="content" source="images/identity-access/architecture-identity.png" alt-text="Diagram that shows an example of the identity dimension of an architecture." border="false" lightbox="images/identity-access/architecture-identity.png":::

### Determine actions for authorization

Next, you want to know what each authenticated identity is trying to do so that those actions can be authorized. The actions can be divided into data plane access and control plane access.

-   **Data plane access**. These actions cause data transfer for inside-out or outside-in access. For example, an application reading and writing data to a database, fetching secrets, or writing logs to a monitoring sink. At the component level, compute that's pulling or pushing images to a registry are considered as data plane operations.

-   **Control plane access**. These actions cause an Azure resource to be created, modified, or deleted. For example, changes to resource properties.

Applications typically target data plane operations, while operations often access both control and data planes. For authorization, note the operational actions that can be performed on the resource. For information about the permitted actions per resource, see [Azure resource provider operations](/azure/role-based-access-control/resource-provider-operations).

### Provide role-based authorization

Based on the responsibility of each identity, authorize actions that should be permitted. *The identity must not be allowed to do more than needed*. Before setting authorization rules, have a clear understanding of who is asking, what are they allowed to do, and to what extent? Those factors lead to choices that combine identity, role, and scope.

Consider the example of a workload identity. The application must have data plane access to the database. So, read and write actions must be allowed to the data resource. However, does the application need control plane access to the secret store? If the workload identity gets compromised by a bad actor, what would be the impact to the system, in terms of confidentiality, integrity, and availability.

##### Role assignment

A role is a *set of permissions* assigned to an identity. Assign roles that allow the identity to just complete the task, and no more. When user\'s permissions are restricted to their job requirements, it becomes easier to identify suspicious or unauthorized behavior in the system.

Ask questions, such as, is read-only access good enough? Does the identity need permission to delete resources?

**Limiting the level of access that users, applications, or services have to Azure resources reduces the potential attack surface**. By granting only the minimum permissions required to perform specific tasks, the risk of a successful attack or unauthorized access is significantly reduced. For example, security teams need only read-only access to security attributes for all technical environments. That level is enough to assess risk factors, identify potential mitigations, and report on the risks.

There are scenarios where users need more access because of their organizational structure and teams organization. They might have an overlap between various roles or single users performing multiple standard roles. In this case, have multiple role assignments based on the business function instead of creating custom role for each such user for better manageability.

**Avoid permissions that specifically reference individual resources or users**. Granular and custom permissions create complexity and confusion as they don\'t carry the intention to new similar resources. This can turn into a complex legacy configuration that's difficult to maintain and negatively impact both security and reliability.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: A granular access control approach allows for better auditing and monitoring of user activities.

A role also has an *associated scope*. The role can operate at the allowed management group, subscriptions, resource groups, resources, or other custom scope. Even if the identity has a limited set of permissions, widening the scope to include resources that are outside their job function, is risky. For example, read access to all source code and data can be dangerous and must be controlled for information exposure.

Roles are assigned to identities by using role-based access controls (RBAC). **Always use IdP-provided RBAC** to take advantage of features, which can be applied with consistency and revoked with rigor.

**Use built-in roles** because they're designed to cover most use cases. Custom roles are a powerful and sometimes useful capability, but they should be reserved for cases when built in roles won\'t work. Customization leads to complexity that increases confusion and makes automation more complex, challenging, and fragile. These factors all negatively impact security.

**Grant roles that start with least privilege and add more based your operational or data access needs**. Your technical teams must have clear guidance to implement permissions.

If you want fine-grained control on RBAC, add conditions on the role assignment based on context such as actions, attributes, and so on.

### Make conditional access choices

Don't give all identities the same level of access. Base your decision on two main factors:

-   **Time**: The duration that the identity can access your environment.

-   **Privilege**: The level of permissions.

Those factors aren't an either, or choice. A compromised identity with more privileges and unlimited timed access, will gain more control of the system and data or use the access to continue to change the environment. Constrain those aspects both as a preventive measure and to control the blast radius.

-   *Just in Time (JIT)* approaches provide the required privileged only when needed.

-   *Just Enough Access (JEA)* provides only the required privileges.

While time and privilege are primary ways, there are other conditions that apply. For example, the device, network, and location, where the access originated can also be used to set policies.

**Use strong controls that filter, detect, and block unauthorized access** including parameters such as user identity and location, device health, workload context, data classification and anomalies.

For example, your workload might need to be accessed by third-party identities, entities such as vendors, partners, and customers. They need the appropriate level of access instead of the default permission given to full-time employees. Clear differentiation of external accounts makes it easier to prevent and detect attacks coming in from these vectors.

Your choice of IdP must be able to provide that differentiation and provide built-in features that grant permission based on the least privilege and have built in threat intelligence. This includes monitoring of access requests and sign-ins. Azure-provided IdP is Azure Active Directory. For more information, see the [Azure facilitation section](#azure-facilitation) in this article.

### Critical impact accounts

Administrative identities introduce some of the highest impact security risks because performing these tasks require privileged access to a broad set of these systems and applications. Compromise or misuse can have a detrimental effect on the business and its information systems. Security of administration is one of the most critical security areas.

Protecting privileged access against determined adversaries requires you to take a complete and thoughtful approach to isolate these systems from risks. Here are some strategies:

-   **Minimize the number of critical impact accounts**.

-   **Have separate roles** instead of elevating privileges for existing identities.

-   **Avoid permanent or standing access** by using JIT features of the IdP. For break glass situations, follow an emergency access process.

-   **Use modern access protocols** such as passwordless authentication or multifactor authentication (MFA). Externalize those mechanisms to your IdP.

-   Enforce key security attributes by using **conditional access policies**.

-   **Decommission administrative accounts** that are unused.

Use single identity across environments and associate single identity with the user or principal. Consistency of identities across cloud and on-premises reduce human errors and the resulting security risk. Teams managing resources in both environments need a consistent authoritative source to achieve security assurances. Work with your central identity team to make sure identities in hybrid environments are synchronized.

> :::image type="icon" source="../_images/risk.svg"::: **Risk**: There's a risk associated with synchronizing high privilege identities. An attacker can get full control of on-premises assets leading to a successful compromise of a cloud account. Evaluate your synchronization strategy by filtering out accounts that can add to the attack surface.

### Establish processes to manage the identity lifecycle

**Access to identities must not live longer than the resources they access**. Ensure you have a process for disabling or deleting identities when there are changes in team structure, or software components.

This applies to source control, data, control planes, workload users, infrastructure, tooling, monitoring data, logs, metrics, and other entities.

**Establish identity governance process** to manage the lifecycle of digital identities, high-privileged users, external/guest users, workload users. Implement access reviews to ensure that identities are detected which have left the organization or the team and their workload permissions are removed.

### Protect nonidentity based secrets

Application secrets such as preshared keys should be considered as vulnerable points in the system. In the two-way communication, if the provider or consumer is compromised, it can introduce significant security risks. They can also be burdensome because of extra operational processes.

**Where possible, avoid using secrets** and consider identity-based authentication for user access to the application itself, not just its resources.

This list provides a summary of guidance. For details on application secrets, see [Recommendations on application secrets](./application-secrets.md).

-   Treat them as secrets that can be dynamically pulled from a secret store. They shouldn't be hard coded your application code, IaC scripts, deployment pipelines or any other artifact.

-   Have the **ability to revoke secrets**.

-   Apply operational practices that handle tasks such as **key rotation and expiration**.

For information about rotation policies, see [Rotation tutorial for resources with two sets of credentials](/azure/key-vault/secrets/tutorial-rotation-dual) and [Tutorial - Updating certificate autorotation frequency in Key Vault](/azure/key-vault/certificates/tutorial-rotate-certificates).

### Keep the development environments safe

All code and scripts, pipeline tooling, source control system should be considered as workload assets. **Access to writes should be gated** with automation and peer review. **Read access to source code should be limited** to roles on a need-to-know basis. Code repository must have versioning and perform **security code reviews** by peers as a regular practice that's integrated with the development lifecycle. Have a process in place that **scans resources regularly** and identify latest vulnerabilities.

Use workload identities to grant access to resources from deployment environments, such as GitHub.

### Have an audit trail

An aspect of identity management is making sure the system is auditable. Audits validate whether the assume-breach strategies are effective. Maintaining a paper trail helps:

-   Verify that identity has been authenticated with strong authentication. **Any action must be traceable** to prevent repudiation attacks.

-   **Detect weak or missing authentication protocols** and provides visibility and insights about user and application sign-ins.

-   Evaluate access from identities to the workload based on security and **compliance requirements** and considers user account risk, device status, and other criteria and policies that you set.

-   **Track progress or deviation** from compliance requirements.

Most resources have data plane access. You want to know which identities access resources and what actions were performed. That information can be considered as security diagnostics for detection purposes.

For more information, see [Recommendations on security monitoring and threat analysis](./monitoring.md).

## Azure facilitation

It's recommended that you always use modern authentication protocols based on all available data points with conditional access. **Azure Active Directory (Azure AD) is the one-stop-shop for identity and access management in Azure**. It covers the management plane of Azure and is well-integrated with data plane of most Azure services. Azure AD is the tenant associated with the workload subscription. Azure AD tracks and manages identities, and their allowed permissions simplifies overall management minimizing the risk of oversights or human errors.

These capabilities natively integrate into the same Azure AD identity and permission model for the user segments:

-   [Azure AD](/azure/active-directory/) - Employees and Enterprise resources

-   [Azure AD B2B](/azure/active-directory/b2b/) - Partners

-   [Azure AD B2C](/azure/active-directory-b2c/) - Customers

-   [Azure AD federation compatibility list - Microsoft Entra](/azure/active-directory/hybrid/connect/how-to-connect-fed-compatibility) - Third-party federation solutions

Azure AD can be used for authentication and authorization of custom applications through Microsoft Authentication Library (MSAL) or platform features, for example authentication setup for WebApps. It covers management plane of Azure, data plane of most of Azure services but also provides the integration capabilities for your applications.

Get current, stay current by getting improvements on an ongoing basis. For more information, see [What\'s new? Release notes - Microsoft Entra](/azure/active-directory/fundamentals/whats-new).

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Azure AD is a single point of failure just like any other foundational service. There's no workaround until the outage is fixed by Azure. However, the rich feature set of Azure AD outweighs the risk of using other custom solutions.

Azure supports open protocols like OAuth2, OpenID Connect, and others. It's recommended that you use those standard authentication and authorization mechanisms instead of designing your own flows.

### Azure RBAC

Azure RBAC represents security principals in Azure AD. *All role assignments* are done through Azure RBAC. Take advantage of built-in roles that provide most permissions you need. For more information, see [Azure AD built-in roles - Microsoft Entra](/azure/active-directory/roles/permissions-reference).

Here are some use cases:

-   By assigning users to roles, you can control access to Azure resources. For more information, see [Overview of Azure Active Directory role-based access control (RBAC) - Microsoft Entra](/azure/active-directory/roles/custom-overview).

-   You can use Privileged Identity Management (PIM) to provide time-based and approval-based role activation roles that are associated with high impact identities. For more information, see [What is Privileged Identity Management? - Microsoft Entra](/azure/active-directory/privileged-identity-management/pim-configure).

For more information, see [Best practices for Azure RBAC](/azure/role-based-access-control/best-practices).

For information about attributes-based controls, see [What is Azure attribute-based access control (Azure ABAC)?](/azure/role-based-access-control/conditions-overview).

### Workload identity

**Your application's identity can be handled by Azure AD**. The service principal associated with the application can dictate its access scope.

For more information, see [Workload identities - Microsoft Entra](/azure/active-directory/workload-identities/workload-identities-overview).

**The service principal is also abstracted by using a managed identity**. The advantage is that Azure manages all credentials for the application.

Not all services support managed identities. In those cases, you can use service principals. However, there's a tradeoff in added management. For more information, see [Managed identities for Azure resources - Microsoft Entra](/azure/active-directory/managed-identities-azure-resources/overview).

### Resource identity

The concept of **managed identities can be extended to Azure resources**. Azure resources can use managed identities to authenticate themselves to other services that support Azure AD authentication. For a list of supported Azure services, see [services that support managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

### Conditional access policies

**Conditional access describes your policy** for an access decision. To enable conditional access, understand what restrictions are required for the use case. Configure Azure AD Conditional Access by setting up Access policy for Azure management based on your operational needs.

For more information, see [Users, groups, workload identity in Conditional Access policy - Microsoft Entra](/azure/active-directory/conditional-access/concept-conditional-access-users-groups).

### Group access management

Instead of granting permissions to specific users, assign access to groups in Azure AD. Work with the central identity team to create a group if a group doesn't exist. You can then add and remove group members externally to Azure and make sure that permissions are current, while also using the group for other purposes, such as mailing lists.

For more information, see [Secure access control using groups in Azure AD - Microsoft Entra](/azure/active-directory/develop/secure-group-access-control).

### Threat detection

Azure AD Identity Protection helps organizations detect, investigate, and remediate identity-based risks. For example, see [What is Azure Active Directory Identity Protection? - Microsoft Entra](/azure/active-directory/identity-protection/overview-identity-protection).

Detection can take the form of reacting to an alert of suspicious activity or proactively hunting for anomalous events in the activity logs. The User and Entity Behavior Analytics (UEBA) capability in Microsoft Sentinel makes it easy to detect suspicious activities. For more information, see [Identify advanced threats with User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel](/azure/sentinel/identify-threats-with-entity-behavior-analytics).

### Hybrid systems

For Azure, **don't synchronize accounts to Azure AD that have high privileges in your existing Active Directory**. This is blocked by default in the default Azure AD Connect configuration, so you only need to confirm that you haven't customized this configuration.

For information about filtering in Azure AD, [Azure AD Connect sync: Configure filtering - Microsoft Entra](/azure/active-directory/hybrid/connect/how-to-connect-sync-configure-filtering).

### Identity logging

**Enable Diagnostic setting on Azure resources** to emit information that can be used as audit trail. You'll be able to know which identity attempted access to what resource and what was the outcome. The collected logs are sent to Azure Monitor.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: There is a tradeoff on cost because of the data storage provisioned to store the logs. There might be a performance impact, especially on the code aspect and logging solutions that's added to the application.

## Example

This example shows the identity dimensions of architecture design. Different types of identities are used in conjunction to provide the required level of access.

:::image type="content" source="images/identity-access/identity-architecture-design.png" alt-text="Diagram that shows the identity dimensions of architecture design." border="false" lightbox="images/identity-access/identity-architecture-design.png":::


### Identity components

-   **System-managed identities**. Entra ID-based access to non-user-facing service data planes, such as key vault, data stores. They're also used to control access to Azure management plane for workload components, deployment agents, and team members, through RBAC.

-   **Workload identities**. The application services within the Azure Kubernetes Service (AKS) cluster use workload identities to authenticate themselves to other components in the solution.

-   **Managed identities**. System components in the client role use system-managed identities, including build agents.

-   **Human identities**. The user and operator authentication is delegated to Azure AD (native, B2B or B2C).

The security of preshared secrets is critical for any application. Azure Key Vault provides a secure storage mechanism for these secrets, including Redis and third-party secrets. 

To ensure that the secrets are not compromised, a rotation mechanism is established. Tokens for OAuth2 and OpenID Connect implementation from Microsoft identity platform are used to authenticate users. 

Azure policy is used to enforce identity configuration, such as Key Vault to use RBAC instead of access policies. Just-In-Time (JIT) and Just-Enough-Administration (JEA) are used to provide traditional standing permissions for human operators. 

Access logs are enabled across all components through Azure Diagnostics or code for code components.

## Related links

- [Rotation tutorial for resources with two sets of credentials](/azure/key-vault/secrets/tutorial-rotation-dual) 
- [Tutorial - Updating certificate autorotation frequency in Key Vault](/azure/key-vault/certificates/tutorial-rotate-certificates)
- [What's new? Release notes - Microsoft Entra](/azure/active-directory/fundamentals/whats-new)
- [Azure AD built-in roles - Microsoft Entra](/azure/active-directory/roles/permissions-reference)
- [Overview of Azure Active Directory role-based access control (RBAC) - Microsoft Entra](/azure/active-directory/roles/custom-overview)
- [Workload identities - Microsoft Entra](/azure/active-directory/workload-identities/workload-identities-overview)
- [Managed identities for Azure resources - Microsoft Entra](/azure/active-directory/managed-identities-azure-resources/overview)
- [Users, groups, workload identity in Conditional Access policy - Microsoft Entra](/azure/active-directory/conditional-access/concept-conditional-access-users-groups)
- [Azure AD Connect sync: Configure filtering - Microsoft Entra](/azure/active-directory/hybrid/connect/how-to-connect-sync-configure-filtering)
 
## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)

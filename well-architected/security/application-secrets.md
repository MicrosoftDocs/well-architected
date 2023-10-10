---
title: Recommendations for protecting application secrets
description: Learn about recommendations for managing application secrets. Find out how to create, store, and distribute secrets securely.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 10/09/2023
ms.topic: conceptual
---

# Recommendations for protecting application secrets

**Applies to this Azure Well-Architected Framework Security checklist recommendation:**

|[SE:09](checklist.md)| Protect application secrets by hardening their storage and restricting access and manipulation and by auditing those actions. Run a reliable and regular rotation process that can improvise rotations for emergencies.|
|---|---|

This guide describes the recommendations for securing sensitive information in applications. Proper management of secrets is crucial for maintaining the security and integrity of your application, workload, and associated data. Improper handling of secrets can lead to data breaches, service disruption, regulatory violations, and other issues.

Credentials, such as API keys, Open Authorization (OAuth) tokens, and Secure Shell (SSH) keys are secrets. Some credentials, such as client-side OAuth tokens, can be dynamically created at runtime. Dynamic secrets still need to be safeguarded despite their temporary nature. Non-credential information, like certificates and digital signature keys, can also be sensitive. Compliance requirements might cause configuration settings that aren't typically considered secret to be treated as application secrets.

**Definitions** 

|Term   |Definition   |
|---------|---------|
|Certificates| Digital files that hold the public keys for encryption or decryption.|
|Credentials | Information that's used to verify the identity of the publisher or consumer in a communication channel.|
|Credential scanning| The process of validating source code to make sure secrets aren't included.|
|Encryption| The process by which data is made unreadable and locked with a secret code.|
|Key| A secret code that's used to lock or unlock encrypted data.|
|Least-privilege access | A Zero Trust principle that aims at minimizing a set of permissions to complete a job function. |
|Managed identity | An identity that's assigned to resources and managed by Azure. |
|Nonsecret | Information that doesn't jeopardize the security posture of the workload if it's leaked.|
|Rotation | The process of regularly updating secrets so that, if they're compromised, they're available only for a limited time. |
|Secret |  A confidential component of the system that facilitates communication between workload components. If leaked, secrets can cause a breach.|
|X.509| A standard that defines the format of public key certificates.|

> [!IMPORTANT]
> **Don't treat nonsecrets like secrets.** Secrets require operational rigor that's unnecessary for nonsecrets and that might result in extra costs.
>
> Application configuration settings, such as URLs for APIs that the application uses, are an example of nonsecrets. This information shouldn't be stored with the application code or application secrets. Consider using a dedicated configuration management system such as Azure App Configuration to manage these settings. For more information, see [What is Azure App Configuration?](/azure/azure-app-configuration/overview).

## Key design strategies

Your secret management strategy should minimize secrets as much as possible and integrate them into the environment by taking advantage of platform features. For example, if you use a managed identity for your application, access information isn't embedded in connection strings and it's safe to store the information in a configuration file. Consider the following areas of concern before storing and managing secrets:

- Created secrets should be kept in secure storage with strict access controls.

- Secret rotation is a proactive operation, whereas revocation is reactive.

- Only trusted identities should have access to secrets.

- You should maintain an audit trail to inspect and validate access to secrets.

Build a strategy around these points to help prevent identity theft, avoid repudiation, and minimize unnecessary exposure to information.

### Safe practices for secret management

If possible, avoid creating secrets. Find ways to **delegate responsibility to the platform**. For example, use the platform's built-in managed identities to handle credentials. Fewer secrets result in reduced surface area and less time spent on secret management.

We recommended that keys have three distinct roles: user, administrator, and auditor. Role distinction helps to ensure only trusted identities have access to secrets with the appropriate level of permission. Educate developers, administrators, and other relevant personnel about the importance of secret management and security best practices.

#### Pre-shared keys

**You can control access by creating distinct keys for each consumer.** For example, a client communicates with a third-party API using a pre-shared key. If another client needs to access the same API, they must use another key. Don't share keys even if two consumers have the same access patterns or roles. Consumer scopes might change over time, and you can't independently update permissions or distinguish usage patterns after a key is shared. Distinct access also makes revocation easier. If a consumer's key is compromised, it's easier to revoke or rotate that key without affecting other consumers.

This guidance applies to different environments. The same key shouldn't be used for both pre-production and production environments. If you're responsible for creating pre-shared keys, make sure you create multiple keys to support multiple clients.

For more information, see [Recommendations for identity and access management](identity-access.md).

#### Secret storage

**Use a secret management system**, like Azure Key Vault, to store secrets in a hardened environment, encrypt at-rest and in-transit, and audit access and changes to secrets. If you need to store application secrets, keep them outside the source code for easy rotation.

Certificates should only be stored in Key Vault or in the OS's certificate store. For example, storing an X.509 certificate in a PFX file or on a disk isn't recommended. If you need a higher level of security, choose systems that have hardware security module (HSM) capabilities instead of software-based secret stores.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: HSM solutions are offered at a higher cost. You might also see an effect on application performance due to added layers of security.

A dedicated secret management system makes it easy to store, distribute, and control access to application secrets. Only authorized identities and services should have access to secret stores. Access to the system can be restricted via permissions. Always apply the least-privilege approach when assigning permissions.

**You also need to control access at the secret level.** Each secret should only have access to a single resource scope. Create isolation boundaries so that a component is only able to use secrets that it needs. If an isolated component is compromised, it can't gain control of other secrets and potentially the entire workload. One way to isolate secrets is to use multiple key vaults. There's no added costs for creating extra key vaults.

**Implement auditing and monitoring for secret access.** Log who accesses secrets and when to identify unauthorized or suspicious activity. For information about logging from a security perspective, see [Recommendations on security monitoring and threat detection](./monitoring.md).

#### Secret rotation

**Have a process in place that maintains secret hygiene.** The longevity of a secret influences the management of that secret. To reduce attack vectors, secrets should be retired and replaced with new secrets as frequently as possible.

Handle OAuth access tokens carefully, taking into consideration their time to live. Consider if the exposure window needs to be adjusted to a shorter period. Refresh tokens must be stored securely with limited exposure to the application. Renewed certificates should also use a new key. For information about refresh tokens, see [Secure OAuth 2.0 On-Behalf-Of refresh tokens](/azure/architecture/example-scenario/secrets/secure-refresh-tokens).

**Replace secrets after they reach their end of life, are no longer used by the workload, or if they've been compromised.** Conversely, don't retire active secrets unless it's an emergency. You can determine a secret's status by viewing access logs. Secret rotation processes shouldn't affect the reliability or performance of the workload. Use strategies that build redundancy in secrets, consumers, and access methods for smooth rotation.

 For more information on how Azure Storage handles rotation, see [Manage account access keys](/azure/storage/common/storage-account-keys-manage?tabs=azure-portal).

Rotation processes should be automated and deployed without any human interaction. Storing secrets in a secret management store that natively supports rotation concepts can simplify this operational task.

### Safe practices for using secrets

As a secret generator or operator, you should be able to distribute secrets in a safe manner. Many organizations use tools to securely share secrets both within the organization and externally to partners. In absence of a tool, have a process for properly  handing off credentials to authorized recipients. Your disaster recovery plans should include secret recovery procedures. Have a process for situations where a key is compromised or leaked and needs to be regenerated on demand. Consider the following best practices for safety when using secrets:

#### Prevent hardcoding

**Don't hard code secrets as static text** in code artifacts such as application code, configuration files, and build-deployment pipelines. This is a high-risk practice that makes the code vulnerable because secrets are exposed to everyone with read access.

You can avoid this situation by using managed identities to eliminate the need to store credentials. Your application uses its assigned identity to authenticate against other resources via the identity provider (IdP). Test in non-production environments with fake secrets during development to prevent accidental exposure of real secrets.

**Use tools that periodically detect exposed secrets** in your application code and build artifacts. You can add these tools as Git pre-commit hooks that scan for credentials before source code commits deploy. Review and sanitize application logs regularly to help ensure that no secrets are inadvertently recorded. You can also reinforce detection via peer reviews.

> [!NOTE]
> If the scanning tools discover a secret, that secret must be considered compromised. It should be revoked.

#### Respond to secret rotation

As a workload owner, you need to **understand the secret rotation plan and policies so that you can incorporate new secrets with minimal disruption to users.** When a secret is rotated, there might be a window when the old secret isn't valid, but the new secret hasn't been placed. During that window, the component that the application is trying to reach doesn't acknowledge requests. You can minimize these issues by building retry logic into the code. You can also use concurrent access patterns that allow you to have multiple credentials that can be safely changed without affecting each other.

**Work with the operations team and be part of the change management process.** You should let credential owners know when you decommission a part of the application that uses credentials that are no longer needed.

**Integrate secret retrieval and configuration into your automated deployment pipeline.** Secret retrieval helps to ensure secrets are automatically fetched during deployment. You can also use secret injection patterns to insert secrets into application code or configuration at runtime, which prevents secrets from being accidentally exposed to logs or version control.

## Azure facilitation

**Store secrets by using Key Vault.** Store secrets in the Azure secret management system, Key Vault, Azure Managed HSM, and other locations. For more information, see [How to choose the right key management solution](/azure/security/fundamentals/key-management-choose).

**Integrate identity-based access control.** Microsoft Entra ID and managed identities help minimize the need for secrets. Microsoft Entra ID offers a highly secure and usable experience for access control with built-in mechanisms for handling key rotation, monitoring for anomalies, and more.

Use Azure role-based access control (RBAC) to assign permissions to users, groups, and applications at a certain scope.

Use an access model to control key vaults, permissions, and secrets. For more information, see [Access model overview](/azure/key-vault/general/secure-your-key-vault#access-model-overview).

**Implement secret exposure detection.** Integrate processes in your workload that detect suspicious activity and periodically check for exposed keys in your application code. Some options include:

- [Azure Devops Credential Scanner task](/azure/security/develop/security-code-analysis-customize#credential-scanner-task)
- [Defender for Cloud secret scanning](/azure/defender-for-cloud/detect-exposed-secrets)
- [Microsoft Defender for Key Vault](/azure/defender-for-cloud/defender-for-key-vault-introduction)
- [GitHub Secret Scanner](https://docs.github.com/en/code-security/secret-scanning/about-secret-scanning)

Don't store keys and secrets for any environment type in application configuration files or continuous integration and continuous delivery (CI/CD) pipelines. Developers should use [Visual Studio Connected Services](/azure/key-vault/general/vs-key-vault-add-connected-service) or local-only files to access credentials.

## Related links

- [Access model overview](/azure/key-vault/general/secure-your-key-vault#access-model-overview)
- [Azure Devops Credential Scanner task](/azure/security/develop/security-code-analysis-customize#credential-scanner-task)
- [Configure the Microsoft Security DevOps Azure DevOps extension](/azure/defender-for-cloud/azure-devops-extension)
- [Configure GitHub Advanced Security for Azure DevOps](/azure/devops/repos/security/configure-github-advanced-security-features)
- [Defender for Cloud secret scanning](/azure/defender-for-cloud/detect-exposed-secrets)
- [How to choose the right key management solution](/azure/security/fundamentals/key-management-choose)
- [Manage account access keys](/azure/storage/common/storage-account-keys-manage?tabs=azure-portal)
- [Microsoft Defender for Key Vault](/azure/defender-for-cloud/defender-for-key-vault-introduction)
- [Recommendations on security monitoring and threat detection](./monitoring.md)
- [Recommendations for identity and access management](identity-access.md)
- [Secure OAuth 2.0 On-Behalf-Of refresh tokens for web services](/azure/architecture/example-scenario/secrets/secure-refresh-tokens)
- [Visual Studio Connected Services](/azure/key-vault/general/vs-key-vault-add-connected-service)

## Community links

- [GitHub secret scanner](https://docs.github.com/code-security/secret-scanning/about-secret-scanning)

## Security checklist

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)

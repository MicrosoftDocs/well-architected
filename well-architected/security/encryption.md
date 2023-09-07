---
title: Recommendations for building an encryption strategy
description: Learn about recommendations for encryption, including key design strategies, scope, native encryption mechanisms, encryption keys, standard encryption algorithms, hashes and checksums, data at rest, data in transit, data in use, and secret management.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for encryption

**Applies to: SE 05**

This guide describes recommendations for protecting your data through the means of encryption. Encryption is the process of making data unreadable by using cryptography algorithms and subsequently locked with a key. In the encrypted state, data can't be deciphered. It can only be decrypted by using a key that's paired with the encryption key.

If not protected, the data can be maliciously modified, leading to loss of integrity and confidentiality.

**Definitions** 

|Terms   |Definition   |
|---------|---------|
|Cipher suite     |  A set of algorithms that secures a network connection over TLS by encrypting and decrypting information.       |    

## Key design strategies

The choice of encryption mechanisms can be enforced by organizational mandates and even some regulatory requirements. For example, data is expected to remain only in the selected region and copies of data are maintained within that region.

Those requirements can often serve as the base minimum. Always strive for a higher level of protection. You're responsible for preventing confidentiality leaks and tampering of sensitive data whether that's external user data or employee data and considering where lives.

### Encryption scenarios

Encryption mechanisms will likely need to secure the data in these stages:

-   **Data at rest**. All information that's kept in storage objects. For example, encrypting through BitLocker when saved to storage on disk.

-   **Data in transit**. Information transferred between components, locations, or programs. For example, encryption with TLS, so packets moving over public and private networks is secure.

-   **Data in use**. Data being actively worked on in memory. For example, encryption with confidential compute that protects data as it's processed.

The preceding choices aren't mutually exclusive. Typically they are used together in the context of the entire solution. In some cases, one might act as a compensating control, for instance, you need isolation to prevent tampering when reading from memory.

### Scope of encryption

Make a distinction between what needs to be encrypted and what doesn't, by classifying your data against the use cases and sensitivity levels. For data that should be encrypted, determine the required level of protection. For example, Do you need end-to-end TLS encryption for all data in transit? For data at rest, which Azure features can meet your requirements? Do you need to double encrypt at every storage point? How do you provide information protection?

Balance your encryption decisions because there are significant tradeoffs.


> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff:** Every encryption hop can introduce performance latency. There are also operational complexities from a troubleshooting and observability perspective. Also, recovery can be a challenge.
>
> Scope those tradeoffs and consider them intentional to the data that's classified as sensitive. Requirements may even drive those tradeoffs. For example a certain type of data must be encrypted and stored within certain thresholds.

There are cases when encryption isn't possible because of technical limitations, investment, and other reasons. Make sure those reasons are clear, valid, and documented.

Strong encryption mechanisms shouldn't be your only form of defense. Always be prepared with data-theft prevention processes applying proper testing methods and anomaly detection.

### Native encryption mechanisms

Most Azure services provide some base level of encryption. Explore all platform-provided encryption options.

It's highly recommended that you don't disable platform capabilities and develop your own functionality. Platform encryption features use modern industry standards and have been developed with deep expertise and test against numerous cases.

In rare occasions where you do need to replace the platform-provided encryption, evaluate all pros and cons, and always use industry standard cryptographic algorithms.

Developers should use cryptography APIs built into operating systems instead of non-platform cryptography libraries. For .NET, follow the [.NET Cryptography Model](/dotnet/standard/security/cryptography-model).

### Encryption keys

By default, Azure uses Microsoft-managed encryption keys to encrypt and decrypt data. Key management is Azure's responsibility.

Alternatively, you can opt for customer-managed keys. Azure still uses your keys to do the work, but you are accountable for all key operations. This gives you the flexibility to change keys when you want. Decryption is a compelling reason for using customer-managed keys.

Strong encryption should be paired with strong decryption. From a security perspective, protection of decryption key is important because rotation is a common way of controlling the blast radius if a key is compromised. Monitor access to detect anomalous access and activities.

Store keys separate from encrypted data. This decoupling provides some level of guarantee that compromise of one entity doesn't impact the other. If you are using customer-managed keys, store them in a key store. For highly sensitive data, they should be stored in a managed Hardware Security Model (HSM).

Both stores are protected with the added layer of identity-based access. If you want, you can deny access, even to the platform.

### Standard encryption algorithms

Use well-established cryptography algorithms instead of creating custom implementations.

Industry standards for algorithms require encryption schemes to have a certain level of entropy. The entropy sources are injected during encryption. This makes the algorithm strong and difficult for an attacker to extract information. Determine the tolerable thresholds of entropy because encryption procedures are processor intensive. Find the right balance so that you're maximizing the compute cycles spent on the encryption relative to the overall performance targets on that request.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff:** If you choose an algorithm that's highly complex or injects entropy more than is reasonable, you'll notice an impact on your performance.

### Hashes and checksums

Hashing is usually an error detection technique. It can also be used in a security context because it's effective in detecting changes to data, which can be caused by tampering. Hash functions are based on cryptography but don't use keys. Instead, they use algorithms to produce checksums, which can verify the integrity of data through comparison.

Applications should use the SHA-2 family of hash algorithms (SHA-256, SHA-384, SHA-512).

### Data at rest

Classify and protect all information storage objects as per the compliance (internal and external) requirements. Here are some recommendations:

-   Encrypt data using native options provided for various storage services, data stores, and other resources used to persist data, even temporarily. Also encrypt your backup data maintaining the same level of security as the original source.

    Some Azure features are listed in the [Azure facilitation section](#azure-facilitation) in this article.

-   Use double encryption, when needed.

    If your business requirements call for higher assurance, double encryption is an option. Data is encrypted in two or more layers using independent customer-managed keys and stored in managed HSM. To read the data, you need access to both keys. If one key is compromised, the other continues to protect the data. This technique aims to increase attacker costs.

    There's another way to double encrypt. Use platform-provided encryption to protect the storage media at the infrastructure level and also apply another layer of strong encryption at the data level. For example, a message broker service has platform-provided encryption through Microsoft-managed keys that protect the message pipe. Then, you can encrypt the messages with customer-managed keys.

    Use more than one encryption key. Use an additional key encryption key (KEK) to protect your data encryption key (DEK).

-   Control access to the data by using identity-based access controls. Provide an additional layer of security through network firewalls that can block unexpected and unsafe access.

-   Store keys in a managed HSM that's access controlled with least privilege. Maintain separation of data and keys to that data.

-   Keep the least amount of data so that you\'re only encrypting what\'s necessary. Your data shouldn\'t live longer than your encryption cycle. When data is no longer needed, delete the encrypted data without spending decryption cycles.

### Data in transit

-   Use secure protocols for client-server communication.

    Transport protocols must have a built-in layer of security. TLS is the industry standard for exchanging data between client and server endpoints.
    
    Using the latest version of TLS is preferred. Don't use versions lower than TLS 1.2. Migrate solutions to support TLS 1.2 and use this version by default. All Azure services support TLS 1.2 on public HTTPS endpoints.
    
    > ![Risk icon](../_images/risk.svg) **Risk:** Older clients that don't support TLS 1.2 might not work properly if backward compatibility isn't supported.
    >
    > All website communication should use HTTPS, no matter the perceived sensitivity of transferred data. During client-server handshake, negotiate the use of HTTP Strict Transport Security (HSTS) policy so that HTTPS transport is maintained and doesn't drop to HTTP at any point during communication. This policy protects against man-in-the-middle attacks.
    
    > ![Risk icon](../_images/risk.svg) **Risk:** Support for HSTS is for newer versions. You might be breaking backward compatibility with older browsers.
    
    > [!NOTE] 
    > The concept of encrypted protocols to establish secure connections can be extended to databases. For example, Azure SQL Database supports tabular data stream (TDS) protocol, which integrates TLS handshake.
    >
    > A cipher suite is a set of algorithms that standardizes the handshake between the client and server. The ciphers make sure the exchange is encrypted and authenticated. The choice of ciphers depends on the TLS version used by the server. For some services, such as Azure Application Gateway, you can control the version of TLS and the cipher suites you want to support. Use cipher suites that use Advanced Encryption Standard (AES) as a symmetric block cipher. AES-128, AES-192, and AES-256 are acceptable.

-   Manage the lifecycle of certificates.

    Certificates have predetermined lifespan. Avoid having long-lived certificates or letting them expire on their own. Have a process in place that renews certificates at an acceptable frequency. If renewals are done at short intervals, the process can be automated.
    
    > [!NOTE] 
    > If you use [certificate pinning](/azure/security/fundamentals/certificate-pinning), be aware of the limitations around agility and certificate management.
    >
    > Your workflow shouldn't allow invalid certificates to be accepted in the environment. The process should be able to validate certificates and enforce that validation check. Also, monitor access logs to make sure the signing key is used with proper permissions.
    >
    > If a key is compromised, the certificate must be revoked immediately. The certificate revocation list (CRL) is provided by the certificate authority (CA) that indicates certificates that have been invalidated before expiration. Your validation check should account for CRLs.
    
    > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff:** The certification validation process can be cumbersome and usually involves a CA. Make an explicit choice about what must be encrypted through certificates. For other types of communication, evaluate if you can add security with localized compensating controls.
    >
    > One way of localizing controls is through mutual TLS (mTLS). It establishes trust between client and server in both directions. mTLS enhances security because both sides have their certificates, which are authenticated using their public/private key pair.  With mTLS, you remove dependency on the external CA. But the tradeoff is the added complexity of managing two certificates.
    
-   Double-encrypt VPN connections, if needed.

    You can add defense in depth to your VPN tunnel through double encryption. By using two VPN servers, you can hide the IP address once between the servers and second time between the server and destination. In the process, data in transit is also encrypted twice.
    
    > ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff:** There are significant tradeoffs. Double VPN setups are often more expensive. Also, connections are slower.
    
-   Have logging and monitoring processes.

    Keep track of access log on resources that has information about clients, their source IP, port, protocol, and so on. You should be able to detect anomalies.

## Azure facilitation

### Customer-managed keys

Store customer-managed keys in Azure Key Vault or Key Vault Managed Hardware Security Model (HSM).

The keys are treated like any other secret by Key Vault. Azure RBAC controls access using a permission model. This identity-based control must be used in conjunction with key vault access policies.

For more information, see [Grant permission to applications to access an Azure key vault using Azure RBAC](/azure/key-vault/general/rbac-guide?tabs=azure-cli).

### Data at rest protection

**Azure Storage** automatically encrypts your data using strong block ciphers when it is persisted to a storage account. 

Storage also provides client-side encryption through libraries for both Blob Storage and Queue storage.

For details, see [Azure Storage encryption](/azure/storage/common/storage-service-encryption). 

**Azure Virtual Machines** use disk files as virtual storage volumes. You can encrypt the virtual disk files. If an attempt is made to mount disk files, the contents of the files cannot be accessed because of the encryption.

Managed disks can be exported from the portal by default. Server-Side Encryption and Encryption-at-Host can protect data only after it's exported. However, data should be protected during the export process. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) helps protect and safeguard your data for that use case.

Azure offers several encryption options for managed disks. For more information, see [Overview of managed disk encryption options - Azure Virtual Machines](/azure/virtual-machines/disk-encryption-overview).

**Azure SQL Database** uses [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) for encrypting a database file at the page level.

### Data in transit protection

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/#product-overview) lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with Azure and your internal connected resources.

### Data in use protection

Azure confidential VMs provide a strong, hardware-enforced boundary and disk encryption that maintains isolation between virtual machines, hypervisor, and host management code.

Azure confidential VMs each have their own dedicated virtual  [Trust Platform Module (TPM)](/windows/security/information-protection/tpm/trusted-platform-module-overview). Encryption is done while securely booting the OS components.

### Secret management

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/#product-overview) can be used to securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets. You can also use it as a key and certificate management solution. Premium SKU supports Hardware security modules (HSM).

## Example

This abstracted example shows different solutions for encryption that can be used to manage keys, certificates, secrets, and others.

:::image type="content" source="images/encryption/manage-keys-certificates-secrets.png" alt-text="Diagram that shows encryption solutions for managing keys, certificates, and secrets." border="false" lightbox="images/encryption/manage-keys-certificates-secrets.png":::

## Related links

- [.NET Cryptography Model](/dotnet/standard/security/cryptography-model)
- [Certificate pinning in Azure services](/azure/security/fundamentals/certificate-pinning)
- [Grant permission to applications to access an Azure key vault using Azure RBAC](/azure/key-vault/general/rbac-guide?tabs=azure-cli)
- [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss)
- [Overview of managed disk encryption options - Azure Virtual Machines](/azure/virtual-machines/disk-encryption-overview)
- [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [Azure Key Vault overview](https://azure.microsoft.com/services/key-vault/#product-overview)
- [Trust Platform Module overview](/windows/security/information-protection/tpm/trusted-platform-module-overview)
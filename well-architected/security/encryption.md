---
title: Encryption strategy recommendations
description: Learn about recommendations for encryption, including encryption mechanisms, encryption keys, encryption algorithms, hashes, checksums, and secret management.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 10/09/2023
ms.topic: conceptual
---

# Recommendations for data encryption

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:07](checklist.md)|Encrypt data by using modern industry-standard methods to guard confidentiality and integrity. Align encryption scope with data classifications; prioritize native platform encryption methods.|
|---|---|

If your data isn't protected, it can be maliciously modified, which leads to loss of integrity and confidentiality.

This guide describes the recommendations for encrypting and protecting your data. Encryption is the process of using cryptography algorithms to **make the data unreadable and lock the data with a key**. In the encrypted state, data can't be deciphered. It can only be decrypted by using a key that's paired with the encryption key.

**Definitions** 

|Terms   |Definition   |
|---------|---------|
|Cipher suite     |  A set of algorithms that are used to encrypt and decrypt information to secure a network connection over Transport Layer Security (TLS). |
|Encryption|The process by which data is made unreadable and locked with a secret code.|
|Decryption|The process by which encrypted data is unlocked by using a secret code. |
|Signature|An encrypted stamp of authentication on data. |
|Signing|The process of verifying data's authenticity by using a signature.|
|Hashing|The process of transforming data to text or numbers with the intent of hiding information.|
|Double encryption|The process of encrypting data by using two or more independent layers of encryption.|
|Keys|A secret code that's used to lock or unlock encrypted data.|
|Certificates|Digital files that hold the public keys for encryption or decryption.|
|X.509| A standard that defines the format of public key certificates.|


## Key design strategies

Organizational mandates or regulatory requirements might enforce encryption mechanisms. For example, there might be a requirement that data must remain only in the selected region, and copies of the data are maintained in that region.

These requirements are often the base minimum. Strive for a higher level of protection. You're responsible for **preventing confidentiality leaks and tampering of sensitive data**, whether it's external user data or employee data.

### Encryption scenarios

Encryption mechanisms likely need to secure the data in three stages:

- **Data at rest** is all information that's kept in storage objects.

  An example of securing data at rest is using BitLocker to encrypt data that's saved to storage on a disk.

- **Data in transit** is information that's transferred between components, locations, or programs.

  An example of securing data in transit is encrypting data with TLS so packets that move over public and private networks are secure.

- **Data in use** is data that's actively being worked on in memory.

  An example of securing data in use is encrypting with confidential compute to protect data as it's processed.

The preceding choices aren't mutually exclusive. They're often used together in the context of the entire solution. One stage might act as a compensating control. For example, you might need to isolate data to prevent tampering when data is read from memory.

### Scope of encryption

**Classify data by its purpose and sensitivity level** to determine what data you need to encrypt. For data that should be encrypted, determine the required level of protection. Do you need end-to-end TLS encryption for all data in transit? For data at rest, which Azure features can meet your requirements? Do you need to double encrypt data at every storage point? How do you implement information protection?

It's important to balance your encryption decisions because there are significant tradeoffs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Every encryption hop can introduce performance latency. Operational complexities can occur in relation to troubleshooting and observability. Recovery can be a challenge.
>
> Scope these tradeoffs. Anticipate tradeoffs for data that's classified as sensitive. Requirements might even determine the tradeoffs, for example if a certain type of data must be encrypted and stored within certain thresholds.

There are cases when encryption isn't possible because of technical limitations, investment, or other reasons. Ensure that those reasons are clear, valid, and documented.

Strong encryption mechanisms shouldn't be your only form of defense. Implement data theft prevention processes, proper testing methods, and anomaly detection.

For information about classification, see [Recommendations on data classification](./data-classification.md).

### Native encryption mechanisms

Most Azure services provide a base level of encryption. **Explore platform-provided encryption options**.

It's highly recommended that you don't disable platform capabilities to develop your own functionality. Platform encryption features use modern industry standards, are developed by experts, and are highly tested.

For rare occasions, if you need to replace the platform-provided encryption, evaluate the pros and cons and use industry-standard cryptographic algorithms.

Developers should use cryptography APIs that are built into the operating system rather than nonplatform cryptography libraries. For .NET, follow the [.NET cryptography model](/dotnet/standard/security/cryptography-model).

### Encryption keys

By default, Azure services use Microsoft-managed encryption keys to encrypt and decrypt data. Azure is responsible for key management.

You can opt for **customer-managed keys**. Azure still uses your keys, but you're accountable for key operations. **You have the flexibility to change keys** when you want. Decryption is a compelling reason to use customer-managed keys.

You should **pair strong encryption with strong decryption**. From a security perspective, protecting a decryption key is important because rotation is a common way to control the blast radius if a key is compromised. Monitor access to detect anomalous access and activities.

**Store keys separate from encrypted data**. This decoupling helps ensure that the compromise of one entity doesn't affect the other. If you use customer-managed keys, store them in a key store. Store highly sensitive data in a managed hardware security module (HSM).

Both stores are protected with identity-based access. This feature enables you to deny access, even to the platform.

### Standard encryption algorithms

**Use cryptography algorithms that are well-established and follow industry standards** instead of creating custom implementations.

Industry standards for algorithms require encryption schemes to have a certain level of entropy. The entropy sources are injected during encryption. Entropy makes the algorithm strong and makes it difficult for an attacker to extract information. **Determine the tolerable thresholds of entropy**. Encryption procedures are processor-intensive. Find the right balance so that you're maximizing the compute cycles that are spent on the encryption, relative to the overall performance targets of the compute request.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: If you choose an algorithm that's highly complex or injects more than a reasonable amount of entropy, it degrades your system's performance.

### Hashes and checksums

Typically, hashing is an error detection technique. You can also use hashing for security because it **detects changes to data that might be caused by tampering**. Hash functions are based on cryptography, but they don't use keys. Hash functions use algorithms to produce checksums. Checksums can compare data to verify the integrity of it.

Applications should use the SHA-2 family of hash algorithms, such as SHA-256, SHA-384, or SHA-512.

### Data at rest

Classify and protect information storage objects in accordance with the internal and external compliance requirements. See the following recommendations:

- **Encrypt data by using native options** that are provided for storage services, data stores, and other resources that are used to persist data. Encrypt this data even if you store data in these storage services or resources only temporarily. Also encrypt your backup data to maintain the same level of security as the original source.

    For more information, see [Data at rest protection](#data-at-rest-protection).

- **Use double encryption**. If your business requirements call for higher assurance, you can perform double encryption. Encrypt data in two or more layers by using independent customer-managed keys. Store the data in a managed HSM. To read the data, you need access to both keys. If one key is compromised, the other key still protects the data. This technique aims to increase attacker costs.

    You can also use platform-provided encryption to double encrypt data. Platform-provided encryption protects the storage media at the infrastructure level, and you apply another layer of encryption at the data level. For example, a message broker service has platform-provided encryption via Microsoft-managed keys that protects the message pipe. This method allows you to encrypt the messages with customer-managed keys.

    Use more than one encryption key. Use a key encryption key (KEK) to protect your data encryption key (DEK).

- **Use identity-based access controls to control access data**. Add network firewalls to provide an extra layer of security that blocks unexpected and unsafe access.

- **Store keys in a managed HSM** that has least-privilege access control. Separate the data from the keys to the data.

- **Store limited amount of data** so that you only encrypt what's necessary. Your data shouldn't live longer than your encryption cycle. When data is no longer needed, delete the encrypted data without spending decryption cycles.

### Data in transit

- **Use secure protocols for client-server communication**. Transport protocols have a built-in layer of security. TLS is the industry standard for exchanging data between client and server endpoints.

    Don't use versions lower than TLS 1.2. Migrate solutions to support TLS 1.2, and use this version by default. All Azure services support TLS 1.2 on public HTTPS endpoints.

    > :::image type="icon" source="../_images/risk.svg"::: **Risk**: Older clients that don't support TLS 1.2 might not work properly if backward compatibility isn't supported.
    >
    > All website communication should use HTTPS, regardless of the sensitivity of the transferred data. During a client-server handshake, negotiate the use of the HTTP Strict Transport Security (HSTS) policy so that HTTPS transport is maintained and doesn't drop to HTTP during communication. This policy protects against man-in-the-middle attacks.
    >
    > Support for HSTS is for newer versions. You might break backward compatibility with older browsers.

    > [!NOTE]
    > You can also encrypt protocols to establish secure connections for databases. For example, Azure SQL Database supports the Tabular Data Stream (TDS) protocol, which integrates a TLS handshake.
    >
    > A cipher suite is a set of algorithms that are used to standardize the handshake between the client and the server. The ciphers ensure that the exchange is encrypted and authenticated. The choice of ciphers depends on the TLS version that the server uses. For some services, such as Azure Application Gateway, you can choose the version of TLS and the cipher suites that you want to support. Implement cipher suites that use the Advanced Encryption Standard (AES) as a symmetric block cipher. AES-128, AES-192, and AES-256 are acceptable.

- **Manage the lifecycle of certificates**. Certificates have a predetermined lifespan. Don't keep long-lived certificates, and don't let them expire on their own. Implement a process that renews certificates at an acceptable frequency. You can automate the process for renewals that occur at short intervals.

    > [!NOTE]
    > If you use [certificate pinning](/azure/security/fundamentals/certificate-pinning), familiarize yourself with the agility and certificate management limitations.
    >
    > Your workflow shouldn't allow invalid certificates to be accepted in the environment. The certificate pinning process should validate certificates and enforce that validation check. You should monitor access logs to ensure that the signing key is used with proper permissions.
    >
    > If a key is compromised, the certificate must be revoked immediately. A certificate authority (CA) provides a certificate revocation list (CRL) that indicates the certificates that are invalidated before their expiration. Your validation check should account for CRLs.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: The certification validation process can be cumbersome and usually involves a CA. Determine the data that you must encrypt with certificates. For other types of communication, determine if you can implement localized compensating controls to add security.
    >
    > One way of localizing controls is with mutual TLS (mTLS). It establishes trust in both directions between the client and the server. Both the client and the server have their own certificates, and each certificate is authenticated with their public or private key pair. With mTLS, you're not dependent on the external CA. The tradeoff is the added complexity of managing two certificates.

- **Double encrypt VPN connections if needed**. Perform double encryption to add defense in depth to your VPN tunnel. When you use two VPN servers, you can hide the IP address between the servers, and also hide the IP address between the server and the destination. During this process, data in transit is also encrypted twice.

    > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Compared to single VPN setups, double VPN setups are often more expensive, and connections are often slower.

- **Implement logging and monitoring processes**. Keep track of access sign-in resources that store information about clients, like their source IP, port, and protocol. Use this information to detect anomalies.

## Azure facilitation

The following sections describe Azure services and features that you can use to encrypt your data.

##### Customer-managed keys

Store customer-managed keys in Azure Key Vault or in a Key Vault-managed HSM.

Key Vault treats the keys like any other secret. Azure role-based access controls (RBAC) access the keys via a permission model. This identity-based control must be used with Key Vault access policies.

For more information, see [Provide access to Key Vault keys, certificates, and secrets by using RBAC](/azure/key-vault/general/rbac-guide).

##### Data-at-rest protection

- **Azure Storage** automatically encrypts your data with block ciphers when the data is persisted to a storage account. For Azure Blob Storage and Azure Queue Storage, Storage also provides client-side encryption via libraries.

  For more information, see [Storage encryption](/azure/storage/common/storage-service-encryption).

- **Azure Virtual Machines** has disk files that serve as virtual storage volumes. You can encrypt the virtual disk files so the contents can't be accessed.

  Managed disks can be exported from the portal. Server-side encryption and encryption at host can protect data only after it's exported. However, you should protect data during the export process. You can use [Azure Disk Encryption](/azure/virtual-machines/windows/disk-encryption-overview) to protect and safeguard your data during the export process.

  Azure offers several encryption options for managed disks. For more information, see [Overview of managed disk encryption options](/azure/virtual-machines/disk-encryption-overview).

- **SQL Database** offers a [transparent data encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) feature that's used to encrypt a database file at the page level.

##### Data-in-transit protection

With [Key Vault](https://azure.microsoft.com/services/key-vault/#product-overview), you can provision, manage, and deploy public and private Secure Sockets Layer (SSL) or TLS certificates. You can use the certificates with Azure and with your internal connected resources.

##### Data-in-use protection

Azure confidential virtual machines (VMs) provide a hardware-enforced boundary. They also provide disk encryption that maintains isolation between VMs, the hypervisor, and host management code.

Each Azure confidential VM has its own dedicated virtual [Trust Platform Module (TPM)](/windows/security/information-protection/tpm/trusted-platform-module-overview). Encryption is performed while the operating system components securely boot.

##### Secret management

You can use [Key Vault](https://azure.microsoft.com/services/key-vault/#product-overview) to securely store and control access to tokens, passwords, certificates, API keys, and other secrets. Use Key Vault as a key and certificate management solution. Premium SKU supports HSMs.

## Example

The following example shows encryption solutions that you can use to manage keys, certificates, and secrets.

:::image type="content" source="images/encryption/manage-keys-certificates-secrets.png" alt-text="Diagram that shows encryption solutions for managing keys, certificates, and secrets." border="false" lightbox="images/encryption/manage-keys-certificates-secrets.png":::

## Related links

- [.NET cryptography model](/dotnet/standard/security/cryptography-model)
- [Azure Disk Encryption](/azure/virtual-machines/windows/disk-encryption-overview)
- [Storage encryption for data at rest](/azure/storage/common/storage-service-encryption)
- [Certificate pinning in Azure services](/azure/security/fundamentals/certificate-pinning)
- [Provide access to Key Vault keys, certificates, and secrets by using RBAC](/azure/key-vault/general/rbac-guide)
- [Overview of managed disk encryption options](/azure/virtual-machines/disk-encryption-overview)
- [Transparent data encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [Trust Platform Module overview](/windows/security/information-protection/tpm/trusted-platform-module-overview)

## Community links

- [Key Vault overview](https://azure.microsoft.com/services/key-vault/#product-overview)

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)


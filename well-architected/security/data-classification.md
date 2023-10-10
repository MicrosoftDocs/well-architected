---
title: Data classification recommendations
description: Learn about data classification and how to apply it to your workloads. Categorize data based on its sensitivity levels, information type, and scope of compliance so that you can apply the correct level of protection.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for data classification

**Applies to Azure Well-Architected Framework Security checklist recommendation:**

|[SE:03](checklist.md)|Classify and consistently apply sensitivity labels on all workload data and systems involved in data processing. Use classification to influence workload design, implementation, and security prioritization.|
|---|---|

This guide describes the recommendations for data classification. Most workloads store various types of data. Not all data is equally sensitive. Data classification helps you categorize data based on its sensitivity level, information type, and scope of compliance so you can apply the correct level of protection. Protection includes access controls, retention policies for different information types, and so on. While the actual security controls based on data classification are out of scope for this article, it provides recommendations for categorizing data based on the preceding criteria set by your organization.

**Definitions**

| Term | Definition |
|---|---|
| Classification |A process to categorize workload assets by sensitivity levels, information type, compliance requirements, and other criteria provided by the organization. |
| Metadata | An implementation for applying taxonomy to assets. |
| Taxonomy | A system to organize classified data by using an agreed upon structure. Typically, a hierarchical depiction of data classification. It has named entities that indicate categorization criteria. |


## Key design strategies

Data classification is a crucial exercise that often drives building a system of record and its function. Classification also helps you correctly size security assurances and helps the triage team expediate discovery during incident response. A prerequisite to the design process is to clearly understand whether data should be treated as confidential, restricted, public, or any other sensitivity classification. It's also essential to determine the locations where data is stored, because the data might be distributed across multiple environments.

Data discovery is necessary to locate the data. Without that knowledge, most designs adopt a middle-ground approach, which might or might not serve the security requirements. Data can be overprotected, resulting in cost and performance inefficiencies. Or it might not be protected enough, which adds to the attack surface.

Data classification is often a cumbersome exercise. There are tools available that can discover data assets and suggest classifications. But don't just rely on tooling. Have a process in place where team members diligently do the exercises. Then use tooling to automate when that's practical.

Along with these best practices, see [Create a well-designed data classification framework](/compliance/assurance/assurance-create-data-classification-framework).

### Understand organization-defined taxonomy

*Taxonomy* is a hierarchical depiction of data classification. It has named entities that indicate the categorization criteria.

In general, there isn't a universal standard for classification or for defining taxonomy. It's driven by an organization's motivation for protecting data. Taxonomy might capture compliance requirements, promised features for the workload users, or other criteria driven by business needs.

Here are some example classification labels for sensitivity levels, information type, and scope of compliance.

| Sensitivity | Information type | Scope of compliance |
|--------|--------| -------- |
| Public, General, Confidential, Highly Confidential, Secret, Top Secret, Sensitive | Financial, Credit Card, Name, Contact Info, Credentials, Banking, Networking, SSN, Health fields, Date of Birth, Intellectual Property, Personally Identifiable Information (PII) | GDPR, HIPAA, PCI, CCPA, SOX, RTB |

As a workload owner, rely on your organization to provide you with a well-defined taxonomy. All workload roles must have a shared understanding of the structure, nomenclature, and definition of the sensitivity levels. Don't define your own classification system.  

### Define the classification scope

Most organizations have a diverse set of labels.

:::image type="content" source="images/classify-data/labels.png" alt-text="Diagram that shows an example of an organization's sensitivity labels." lightbox="images/classify-data/labels.png":::

Clearly identify which data assets and components are in-scope and out-of-scope for each sensitivity level. You should have a clear objective on the outcome. The objective could be quicker triage, accelerated disaster recovery, or regulatory audits. When you clearly understand the objectives, it ensures you correctly size your classification efforts.

Start with these simple questions and expand as necessary based on your system complexity:

- What's the origin of data and information type?
- What's the expected restriction based on access? For example, is it public information data, regulatory, or other expected use cases?  
- What's the data footprint? Where is data stored? How long should the data be retained?
- Which components of the architecture interact with the data?
- How does the data move through the system?
- What information is expected in the audit reports?
- Do you need to classify preproduction data?

#### Take inventory of your data stores

If you have an existing system, take inventory of all data stores and components that are in scope. On the other hand, if you're designing a new system, create a data flow dimension of the architecture and have an initial categorization per taxonomy definitions. Classification applies to the system as a whole. It's distinctly different from classifying configuration secrets and nonsecrets.

#### Define your scope

Be granular and explicit when defining the scope. Suppose your data store is a tabular system. You want to classify sensitivity at the table level or even the columns within the table. Also, be sure to extend classification to nondata store components that might be related or have a part in processing the data. For example, have you classified the backup of your highly sensitive data store? If you're caching user-sensitive data, is the caching data store in scope? If you use analytical data stores, how is the aggregated data classified?

### Design according to classification labels

Classification should influence your architectural decisions. The most obvious area is your segmentation strategy, which should consider the varied classification labels.

For example, the labels influence the traffic isolation boundaries. There might be critical flows where end-to-end transport layer security (TLS) is required, while other packets can be sent over HTTP. If there are messages transmitted over a message broker, certain messages might need to be signed.

**For data at rest, the levels will affect the encryption choices**. You might choose to protect highly sensitive data through double encryption. Different application secrets might even require control with varied levels of protection. You might be able to justify storing secrets in a hardware security module (HSM) store, which offers higher restrictions. Compliance labels also dictate decisions about the right protection standards. For example, The PCI-DSS standard mandates the use of FIPS 140-2 Level 3 protection, which is available only with HSMs. In other cases, it might be acceptable for other secrets to be stored in a regular secret management store.

If you need to protect data in use, you might want to incorporate confidential compute in the architecture.

**Classification information should move with the data as it transitions through the system** and across components of the workload. Data labeled as confidential should be treated as confidential by all components that interact with it. For example, be sure to protect personal data by removing or obfuscating it from any kind of application logs.

**Classification impacts the design of your report** in the way data should be exposed. For example, based on your information type labels, do you need to apply a data masking algorithm for obfuscation as a result of the information type label? Which roles should have visibility into the raw data versus masked data? If there are any compliance requirements for reporting, how is data mapped to regulations and standards? When you have this understanding, it's easier to demonstrate compliance with specific requirements and generate reports for auditors.

It also impacts the data lifecycle management operations, such as data retention and decommissioning schedules.

### Apply taxonomy for querying

There are many ways to apply taxonomy labels to the identified data. Using a classification schema with metadata is the most common way to indicate the labels. Standardization through schema makes sure that reporting is accurate, minimizes chances of variation, and avoids the creation of custom queries. Build automated checks to catch invalid entries.

You can apply labels manually, programmatically, or use a combination of both. The architecture design process should include design of the schema. Whether you have an existing system or are building a new one, when applying labels, maintain consistency in the key/value pairs.

Keep in mind that not all data can be clearly classified. Make an explicit decision about how the data that can't be classified should be represented in reporting.

The actual implementation depends on the type of resources. Certain Azure resources have built-in classification systems. For example, Azure SQL Server has a classification engine, supports dynamic masking, and can generate reports based on metadata. Azure Service Bus supports including a message schema that can have attached metadata. When you design your implementation, evaluate the features supported by the platform and take advantage of them. Make sure metadata used for classification is isolated and stored separately from the data stores.

There are also specialized classification tools that can detect and apply labels automatically. These tools are connected to your data sources. Microsoft Purview has autodiscover capabilities. There are also third-party tools that offer similar capabilities. The discovery process should be validated through manual verification.

**Review data classification regularly**. Classification maintenance should be built into operations, otherwise stale metadata can lead to erroneous results for the identified objectives and compliance issues.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff**: Be mindful of the cost tradeoff on tooling. Classification tools require training and can be complex.

Ultimately, classification must roll up to the organization through central teams. Get input from them about the expected report structure. Also, take advantage of centralized tools and processes to have organizational alignment and also alleviate operational costs.

## Azure facilitation

Microsoft Purview unifies Azure Purview and Microsoft Purview solutions to provide visibility into data assets throughout your organization. For more information, see [What is Microsoft Purview?](/purview/purview)

Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse Analytics offer built-in classification features. Use these tools to discover, classify, label, and report the sensitive data in your databases. For more information, see [Data discovery and classification](/azure/azure-sql/database/data-discovery-and-classification-overview).

## Organizational alignment

Cloud Adoption Framework provides guidance for central teams about how to classify data so that workload teams can follow the organizational taxonomy.

For more information, see [What is data classification? - Cloud Adoption Framework](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification).

## Related links

- [Data classification and sensitivity label taxonomy - Microsoft Service Assurance](/compliance/assurance/assurance-data-classification-and-labels)
- [Create a well-designed data classification framework - Microsoft Service Assurance](/compliance/assurance/assurance-create-data-classification-framework)

## Next step

Refer to the complete set of recommendations.

> [!div class="nextstepaction"]
> [Security checklist](checklist.md)
